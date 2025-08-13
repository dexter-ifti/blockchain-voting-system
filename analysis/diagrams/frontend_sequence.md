```mermaid
sequenceDiagram
    participant U as User Browser
    participant R as React App
    participant L as localStorage
    participant B as Backend API
    participant M as MetaMask Wallet
    participant SC as Smart Contract

    Note over U,SC: Complete Frontend User Journey

    %% Landing & Authentication
    rect rgb(240, 248, 255)
        Note over U,L: Phase 1: User Authentication
        U->>R: Navigate to /voterLogin
        R->>L: Check existing voterData
        
        alt Already authenticated
            R->>R: Redirect to /voterDashboard
        else Not authenticated
            R->>R: Render VoterLoginComponent
            U->>R: Input Aadhaar number
            R->>R: Validate 12-digit format
            U->>R: Click "Log In"
            
            R->>B: POST /api/validateVoter
            Note right of R: {aadhar: "123456789012"}
            B-->>R: {success: true, phone: "+91888*****006"}
            
            R->>R: setState(otpSent: true)
            R->>R: Show OTP input field
            
            U->>R: Input 6-digit OTP
            U->>R: Click "Verify OTP"
            
            R->>B: POST /api/verifyCode
            Note right of R: {aadhar, code}
            B-->>R: {success: true, name, email, dob, phone}
            
            R->>L: Store complete voterData
            Note right of L: {isLogin: true, profile data}
            R->>R: navigate("/voterDashboard")
        end
    end

    %% Dashboard Loading & Blockchain Connection
    rect rgb(240, 255, 240)
        Note over R,SC: Phase 2: Dashboard & Blockchain Setup
        R->>R: VoterDashboard component loads
        R->>L: Verify authentication state
        R->>R: Render sidebar + VoterHome (default)
        
        U->>R: Click "Vote Now" in sidebar
        R->>R: Switch to VoteNow component
        R->>R: useEffect() triggers blockchain connection
        
        R->>M: connectToBlockchain()
        R->>M: window.ethereum.request({method: "eth_requestAccounts"})
        M->>U: Show MetaMask connection prompt
        U->>M: Approve wallet connection
        M-->>R: Account address returned
        
        R->>SC: new web3.eth.Contract(ABI, contractAddress)
        R->>SC: getCandidates() - Fetch all candidates
        SC-->>R: Candidate[] with {id, name, party, voteCount, logo}
        
        parallel
            R->>SC: isElectionStarted()
            SC-->>R: boolean (election status)
        and
            R->>SC: resultStatus()
            SC-->>R: boolean (results declared?)
        and
            R->>SC: checkIfVoted(aadhaar)
            SC-->>R: boolean (user voted?)
        end
        
        R->>R: Update component state with all fetched data
        R->>R: Conditionally render voting UI vs results
    end

    %% Voting Process
    rect rgb(255, 240, 240)
        Note over U,SC: Phase 3: Voting Interaction
        alt Election started AND user hasn't voted
            R->>R: Render candidate cards with "Vote Now" buttons
            U->>R: Click "Vote Now" for selected candidate
            
            R->>SC: addVote(candidateId, aadhaar)
            SC->>M: Initiate blockchain transaction
            M->>U: Show transaction confirmation
            Note right of M: Gas fee, transaction details
            
            alt User approves transaction
                U->>M: Confirm transaction
                M->>SC: Execute addVote transaction
                SC->>SC: Validate: user hasn't voted, aadhaar not used
                SC->>SC: Record vote, increment candidate count
                SC->>SC: Update voter tracking mappings
                SC-->>M: Transaction success
                M-->>R: Vote recorded successfully
                
                R->>R: setHasVotedState(true)
                R->>R: Hide "Vote Now" buttons
                R->>SC: getVoterChoice() - Get voted party
                SC-->>R: Party name string
                R->>R: Display "You voted for {party}"
                
            else User rejects transaction
                U->>M: Reject transaction
                M-->>R: Transaction cancelled
                R->>R: Show error message
            end
            
        else User already voted OR election not started
            R->>R: Show appropriate status message
            
            alt Results declared
                R->>SC: getWinner()
                SC-->>R: Winner candidate object
                R->>R: Display winner card with vote count
            else Results not declared
                R->>R: Display "Please wait for results"
            end
        end
    end

    %% Admin Flow (Alternative Path)
    rect rgb(255, 248, 240)
        Note over U,SC: Phase 4: Admin Election Management
        Note over U: Alternative: Admin user flow
        U->>R: Navigate to /adminLogin
        R->>R: Render AdminLoginComponent
        U->>R: Input username/password
        
        R->>B: GET /api/validateAdmin/{name}/{pass}
        Note right of R: ⚠️ Credentials in URL (security risk)
        B-->>R: {status: true, admin profile}
        
        R->>L: Store adminData {isLogin: true}
        R->>R: navigate("/adminDashboard")
        
        U->>R: Navigate to "Controls" in admin sidebar
        R->>R: Load Controls component
        
        U->>R: Click "Start Election"
        R->>M: Connect to MetaMask (admin wallet)
        R->>SC: startElection()
        SC->>SC: Verify msg.sender == owner
        SC->>SC: Set electionStarted = true
        SC-->>R: Election started confirmation
        
        U->>R: Click "Declare Results"
        R->>SC: declareResults()
        SC->>SC: Calculate winner (max votes)
        SC->>SC: Set isResultDeclared = true
        SC-->>R: Winner candidate details
        R->>R: Display winner information
    end

    Note over U,SC: Frontend State Management Patterns
    Note over R: • useState/useEffect in each component
    Note over L: • localStorage for auth persistence
    Note over R: • No global state management (Redux/Context)
    Note over R: • Direct API calls (no centralized client)
    Note over M: • MetaMask for wallet/transaction management
    Note over R: • Alert() for user feedback (no toast system)
```

**Key Frontend Architectural Notes:**

1. **Component State Pattern**: Each component manages its own state with hooks, no centralized store
2. **Authentication Flow**: Two-step process (Aadhaar → OTP) with localStorage persistence
3. **Blockchain Integration**: Direct Web3 calls with MetaMask transaction confirmations
4. **Error Handling**: Manual try-catch blocks with alert() feedback
5. **Route Protection**: Simple boolean checks against localStorage auth flags
6. **Real-time Updates**: No WebSocket/polling - manual refresh required for election changes
