# Finance Fundamentals

## Introduction

Before learning about trading systems, exchanges, orders, FIX, market data, or financial monitoring, we need to understand one simple question:

> **What is finance?**

If you are completely new to finance, do not worry about the terminology.

This chapter starts from the absolute beginning and gradually builds the concepts required to understand financial markets and, eventually, trading-system monitoring.

The objective is **not** to become a financial analyst or professional trader.

The objective is to understand the financial world well enough that when a trading application generates an alert, you can understand **what business activity that application is supporting and why the alert matters**.

---

# 1. What Is Finance?

At the simplest level:

> **Finance is the management, movement, investment, borrowing, lending, and use of money and financial resources.**

People and organizations constantly make decisions involving money.

For example:

### An individual

You earn:

```text
Salary
  ↓
Income
```

Then you may:

```text
Spend
Save
Invest
Borrow
```

### A company

A company may:

```text
Raise money
     ↓
Invest in the business
     ↓
Generate revenue
     ↓
Pay expenses
     ↓
Generate profit
```

### A government

A government may:

```text
Collect taxes
     ↓
Borrow money
     ↓
Spend money
     ↓
Fund infrastructure and services
```

All of these activities are part of the broader world of finance.

---

# 2. Why Does Finance Exist?

Imagine a world where:

* One person has extra money.
* Another person needs money.
* One company wants to raise capital.
* Another person wants to invest.
* A farmer wants to protect against changing commodity prices.
* A company wants to exchange one currency for another.

There needs to be a system that allows these needs to interact.

That system is the financial system.

A simplified idea is:

```text
People / Companies with Money
            │
            ▼
      Financial System
            │
            ▼
People / Companies Needing Money
```

The financial system helps money and financial resources move between participants.

---

# 3. The Financial System

The financial system is much larger than just stock markets.

It includes:

* Banks
* Stock markets
* Bond markets
* Currency markets
* Commodity markets
* Derivatives markets
* Investment firms
* Brokers
* Exchanges
* Clearing organizations
* Settlement systems
* Regulators
* Payment systems

A simplified view:

```text
                         FINANCIAL SYSTEM
                                │
       ┌────────────────────────┼────────────────────────┐
       ▼                        ▼                        ▼
     Banks                   Markets                Institutions
       │                        │                        │
       ▼                        ▼                        ▼
   Lending                 Trading                  Investment
   Deposits                Investing                Management
   Payments                Hedging                  Risk
```

---

# 4. Money vs Financial Assets

One of the first distinctions to understand is:

> **Money is not the same thing as a financial asset.**

Money can be used to buy things.

A financial asset represents some form of financial value or claim.

Examples include:

* Stocks
* Bonds
* Mutual funds
* ETFs
* Certain derivatives
* Cash and cash equivalents

For example:

```text
₹100,000 Cash
```

is different from:

```text
₹100,000 worth of Shares
```

The first is money.

The second represents ownership in financial assets.

The value of the shares can change.

---

# 5. What Is an Asset?

An asset is something that has economic value.

Examples outside financial markets include:

* House
* Car
* Land
* Machinery

Financial assets include:

* Stocks
* Bonds
* Cash
* Fund units
* Certain contracts

For our purposes:

> **A financial asset is an asset whose value is represented through a financial claim, ownership interest, or financial contract.**

---

# 6. Ownership vs Lending

This distinction helps explain many financial instruments.

Suppose a company needs ₹10 crore.

There are different ways it can obtain money.

## Option 1 — Sell Ownership

The company can issue shares.

An investor buys shares.

The investor now owns a portion of the company.

Conceptually:

```text
Investor
   │
   │ Money
   ▼
Company
   │
   │ Shares
   ▼
Investor
```

This is the basic idea behind **equity**.

---

## Option 2 — Borrow Money

The company could borrow money.

For example:

```text
Investor
   │
   │ Money
   ▼
Company
   │
   │ Promise to repay
   ▼
Investor
```

This is the basic idea behind **debt**, such as bonds.

Therefore:

> **Equity generally represents ownership. Debt generally represents borrowing/lending.**

This distinction becomes important when we study asset classes.

---

# 7. What Is Investing?

Investing generally means putting money into something with the expectation of receiving a future benefit or return.

For example:

```text
Today
₹100,000
   ↓
Investment
   ↓
Future value
₹110,000
```

The future outcome is not guaranteed.

It could also become:

```text
₹90,000
```

Therefore investing involves:

* Potential return
* Risk
* Time
* Uncertainty

---

# 8. What Is Trading?

Trading is the buying and selling of financial instruments.

For example:

```text
Buyer                         Seller
  │                             │
  │ Wants to buy                │ Wants to sell
  └──────────┐       ┌──────────┘
             ▼       ▼
               Market
                  │
                  ▼
                Trade
```

A trader may buy and later sell an asset.

The objective can vary depending on the participant.

For example:

* Investment
* Hedging
* Market making
* Speculation
* Risk management
* Portfolio management

We will study these in more detail later.

---

# 9. Investing vs Trading

These concepts overlap, but they are not identical.

## Investing

Usually focuses more on:

* Longer-term ownership
* Expected future value
* Portfolio objectives
* Risk and return

## Trading

Usually focuses more on:

* Buying and selling
* Execution
* Market conditions
* Price movements
* Liquidity
* Timing

Do not think of these as strict categories.

Someone can be an investor and also trade.

The important point is:

> **Trading is the activity of buying and selling, while investing is the broader process of allocating money toward future financial benefit.**

---

# 10. What Is a Financial Market?

A financial market is a system where financial instruments can be bought and sold.

Examples include:

* Stock markets
* Bond markets
* Foreign exchange markets
* Commodity markets
* Derivatives markets

At its simplest:

```text
Buyer
  │
  │ wants to buy
  ▼
Financial Market
  ▲
  │ wants to sell
  │
Seller
```

The market helps participants interact.

---

# 11. Why Do Financial Markets Exist?

Financial markets perform several important functions.

## 11.1 Connecting Buyers and Sellers

A market provides a place or mechanism for participants to trade.

```text
Buyer ──────┐
            ├──► Market ◄─── Seller
Buyer ──────┘
```

---

## 11.2 Price Discovery

Markets help determine prices through supply and demand.

If many people want to buy something and fewer people want to sell it, its price may increase.

If many people want to sell and fewer people want to buy, its price may decrease.

Simplified:

```text
More Demand
     ↓
Potentially Higher Price

More Supply
     ↓
Potentially Lower Price
```

Real markets are more complicated, but this is the basic idea.

---

## 11.3 Liquidity

Markets can make it easier to buy or sell assets.

A highly liquid asset generally has many buyers and sellers.

A less liquid asset may be harder to trade without affecting its price.

This matters enormously to trading systems.

Why?

Because the system may need to process a large number of transactions quickly.

---

## 11.4 Capital Formation

Financial markets help companies and governments raise money.

For example:

```text
Investors
    │
    │ Capital
    ▼
Company
    │
    ├── Expansion
    ├── Research
    ├── Hiring
    └── Infrastructure
```

---

## 11.5 Risk Management

Financial markets also allow participants to manage risk.

For example, derivatives can be used to reduce exposure to:

* Currency movements
* Interest rates
* Commodity prices
* Market prices

This concept becomes important when we study derivatives and risk management.

---

# 12. What Is Capital?

Capital is broadly money or financial resources available for use.

For example:

```text
Company has ₹50 crore
```

That money may be used for:

* Building infrastructure
* Hiring employees
* Research
* Expansion
* Acquisitions

In financial markets, the term capital can have several more specific meanings depending on context.

For now, remember:

> **Capital is financial resources that can be used to support economic activity or investment.**

---

# 13. What Is Revenue?

Revenue is the money a company generates from its normal business activities.

For example:

```text
Company sells products
        ↓
Customers pay ₹10 crore
        ↓
Revenue = ₹10 crore
```

Revenue is not the same as profit.

---

# 14. What Is an Expense?

An expense is money spent to operate a business.

Examples:

* Salaries
* Rent
* Cloud infrastructure
* Electricity
* Software
* Marketing
* Equipment

For example:

```text
Revenue
₹10 crore

Expenses
₹7 crore
```

The difference contributes to profit before considering other items.

---

# 15. What Is Profit?

At a very basic level:

```text
Profit = Revenue - Expenses
```

Example:

```text
Revenue = ₹10 crore
Expenses = ₹7 crore

Profit = ₹3 crore
```

This is a simplified representation.

Real financial statements contain many more components.

For a trading SRE, you generally do not need deep accounting knowledge to understand trading-system monitoring.

---

# 16. What Is Loss?

A loss can occur when expenses or negative financial outcomes exceed gains.

For a simple business example:

```text
Revenue = ₹5 crore
Expenses = ₹7 crore

Loss = ₹2 crore
```

In trading, losses can also occur when an asset is sold for less than its purchase price.

Example:

```text
Bought at ₹1,000
Sold at ₹900

Loss = ₹100
```

---

# 17. What Is Return?

Return describes the gain or loss generated from an investment.

Suppose:

```text
Initial investment = ₹10,000
Final value = ₹11,000
```

The gain is:

```text
₹1,000
```

A simple percentage return is:

```text
Return = (Gain / Initial Investment) × 100

       = (1,000 / 10,000) × 100

       = 10%
```

In real finance, return calculations can become more sophisticated.

For our foundation, remember:

> **Return tells us how an investment performed.**

---

# 18. What Is Risk?

Risk is the possibility that an outcome will differ from what was expected, including the possibility of loss.

For example:

```text
Expected:
₹100,000 → ₹110,000

Possible:
₹100,000 → ₹90,000
```

The uncertainty represents risk.

Risk exists throughout trading systems.

Examples:

* Market risk
* Credit risk
* Liquidity risk
* Operational risk
* Technology risk
* Counterparty risk

Later, we will connect these to monitoring.

---

# 19. What Is Liquidity?

Liquidity describes how easily an asset can be bought or sold without causing a significant price impact.

A highly liquid market generally has:

* Many buyers
* Many sellers
* High trading activity
* Relatively easy execution

A less liquid market may have:

* Fewer buyers
* Fewer sellers
* Lower trading activity
* Larger price impact when trading

Think of it this way:

> **Liquidity = How easily can I trade without significantly disturbing the price?**

---

# 20. What Is Volume?

Volume represents the amount of trading activity during a period.

Depending on context, volume might mean:

* Number of orders
* Number of shares traded
* Number of contracts
* Number of transactions
* Value of transactions

For example:

```text
10:00 AM
1,000 orders

11:00 AM
1,500 orders

12:00 PM
800 orders
```

Volume changes over time.

This is extremely important for monitoring.

---

# 21. What Is Volatility?

Volatility describes how much and how quickly a price or market variable changes.

Imagine two assets.

### Asset A

```text
₹100
₹101
₹100
₹102
₹101
```

Small changes.

### Asset B

```text
₹100
₹120
₹90
₹130
₹80
```

Large changes.

Asset B is much more volatile.

A simple memory trick:

> **Volatility = How much the value moves around.**

---

# 22. What Is a Price?

Price is the amount of money required to buy or sell something at a particular point in time under specific market conditions.

For example:

```text
Stock price = ₹500
```

But in a live market, there is not necessarily just one "price."

There can be:

* Bid price
* Ask price
* Last traded price
* Opening price
* Closing price
* High price
* Low price

These will become important later.

---

# 23. What Is Bid?

The **bid** is the price a buyer is willing to pay.

Example:

```text
Buyer is willing to pay ₹99
```

Bid:

```text
₹99
```

Think:

> **Bid = Buyer says "I will buy at this price."**

---

# 24. What Is Ask?

The **ask**, also called the offer, is the price at which a seller is willing to sell.

Example:

```text
Seller is willing to sell at ₹101
```

Ask:

```text
₹101
```

Think:

> **Ask = Seller says "I will sell at this price."**

---

# 25. Bid vs Ask

Suppose:

```text
Bid = ₹99
Ask = ₹101
```

Then:

```text
Buyer → ₹99

Seller → ₹101
```

There is a difference of:

```text
₹101 - ₹99 = ₹2
```

That difference is called the **spread**.

---

# 26. What Is Spread?

Spread is generally the difference between the best available ask and bid.

```text
Spread = Ask - Bid
```

Example:

```text
Bid = ₹99
Ask = ₹101

Spread = ₹2
```

A smaller spread generally indicates a tighter market.

A larger spread can indicate lower liquidity or higher uncertainty, among other factors.

For monitoring, unusual changes in spreads can sometimes be meaningful business signals.

---

# 27. What Is a Transaction?

A transaction is an exchange between parties.

In financial markets, this could involve:

```text
Money ↔ Financial Asset
```

For example:

```text
Buyer gives money
        ↕
Seller gives shares
```

A transaction becomes especially important to us because trading systems process huge numbers of such events.

---

# 28. What Is a Position?

A position represents an individual's or organization's exposure or holding in an asset or instrument.

For example:

```text
Own 1,000 shares of Company X
```

This represents a position.

A position can generally be:

* Long
* Short
* Flat

At a very basic level:

### Long

You own the asset or have positive exposure.

```text
Own shares
```

### Short

You have a negative exposure or have sold something you do not currently own, subject to the relevant market mechanics.

### Flat

No meaningful position.

Position management becomes especially important in risk systems.

---

# 29. What Is a Portfolio?

A portfolio is a collection of investments or positions.

For example:

```text
Portfolio
│
├── Company A shares
├── Company B shares
├── Government bonds
├── ETF
└── Commodity exposure
```

Think:

> **Position = One exposure**

> **Portfolio = Collection of exposures**

---

# 30. What Is an Investor?

An investor allocates money toward an asset or investment with the expectation of achieving some future financial outcome.

Examples:

* Individual investor
* Pension fund
* Mutual fund
* Insurance company
* Asset manager

---

# 31. What Is a Trader?

A trader participates in buying and selling financial instruments.

A trader may work for:

* Investment bank
* Brokerage
* Hedge fund
* Asset manager
* Proprietary trading firm
* Market-making firm

A trader may focus on:

* Execution
* Market opportunities
* Liquidity
* Risk
* Hedging
* Client orders

For our purposes, remember:

> **A trader interacts with trading systems to create, manage, and execute orders.**

---

# 32. What Is a Broker?

A broker helps facilitate transactions between buyers and sellers.

A simplified flow:

```text
Client
  ↓
Broker
  ↓
Market / Exchange
  ↓
Execution
```

Brokers can provide services such as:

* Order routing
* Market access
* Execution
* Account services

The exact responsibilities depend on the organization and market.

---

# 33. What Is an Exchange?

An exchange is an organized marketplace where financial instruments can be traded under defined rules.

Examples of exchange activities include:

* Matching buyers and sellers
* Publishing market information
* Managing trading rules
* Processing orders
* Reporting trades

From a technology perspective, an exchange is a critical external dependency for many trading systems.

Therefore:

```text
Exchange Connectivity
        ↓
Trading System Availability
        ↓
Ability to Execute Orders
```

---

# 34. What Is a Market Maker?

A market maker generally provides liquidity by continuously or frequently quoting prices at which they are willing to buy and sell.

Conceptually:

```text
Market Maker

Bid = ₹99
Ask = ₹101
```

They help create a market where participants can trade.

Market makers are important because their activity contributes to liquidity and market functioning.

---

# 35. Why These Concepts Matter to an SRE

At this point, you may wonder:

> "Why am I learning profit, liquidity, volume, bid, ask, and volatility if my job is DevOps?"

Because the technology is supporting the business.

Consider this alert:

```text
Order volume dropped by 80%
```

Without finance knowledge, you may think:

> "Application is broken."

With finance knowledge, you ask:

```text
Is the market open?
        ↓
Is today a trading day?
        ↓
Is this a normal session?
        ↓
Is volume normally low at this time?
        ↓
Are other trading systems also seeing low activity?
```

Now consider:

```text
Market-data latency increased
```

You need to understand that stale market information may affect trading decisions.

Or:

```text
Order rejection rate increased
```

You need to determine whether:

```text
Application Failure
```

or:

```text
Expected Risk Rejection
```

is occurring.

This is why domain knowledge matters.

---

# 36. Business Metrics vs Technical Metrics

A major concept for a Trading SRE is the difference between **technical health** and **business health**.

## Technical Metrics

Examples:

```text
CPU
Memory
Disk
Network
JVM
Database
Pods
Containers
```

These answer:

> **Is the technology healthy?**

---

## Business Metrics

Examples:

```text
Orders/sec
Trades/sec
Order rejection rate
Execution latency
Fill rate
Market-data freshness
Transaction volume
```

These answer:

> **Is the financial business process behaving normally?**

A system can be technically healthy but business-unhealthy.

Example:

```text
CPU = 30%
Memory = 40%
Pods = Healthy
Network = Healthy

BUT

Orders = 0
```

If the market is supposed to be active, that could be a serious incident.

---

# 37. The Most Important SRE Mental Model

Always connect:

```text
BUSINESS
   ↓
APPLICATION
   ↓
INFRASTRUCTURE
```

For example:

```text
Trading Business
      ↓
Orders
      ↓
OMS
      ↓
Application Servers
      ↓
CPU / Memory / Network
```

If something goes wrong at the infrastructure layer, it may affect the application.

If something goes wrong at the application layer, it may affect trading.

If something goes wrong with trading, it may create business impact.

---

# 38. Example: Connecting Finance to Technology

Suppose a market opens.

Normally:

```text
1,000 orders/sec
```

Today:

```text
3,000 orders/sec
```

The trading system receives much more traffic.

This may cause:

```text
Order Volume ↑
      ↓
Application Traffic ↑
      ↓
CPU ↑
      ↓
Queue Depth ↑
      ↓
Latency ↑
      ↓
Timeouts ↑
      ↓
Order Failures ↑
      ↓
Business Impact
```

This is a perfect example of why a Trading SRE needs both:

**finance knowledge + technical knowledge.**

---

# 39. Another Example: Market Holiday

Suppose the system normally receives:

```text
10,000 orders/min
```

But today:

```text
0 orders/min
```

A naive monitoring rule might say:

```text
Orders < 1,000
       ↓
CRITICAL ALERT
```

But suppose today is a market holiday.

Then:

```text
Market Closed
      ↓
No Normal Trading
      ↓
Orders = 0
      ↓
Expected
```

There is no incident.

This is why your future monitoring must understand **business context**.

---

# 40. The Finance-to-SRE Chain

Remember this chain:

```text
Financial Market
      ↓
Trading Activity
      ↓
Orders
      ↓
Trades
      ↓
Trading Applications
      ↓
Infrastructure
      ↓
Telemetry
      ↓
Monitoring
      ↓
Alerts
      ↓
Incident Response
      ↓
Reliability
```

This chain will appear repeatedly throughout this entire Finance & Trading section.

---

# 41. Beginner Memory Map

If you forget everything else from this chapter, remember these relationships:

```text
FINANCE
│
├── Money
│
├── Markets
│
├── Assets
│
├── Risk
│
└── Investment
```

Then:

```text
MARKET
│
├── Buyers
├── Sellers
├── Price
├── Liquidity
├── Volume
└── Trading
```

Then:

```text
TRADING
│
├── Order
├── Execution
└── Trade
```

Then:

```text
TRADING SYSTEM
│
├── OMS
├── Risk
├── Router
├── FIX
├── Exchange
└── Market Data
```

Finally:

```text
TRADING SRE
│
├── Metrics
├── Logs
├── Traces
├── Alerts
├── Incidents
├── Capacity
└── Predictive Monitoring
```

---

# 42. Quick Revision

| Term             | Simple Meaning                                           |
| ---------------- | -------------------------------------------------------- |
| Finance          | Management and movement of money and financial resources |
| Financial Market | System where financial instruments are traded            |
| Asset            | Something with economic value                            |
| Financial Asset  | Financial instrument or claim representing value         |
| Investment       | Putting money into something expecting future benefit    |
| Trading          | Buying and selling financial instruments                 |
| Capital          | Financial resources available for use                    |
| Revenue          | Money generated from business activities                 |
| Expense          | Cost of operating a business                             |
| Profit           | Gain after expenses                                      |
| Loss             | Negative financial outcome                               |
| Return           | Gain or loss from an investment                          |
| Risk             | Possibility of an unexpected outcome or loss             |
| Liquidity        | Ease of buying/selling without major price impact        |
| Volume           | Amount of trading activity                               |
| Volatility       | Degree of price movement                                 |
| Price            | Amount at which something can be bought/sold             |
| Bid              | Price a buyer is willing to pay                          |
| Ask              | Price a seller is willing to accept                      |
| Spread           | Difference between bid and ask                           |
| Transaction      | Exchange between parties                                 |
| Position         | Exposure/holding in an instrument                        |
| Portfolio        | Collection of positions/investments                      |
| Investor         | Participant allocating money toward investments          |
| Trader           | Participant involved in buying and selling               |
| Broker           | Facilitates market transactions                          |
| Exchange         | Organized marketplace for trading                        |
| Market Maker     | Participant providing buy/sell liquidity                 |

---

# 43. Final Mental Model

Think about the entire chapter as one story:

```text
People and organizations have MONEY
              ↓
They want to INVEST, BORROW, LEND or MANAGE RISK
              ↓
Financial MARKETS connect participants
              ↓
Financial ASSETS are bought and sold
              ↓
TRADING takes place
              ↓
Trading creates ORDERS
              ↓
Orders are EXECUTED
              ↓
Executions create TRADES
              ↓
Trades are processed, CLEARED and SETTLED
              ↓
Software systems support every step
              ↓
DevOps/SRE engineers monitor those systems
              ↓
Monitoring detects abnormal behavior
              ↓
Incident response restores the service
              ↓
Predictive monitoring tries to detect problems
BEFORE they affect trading
```

The key idea to remember is:

> **Finance explains the business. Trading explains the activity. Trading technology makes the activity possible. Monitoring tells us whether the technology is supporting that activity correctly.**

That relationship is the foundation for everything that follows in this repository.
