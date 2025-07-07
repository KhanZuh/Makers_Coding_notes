# Key Terms

## Deployment
**Definition: Moving software from your local computer to the internet for public access**

Simple Example:

- You build a website on your laptop
- You upload it to a web server so anyone can visit it
- You make it more stable/reliable for real users

Real-world analogy: Like moving from cooking in your home kitchen to opening a restaurant - you need to scale up and make it professional.

## Abstraction
**Definition: Hiding complex details to focus on what matters**

Car Example (from text):

```
Driver's View (Abstraction Layer):
┌─────────────────────────┐
│ Steering Wheel          │
│ Gas Pedal               │
│ Brake Pedal             │
│ Gear Shift              │
└─────────────────────────┘
            │
    (hides complexity)
            │
┌─────────────────────────┐
│ Engine mechanics        │
│ Transmission details    │
│ Fuel injection system   │
│ Electrical systems      │
└─────────────────────────┘
```
Programming Example:

When you use print("Hello"), you don't need to know how the computer displays pixels on screen
The programming language abstracts away the hardware details

## Cloud Computing
**Definition: Renting computing power instead of buying/maintaining your own servers**

Traditional vs Cloud Comparison:

```
On-Premise (Old Way):

Your Company:
┌─────────────────────────┐
│ Buy servers ($$$)       │
│ Set up physical space   │
│ Install & configure     │
│ Maintain & repair       │
│ Pay electricity bills   │
│ Hire IT staff           │
└─────────────────────────┘
Cloud Computing (New Way):

Your Company:          Cloud Provider (AWS):
┌─────────────────┐    ┌─────────────────────────┐
│ Upload your app │───▶│ Runs 2-12 copies        │
│ Pay monthly fee │    │ Scales automatically    │
│ Focus on code   │    │ Handles maintenance     │
└─────────────────┘    │ Manages infrastructure  │
                       └─────────────────────────┘
```

Real-world analogy: Like renting office space instead of buying a building - you pay for what you use and someone else handles maintenance.

## CI/CD (Continuous Integration/Continuous Deployment)
**Definition: Automated process for testing and deploying code**

```
The CI/CD Pipeline:

Developer → Code Repository → Automated Tests → Deployment
    │              │                │              │
    │              │                │              │
    ▼              ▼                ▼              ▼
Write code    Push to Git    Run tests, check   Deploy to
on laptop     (like GitHub)   style, security   live website
Detailed Flow:
```

1. Developer pushes code to repository (like GitHub)
2. CI system activates and downloads the new code
3. Automated tests run:
4. Unit tests (does the code work?)
5. Style checks (is the code formatted properly?)
6. Security scans (any vulnerabilities?)
7. Coverage tests (are enough tests written?)
8. If tests pass: CD automatically deploys to production
9. If tests fail: Deployment is blocked, developer gets notified

Benefits:
- Catches bugs before users see them
- Reduces manual deployment errors
- Enables faster, more frequent releases
- Gives confidence to make changes

Real-world analogy: Like a factory assembly line with quality control checkpoints - nothing goes to customers without passing all the checks.


### How These Concepts Work Together
```
Developer writes code (abstraction - using high-level languages)
        ↓
CI/CD pipeline tests and validates
        ↓
Deployment to cloud infrastructure (abstraction - don't manage servers)
        ↓
Users access the application via internet
```




