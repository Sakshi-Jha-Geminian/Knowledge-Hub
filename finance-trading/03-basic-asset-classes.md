# Basic Asset Classes

## Introduction

In the previous chapters, we learned:

- What finance is
- What financial markets are
- Why financial markets exist
- Who participates in them
- What trading means
- What an order is at a high level
- Why market data and trading systems matter

Now we need to answer one of the most important questions:

> **What exactly are people buying and selling in financial markets?**

The answer is:

> **Financial assets and financial contracts.**

These are grouped into different **asset classes**.

Understanding asset classes is essential for a Trading DevOps/SRE engineer because different asset classes can have:

- Different trading behavior
- Different market hours
- Different liquidity
- Different order types
- Different execution mechanisms
- Different market-data patterns
- Different risk characteristics
- Different system architectures
- Different monitoring requirements

For example, monitoring an equity trading system is not necessarily the same as monitoring an FX or derivatives trading system.

---

# 1. What Is an Asset Class?

An **asset class** is a group of financial instruments that have similar characteristics.

Common asset classes include:

```text
Financial Assets
│
├── Equities
├── Fixed Income
├── Cash / Money Market
├── Foreign Exchange
├── Commodities
└── Alternatives
```

Financial derivatives are often discussed separately because they are contracts whose value is derived from another asset or variable:

```text
Derivatives
│
├── Futures
├── Options
├── Forwards
└── Swaps
```

There are different ways to classify financial instruments, and classifications can vary between institutions.

For this repository, we will use a practical classification that is useful for understanding trading systems.

# 2. The Most Important Mental Model

Before learning individual asset classes, remember this:

```text
ASSET
  ↓
Something with financial/economic value

ASSET CLASS
  ↓
Category of similar financial instruments

INSTRUMENT
  ↓
Specific tradable financial product

ORDER
  ↓
Instruction to buy or sell that instrument

TRADE
  ↓
Successful execution of compatible orders
```

For example:

```text
Asset Class
    ↓
Equity
    ↓
Instrument
    ↓
Apple stock
    ↓
Order
    ↓
Buy 100 shares
    ↓
Execution
    ↓
Trade
```

This hierarchy will be extremely useful later.

# 3. Why Do Asset Classes Matter to an SRE?

Suppose your monitoring system reports:

```text
Orders/sec = 2,000
```

That number alone means very little.

You need context.

Is it:

- 2,000 equity orders/sec?

or:

- 2,000 FX orders/sec?

or:

- 2,000 options orders/sec?

The expected workload can be completely different.

Similarly:

```text
Market Data Messages = 500,000/sec
```

may be normal during a particular market event but abnormal during another period.

Therefore:

> **An SRE should understand what is being traded before deciding what "normal" system behavior looks like.**

# 4. Equity

The first major asset class is **equity**.

Equity represents ownership in a company.

The most familiar example is a **share of stock**.

Suppose a company has:

```text
10,000,000 shares
```

and you own:

```text
1,000 shares
```

You own a small portion of the company.

Conceptually:

```text
Company
   │
   ├── Share 1
   ├── Share 2
   ├── Share 3
   ├── ...
   └── Share 10,000,000
```

Owning shares generally gives the investor an ownership interest, subject to the rights associated with that security.

# 5. What Is a Stock?

A **stock** generally represents an ownership interest in a company.

For example:

```text
Company: XYZ
Share Price: $100
```

If you own:

```text
100 shares
```

the market value of those shares is approximately:

```text
100 × $100 = $10,000
```

The market price can change continuously during trading.

For example:

```text
10:00 → $100
10:01 → $101
10:02 → $99
10:03 → $103
```

This price movement creates trading activity.

# 6. Equity Trading Example

Imagine a trader wants to buy:

```text
1,000 shares
```

The simplified flow is:

```text
Trader
  ↓
Order: Buy 1,000 shares
  ↓
Trading Application
  ↓
Order Management System
  ↓
Risk Checks
  ↓
Order Routing
  ↓
Exchange / Trading Venue
  ↓
Matching
  ↓
Execution
  ↓
Trade
```

Each stage may produce telemetry.

For example:

- Order received
- Order validated
- Risk check passed
- Order routed
- Order acknowledged
- Order partially filled
- Order fully filled
- Trade confirmed

These events are important for monitoring.

# 7. Equity Monitoring Considerations

For equity trading systems, useful monitoring signals can include:

### Technical

- CPU
- Memory
- Network
- Database latency
- Application latency
- Queue depth
- Thread pools
- Connection counts

### Trading

- Orders/sec
- Trades/sec
- Order rejection rate
- Fill rate
- Execution latency
- Market-data latency
- Order acknowledgements
- Exchange connectivity

### Business

- Trading volume
- Active instruments
- Position changes
- Notional value
- Market session
- Trading calendar

# 8. Fixed Income

The second major asset class is **fixed income**.

The simplest example is a **bond**.

A bond is generally a debt instrument.

Instead of owning part of a company, the investor is generally lending money to an issuer under defined terms.

# 9. Equity vs Bond

This distinction is extremely important.

### Equity

```text
Investor
   │
   │ Money
   ▼
Company

Investor receives:
Ownership interest
```

### Bond

```text
Investor
   │
   │ Money
   ▼
Issuer

Investor receives:
Debt claim / contractual payments
```

Memory trick:

**Stock = Ownership**

**Bond = Lending**

This is simplified, but it is an excellent foundation.

# 10. What Is a Bond?

Suppose a company needs:

```text
$100 million
```

It can borrow money from investors by issuing bonds.

An investor purchases the bond.

Conceptually:

```text
Investor
   │
   │ $1 million
   ▼
Company
   │
   │ Contractual obligation
   ▼
Investor
```

The bond has terms such as:

- Principal
- Interest/coupon
- Maturity
- Payment schedule

# 11. Principal

The **principal** is the amount associated with the debt instrument that is generally due according to its terms.

For example:

```text
Bond Principal = $10,000
```

The investor may receive interest according to the bond's terms and repayment of principal at maturity, subject to the issuer's ability and the instrument's terms.

# 12. Coupon

A bond may make periodic interest payments.

This interest payment is commonly called the **coupon**.

For example:

```text
Principal = $10,000
Coupon Rate = 5%
```

A simplified annual coupon calculation would be:

```text
$10,000 × 5%
= $500 per year
```

Actual payment frequency and conventions can vary.

# 13. Maturity

**Maturity** is the date when a debt instrument reaches the end of its contractual term.

For example:

```text
Issue Date
   ↓
1 Jan 2026

Maturity
   ↓
1 Jan 2031
```

At maturity, the issuer generally repays the relevant principal according to the instrument's terms.

# 14. Fixed Income Monitoring

Fixed-income systems may have monitoring requirements around:

- Pricing
- Market data
- Order volume
- Trade volume
- Yield calculations
- Interest-rate data
- Settlement
- Reference data
- Maturity information

Compared with equities, fixed-income markets can have different liquidity and trading patterns.

Therefore:

> **Do not blindly reuse equity thresholds for fixed-income systems.**

# 15. Cash and Money Market Instruments

Cash is often treated separately from traditional investment asset classes.

Examples include:

- Cash
- Bank deposits
- Treasury bills
- Commercial paper
- Other short-term instruments

These instruments are generally associated with short-term liquidity and capital preservation, although risk varies by instrument.

For trading systems, money-market workflows may involve:

- Short maturities
- Interest-rate calculations
- Settlement processing
- Reference data
- Payment processing

# 16. Foreign Exchange

**Foreign Exchange**, commonly called **FX** or **Forex**, is the market for trading currencies.

Instead of buying a company, you are exchanging one currency against another.

For example:

```text
EUR/USD = 1.1000
```

This broadly means:

```text
1 EUR ≈ 1.10 USD
```

The exact interpretation and market convention depend on the currency pair.

# 17. Currency Pair

FX is generally quoted as a pair.

Examples:

- EUR/USD
- GBP/USD
- USD/JPY
- USD/INR

A pair contains:

```text
Base Currency / Quote Currency
```

For:

```text
EUR/USD
```

- EUR = Base Currency
- USD = Quote Currency

# 18. FX Example

Suppose:

```text
EUR/USD = 1.1000
```

A simplified interpretation is:

```text
1 EUR = 1.10 USD
```

If the price moves to:

```text
1.1200
```

the EUR has strengthened relative to USD in this quoted pair.

# 19. Why FX Is Important for Monitoring

FX markets are particularly interesting from an SRE perspective because they can involve:

- Global trading
- Multiple time zones
- High message rates
- Real-time pricing
- Multiple liquidity providers
- Electronic execution
- Low-latency requirements
- Market-data feeds

A global FX platform may receive market data from multiple sources.

```text
Liquidity Provider A ──┐
Liquidity Provider B ──┤
Liquidity Provider C ──┼──► FX Trading System
Liquidity Provider D ──┤
Liquidity Provider E ──┘
```

Monitoring therefore needs to understand:

- Feed availability
- Feed latency
- Price freshness
- Connection status
- Message rate
- Price discrepancies

---
# 20. Commodities

Commodities are another major category.

Examples include:

### Energy

- Crude oil
- Natural gas

### Metals

- Gold
- Silver
- Copper

### Agriculture

- Wheat
- Corn
- Soybeans

Commodities can be traded through different market structures and instruments.

# 21. Physical Commodity vs Financial Contract

This is an important distinction.

Suppose you trade crude oil.

You could be dealing with:

- Physical commodity

or

- Financial contract referencing crude oil

For example, a futures contract may reference the price of crude oil without the trader simply buying a barrel of oil and storing it.

Therefore:

> **Trading a commodity-related financial instrument does not necessarily mean physically possessing the commodity.**

# 22. Commodity Trading and Monitoring

Commodity systems can be affected by:

- Market hours
- Global events
- Weather
- Supply disruptions
- Geopolitical events
- Inventory data
- High volatility
- Sudden volume changes

From an SRE perspective, this can create sudden workload changes.

Example:

```text
Unexpected Market Event
        ↓
Volatility ↑
        ↓
Trading Activity ↑
        ↓
Order Volume ↑
        ↓
Market Data ↑
        ↓
System Load ↑
```

This is exactly the type of pattern predictive monitoring should eventually detect.

# 23. Mutual Funds

A **mutual fund** pools money from multiple investors.

Conceptually:

```text
Investor A ──┐
Investor B ──┤
Investor C ──┼──► Mutual Fund
Investor D ──┤
Investor E ──┘
                   │
                   ▼
              Investments
```

The fund can invest in:

- Stocks
- Bonds
- Money-market instruments
- Other securities

The investors own units/shares of the fund.

# 24. ETF

ETF stands for:

**Exchange-Traded Fund**

An ETF is a fund whose shares are traded on an exchange.

This creates an interesting combination:

```text
Fund
  +
Exchange Trading
```

For example:

```text
ETF
 ↓
Contains multiple assets
 ↓
ETF shares trade on exchange
```

This means ETF systems can involve both:

- Fund-related processes
- Exchange-based trading processes

# 25. Mutual Fund vs ETF

A simplified comparison:

### Mutual Fund

- Pooled investment: Yes
- Represents portfolio: Yes
- Exchange-traded throughout session: Generally No
- Intraday trading: Usually not like exchange-traded shares
- Price behavior: Typically based on NAV calculation
- Trading infrastructure: Fund processing

### ETF

- Pooled investment: Yes
- Represents portfolio: Yes
- Exchange-traded throughout session: Yes
- Intraday trading: Yes
- Price behavior: Market price changes intraday
- Trading infrastructure: Exchange + fund infrastructure

The exact structure and rules vary.

For monitoring, ETFs can create additional real-time trading workloads.

# 26. What Is NAV?

NAV means:

**Net Asset Value**

At a simplified level:

```text
NAV = (Assets - Liabilities) / Number of Units
```

For example:

```text
Assets = ₹100 crore
Liabilities = ₹5 crore
Units = 10 crore

NAV = ₹95 / unit
```

Actual NAV calculations can involve more complex valuation and accounting rules.

NAV is especially important in funds.

# 27. Derivatives

Now we reach one of the most important categories for trading systems:

**Derivatives**

A derivative is a financial contract whose value is derived from an underlying asset, rate, index, or other variable.

Examples:

- Futures
- Options
- Forwards
- Swaps

The key word is:

**Underlying**

# 28. What Is an Underlying?

Suppose we have:

```text
Apple stock
```

and a derivative contract whose value depends on Apple stock.

Then:

```text
Apple stock
    ↓
Underlying
    ↓
Derivative value
```

Another example:

```text
Crude oil price
      ↓
Underlying
      ↓
Oil futures contract
```

Another:

```text
Interest rate
      ↓
Underlying variable
      ↓
Interest-rate derivative
```

# 29. Why Do Derivatives Exist?

Derivatives can be used for:

### Hedging

Reducing or managing risk.

### Speculation

Taking a position based on an expected market movement.

### Arbitrage

Attempting to benefit from price differences between related markets, subject to costs and risks.

### Exposure Management

Adjusting exposure without necessarily buying or selling the underlying asset directly.

# 30. Futures

A **futures contract** is a standardized derivative contract traded on an organized venue in many markets.

It generally creates an obligation to transact according to specified contract terms.

For example:

```text
Commodity
    ↓
Futures Contract
    ↓
Future price exposure
```

Futures can exist on:

- Equity indexes
- Commodities
- Interest rates
- Currencies
- Other underlying assets

# 31. Futures Example

Imagine a simplified crude-oil futures contract.

A trader expects the price to increase.

They take a position in a futures contract.

If the market moves in their favor, the position can generate a gain.

If it moves against them, the position can generate a loss.

The actual mechanics of margin, daily settlement, contract specifications, and obligations are more detailed.

# 32. Options

An **option** is a derivative contract that gives the holder a right, but generally not an obligation, to buy or sell an underlying asset at specified terms.

The two basic types are:

- Call
- Put

# 33. Call Option

A call option generally gives the holder the right to **buy** the underlying at a specified price, subject to the contract terms.

Memory trick:

> **Call → Right to Buy**

# 34. Put Option

A put option generally gives the holder the right to **sell** the underlying at a specified price, subject to the contract terms.

Memory trick:

> **Put → Right to Sell**

# 35. Strike Price

The **strike price**, also called the exercise price, is the price specified in an option contract at which the underlying can be bought or sold under the contract terms.

Example:

```text
Current Stock Price = $100
Call Option Strike = $110
```

The option contract contains the right to buy at:

```text
$110
```

subject to its terms.

# 36. Option Premium

The buyer of an option generally pays a price for the option.

This is called the:

**Premium**

For example:

```text
Option Premium = $5
```

The premium is paid to obtain the rights specified by the option contract.

# 37. Expiration

Options have an expiration date.

For example:

```text
Option
  ↓
Expiration
  ↓
31 Dec 2026
```

At or after expiration, the option's rights are handled according to the contract's rules.

Expiration dates can create unusual trading activity.

This is important for monitoring.

# 38. Why Derivatives Are Important for SREs

Derivative systems can involve additional complexity.

Examples:

- Contract metadata
- Expiration
- Strike prices
- Contract multipliers
- Margin
- Position limits
- Risk calculations
- Pricing models
- Greeks
- Settlement
- Clearing

Therefore:

```text
Derivative Trading
      ↓
Order
      ↓
Risk
      ↓
Pricing
      ↓
Execution
      ↓
Position
      ↓
Margin
      ↓
Settlement
```

This creates many potential monitoring points.

# 39. Forwards

A **forward contract** is a customized agreement between parties to transact an underlying asset or value at a future date under agreed terms.

Unlike standardized exchange-traded futures, forwards are often traded **over the counter (OTC)**.

Simplified:

```text
Party A
   ↕
Customized Contract
   ↕
Party B
```

Because forwards can be customized, their operational workflows can differ significantly from standardized exchange-traded products.

# 40. Swaps

A swap is a derivative contract in which parties exchange specified cash flows according to agreed terms.

Examples include:

- Interest-rate swaps
- Currency swaps

A simplified example:

```text
Party A
   │
   │ Cash Flow A
   ▼
Party B

Party B
   │
   │ Cash Flow B
   ▼
Party A
```

Swaps can be complex financial instruments and often involve significant lifecycle processing.

# 41. Exchange-Traded vs OTC

This distinction is very important.

## Exchange-Traded

The instrument is traded through an organized venue.

Examples can include:

- Listed stocks
- Many futures
- Many listed options

Simplified:

```text
Participant
    ↓
Exchange
    ↓
Matching
```

## OTC

OTC means:

**Over The Counter**

The transaction is negotiated directly between counterparties or through dealers/other intermediaries rather than through a centralized exchange order book.

Examples can include:

- Many forwards
- Many swaps
- Certain bonds and other instruments

Simplified:

```text
Party A
   ↕
Dealer / Counterparty
   ↕
Party B
```

# 42. Why Exchange vs OTC Matters to Monitoring

Exchange-traded systems may focus heavily on:

- Order books
- Matching
- Market data
- Exchange connectivity
- Execution latency

OTC systems may have stronger emphasis on:

- Counterparties
- Trade capture
- Confirmations
- Valuation
- Reference data
- Lifecycle events
- Settlement
- Reconciliation

Therefore:

> **The market structure influences the technology and monitoring architecture.**

# 43. Asset Class Comparison

### Equity
- Basic Idea: Ownership
- Example: Stock
- Typical Concern: Orders, execution, market data

### Fixed Income
- Basic Idea: Lending/debt
- Example: Bond
- Typical Concern: Pricing, yields, liquidity

### Cash/Money Market
- Basic Idea: Short-term funds
- Example: Treasury bill
- Typical Concern: Rates, settlement

### FX
- Basic Idea: Currency exchange
- Example: EUR/USD
- Typical Concern: Pricing, liquidity, feed latency

### Commodity
- Basic Idea: Commodity exposure
- Example: Gold, Oil
- Typical Concern: Volatility, volume, market data

### Mutual Fund
- Basic Idea: Pooled investment
- Example: Equity fund
- Typical Concern: NAV, subscriptions, redemptions

### ETF
- Basic Idea: Exchange-traded fund
- Example: Index ETF
- Typical Concern: Market price, NAV, orders

### Futures
- Basic Idea: Standardized derivative
- Example: Oil Future
- Typical Concern: Margin, expiry, execution

### Options
- Basic Idea: Right to buy/sell
- Example: Call Option
- Typical Concern: Greeks, strike, expiry

### Forward
- Basic Idea: Customized future transaction
- Example: FX Forward
- Typical Concern: Counterparty, settlement

### Swap
- Basic Idea: Exchange of cash flows
- Example: Interest-rate swap
- Typical Concern: Valuation, lifecycle, settlement

# 44. Asset Class → System Behavior

Different asset classes can produce different workloads.

### Equity

```text
Equity
   ↓
High order volume
   ↓
High message rate
   ↓
Low-latency execution
```

### FX

```text
FX
   ↓
Multiple liquidity providers
   ↓
Continuous pricing
   ↓
High market-data activity
```

### Derivatives

```text
Derivatives
   ↓
Contracts
   ↓
Pricing + Risk
   ↓
Margin + Position
   ↓
Settlement
```

### Funds

```text
Funds
   ↓
Subscriptions / Redemptions
   ↓
NAV
   ↓
Accounting
   ↓
Settlement
```

This is why a single generic monitoring strategy is not enough.

# 45. Asset Class → Monitoring Strategy

```text
Asset Class
     ↓
Trading Behavior
     ↓
Expected Workload
     ↓
System Behavior
     ↓
Monitoring Baseline
     ↓
Alert Threshold
```

For example:

```text
Equity Market Open
       ↓
Order Volume ↑
       ↓
Expected CPU ↑
       ↓
Expected Queue Depth ↑
```

An alert should not necessarily fire simply because CPU increased.

The monitoring system should understand:

> **Why did CPU increase?**

# 46. Business-Aware Threshold Example

Suppose:

```text
Normal CPU:
40–60%
```

At market open:

```text
CPU:
70–80%
```

A static alert:

```text
CPU > 70%
```

would generate an alert.

But if market open routinely causes CPU to reach 80%, this may be normal.

Instead:

```text
Market Closed
    ↓
CPU threshold = 70%

Market Open
    ↓
CPU threshold = 90%
```

This is a simplified example of **context-aware monitoring**.

# 47. Volume Patterns by Asset Class

Different instruments can have different trading patterns.

For example:

```text
Equities
    ↓
Market open / close may produce large activity
```

```text
FX
    ↓
Activity can follow global sessions
```

```text
Futures
    ↓
Activity depends on contract and session
```

```text
Options
    ↓
Activity can change around expiry and market events
```

```text
Bonds
    ↓
Liquidity can differ significantly by instrument
```

Therefore, when building predictive monitoring:

> **Baseline the system according to the business context.**

# 48. Asset Class and Market Data

Each asset class can generate different market-data characteristics.

For example:

```text
Equities
   ↓
Quotes + trades + order-book updates
```

```text
FX
   ↓
Bid/ask prices from multiple sources
```

```text
Derivatives
   ↓
Quotes + trades + contract data + pricing information
```

```text
Fixed Income
   ↓
Prices + yields + reference information
```

Monitoring can therefore include:

- Feed availability
- Feed latency
- Message throughput
- Data freshness
- Missing messages
- Sequence gaps
- Price anomalies

# 49. Asset Class and Risk

Different assets create different risk exposures.

For example:

```text
Equity
   ↓
Market risk
```

```text
Bond
   ↓
Interest-rate + credit risk
```

```text
FX
   ↓
Currency risk
```

```text
Commodity
   ↓
Commodity price risk
```

```text
Derivative
   ↓
Market + leverage + counterparty + model-related risks
```

The exact risk profile depends on the instrument and position.

This matters because risk systems are often critical components of trading architecture.

# 50. Asset Class and Position

Remember:

```text
Asset
   ↓
Position
   ↓
Risk
```

Example:

```text
Trader owns:
10,000 shares
      ↓
Equity position
      ↓
Market exposure
      ↓
Risk calculation
```

For derivatives:

```text
Trader owns:
1,000 options
      ↓
Option position
      ↓
Delta / Gamma / Vega / Theta
      ↓
Risk exposure
```

We will learn these concepts later.

# 51. The "Instrument" Concept

You will frequently hear the word:

**Instrument**

An instrument is a specific financial product, security, or contract that can be traded or otherwise referenced in financial systems.

Examples:

### Equity

```text
AAPL Stock
```

### Bond

```text
US Treasury 10-Year Bond
```

### FX

```text
EUR/USD
```

### Future

```text
Crude Oil Future
```

### Option

```text
AAPL Call Option
```

Each instrument usually has identifying attributes.

# 52. Instrument Identifier

Trading systems need a reliable way to identify instruments.

An instrument may have identifiers such as:

- Ticker
- ISIN
- CUSIP
- FIGI
- Exchange Symbol
- Internal Security ID

Different markets and organizations use different identifiers.

For example:

```text
Instrument
    ↓
Security ID
    ↓
Reference Data
    ↓
Trading System
```

This is called **reference data**.

Reference data will become an important monitoring topic later.

# 53. Reference Data

Reference data is information that describes financial instruments and other entities used by financial systems.

Examples:

- Instrument ID
- Currency
- Exchange
- Tick Size
- Lot Size
- Contract Size
- Maturity
- Strike Price
- Settlement Rules

If reference data is wrong, trading systems can behave incorrectly even when the infrastructure is perfectly healthy.

Example:

```text
Incorrect Instrument Configuration
            ↓
Incorrect Order Validation
            ↓
Order Rejection
            ↓
Business Impact
```

This is an important example of why monitoring cannot focus only on CPU and memory.

# 54. Asset Class → Business Impact

Consider the following incident:

Option reference data is incorrect.

The infrastructure may show:

```text
CPU = 40%
Memory = 50%
Network = Healthy
Pods = Healthy
```

Everything looks technically fine.

But:

```text
Option Orders
      ↓
Rejected
      ↓
Trading Impact
```

Therefore:

> **Technical health does not always mean business health.**

This is one of the most important lessons for a Trading SRE.

# 55. The Monitoring Pyramid for Asset Classes

Think of monitoring in layers:

```text
                    BUSINESS
                       ▲
                       │
                Trading Metrics
                       │
                Application Health
                       │
                Infrastructure
                       │
                    Hardware
```

For example:

```text
Hardware
  ↓
CPU / Memory

Infrastructure
  ↓
Kubernetes / VM / Network

Application
  ↓
OMS / Risk / Router

Trading
  ↓
Orders / Trades / Latency

Business
  ↓
Execution / Revenue / Exposure / Risk
```

The higher you go, the closer you are to actual business impact.

# 56. A Complete Example

Suppose we monitor an equity trading platform.

### Step 1 — Market Event

```text
Market Opens
```

### Step 2 — Business Activity

```text
Orders ↑
Trades ↑
Market Data ↑
```

### Step 3 — Application Load

```text
OMS throughput ↑
Risk checks ↑
Routing requests ↑
```

### Step 4 — Infrastructure Load

```text
CPU ↑
Memory ↑
Network ↑
Queue depth ↑
```

### Step 5 — Potential Problem

```text
Queue depth continues increasing
```

### Step 6 — Predictive Signal

```text
Queue growth rate ↑
```

The monitoring system predicts:

```text
Capacity exhaustion likely in 10 minutes
```

### Step 7 — Preventive Action

Potential actions could include:

- Scaling
- Load balancing
- Routing adjustment
- Capacity increase
- Investigation

This is the kind of business-aware predictive monitoring we ultimately want to build.

# 57. Beginner Memory Map

Remember asset classes like this:

```text
EQUITY
↓
Own a piece

BOND
↓
Lend money

FX
↓
Exchange currencies

COMMODITY
↓
Exposure to physical goods/resources

MUTUAL FUND
↓
Pool money

ETF
↓
Fund traded on exchange

FUTURE
↓
Standardized future contract

OPTION
↓
Right, not obligation

FORWARD
↓
Customized future contract

SWAP
↓
Exchange cash flows
```

# 58. The Most Important Distinction

Do not confuse:

```text
Asset Class
```

with:

```text
Instrument
```

For example:

```text
Equity
   ↓
Asset Class

AAPL Stock
   ↓
Instrument
```

Similarly:

```text
Derivative
   ↓
Category

AAPL Call Option
   ↓
Specific Instrument
```

# 59. What an SRE Should Ask

Whenever someone tells you:

> "We have a problem with trading."

Do not immediately start checking CPU.

First ask:

### 1. What asset class?

- Equity?
- FX?
- Fixed Income?
- Commodity?
- Derivative?

### 2. What instrument?

Which security or contract?

### 3. What market?

Which exchange or venue?

### 4. What session?

- Market open?
- Market close?
- After-hours?

### 5. What business activity?

- Orders?
- Trades?
- Market data?
- Settlement?

### 6. What changed?

- Volume?
- Latency?
- Rejections?
- Prices?
- Connectivity?

### 7. What technical component supports it?

- OMS?
- Risk?
- Market-data handler?
- Router?
- Database?
- Network?

This questioning framework will be extremely useful during incident triage.

# 60. Quick Revision

| Term | Meaning |
|--------|---------|
| Asset Class | Category of similar financial instruments |
| Equity | Ownership interest in a company |
| Stock | Tradable equity/security representing ownership interest |
| Fixed Income | Debt-related financial instruments |
| Bond | Debt instrument |
| Principal | Amount associated with a debt instrument that is generally repaid according to its terms |
| Coupon | Interest payment associated with a bond |
| Maturity | Date when a financial instrument reaches the end of its contractual term |
| Cash | Money/liquid funds |
| FX | Foreign exchange |
| Currency Pair | Two currencies quoted relative to each other |
| Commodity | Financial/physical exposure related to goods such as oil or gold |
| Mutual Fund | Pooled investment vehicle |
| ETF | Exchange-traded fund |
| NAV | Net Asset Value |
| Derivative | Contract whose value derives from an underlying |
| Underlying | Asset/rate/index/variable underlying a derivative |
| Future | Standardized derivative contract |
| Option | Contract giving a right, generally not an obligation |
| Call | Option giving right to buy |
| Put | Option giving right to sell |
| Strike | Specified exercise price of an option |
| Premium | Price paid for an option |
| Expiration | Date associated with the end of an option/contract term |
| Forward | Customized future transaction contract |
| Swap | Contract involving exchange of specified cash flows |
| OTC | Over-the-counter trading |
| Instrument | Specific financial product/security/contract |
| Reference Data | Information describing instruments and related entities |

# 61. Final Mental Model

The entire chapter can be reduced to this:

```text
                    FINANCIAL MARKETS
                           │
                           ▼
                    ASSET CLASSES
                           │
        ┌──────────────────┼──────────────────┐
        ▼                  ▼                  ▼
      Equity           Fixed Income          FX
        │                  │                  │
      Stock               Bond             Currency
        │                  │                  │
        └──────────────────┼──────────────────┘
                           │
                           ▼
                      DERIVATIVES
                           │
              ┌────────────┼────────────┐
              ▼            ▼            ▼
           Futures       Options      Swaps
              │            │            │
              └────────────┼────────────┘
                           ▼
                       INSTRUMENT
                           │
                           ▼
                         ORDER
                           │
                           ▼
                       EXECUTION
                           │
                           ▼
                         TRADE
                           │
                           ▼
                    POSITION / RISK
                           │
                           ▼
                    CLEARING / SETTLEMENT
```

And from the SRE perspective:

```text
Asset Class
     ↓
Instrument
     ↓
Trading Activity
     ↓
Orders / Market Data
     ↓
Trading Applications
     ↓
Infrastructure
     ↓
Metrics + Logs + Traces
     ↓
Monitoring
     ↓
Alerts
     ↓
Incident Response
     ↓
Business Impact
```

The most important lesson is:

> **Different financial products create different trading behavior, different workloads, different risks, and therefore different monitoring requirements.**

If you understand the asset class first, the technology becomes much easier to understand.

# 62. What Comes Next?

We now know:

```text
Finance
   ↓
Financial Markets
   ↓
Asset Classes
```
