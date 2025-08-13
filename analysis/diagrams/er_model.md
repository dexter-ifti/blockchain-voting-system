```mermaid
erDiagram
    %% Database Collections (Schema-less MongoDB)
    
    VOTERS {
        string aadhar PK "Primary identifier (Aadhaar number)"
        string phone "Mobile number for OTP delivery"
        string name "Full name of voter"
        string uname "Username/Email"
        string dob "Date of birth"
        string otp "Temporary OTP (cleared after verification)"
        Date otpCreatedAt "OTP generation timestamp"
    }
    
    ADMIN {
        string uname PK "Admin username"
        string pass "⚠️ PLAINTEXT PASSWORD"
        string name "Admin full name"
        string phone "Admin contact number"
    }

    %% External System Relationships
    TWILIO_ACCOUNTS {
        string account_sid "Twilio Account SID"
        string auth_token "Twilio Auth Token"
        string phone_number "Twilio Phone Number"
        string mapped_user_phone "Target user phone numbers"
    }

    %% Smart Contract Entities (Referenced but not stored in backend)
    BLOCKCHAIN_VOTES {
        uint256 candidate_id "Voted candidate ID"
        uint256 aadhaar_number "Voter's Aadhaar (blockchain)"
        address wallet_address "MetaMask wallet address"
        uint256 block_timestamp "Vote timestamp"
    }

    %% Relationships
    VOTERS ||--o{ BLOCKCHAIN_VOTES : "votes recorded on blockchain"
    VOTERS ||--|| TWILIO_ACCOUNTS : "phone number maps to Twilio account"
    ADMIN ||--o{ ELECTION_MANAGEMENT : "manages election via smart contract"

    %% Notes and Constraints
    VOTERS ||--o| OTP_SESSION : "temporary OTP stored during auth"
    
    %% Missing Relationships (Technical Debt)
    %% No foreign keys or referential integrity
    %% No user roles or permissions table
    %% No audit logging table
    %% No session management
```

**Schema Notes:**

1. **No Formal Schema**: Collections are schema-less with structure inferred from application code
2. **Missing Constraints**: No validation, unique constraints, or indexes defined
3. **Security Issues**: 
   - Admin passwords stored in plaintext
   - No password policies or rotation
4. **Temporary Data**: OTP fields are created/destroyed during authentication flow
5. **External Dependencies**:
   - Twilio account mapping hardcoded in application logic
   - Blockchain voting data not stored in backend (handled by smart contract)
6. **Data Integrity Risks**:
   - No referential integrity between collections
   - No audit trail for data changes
   - No backup or recovery strategy visible
