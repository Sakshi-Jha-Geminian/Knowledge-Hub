# Market Participants

## Introduction

We now know:

- What finance is
- What financial markets are
- What trading means
- What asset classes are
- What instruments are

The next question is:

> **Who are the people and organizations actually participating in these markets?**

A financial market is not just:

```text
Trader → Exchange → Trade
```

It is a much larger ecosystem.

A real trading environment can look more like:

```text
Investors
   ↓
Banks / Brokers
   ↓
Trading Systems
   ↓
Exchanges / Trading Venues
   ↓
Clearing
   ↓
Settlement
   ↓
Custody
   ↓
Reporting / Regulation
```

And behind these participants are hundreds or thousands of applications, databases, APIs, message queues, networks, market-data feeds, and operational processes.

For a Trading SRE, understanding **who does what** is critical because when something fails, you need to know:

> **Which participant is affected, which system supports them, and what business process is being interrupted?**

# 1. What Is a Market Participant?

A **market participant** is a person, organization, or institution that takes part in financial-market activities.

Participants can:

- Buy securities
- Sell securities
- Provide liquidity
- Execute orders
- Manage investments
- Manage risk
- Provide financing
- Provide market data
- Clear trades
- Settle trades
- Provide custody
- Regulate markets

Different participants have different goals.

For example:

```text
Investor
↓
Wants to invest money

Trader
↓
Wants to execute trades

Market Maker
↓
Wants to provide liquidity

Broker
↓
Wants to execute orders for clients

Exchange
↓
Wants to operate a trading venue

Clearing House
↓
Wants to manage clearing and counterparty risk

Custodian
↓
Wants to hold and service assets
```

# 2. The Simplest Market Ecosystem

Imagine you want to buy shares of a company.

You are the:

```text
Investor
```

You submit an order through a:

```text
Broker
```

The broker sends the order to a:

```text
Trading Venue / Exchange
```

The exchange matches your order with another participant.

Then the trade goes through:

```text
Clearing
↓
Settlement
↓
Custody
```

Simplified:

```text
Investor
   ↓
Broker
   ↓
Trading Venue
   ↓
Execution
   ↓
Clearing
   ↓
Settlement
   ↓
Custodian
```

This simple flow will become the foundation for understanding trading systems.

# 3. Major Categories of Participants

```text
Market Participants
│
├── Investors
│   ├── Retail Investors
│   ├── Institutional Investors
│   ├── Pension Funds
│   ├── Mutual Funds
│   ├── Insurance Companies
│   └── Sovereign Wealth Funds
│
├── Trading / Intermediaries
│   ├── Traders
│   ├── Brokers
│   ├── Dealers
│   ├── Market Makers
│   └── Investment Banks
│
├── Market Infrastructure
│   ├── Exchanges
│   ├── Trading Venues
│   ├── Clearing Houses
│   ├── Central Securities Depositories
│   └── Custodians
│
├── Information Providers
│   ├── Market Data Providers
│   ├── Reference Data Providers
│   └── Research Providers
│
└── Oversight
    ├── Regulators
    ├── Central Banks
    └── Self-Regulatory Organizations
```

Not every institution fits neatly into one category.

A large financial institution can perform several roles.

# 4. Retail Investor

A **retail investor** is an individual investing their own money.

Examples:

```text
Person A
↓
Buys 100 shares

Person B
↓
Buys an ETF

Person C
↓
Buys a bond
```

Retail investors usually access markets through:

- Brokers
- Banks
- Investment platforms
- Trading applications

They normally do not connect directly to an exchange's matching engine.

# 5. Retail Trading Example

Suppose an individual wants to buy 100 shares.

The flow may look like:

```text
Retail Investor
      ↓
Trading App
      ↓
Broker
      ↓
Order Management System
      ↓
Risk Checks
      ↓
Order Routing
      ↓
Exchange
      ↓
Execution
```

From an SRE perspective, every step may be monitored.

For example:

```text
Trading App
↓
API availability

Broker
↓
Order acceptance

Risk System
↓
Risk-check latency

Order Router
↓
Routing latency

Exchange Connection
↓
Connectivity

Execution
↓
Fill / rejection
```

# 6. Institutional Investor

An **institutional investor** is an organization that invests money on behalf of itself or others.

Examples include:

- Pension funds
- Mutual funds
- Insurance companies
- Asset managers
- Sovereign wealth funds
- Endowments
- Foundations

Institutional investors can trade much larger amounts than individual investors.

# 7. Why Institutional Investors Matter

Suppose a retail investor sends:

```text
100 shares
```

An institution might send:

```text
1,000,000 shares
```

The scale is very different.

Large institutional orders can create:

- High order volume
- Large notional values
- Complex execution strategies
- Market impact
- Risk exposure
- More sophisticated routing

Therefore:

> **Institutional trading systems often require extremely strong reliability, scalability, observability, and controls.**

# 8. Asset Manager

An **asset manager** manages investments on behalf of clients.

```text
Clients
   ↓
Money
   ↓
Asset Manager
   ↓
Investment Decisions
   ↓
Trades
   ↓
Portfolio
```

The asset manager may decide:

- Buy
- Sell
- Hold
- Rebalance
- Hedge

# 9. Portfolio Manager

A **Portfolio Manager (PM)** is responsible for managing a portfolio or investment strategy.

Example portfolio:

```text
Portfolio
│
├── Apple
├── Microsoft
├── Treasury Bonds
├── ETF
└── Cash
```

The PM decides how the portfolio should be positioned based on the investment strategy.

Example:

```text
Current Allocation

Equity = 60%
Bonds  = 30%
Cash   = 10%
```

The PM may decide:

- Increase Equity
- Decrease Bonds

That decision eventually creates trading activity.

# 10. Portfolio Manager → Trader

This distinction is very important.

```text
Portfolio Manager
        ↓
Investment Decision
        ↓
Trade Instruction
        ↓
Trader
        ↓
Execution
```

The PM may say:

```text
Buy 500,000 shares
```

The trader determines how to execute that instruction efficiently.

---
# 11. Trader

A **trader** is responsible for executing trades.

A trader may:

- Receive orders
- Analyze market conditions
- Select execution venues
- Manage order timing
- Monitor fills
- Modify orders
- Cancel orders
- Manage execution risk

The trader may interact with:

- Exchanges
- Brokers
- Market makers
- Liquidity providers
- Electronic trading systems

# 12. Trader vs Portfolio Manager

This distinction is extremely important.

### Portfolio Manager

Primarily decides:

> **What should we own or sell?**

### Trader

Primarily focuses on:

> **How should we execute the trade?**

Simplified:

```text
Portfolio Manager
        ↓
WHAT to trade?
        ↓
Trader
        ↓
HOW to execute?
```

This is simplified because actual organizational responsibilities vary.

# 13. Broker

A **broker** acts as an intermediary between clients and financial markets.

The broker may provide:

- Order execution
- Market access
- Trading platforms
- Research
- Reporting
- Account services

Simplified:

```text
Client
  ↓
Broker
  ↓
Market
```

# 14. Broker Example

Suppose:

```text
Investor
↓
Buy 100 shares
```

The investor may submit the order to the broker.

The broker's systems may perform:

```text
Order Validation
       ↓
Risk Checks
       ↓
Routing
       ↓
Execution
       ↓
Confirmation
```

If any component fails, the order may be delayed or rejected.

This is why broker systems are critical production systems.

# 15. Dealer

A **dealer** generally trades for its own account and may act as principal in transactions.

Simplified:

```text
Customer
   ↕
Dealer
```

The dealer may quote prices and transact with clients.

This is different from a pure agency broker, which generally acts on behalf of clients rather than taking the other side as principal.

# 16. Broker vs Dealer

Remember:

```text
BROKER
↓
Intermediary / Agent

DEALER
↓
May Trade as Principal
```

A firm can perform both brokerage and dealing activities.

# 17. Market Maker

A **market maker** provides liquidity by being willing to buy and/or sell an instrument, often by continuously or regularly quoting prices.

Example quote:

```text
Bid = $99.95
Ask = $100.05
```

The market maker is offering:

```text
Buy at $99.95
Sell at $100.05
```

subject to market conditions, strategy, and obligations.

# 18. Bid and Ask

These are extremely important terms.

### Bid

The price a participant is willing to pay to buy.

### Ask

The price at which a participant is willing to sell.

Example:

```text
Bid      Ask
$99.95   $100.05
```

The difference is the:

**Bid-Ask Spread**

```text
$100.05 - $99.95
= $0.10
```

# 19. Why Market Makers Matter

Market makers provide liquidity.

Without sufficient liquidity:

```text
Buyer
   ↓
Cannot easily find seller
```

With liquidity:

```text
Buyer ←→ Market Maker ←→ Seller
```

Market makers can therefore play an important role in efficient trading.

From an SRE perspective, market-making systems may have demanding requirements for:

- Low latency
- High availability
- Market-data processing
- Order generation
- Connectivity
- Risk controls

# 20. Liquidity Provider

A **liquidity provider** supplies tradable prices or liquidity to a market.

In electronic markets, a liquidity provider may continuously send:

- Bid
- Ask
- Quantity

Example:

```text
Bid:
100.00 × 500

Ask:
100.02 × 700
```

Meaning:

```text
Buy 500 at 100.00
Sell 700 at 100.02
```

# 21. Investment Bank

An **investment bank** is a financial institution involved in activities such as:

- Investment banking
- Capital markets
- Trading
- Sales
- Market making
- Research
- Advisory
- Financing

Large investment banks can contain enormous trading technology ecosystems.

```text
Investment Bank
│
├── Equity Trading
├── Fixed Income
├── FX
├── Commodities
├── Derivatives
├── Prime Brokerage
├── Risk
├── Operations
└── Technology
```

# 22. Prime Broker

A **prime broker** provides services to institutional clients, particularly hedge funds and sophisticated investors.

Services can include:

- Trade execution support
- Financing
- Securities lending
- Custody-related services
- Clearing support
- Reporting
- Risk management

```text
Hedge Fund
    ↓
Prime Broker
    ↓
Markets
```

# 23. Hedge Fund

A **hedge fund** is an investment vehicle that can use a wide variety of strategies.

Examples include:

- Long/Short
- Macro
- Relative Value
- Arbitrage
- Quantitative Strategies
- Event-Driven Strategies

Some hedge funds use highly automated systems.

```text
Market Data
   ↓
Strategy
   ↓
Order Generation
   ↓
Risk
   ↓
Execution
```

# 24. Algorithmic Trading

Algorithmic trading means using computer programs to execute trading strategies or orders.

Manual trading:

```text
Human
↓
Click
↓
Order
```

Algorithmic trading:

```text
Market Data
     ↓
Trading Algorithm
     ↓
Decision
     ↓
Order
     ↓
Execution
```

# 25. High-Frequency Trading

**High-Frequency Trading (HFT)** is a form of algorithmic trading characterized by extremely fast automated decision-making and execution.

HFT systems can be highly sensitive to:

- Latency
- Network performance
- Market-data delays
- CPU performance
- System jitter
- Connectivity

For such systems:

```text
Milliseconds matter.
```

# 26. Why HFT Matters to SRE

Imagine:

```text
Market Data Delay = 5 ms
```

That may sound tiny.

But for a latency-sensitive strategy, it can be significant.

Monitoring may include:

- Market Data Latency
- Order Processing Latency
- Network Latency
- Exchange Latency
- Queue Latency
- Application Latency

# 27. Exchange

An **exchange** is a marketplace where financial instruments are traded according to defined rules.

Examples:

- NYSE
- Nasdaq
- CME
- LSE
- Deutsche Börse
- HKEX
- SGX
- NSE
- BSE

# 28. What Does an Exchange Do?

Simplified workflow:

```text
Orders
  ↓
Exchange
  ↓
Matching Engine
  ↓
Execution
  ↓
Trade
```

An exchange may also provide:

- Market data
- Trading rules
- Connectivity
- Order-management interfaces
- Trade reporting
- Reference information

# 29. Matching Engine

The **matching engine** is a core component of many electronic trading venues.

Its job is to match compatible buy and sell orders.

```text
Buy Orders
    ↓
Matching Engine
    ↑
Sell Orders
```

Example:

```text
Buyer:
Buy 100 @ $100

Seller:
Sell 100 @ $100
```

A trade can be executed when the orders are compatible.

# 30. Why Matching Engines Are Critical

Imagine the matching engine stops:

```text
Orders
  ↓
X
Matching Engine
  ↓
No execution
```

Requirements:

- Extremely high availability
- Low latency
- High throughput
- Strong correctness
- Fault tolerance
- Careful change management

For an SRE, this is a critical system.

# 31. Trading Venue

Not every trading transaction occurs on a traditional exchange.

A **trading venue** is a broader concept covering systems or marketplaces where trades can occur.

Examples:

- Exchanges
- Alternative Trading Systems (ATS)
- Electronic Communication Networks (ECNs)
- Multilateral Trading Facilities (MTFs)
- Other regulated venues

# 32. ECN

ECN stands for:

**Electronic Communication Network**

It is an electronic system that facilitates matching or routing of buy and sell interests.

```text
Electronic
+
Participants
+
Orders
+
Matching / Execution
```

# 33. ATS

ATS means:

**Alternative Trading System**

An ATS facilitates transactions outside traditional exchange structures.

Important idea:

```text
Different Venue
↓
Different Connectivity
↓
Different APIs
↓
Different Operational Behavior
```

# 35. Market Data Is Extremely Important

Imagine a trading algorithm receives stale prices.

```text
Actual Market Price
       ↓
$100

Trading System Sees
       ↓
$95
```

The system may make incorrect decisions.

Therefore market-data monitoring is critical.

Useful metrics include:

- Feed Availability
- Feed Latency
- Message Rate
- Sequence Gaps
- Data Freshness
- Dropped Messages
- Connection Status

# 36. Reference Data Provider

A **reference data provider** supplies information describing financial instruments and related entities.

Examples:

- Instrument ID
- Ticker
- Currency
- Exchange
- Country
- Maturity
- Strike
- Contract Size

Reference data can be used by:

- Trading systems
- Risk systems
- Settlement systems
- Reporting systems
- Compliance systems

# 37. Clearing House

After a trade is executed, the process is not necessarily finished.

The trade generally needs to go through post-trade processing.

One important participant is the:

**Clearing House**

A clearing house helps manage the clearing process and counterparty risk for transactions within its clearing framework.

Simplified:

```text
Trade
 ↓
Clearing
 ↓
Settlement
```

# 38. Central Counterparty

A **Central Counterparty (CCP)** can become the buyer to every seller and the seller to every buyer for cleared trades, subject to the applicable clearing arrangement.

Simplified:

```text
Buyer
  ↓
 CCP
  ↑
Seller
```

Instead of participants directly relying on each other in the same way, the CCP structure helps centralize certain counterparty and clearing processes.

# 39. Why Clearing Matters

Clearing can involve:

- Trade validation
- Netting
- Margin
- Collateral
- Counterparty risk management
- Default management

If clearing systems fail:

```text
Trades
  ↓
Post-trade processing
  ↓
Blocked / Delayed
```

Therefore clearing infrastructure is business-critical.

# 40. Settlement

**Settlement** is the process through which the obligations created by a trade are completed.

A simplified securities example:

```text
Buyer
   ↓
Money
   ↓
Seller

Seller
   ↓
Security
   ↓
Buyer
```

The exact settlement process varies by asset class, market, and jurisdiction.

# 41. Custodian

A **custodian** provides safekeeping and servicing of financial assets for clients.

A custodian may handle activities such as:

- Asset safekeeping
- Settlement support
- Corporate actions
- Income processing
- Reporting
- Reconciliation

Simplified:

```text
Investor
   ↓
Custodian
   ↓
Assets
```

# 42. Central Securities Depository

A **Central Securities Depository (CSD)** provides infrastructure for holding securities in electronic form and supporting settlement-related processes.

Conceptually:

```text
Securities
   ↓
Central Securities Depository
   ↓
Settlement Infrastructure
```

Examples include market-specific central securities depositories.

# 43. Regulator

A **regulator** oversees financial markets and participants according to applicable laws and regulations.

Examples include:

- SEC
- FINRA
- FCA
- SEBI
- Other national or regional regulators

Regulators may focus on:

- Market integrity
- Investor protection
- Reporting
- Capital requirements
- Conduct
- Risk management
- Transparency

# 44. Why Regulators Matter to Technology Teams

Regulatory requirements can create technical requirements.

Example:

```text
Regulation
   ↓
Data Retention
   ↓
Logs
   ↓
Audit Trails
   ↓
Monitoring
```

Or:

```text
Regulatory Reporting
   ↓
Data Collection
   ↓
Processing
   ↓
Validation
   ↓
Submission
```

Therefore compliance systems can also become production-critical.

# 45. Central Bank

A **central bank** manages monetary policy and performs other functions depending on the country.

Examples include:

- Federal Reserve
- European Central Bank
- Reserve Bank of India
- Bank of England

Central banks can influence financial markets through:

- Interest-rate decisions
- Monetary policy
- Liquidity operations
- Currency-related activities
- Financial stability measures

Their decisions can create major changes in market activity.

# 46. Why Central Bank Events Matter to Monitoring

Suppose a major interest-rate announcement occurs.

```text
Central Bank Announcement
          ↓
Market Volatility ↑
          ↓
Orders ↑
          ↓
Market Data ↑
          ↓
System Load ↑
```

A monitoring system that does not understand this context may generate many false alerts.

This is a perfect example of:

> **Business-aware monitoring.**

# 47. Research Analyst

A research analyst analyzes companies, industries, markets, economies, or financial instruments.

Research can influence investment decisions.

Simplified:

```text
Research
   ↓
Analysis
   ↓
Investment Decision
   ↓
Trade
```

Research itself may not execute trades, but it can influence trading activity.

# 48. Sales

In financial institutions, sales teams can communicate with clients and help facilitate trading activity.

For example:

```text
Client
  ↓
Sales
  ↓
Trader
  ↓
Execution
```

This is particularly important in institutional markets.

# 49. Operations

Operations teams support the lifecycle of trades after execution.

They may handle:

- Trade confirmation
- Settlement
- Reconciliation
- Exceptions
- Corporate actions
- Break management

Simplified:

```text
Trade
 ↓
Operations
 ↓
Validation
 ↓
Settlement
 ↓
Reconciliation
```

# 50. Risk Management

Risk teams monitor and manage financial risk.

Examples include:

- Market risk
- Credit risk
- Liquidity risk
- Counterparty risk
- Operational risk

Trading systems often interact with risk systems before and after execution.

For example:

```text
Order
  ↓
Pre-trade Risk
  ↓
Approved?
  ├── No → Reject
  └── Yes
       ↓
     Execute
```

# 51. Compliance

Compliance teams ensure activities follow applicable rules, laws, and internal policies.

Examples include:

- Trade surveillance
- Regulatory reporting
- Market abuse monitoring
- Know Your Customer processes
- Transaction monitoring

Technology plays a major role in supporting these activities.

# 52. Trade Surveillance

Trade surveillance systems look for suspicious or unusual trading behavior.

Examples of patterns that may require investigation include:

- Unusual order behavior
- Potential market manipulation
- Suspicious trading patterns
- Abnormal transactions

Simplified:

```text
Trading Activity
      ↓
Surveillance System
      ↓
Pattern Detection
      ↓
Alert
      ↓
Investigation
```

This is another example of a system where monitoring and alerting are business-critical.

# 53. The Complete Market Ecosystem

```text
                     MARKET PARTICIPANTS
                           Investors
                              │
                              ▼
                    Asset Managers / Funds
                              │
                              ▼
                         Portfolio
                         Managers
                              │
                              ▼
                           Traders
                              │
                              ▼
                    Brokers / Dealers
                              │
                              ▼
                  Trading Venue / Exchange
                              │
                              ▼
                       Matching Engine
                              │
                              ▼
                            Trade
                              │
                              ▼
                         Clearing
                              │
                              ▼
                         Settlement
                              │
                              ▼
                          Custodian
```

Supporting the entire ecosystem:

- Market Data Providers
- Reference Data Providers
- Risk Systems
- Compliance Systems
- Regulators
- Central Banks
- Technology Teams
- Operations Teams

# 54. Market Participant → System Mapping

| Participant | What They Do | Systems They May Use |
|------------|-------------|----------------------|
| Retail Investor | Invests/trades | Trading app, broker APIs |
| Institutional Investor | Invests large amounts | OMS, EMS, portfolio systems |
| Portfolio Manager | Makes investment decisions | Portfolio management system |
| Trader | Executes trades | OMS, EMS, trading terminals |
| Broker | Provides market access | Order management, routing |
| Dealer | Trades as principal | Trading/risk systems |
| Market Maker | Provides liquidity | Pricing, quoting, execution |
| Investment Bank | Provides financial services | Large trading ecosystem |
| Hedge Fund | Runs investment strategies | OMS, EMS, algorithms |
| Exchange | Provides marketplace | Matching engine |
| Clearing House | Clears trades | Clearing/risk systems |
| Custodian | Holds assets | Custody systems |
| Market Data Provider | Distributes data | Feed distribution |
| Reference Data Provider | Provides instrument information | Reference-data platforms |
| Regulator | Oversees markets | Regulatory systems |
| Central Bank | Monetary functions | Policy and market systems |
| Operations | Supports trade lifecycle | Post-trade systems |
| Risk | Controls exposure | Risk engines |
| Compliance | Ensures compliance | Surveillance/reporting |

# 55. Why Participant Knowledge Matters During Incidents

Suppose an alert says:

```text
Order rejection rate = 30%
```

A beginner might immediately check:

- CPU
- Memory
- Disk

But a Trading SRE should first ask:

- Who is affected?
- Which asset class?
- Which venue?
- Which component?

Now the investigation becomes much more targeted.

# 56. Example Incident

```text
Alert:
Order rejection rate > 10%
```

Step 1:

```text
Order Rejections
       ↓
Only FX orders
```

Step 2:

```text
FX
 ↓
Only EUR/USD
```

Step 3:

```text
EUR/USD
 ↓
Only Liquidity Provider A
```

Step 4:

```text
LP A
 ↓
Connectivity problem
```

Step 5:

```text
Trading Router
 ↓
Unable to send orders to LP A
```

Now the incident is much clearer.

Instead of:

> Trading system is broken.

You can say:

> EUR/USD orders routed to Liquidity Provider A are failing because the router-to-provider connection is unavailable. Other FX instruments and providers are operating normally.

That is high-quality incident triage.

# 57. Participant Relationships

```text
INVESTOR
"What do I want to invest in?"
        ↓
PORTFOLIO MANAGER
"What should the portfolio hold?"
        ↓
TRADER
"How should I execute it?"
        ↓
BROKER / DEALER
"How do I access the market?"
        ↓
EXCHANGE / VENUE
"Where does the trade happen?"
        ↓
MATCHING ENGINE
"Which orders match?"
        ↓
CLEARING
"Who owes what?"
        ↓
SETTLEMENT
"Exchange the assets and money."
        ↓
CUSTODY
"Hold and service the assets."
```

# 58. The Technology Behind Participants

```text
                    Trading Ecosystem
                         Users
                           │
                           ▼
                    Trading Platform
                           │
             ┌─────────────┼─────────────┐
             ▼             ▼             ▼
            OMS           EMS          Risk
```

...continued through the ecosystem.

# 59. What the SRE Actually Cares About

You do not need to become a trader.

You need to understand enough finance to answer:

- What is happening?
- Why is it happening?
- Which business process is affected?
- Which system supports that process?
- What is normal?
- What is abnormal?
- How severe is the impact?
- Who should be contacted?

# 60. Business Context vs Technical Context

```text
Technical Context
CPU = 85%
Memory = 70%
Latency = 200 ms
Queue = 50,000
```

```text
Business Context
Market just opened
Trading volume is high
FX session overlap is occurring
Major economic announcement happened
Options are approaching expiration
Exchange is experiencing high traffic
```

Correct interpretation requires combining both.

# 61. Participant-Aware Monitoring

```text
Alert
 ↓
Affected Participant
 ↓
Institutional Clients
 ↓
Asset Class
 ↓
Equity
 ↓
Venue
 ↓
NYSE
 ↓
Component
 ↓
Order Router
```

This creates richer alert context and enables **business-aware observability**.

# 62. Participant-Based Incident Severity

Incident severity can depend on:

- Number of participants affected
- Type of participant
- Asset class
- Trading session
- Market conditions
- Financial exposure
- Duration
- Regulatory impact

# 63. A Practical SRE Question Set

When investigating a trading incident, ask:

- Who is affected?
- What are they trying to do?
- Which asset class?
- Which instrument?
- Which exchange or venue?
- Which trading session?
- How many orders and trades?
- Which system is involved?
- What changed?
- What is the business impact?

# 64. Memory Trick

> **Investors decide, portfolio managers allocate, traders execute, brokers connect, exchanges match, clearing manages obligations, settlement completes the exchange, and custodians hold the assets.**

# 65. Final Mental Model

```text
FINANCIAL MARKET
      ↓
Investors
      ↓
Asset Managers
      ↓
Portfolio Managers
      ↓
Traders
      ↓
Brokers / Dealers
      ↓
Exchanges / Trading Venues
      ↓
Matching Engine
      ↓
Trade
      ↓
Clearing
      ↓
Settlement
      ↓
Custody
```

Supported by:

- Market Data
- Risk
- Compliance
- Technology
- Monitoring
- SRE / DevOps

