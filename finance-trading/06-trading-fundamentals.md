# Trading Fundamentals

## Introduction

Before learning trading systems, order management systems, exchanges, FIX, market data, or trading-system monitoring, you need to understand one simple question:

> **What actually happens when someone wants to buy or sell something in a financial market?**

A trading system exists to turn an intention to trade into an actual transaction.

At the simplest level:

```text
Someone wants to BUY
        ↓
An order is created
        ↓
Order is validated
        ↓
Order is routed
        ↓
A buyer meets a seller
        ↓
Order is executed
        ↓
Trade is created
        ↓
Trade is processed
        ↓
Position is updated
        ↓
P&L / Risk is updated
        ↓
Trade is cleared and settled
```

As an SRE or Monitoring Engineer, your job is not to become a professional trader.

Your job is to understand this business flow well enough to answer:

- What is supposed to happen?
- What application performs it?
- What data moves between systems?
- What can go wrong?
- How would the failure appear?
- What should we monitor?
- What should we alert on?
- Is the behavior actually abnormal or is it normal market behavior?

That is the purpose of this chapter.

# 1. What Is Trading?

Trading is the process of buying and selling financial instruments.

Examples:

- Stocks
- Bonds
- Currencies
- Commodities
- Futures
- Options
- ETFs
- Other financial instruments

At the simplest level:

```text
Buyer
  ↓
Wants to buy
  ↓
Seller
  ↓
Wants to sell
```

When their conditions match, a transaction can occur.

# 2. The Simplest Possible Example

Imagine:

```text
Alice wants to BUY 100 shares
at $100 per share.

Bob wants to SELL 100 shares
at $100 per share.
```

The market can match them.

Result:

```text
Alice
BUY 100 @ $100
        ↓ MATCH
Bob
SELL 100 @ $100
```

Trade:

```text
100 shares @ $100
```

Notional value:

```text
100 × $100
= $10,000
```

This is the fundamental idea behind a trade.

# 3. Why Do Markets Exist?

Financial markets allow participants to:

- Buy assets
- Sell assets
- Raise capital
- Manage risk
- Transfer risk
- Invest
- Hedge
- Discover prices
- Provide liquidity

For example, a company may need money to expand.

It can issue shares or bonds.

Investors can purchase them.

Therefore:

```text
Company
   ↓
Raises Capital
   ↓
Investors
```

Markets connect these participants.

# 4. Price Discovery

One of the most important functions of a financial market is:

**Price Discovery**

Price discovery means determining the price at which buyers and sellers are willing to transact.

Suppose:

```text
Buyers:
$99
$99.50
$100

Sellers:
$100.50
$101
$102
```

There is currently no exact match.

Then perhaps a buyer agrees to:

```text
$100.50
```

and a seller accepts it.

A trade can occur.

The market has discovered a transaction price.

# 5. Buyers and Sellers

Every trade fundamentally involves two sides:

```text
BUY
SELL
```

One participant wants to buy.

Another participant wants to sell.

For a basic stock trade:

```text
Buyer
  +
Seller
  ↓
Trade
```

However, the buyer and seller may not directly know or communicate with each other.

There may be many systems and intermediaries between them.

# 6. Trading Is More Than Buying and Selling

In an enterprise trading environment, the complete flow may look like:

```text
Trader
  ↓
Trading Application
  ↓
Order Management System
  ↓
Risk Checks
  ↓
Order Router
  ↓
Broker / Trading Venue
  ↓
Exchange
  ↓
Matching Engine
  ↓
Execution
  ↓
Execution Report
  ↓
Trade Capture
  ↓
Clearing
  ↓
Settlement
  ↓
Position / P&L
```

Every arrow can represent:

- Network communication
- API call
- FIX message
- Database operation
- Queue
- Event
- Service-to-service communication

This is where finance becomes extremely important to an SRE.

# 7. Trading Intent

Everything starts with an intention.

For example:

> "I want to buy 1,000 shares of XYZ."

That intention must become a structured instruction.

Example:

```text
Instrument     = XYZ
Side           = BUY
Quantity       = 1,000
Order Type     = LIMIT
Limit Price    = $100
Time in Force  = DAY
```

This becomes an:

**Order**

# 8. Order

An order is an instruction to buy or sell an instrument.

Example:

```text
BUY
XYZ
1,000 Shares
LIMIT $100
```

The order contains information needed by downstream systems.

Common fields include:

- Order ID
- Instrument
- Side
- Quantity
- Price
- Order Type
- Time in Force
- Account
- Trader
- Strategy
- Venue
- Timestamp

# 9. Order Lifecycle

An order does not instantly become a trade.

It goes through a lifecycle.

A simplified lifecycle:

```text
Created
   ↓
Validated
   ↓
Risk Checked
   ↓
Accepted
   ↓
Routed
   ↓
Working
   ↓
Partially Filled
   ↓
Fully Filled
   ↓
Completed
```

But an order can also fail:

```text
Created
   ↓
Rejected
```

or

```text
Working
   ↓
Cancelled
```

or

```text
Working
   ↓
Expired
```

# 10. Order States

Different trading platforms may use different state names, but common concepts include:

- NEW
- PENDING
- ACCEPTED
- REJECTED
- WORKING
- PARTIALLY FILLED
- FILLED
- CANCELLED
- EXPIRED

Do not assume every platform uses exactly these names.

The important thing is understanding the state transitions.

# 11. Order State Machine

Think of an order as a state machine.

```text
                 ┌───────────┐
                 │   NEW     │
                 └─────┬─────┘
                       ↓
                 ┌───────────┐
                 │ VALIDATED │
                 └─────┬─────┘
                       ↓
                 ┌───────────┐
                 │ ACCEPTED  │
                 └─────┬─────┘
                       ↓
                 ┌───────────┐
                 │ WORKING   │
                 └─────┬─────┘
                       │
              ┌────────┼────────┐
              ↓        ↓        ↓
          PARTIAL    FILLED   CANCELLED
              │
              ↓
           FILLED
```

A rejection path may happen earlier:

```text
NEW
 ↓
VALIDATION
 ↓
REJECTED
```

# 12. Why Order States Matter for Monitoring

Suppose your dashboard shows:

```text
NEW Orders      = 100,000
ACCEPTED Orders = 99,900
FILLED Orders   = 20,000
```

That may be normal.

But suppose:

```text
NEW      = 100,000
ACCEPTED = 100
```

That could indicate:

- Risk system failure
- Validation failure
- Downstream outage
- Queue backlog
- Database issue
- Network problem

The state distribution itself can become a monitoring signal.

# 13. Order Acceptance

An order being accepted does not necessarily mean:

> "The trade happened."

It usually means the system has accepted the order for further processing.

```text
Order
 ↓
Validation
 ↓
Accepted
 ↓
Routing
 ↓
Execution
```

The order may still remain unfilled.

# 14. Order Rejection

An order can be rejected for many reasons.

Examples:

- Invalid Instrument
- Invalid Quantity
- Invalid Price
- Risk Limit Exceeded
- Insufficient Funds
- Market Closed
- Instrument Restricted
- Invalid Account
- System Error
- Venue Rejection

Important:

> **Not every rejection is a technical incident.**

Some rejections are expected business behavior.

# 15. Business Rejection vs Technical Failure

Consider:

```text
Order Rejected
↓
Risk Limit Exceeded
```

This may be completely normal.

Now consider:

```text
Order Rejected
↓
Database Unavailable
```

That is a technical issue.

Monitoring must distinguish between them.

Otherwise:

```text
Expected Business Rejection
        ↓
Alert
        ↓
False Positive
```

# 16. Pre-Trade Validation

Before an order reaches the market, the system may validate it.

Examples:

- Is the instrument valid?
- Is quantity valid?
- Is price valid?
- Is account valid?
- Is the market open?
- Is this order type supported?

This prevents invalid orders from entering the trading workflow.

# 17. Pre-Trade Risk Checks

Trading systems often perform risk checks before allowing orders to proceed.

Examples:

- Position Limits
- Notional Limits
- Credit Limits
- Exposure Limits
- Price Limits
- Order Size Limits
- Buying Power
- Margin Requirements

Simplified:

```text
Order
 ↓
Risk Check
 ↓
PASS → Continue

FAIL → Reject
```

# 18. Example of a Risk Check

Suppose:

```text
Maximum Allowed Position = 10,000 Shares
Current Position         = 9,500 Shares
```

A trader submits:

```text
BUY 1,000 Shares
```

Potential position:

```text
9,500 + 1,000
= 10,500
```

If the limit is strict:

```text
10,500 > 10,000
```

The order may be rejected.

This is a business rule, not necessarily an application failure.

# 19. Order Routing

After validation and risk checks, an order may need to be sent to a particular trading venue.

This is:

**Order Routing**

```text
Order
 ↓
Order Router
 ├── Exchange A
 ├── Exchange B
 ├── Broker
 └── Alternative Venue
```

The routing decision can depend on:

- Price
- Liquidity
- Venue availability
- Order type
- Instrument
- Trading strategy
- Cost
- Rules

# 20. Trading Venue

A trading venue is a marketplace or facility where trading can occur.

Examples can include:

- Stock Exchange
- Electronic Trading Venue
- Alternative Trading System
- Broker Market
- Other Execution Venue

The exact terminology depends on the market.

# 21. Exchange

An exchange is a regulated marketplace where financial instruments can be traded under defined rules.

Examples around the world include:

- NYSE
- NASDAQ
- LSE
- CME
- Eurex
- NSE
- BSE

The important concept is:

```text
Trading Participants
        ↓
Exchange
        ↓
Matching / Trading Mechanism
```

# 22. Matching Engine

The matching engine is one of the most important components of an electronic trading venue.

Its job is to match compatible buy and sell orders according to the venue's rules.

Simplified:

```text
BUY ORDERS
    ↓
┌───────────────┐
│   MATCHING    │
│    ENGINE     │
└───────────────┘
    ↑
SELL ORDERS
```

When orders match:

```text
BUY 100 @ $100
      ↕
SELL 100 @ $100
```

an execution can occur.

# 23. Order Book and Matching

Imagine:

### Buy Side

```text
Price     Quantity
100.00     1,000
99.90        500
99.80        800
```

### Sell Side

```text
Price     Quantity
100.10       500
100.20       800
100.30       400
```

There is no immediate match between:

```text
Best Bid = $100.00
Best Ask = $100.10
```

Spread:

```text
$100.10 - $100.00
= $0.10
```

# 24. When a Match Happens

Suppose a seller submits:

```text
SELL 500 @ $100.00
```

Now:

```text
BUY 500 @ $100.00
      ↕
SELL 500 @ $100.00
```

The orders match.

Trade:

```text
500 Shares @ $100.00
```

# 25. Price-Time Priority

Many electronic markets use some form of price-time priority, although exact matching rules vary by venue.

### Price Priority

Better prices generally have priority.

For buyers:

```text
$101
```

is better than:

```text
$100
```

For sellers:

```text
$99
```

is better than:

```text
$100
```

### Time Priority

If two orders have the same price, the earlier order may receive priority.

Example:

```text
Order A:
BUY 500 @ $100
10:00:01

Order B:
BUY 500 @ $100
10:00:02
```

Order A may have priority.

Exact rules depend on the venue.

# 26. Execution

When the matching engine matches compatible orders, an execution occurs.

Example:

```text
BUY
1,000 @ $100

SELL
600 @ $100
```

Possible result:

```text
Execution:
600 @ $100
```

The buy order remains:

```text
Remaining = 400
```

# 27. Partial Execution

An order does not always execute completely.

Example:

```text
Order:
BUY 1,000 Shares

Execution 1:
300 Shares

Execution 2:
400 Shares

Execution 3:
300 Shares
```

Total:

```text
1,000 Shares
```

One order produced multiple executions.

# 28. Execution Report

After execution, the trading system needs to know what happened.

An execution report may contain information such as:

- Order ID
- Execution ID
- Instrument
- Side
- Executed Quantity
- Execution Price
- Remaining Quantity
- Order Status
- Timestamp
- Venue

Simplified:

```text
Order:
BUY 1,000

Execution Report:
Filled 300 @ $100
Remaining 700
```

# 29. Trade Capture

After execution, the trade usually needs to be recorded in downstream systems.

This process is commonly called:

**Trade Capture**

Simplified:

```text
Execution
   ↓
Trade Capture
   ↓
Trade Record
```

A trade record may contain:

- Trade ID
- Order ID
- Instrument
- Quantity
- Price
- Timestamp
- Counterparty
- Account
- Venue
- Currency

# 30. Position Update

Once a trade is captured, the relevant position may change.

Suppose:

```text
Current Position = +1,000 Shares
```

Then:

```text
BUY 500 Shares
```

New position:

```text
+1,500 Shares
```

This can affect:

- Exposure
- Risk
- P&L
- Margin
- Portfolio Value

# 31. Position Lifecycle

A position can change continuously.

Example:

```text
Start:
0

BUY 1,000
↓
+1,000

SELL 300
↓
+700

SELL 700
↓
0
```

If you sell more than your long position, depending on the market, instrument, and account rules, you may move into a short position.

# 32. Trading and P&L

Suppose:

```text
BUY 1,000 Shares @ $100
```

Position:

```text
+1,000
```

Current price:

```text
$105
```

Approximate unrealized P&L:

```text
($105 - $100) × 1,000
= $5,000
```

If the price becomes:

```text
$95
```

then:

```text
($95 - $100) × 1,000
= -$5,000
```

# 33. Why Pricing Data Is Critical

Notice what happened.

Position stayed:

```text
1,000 Shares
```

But P&L changed because:

```text
Price Changed
```

Therefore, if market data is wrong:

```text
Incorrect Price
      ↓
Incorrect Valuation
      ↓
Incorrect P&L
      ↓
Incorrect Risk
```

This is why market-data monitoring is extremely important.

# 34. Clearing

After a trade occurs, there are processes that determine and manage the obligations of the parties.

This is broadly part of:

**Clearing**

Clearing can involve:

- Confirming obligations
- Netting
- Margin
- Risk management
- Preparing transactions for settlement

The exact process depends on the market structure.

# 35. Settlement

Settlement is the completion of the transaction's financial obligations.

Simplified:

```text
Buyer
  ↓
Cash
  ↓
Seller

Seller
  ↓
Asset
  ↓
Buyer
```

The actual operational structure can involve custodians, clearing organizations, settlement systems, and other intermediaries.

# 36. Reconciliation

After transactions are processed, systems need to agree.

For example:

```text
Trading System:
1,000 Trades

Clearing System:
1,000 Trades

Settlement System:
1,000 Trades
```

Everything matches.

But if:

```text
Trading:
1,000

Clearing:
999
```

there is a discrepancy.

This is a:

**Reconciliation Break**

# 37. End-to-End Trading Flow

Now combine everything.

```text
                     TRADING FLOW

Trader
  │
  ▼
Trading Application
  │
  ▼
Order Creation
  │
  ▼
Validation
  │
  ▼
Pre-Trade Risk
  │
  ├──── FAIL ────► REJECT
  │
  ▼
Order Management
  │
  ▼
Order Routing
  │
  ▼
Broker / Venue
  │
  ▼
Exchange
  │
  ▼
Matching Engine
  │
  ├──── NO MATCH ───► ORDER REMAINS WORKING
  │
  ▼
Execution
  │
  ▼
Execution Report
  │
  ▼
Trade Capture
  │
  ▼
Position Update
  │
  ├──────────────► Risk
  │
  ├──────────────► P&L
  │
  └──────────────► Portfolio
  │
  ▼
Clearing
  │
  ▼
Settlement
  │
  ▼
Reconciliation
```

This diagram is one of the most important diagrams in the entire finance-learning path.

# 38. The Same Flow from an SRE Perspective

Now forget the business terminology for a moment.

Imagine you are the SRE.

You see:

```text
Trader
  ↓
Application
  ↓
API
  ↓
Service
  ↓
Risk Service
  ↓
Message Queue
  ↓
Order Router
  ↓
FIX Gateway
  ↓
Network
  ↓
Exchange
```

Every component can fail.

Examples:

- Application Down
- API Timeout
- Database Slow
- Risk Service Down
- Queue Backlog
- FIX Connection Lost
- Network Latency
- Exchange Rejecting Orders
- Market Data Stale

Therefore the trading flow becomes your:

**Monitoring Topology**

# 39. What Should You Monitor?

At every stage, ask four questions:

1. Is it working?
2. Is it fast?
3. Is it processing the expected volume?
4. Is the result correct?

### Order Entry

Monitor:

- Orders/sec
- Order Latency
- Order Rejection Rate
- API Errors
- Request Latency

### Risk

Monitor:

- Risk-Check Latency
- Risk Rejection Rate
- Risk Service Availability
- Queue Depth

### Routing

Monitor:

- Orders Routed
- Routing Latency
- Venue Rejection Rate
- Connection Status

### Execution

Monitor:

- Execution Rate
- Fill Rate
- Partial Fills
- Execution Latency

### Market Data

Monitor:

- Message Rate
- Data Freshness
- Sequence Gaps
- Feed Latency
- Connection Status

### Post Trade

Monitor:

- Trade Capture Rate
- STP Rate
- Settlement Failures
- Reconciliation Breaks

# 40. Four Golden Monitoring Questions

For every trading component, remember:

```text
             TRADING MONITORING
                 ┌─────────┐
                 │ HEALTH  │
                 └────┬────┘
                      │
        ┌─────────────┼─────────────┐
        ▼             ▼             ▼
     VOLUME         LATENCY        ERRORS
        │             │             │
        └─────────────┼─────────────┘
                      ▼
                  CORRECTNESS
```

Ask:

### 1. Health

Is the service available?

### 2. Volume

Is it processing the expected amount of work?

### 3. Latency

Is it processing quickly enough?

### 4. Correctness

Is it producing the correct result?

This model is extremely useful when designing dashboards and alerts.

# 41. Volume Is Not Always Constant

A trading system does not receive the same workload all day.

Example:

```text
06:00    Low
08:00    Increasing
09:30    Very High
11:00    Moderate
15:00    High
16:00    Very High
18:00    Low
```

The exact pattern depends on the market.

Therefore:

> **Static thresholds can be dangerous.**

# 42. Static Threshold Example

Suppose:

```text
CPU > 80%
```

creates an alert.

At market open:

```text
CPU = 85%
```

because:

```text
Order Volume = 5× Normal
```

This may be expected.

If you alert blindly:

```text
CPU > 80%
↓
ALERT
```

you may create noise.

# 43. Context-Aware Threshold

Instead, think:

```text
IF

Market = Open

AND

Order Volume = 5× Baseline

AND

CPU = 85%

AND

Latency = Normal

AND

Error Rate = Normal

THEN

Do Not Necessarily Alert
```

But:

```text
Market = Open

AND

CPU = 95%

AND

Queue Depth ↑

AND

Latency ↑

AND

Error Rate ↑
```

is much more concerning.

This is the beginning of:

**Business-Aware Predictive Monitoring**

# 44. Market Hours Matter

Always know:

```text
Is The Market Open?
```

Because:

```text
Order Rate = 0
```

means something completely different when:

```text
Market Closed
```

versus:

```text
Market Open
```

# 45. Trading Calendar and Monitoring

Monitoring should ideally understand:

- Trading Day
- Holiday
- Weekend
- Early Close
- Late Open
- Market Session
- Pre-Market
- Regular Session
- After-Hours

Example:

```text
IF market_closed:
    expected_order_rate ≈ 0
```

versus:

```text
IF market_open:
    expected_order_rate > 0
```

The exact expected values depend on the business.

# 46. Market Events

Market behavior can change because of events.

Examples:

- Economic Announcement
- Company Earnings
- Interest Rate Decision
- Geopolitical Event
- Market Crash
- Major News
- Index Rebalancing
- Options Expiration

These can cause:

```text
Volume ↑
Volatility ↑
Orders ↑
Market Data ↑
CPU ↑
Network Traffic ↑
Queue Depth ↑
```

A monitoring system should understand these patterns where relevant.

# 47. Trading System Failure Example

Imagine:

```text
Market Open
```

Suddenly:

```text
Order Volume = Normal
CPU          = Normal
Memory       = Normal
```

But:

```text
Order Success Rate

99.9% → 60%
```

This is a major signal.

Why?

Because infrastructure metrics look healthy.

But:

```text
Business Transaction
        ↓
BROKEN
```

This demonstrates why:

> **Infrastructure monitoring alone is not enough for trading systems.**

# 48. Market Data Failure Example

Suppose:

```text
Market Data Messages/sec

Normal  = 50,000
Current = 0
```

and:

```text
Last Message Age = 30 Seconds
```

while:

```text
Market = OPEN
```

This is highly suspicious.

Potential causes:

- Feed Disconnected
- Network Failure
- Consumer Failure
- Exchange Issue
- Market-Data Gateway Failure

# 49. Queue Backlog Example

Suppose:

```text
Incoming Orders = 20,000/sec
Processing      = 15,000/sec
```

Difference:

```text
5,000/sec
```

Backlog will grow.

Example:

```text
Queue Depth

1,000
5,000
10,000
20,000
50,000
100,000
```

This is a classic early-warning pattern.

The service may still be technically "up."

But it is falling behind.

# 50. Latency Degradation Example

Suppose:

```text
Normal Order Latency = 5 ms
```

Historical pattern:

```text
5 ms
5 ms
6 ms
6 ms
7 ms
10 ms
15 ms
30 ms
60 ms
```

Before the service completely fails, latency is already telling you:

> **Something is getting worse.**

This is exactly the type of signal used in predictive monitoring.

# 51. Error Rate and Volume Must Be Viewed Together

Suppose:

```text
Errors = 1,000
```

Is that bad?

Not enough information.

If:

```text
Total Transactions = 10,000,000
```

then:

```text
Error Rate = 0.01%
```

But if:

```text
Total Transactions = 2,000
```

then:

```text
Error Rate = 50%
```

Therefore monitor:

```text
Error Count
+
Total Volume
+
Error Rate
```

# 52. Rejection Rate and Market Conditions

Suppose order rejection rate increases.

Possible causes:

- Risk Limits
- Market Closed
- Invalid Orders
- Price Limits
- Venue Rejection
- System Failure

Do not immediately conclude:

```text
Application Broken
```

Instead ask:

- What type of rejection?
- Why?
- Is it expected?
- Is it correlated with market conditions?
- Did the rejection rate change suddenly?

# 53. Correlation Is Extremely Important

A single metric can be misleading.

Example:

```text
CPU ↑
```

Could mean:

- High Traffic
- Memory Pressure
- Infinite Loop
- Garbage Collection
- Market Event

But:

```text
CPU ↑
+
Queue ↑
+
Latency ↑
+
Errors ↑
+
Order Throughput ↓
```

is much stronger evidence of a system problem.

This is:

**Metric Correlation**

# 54. A Trading SRE Thinks in Chains

Do not think:

```text
CPU High
```

Think:

```text
Market Opened
   ↓
Order Volume Increased
   ↓
Message Rate Increased
   ↓
CPU Increased
   ↓
Queue Started Growing
   ↓
Latency Increased
   ↓
Order Rejection Increased
```

Now you have a story.

That story helps identify the likely root cause.

# 55. Healthy Trading System

A healthy trading system might look like:

```text
Market Open
     ↓
Expected Volume
     ↓
Orders Accepted
     ↓
Risk Checks Normal
     ↓
Orders Routed
     ↓
Executions Received
     ↓
Trades Captured
     ↓
Positions Updated
     ↓
P&L Updated
     ↓
Clearing / Settlement
     ↓
Reconciliation Successful
```

# 56. Unhealthy Trading System

Example:

```text
Market Open
     ↓
Orders Arrive
     ↓
Risk Service Slow
     ↓
Queue Grows
     ↓
Order Latency Increases
     ↓
Orders Time Out
     ↓
Rejections Increase
     ↓
Executions Decrease
     ↓
Business Impact
```

Notice:

The first visible business problem may happen much later than the original technical problem.

This is why predictive monitoring matters.

# 57. Early Warning Signals

Potential early indicators include:

- Latency Slowly Increasing
- Queue Depth Slowly Increasing
- Message Processing Rate Decreasing
- Error Rate Slowly Increasing
- Connection Reconnects Increasing
- Market-Data Freshness Degrading
- Sequence Gaps Increasing
- CPU Approaching Saturation
- Database Response Time Increasing
- Thread Pool Utilization Increasing

These may appear before a major incident.

# 58. Predictive Monitoring Example

Historical data shows:

> Whenever queue depth exceeds 20,000, latency increases within 10 minutes.

Then today:

```text
Queue = 18,000
```

and rising quickly.

Instead of waiting for:

```text
Queue = 100,000
Latency = 2 Seconds
Orders Failing
```

you can detect the pattern earlier.

Possible actions:

- Alert
- Investigate
- Scale
- Restart Unhealthy Consumer
- Reduce Backlog
- Escalate

This is proactive monitoring.

# 59. What You Should Remember

The entire trading process can be remembered with this sentence:

> **An intention becomes an order, an order becomes an execution, an execution becomes a trade, a trade changes a position, and the position affects risk and P&L before the trade is ultimately cleared, settled, and reconciled.**

Short version:

```text
INTENT
  ↓
ORDER
  ↓
EXECUTION
  ↓
TRADE
  ↓
POSITION
  ↓
RISK / P&L
  ↓
CLEARING
  ↓
SETTLEMENT
  ↓
RECONCILIATION
```

# 60. The SRE Version

For your role, memorize this version:

```text
BUSINESS EVENT
      ↓
APPLICATION
      ↓
API / MESSAGE
      ↓
SERVICE
      ↓
QUEUE
      ↓
DATABASE
      ↓
NETWORK
      ↓
TRADING VENUE
      ↓
EXECUTION
      ↓
DOWNSTREAM SYSTEMS
```

And monitor:

- Health
- Volume
- Latency
- Errors
- Queue
- Data Freshness
- Connectivity
- Correctness
- Business Impact

# 61. Final Mental Model

If you remember only one diagram from this chapter, remember this:

```text
                     TRADER
                        │
                        ▼
                     ORDER
                        │
                        ▼
                VALIDATION / RISK
                    │       │
                 FAIL       PASS
                  │           │
                  ▼           ▼
               REJECT      ROUTING
                              │
                              ▼
                           VENUE
                              │
                              ▼
                       MATCHING ENGINE
                              │
                   ┌──────────┴──────────┐
                   │                     │
                NO MATCH               MATCH
                   │                     │
                   ▼                     ▼
               WORKING              EXECUTION
                                         │
                                         ▼
                                  EXECUTION REPORT
                                         │
                                         ▼
                                    TRADE CAPTURE
                                         │
                                         ▼
                                     POSITION
                                         │
                          ┌──────────────┼──────────────┐
                          ▼              ▼              ▼
                         RISK           P&L          PORTFOLIO
                          │              │              │
                          └──────────────┼──────────────┘
                                         ▼
                                      CLEARING
                                         │
                                         ▼
                                     SETTLEMENT
                                         │
                                         ▼
                                   RECONCILIATION
```

And around the entire flow:

```text
                 ┌──────────────────────────┐
                 │       MONITORING         │
                 │                          │
                 │ Health                   │
                 │ Volume                   │
                 │ Latency                  │
                 │ Errors                   │
                 │ Queue Depth              │
                 │ Market Data Freshness    │
                 │ Connectivity             │
                 │ Business Success         │
                 │ Trading Calendar         │
                 │ Market Conditions        │
                 └──────────────────────────┘
```

This is the foundation you need before moving into the deeper topics of:

- Trading Lifecycle
- Order Management Systems
- Execution Management Systems
- Exchanges and Matching Engines
- Market Data
- FIX Protocol
- Trade Lifecycle
- Clearing and Settlement
- Front Office / Middle Office / Back Office Systems
- Trading-System Architecture
- Trading-System Observability
- Predictive Monitoring for Trading Systems
