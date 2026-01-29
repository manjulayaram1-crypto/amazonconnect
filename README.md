# amazonconnect

# Amazon Connect - Auth Flow

This repository contains the Amazon Connect contact flow for self-service IVR system.

---

## 📞 Main Flow Overview

```mermaid
flowchart LR
    A[📞 Call Starts] --> B[🔐 Authenticate]
    B --> C[🤖 Lex Bot]
    C --> D{Choose Service}
    D --> E[✅ Process Request]
    E --> F{Need More Help?}
    F -->|Yes| C
    F -->|No| G[👋 End Call]
```

---

## 🔐 Authentication Flow

```mermaid
flowchart TD
    Start([📞 Incoming Call]) --> Setup[⚙️ Setup Voice & Logging]
    Setup --> Lookup["🔍 Lambda: customerbyphone"]
    Lookup --> Check{Customer Found?}
    
    Check -->|✅ Yes| SetAttrs["📝 Set Customer Info"]
    Check -->|❌ No| NotAuth["⚠️ Not Authenticated"]
    
    SetAttrs --> Welcome["👋 Welcome Message"]
    NotAuth --> MainBot
    Welcome --> MainBot["🤖 Main Lex Bot"]
```

---

## 🎯 Available Services (Intents)

```mermaid
flowchart TD
    Bot["🤖 Lex Bot: selfserbot"] --> Router{What do you need?}
    
    Router -->|"💰 Check Balance"| Balance[checkbalance]
    Router -->|"📋 Get Quote"| Quote[GetInsuranceQuote2]
    Router -->|"📄 Policy Info"| Policy[GetPolicyInfo]
    Router -->|"📊 Claim Status"| ClaimStatus[claimstatus]
    Router -->|"💸 Transfer Money"| Transfer[TransferMoney]
    Router -->|"📝 File Claim"| FileClaim[FileInsuranceClaim]
    Router -->|"📞 Request Callback"| Callback[RequestCallback]
    Router -->|"❓ FAQ"| FAQ[FAQQuery]
    Router -->|"👤 Agent"| Agent[GetSupport]
    Router -->|"👋 End"| EndCall[EndCall]
```

---

## 💰 Check Balance Flow

```mermaid
flowchart LR
    A[checkbalance] --> B["📝 Set accountType"]
    B --> C["⚙️ Lambda: ProcessSelfService"]
    C --> D["💰 Show Balance"]
    D --> E["🔄 Anything else?"]
```

---

## 📋 Get Insurance Quote Flow

```mermaid
flowchart LR
    A[GetInsuranceQuote2] --> B["📝 Collect Info"]
    B --> C["⚙️ Lambda: getinsurancequote"]
    C --> D["📋 Show Premium"]
    D --> E["📧 Lambda: sendquote"]
    E --> F["🔄 Anything else?"]
```

---

## 📄 Get Policy Info Flow

```mermaid
flowchart LR
    A[GetPolicyInfo] --> B["⚙️ Lambda: getpolicy"]
    B --> C{Found?}
    C -->|Yes| D["📄 Show Policy Details"]
    C -->|No| E["❌ No policy found"]
    D --> F["🔄 Anything else?"]
    E --> F
```

---

## 📊 Check Claim Status Flow

```mermaid
flowchart LR
    A[claimstatus] --> B["⚙️ Lambda: CheckClaimStatus"]
    B --> C{Found?}
    C -->|Yes| D["📊 Show Status"]
    C -->|No| E["❌ No claim found"]
    D --> F["🔄 Anything else?"]
    E --> F
```

---

## 💸 Self-Service Actions

### Transfer Money
```mermaid
flowchart LR
    T1[TransferMoney] --> T2["📝 Collect: amount, from, to"]
    T2 --> T3["⚙️ Lambda: selfservicebot"]
    T3 --> T4["✅ Confirmation + Transaction ID"]
```

### File Insurance Claim
```mermaid
flowchart LR
    F1[FileInsuranceClaim] --> F2["📝 Collect: type, date, description"]
    F2 --> F3["⚙️ Lambda: selfservicebot"]
    F3 --> F4["✅ Claim Filed"]
```

### Request Callback
```mermaid
flowchart LR
    C1[RequestCallback] --> C2["📝 Collect: reason, time"]
    C2 --> C3["⚙️ Lambda: selfservicebot"]
    C3 --> C4["✅ Callback Scheduled"]
```

---

## ❓ FAQ & 👤 Agent Transfer

```mermaid
flowchart LR
    subgraph FAQ
        FAQ1[FAQQuery] --> FAQ2["🤖 Lex Bot: travel"]
        FAQ2 --> FAQ3["💬 Answer"]
    end

    subgraph Agent
        A1[GetSupport] --> A2["👤 Connecting..."]
        A2 --> A3["📞 Transfer to Agent"]
    end
```

---

## 🗺️ Complete Flow Summary

```mermaid
flowchart TB
    subgraph INIT ["🚀 INITIALIZATION"]
        Start([📞 Call]) --> Auth["🔐 Authenticate"]
        Auth --> Welcome["👋 Welcome"]
    end

    subgraph MAIN ["🤖 MAIN MENU"]
        Welcome --> Lex["Lex Bot: selfserbot"]
    end

    subgraph SERVICES ["⚙️ SERVICES"]
        Lex --> S1["💰 Balance"]
        Lex --> S2["📋 Quote"]
        Lex --> S3["📄 Policy"]
        Lex --> S4["📊 Claim Status"]
        Lex --> S5["💸 Transfer"]
        Lex --> S6["📝 File Claim"]
        Lex --> S7["📞 Callback"]
        Lex --> S8["❓ FAQ"]
        Lex --> S9["👤 Agent"]
    end

    subgraph FOLLOWUP ["🔄 FOLLOW-UP"]
        S1 --> More{More help?}
        S2 --> More
        S3 --> More
        S4 --> More
        S5 --> More
        S6 --> More
        S7 --> More
        More -->|Yes| Lex
        More -->|No| End
    end

    subgraph EXIT ["👋 EXIT"]
        S8 --> End([🔴 End Call])
        S9 --> End
    end
```

---

## 📦 Lambda Functions

| Function | Purpose |
|----------|---------|
| `customerbyphone` | Authenticate customer by phone |
| `ProcessSelfService` | Process balance inquiries |
| `getinsurancequote` | Calculate insurance quotes |
| `sendquote` | Email quote to customer |
| `getpolicy` | Retrieve policy information |
| `CheckClaimStatus` | Check claim status |
| `selfservicebot` | Handle transfers, claims, callbacks |

---

## 🤖 Lex Bots

| Bot | Purpose |
|-----|---------|
| `selfserbot` | Main intent recognition |
| `travel` | FAQ handling |

---

## 📝 Supported Intents

- `checkbalance` - Check account balance
- `GetInsuranceQuote2` - Get insurance quote
- `GetPolicyInfo` - Get policy information
- `claimstatus` - Check claim status
- `TransferMoney` - Transfer funds
- `FileInsuranceClaim` - File new claim
- `RequestCallback` - Request callback
- `FAQQuery` - Ask FAQ questions
- `GetSupport` - Transfer to agent
- `EndCall` - End the call
📸 Screenshots
DynamoDB Tables
<img width="316" height="635" alt="image" src="https://github.com/user-attachments/assets/1fe471b0-aae4-444a-b8e3-f8459060d3d3" />

Quote Email

<img width="1035" height="730" alt="image" src="https://github.com/user-attachments/assets/817a4fa1-8b78-46b4-ab83-5ee3130fa63f" />

Quote Completion Form

<img width="883" height="947" alt="image" src="https://github.com/user-attachments/assets/14371daa-e625-498b-a10a-e09fbaeb3fd0" />
Purchase Confirmation
<img width="783" height="825" alt="image" src="https://github.com/user-attachments/assets/3ff4b057-0305-46f1-8d34-21cf54885e94" />

Create Lex Bots - Set up selfserbot and travel bots
Deploy Lambda Functions - Deploy all Lambda functions
Create DynamoDB Tables - Create the 8 required tables
Configure S3 - Host the quote website on S3
Setup SES - Configure email sending

Purchase Confirmation


