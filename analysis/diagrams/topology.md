```mermaid
graph TB
    subgraph "User Layer"
        Browser[Web Browser<br/>Chrome/Firefox/Safari]
        MetaMask[MetaMask Extension<br/>Web3 Wallet Provider]
    end

    subgraph "Frontend Layer (Port 5173)"
        React[React SPA<br/>Vite Dev Server<br/>Tailwind CSS]
        Router[React Router<br/>Client-side Routing]
        Storage[localStorage<br/>Auth Persistence]
    end

    subgraph "Backend Layer (Port 3001)"
        Express[Express.js API<br/>Node.js Runtime<br/>CORS Enabled]
        Auth[Authentication Routes<br/>OTP + Admin Validation]
        Middleware[Body Parser<br/>CSP Headers]
    end

    subgraph "Database Layer"
        MongoDB[(MongoDB<br/>localhost:27017<br/>Collections: voters, admin)]
        Collections[Schema-less Collections<br/>No Validation<br/>No Indexes]
    end

    subgraph "External Services"
        Twilio1[Twilio Account 1<br/>Phone: +918872241006<br/>SID + Token]
        Twilio2[Twilio Account 2<br/>Phone: +918889756729<br/>SID + Token]
        Twilio3[Twilio Account 3<br/>Phone: +916304521325<br/>SID + Token]
    end

    subgraph "Blockchain Layer"
        Network[Ethereum Network<br/>Testnet/Mainnet<br/>Gas Fees Required]
        Contract[Smart Contract<br/>Elections.sol<br/>Owner-Controlled]
        Storage2[Blockchain Storage<br/>Candidate Data<br/>Vote Records<br/>Election State]
    end

    subgraph "Development Environment"
        VSCode[VS Code Editor<br/>Development Tools]
        Git[Git Version Control<br/>GitHub Repository]
        Terminal[Terminal/Command Line<br/>npm/node commands]
    end

    %% User Interactions
    Browser --> React
    Browser --> MetaMask
    MetaMask --> Network

    %% Frontend Connections
    React --> Router
    React --> Storage
    React --> Express
    React --> MetaMask

    %% Backend Connections
    Express --> Auth
    Express --> Middleware
    Express --> MongoDB
    Express --> Twilio1
    Express --> Twilio2
    Express --> Twilio3

    %% Database Connections
    MongoDB --> Collections

    %% Blockchain Connections
    MetaMask --> Contract
    Contract --> Storage2
    Contract --> Network

    %% Development Connections
    VSCode --> Terminal
    Terminal --> Git
    Terminal --> React
    Terminal --> Express

    %% Network Flows
    React -.->|API Calls| Express
    Express -.->|SMS OTP| Twilio1
    React -.->|Web3 Calls| Contract
    Storage -.->|Auth Data| React

    %% Styling
    classDef frontend fill:#e3f2fd,stroke:#1976d2,stroke-width:2px
    classDef backend fill:#fff3e0,stroke:#f57c00,stroke-width:2px
    classDef database fill:#e8f5e8,stroke:#388e3c,stroke-width:2px
    classDef blockchain fill:#f3e5f5,stroke:#7b1fa2,stroke-width:2px
    classDef external fill:#fce4ec,stroke:#c2185b,stroke-width:2px
    classDef dev fill:#f5f5f5,stroke:#616161,stroke-width:2px

    class Browser,MetaMask,React,Router,Storage frontend
    class Express,Auth,Middleware backend
    class MongoDB,Collections database
    class Network,Contract,Storage2 blockchain
    class Twilio1,Twilio2,Twilio3 external
    class VSCode,Git,Terminal dev
```

**Production Deployment Topology Requirements:**

## Current Development Setup

### Development Stack (localhost)
- **Frontend**: Vite dev server (localhost:5173)
- **Backend**: Node.js server (localhost:3001)
- **Database**: Local MongoDB (localhost:27017)
- **Blockchain**: MetaMask → Ethereum testnet/mainnet
- **SMS**: 3 Twilio accounts for OTP delivery

### Production Architecture Needs

```mermaid
graph TB
    subgraph "Load Balancer Layer"
        LB[Load Balancer<br/>Nginx/HAProxy<br/>SSL Termination]
    end

    subgraph "Web Tier (Auto-Scaling)"
        Web1[Web Server 1<br/>Nginx + Static Files]
        Web2[Web Server 2<br/>Nginx + Static Files]
        CDN[CDN<br/>CloudFront/CloudFlare<br/>Asset Delivery]
    end

    subgraph "Application Tier (Auto-Scaling)"
        App1[API Server 1<br/>Node.js + PM2]
        App2[API Server 2<br/>Node.js + PM2]
        Queue[Background Jobs<br/>Bull/Agenda<br/>OTP Processing]
    end

    subgraph "Database Tier"
        Primary[(MongoDB Primary<br/>Write Operations)]
        Secondary[(MongoDB Secondary<br/>Read Replicas)]
        Backup[(Backup Storage<br/>S3/GCS)]
    end

    subgraph "Monitoring & Logging"
        Monitor[Monitoring<br/>Prometheus + Grafana]
        Logs[Centralized Logging<br/>ELK Stack]
        Alerts[Alerting<br/>PagerDuty/Slack]
    end

    subgraph "External Dependencies"
        TwilioHA[Twilio<br/>High Availability<br/>Multiple Regions]
        Blockchain[Ethereum Network<br/>Infura/Alchemy<br/>RPC Endpoints]
    end

    %% Connections
    LB --> Web1
    LB --> Web2
    Web1 --> CDN
    Web1 --> App1
    Web2 --> App2
    App1 --> Primary
    App1 --> Queue
    App2 --> Primary
    App2 --> Secondary
    App1 --> TwilioHA
    App2 --> TwilioHA
    Primary --> Backup
    Secondary --> Backup
    
    %% Monitoring
    Monitor --> Web1
    Monitor --> App1
    Monitor --> Primary
    Logs --> App1
    Logs --> App2
```

## Network & Security Architecture

### Port Configuration

| Service | Port | Protocol | Access |
|---------|------|----------|--------|
| **Frontend (Dev)** | 5173 | HTTP | Public |
| **Frontend (Prod)** | 80/443 | HTTP/HTTPS | Public |
| **Backend API** | 3001 | HTTP | Internal |
| **MongoDB** | 27017 | TCP | Internal |
| **Health Checks** | 3000 | HTTP | Internal |

### Security Boundaries

```mermaid
graph LR
    subgraph "Public Internet"
        User[End Users]
        Attack[Potential Attackers]
    end

    subgraph "DMZ Zone"
        WAF[Web Application Firewall<br/>DDoS Protection]
        LB[Load Balancer<br/>SSL Termination]
    end

    subgraph "Application Zone"
        Frontend[Frontend Assets<br/>Static Files Only]
        API[Backend API<br/>Business Logic]
    end

    subgraph "Data Zone"
        DB[(Database<br/>Encrypted at Rest)]
        Secrets[Secret Management<br/>AWS Secrets Manager]
    end

    User --> WAF
    Attack -.->|Blocked| WAF
    WAF --> LB
    LB --> Frontend
    LB --> API
    API --> DB
    API --> Secrets

    classDef public fill:#ffebee,stroke:#d32f2f
    classDef dmz fill:#fff3e0,stroke:#f57c00
    classDef app fill:#e8f5e8,stroke:#388e3c
    classDef data fill:#e3f2fd,stroke:#1976d2

    class User,Attack public
    class WAF,LB dmz
    class Frontend,API app
    class DB,Secrets data
```

### Infrastructure Requirements

| Component | Development | Production |
|-----------|-------------|------------|
| **Compute** | Local machine | 2+ EC2/VM instances |
| **Storage** | Local disk | EBS/Persistent disks |
| **Network** | Localhost | VPC with subnets |
| **DNS** | localhost:ports | Domain + Route53/CloudDNS |
| **SSL/TLS** | None | Let's Encrypt/Commercial cert |
| **Backup** | Manual | Automated daily backups |
| **Monitoring** | Console logs | Prometheus + Grafana |
| **Secrets** | .env files | Secret management service |

### Deployment Pipeline Architecture

```mermaid
graph LR
    subgraph "Source Control"
        GitHub[GitHub Repository<br/>Feature Branches]
        PR[Pull Requests<br/>Code Review]
    end

    subgraph "CI Pipeline"
        Test[Automated Tests<br/>Jest + Cypress]
        Build[Build Artifacts<br/>Docker Images]
        Security[Security Scan<br/>Snyk/OWASP]
    end

    subgraph "Deployment Stages"
        Dev[Development<br/>Auto Deploy]
        Staging[Staging<br/>Manual Approval]
        Prod[Production<br/>Blue/Green]
    end

    subgraph "Infrastructure"
        K8s[Kubernetes<br/>Container Orchestration]
        Monitor[Monitoring<br/>Health Checks]
    end

    GitHub --> PR
    PR --> Test
    Test --> Build
    Build --> Security
    Security --> Dev
    Dev --> Staging
    Staging --> Prod
    Prod --> K8s
    K8s --> Monitor
    Monitor -.->|Alerts| GitHub
```

**Missing Production Components:**
1. **Container Orchestration**: No Docker/Kubernetes setup
2. **Service Discovery**: No automatic service registration
3. **Circuit Breakers**: No failure isolation mechanisms
4. **Rate Limiting**: No API rate limiting implementation
5. **Caching Layer**: No Redis/Memcached for performance
6. **Message Queues**: No async processing for OTP/emails
7. **Database Clustering**: No high availability setup
8. **Disaster Recovery**: No cross-region backup strategy
