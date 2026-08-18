# Order Types

## Introduction

An **order** is an instruction given to a broker, exchange, or trading system to buy or sell a financial instrument.

For example:

```text
BUY 100 shares of ABC
```

looks simple.

But a real trading system needs to know much more:

* What should be bought?
* How much should be bought?
* At what price?
* How quickly should it execute?
* Should the order remain active?
* What should happen if only part of it can be executed?
* Should it be cancelled after a certain time?
* Should it activate only after a particular price is reached?

This is why financial markets provide different **order types**.

The most important order types are:

* Market Order
* Limit Order
* Stop Order
* Stop-Limit Order

There are also different instructions controlling how long an order remains active:

* Day
* GTC
* IOC
* FOK

Understanding order types is one of the most important foundations of trading.

---

# 1. What Is an Order?

An **order** is an instruction to buy or sell a financial instrument.

Example:

```text
BUY
Instrument: ABC
Quantity: 100
```

or:

```text
SELL
Instrument: ABC
Quantity: 100
```

An order is **not necessarily a trade**.

This distinction is extremely important.

```text
ORDER
  ↓
Matching / Execution
  ↓
TRADE
```

An order becomes a trade only when it is actually executed.

---

# 2. Why Do Different Order Types Exist?

Imagine you want to buy a stock currently trading around ₹100.

You have different priorities.

### Situation 1 — "Buy immediately"

You care more about execution than exact price.

You may use:

```text
Market Order
```

### Situation 2 — "I will buy only at ₹98 or lower"

You care about price.

You may use:

```text
Limit Order
```

### Situation 3 — "Buy only after price rises above ₹105"

You want an order to activate after a trigger.

You may use:

```text
Stop Order
```

### Situation 4 — "After ₹105, activate my order, but don't pay above ₹106"

You need both a trigger and a price limit.

You may use:

```text
Stop-Limit Order
```

So the basic idea is:

```text
Market Order
    ↓
"I want execution."

Limit Order
    ↓
"I want a specific price or better."

Stop Order
    ↓
"Activate when price reaches a trigger."

Stop-Limit
    ↓
"Activate at trigger, then respect my limit price."
```

---

# 3. Important Components of an Order

A real order contains multiple fields.

A simplified order might look like:

```text
Order ID: ORD12345
Instrument: ABC
Side: BUY
Quantity: 100
Order Type: LIMIT
Limit Price: ₹100
Time-in-Force: DAY
```

Important fields include:

| Field         | Meaning                                  |
| ------------- | ---------------------------------------- |
| Order ID      | Unique identifier                        |
| Instrument    | What is being traded                     |
| Side          | Buy or Sell                              |
| Quantity      | Number of units                          |
| Order Type    | How the order should execute             |
| Price         | Relevant order price                     |
| Trigger Price | Price that activates a conditional order |
| Time-in-Force | How long the order remains active        |
| Account       | Account placing the order                |
| Status        | Current state of the order               |

---

# 4. Buy Order

A **buy order** instructs the system to purchase an instrument.

Example:

```text
BUY
ABC
100 shares
```

The trader wants to increase their long exposure or reduce a short position, depending on the existing position.

---

# 5. Sell Order

A **sell order** instructs the system to sell an instrument.

Example:

```text
SELL
ABC
100 shares
```

A sell order may:

* Reduce a long position
* Open/increase a short position
* Transfer/close exposure depending on the market and account

Therefore:

> **Buy does not always mean "opening a long position," and sell does not always mean "closing a position."**

The existing position matters.

---

# 6. Market Order

A **market order** is an instruction to execute immediately at the best available prices in the market, subject to market conditions and venue rules.

Example:

```text
BUY
ABC
100 shares
Market Order
```

The trader is saying:

> "Execute this order using the available market liquidity."

---

# 7. Market Order Example

Suppose the order book is:

```text
SELL SIDE

₹100.00 → 50 shares
₹100.10 → 100 shares
₹100.20 → 200 shares
```

You submit:

```text
BUY 150 shares
MARKET
```

The system may execute:

```text
50 shares @ ₹100.00
100 shares @ ₹100.10
```

Average execution price:

```text
(50 × 100 + 100 × 100.10) / 150

= ₹100.0667
```

So your average execution price is approximately:

```text
₹100.07
```

This demonstrates that a market order does not necessarily execute the entire quantity at one price.

---

# 8. Main Characteristic of a Market Order

The main priority is:

> **Execution**

The trader generally does not specify an exact execution price.

Therefore:

```text
Market Order
    ↓
Higher execution certainty
    +
Less price certainty
```

---

# 9. Advantages of Market Orders

Market orders can be useful when:

* Immediate execution is important
* The instrument is highly liquid
* The spread is small
* The trader does not need a precise price
* The market is moving quickly

---

# 10. Risks of Market Orders

Market orders can experience:

* Slippage
* Unexpected execution prices
* Multiple execution prices
* Large market impact
* Poor execution in low-liquidity markets

Example:

```text
Expected:
₹100

Actual:
₹103
```

The difference may represent unfavorable execution caused by market conditions.

---

# 11. Limit Order

A **limit order** specifies the maximum price a buyer is willing to pay or the minimum price a seller is willing to accept.

### Buy Limit

```text
BUY
100 shares
Limit = ₹100
```

The trader does not want to pay more than ₹100.

### Sell Limit

```text
SELL
100 shares
Limit = ₹110
```

The trader does not want to sell below ₹110.

---

# 12. Buy Limit Order

Suppose:

```text
Current price = ₹105
```

You submit:

```text
BUY 100
LIMIT ₹100
```

The order can execute only at ₹100 or a better eligible price.

It will not execute at:

```text
₹101
₹102
₹103
₹104
```

if those prices are above the specified buy limit.

---

# 13. Sell Limit Order

Suppose:

```text
Current price = ₹105
```

You submit:

```text
SELL 100
LIMIT ₹110
```

The order can execute at ₹110 or a better eligible price.

It should not execute below your specified sell limit.

---

# 14. Main Characteristic of a Limit Order

The main priority is:

> **Price control**

Therefore:

```text
Limit Order
    ↓
Higher price certainty
    +
Lower execution certainty
```

A limit order may remain open for a long time or expire without execution.

---

# 15. Advantages of Limit Orders

Limit orders can help:

* Control execution price
* Reduce unexpected price movement
* Avoid paying above a desired buy price
* Avoid selling below a desired sell price
* Reduce some forms of slippage

---

# 16. Risks of Limit Orders

The biggest risk is:

> **The order may never execute.**

Example:

```text
Current price = ₹100

Buy Limit = ₹90
```

If the price never reaches ₹90:

```text
Execution = 0
```

The trader gets price control but no guaranteed execution.

---

# 17. Market Order vs Limit Order

| Feature                     | Market Order                                          | Limit Order                       |
| --------------------------- | ----------------------------------------------------- | --------------------------------- |
| Main priority               | Execution                                             | Price                             |
| Price guaranteed?           | No                                                    | Within order terms                |
| Execution guaranteed?       | No, though generally designed for immediate execution | No                                |
| Slippage risk               | Higher                                                | Usually more controlled           |
| Can remain open?            | Usually intended for immediate execution              | Yes                               |
| Useful in illiquid markets? | Can be risky                                          | Often provides more price control |

### Memory Trick

> **Market = "Execute now."**

> **Limit = "Execute only at my acceptable price."**

---

# 18. Stop Order

A **stop order** is a conditional order that becomes active when a specified trigger price is reached.

Before the trigger:

```text
Order is waiting.
```

After the trigger:

```text
Order becomes active according to the order's rules.
```

---

# 19. Stop Order Example — Sell

Suppose you own a stock:

```text
Current price = ₹100
```

You want protection if the price falls.

You place:

```text
SELL
100 shares
STOP = ₹90
```

The order is waiting while price remains above the trigger.

If the trigger condition is met:

```text
₹100
 ↓
₹97
 ↓
₹93
 ↓
₹90
```

the stop order becomes active.

Depending on the specific stop-order rules, it may then execute as a marketable order.

---

# 20. Stop Order Example — Buy

Stop orders can also be used for entering a position.

Suppose:

```text
Current price = ₹100
```

You believe the price may continue upward if it breaks ₹110.

You submit:

```text
BUY
100 shares
STOP = ₹110
```

If the trigger condition is met:

```text
₹100
 ↓
₹105
 ↓
₹110
```

the order becomes active.

---

# 21. Stop Order vs Limit Order

This is an important distinction.

### Limit Order

The order is active according to its instructions and waits for an acceptable price.

```text
BUY LIMIT ₹100
```

Means:

> Buy at ₹100 or better.

### Stop Order

The order waits for a trigger condition.

```text
BUY STOP ₹110
```

Means:

> Activate the order when the stop condition is reached.

---

# 22. Stop-Limit Order

A **stop-limit order** combines:

* A stop/trigger price
* A limit price

Example:

```text
BUY
Quantity = 100

Stop Price = ₹110
Limit Price = ₹112
```

The order remains inactive until the stop trigger is reached.

After activation, it becomes a limit order with the specified limit price.

---

# 23. Stop-Limit Example

Suppose:

```text
Current price = ₹100
Stop = ₹110
Limit = ₹112
```

Price moves:

```text
₹100
₹105
₹109
₹110
```

At ₹110:

```text
Stop condition triggered
```

The order becomes a limit order:

```text
BUY
100
LIMIT ₹112
```

It may execute at ₹112 or better, depending on available liquidity.

---

# 24. Stop Order vs Stop-Limit Order

| Stop Order                             | Stop-Limit                   |
| -------------------------------------- | ---------------------------- |
| Has trigger                            | Has trigger                  |
| Becomes active after trigger           | Becomes active after trigger |
| Typically becomes marketable           | Becomes a limit order        |
| More execution certainty after trigger | More price control           |
| Can experience significant slippage    | May remain unfilled          |

### Memory Trick

```text
STOP
↓
Trigger

STOP-LIMIT
↓
Trigger + Price Limit
```

---

# 25. Major Risk of Stop-Limit Orders

Suppose:

```text
Stop = ₹90
Limit = ₹89
```

The market suddenly falls:

```text
₹95
 ↓
₹90
 ↓
₹85
```

The stop is triggered, but the market may already be below the limit price.

Therefore:

```text
Order may NOT execute.
```

This is the trade-off:

> **More price control can mean less execution certainty.**

---

# 26. Stop-Loss Order

A **stop-loss** is a risk-management instruction designed to limit potential losses.

It is often implemented using a stop order or stop-limit order, depending on the market and strategy.

Example:

```text
Entry = ₹100

Stop-loss = ₹90
```

The trader is attempting to exit if price moves significantly against the position.

---

# 27. Take-Profit Order

A **take-profit** instruction is designed to exit a position when a target level is reached.

Example:

```text
Entry = ₹100
Target = ₹120
```

The trader wants to capture gains around ₹120.

A take-profit can commonly be implemented with a limit order, depending on the trading setup.

---

# 28. Stop-Loss vs Take-Profit

Suppose:

```text
Entry = ₹100
```

The trader defines:

```text
Stop-loss = ₹90
Take-profit = ₹120
```

Conceptually:

```text
            ₹120
              ↑
        TAKE PROFIT
              |
              |
          ENTRY ₹100
              |
              |
        STOP LOSS
              ↓
             ₹90
```

This defines a planned exit for both favorable and unfavorable price movements.

---

# 29. Trailing Stop

A **trailing stop** is a stop order whose trigger follows the market price according to a defined trailing amount or percentage.

Example:

```text
Stock = ₹100
Trailing distance = ₹10
```

Initial stop:

```text
₹90
```

Price rises to:

```text
₹110
```

The trailing stop may move to:

```text
₹100
```

Price rises to:

```text
₹120
```

The trailing stop may move to:

```text
₹110
```

The exact behavior depends on the implementation.

---

# 30. Trailing Stop Example

```text
Price = ₹100
Stop = ₹90

Price → ₹110
Stop → ₹100

Price → ₹120
Stop → ₹110

Price → ₹115
Stop remains around ₹110
```

The stop generally moves in the favorable direction but does not move backward in the same way.

---

# 31. Fixed Trailing Amount

A trailing stop can use a fixed price distance.

Example:

```text
Current price = ₹100
Trailing distance = ₹5
```

Stop:

```text
₹95
```

If price increases to ₹110:

```text
Stop → ₹105
```

---

# 32. Percentage Trailing Stop

A trailing stop can also use a percentage.

Example:

```text
Current price = ₹100
Trailing distance = 5%
```

Stop:

```text
₹95
```

If price rises to ₹120:

```text
Stop ≈ ₹114
```

assuming a simple 5% trailing calculation.

---

# 33. Pegged Orders

A **pegged order** has a price that is automatically related to another market price or reference price.

For example, an order may be linked to:

* Best bid
* Best ask
* Midpoint
* Another reference price

The exact behavior depends on the venue.

Pegged orders are more advanced and are commonly encountered in professional electronic trading systems.

---

# 34. Marketable Limit Order

A **marketable limit order** is a limit order whose limit price is aggressive enough to immediately interact with available liquidity.

Example:

Best ask:

```text
₹100
```

Trader sends:

```text
BUY LIMIT ₹105
```

Because the limit is above the current ask, the order may execute immediately at available prices up to ₹105.

This can provide more price protection than an unrestricted market order.

---

# 35. Why Use a Marketable Limit Order?

It can provide a compromise:

```text
Market Order
↓
Execution priority
↓
Less price protection
```

versus:

```text
Limit Order
↓
Price protection
↓
Possible non-execution
```

A marketable limit order can attempt immediate execution while imposing a worst acceptable price.

---

# 36. Hidden Order

A **hidden order** is an order whose full quantity is not displayed publicly in the visible order book, subject to venue rules.

This can be used to reduce information leakage about large trading intentions.

---

# 37. Iceberg Order

An **iceberg order** is a large order where only a portion of the total quantity is displayed at a time.

Example:

```text
Total order = 10,000 shares

Visible quantity = 1,000
```

The market sees approximately:

```text
1,000
```

while the system manages the remaining quantity according to the order's rules.

Once the displayed quantity is executed, another portion may become visible.

---

# 38. Iceberg Order Example

Suppose:

```text
Total = 10,000
Display = 1,000
```

Execution may happen in stages:

```text
1,000
↓
1,000
↓
1,000
↓
...
↓
Final quantity
```

The purpose is generally to reduce the visibility of the full order size.

---

# 39. Reserve Quantity

In an iceberg-style order, the quantity that remains hidden after the displayed quantity is called the **reserve quantity**, depending on the venue's terminology.

Example:

```text
Total = 10,000
Displayed = 1,000
Reserve = 9,000
```

---

# 40. Immediate-or-Cancel Order

An **IOC order** means:

> Execute as much as possible immediately; cancel the remainder.

Example:

```text
Order:
BUY 1,000

Available immediately:
600
```

Result:

```text
Executed = 600
Cancelled = 400
```

---

# 41. Fill-or-Kill Order

An **FOK order** means:

> Execute the entire requested quantity immediately or cancel the entire order.

Example:

```text
Order = 1,000
Available = 600
```

Result:

```text
Executed = 0
Cancelled = 1,000
```

because the complete quantity was not available.

---

# 42. IOC vs FOK

| IOC                          | FOK                                             |
| ---------------------------- | ----------------------------------------------- |
| Partial execution allowed    | Partial execution not allowed                   |
| Remaining quantity cancelled | Entire order cancelled if full fill unavailable |
| "Fill what you can now"      | "Fill everything now or nothing"                |

### Memory Trick

```text
IOC = Some is okay.

FOK = Everything or nothing.
```

---

# 43. Day Order

A **Day order** remains active during the applicable trading session.

If it is not executed by the end of the session, it generally expires or is cancelled according to venue rules.

Example:

```text
9:30 AM
↓
Order placed

During session:
Order remains active

Market close:
Unfilled quantity expires
```

---

# 44. GTC Order

**GTC** means **Good Till Cancelled**.

The order remains active until it is:

* Filled
* Cancelled
* Expired according to applicable rules

Example:

```text
BUY 100
LIMIT ₹90
GTC
```

The order can remain active beyond the current session if the venue supports GTC behavior.

---

# 45. Time-in-Force

**Time-in-force (TIF)** controls how long an order remains active and how execution should occur.

Common TIF instructions include:

```text
DAY
GTC
IOC
FOK
```

Think:

> **Order Type = How should the order behave?**

> **Time-in-Force = How long / under what timing conditions should it remain active?**

---

# 46. Order Type vs Time-in-Force

These are different concepts.

Example:

```text
Order Type:
LIMIT

Time-in-Force:
DAY
```

This means:

> Use limit-price rules and keep the order active for the applicable trading day.

Another example:

```text
Order Type:
LIMIT

Time-in-Force:
IOC
```

This means:

> Use limit-price rules, attempt execution immediately, and cancel the remaining quantity.

---

# 47. Order Status

An order can move through different statuses.

Common statuses include:

```text
NEW
ACCEPTED
OPEN
PARTIALLY_FILLED
FILLED
CANCEL_PENDING
CANCELLED
REJECTED
EXPIRED
```

Exact status names vary by trading system.

---

# 48. Order Lifecycle

A simplified lifecycle:

```text
Order Created
      ↓
Order Submitted
      ↓
Order Validated
      ↓
Order Accepted
      ↓
Order Routed
      ↓
Order Open
      ↓
Execution
      ↓
Filled / Partially Filled
      ↓
Completed
```

Alternative paths:

```text
Rejected
Cancelled
Expired
```

---

# 49. Order Lifecycle Example

Suppose a trader submits:

```text
BUY
1,000 ABC
LIMIT ₹100
```

The system may process:

```text
NEW
 ↓
VALIDATING
 ↓
ACCEPTED
 ↓
OPEN
 ↓
PARTIALLY_FILLED
 ↓
FILLED
```

If only 600 shares execute:

```text
Executed = 600
Remaining = 400
```

The order may remain:

```text
PARTIALLY_FILLED
```

until the rest executes or is cancelled/expired.

---

# 50. Order Cancellation

An order can generally be cancelled if it has not already been fully executed and the venue/rules allow cancellation.

Example:

```text
Order:
BUY 1,000 @ ₹90

Executed:
0

Trader:
CANCEL
```

Order status:

```text
CANCELLED
```

---

# 51. Order Modification

Some trading venues allow an existing order to be modified.

For example:

```text
Original:
BUY 100 @ ₹100
```

Modified:

```text
BUY 100 @ ₹99
```

Depending on venue rules, modifying an order may affect its queue priority.

---

# 52. Queue Position

When multiple orders exist at the same price, an order's position in the queue can matter.

Example:

```text
BUY @ ₹100

Order A → 9:00:01
Order B → 9:00:02
Order C → 9:00:03
```

If price-time priority applies:

```text
A
↓
B
↓
C
```

Order A generally has priority.

---

# 53. Order Priority

Order priority determines which orders are considered first for execution.

Common factors may include:

* Price
* Time
* Order type
* Venue-specific rules

One common model is:

```text
Better Price
     ↓
Earlier Time
```

This is known as price-time priority.

---

# 54. Passive Order

A **passive order** generally rests in the order book and provides liquidity rather than immediately consuming existing liquidity.

Example:

```text
Best bid = ₹99

Trader:
BUY LIMIT ₹99
```

The order may rest on the bid side.

---

# 55. Aggressive Order

An **aggressive order** is an order that immediately interacts with available liquidity.

Example:

```text
Best ask = ₹100

Trader:
BUY LIMIT ₹105
```

The order can immediately execute against available sell orders priced at or below ₹105.

---

# 56. Maker and Taker Relationship

```text
Passive Order
      ↓
Provides Liquidity
      ↓
Maker

Aggressive Order
      ↓
Consumes Liquidity
      ↓
Taker
```

This distinction can affect:

* Fees
* Execution behavior
* Liquidity
* Trading strategy

depending on the venue.

---

# 57. Order Book Example

Consider:

```text
ASKS

₹103 → 500
₹102 → 300
₹101 → 200

-----------------

BIDS

₹99 → 400
₹98 → 700
₹97 → 900
```

Best bid:

```text
₹99
```

Best ask:

```text
₹101
```

Spread:

```text
₹101 - ₹99
= ₹2
```

---

# 58. What Happens With a Market Buy?

Suppose:

```text
BUY 400 MARKET
```

The order consumes:

```text
₹101 → 200
₹102 → 200
```

because only 200 shares were available at ₹101.

Average execution:

```text
(200 × 101 + 200 × 102) / 400

= ₹101.50
```

This demonstrates:

* Market order
* Market depth
* Multiple fills
* Price levels
* Slippage/price movement

---

# 59. What Happens With a Limit Buy?

Suppose:

```text
BUY 400
LIMIT ₹101
```

Only:

```text
200 shares @ ₹101
```

are immediately available.

The order may:

```text
Execute = 200
Remaining = 200
```

The remaining 200 can stay open if the order's time-in-force permits.

It will not automatically execute at ₹102 because the limit is ₹101.

---

# 60. What Happens With an IOC Limit Order?

Same market:

```text
ASK:
₹101 → 200
₹102 → 300
```

Order:

```text
BUY 400
LIMIT ₹101
IOC
```

Execution:

```text
200 @ ₹101
```

Remaining:

```text
200 → cancelled
```

Final:

```text
Filled = 200
Cancelled = 200
```

---

# 61. What Happens With an FOK Limit Order?

Same market:

```text
ASK:
₹101 → 200
₹102 → 300
```

Order:

```text
BUY 400
LIMIT ₹101
FOK
```

Only 200 are available at an acceptable price.

Therefore:

```text
400 cannot be fully executed
```

Result:

```text
Executed = 0
Cancelled = 400
```

---

# 62. Market Order and Liquidity

Market orders consume available liquidity.

Imagine:

```text
ASK

₹100 → 100
₹101 → 100
₹102 → 100
₹103 → 100
```

You submit:

```text
BUY 400 MARKET
```

You may receive:

```text
100 @ ₹100
100 @ ₹101
100 @ ₹102
100 @ ₹103
```

Average:

```text
₹101.50
```

This is why large market orders can have significant execution costs.

---

# 63. Market Order and Slippage

Suppose:

```text
Expected price = ₹100
Actual average = ₹101.50
```

Approximate adverse price difference:

```text
₹1.50
```

For 400 shares:

```text
₹1.50 × 400
= ₹600
```

This is a simplified illustration of execution impact/slippage.

---

# 64. Order Size Matters

The same order type can behave differently depending on order size.

Small order:

```text
BUY 10
```

Large order:

```text
BUY 100,000
```

A liquid market may absorb 10 shares easily.

100,000 shares may consume multiple price levels.

Therefore:

> **Order size + market depth + liquidity strongly influence execution.**

---

# 65. Order Type and Execution Trade-Off

There is no universally "best" order type.

Each order type makes a trade-off.

```text
MARKET
Execution certainty ↑
Price certainty ↓

LIMIT
Price certainty ↑
Execution certainty ↓

STOP
Conditional activation

STOP-LIMIT
Conditional activation
+
Price protection
```

---

# 66. Order Types and Risk

Different order types introduce different risks.

| Order      | Main Risk                                   |
| ---------- | ------------------------------------------- |
| Market     | Slippage                                    |
| Limit      | Non-execution                               |
| Stop       | Execution price uncertainty                 |
| Stop-Limit | Non-execution after trigger                 |
| IOC        | Partial execution                           |
| FOK        | No execution                                |
| GTC        | Order remaining active longer than intended |

---

# 67. Order Types in Automated Trading

Algorithmic trading systems may generate orders automatically.

Example:

```text
Market Data
     ↓
Strategy
     ↓
Signal
     ↓
Order Decision
     ↓
Choose Order Type
     ↓
Risk Check
     ↓
Execution
```

The algorithm may choose:

```text
Market
Limit
Stop
Stop-Limit
```

depending on its strategy.

---

# 68. Smart Order Routing

**Smart Order Routing (SOR)** is a mechanism that attempts to route an order to an appropriate execution venue based on factors such as:

* Price
* Liquidity
* Fees
* Execution probability
* Market conditions

This is more common in markets where multiple execution venues are available.

---

# 69. Execution Algorithms

Large orders may be divided into smaller orders using execution algorithms.

Examples include:

* VWAP
* TWAP
* Participation strategies
* Implementation-shortfall strategies

The objective can include reducing market impact and achieving better execution quality.

---

# 70. VWAP Execution

Suppose a trader wants to buy:

```text
100,000 shares
```

Instead of buying everything immediately, a VWAP-style execution strategy may distribute orders based on expected market volume.

Conceptually:

```text
Market Volume
      ↓
Expected Volume Profile
      ↓
Execution Schedule
      ↓
Multiple Smaller Orders
```

---

# 71. TWAP Execution

A TWAP strategy may distribute an order relatively evenly across a chosen time period.

Example:

```text
Total = 60,000 shares
Time = 60 minutes
```

A simplified schedule might target:

```text
1,000 shares/minute
```

Actual algorithms are generally more sophisticated.

---

# 72. Iceberg vs Execution Algorithm

These are related but different.

### Iceberg

Controls **how much of an order is visible**.

### Execution Algorithm

Controls **how an order is executed over time and/or across market conditions**.

---

# 73. Parent Order

A **parent order** is the original larger order that an execution strategy may divide into smaller orders.

Example:

```text
Parent Order
100,000 shares
       ↓
Child Orders
10,000
8,000
12,000
...
```

---

# 74. Child Order

A **child order** is an individual order generated as part of executing a larger parent order.

Example:

```text
Parent:
BUY 100,000

Child:
BUY 5,000
```

The child order is part of the larger execution plan.

---

# 75. Order Splitting

**Order splitting** means dividing a large order into smaller orders.

Reasons can include:

* Reduce market impact
* Improve execution
* Hide total order size
* Follow volume constraints
* Manage liquidity

---

# 76. Execution Quality

**Execution quality** evaluates how well an order was executed.

Factors can include:

* Execution price
* Slippage
* Spread
* Market impact
* Fill rate
* Execution speed
* Fees

---

# 77. Implementation Shortfall

**Implementation shortfall** is a concept used to measure the cost of executing a trading decision compared with a reference such as the decision price.

It can include effects from:

* Price movement
* Trading costs
* Slippage
* Market impact
* Unfilled quantity

It is widely used in institutional execution analysis.

---

# 78. Order Rejection

An order can be rejected before execution.

Possible reasons:

```text
Insufficient funds
Invalid quantity
Invalid price
Risk limit exceeded
Market closed
Instrument unavailable
Account restriction
Invalid order type
```

Example:

```text
BUY 1,000,000 shares

Maximum allowed:
100,000
```

The risk system may reject the order.

---

# 79. Order Validation

Before routing an order, a trading system may validate:

```text
Instrument
Side
Quantity
Price
Order Type
Account
Market Status
Risk Limits
Buying Power
```

Only valid orders should continue toward execution.

---

# 80. Pre-Trade Risk Check

A **pre-trade risk check** occurs before an order reaches the market.

It may verify:

```text
Is the order allowed?
       ↓
Is quantity allowed?
       ↓
Is price valid?
       ↓
Is exposure within limits?
       ↓
Is sufficient margin available?
```

If the check fails:

```text
ORDER REJECTED
```

---

# 81. Post-Trade Processing

After execution, systems may:

* Record trade
* Update position
* Update P&L
* Update exposure
* Update margin
* Send confirmations
* Perform reconciliation
* Store audit information

---

# 82. Order Confirmation

An **order confirmation** provides information about the status and/or execution of an order.

It may include:

```text
Order ID
Instrument
Side
Quantity
Price
Status
Execution quantity
Timestamp
```

---

# 83. Execution Report

An **execution report** provides information about an execution event.

Example:

```text
Order ID: ORD123
Trade ID: TR456
Instrument: ABC
Side: BUY
Quantity: 500
Price: ₹100
Status: FILLED
```

Exact fields depend on the trading protocol/system.

---

# 84. FIX Protocol and Orders

The **FIX protocol (Financial Information eXchange)** is a widely used messaging standard for electronic trading communications.

Trading systems can use FIX messages to communicate:

* Orders
* Execution reports
* Market information
* Order status
* Trade information

A simplified conceptual flow:

```text
Trading System
      ↓
FIX Message
      ↓
Broker / Venue
      ↓
Execution
      ↓
Execution Report
      ↓
Trading System
```

You do not need to memorize FIX message details yet.

The important concept is:

> **FIX provides a standardized way for financial systems to communicate trading information.**

---

# 85. Common Order Types — Quick Table

| Order Type    | Main Purpose                          |
| ------------- | ------------------------------------- |
| Market        | Execute using available market prices |
| Limit         | Control acceptable execution price    |
| Stop          | Activate after trigger                |
| Stop-Limit    | Trigger + price limit                 |
| Trailing Stop | Stop follows favorable price movement |
| Iceberg       | Hide part of large order              |
| Pegged        | Price follows a reference             |
| IOC           | Execute immediately, cancel remainder |
| FOK           | Execute all immediately or cancel     |
| Day           | Active for trading day                |
| GTC           | Remain active until cancelled/expired |

---

# 86. Important Comparison

## Market

```text
"I want to trade now."
```

## Limit

```text
"I want this price or better."
```

## Stop

```text
"I want this order to activate when the market reaches this level."
```

## Stop-Limit

```text
"I want it to activate at this level, but I also want price protection."
```

## Trailing Stop

```text
"I want my stop to follow the market as it moves in my favor."
```

## IOC

```text
"Execute what you can immediately; cancel the rest."
```

## FOK

```text
"Execute everything immediately or execute nothing."
```

## GTC

```text
"Keep this order active until it is filled, cancelled, or otherwise expires."
```

---

# 87. Most Important Order-Type Decision Tree

When deciding what an order means, ask:

```text
Do I prioritize immediate execution?
        |
       YES
        ↓
   Market Order
```

If not:

```text
Do I want a maximum/minimum price?
        |
       YES
        ↓
   Limit Order
```

If the order should activate only after a price condition:

```text
Do I need a trigger?
        |
       YES
        ↓
   Stop Order
```

If you need both:

```text
Trigger + Price Protection
        ↓
   Stop-Limit
```

If the order should follow a favorable movement:

```text
Dynamic Trigger
        ↓
   Trailing Stop
```

---

# 88. The Three Questions Every Order Answers

Every order essentially answers three major questions:

### 1. What?

```text
Instrument
```

Example:

```text
AAPL
```

### 2. How much?

```text
Quantity
```

Example:

```text
100 shares
```

### 3. How?

```text
Order Type
```

Example:

```text
Limit
```

Additional instructions answer:

```text
At what price?
When should it activate?
How long should it remain active?
What happens to unfilled quantity?
```

---

# 89. Complete Example

Suppose:

```text
Instrument = ABC
Current Price = ₹100
```

Trader wants to buy:

```text
Quantity = 1,000
```

Different order choices produce different behavior.

### Market

```text
BUY 1,000 MARKET
```

Priority:

```text
Execution
```

Price:

```text
Unknown until executed
```

---

### Limit

```text
BUY 1,000 LIMIT ₹98
```

Priority:

```text
Price
```

Possible result:

```text
Filled = 0
```

if price never reaches an acceptable level.

---

### Stop

```text
BUY 1,000 STOP ₹105
```

Behavior:

```text
Wait
 ↓
Price reaches trigger
 ↓
Order activates
```

---

### Stop-Limit

```text
BUY 1,000
STOP ₹105
LIMIT ₹106
```

Behavior:

```text
Price reaches ₹105
        ↓
Order activates
        ↓
Buy only at ₹106 or better
```

---

### IOC

```text
BUY 1,000
LIMIT ₹100
IOC
```

Behavior:

```text
Execute immediately available quantity
        ↓
Cancel remaining quantity
```

---

### FOK

```text
BUY 1,000
LIMIT ₹100
FOK
```

Behavior:

```text
If all 1,000 are available immediately:
        ↓
Execute

Otherwise:
        ↓
Cancel entire order
```

---

# 90. Common Beginner Mistakes

## Mistake 1 — Thinking an Order Is a Trade

Incorrect:

> "I placed an order, so I bought the stock."

Correct:

> An order is only an instruction. It must be executed before it becomes a trade.

---

## Mistake 2 — Thinking Limit Orders Always Execute

Incorrect:

> "I placed a limit order, so it will execute at that price."

Correct:

> A limit order controls price but does not guarantee execution.

---

## Mistake 3 — Thinking Market Orders Always Execute at LTP

Incorrect:

> "The LTP is ₹100, so my market order will execute at ₹100."

Correct:

> A market order interacts with available liquidity, which may produce multiple execution prices.

---

## Mistake 4 — Confusing Stop and Stop-Limit

Remember:

```text
Stop
=
Trigger

Stop-Limit
=
Trigger + Limit
```

---

## Mistake 5 — Thinking Stop-Loss Guarantees the Exact Price

A stop-loss is intended to control risk, but in fast-moving or illiquid markets, the actual execution price can differ from the trigger.

---

## Mistake 6 — Ignoring Time-in-Force

An order's behavior also depends on how long it is allowed to remain active.

```text
LIMIT + DAY
```

is different from:

```text
LIMIT + GTC
```

---

# 91. Order Types from a Risk Perspective

Different order types solve different problems.

### Market Order

Problem solved:

> "I need execution."

Risk:

> "I may receive a worse price."

### Limit Order

Problem solved:

> "I need price protection."

Risk:

> "I may not execute."

### Stop Order

Problem solved:

> "I need a conditional trigger."

Risk:

> "Execution price may vary."

### Stop-Limit

Problem solved:

> "I need a conditional trigger and price protection."

Risk:

> "The order may not execute."

---

# 92. Order Types from a Trading-System Perspective

A production trading platform may need to process:

```text
Order Type
    ↓
Validation
    ↓
Risk Check
    ↓
Routing
    ↓
Matching
    ↓
Execution
    ↓
Status Update
```

For example:

```text
BUY 10,000
LIMIT ₹100
IOC
```

The system must understand:

```text
BUY
↓
10,000 units
↓
LIMIT
↓
Maximum acceptable price = ₹100
↓
IOC
↓
Execute immediately available quantity
↓
Cancel remaining quantity
```

This demonstrates why order types are not just trading terminology.

They are actual **business rules implemented in software**.

---

# 93. Monitoring Order Types in Production

If you monitor a trading system, useful metrics may include:

```text
Orders Submitted
Orders Accepted
Orders Rejected
Orders Cancelled
Orders Expired
Orders Filled
Partial Fills
Fill Rate
Reject Rate
Cancel Rate
Execution Latency
Order Processing Latency
Average Execution Price
Slippage
```

For example:

```text
Order Reject Rate
        ↑
        |
     ALERT
        |
Investigate:
- Risk limits?
- Invalid orders?
- Exchange issue?
- Configuration?
- Market status?
```

---

# 94. Example Production Incident

Imagine an automated trading platform normally has:

```text
Order Reject Rate = 0.2%
```

Suddenly:

```text
Order Reject Rate = 15%
```

This is a major anomaly.

Possible causes:

```text
Risk configuration changed
        OR
Exchange rejected orders
        OR
Invalid instrument data
        OR
Market closed unexpectedly
        OR
Buying-power calculation failure
        OR
Order validation bug
```

Understanding order types allows an engineer to investigate intelligently.

---

# 95. Order-Type Cheat Sheet

```text
MARKET
→ Execute using available market prices.

LIMIT
→ Execute only at specified price or better.

STOP
→ Activate when trigger condition is reached.

STOP-LIMIT
→ Trigger first, then use limit-price rules.

TRAILING STOP
→ Stop follows favorable price movement.

IOC
→ Execute immediately; cancel remainder.

FOK
→ Execute entire order immediately or cancel all.

DAY
→ Active for the applicable trading session.

GTC
→ Remains active until cancelled/expired.

ICEBERG
→ Display only part of total order quantity.

PEGGED
→ Price is linked to a reference price.
```

---

# 96. Master Comparison Table

| Order Type    |        Trigger? |              Price Control? | Immediate Execution? | Partial Fill? | Main Purpose                |
| ------------- | --------------: | --------------------------: | -------------------: | ------------: | --------------------------- |
| Market        |              No |                         Low |             Intended |      Possible | Fast execution              |
| Limit         |              No |                         Yes |             Possible |      Possible | Price control               |
| Stop          |             Yes |  Depends on resulting order |        After trigger |      Possible | Conditional execution       |
| Stop-Limit    |             Yes |                         Yes |        After trigger |      Possible | Conditional + price control |
| Trailing Stop |         Dynamic |   Depends on implementation |        After trigger |      Possible | Follow favorable movement   |
| IOC           |              No |        If paired with limit |                  Yes |           Yes | Immediate partial execution |
| FOK           |              No |        If paired with limit |                  Yes |            No | Full immediate execution    |
| Iceberg       |              No | Depends on underlying order |              Depends |       Depends | Reduce visible size         |
| Pegged        | Reference-based |                     Dynamic |              Depends |       Depends | Follow reference price      |

---

# 97. Final Mental Model

Remember order types using this simple structure:

```text
                    ORDER
                      |
        +-------------+-------------+
        |             |             |
     EXECUTE       CONTROL       TRIGGER
        |             |             |
     MARKET         LIMIT          STOP
                                      |
                               +------+------+
                               |             |
                           STOP-LIMIT   TRAILING STOP
```

And time-in-force sits alongside the order type:

```text
ORDER TYPE
    +
TIME-IN-FORCE
    ↓
COMPLETE ORDER INSTRUCTION
```

Example:

```text
BUY
100 shares
LIMIT ₹100
IOC
```

means:

```text
BUY
↓
100 shares
↓
Do not pay above ₹100
↓
Attempt immediately
↓
Cancel whatever cannot be executed immediately
```

---

# 98. Key Takeaways

1. An **order** is an instruction; a **trade** is an execution.
2. A **market order** prioritizes execution.
3. A **limit order** prioritizes price control.
4. A **stop order** activates after a trigger condition.
5. A **stop-limit order** combines a trigger with a price limit.
6. A **trailing stop** follows the market according to a defined trailing rule.
7. A **market order** can execute at multiple prices.
8. A **limit order** may remain unfilled.
9. **IOC** allows partial execution but cancels the remainder.
10. **FOK** requires complete immediate execution or cancels the order.
11. **Day** orders generally expire at the end of the applicable trading session.
12. **GTC** orders can remain active until cancelled or otherwise expire, subject to venue rules.
13. An **iceberg order** hides part of a larger order's quantity.
14. A **pegged order** derives its price from a reference.
15. **Stop-loss** is a risk-management concept, not necessarily a separate underlying order type.
16. **Take-profit** is an exit objective that can be implemented using different order instructions.
17. **Order type and time-in-force are different concepts.**
18. Order size, liquidity, market depth, and volatility strongly influence execution.
19. Market orders can experience **slippage**.
20. Limit orders can suffer from **non-execution**.
21. Stop orders can experience execution-price uncertainty.
22. Stop-limit orders can fail to execute after being triggered.
23. Order lifecycle commonly involves creation, validation, acceptance, routing, execution, and completion.
24. Trading systems use order IDs and execution reports to track order activity.
25. Pre-trade risk checks can reject orders before they reach the market.
26. Large orders may be split into smaller child orders.
27. Execution algorithms can use approaches such as VWAP and TWAP.
28. Order types are not merely theoretical—they are implemented as actual business rules in trading software.
29. Understanding order types is essential for understanding **trading, risk management, execution, and monitoring**.
30. The fundamental trade-off is:

```text
EXECUTION CERTAINTY
        vs
PRICE CERTAINTY
```

---

# 99. One-Line Memory Formula

```text
MARKET
= "Execute."

LIMIT
= "Price matters."

STOP
= "Wait for trigger."

STOP-LIMIT
= "Trigger + price protection."

TRAILING STOP
= "Follow the market."

IOC
= "Execute what you can now."

FOK
= "Everything now or nothing."

DAY
= "For this session."

GTC
= "Keep it active."

ICEBERG
= "Hide the full size."
```

Once these concepts are clear, the next step is to understand **how orders move through a real trading architecture**, from trader → OMS → risk engine → broker/exchange → matching engine → execution report → position/P&L system.
