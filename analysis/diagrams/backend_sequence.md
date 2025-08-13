```mermaid
sequenceDiagram
    participant C as Client (Frontend)
    participant A as Express App
    participant O as OTPAuth Route Handler
    participant V as validateAdmin Route
    participant DB as MongoDB
    participant T as Twilio SMS API

    Note over C,T: Complete Backend Authentication Flow

    %% Voter Authentication Flow
    rect rgb(240, 248, 255)
        Note over C,DB: Phase 1: Voter OTP Request
        C->>+A: POST /api/validateVoter
        Note right of C: Body: {aadhar: "123456789"}
        
        A->>+O: Route to validateVoter handler
        O->>+DB: findOne("voters", {aadhar})
        
        alt User exists in voters collection
            DB-->>-O: User document {aadhar, phone, name, etc}
            O->>O: Generate random 6-digit OTP
            O->>O: Select Twilio account based on phone number
            
            alt Valid phone number mapping
                O->>+T: Send SMS via Twilio API
                Note right of O: Message: "Your verification code is {otp}"
                T-->>-O: SMS delivery confirmation
                
                O->>+DB: Update user document
                Note right of O: Set: {otp, otpCreatedAt: new Date()}
                DB-->>-O: Update confirmation
                
                O-->>A: {success: true, message: "OTP sent", phone: masked}
                A-->>-C: 200 OK
            else Invalid phone number
                O-->>A: {success: false, message: "Invalid phone number"}
                A-->>-C: 400 Bad Request
            end
        else User not found
            DB-->>-O: null
            O-->>A: {success: false, message: "User not found"}
            A-->>-C: 404 Not Found
        end
    end

    %% OTP Verification Flow
    rect rgb(240, 255, 240)
        Note over C,DB: Phase 2: OTP Verification
        C->>+A: POST /api/verifyCode
        Note right of C: Body: {aadhar: "123456789", code: "123456"}
        
        A->>+O: Route to verifyCode handler
        O->>+DB: findOne("voters", {aadhar})
        DB-->>-O: User document with stored OTP
        
        alt OTP matches and is valid
            O->>+DB: Clear OTP fields
            Note right of O: Unset: {otp, otpCreatedAt}
            DB-->>-O: Clear confirmation
            
            O-->>A: {success: true, name, email, dob, phone}
            A-->>-C: 200 OK + User Profile Data
        else Invalid OTP
            O-->>A: {success: false, message: "Invalid verification code"}
            A-->>-C: 401 Unauthorized
        end
    end

    %% Admin Authentication Flow
    rect rgb(255, 248, 240)
        Note over C,DB: Phase 3: Admin Login (Security Risk)
        C->>+A: GET /api/validateAdmin/{name}/{pass}
        Note right of C: URL params contain plaintext credentials
        
        A->>+V: Route to validateAdmin handler
        V->>+DB: find("admin", {uname: name, pass: pass})
        Note right of V: ⚠️ Plaintext password comparison
        
        alt Admin credentials match
            DB-->>-V: Admin document
            V-->>A: {status: true, name, email, phone}
            A-->>-C: 200 OK + Admin Profile
        else Invalid credentials
            DB-->>-V: Empty array
            V-->>A: {success: false, message: "No Data Found"}
            A-->>-C: 400 Bad Request
        end
    end

    Note over C,T: Security Issues Highlighted in Red
    Note over V: ⚠️ Admin auth uses URL params + plaintext passwords
    Note over O: ⚠️ No input validation on any endpoints
    Note over A: ⚠️ No rate limiting on OTP requests
```
