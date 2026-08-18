# Financial Markets Terminology

## Introduction

Financial markets have their own language.

When you start working with trading systems, you will repeatedly hear terms such as:

- Market
- Instrument
- Security
- Asset
- Order
- Trade
- Position
- Portfolio
- Price
- Bid
- Ask
- Spread
- Volume
- Liquidity
- Volatility
- Notional Value
- Market Value
- P&L
- Exposure
- Margin
- Collateral
- Settlement
- Clearing
- Fill
- Execution
- Order Book
- Market Data
- Trading Session

At first, these terms can feel confusing because many of them sound similar.

The goal of this chapter is to make these terms so clear that when you see them in:

- Dynatrace
- Splunk
- Grafana
- Datadog
- Logs
- Dashboards
- Alerts
- Incident tickets
- Trading applications
- Production incidents

you immediately understand what the system is talking about.

---

# 1. The Most Important Mental Model

Before learning individual terms, remember this:

```text
ASSET
  ↓
INSTRUMENT
  ↓
ORDER
  ↓
EXECUTION
  ↓
TRADE
  ↓
POSITION
  ↓
PORTFOLIO
  ↓
P&L / EXPOSURE / RISK
```

This is not a complete trading lifecycle, but it is an excellent mental model.

For example:

```text
Stock
  ↓
Trading Instrument
  ↓
Buy Order
  ↓
Execution
  ↓
Trade
  ↓
Position
  ↓
Portfolio
  ↓
Profit / Loss
```

We will build every concept around this flow.

# 2. Financial Asset

An **asset** is something that has economic value.

Examples:

- Cash
- Stocks
- Bonds
- Currencies
- Commodities
- Real Estate
- Derivatives

In trading systems, we are mostly concerned with financial assets and financial instruments.

# 3. Financial Instrument

A **financial instrument** is a contract or financial asset that can have monetary value and can often be traded.

Examples:

- Stock
- Bond
- Option
- Future
- Currency
- ETF
- Swap

A useful way to think about it:

> **An instrument is something a trading system can identify, price, trade, and manage.**

# 4. Asset Class

An **asset class** is a broad category of financial assets/instruments with similar characteristics.

Common asset classes include:

- Equities
- Fixed Income
- Foreign Exchange
- Commodities
- Derivatives
- Cash / Money Markets

Example:

```text
Asset Class
    ↓
Equities
    ↓
Stock
    ↓
Apple Inc.
```

# 5. Security

A **security** is a financial instrument representing some financial right, ownership interest, debt claim, or other legally defined interest, depending on the instrument and jurisdiction.

Examples can include:

- Stocks
- Bonds
- Certain ETFs

Not every financial instrument should be casually treated as a "security" in every legal context.

For learning trading systems, the important distinction is:

```text
Financial Instrument
        ↓
May be a Security
        ↓
May be a Derivative
        ↓
May belong to another Financial Category
```

# 6. Issuer

The **issuer** is the entity that creates or issues a financial instrument.

For example:

```text
Company
   ↓
Issues Shares
   ↓
Investors
```

For a bond:

```text
Government / Company
        ↓
Issues Bond
        ↓
Investors
```

The issuer is important in reference data.

A trading system may store:

- Instrument ID
- Issuer
- Currency
- Exchange
- Country
- Maturity

# 7. Ticker Symbol

A **ticker symbol** is a short identifier used to represent a traded instrument on a particular market or platform.

Examples:

- AAPL
- MSFT
- TSLA

However, do not assume a ticker is globally unique.

The same or similar symbol can exist across different venues or markets.

# 8. Instrument ID

Trading systems often use an internal or standardized identifier instead of relying only on ticker symbols.

For example:

```text
Instrument ID
    ↓
Internal Database Identifier
```

It may be something like:

```text
INSTR-1002938
```

This is important because the same instrument may have:

- Different symbols
- Different venues
- Different currencies
- Different trading sessions

# 9. ISIN

**ISIN** stands for:

**International Securities Identification Number**

It is a standardized identifier used for securities.

Example format:

```text
USXXXXXXXXXX
```

The exact identifier depends on the security.

The important point:

> **ISIN identifies a security; it is not simply the same thing as a ticker symbol.**

# 10. CUSIP

**CUSIP** is another identification system widely used for securities in North American markets.

A trading system may therefore contain multiple identifiers:

- Ticker
- ISIN
- CUSIP
- Internal Instrument ID
- Exchange Symbol

This is important when troubleshooting reference-data problems.

# 11. Currency

A **currency** is a medium of exchange issued by a country or monetary authority.

Examples:

- USD
- EUR
- GBP
- JPY
- INR
- CHF

Currencies are extremely important in trading because transactions often involve multiple currencies.

# 12. Currency Pair

In FX, currencies are commonly quoted as a pair.

Example:

```text
EUR/USD
```

This means:

> How many USD are needed to buy one EUR?

If:

```text
EUR/USD = 1.1000
```

then approximately:

```text
1 EUR = 1.10 USD
```

# 13. Base Currency

In:

```text
EUR/USD
```

the first currency is the:

**Base Currency**

So:

```text
EUR = Base
USD = Quote
```

# 14. Quote Currency

The second currency is the:

**Quote Currency**

For:

```text
EUR/USD
```

we have:

```text
EUR = Base
USD = Quote
```

Memory trick:

> **First = Base, Second = Quote**

# 15. Price

The **price** is the value at which an instrument is quoted or traded.

Example:

```text
Stock Price = $100
```

But in trading systems, you should always ask:

- Price of what?
- In which currency?
- At what time?
- From which venue?

Because prices change continuously.

# 16. Market Price

The **market price** generally refers to the current or recent price at which an instrument can be traded or has traded.

For a liquid stock:

```text
Current Price = $100.25
```

But a trading system may need more detailed information:

- Bid
- Ask
- Last Trade
- Timestamp
- Venue
- Quantity

# 17. Last Traded Price

The **Last Traded Price (LTP)** is the price at which the most recent trade occurred.

Example:

```text
Last Trade:
100 shares @ $100.25
```

Then:

```text
LTP = $100.25
```

Important:

> LTP is not necessarily the same as the current bid or ask.

# 18. Bid

The **bid** is the highest price currently being offered by a buyer in a market or order book, under the relevant market structure.

Example:

```text
Bid = $100.20
```

A participant is willing to buy at that price.

# 19. Ask

The **ask**, also called the offer, is the lowest price currently being offered by a seller.

Example:

```text
Ask = $100.25
```

A participant is willing to sell at that price.

# 20. Bid-Ask Spread

The difference between the ask and bid is the:

**Bid-Ask Spread**

Example:

```text
Bid = $100.20
Ask = $100.25

Spread = $0.05
```

Formula:

```text
Spread = Ask - Bid
```

# 21. Why Spread Matters for Monitoring

Spread can be a useful market-health indicator.

Suppose normally:

```text
Spread = $0.05
```

Suddenly:

```text
Spread = $2.00
```

Possible reasons include:

- Low liquidity
- Market volatility
- Market-data problems
- Venue connectivity problems
- Market opening/closing conditions
- News events

Therefore, a sudden spread increase may be worth investigating.

# 22. Volume

**Volume** represents the amount of trading activity during a period.

For example:

```text
10:00 - 10:01

Trades = 20,000
Shares = 5,000,000
```

Depending on the context, volume may refer to:

- Number of shares
- Number of contracts
- Number of trades
- Number of messages

Always check what the metric actually measures.

# 23. Trading Volume

Trading volume usually refers to the quantity of an instrument traded over a specified period.

Example:

```text
Daily Volume:
50 Million Shares
```

# 24. Order Volume

In a technology environment, **order volume** may refer to the number or quantity of orders received or generated.

Example:

```text
Orders per Second = 10,000
```

This is not necessarily the same as traded volume.

Important distinction:

```text
Order Volume
      ≠
Trading Volume
```

# 25. Message Rate

A trading system may process many messages that are not trades.

Examples:

- New Order
- Order Cancel
- Order Replace
- Execution Report
- Market Data Update
- Heartbeat

Therefore a system may have:

```text
Messages/sec = 100,000
Trades/sec   =   2,000
```

This distinction is extremely important for capacity monitoring.

# 26. Liquidity

**Liquidity** describes how easily an asset can be bought or sold without causing a significant price impact.

### Highly Liquid

- Many buyers
- Many sellers
- Large volume
- Small spread

### Less Liquid

- Few buyers
- Few sellers
- Low volume
- Large spread

# 27. Why Liquidity Matters to SRE

Liquidity can influence system behavior.

During high liquidity:

```text
High Orders
High Market Data
High Messages
```

During low liquidity:

```text
Low Trading
Large Spreads
Different Market Behavior
```

Therefore, monitoring thresholds should sometimes account for market conditions.

# 28. Volatility

**Volatility** describes how much and how quickly prices vary over time.

### High Volatility

```text
$100
 ↓
$105
 ↓
$98
 ↓
$110
```

### Low Volatility

```text
$100
 ↓
$100.10
 ↓
$99.95
 ↓
$100.05
```

# 29. Why Volatility Matters

High volatility can cause:

- More trading
- More orders
- More market data
- More risk checks
- More CPU usage
- More network traffic
- More queue activity

Therefore:

> High CPU during a major market event may not automatically mean a software failure.

It may be correlated with legitimate market activity.

This is extremely important for predictive monitoring.

# 30. Notional Value

**Notional value** is the stated or reference value of the underlying transaction or position, depending on the instrument.

For a simple stock example:

```text
Quantity = 1,000 Shares
Price    = $100

Notional Value = $100,000
```

Formula:

```text
Notional = Quantity × Price
```

For derivatives, notional can be much larger than the amount of cash initially exchanged.

# 31. Market Value

**Market value** is the current value of an asset or position based on the relevant market price.

Example:

```text
Position:
1,000 Shares

Current Price:
$110

Market Value:
$110,000
```

# 32. Position

A **position** represents an exposure to an instrument.

You can have:

- Long Position
- Short Position
- Flat / No Position

# 33. Long Position

A **long position** generally means you own or are economically exposed to an asset and benefit if its price rises.

Example:

```text
Buy 100 Shares @ $100
```

You now have:

```text
Long = +100 Shares
```

If the price becomes:

```text
$110
```

the position has an unrealized gain of:

```text
$10 × 100
= $1,000
```

before considering fees, taxes, and other factors.

# 34. Short Position

A **short position** generally means you have an exposure that benefits from a decline in price.

Example:

```text
Short 100 Shares @ $100
```

If the price falls to:

```text
$90
```

the position has a gain of approximately:

```text
$10 × 100
= $1,000
```

before costs and other considerations.

# 35. Position Size

Position size tells you how much exposure you have.

Examples:

```text
Position = 10,000 Shares
```

or

```text
Position = 500 Contracts
```

# 36. Portfolio

A **portfolio** is a collection of investments or positions.

Example:

```text
Portfolio
│
├── AAPL
│   └── +1,000 Shares
│
├── MSFT
│   └── +500 Shares
│
├── US Treasury Bond
│   └── $1M Face Value
│
└── Cash
    └── $100,000
```

# 37. P&L

P&L means:

**Profit and Loss**

It measures financial gain or loss.

Two important concepts are:

- Realized P&L
- Unrealized P&L

# 38. Realized P&L

Realized P&L occurs when a position is closed or a gain/loss is realized through a transaction.

Example:

```text
Buy:
100 Shares @ $100

Sell:
100 Shares @ $110
```

Gain:

```text
($110 - $100) × 100
= $1,000
```

before transaction costs and other adjustments.

# 39. Unrealized P&L

Unrealized P&L represents the current gain/loss on an open position based on its current valuation.

Example:

```text
Buy:
100 Shares @ $100

Current Price:
$110
```

Unrealized P&L:

```text
$1,000
```

The gain is not necessarily realized until the position is closed.

# 40. P&L Monitoring

Trading systems may continuously calculate:

- P&L
- Daily P&L
- Realized P&L
- Unrealized P&L
- Portfolio P&L
- Desk P&L
- Strategy P&L

A sudden unexpected P&L movement can be a business-critical alert.

Possible causes include:

- Market movement
- Incorrect pricing
- Reference-data issue
- Position mismatch
- Trade duplication
- Missing trade
- Valuation problem

# 41. Exposure

**Exposure** describes how much financial risk or economic sensitivity a portfolio or position has to a particular factor, asset, currency, counterparty, or market.

Examples:

- USD Exposure
- Equity Exposure
- Interest Rate Exposure
- FX Exposure
- Counterparty Exposure

Example:

```text
Portfolio
↓
USD Exposure = $10M
```

# 42. Risk

Risk is the possibility of an unfavorable outcome.

In trading systems, common categories include:

- Market Risk
- Credit Risk
- Liquidity Risk
- Counterparty Risk
- Operational Risk
- Technology Risk

# 43. Market Risk

Risk arising from changes in market prices or rates.

Examples:

```text
Stock Price ↓
Interest Rate ↑
FX Rate Changes
Commodity Price Changes
```

# 44. Credit Risk

The risk that a counterparty may fail to meet its financial obligations.

Example:

```text
Counterparty
      ↓
Owes Money
      ↓
Cannot Pay
```

# 45. Counterparty

A **counterparty** is the other party involved in a financial transaction.

Example:

```text
You
 ↕
Counterparty
```

For institutions:

```text
Bank A
 ↕
Bank B
```

# 46. Margin

**Margin** is collateral or funds required to support certain positions or transactions, particularly leveraged or derivative transactions.

Think of it as:

> **Financial protection supporting the position.**

For example:

```text
Position Exposure = $1,000,000
Required Margin = $100,000
```

The exact calculation depends on the instrument, market, clearing arrangement, and applicable rules.

# 47. Collateral

**Collateral** is an asset or other eligible value pledged to secure an obligation.

Examples may include:

- Cash
- Government Securities
- Other Eligible Assets

Simplified:

```text
Borrow / Trade
      ↓
Provide Collateral
      ↓
Reduce Counterparty Risk
```

# 48. Order

An **order** is an instruction to buy or sell an instrument.

Example:

```text
BUY
AAPL
100 Shares
```

An order may contain:

- Instrument
- Side
- Quantity
- Price
- Order Type
- Time-in-Force
- Account
- Venue

# 49. Side

The **side** tells us whether the order is:

```text
BUY
```

or

```text
SELL
```

This seems simple, but side is one of the most important fields in trading messages.

# 50. Quantity

Quantity specifies how much of the instrument is being traded.

Example:

```text
Quantity = 1,000 Shares
```

For derivatives:

```text
Quantity = 100 Contracts
```

# 51. Order Type

An order type determines how an order should behave.

Common types include:

- Market Order
- Limit Order
- Stop Order
- Stop-Limit Order

Different venues and systems support different order types.

# 52. Market Order

A **market order** generally requests immediate execution at the best available prices in the market, subject to liquidity and market conditions.

Example:

```text
BUY 100 AAPL
Market
```

The system tries to execute it without specifying a maximum purchase price.

# 53. Limit Order

A **limit order** specifies a price constraint.

Example:

```text
BUY 100 AAPL
LIMIT $100
```

The buyer is willing to pay no more than the specified limit price, subject to the order's rules.

# 54. Stop Order

A stop order becomes active or triggers when a specified condition or price level is reached.

Example concept:

```text
Current Price = $100
Stop = $95
```

If the trigger condition is reached, the order may become active according to its configuration.

# 55. Time in Force

**Time in Force (TIF)** tells the system how long an order should remain active.

Examples include:

- DAY
- GTC
- IOC
- FOK

# 56. DAY

A **DAY** order generally remains active for the trading day or session according to the venue's rules.

If it does not execute, it may expire.

# 57. GTC

GTC means:

**Good Till Cancelled**

The order remains active until it is:

- Executed
- Cancelled
- Expired

subject to broker and venue rules.

# 58. IOC

IOC means:

**Immediate or Cancel**

The order attempts to execute immediately.

Any portion that cannot execute immediately is cancelled according to the applicable rules.

# 59. FOK

FOK means:

**Fill or Kill**

The order must be executed completely according to its conditions immediately, or it is cancelled.

Example:

```text
Buy 1,000 Shares

Available:
700 Shares
```

A FOK order would not partially fill under the standard FOK concept.

# 60. Fill

A **fill** occurs when an order or part of an order is executed.

Example:

```text
Order:
Buy 1,000 Shares

Execution:
400 Shares
```

Then:

```text
Filled = 400
Remaining = 600
```
# 61. Partial Fill

A **partial fill** occurs when only part of the requested quantity is executed.

Example:

```text
Requested = 1,000
Executed = 600
Remaining = 400
```

This is extremely common in real markets.

# 62. Execution

An **execution** is the event in which an order is matched and traded.

Example:

```text
Order:
BUY 100 @ $100

Execution:
100 @ $100
```

# 63. Trade

A **trade** is the completed transaction resulting from matched buy and sell interests.

Example:

```text
Buyer
  ↕
100 Shares @ $100
  ↕
Seller
```

# 64. Order vs Trade

This distinction is critical.

```text
ORDER
↓
Instruction
```

while:

```text
TRADE
↓
Completed Transaction
```

An order may:

- Never execute
- Execute partially
- Execute completely

Example:

```text
Order:
1,000 Shares

Trades:
400 Shares
300 Shares
300 Shares
```

One order may therefore result in multiple executions and trades.

# 65. Order ID

Every order usually has a unique identifier.

Example:

```text
Order ID:
ORD-123456
```

This is extremely important during incident investigation.

If a user says:

> My order failed.

you want:

```text
Order ID
↓
Search Logs
↓
Trace Order
↓
Find Failure
```

# 66. Trade ID

A trade or execution may have its own identifier.

Example:

```text
Trade ID:
TRD-789123
```

Now you can distinguish:

```text
Order ID
ORD-123456

Trade ID
TRD-789123
```

# 67. Execution ID

Some systems also assign a separate execution identifier.

This allows one order to have multiple executions.

Example:

```text
Order
ORD-100
│
├── Execution EX-1
│   └── 300 Shares
│
├── Execution EX-2
│   └── 400 Shares
│
└── Execution EX-3
    └── 300 Shares

Total:
1,000 Shares
```

# 68. Order Book

An **order book** contains active buy and sell orders for an instrument.

Simplified:

```text
BUY SIDE             SELL SIDE

$99.90   500         $100.10   300
$99.95   700         $100.05   400
$100.00 1000         $100.02   600
```

The exact presentation and ordering depend on the venue.

# 69. Market Depth

Market depth refers to the amount of available buying and selling interest at different price levels.

Example:

```text
Price       Quantity
100.00       1,000
100.01         800
100.02         600
100.03         500
```

More depth can indicate more available liquidity at those levels, though depth alone does not guarantee execution.

# 70. Level 1 Market Data

Level 1 market data generally contains top-of-book information such as:

- Best Bid
- Best Ask
- Last Trade
- Volume

Example:

```text
Bid  = $100.00
Ask  = $100.05
Last = $100.03
```

# 71. Level 2 Market Data

Level 2 data generally provides more detailed market depth.

Example:

### Bid Side

```text
$100.00 → 1,000
$99.95  → 2,000
$99.90  → 3,000
```

### Ask Side

```text
$100.05 → 1,500
$100.10 → 2,500
$100.15 → 4,000
```

Exact definitions vary by market-data provider.

# 72. Quote

A **quote** provides current buying and/or selling prices.

Example:

```text
Bid = $100.00
Ask = $100.05
```

A quote is not the same thing as a trade.

# 73. Quote vs Trade

Remember:

```text
QUOTE
↓
Someone is willing to buy/sell
```

```text
TRADE
↓
A transaction actually occurred
```

Example:

```text
Quote:
Bid $100
Ask $100.05

Trade:
100 Shares Executed @ $100.05
```

# 74. Market Data Feed

A market data feed is a stream of information about market activity.

It can contain:

- Quotes
- Trades
- Order Book Updates
- Reference Information
- Status Messages

Trading systems may consume thousands or millions of messages.

# 75. Market Data Latency

Market data latency is the delay between the occurrence or publication of market information and its arrival or processing by a consumer.

Simplified:

```text
Exchange
  ↓
Market Data Feed
  ↓
Network
  ↓
Consumer
```

If:

```text
Expected = 1 ms
Actual   = 100 ms
```

there may be a serious issue for latency-sensitive systems.

# 76. Stale Data

**Stale data** means data that is older than expected or no longer reflects the current state.

Example:

```text
Expected Update Every 100 ms

Last Update:
10 Seconds Ago
```

This is suspicious.

Monitoring may therefore check:

- Data Freshness
- Last Message Timestamp
- Sequence Number
- Message Rate

# 77. Sequence Number

Market-data and trading protocols often use sequence numbers to help detect missing or out-of-order messages.

Example:

```text
1001
1002
1003
1005
```

What happened?

```text
1004 Missing
```

This may indicate:

- Packet loss
- Feed issue
- Consumer problem
- Message-processing issue

# 78. Heartbeat

A heartbeat is a periodic message indicating that a connection or session is still alive.

Example:

```text
Every 5 Seconds

Heartbeat
Heartbeat
Heartbeat
Heartbeat
```

If heartbeats stop:

```text
Connection May Be Broken
```

# 79. Connectivity

Connectivity refers to the ability of systems to communicate with each other.

Example:

```text
Trading System
      ↓
Network
      ↓
Exchange
```

If connectivity is lost:

```text
Trading System
      X
Exchange
```

Orders may fail.

# 80. Trading Session

A trading session is a defined period during which a market or venue operates.

A market may have:

- Pre-market
- Regular Session
- Post-market

The exact sessions depend on the venue and asset class.

# 81. Market Open

Market open is the beginning of a particular trading session.

The opening period can be extremely important for monitoring because activity may increase sharply.

Example:

```text
Before Open
Orders = 10,000/sec

After Open
Orders = 100,000/sec
```

# 82. Market Close

Market close marks the end of a trading session.

Close-related periods may also generate unusual activity because of:

- Portfolio rebalancing
- Closing orders
- Auctions
- Position management
- End-of-day processing

# 83. Pre-Market

Pre-market is a trading period before the regular market session in markets that support it.

It can have different:

- Liquidity
- Participants
- Trading rules
- Volume
- Spreads

Therefore thresholds may need to be different.

# 84. After-Hours

After-hours trading occurs outside the regular session where supported.

Remember:

```text
Normal Session
≠
After-Hours Session
```

Market conditions can be significantly different.

# 85. Auction

Some markets use auctions to determine prices at specific points, such as opening or closing.

A simplified auction process:

```text
Buy Orders
      +
Sell Orders
      ↓
Auction Mechanism
      ↓
Price Discovery
      ↓
Executions
```

Auction periods can create unusual order and message patterns.

# 86. Settlement Date

The **settlement date** is the date on which the obligations of a trade are completed, according to the applicable market rules.

Do not assume:

```text
Trade Date = Settlement Date
```

They may differ.

# 87. Trade Date

The **trade date** is the date on which the transaction occurs.

Often represented as:

```text
T
```

Then settlement may happen later:

```text
T
↓
Settlement
```

# 88. T+1

T+1 means settlement occurs one business day after the trade date, under a market's applicable settlement convention.

Example:

```text
Trade:
Monday

Settlement:
Tuesday
```

assuming Tuesday is a valid settlement business day.

# 89. T+2

T+2 means:

```text
Trade Date
+
2 Business Days
```

Settlement conventions vary by market and asset class, so always verify the specific market.

# 90. Business Day

A business day is a day considered operational for the relevant financial process.

It may exclude:

- Weekends
- Market Holidays
- Bank Holidays

The exact calendar depends on:

- Country
- Exchange
- Asset Class
- Settlement System

This is extremely important for monitoring.

# 91. Trading Calendar

A trading calendar tells systems when a market is:

- Open
- Closed
- Partially Open
- Operating on Special Hours

Example:

```text
Monday    → Open
Tuesday   → Open
Wednesday → Holiday
Thursday  → Open
Friday    → Open
```

# 92. Why Trading Calendars Matter for SRE

Suppose your monitoring expects:

```text
Orders > 10,000/sec
```

But today is a market holiday.

Expected:

```text
Orders ≈ 0
```

If your monitoring does not understand the calendar:

```text
LOW ORDER VOLUME
↓
FALSE ALERT
```

Therefore:

> **Trading calendars are part of monitoring logic.**

# 93. Holiday

A market holiday means the relevant market is closed or operates differently.

Examples can include:

- National Holidays
- Exchange Holidays
- Bank Holidays
- Special Closures

Different markets may have different holidays.

# 94. Notional vs Quantity

Consider:

```text
100 Shares
Price = $500
```

Quantity:

```text
100 Shares
```

Notional:

```text
100 × $500
= $50,000
```

So:

```text
Quantity
≠
Notional
```

Both may be important monitoring dimensions.

# 95. Price vs Value

Consider:

```text
1 Share
Price = $100
```

Price:

```text
$100 per Share
```

Value:

```text
$100
```

Now:

```text
1,000 Shares
Price = $100
```

Price remains:

```text
$100
```

But position value becomes:

```text
$100,000
```

# 96. Gross vs Net Exposure

Suppose:

```text
Long Position  = $10M
Short Position = $8M
```

Gross Exposure:

```text
$10M + $8M
= $18M
```

Net Exposure:

```text
$10M - $8M
= $2M
```

So:

```text
Gross Exposure = Total Absolute Exposure
Net Exposure   = Offset Long and Short Exposure
```

Exact institutional calculations can be more complex.

# 97. Leverage

Leverage means gaining exposure larger than the amount of capital directly committed, often through borrowing or derivatives.

Example:

```text
Capital  = $100,000
Exposure = $500,000
```

Leverage:

```text
5x
```

Leverage increases both potential gains and potential losses.

# 98. Reconciliation

**Reconciliation** means comparing records from different systems to ensure they agree.

Example:

```text
Trading System
      ↓
100 Trades

Clearing System
      ↓
98 Trades
```

Difference:

```text
2 Trades
```

This is a:

**Reconciliation Break**

# 99. Break

A **break** is a mismatch or exception between expected and actual records.

Example:

```text
System A:
Position = 10,000

System B:
Position = 9,500
```

Break:

```text
500 Units
```

Breaks are extremely important in post-trade operations.

# 100. Exception

An exception occurs when a process does not behave as expected.

Examples:

- Trade Rejected
- Settlement Failed
- Reference Data Missing
- Position Mismatch
- Payment Failed

Exception monitoring is a major part of financial operations.

# 101. Straight-Through Processing

**Straight-Through Processing (STP)** means processing a transaction electronically through the workflow with minimal manual intervention.

Example:

```text
Order
 ↓
Execution
 ↓
Trade Capture
 ↓
Clearing
 ↓
Settlement
```

No manual intervention is required for the normal path.

# 102. STP Rate

STP rate measures how many transactions complete the intended automated workflow without manual intervention.

Example:

```text
10,000 Trades
9,900 Automated
100 Manual Exceptions
```

STP Rate:

```text
99%
```

A falling STP rate can be an important operational indicator.

# 103. Straight-Through Processing Failure

Suppose:

```text
STP Rate

Normal  = 99.8%
Current = 92%
```

This could indicate:

- Integration failures
- Validation failures
- Reference-data problems
- Settlement issues
- Application errors

This is a good business-level monitoring metric.

# 104. FIX

FIX stands for:

**Financial Information eXchange**

FIX is a widely used messaging protocol standard for electronic trading communications.

It can be used for messages such as:

- Order
- Execution Report
- Cancel
- Replace
- Trade-related messages

A simplified flow:

```text
Trading System
      ↓
FIX
      ↓
Broker / Exchange / Venue
```

FIX will be covered in much greater detail in a dedicated chapter.

# 105. API

An API allows systems to communicate programmatically.

Example:

```text
Trading System
      ↓
API
      ↓
Broker
```

APIs may be used for:

- Order submission
- Market data
- Account information
- Trade status
- Reporting

# 106. Latency

Latency is the time taken for an operation or message to travel through a system.

Example:

```text
Order Submitted
      ↓
10 ms
      ↓
Exchange Received
```

Latency:

```text
10 ms
```

# 107. End-to-End Latency

End-to-end latency measures the complete journey.

Example:

```text
Client
 ↓
API
 ↓
OMS
 ↓
Risk
 ↓
Router
 ↓
Network
 ↓
Exchange
```

If total time is:

```text
25 ms
```

then:

```text
E2E Latency = 25 ms
```

# 108. Throughput

Throughput is the amount of work processed per unit of time.

Examples:

- Orders/sec
- Trades/sec
- Messages/sec
- Transactions/sec

Example:

```text
10,000 Orders/sec
```

# 109. Capacity

Capacity is the amount of workload a system can handle while meeting its required performance and reliability targets.

Example:

```text
Current  = 50,000 Orders/sec
Capacity = 100,000 Orders/sec
```

Headroom:

```text
50,000 Orders/sec
```

# 110. Queue

A queue temporarily stores work waiting to be processed.

Example:

```text
Incoming Orders
      ↓
    Queue
      ↓
 Processing
```

If incoming traffic exceeds processing capacity:

```text
Queue Size ↑
```

A growing queue can be an early warning signal.

# 111. Queue Depth

Queue depth is the number of items currently waiting in a queue.

Example:

```text
Normal  = 500
Current = 20,000
```

This can indicate:

- Processing slowdown
- Traffic spike
- Downstream failure
- Consumer failure
- Capacity exhaustion

# 112. Backlog

A backlog is accumulated work that has not yet been processed.

Example:

```text
Incoming   = 10,000/sec
Processing =  8,000/sec
```

Difference:

```text
2,000/sec
```

causes backlog growth.

# 113. Throughput vs Latency

These are different concepts.

### Latency

How long does one operation take?

```text
100 ms
```

### Throughput

How many operations can we process?

```text
10,000/sec
```

A system can have:

```text
High Throughput
+
High Latency
```

or

```text
Low Latency
+
Low Throughput
```

You need both metrics.

# 114. Availability

Availability measures whether a system is operational and accessible when required.

Example:

```text
99.99% Availability
```

For trading systems, availability requirements can vary significantly by component and business process.

# 115. Reliability

Reliability describes the ability of a system to perform correctly and consistently over time.

A system can be:

```text
Available
```

but still:

```text
Unreliable
```

Example:

```text
Application Responds
BUT
20% of Orders Fail
```

Technically:

```text
Up
```

Business-wise:

```text
Unhealthy
```

# 116. Business Availability

This is particularly important in financial systems.

Consider:

```text
System = UP
```

but:

```text
Orders = Rejected
```

The system is technically available but the trading capability is effectively unavailable.

Therefore monitoring should sometimes measure:

```text
Business Transaction Success
```

rather than only:

```text
Server Up/Down
```

# 117. Error Rate

Error rate measures the percentage or count of failed operations.

Example:

```text
Total Orders  = 100,000
Failed Orders = 500
```

Error Rate:

```text
0.5%
```

# 118. Rejection Rate

Rejection rate measures how many orders are rejected.

Example:

```text
Orders    = 10,000
Rejected  = 200
```

Rejection Rate:

```text
2%
```

A rejection can happen because of:

- Risk checks
- Invalid order
- Market rules
- Connectivity issues
- System errors
- Insufficient funds
- Instrument restrictions

You must distinguish expected business rejections from technical failures.

# 119. Success Rate

Success rate measures the percentage of operations completed successfully.

If:

```text
Total   = 10,000
Success = 9,900
```

then:

```text
Success Rate = 99%
```

# 120. Business Transaction

A business transaction is an operation meaningful to the business.

Examples:

- Submit Order
- Execute Trade
- Cancel Order
- Settle Trade
- Update Position
- Process Payment

This is more meaningful than simply:

```text
HTTP 200
```

# 121. Business Transaction Monitoring

Instead of monitoring only:

- CPU
- Memory
- Disk

we can monitor:

- Orders Submitted
- Orders Accepted
- Orders Rejected
- Orders Executed
- Trades Captured
- Settlement Success

This provides business-aware observability.

# 122. The Monitoring Perspective

Now connect terminology to SRE.

| Financial Term | Monitoring Question |
|---------------|--------------------|
| Order | Are orders being accepted? |
| Trade | Are trades executing? |
| Fill | Are orders receiving fills? |
| Position | Are positions updating correctly? |
| P&L | Is valuation behaving normally? |
| Market Data | Is data fresh? |
| Liquidity | Is market depth normal? |
| Volume | Is activity within expected range? |
| Volatility | Is the market unusually active? |
| Spread | Is liquidity behaving normally? |
| Latency | Are orders/data delayed? |
| Queue | Is work accumulating? |
| Rejection | Why are orders failing? |
| Settlement | Are trades settling? |
| Reconciliation | Are systems agreeing? |
| STP | Are workflows completing automatically? |
| Trading Calendar | Should the market even be active? |

# 123. A Real Monitoring Example

Imagine your dashboard shows:

```text
Order Rate
Normal  = 20,000/sec
Current = 80,000/sec

CPU
Normal  = 60%
Current = 90%

Queue
Normal  = 1,000
Current = 30,000

Latency
Normal  = 5 ms
Current = 80 ms
```

A beginner may say:

> CPU is high.

A Trading SRE should say:

- Order volume increased 4×
- Queue depth is growing
- Latency increased 16×
- CPU is near saturation

Then investigate:

> Why did order volume increase?

Possible reasons:

- Market open
- Major market event
- Trading algorithm malfunction
- Duplicate orders

The financial context changes the investigation.

# 124. Another Monitoring Example

Suppose:

```text
Order Rate = 0
```

Is that an incident?

Not necessarily.

Check:

```text
Is the Market Open?
```

If:

```text
Market = Closed
```

Then:

```text
Order Rate = 0
```

may be completely normal.

But:

```text
Market = Open
Order Rate = 0
```

could be a serious incident.

This is the essence of:

> **Business-aware monitoring.**

# 125. Another Example: High CPU

Suppose:

```text
CPU = 95%
```

At:

```text
2:00 AM
Market Closed
```

That may be suspicious.

But:

```text
CPU = 95%

Market Open
Major Economic Announcement
Order Volume = 5× Normal
```

may be expected.

The alert should therefore include context.

# 126. Terminology Cheat Sheet

### Asset
Financial item with economic value.

### Liquidity
Ease of trading without significant price impact.

### Volatility
Degree of price variation.

### Order
Instruction to buy or sell.

### Execution
An order is matched and executed.

### Fill
Executed quantity.

### Trade
Completed transaction.

### Position
Current exposure to an instrument.

### Portfolio
Collection of positions and investments.

### P&L
Profit and Loss.

### Exposure
Financial or economic sensitivity.

### Margin
Required financial support for positions or trades.

### Collateral
Assets pledged to secure obligations.

### Order Book
Active buy and sell interest.

### Market Data
Information about prices, quotes, and trades.

### Settlement
Completion of trade obligations.

### Clearing
Post-trade management of obligations, margin, and risk.

### Reconciliation
Comparing records between systems.

### STP
Straight-Through Processing.

### Latency
Time taken to process or communicate.

### Throughput
Work processed per unit time.

### Queue
Work waiting to be processed.

### Trading Calendar
Defines market operating days and sessions.

### Business Day
Day on which relevant financial operations occur.

# 127. Most Important Distinctions to Memorize

### Order vs Trade

```text
Order = Request
Trade = Completed Transaction
```

### Quote vs Trade

```text
Quote = Willingness to Buy/Sell
Trade = Actual Transaction
```

### Bid vs Ask

```text
Bid = Buyer
Ask = Seller
```

### Price vs Notional

```text
Price    = Value per Unit
Notional = Overall Transaction Value
```

### Quantity vs Volume

```text
Quantity = Amount in One Order/Position
Volume   = Activity During a Period
```

### Position vs Portfolio

```text
Position  = Exposure to One Instrument
Portfolio = Collection of Positions
```

### Realized vs Unrealized P&L

```text
Realized   = Closed Position Gain/Loss
Unrealized = Open Position Gain/Loss
```

### Clearing vs Settlement

```text
Clearing
→ Determine / Manage Obligations & Risk

Settlement
→ Exchange Assets and Cash
```

### Availability vs Reliability

```text
Availability
→ Is it Accessible?

Reliability
→ Does it Work Correctly?
```

### Latency vs Throughput

```text
Latency
→ How Long?

Throughput
→ How Much Work?
```

# 128. Final Memory Map

Remember financial markets like this:

```text
                    FINANCIAL MARKET
                         ASSET
                           │
                           ▼
                      INSTRUMENT
                           │
                           ▼
                         PRICE
                           │
             ┌─────────────┴─────────────┐
             ▼                           ▼
           BID                          ASK
             │                           │
             └─────────────┬─────────────┘
                           ▼
                       ORDER BOOK
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
                       POSITION
                           │
                           ▼
                       PORTFOLIO
                           │
             ┌─────────────┼─────────────┐
             ▼             ▼             ▼
            P&L         EXPOSURE        RISK
```

And from the SRE perspective:

```text
MARKET
  ↓
MARKET DATA
  ↓
ORDERS
  ↓
EXECUTION
  ↓
TRADES
  ↓
POSITIONS
  ↓
P&L / RISK
  ↓
CLEARING
  ↓
SETTLEMENT
  ↓
RECONCILIATION
```

At every stage ask:

- Is it available?
- Is it correct?
- Is it fast enough?
- Is the volume normal?
- Is the data fresh?
- Are errors increasing?
- Are queues growing?
- Is the market actually open?
- Is the behavior expected for this trading session?

That mindset is what turns financial terminology into **monitoring knowledge**.
