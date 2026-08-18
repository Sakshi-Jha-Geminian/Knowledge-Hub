# Order Lifecycle

## Introduction

An **order lifecycle** describes everything that happens to a trading order from the moment a trader or trading strategy creates it until the order is finally:

- Filled
- Partially Filled
- Cancelled
- Rejected
- Expired
- Otherwise Completed

A simple view is:

```text
Order Created
      ↓
Order Submitted
      ↓
Order Validated
      ↓
Risk Checks
      ↓
Order Accepted
      ↓
Order Routed
      ↓
Order Enters Market
      ↓
Order Matched
      ↓
Execution
      ↓
Order Status Updated
      ↓
Filled / Partially Filled / Cancelled / Expired
```

Understanding the order lifecycle is extremely important because a trading system is not simply:

```text
Trader → Exchange
```

There are many systems and checks between those two points.

# 1. What Is an Order Lifecycle?

The **order lifecycle** is the sequence of states and events an order goes through during its lifetime.

For example:

```text
NEW
 ↓
ACCEPTED
 ↓
OPEN
 ↓
PARTIALLY_FILLED
 ↓
FILLED
```

But not every order follows this exact path.

An order could instead become:

```text
NEW
 ↓
REJECTED
```

or:

```text
NEW
 ↓
ACCEPTED
 ↓
OPEN
 ↓
CANCELLED
```

or:

```text
NEW
 ↓
ACCEPTED
 ↓
OPEN
 ↓
EXPIRED
```

Therefore:

> **The order lifecycle is a state machine.**

# 2. Why Is the Order Lifecycle Important?

Imagine a trader submits:

```text
BUY 1,000 Shares of ABC
```

The trader needs to know:

- Was the order received?
- Was it accepted?
- Did risk checks pass?
- Was it sent to the exchange?
- Is it currently open?
- Did anything execute?
- How much executed?
- At what price?
- How much remains?
- Was it cancelled?
- Was it rejected?
- Why was it rejected?

Without order lifecycle tracking, a trading system would not know what happened to the order.

# 3. Basic Order Lifecycle

A simplified lifecycle looks like this:

```text
                    ORDER CREATED
                          |
                          ↓
                    ORDER SUBMITTED
                          |
                          ↓
                     VALIDATION
                          |
                          ↓
                     RISK CHECK
                          |
                    +-----+-----+
                    |           |
                  FAIL        PASS
                    |           |
                    ↓           ↓
                REJECTED     ACCEPTED
                                |
                                ↓
                              ROUTED
                                |
                                ↓
                          MARKET / VENUE
                                |
                                ↓
                            EXECUTION
                                |
                    +-----------+-----------+
                    |           |           |
                  FULL        PARTIAL      NONE
                  FILL          FILL       |
                    |           |           |
                    ↓           ↓           ↓
                 FILLED   PARTIALLY_FILLED  OPEN
                                            |
                                  +---------+---------+
                                  |                   |
                              CANCELLED            EXPIRED
```

This is simplified, but it gives us the correct mental model.

# 4. Order Lifecycle vs Trade Lifecycle

These are related but different.

## Order Lifecycle

Tracks the instruction:

```text
BUY 1,000 ABC
```

## Trade Lifecycle

Tracks the actual execution:

```text
500 ABC
₹100.20
```

An order can generate multiple trades.

Example:

```text
ORDER
BUY 1,000
      |
      +---- Trade 1: 300 @ ₹100
      |
      +---- Trade 2: 400 @ ₹100.10
      |
      +---- Trade 3: 300 @ ₹100.20
```

Therefore:

> **One order can result in multiple executions/trades.**

# 5. Order Creation

The lifecycle begins when an order is created.

Example:

```text
BUY
Instrument: ABC
Quantity: 1,000
Order Type: LIMIT
Limit Price: ₹100
```

The trading application creates an order object.

A unique identifier is usually assigned.

Example:

```text
Order ID = ORD-12345
```

# 6. Order ID

An **Order ID** uniquely identifies an order within a trading system or context.

Example:

```text
ORD-20260818-12345
```

It allows systems to track:

- Order Submission
- Validation
- Routing
- Execution
- Modification
- Cancellation
- Final Status

Without identifiers, tracking individual orders would be extremely difficult.

# 7. Client Order ID

A trading application may generate its own identifier, often called a:

**Client Order ID**

Example:

```text
CLIENT-98765
```

This helps the client system correlate its internal order with downstream messages.

# 8. Exchange / Venue Order ID

After an order is accepted by a broker or exchange, another identifier may be assigned.

Example:

```text
Client Order ID
       ↓
Broker Order ID
       ↓
Exchange Order ID
```

The exact identifiers depend on the trading architecture.

Important concept:

> **The same business order may have different identifiers across different systems.**

# 9. Order Submission

After creation, the order is submitted.

```text
Trading Application
        ↓
Order Management System
        ↓
Order Submission
```

The order may be sent to an OMS, EMS, broker, or another execution component depending on the architecture.

# 10. Order Management System

An **Order Management System (OMS)** manages trading orders.

An OMS may handle:

- Order Creation
- Order Modification
- Order Cancellation
- Order Status
- Order Routing
- Order Tracking
- Order History
- Order Allocation

Simplified architecture:

```text
Trader
  ↓
OMS
  ↓
Risk
  ↓
Execution
  ↓
Exchange
```

# 11. Order Validation

Before an order can proceed, the system validates it.

Typical checks may include:

- Is the instrument valid?
- Is quantity valid?
- Is price valid?
- Is order type valid?
- Is account valid?
- Is market open?
- Are required fields present?

Example:

```text
BUY
Quantity = -100
```

This is invalid because quantity cannot normally be negative.

The system may reject the order.

# 12. Pre-Trade Risk Checks

After or as part of validation, the order may pass through a **pre-trade risk engine**.

The risk engine can check:

- Position Limits
- Exposure Limits
- Quantity Limits
- Price Limits
- Buying Power
- Margin
- Credit Limits
- Restricted Instruments
- Account Permissions
- Trading Limits

Example:

```text
Requested:
BUY 1,000,000 Shares

Allowed:
100,000 Shares
```

The risk system may reject the order.

# 13. Order Rejection

If validation or risk checks fail:

```text
ORDER
  ↓
VALIDATION
  ↓
FAIL
  ↓
REJECTED
```

Example:

```text
BUY 1,000,000 ABC

Reason:
Maximum Quantity Exceeded
```

The order never reaches the exchange.

Important:

> **A rejected order is different from an order that reaches the market but does not execute.**

# 14. Accepted Order

If validation and risk checks pass:

```text
ORDER
  ↓
VALIDATION
  ↓
RISK CHECK
  ↓
PASS
  ↓
ACCEPTED
```

An accepted order is allowed to continue through the trading system.

However:

> **Accepted does not necessarily mean filled.**

It simply means the order passed the relevant checks and was accepted by that stage or system.

# 15. Order Routing

After acceptance, the order may be routed toward an execution venue.

Example:

```text
OMS
 ↓
Execution Management System
 ↓
Broker
 ↓
Exchange
```

Or:

```text
OMS
 ↓
Smart Order Router
 ↓
Venue A
Venue B
Venue C
```

The routing architecture depends on the market and organization.

# 16. Smart Order Routing

A **Smart Order Router (SOR)** can determine where an order should be sent.

Factors may include:

- Available Liquidity
- Price
- Fees
- Venue Availability
- Execution Probability
- Market Conditions

Conceptually:

```text
Order
  ↓
SOR
  |
  +---- Venue A
  |
  +---- Venue B
  |
  +---- Venue C
```

# 17. Exchange / Trading Venue

The order eventually reaches a trading venue.

The venue may have a:

**Matching Engine**

The matching engine determines whether compatible buy and sell orders can be matched.

# 18. Matching Engine

A matching engine is a core component of an electronic trading venue.

Its job is broadly:

```text
Buy Orders
    +
Sell Orders
    ↓
Matching Rules
    ↓
Trades
```

Example:

```text
BUY 100 @ ₹100
SELL 100 @ ₹100
```

Result:

```text
TRADE
100 Shares @ ₹100
```

# 19. Order Book

The matching engine generally works with an order book.

Example:

```text
ASKS

₹103 → 500
₹102 → 300
₹101 → 200

---------------

BIDS

₹99 → 400
₹98 → 700
₹97 → 900
```

The order book represents available buying and selling interest according to the venue's rules.

# 20. Order Enters the Book

A limit order may not immediately match.

Example:

```text
BUY 1,000
LIMIT ₹99
```

Suppose:

```text
Best Ask = ₹101
```

The order cannot execute because the buyer's limit is below the available ask.

The order may therefore rest in the book:

```text
BUY ₹99
Quantity = 1,000
```

Status:

```text
OPEN
```

# 21. Order Matching

Suppose another participant submits:

```text
SELL 500
LIMIT ₹99
```

Now the orders may match.

Result:

```text
BUY:
1,000 → 500 Remaining

SELL:
500 → 0 Remaining
```

The buy order becomes:

```text
PARTIALLY_FILLED
```

# 22. Partial Fill

A **partial fill** occurs when only part of the requested quantity is executed.

Example:

```text
Original Order:
BUY 1,000

Execution:
500 @ ₹99

Remaining:
500

Status:
PARTIALLY_FILLED
```

# 23. Multiple Partial Fills

An order can have many executions.

Example:

```text
BUY 1,000

Executions:

Trade 1:
200 @ ₹100

Trade 2:
300 @ ₹100.10

Trade 3:
500 @ ₹100.20
```

Total:

```text
200 + 300 + 500
= 1,000
```

Final status:

```text
FILLED
```

# 24. Average Execution Price

When an order has multiple executions, we can calculate the weighted average execution price.

Example:

```text
200 @ ₹100
300 @ ₹100.10
500 @ ₹100.20
```

Calculation:

```text
(200 × 100
 + 300 × 100.10
 + 500 × 100.20)
÷ 1,000
```

Result:

```text
₹100.13
```

So the order's average execution price is:

```text
₹100.13
```

# 25. Filled Order

An order is **filled** when the complete requested quantity has been executed.

Example:

```text
Requested = 1,000

Executed:
1,000
```

Therefore:

```text
Remaining = 0
Status = FILLED
```

# 26. Cancelled Order

An order can be cancelled before it is fully executed if cancellation is allowed.

Example:

```text
BUY 1,000 @ ₹95
```

Only:

```text
200
```

have executed.

Trader requests cancellation.

Result:

```text
Executed = 200
Remaining = 800
Cancelled = 800
```

The order is no longer active.

# 27. Cancel Request

Cancellation itself can have a lifecycle.

It is not always:

```text
Cancel Clicked
↓
Immediately Cancelled
```

A more realistic flow:

```text
Cancel Request
      ↓
Cancel Pending
      ↓
Venue Response
      ↓
Cancelled
```

This matters because an order might execute while a cancellation request is being processed.

# 28. Cancel Race Condition

Consider:

```text
Order:
BUY 1,000
```

Trader sends:

```text
CANCEL
```

At almost the same time, the exchange matches:

```text
500 Shares
```

The final result might be:

```text
Executed = 500
Cancelled = 500
```

This is one reason trading systems must carefully handle asynchronous events.

# 29. Expired Order

An order can expire.

Example:

```text
LIMIT ORDER
Time-in-Force = DAY
```

If it remains unfilled until the applicable session ends:

```text
OPEN
 ↓
END OF SESSION
 ↓
EXPIRED
```

The exact behavior depends on venue rules.

# 30. Rejected vs Cancelled vs Expired

These are very different.

## Rejected

The system did not accept the order.

```text
ORDER
 ↓
VALIDATION / RISK
 ↓
REJECTED
```

## Cancelled

An accepted/open order was intentionally cancelled.

```text
OPEN
 ↓
CANCEL
 ↓
CANCELLED
```

## Expired

The order reached the end of its allowed lifetime.

```text
OPEN
 ↓
TIME LIMIT
 ↓
EXPIRED
```

# 31. Order State Machine

A useful mental model is:

```text
                  +------------+
                  |    NEW     |
                  +-----+------+
                        |
                        ↓
                  +-----------+
                  | VALIDATED |
                  +-----+-----+
                        |
              +---------+---------+
              |                   |
            FAIL                  PASS
              |                   |
              ↓                   ↓
         +---------+         +----------+
         | REJECTED|         | ACCEPTED |
         +---------+         +-----+----+
                                      |
                                      ↓
                                +-----------+
                                |   OPEN    |
                                +-----+-----+
                                      |
                    +-----------------+----------------+
                    |                 |                |
                    ↓                 ↓                ↓
                PARTIAL            FILLED          CANCELLED
                    |
                    ↓
                  OPEN
                    |
                    ↓
                EXPIRED
```

This is simplified. Real trading systems can have many more states.

# 32. Why Order States Matter

Order states allow systems to answer:

> Where is my order?

For example:

### NEW

The order has just entered processing.

### OPEN

The order remains active.

### PARTIALLY_FILLED

Some quantity executed but some remains.

### FILLED

The complete quantity executed.

### CANCELLED

The remaining quantity is no longer active.

# 33. Order Events

Instead of thinking only about states, trading systems also process **events**.

Examples:

- OrderCreated
- OrderSubmitted
- OrderAccepted
- OrderRejected
- OrderRouted
- OrderOpened
- ExecutionReceived
- OrderPartiallyFilled
- OrderFilled
- CancelRequested
- OrderCancelled
- OrderExpired
- OrderModified

Events tell us:

> **What happened?**

States tell us:

> **What is the current condition of the order?**

# 34. State vs Event

This distinction is extremely important.

### Event

Something happened.

```text
ExecutionReceived
```

### State

The current status is:

```text
PARTIALLY_FILLED
```

Example:

```text
ExecutionReceived
        ↓
Order State Changes
        ↓
PARTIALLY_FILLED
```

# 35. Event-Driven Trading Systems

Modern trading systems are often event-driven.

Conceptually:

```text
Order Event
     ↓
Message Bus
     ↓
Consumers
     |
     +---- Risk
     |
     +---- Position
     |
     +---- P&L
     |
     +---- Monitoring
     |
     +---- Audit
```

This allows multiple systems to react to the same event.

# 36. Execution Report

After an execution, a downstream system may receive an execution report.

Example:

```text
Order ID: ORD123
Execution ID: EXE456
Instrument: ABC
Side: BUY
Executed Quantity: 500
Price: ₹100.20
Status: PARTIALLY_FILLED
Timestamp: 10:32:14.123
```

The trading system can use this information to update the order.

# 37. Execution ID

Each execution may have its own identifier.

Example:

```text
Order ID:
ORD123

Execution ID:
EXE001
```

If an order is filled through multiple trades:

```text
ORD123
 |
 +-- EXE001
 |
 +-- EXE002
 |
 +-- EXE003
```

This allows detailed trade-level tracking.

# 38. Parent Order and Child Orders

Large institutional orders may be represented as:

```text
Parent Order

BUY 100,000
       |
       +---- Child 1 → 10,000
       +---- Child 2 → 15,000
       +---- Child 3 → 20,000
       +---- Child 4 → 5,000
       +---- ...
```

The parent order tracks the overall objective.

Child orders represent individual execution attempts.

# 39. Parent Order Lifecycle

A parent order may have:

```text
PARENT
BUY 100,000
     ↓
Execution Algorithm
     ↓
Child Orders
     ↓
Multiple Venues
     ↓
Multiple Executions
     ↓
Parent Progress
```

Example:

```text
Target     = 100,000
Executed   = 65,000
Remaining  = 35,000
```

The parent order is still incomplete.

# 40. Order Amendments

An order may sometimes be modified.

Example:

```text
Original:
BUY 1,000 @ ₹100

Modified:
BUY 1,000 @ ₹99
```

The modification may generate its own event:

```text
OrderModifyRequested
        ↓
ModificationAccepted
        ↓
OrderUpdated
```

Depending on venue rules, modifying price or quantity can affect queue priority.

# 41. Cancel-and-Replace

Some systems handle modifications as:

```text
Cancel Existing Order
        ↓
Create Replacement Order
```

This is commonly called:

**Cancel-and-Replace**

Example:

```text
Old:
BUY 1,000 @ ₹100
        ↓
Cancel
        ↓
New:
BUY 1,000 @ ₹99
```

This can have implications for:

- Order ID
- Queue Position
- Execution
- Audit Trail

# 42. Order Timestamps

Trading systems must carefully record timestamps.

Important timestamps may include:

- Order Creation Time
- Order Submission Time
- Risk Check Time
- Exchange Acceptance Time
- Execution Time
- Cancellation Request Time
- Cancellation Confirmation Time

Example:

```text
09:30:00.100
Order Created

09:30:00.105
Submitted

09:30:00.110
Risk Passed

09:30:00.120
Exchange Accepted

09:30:00.130
Execution
```

# 43. Why Timing Matters

Trading systems often operate at very high speed.

A difference of milliseconds or microseconds can matter.

For example:

```text
Order A → 10:00:00.001

Order B → 10:00:00.002
```

Depending on venue rules, Order A may have priority.

Therefore:

> **Accurate timestamps are critical for trading-system analysis and auditing.**

# 44. Order Latency

**Order latency** measures how long it takes for an order or message to travel through part of the system.

Example:

```text
Trader
 ↓ 2 ms
OMS
 ↓ 1 ms
Risk
 ↓ 2 ms
Router
 ↓ 3 ms
Exchange
```

Total simplified latency:

```text
2 + 1 + 2 + 3
= 8 ms
```

In real systems, latency measurement is more detailed.

# 45. Important Latency Metrics

Trading platforms may monitor:

- Order Creation → Submission
- Submission → Risk
- Risk → Routing
- Routing → Exchange
- Exchange → Execution Report
- Execution Report → Position Update

These can help identify performance bottlenecks.

# 46. Order Lifecycle Monitoring

For an SRE or Monitoring Engineer, useful metrics include:

## Volume

- Orders/sec
- Executions/sec
- Cancel Requests/sec

## Reliability

- Order Reject Rate
- Order Failure Rate
- Execution Failure Rate

## Performance

- Order Latency
- Execution Latency
- Risk Check Latency
- Routing Latency

## Business Metrics

- Fill Rate
- Partial Fill Rate
- Average Execution Price
- Slippage

# 47. Order Reject Rate

Suppose:

```text
Total Orders = 100,000
Rejected    = 200
```

Reject rate:

```text
200 / 100,000 × 100
= 0.2%
```

If it suddenly increases:

```text
0.2%
 ↓
5%
 ↓
15%
```

that could indicate a serious problem.

# 48. Order Fill Rate

Suppose:

```text
Orders Submitted   = 10,000
Orders Fully Filled = 9,000
```

Simplified fill rate:

```text
9,000 / 10,000 × 100
= 90%
```

However, real execution analysis can distinguish between:

- Order-Level Fill Rate
- Quantity-Level Fill Rate
- Partial Fills
- Execution Probability

# 49. Order Processing Latency

Suppose:

```text
Order Received:
10:00:00.000

Order Accepted:
10:00:00.005
```

Processing latency:

```text
5 ms
```

If normal latency is:

```text
5 ms
```

but suddenly becomes:

```text
500 ms
```

the trading system may be experiencing a serious performance problem.

# 50. Example Trading-System Incident

Imagine:

```text
Normal Order Latency:
5 ms
```

Suddenly:

```text
Order Latency:
2,000 ms
```

At the same time:

```text
Order Queue:
↑↑↑

CPU:
95%

Thread Pool:
Exhausted

Rejected Orders:
↑
```

Possible root causes include:

- Application Overload
- Database Latency
- Message Queue Backlog
- Network Problems
- External Venue Latency
- Thread Starvation
- Lock Contention

The order lifecycle helps identify exactly where the delay occurred.

# 51. Order Queue

Orders may be placed into queues before processing.

Example:

```text
Incoming Orders
       ↓
+----------------+
| Order Queue    |
+----------------+
       ↓
Risk Engine
```

If processing is slower than incoming traffic:

```text
Incoming Rate > Processing Rate
```

the queue grows.

# 52. Queue Backlog

Example:

### Normal

```text
Incoming   = 10,000/sec
Processing = 10,000/sec

Queue ≈ Stable
```

### Problem

```text
Incoming   = 15,000/sec
Processing = 10,000/sec
```

Then:

```text
Queue
 ↓
100
 ↓
1,000
 ↓
10,000
 ↓
100,000
```

This can cause increasing order latency.

# 53. Duplicate Orders

One dangerous problem in trading systems is **duplicate order submission**.

Example:

```text
Client Sends:
BUY 1,000
```

The client does not receive a response because of a network problem.

It may incorrectly assume:

```text
Order Failed
```

and send again:

```text
BUY 1,000
```

But the first order may actually have been accepted.

Potential result:

```text
Order 1 → BUY 1,000

Order 2 → BUY 1,000

Total Exposure = 2,000
```

This is why idempotency and order-state reconciliation are extremely important.

# 54. Idempotency

**Idempotency** means that repeating the same request does not unintentionally create duplicate business effects.

For trading systems, unique client order identifiers and carefully designed request handling can help prevent duplicate orders.

Conceptually:

```text
Request:
CLIENT-123

Retry:
CLIENT-123
```

The system can recognize:

> "This is the same request."

rather than creating an unintended duplicate.

Exact implementation varies by system and protocol.

# 55. Order Reconciliation

Trading systems need reconciliation because systems can temporarily disagree.

Example:

```text
Client Says:
ORDER = OPEN

Exchange Says:
ORDER = FILLED
```

This discrepancy must be detected and resolved.

A reconciliation process may compare:

```text
Internal Orders
      vs
Broker Orders
      vs
Exchange Executions
```

# 56. Position Update

When an order executes, the position system needs to update the trader's position.

Example:

```text
Initial Position:
0 Shares

Execution:
BUY 100

New Position:
+100 Shares
```

Another execution:

```text
SELL 40
```

Position:

```text
+60 Shares
```

# 57. P&L Update

Executions also affect profit and loss calculations.

Example:

```text
BUY 100 @ ₹100
```

Later:

```text
Market Price = ₹110
```

Simplified unrealized gain:

```text
(₹110 - ₹100) × 100
= ₹1,000
```

The execution event therefore flows into:

```text
Execution
 ↓
Position
 ↓
P&L
 ↓
Risk
 ↓
Monitoring
```

# 58. Audit Trail

Financial trading systems require detailed records of what happened.

An audit trail may contain:

- Who submitted the order?
- When?
- What instrument?
- What quantity?
- What price?
- What order type?
- Which account?
- Which system?
- What risk checks occurred?
- What happened afterward?

Example:

```text
09:30:00
Order Created

09:30:01
Risk Approved

09:30:02
Sent to Broker

09:30:03
Accepted

09:30:04
500 Executed

09:30:05
Remaining Cancelled
```

# 59. Why Auditability Matters

Audit trails help with:

- Troubleshooting
- Compliance
- Dispute Resolution
- Incident Investigation
- Trade Reconstruction
- Risk Analysis
- Regulatory Requirements

For financial systems:

> **Being able to explain what happened is almost as important as making it happen.**

# 60. Complete Example

Let's follow one order from beginning to end.

Trader wants:

```text
BUY 1,000 ABC
LIMIT ₹100
DAY
```

### Step 1 — Creation

```text
Order ID = ORD123

Status:
NEW
```

### Step 2 — Validation

System checks:

- Instrument Valid?
- Quantity Valid?
- Price Valid?
- Account Valid?

Result:

```text
PASS
```

### Step 3 — Risk

System checks:

- Buying Power?
- Position Limit?
- Exposure?
- Trading Permission?

Result:

```text
PASS
```

### Step 4 — Acceptance

```text
Status:
ACCEPTED
```

### Step 5 — Routing

Order is sent to the appropriate venue.

```text
ROUTED
```

### Step 6 — Open

Market conditions:

```text
Best Ask = ₹101
Our Limit = ₹100
```

Therefore:

```text
No Immediate Execution
```

Order rests in the book.

```text
Status:
OPEN
```

### Step 7 — Partial Execution

A seller becomes available:

```text
SELL 400 @ ₹100
```

Our order executes:

```text
400 @ ₹100
```

Remaining:

```text
600
```

Status:

```text
PARTIALLY_FILLED
```

### Step 8 — More Execution

Another seller provides:

```text
SELL 600 @ ₹100
```

Remaining quantity:

```text
0
```

Status:

```text
FILLED
```

# 61. Complete Lifecycle Diagram

```text
                         TRADER / STRATEGY
                                |
                                ↓
                         ORDER CREATION
                                |
                                ↓
                         ORDER ID CREATED
                                |
                                ↓
                         ORDER SUBMISSION
                                |
                                ↓
                          VALIDATION
                                |
                       +--------+--------+
                       |                 |
                     FAIL               PASS
                       |                 |
                       ↓                 ↓
                   REJECTED          RISK CHECK
                                         |
                                +--------+--------+
                                |                 |
                              FAIL               PASS
                                |                 |
                                ↓                 ↓
                            REJECTED          ACCEPTED
                                                  |
                                                  ↓
                                               ROUTING
                                                  |
                                                  ↓
                                         BROKER / VENUE
                                                  |
                                                  ↓
                                           MATCHING ENGINE
                                                  |
                                  +---------------+---------------+
                                  |               |               |
                               NO MATCH        PARTIAL          FULL
                                  |               |               |
                                  ↓               ↓               ↓
                                OPEN       PARTIALLY_FILLED     FILLED
                                  |               |
                         +--------+--------+      |
                         |                 |      |
                      CANCEL             EXPIRE   |
                         |                 |      |
                         ↓                 ↓      |
                     CANCELLED          EXPIRED  |
                                                 ↓
                                              COMPLETE
                                                  |
                                                  ↓
                                        POSITION / P&L / RISK
                                                  |
                                                  ↓
                                             AUDIT TRAIL
```

# 62. What an SRE Should Monitor

If you are monitoring a financial trading platform, the order lifecycle gives you many monitoring points.

## Order Intake

Monitor:

- Orders/sec
- Order Ingestion Latency
- Queue Depth

## Validation

Monitor:

- Validation Failures
- Validation Latency
- Invalid Order Rate

## Risk

Monitor:

- Risk Rejection Rate
- Risk-Check Latency
- Risk Engine Availability

## Routing

Monitor:

- Routing Latency
- Routing Failures
- Venue Connectivity

## Execution

Monitor:

- Execution Latency
- Fill Rate
- Partial Fills
- Slippage

## Completion

Monitor:

- Cancellation Rate
- Expiration Rate
- Reconciliation Failures
- Position Update Failures

# 63. Golden Signals for Order Processing

The classic monitoring idea of:

- Latency
- Traffic
- Errors
- Saturation

can be applied to order processing.

## Traffic

- Orders/sec
- Executions/sec

## Latency

- Order Processing Latency
- Risk Latency
- Routing Latency
- Execution Latency

## Errors

- Rejected Orders
- Failed Requests
- Connection Failures
- Reconciliation Errors

## Saturation

- CPU
- Memory
- Thread Pools
- Message Queues
- Connection Pools

# 64. Example Monitoring Dashboard

A trading order dashboard might show:

```text
+---------------------------------------+
|        ORDER PROCESSING               |
+---------------------------------------+
| Orders/sec             25,400         |
| Executions/sec          8,900         |
| Reject Rate               0.3%        |
| Fill Rate                91.4%        |
| Avg Latency                4 ms       |
| P99 Latency               18 ms       |
| Queue Depth                120        |
+---------------------------------------+
```

This gives engineers a quick view of system health.

# 65. P95 and P99 Order Latency

Average latency can hide problems.

Example:

```text
Average = 5 ms

P95 = 10 ms
P99 = 100 ms
```

This means a small percentage of orders are taking much longer.

For trading systems, tail latency can be extremely important.

# 66. Order Lifecycle and Predictive Monitoring

The order lifecycle can also be used for predictive monitoring.

Suppose historical data shows:

```text
Queue Depth ↑
      ↓
Order Latency ↑
      ↓
Reject Rate ↑
```

The system can potentially learn that:

```text
High Queue Depth
+
High CPU
+
Increasing Latency
```

often precedes:

```text
Order Processing Failure
```

A predictive monitoring system can therefore alert before the system completely fails.

# 67. Example Predictive Scenario

Normal:

```text
Queue    = 100
Latency  = 5 ms
Rejects  = 0.2%
```

Warning:

```text
Queue    = 5,000
Latency  = 30 ms
Rejects  = 1%
```

Critical trend:

```text
Queue    = 50,000
Latency  = 200 ms
Rejects  = 8%
```

A predictive system could detect:

```text
Queue Growth
+
Latency Growth
+
Reject Growth
```

and raise an early warning.

# 68. Common Order Lifecycle Failures

Some important failure scenarios include:

## 1. Order Rejected

Possible causes:

- Risk Limit
- Invalid Order
- Account Restriction

## 2. Order Stuck

Status remains:

```text
OPEN
```

for unusually long periods.

Possible causes:

- No Liquidity
- Routing Issue
- Venue Problem
- Application Issue

## 3. Execution Delay

Order accepted but execution report delayed.

Possible causes:

- Network
- Venue
- Message Queue
- Processing Bottleneck

## 4. Cancel Failure

```text
Cancel Requested
```

but order remains active.

This can be particularly dangerous.

## 5. Reconciliation Failure

```text
Internal:
OPEN

Venue:
FILLED
```

This must be investigated.

# 69. Stuck Order

A **stuck order** is an order that remains in an unexpected state for longer than normal.

Example:

Expected:

```text
OPEN → FILLED
```

But:

```text
OPEN
 ↓
5 Minutes
 ↓
10 Minutes
 ↓
30 Minutes
```

Possible causes:

- No Market Liquidity
- Venue Connectivity Issue
- Message Processing Issue
- State Synchronization Issue
- Broker Problem
- Application Bug

# 70. Orphaned Order

An **orphaned order** can refer to an order whose state or ownership relationship has become inconsistent across systems.

Example:

```text
Trading System:
Order Exists

Execution System:
Order Unknown
```

or:

```text
Parent:
Missing

Child:
Still Active
```

Such inconsistencies can require reconciliation.

# 71. Order State Synchronization

Multiple systems may track the same order.

Example:

```text
OMS
 |
 +---- Risk System
 |
 +---- Execution System
 |
 +---- Broker
 |
 +---- Exchange
 |
 +---- Position System
```

All systems need consistent state.

If they disagree:

```text
OMS:
FILLED

Broker:
OPEN
```

the system needs investigation and reconciliation.

# 72. Event Ordering

Trading systems must also handle events in the correct order.

Example:

```text
Execution
Cancel
```

versus

```text
Cancel
Execution
```

These can produce different outcomes.

Therefore, systems must carefully handle:

- Event Timestamps
- Sequence Numbers
- Message Ordering
- Duplicate Messages
- Missing Messages

# 73. Sequence Numbers

Trading protocols and market-data systems may use sequence numbers to detect:

- Missing Messages
- Duplicate Messages
- Out-of-Order Messages

Example:

```text
1001
1002
1003
1005
```

The system notices:

```text
1004 Missing
```

This can trigger recovery or reconciliation procedures.

# 74. Duplicate Execution Messages

Suppose the system receives:

```text
Execution ID = EXE123
```

twice.

Without duplicate detection, the system might incorrectly update:

- Position
- P&L

twice.

Therefore:

> **Execution processing must often be idempotent as well.**

# 75. Exactly-Once vs At-Least-Once Processing

Distributed systems often discuss message delivery semantics.

## At-Most-Once

Message may be processed zero or one time.

## At-Least-Once

Message may be delivered more than once.

## Exactly-Once

The system attempts to ensure one logical business effect.

In financial systems, duplicate prevention and reconciliation are especially important because an incorrect duplicate execution can affect:

- Position
- P&L
- Risk
- Cash
- Exposure

# 76. Order Lifecycle and Database

Trading systems may persist order information in databases.

Example:

## Orders Table

```text
Order ID
Account
Instrument
Side
Quantity
Price
Type
Status
Timestamp
```

## Executions Table

```text
Execution ID
Order ID
Quantity
Price
Timestamp
Venue
```

Relationship:

```text
ORDER
  |
  +---- EXECUTION
  |
  +---- EXECUTION
  |
  +---- EXECUTION
```

# 77. Order Lifecycle and Logs

Logs can help reconstruct what happened.

Example:

```text
10:00:01 INFO
Order ORD123 Received

10:00:01 INFO
Risk Check Passed

10:00:01 INFO
Order Routed

10:00:02 INFO
Execution EXE456 Received

10:00:02 INFO
Order ORD123 Partially Filled
```

When investigating incidents, engineers correlate:

```text
Logs
+
Metrics
+
Traces
+
Order Events
```

# 78. Distributed Tracing

In a modern architecture:

```text
Client
 ↓
API Gateway
 ↓
OMS
 ↓
Risk Service
 ↓
Execution Service
 ↓
Broker Adapter
```

Distributed tracing can show where time was spent.

Example:

```text
Total = 25 ms

API Gateway    = 2 ms
OMS            = 4 ms
Risk           = 3 ms
Execution      = 5 ms
Broker Adapter = 11 ms
```

This makes performance investigation much easier.

# 79. Order Lifecycle and Observability

A complete observability strategy uses:

- Metrics
- Logs
- Traces
- Events

## Metrics

Tell us:

```text
How Much?
How Fast?
How Often?
```

## Logs

Tell us:

```text
What Happened?
```

## Traces

Tell us:

```text
Where Did The Request Travel?
Where Did It Spend Time?
```

## Events

Tell us:

```text
What State-Changing Action Occurred?
```

# 80. Order Lifecycle Example With Observability

Suppose an order takes:

```text
500 ms
```

Metrics show:

```text
P99 Latency ↑
```

Trace shows:

```text
OMS            = 10 ms
Risk           = 20 ms
Execution      = 30 ms
Broker Adapter = 440 ms
```

Logs show:

```text
Broker Connection Retry
```

Now the likely problem is:

```text
Broker Connectivity
```

rather than the entire trading application.

# 81. Important Order Lifecycle Terms

- **Order**: Instruction to buy or sell
- **Order ID**: Unique order identifier
- **Client Order ID**: Client-generated identifier
- **Execution ID**: Identifier for an execution
- **OMS**: Order Management System
- **Risk Engine**: Performs risk checks
- **Router**: Routes orders to venues
- **Matching Engine**: Matches compatible orders
- **Order Book**: Contains trading interest
- **Fill**: Executed quantity
- **Partial Fill**: Part of order executed
- **Rejection**: Order not accepted
- **Cancellation**: Active order stopped
- **Expiration**: Order lifetime ended
- **Reconciliation**: Resolving state differences
- **Audit Trail**: Historical record of actions

# 82. Complete Order Lifecycle Cheat Sheet

```text
1. CREATE
   ↓
2. ASSIGN ORDER ID
   ↓
3. SUBMIT
   ↓
4. VALIDATE
   ↓
5. PRE-TRADE RISK
   ↓
6. ACCEPT / REJECT
   ↓
7. ROUTE
   ↓
8. ENTER VENUE
   ↓
9. MATCH
   ↓
10. EXECUTE
   ↓
11. UPDATE ORDER
   ↓
12. UPDATE POSITION
   ↓
13. UPDATE P&L / RISK
   ↓
14. AUDIT
```

Possible final states:

- FILLED
- CANCELLED
- EXPIRED
- REJECTED

# 83. Simple Real-World Analogy

Think of ordering food online.

```text
Customer
 ↓
Places Order
 ↓
Restaurant Validates
 ↓
Restaurant Accepts
 ↓
Kitchen Processes
 ↓
Food Prepared
 ↓
Delivery
 ↓
Completed
```

Trading is similar:

```text
Trader
 ↓
Creates Order
 ↓
Trading System Validates
 ↓
Risk Checks
 ↓
Order Accepted
 ↓
Venue Processes
 ↓
Matching
 ↓
Execution
 ↓
Position Updated
```

The difference is that financial systems operate at much higher speed and require strict control, tracking, and auditability.

# 84. The Most Important Mental Model

Always think:

```text
ORDER
  ↓
Can the system accept it?
  ↓
Can the trader take this risk?
  ↓
Where should it go?
  ↓
Can it match?
  ↓
Did it execute?
  ↓
How much executed?
  ↓
What remains?
  ↓
What is the final state?
  ↓
Did position / P&L / risk update?
```

This is the order lifecycle.

# 85. Key Takeaways

1. An order lifecycle describes the complete journey of an order.
2. An order is an instruction
