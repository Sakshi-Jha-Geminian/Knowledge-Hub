# Trading Terminology

## Introduction

Financial markets have their own language.

When traders, brokers, portfolio managers, market analysts, or trading systems communicate, they frequently use short terms such as:

* Bid
* Ask
* Spread
* Volume
* Liquidity
* Volatility
* Long
* Short
* Position
* Order
* Fill
* Slippage
* Leverage
* Margin
* Stop-loss
* Take-profit
* Market order
* Limit order
* Market depth
* Open interest

If you do not understand these terms, trading-related conversations can sound extremely confusing.

This chapter builds a practical vocabulary for understanding trading.

The goal is not simply to memorize definitions.

You should understand:

> **What the term means → why it exists → how it works → where it appears in a trading system → and why it matters.**

---

# 1. What Is Trading Terminology?

**Trading terminology** is the collection of words and expressions used to describe:

* Financial instruments
* Market prices
* Orders
* Positions
* Buying and selling
* Market activity
* Risk
* Execution
* Profit and loss
* Market conditions
* Trading strategies
* Portfolio activity

For example:

If someone says:

> "The stock has a tight spread, high liquidity, and increasing volatility."

A beginner may not understand this.

A trader understands:

* **Tight spread** → buying and selling prices are close
* **High liquidity** → many buyers and sellers are available
* **Increasing volatility** → price is moving more aggressively

Therefore, learning trading terminology is essential before studying advanced trading systems.

---

# 2. The Most Important Trading Concepts

Before going through individual terms, remember these major categories.

| Category          | Examples                                |
| ----------------- | --------------------------------------- |
| Price             | Bid, Ask, Last Price                    |
| Market Activity   | Volume, Turnover                        |
| Market Quality    | Liquidity, Spread, Depth                |
| Price Movement    | Volatility, Trend                       |
| Trading Direction | Long, Short                             |
| Orders            | Market, Limit, Stop                     |
| Execution         | Fill, Partial Fill, Slippage            |
| Risk              | Stop-loss, Drawdown, Exposure           |
| Financing         | Margin, Leverage                        |
| Derivatives       | Contract, Strike, Expiry, Open Interest |
| Profit/Loss       | Realized P&L, Unrealized P&L            |
| Portfolio         | Position, Holdings, Exposure            |
| Market Structure  | Order Book, Market Depth                |
| Timing            | Open, Close, Settlement                 |

---

# 3. Asset

An **asset** is something that has economic value.

In financial markets, examples include:

* Stocks
* Bonds
* Currencies
* Commodities
* ETFs
* Futures
* Options
* Cryptocurrencies

Example:

If you own shares of a company, those shares are a financial asset.

---

# 4. Security

A **security** is a tradable financial instrument that represents some form of financial value or claim.

Examples include:

* Stocks
* Bonds
* Certain derivatives
* Other marketable financial instruments

A stock represents ownership in a company.

A bond represents a debt relationship between the issuer and investor.

---

# 5. Instrument

The word **instrument** is commonly used as a general term for something that can be traded.

Examples:

* Equity instrument
* Debt instrument
* Derivative instrument
* Currency instrument

In a trading platform, you may see:

```text
Instrument: AAPL
Instrument: EUR/USD
Instrument: NIFTY
Instrument: Gold Futures
```

The exact meaning depends on the market.

---

# 6. Ticker Symbol

A **ticker symbol** is a short identifier used to represent a financial instrument.

Examples:

```text
AAPL
MSFT
TSLA
AMZN
```

Instead of repeatedly writing a company's complete name, market systems use its ticker.

For example:

```text
Apple Inc. → AAPL
Microsoft → MSFT
Tesla → TSLA
```

A ticker is especially important in trading systems because orders, prices, and market data are often associated with ticker symbols.

---

# 7. Price

The **price** is the monetary value at which an asset is quoted or traded.

For example:

```text
Stock price = ₹500
```

This means the current quoted/traded value is around ₹500 per share.

However, there is an important distinction between:

* Bid price
* Ask price
* Last traded price

These are not necessarily the same.

---

# 8. Bid Price

The **bid price** is the highest price a buyer is currently willing to pay.

Example:

```text
Bid = ₹99
```

This means a buyer is currently willing to purchase the asset for ₹99.

### Memory Trick

> **Bid = Buyer**

Both start with **B**.

---

# 9. Ask Price

The **ask price** is the lowest price at which a seller is currently willing to sell.

Example:

```text
Ask = ₹101
```

A seller is willing to sell for ₹101.

### Memory Trick

> **Ask = seller's asking price**

---

# 10. Bid-Ask Spread

The **bid-ask spread** is the difference between the ask price and bid price.

Formula:

```text
Spread = Ask Price - Bid Price
```

Example:

```text
Bid = ₹99
Ask = ₹101

Spread = ₹101 - ₹99
       = ₹2
```

The spread represents a basic part of the cost of trading immediately.

---

# 11. Tight Spread

A **tight spread** means the bid and ask prices are very close.

Example:

```text
Bid = ₹100.00
Ask = ₹100.05
Spread = ₹0.05
```

This generally indicates a competitive market with buyers and sellers close together.

Highly liquid instruments often have relatively tight spreads.

---

# 12. Wide Spread

A **wide spread** means the difference between bid and ask is large.

Example:

```text
Bid = ₹95
Ask = ₹105

Spread = ₹10
```

Wide spreads can occur when:

* Liquidity is low
* Market uncertainty is high
* Trading activity is low
* The instrument is difficult to trade
* The market is moving rapidly

---

# 13. Last Traded Price

The **Last Traded Price (LTP)** is the price at which the most recent transaction occurred.

Example:

```text
Bid = ₹99
Ask = ₹101
LTP = ₹100
```

The LTP tells us the price of the most recent completed trade.

It is different from the current bid and ask.

---

# 14. Quote

A **quote** represents the current market pricing information for an instrument.

A basic quote may contain:

```text
Bid: ₹99
Ask: ₹101
Last: ₹100
```

A more complete quote may include:

* Bid price
* Bid quantity
* Ask price
* Ask quantity
* Last traded price
* Trading volume
* High
* Low
* Open
* Previous close

---

# 15. Market Data

**Market data** is information describing what is happening in a financial market.

It can include:

* Prices
* Bid/ask
* Trades
* Volume
* Order book
* Market depth
* Open interest
* High/low
* Trading status

Trading systems consume market data continuously.

For example:

```text
10:00:01 → AAPL = $200.10
10:00:02 → AAPL = $200.15
10:00:03 → AAPL = $200.12
```

A trading system may receive thousands or millions of such updates.

---

# 16. Market Data Feed

A **market data feed** is a stream of market information delivered from a market data provider or exchange to consumers.

Consumers may include:

* Brokers
* Trading platforms
* Banks
* Hedge funds
* Exchanges
* Market-making systems
* Risk systems
* Monitoring systems

A market data feed may contain:

```text
Instrument
Timestamp
Bid
Ask
Last Price
Volume
Trade information
```

---

# 17. Level 1 Market Data

**Level 1 data** generally provides basic market information.

Typical information includes:

* Best bid
* Best ask
* Last traded price
* Volume

Example:

```text
AAPL

Bid: 199.95
Ask: 200.05
Last: 200.00
Volume: 1,250,000
```

---

# 18. Level 2 Market Data

**Level 2 market data** provides more detailed information about orders at multiple price levels.

Example:

```text
BUY ORDERS

Price     Quantity
100.00    500
99.95     800
99.90     1200


SELL ORDERS

Price     Quantity
100.05    600
100.10    900
100.15    1500
```

This provides a deeper view of market demand and supply.

---

# 19. Order Book

An **order book** is a record of outstanding buy and sell orders.

It is generally divided into:

```text
BID SIDE       | ASK SIDE
---------------|--------------
Buy orders     | Sell orders
```

Example:

```text
Bids             Asks

₹99.00  500      ₹101.00  400
₹98.50  800      ₹101.50  700
₹98.00  1000     ₹102.00  900
```

The order book helps show:

* Available liquidity
* Buying interest
* Selling interest
* Market depth

---

# 20. Market Depth

**Market depth** refers to how many buy and sell orders exist at different price levels.

A deep market has many orders available around the current price.

A shallow market has relatively fewer orders.

### Why It Matters

Suppose you want to sell:

```text
10 shares
```

If there are thousands of buyers available, your order may execute easily.

But if there are very few buyers, your order may:

* Take longer
* Execute at multiple prices
* Cause price movement

---

# 21. Liquidity

**Liquidity** describes how easily an asset can be bought or sold without causing a significant change in its price.

A highly liquid asset usually has:

* Many buyers
* Many sellers
* High trading activity
* Tight spreads
* Significant market depth

A low-liquidity asset may have:

* Few participants
* Wide spreads
* Limited orders
* Greater price impact

---

# 22. High Liquidity Example

Suppose a stock has:

```text
Bid = ₹100.00
Ask = ₹100.01
```

There are thousands of shares available.

A trader can generally enter or exit relatively easily.

This is an example of a liquid market.

---

# 23. Low Liquidity Example

Suppose:

```text
Bid = ₹90
Ask = ₹110
```

There are very few orders.

The trader may have difficulty executing a large order without affecting the price.

This represents a less liquid market.

---

# 24. Volume

**Volume** represents the amount of an instrument traded during a particular period.

Example:

```text
Stock traded today = 5,000,000 shares
```

Volume is often used to understand market activity.

High volume generally means more trading activity.

---

# 25. Trading Volume vs Order Quantity

These are different concepts.

### Order Quantity

How much you want to trade.

```text
Buy 100 shares
```

Quantity = 100.

### Trading Volume

How much has actually been traded over a period.

```text
Today's volume = 5 million shares
```

Volume = 5 million shares.

---

# 26. Turnover

**Turnover** represents the monetary value of trading activity.

Example:

```text
10,000 shares × ₹100
= ₹1,000,000
```

The trading value is ₹1 million.

Volume measures units traded.

Turnover measures monetary value traded.

---

# 27. Volatility

**Volatility** measures how much and how quickly an asset's price changes.

High volatility:

```text
₹100
₹108
₹97
₹115
₹105
```

Low volatility:

```text
₹100
₹100.50
₹100.20
₹100.70
₹100.40
```

High volatility means larger price movements.

Low volatility means smaller price movements.

---

# 28. Historical Volatility

**Historical volatility** measures how much an asset's price has moved historically.

It is calculated using historical price data.

It helps answer:

> "How much has this asset typically moved in the past?"

---

# 29. Implied Volatility

**Implied volatility (IV)** is a volatility estimate derived from market prices of options.

It reflects the market's expectations about future price movement.

Important:

> Historical volatility looks backward.

> Implied volatility is derived from option prices and reflects expectations about future movement.

---

# 30. Trend

A **trend** is the general direction in which a market or asset is moving.

Three common descriptions are:

### Uptrend

Prices generally move upward.

```text
100 → 105 → 110 → 115
```

### Downtrend

Prices generally move downward.

```text
115 → 110 → 105 → 100
```

### Sideways

Price moves within a relatively narrow range.

```text
100 → 103 → 99 → 102 → 101
```

---

# 31. Bull Market

A **bull market** is a market characterized by generally rising prices and positive sentiment.

Memory trick:

> Bull attacks upward with its horns.

---

# 32. Bear Market

A **bear market** is a market characterized by significant or sustained price declines.

Memory trick:

> Bear attacks downward with its paws.

---

# 33. Bullish

**Bullish** means expecting prices to rise.

Example:

> "The trader is bullish on the stock."

Meaning:

> The trader expects the stock price to increase.

---

# 34. Bearish

**Bearish** means expecting prices to fall.

Example:

> "The trader is bearish on the stock."

Meaning:

> The trader expects the stock price to decrease.

---

# 35. Long Position

A **long position** means you own or have exposure to an asset with the expectation that its value will increase.

Simple example:

```text
Buy at ₹100
Sell at ₹120

Profit = ₹20
```

The trader is **long**.

### Memory Trick

> Long = expecting price to go up.

---

# 36. Short Position

A **short position** means taking a position that benefits when the price falls.

Conceptually:

```text
Sell at ₹100
Buy back at ₹80

Profit = ₹20
```

The exact mechanics depend on the market and instrument.

### Memory Trick

> Short = expecting price to go down.

---

# 37. Going Long

**Going long** means entering a position with an expectation of price appreciation.

Example:

```text
Current price = ₹500

Trader buys 100 shares.

Long position = 100 shares
```

If price increases to ₹550:

```text
Profit = ₹50 × 100
       = ₹5,000
```

Ignoring fees and taxes.

---

# 38. Going Short

**Going short** means entering a position designed to benefit from a decline in price.

Example:

```text
Short at ₹500
Cover at ₹450
```

Profit:

```text
₹500 - ₹450 = ₹50
```

For 100 units:

```text
₹50 × 100 = ₹5,000
```

Ignoring fees and other costs.

---

# 39. Position

A **position** represents a trader's current exposure to an instrument.

Example:

```text
AAPL
Quantity = +100
```

This could represent a long position.

A negative quantity in some systems may represent a short position:

```text
AAPL
Quantity = -100
```

Position information can include:

* Instrument
* Quantity
* Entry price
* Current price
* Side
* Unrealized P&L
* Realized P&L

---

# 40. Position Size

**Position size** is the amount of an asset involved in a trade.

Example:

```text
Buy 500 shares
```

Position size = 500 shares.

Position sizing is important for risk management.

---

# 41. Entry Price

The **entry price** is the price at which a position is opened.

Example:

```text
Buy 100 shares at ₹500
```

Entry price = ₹500.

---

# 42. Exit Price

The **exit price** is the price at which a position is closed.

Example:

```text
Bought at ₹500
Sold at ₹550
```

Exit price = ₹550.

---

# 43. Holding

A **holding** is an asset currently owned by an investor or portfolio.

Example:

```text
AAPL → 100 shares
MSFT → 50 shares
```

These are holdings.

---

# 44. Exposure

**Exposure** represents how much financial risk or market sensitivity a trader or portfolio has to an asset, market, sector, currency, or other factor.

Example:

```text
Portfolio value = ₹10 lakh
Technology exposure = ₹4 lakh
```

The portfolio has ₹4 lakh of technology exposure.

Exposure can be:

* Long exposure
* Short exposure
* Gross exposure
* Net exposure

---

# 45. Gross Exposure

**Gross exposure** measures total long and short exposure without offsetting them.

Example:

```text
Long exposure = ₹10 lakh
Short exposure = ₹6 lakh

Gross exposure = ₹16 lakh
```

---

# 46. Net Exposure

**Net exposure** offsets long and short exposure.

Example:

```text
Long = ₹10 lakh
Short = ₹6 lakh

Net exposure = ₹4 lakh
```

So:

```text
Gross exposure = 16 lakh
Net exposure = 4 lakh
```

---

# 47. Notional Value

**Notional value** represents the total value controlled by a position or contract before considering factors such as margin.

Example:

```text
1,000 shares × ₹500
= ₹500,000
```

Notional value = ₹500,000.

Notional value is especially important in derivatives.

---

# 48. Order

An **order** is an instruction to buy or sell a financial instrument.

Example:

```text
BUY
AAPL
100 shares
```

An order usually contains information such as:

* Instrument
* Side
* Quantity
* Order type
* Price
* Time-in-force
* Order ID

---

# 49. Buy Order

A **buy order** instructs the trading system to purchase an asset.

Example:

```text
BUY 100 AAPL
```

---

# 50. Sell Order

A **sell order** instructs the trading system to sell an asset.

Example:

```text
SELL 100 AAPL
```

---

# 51. Order Side

The **side** of an order indicates whether it is:

```text
BUY
```

or

```text
SELL
```

This is one of the most fundamental fields in an order.

---

# 52. Order Type

**Order type** determines how the order should be executed.

Common order types include:

* Market order
* Limit order
* Stop order
* Stop-limit order

---

# 53. Market Order

A **market order** instructs the system to execute the order at the best available price in the market.

Example:

```text
Buy 100 shares
Order type = Market
```

The trader prioritizes execution rather than a specific price.

### Advantage

Higher likelihood of immediate execution.

### Risk

The final execution price may differ from the expected price, especially in volatile or illiquid markets.

This is where **slippage** becomes important.

---

# 54. Limit Order

A **limit order** specifies the maximum or minimum acceptable price.

For a buy:

```text
Buy 100 shares
Limit price = ₹100
```

The trader does not want to pay more than ₹100.

For a sell:

```text
Sell 100 shares
Limit price = ₹110
```

The trader does not want to sell below ₹110.

### Important

A limit order provides price control but does not guarantee execution.

---

# 55. Stop Order

A **stop order** becomes active when a specified trigger price is reached.

Example:

```text
Current price = ₹100
Stop price = ₹90
```

If price reaches the trigger, the stop order can activate according to the market's rules.

Stop orders are commonly used for:

* Risk management
* Exiting positions
* Entering trades after price confirmation

---

# 56. Stop-Loss Order

A **stop-loss** is an order or strategy designed to limit potential losses.

Example:

```text
Bought at ₹100

Stop-loss = ₹90
```

If the market falls significantly, the stop-loss is intended to exit the position around the defined trigger.

The exact execution price is not necessarily guaranteed.

---

# 57. Take-Profit

A **take-profit** order or strategy is designed to close a position when a desired profit level is reached.

Example:

```text
Buy = ₹100
Take-profit = ₹120
```

If the price reaches the target, the position may be closed.

---

# 58. Stop-Loss vs Take-Profit

| Concept     | Purpose                |
| ----------- | ---------------------- |
| Stop-loss   | Limit potential loss   |
| Take-profit | Lock in desired profit |

Example:

```text
Entry = ₹100
Stop-loss = ₹90
Take-profit = ₹120
```

The trader has defined both downside and upside levels.

---

# 59. Trigger Price

A **trigger price** is the price level that activates an order or trading condition.

Example:

```text
Trigger = ₹90
```

When the market reaches the trigger, the order may become active.

---

# 60. Execution

**Execution** is the process of actually completing a buy or sell transaction.

Example:

```text
Order:
BUY 100 shares

Execution:
100 shares bought at ₹100.05
```

The order has been executed.

---

# 61. Fill

A **fill** occurs when an order, or part of an order, is executed.

Example:

```text
Order = Buy 1,000 shares

Executed = 1,000 shares
```

The order is fully filled.

---

# 62. Partial Fill

A **partial fill** occurs when only part of an order is executed.

Example:

```text
Order:
BUY 1,000 shares

Executed:
400 shares

Remaining:
600 shares
```

The order is partially filled.

This is common when insufficient liquidity exists at the desired price.

---

# 63. Filled Order

A **filled order** is an order for which the requested quantity has been completely executed.

Example:

```text
Requested = 1,000
Executed = 1,000
Remaining = 0
```

Status:

```text
FILLED
```

---

# 64. Open Order

An **open order** is an order that has not yet been completely executed or cancelled.

Example:

```text
Requested = 1,000
Executed = 400
Remaining = 600
```

The remaining order is still open.

---

# 65. Cancelled Order

A **cancelled order** is an order that has been withdrawn before completion.

Example:

```text
Order = Buy 1,000

Executed = 300
Cancelled remaining = 700
```

The final executed quantity is 300.

---

# 66. Rejected Order

A **rejected order** is an order that the trading system or market does not accept for execution.

Possible reasons include:

* Invalid quantity
* Invalid price
* Insufficient funds
* Risk limit violation
* Market closed
* Invalid instrument
* Account restriction
* Incorrect order parameters

---

# 67. Order ID

An **Order ID** is a unique identifier assigned to an order.

Example:

```text
Order ID: ORD-982734
```

Trading systems use order IDs to:

* Track orders
* Modify orders
* Cancel orders
* Audit transactions
* Investigate failures

---

# 68. Trade ID

A **Trade ID** identifies an executed trade.

One order can sometimes result in multiple executions.

Therefore:

```text
Order ID
    ↓
Execution 1 → Trade ID
Execution 2 → Trade ID
Execution 3 → Trade ID
```

This distinction is important in trading systems.

---

# 69. Execution Price

The **execution price** is the actual price at which an order was executed.

Example:

```text
Expected = ₹100
Actual execution = ₹100.20
```

Execution price = ₹100.20.

---

# 70. Slippage

**Slippage** is the difference between the expected price and the actual execution price.

Example:

```text
Expected buy price = ₹100
Actual execution = ₹101

Slippage = ₹1
```

Slippage can occur because of:

* Rapid price movement
* Low liquidity
* Large orders
* Wide spreads
* Market orders
* Network/system delays

---

# 71. Positive and Negative Slippage

Slippage is not always unfavorable.

Example for a buy:

```text
Expected = ₹100
Executed = ₹99.50
```

The trader got a better price.

For a buy:

```text
Expected = ₹100
Executed = ₹101
```

The trader got a worse price.

---

# 72. Market Impact

**Market impact** is the effect an order has on the market price.

Suppose the market has limited liquidity.

A trader wants to buy:

```text
1,000,000 shares
```

The large order may consume available sell orders and push the price upward.

That price movement caused by the order is market impact.

---

# 73. Transaction Cost

Trading involves costs.

Examples include:

* Commission
* Brokerage
* Bid-ask spread
* Slippage
* Exchange fees
* Taxes
* Market impact

Therefore:

```text
Actual Trading Cost
≠
Only Brokerage
```

A professional trading system considers multiple sources of transaction cost.

---

# 74. Commission

A **commission** is a fee charged for executing or facilitating a transaction.

Example:

```text
Trade value = ₹100,000
Commission = ₹100
```

The exact fee structure depends on the broker, exchange, product, and jurisdiction.

---

# 75. Brokerage

**Brokerage** is the fee charged by a broker for providing trading services.

It may be:

* Fixed
* Percentage-based
* Tiered
* Product-specific

---

# 76. Leverage

**Leverage** allows a trader to control a larger position using a smaller amount of capital.

Example:

```text
Capital = ₹10,000
Leverage = 5×

Position controlled = ₹50,000
```

Leverage increases both potential gains and potential losses.

### Important

> Leverage does not remove risk. It magnifies exposure relative to the capital committed.

---

# 77. Margin

**Margin** is the amount of money or collateral required to support a leveraged position or certain types of trades.

Example:

```text
Position value = ₹100,000
Required margin = ₹20,000
```

The trader provides ₹20,000 as margin while controlling a ₹100,000 position, subject to the product and market rules.

---

# 78. Initial Margin

**Initial margin** is the amount of collateral required when opening certain leveraged positions, especially derivatives.

---

# 79. Maintenance Margin

**Maintenance margin** is the minimum amount of equity/collateral that must generally be maintained to keep a leveraged position open.

If account equity falls below the required level, the trader may face a **margin call** or forced liquidation depending on the system and rules.

---

# 80. Margin Call

A **margin call** occurs when the account no longer has enough required margin to support its positions.

Example:

```text
Required maintenance margin = ₹20,000
Account equity = ₹15,000
```

The account may need additional funds or the position may be reduced/closed.

---

# 81. Liquidation

**Liquidation** means closing a position.

In risk-management contexts, liquidation may happen automatically when:

* Margin requirements are breached
* Risk limits are exceeded
* The account cannot support the position

---

# 82. Buying Power

**Buying power** represents how much a trader can potentially use to enter new positions based on available capital, margin rules, leverage, and account restrictions.

Example:

```text
Cash = ₹50,000
Available buying power = ₹100,000
```

The difference may result from permitted leverage or margin rules.

---

# 83. Cash Balance

**Cash balance** represents available cash in a trading account.

It is different from total portfolio value.

Example:

```text
Cash = ₹20,000
Stocks = ₹80,000

Portfolio value = ₹100,000
```

---

# 84. Equity

In a trading account, **equity** generally refers to the current value of the account after accounting for positions and applicable gains/losses.

A simplified representation is:

```text
Account Equity
=
Cash
+
Market Value of Positions
```

Exact definitions can vary by broker and product.

---

# 85. Realized P&L

**Realized Profit and Loss (P&L)** is profit or loss resulting from a position that has been closed.

Example:

```text
Buy = ₹100
Sell = ₹120

Realized profit = ₹20
```

For 100 shares:

```text
₹20 × 100 = ₹2,000
```

Ignoring costs.

---

# 86. Unrealized P&L

**Unrealized P&L** is the current gain or loss on an open position.

Example:

```text
Bought at ₹100
Current price = ₹120
```

Unrealized profit:

```text
₹20 per share
```

The profit becomes realized when the position is closed.

---

# 87. P&L

**P&L** stands for **Profit and Loss**.

It measures financial performance.

A simple formula for a long position is:

```text
P&L = (Exit Price - Entry Price) × Quantity
```

Example:

```text
Entry = ₹100
Exit = ₹120
Quantity = 100

P&L = (120 - 100) × 100
    = ₹2,000
```

Ignoring fees and taxes.

---

# 88. Drawdown

**Drawdown** measures the decline from a previous peak in portfolio or strategy value.

Example:

```text
Peak = ₹1,00,000
Portfolio falls to = ₹80,000

Drawdown = ₹20,000
```

Percentage drawdown:

```text
₹20,000 / ₹1,00,000 × 100
= 20%
```

---

# 89. Maximum Drawdown

**Maximum drawdown (MDD)** is the largest peak-to-trough decline over a specified period.

It is an important risk metric.

Example:

```text
Portfolio Peak = ₹10 lakh
Lowest point after peak = ₹7 lakh

Maximum Drawdown = ₹3 lakh
Percentage = 30%
```

---

# 90. Risk

**Risk** is the possibility that an investment or trading activity produces an unfavorable outcome.

Trading risks include:

* Market risk
* Liquidity risk
* Credit risk
* Operational risk
* Technology risk
* Model risk
* Counterparty risk
* Execution risk
* Regulatory risk

---

# 91. Risk Limit

A **risk limit** defines the maximum acceptable amount of exposure or loss.

Examples:

```text
Maximum position = ₹10 lakh
Maximum daily loss = ₹50,000
Maximum order quantity = 10,000
```

Trading systems may automatically reject orders that violate risk limits.

---

# 92. Position Limit

A **position limit** defines how large a position a trader or account is allowed to maintain.

Example:

```text
Maximum position = 10,000 shares
```

If the trader already owns 9,500 shares, an order for another 1,000 shares may violate the limit.

---

# 93. Exposure Limit

An **exposure limit** restricts how much financial exposure can be taken to a specific:

* Instrument
* Sector
* Currency
* Market
* Counterparty
* Asset class

---

# 94. Order Limit

An **order limit** restricts characteristics of an individual order.

For example:

```text
Maximum order quantity = 5,000
```

An order for 10,000 units may be rejected.

---

# 95. Time-in-Force

**Time-in-force (TIF)** determines how long an order remains active.

Common examples include:

* Day
* GTC
* IOC
* FOK

---

# 96. Day Order

A **Day order** remains active during the trading session and is generally cancelled if not executed by the end of the session.

---

# 97. GTC — Good Till Cancelled

**GTC** means **Good Till Cancelled**.

The order remains active until:

* It is executed
* It is cancelled
* It expires according to applicable rules

The exact behavior can depend on the broker or venue.

---

# 98. IOC — Immediate or Cancel

**IOC** means **Immediate or Cancel**.

The order attempts to execute immediately.

Any quantity that cannot be executed immediately is cancelled.

Example:

```text
Order = 1,000 shares

Immediately executed = 600
Remaining = 400

400 → cancelled
```

---

# 99. FOK — Fill or Kill

**FOK** means **Fill or Kill**.

The entire order must be executed immediately.

If the complete quantity cannot be filled:

```text
Entire order → cancelled
```

---

# 100. Market Open

The **market open** is the beginning of a trading session.

The exact opening time depends on the exchange and market.

---

# 101. Market Close

The **market close** is the end of the regular trading session.

After the regular close, some markets may have:

* After-hours trading
* Extended-hours trading
* Post-market processing

---

# 102. Trading Session

A **trading session** is a defined period during which a market is open for trading.

A session may contain:

* Opening
* Continuous trading
* Closing
* Auction periods

depending on the exchange.

---

# 103. Pre-Market

**Pre-market** refers to trading activity occurring before the regular market session.

Availability depends on the market and exchange.

---

# 104. After-Hours Trading

**After-hours trading** refers to trading outside the regular market session.

It may have:

* Lower liquidity
* Wider spreads
* Different order rules
* Greater volatility

compared with regular trading.

---

# 105. Settlement

**Settlement** is the process through which a completed trade is finalized.

This may involve:

* Transfer of securities
* Transfer of cash
* Confirmation
* Clearing
* Final ownership records

Settlement timing depends on the market and instrument.

---

# 106. Clearing

**Clearing** is the process of determining obligations between trading parties after transactions occur.

A clearing process may determine:

* Who owes money
* Who delivers securities
* Net obligations
* Collateral requirements

---

# 107. Counterparty

A **counterparty** is the other party involved in a financial transaction.

For example:

```text
Buyer ↔ Seller
```

Each party is the counterparty to the other.

---

# 108. Counterparty Risk

**Counterparty risk** is the possibility that the other party fails to fulfill its obligations.

For example:

A party may fail to:

* Deliver securities
* Pay money
* Meet contractual obligations

---

# 109. Broker

A **broker** facilitates trading between market participants and the market venue.

A broker may provide:

* Order routing
* Account services
* Trading interfaces
* Market access
* Reporting
* Risk controls

---

# 110. Exchange

An **exchange** is a regulated marketplace where financial instruments can be traded according to defined rules.

Examples include stock exchanges and derivatives exchanges.

The exchange may provide:

* Trading infrastructure
* Order matching
* Market data
* Trading rules
* Market surveillance

---

# 111. Trading Venue

A **trading venue** is a place or system where orders can interact and trades can occur.

Depending on the market, venues may include:

* Exchanges
* Electronic marketplaces
* Alternative trading systems
* Other regulated execution venues

---

# 112. Order Matching

**Order matching** is the process of matching compatible buy and sell orders.

Example:

```text
Buyer:
Buy 100 @ ₹100

Seller:
Sell 100 @ ₹100
```

The matching engine can match these orders.

---

# 113. Matching Engine

A **matching engine** is the system responsible for matching buy and sell orders according to the venue's rules.

A simplified process:

```text
Order arrives
      ↓
Validate order
      ↓
Check order book
      ↓
Find compatible order
      ↓
Execute trade
      ↓
Publish trade information
```

Matching engines are critical components of electronic markets.

---

# 114. Price-Time Priority

Many markets use a form of **price-time priority**.

This generally means:

1. Better price gets priority.
2. At the same price, earlier order gets priority.

Example:

```text
Buy Orders

₹100 → 9:00 AM
₹100 → 9:05 AM
₹99  → 9:01 AM
```

The ₹100 orders generally have priority over ₹99.

Among the two ₹100 orders, the earlier one generally has priority.

Exact matching rules depend on the venue.

---

# 115. Liquidity Provider

A **liquidity provider** supplies buy and/or sell orders to the market.

Liquidity providers help other participants execute trades.

---

# 116. Market Maker

A **market maker** continuously provides buy and sell quotes in a market, subject to the applicable rules and strategy.

Example:

```text
Bid = ₹99.90
Ask = ₹100.10
```

The market maker may earn from the spread while managing inventory and other risks.

---

# 117. Market Taker

A **market taker** is a participant who consumes existing liquidity from the order book.

For example:

If the best ask is:

```text
₹100
```

and a trader sends a marketable buy order, the trader may take liquidity from the sell side.

---

# 118. Maker vs Taker

| Maker                      | Taker                               |
| -------------------------- | ----------------------------------- |
| Adds liquidity             | Consumes liquidity                  |
| Places an order that rests | Executes against existing orders    |
| Order may remain in book   | Order generally matches immediately |

The exact definitions and fee structures depend on the venue.

---

# 119. Tick Size

**Tick size** is the minimum permitted price movement for an instrument.

Example:

```text
Tick size = ₹0.05
```

Valid prices might include:

```text
₹100.00
₹100.05
₹100.10
₹100.15
```

But:

```text
₹100.03
```

may not be valid if the tick size is ₹0.05.

---

# 120. Lot Size

**Lot size** is the standardized number of units represented by one trading lot for certain instruments.

Example:

```text
1 lot = 50 units
```

If a trader buys:

```text
2 lots
```

the exposure is:

```text
2 × 50 = 100 units
```

Lot sizes depend on the instrument and market.

---

# 121. Contract

A **contract** is a standardized or customized agreement representing a financial obligation or right.

Examples:

* Futures contract
* Options contract
* Forward contract

---

# 122. Expiration / Expiry

The **expiration date** is the date on which a derivative contract reaches the end of its defined life.

For options:

```text
Option
Expiry = specific date
```

After expiration, the option no longer exists as an active contract, subject to settlement rules.

---

# 123. Strike Price

The **strike price** is the predetermined price at which an option can be exercised, subject to the contract terms.

Example:

```text
Call option
Strike = ₹500
```

The option's relationship to the underlying price is evaluated relative to ₹500.

---

# 124. Underlying Asset

The **underlying asset** is the asset or reference value on which a derivative is based.

Examples:

```text
Stock option
Underlying = Stock

Gold futures
Underlying = Gold

Index option
Underlying = Index
```

---

# 125. Futures

A **futures contract** is a standardized derivative contract involving an obligation to buy or sell an underlying asset or reference value according to specified terms at a future date.

Futures commonly involve:

* Contract size
* Expiration
* Price
* Margin
* Settlement rules

---

# 126. Options

An **option** is a derivative contract that gives the holder a right, but generally not an obligation, to buy or sell an underlying asset at specified terms.

Two basic types are:

* Call
* Put

---

# 127. Call Option

A **call option** gives the holder the right, but generally not the obligation, to buy the underlying at the strike price under the contract terms.

A call buyer generally benefits when the underlying price rises sufficiently relative to the option's cost and terms.

---

# 128. Put Option

A **put option** gives the holder the right, but generally not the obligation, to sell the underlying at the strike price under the contract terms.

A put buyer generally benefits when the underlying price falls sufficiently relative to the option's cost and terms.

---

# 129. Option Premium

The **option premium** is the price paid by the option buyer to the option seller for the option contract.

Example:

```text
Option premium = ₹20
```

The premium is a key component of option pricing.

---

# 130. Open Interest

**Open interest (OI)** represents the number of outstanding derivative contracts that remain open.

It is not the same as trading volume.

### Volume

Counts contracts traded during a period.

### Open Interest

Counts contracts that remain open.

Example:

```text
Today:
1,000 contracts traded

Open interest:
500 contracts remain open
```

---

# 131. Volume vs Open Interest

| Volume                                      | Open Interest                             |
| ------------------------------------------- | ----------------------------------------- |
| Trading activity                            | Outstanding contracts                     |
| Measured over a period                      | Represents open positions                 |
| Can increase with opening or closing trades | Changes based on opening/closing activity |

This distinction is extremely important in derivatives.

---

# 132. Intraday Trading

**Intraday trading** means opening and closing a position within the same trading session.

Example:

```text
Buy at 10:00 AM
Sell at 2:00 PM
```

The position is closed before the session ends.

---

# 133. Position Trading

**Position trading** generally involves holding positions for longer periods, potentially weeks, months, or longer.

---

# 134. Swing Trading

**Swing trading** generally seeks to capture price movements over a period ranging from several days to several weeks, depending on the strategy.

---

# 135. Scalping

**Scalping** is a trading approach that attempts to capture very small price movements through many short-duration trades.

Scalpers often care heavily about:

* Spread
* Liquidity
* Execution speed
* Slippage
* Transaction costs

---

# 136. Algorithmic Trading

**Algorithmic trading** uses computer programs to automatically execute trading decisions according to predefined rules or models.

Example:

```text
IF price > moving_average
AND volume > threshold
THEN generate BUY order
```

---

# 137. High-Frequency Trading

**High-Frequency Trading (HFT)** is a form of algorithmic trading characterized by extremely fast order generation and execution, sophisticated technology, and high trading activity.

HFT systems often care about:

* Latency
* Market data speed
* Network performance
* Order execution
* Co-location
* Infrastructure reliability

---

# 138. Trading Strategy

A **trading strategy** is a defined method for deciding:

* When to enter
* When to exit
* What to trade
* How much to trade
* How to manage risk

Example:

```text
IF price crosses above moving average
AND volume increases
THEN BUY
```

---

# 139. Signal

A **signal** is information or a condition that suggests a potential trading action.

Examples:

```text
BUY signal
SELL signal
```

Signals may be generated from:

* Price
* Volume
* Technical indicators
* Fundamental data
* News
* Quantitative models
* Machine learning models

---

# 140. Indicator

A **technical indicator** is a mathematical calculation derived from market data.

Examples:

* Moving Average
* RSI
* MACD
* Bollinger Bands

Indicators are commonly used to analyze price and market behavior.

---

# 141. Moving Average

A **moving average** calculates the average price over a specified number of periods.

Example:

For five prices:

```text
100
102
104
106
108
```

Average:

```text
(100 + 102 + 104 + 106 + 108) / 5
= 104
```

As new data arrives, the calculation moves forward.

---

# 142. Support

**Support** is a price region where buying interest may be strong enough to slow or stop a decline.

Example:

```text
Price repeatedly approaches ₹100
and then rises.
```

₹100 may be considered a support level.

Support is not a guarantee that price cannot fall below it.

---

# 143. Resistance

**Resistance** is a price region where selling interest may be strong enough to slow or stop an increase.

Example:

```text
Price repeatedly approaches ₹150
and then falls.
```

₹150 may be considered a resistance level.

---

# 144. Breakout

A **breakout** occurs when price moves beyond a significant support or resistance area.

Example:

```text
Resistance = ₹100

Price:
98 → 99 → 100 → 103
```

The movement above ₹100 may be described as a breakout.

---

# 145. Pullback

A **pullback** is a temporary movement against the current trend.

Example:

```text
100 → 110 → 106 → 115
```

The movement from ₹110 to ₹106 may be described as a pullback within an upward trend.

---

# 146. Gap

A **gap** occurs when the price moves between trading periods without transactions occurring across a range of prices.

Example:

```text
Previous close = ₹100
Next open = ₹110
```

There is a gap between ₹100 and ₹110.

---

# 147. Market Rally

A **rally** is a sustained upward movement in price.

Example:

```text
100 → 105 → 110 → 118 → 125
```

This can be described as a rally.

---

# 148. Sell-Off

A **sell-off** is a period of significant selling pressure and declining prices.

Example:

```text
200 → 190 → 175 → 160
```

---

# 149. Correction

A **correction** generally refers to a meaningful decline from a recent market high.

It is often used to describe a decline that is less severe than a major bear market, although exact definitions can vary.

---

# 150. Rally vs Sell-Off

| Term     | General Meaning          |
| -------- | ------------------------ |
| Rally    | Strong upward movement   |
| Sell-off | Strong downward movement |

---

# 151. Momentum

**Momentum** refers to the strength or persistence of price movement.

If an asset continues moving strongly in one direction, traders may describe it as having strong momentum.

---

# 152. Market Sentiment

**Market sentiment** represents the overall attitude or expectations of market participants.

It may be:

* Bullish
* Bearish
* Neutral

Sentiment can be influenced by:

* Economic data
* Earnings
* News
* Interest rates
* Geopolitical events
* Market performance

---

# 153. Risk-On

A **risk-on** environment generally means investors are more willing to take risk.

Riskier assets may experience stronger demand.

---

# 154. Risk-Off

A **risk-off** environment generally means investors become more cautious and seek relatively safer assets.

---

# 155. Arbitrage

**Arbitrage** is the attempt to profit from price differences for the same or economically related asset across markets or instruments.

Example:

```text
Market A:
Price = ₹100

Market B:
Price = ₹102
```

A trader may attempt to buy at ₹100 and sell at ₹102, subject to execution, costs, timing, and market constraints.

---

# 156. Hedging

**Hedging** means taking a position designed to reduce or offset the risk of another position.

Example:

A company expects to receive foreign currency later and worries that exchange rates may move unfavorably.

It may use a derivative to hedge the currency risk.

---

# 157. Speculation

**Speculation** involves taking a position with the expectation of profiting from future price movements.

Example:

A trader buys a stock because they expect its price to rise.

---

# 158. Diversification

**Diversification** means spreading investments or exposures across different assets, sectors, markets, or risk factors.

Example:

Instead of:

```text
100% Technology
```

a portfolio might contain:

```text
Technology
Healthcare
Financials
Energy
Bonds
Cash
```

Diversification can reduce concentration risk, although it does not eliminate market risk.

---

# 159. Concentration Risk

**Concentration risk** occurs when too much exposure is concentrated in one:

* Asset
* Company
* Sector
* Country
* Currency
* Strategy

Example:

```text
Portfolio = ₹10 lakh

Technology stocks = ₹9 lakh
```

The portfolio has high technology concentration.

---

# 160. Portfolio

A **portfolio** is the collection of investments or positions held by an individual, institution, fund, or trading account.

Example:

```text
AAPL = 100 shares
MSFT = 50 shares
Gold = 2 contracts
Cash = ₹2 lakh
```

Together, these form a portfolio.

---

# 161. Portfolio Value

**Portfolio value** is the current total value of the portfolio.

A simplified calculation:

```text
Portfolio Value
=
Cash
+
Market Value of Holdings
```

Actual portfolio valuation can be more complex when derivatives, currencies, liabilities, and other factors are involved.

---

# 162. Asset Allocation

**Asset allocation** describes how a portfolio's capital is distributed among asset classes.

Example:

```text
Equities = 60%
Bonds = 30%
Cash = 10%
```

---

# 163. Benchmark

A **benchmark** is a reference index or standard used to evaluate investment or portfolio performance.

Example:

```text
Portfolio return = 12%
Benchmark return = 10%
```

The portfolio outperformed the benchmark by 2 percentage points.

---

# 164. Alpha

**Alpha** generally refers to performance relative to an appropriate benchmark after considering the relevant methodology and risk factors.

Simplified example:

```text
Portfolio = +12%
Benchmark = +10%

Excess return = +2%
```

This simplified difference is often described informally as alpha, although professional alpha calculations can be more sophisticated.

---

# 165. Beta

**Beta** measures how sensitive an asset or portfolio is to movements in a reference market or benchmark.

Simplified interpretation:

```text
Beta = 1
```

The asset tends to move roughly in line with the benchmark.

```text
Beta > 1
```

The asset tends to have greater sensitivity.

```text
Beta < 1
```

The asset tends to have lower sensitivity.

Beta is a statistical measure and does not guarantee future movement.

---

# 166. Sharpe Ratio

The **Sharpe ratio** is a performance measure that compares excess return with volatility.

A simplified formula is:

```text
Sharpe Ratio
=
(Return - Risk-Free Rate) / Volatility
```

A higher Sharpe ratio generally indicates more return relative to the amount of volatility assumed, under the chosen methodology.

---

# 167. Risk-Adjusted Return

**Risk-adjusted return** evaluates returns relative to the amount or type of risk taken.

Two strategies may both produce:

```text
10% return
```

But if one takes significantly more risk, their risk-adjusted performance may be different.

---

# 168. Benchmark Tracking

**Benchmark tracking** refers to monitoring how closely a portfolio or strategy follows its benchmark.

This is particularly important for index-tracking funds and passive investment strategies.

---

# 169. NAV

**NAV** stands for **Net Asset Value**.

For a fund, a simplified concept is:

```text
NAV
=
(Net Assets) / (Units Outstanding)
```

Example:

```text
Assets = ₹10 crore
Liabilities = ₹1 crore
Units = 1 crore

NAV = ₹9 per unit
```

Actual fund valuation follows applicable rules.

---

# 170. ETF

An **Exchange-Traded Fund (ETF)** is an investment fund whose units trade on an exchange.

An ETF may track:

* Index
* Sector
* Commodity
* Asset class
* Investment strategy

ETF units can generally be bought and sold during market hours like other exchange-traded securities.

---

# 171. Index

An **index** represents the performance of a group or basket of securities according to defined rules.

Examples include:

* Broad market indices
* Sector indices
* Large-cap indices
* Bond indices

An index is often used as a benchmark or market indicator.

---

# 172. Index Weight

An **index weight** represents the proportion assigned to a particular constituent in an index.

Example:

```text
Company A = 10%
Company B = 8%
Company C = 5%
```

These weights determine how much each constituent contributes to the index according to the index methodology.

---

# 173. Market Capitalization

**Market capitalization** is the total market value of a company's outstanding shares.

Formula:

```text
Market Capitalization
=
Share Price × Shares Outstanding
```

Example:

```text
Share price = ₹100
Shares outstanding = 1 crore

Market cap = ₹100 crore
```

---

# 174. Large Cap, Mid Cap, Small Cap

These terms generally describe companies according to their market capitalization.

Exact classification thresholds vary by market and regulatory framework.

Generally:

```text
Large Cap → Larger companies
Mid Cap   → Medium-sized companies
Small Cap → Smaller companies
```

---

# 175. Dividend

A **dividend** is a distribution of part of a company's earnings or other distributable resources to shareholders, subject to the company's policies and applicable rules.

Example:

```text
Dividend = ₹5 per share
```

An investor owning 100 shares receives:

```text
₹5 × 100 = ₹500
```

before applicable taxes and adjustments.

---

# 176. Ex-Dividend Date

The **ex-dividend date** is the date from which a buyer may no longer be entitled to receive the upcoming dividend, depending on the market's settlement and record-date rules.

Dividend terminology is closely connected to settlement timing.

---

# 177. Record Date

The **record date** is the date on which eligibility for a corporate action such as a dividend is determined according to the issuer's rules.

---

# 178. Corporate Action

A **corporate action** is an event initiated by a company that affects shareholders or securities.

Examples:

* Dividend
* Stock split
* Bonus issue
* Rights issue
* Merger
* Acquisition
* Share buyback

Trading and portfolio systems must correctly process corporate actions because they can change positions, prices, or entitlements.

---

# 179. Stock Split

A **stock split** increases the number of shares while proportionally reducing the price per share, according to the split ratio.

Example:

```text
Before:
10 shares × ₹100
= ₹1,000

2-for-1 split:

20 shares × ₹50
= ₹1,000
```

Ignoring market movements and other effects.

---

# 180. Reverse Stock Split

A **reverse stock split** reduces the number of shares while proportionally increasing the price per share.

Example:

```text
10 shares × ₹10
= ₹100

1-for-2 reverse split:

5 shares × ₹20
= ₹100
```

Ignoring other effects.

---

# 181. Market Order vs Limit Order

This is one of the most important distinctions to remember.

| Market Order             | Limit Order                            |
| ------------------------ | -------------------------------------- |
| Prioritizes execution    | Prioritizes price                      |
| No fixed execution price | Specifies acceptable price             |
| More execution certainty | Less execution certainty               |
| Can experience slippage  | Price is controlled within order terms |

### Memory Trick

> **Market order = "Get me in/out."**

> **Limit order = "Get me this price or better."**

---

# 182. Bid vs Ask

Another essential distinction:

```text
Bid → Highest price buyers are willing to pay
Ask → Lowest price sellers are willing to accept
```

Example:

```text
Bid = ₹99
Ask = ₹101
```

A buyer looking to execute immediately may interact with the ask.

A seller looking to execute immediately may interact with the bid.

---

# 183. Spread vs Slippage

These concepts are often confused.

### Spread

Difference between:

```text
Bid and Ask
```

### Slippage

Difference between:

```text
Expected execution price
and
Actual execution price
```

Example:

```text
Bid = ₹99
Ask = ₹101

Spread = ₹2
```

If you expected to buy at ₹101 but actually bought at ₹102:

```text
Slippage = ₹1
```

---

# 184. Volume vs Liquidity

These are related but not identical.

### Volume

How much trading occurred.

### Liquidity

How easily you can trade without significantly moving the price.

A market can have high historical volume but temporarily poor liquidity.

---

# 185. Volatility vs Risk

These are related but not identical.

### Volatility

Measures price movement.

### Risk

Refers more broadly to the possibility of an unfavorable outcome.

High volatility can increase certain types of risk, but risk includes much more than volatility.

---

# 186. Position vs Order

This distinction is critical.

### Order

Instruction to trade.

```text
BUY 100 AAPL
```

### Position

Resulting exposure after execution.

```text
AAPL
+100 shares
```

Think:

```text
ORDER
  ↓
EXECUTION
  ↓
POSITION
```

---

# 187. Order vs Trade

An **order** is an instruction.

A **trade** is an executed transaction.

Example:

```text
Order:
BUY 1,000 shares

Execution 1:
400 shares

Execution 2:
600 shares
```

The original order produced multiple executions.

---

# 188. Realized vs Unrealized P&L

Remember:

```text
Open position
     ↓
Unrealized P&L
```

When position closes:

```text
Closed position
     ↓
Realized P&L
```

Example:

```text
Buy = ₹100
Current = ₹120

Unrealized = +₹20
```

After selling:

```text
Sell = ₹120

Realized = +₹20
```

---

# 189. Long vs Short

| Long                                   | Short                                      |
| -------------------------------------- | ------------------------------------------ |
| Generally benefits from price increase | Generally benefits from price decrease     |
| Buy then sell                          | Sell then buy/cover                        |
| Positive exposure                      | Negative exposure in many position systems |

Simplified example:

```text
LONG:
Buy 100
Sell 120
Profit = 20
```

```text
SHORT:
Sell 100
Buy 80
Profit = 20
```

---

# 190. Leverage vs Margin

These terms are connected but different.

### Leverage

How much exposure you can control relative to your capital.

### Margin

The capital/collateral required to support the position.

Example:

```text
Position = ₹100,000
Margin = ₹20,000

Approximate leverage = 5×
```

This is a simplified illustration.

---

# 191. Liquidity vs Market Depth

### Liquidity

How easily you can buy or sell without significant price impact.

### Market Depth

The amount of orders available across different price levels.

Market depth is one factor that contributes to liquidity.

---

# 192. Trading Terminology in a Real Example

Suppose a trading system receives the following information:

```text
Instrument: ABC
Bid: ₹99
Ask: ₹100
Last: ₹99.50
Volume: 500,000
```

A trader submits:

```text
BUY
Quantity: 1,000
Order Type: Market
```

The order is matched against available sell orders.

Suppose the executions are:

```text
400 shares @ ₹100
600 shares @ ₹100.20
```

Then:

```text
Total quantity = 1,000
```

The order is:

```text
FILLED
```

Average execution price:

```text
(400 × 100 + 600 × 100.20) / 1,000
= ₹100.12
```

The trader expected approximately ₹100 but received an average execution price of ₹100.12.

This illustrates:

* Market order
* Ask price
* Execution
* Multiple fills
* Average execution price
* Slippage
* Liquidity
* Market depth

---

# 193. Trading Lifecycle Vocabulary

A simplified trading lifecycle looks like this:

```text
Market Data
     ↓
Trading Decision
     ↓
Order Creation
     ↓
Risk Validation
     ↓
Order Routing
     ↓
Exchange / Venue
     ↓
Order Matching
     ↓
Execution
     ↓
Trade Confirmation
     ↓
Position Update
     ↓
P&L Update
     ↓
Settlement
```

Important terms associated with each stage:

| Stage       | Important Terms             |
| ----------- | --------------------------- |
| Market Data | Bid, Ask, LTP, Volume       |
| Decision    | Signal, Strategy            |
| Order       | Side, Quantity, Price, Type |
| Risk        | Margin, Exposure, Limits    |
| Routing     | Broker, Venue               |
| Matching    | Order Book, Matching Engine |
| Execution   | Fill, Slippage              |
| Position    | Long, Short, Exposure       |
| Performance | P&L                         |
| Settlement  | Clearing, Settlement        |

---

# 194. Trading System Example

Imagine a trading application.

A user wants to buy 500 shares.

### Step 1 — User Creates Order

```text
Instrument = AAPL
Side = BUY
Quantity = 500
Type = LIMIT
Limit Price = $200
```

### Step 2 — Risk System Checks Order

It checks:

```text
Is the account active?
Is there enough buying power?
Is quantity allowed?
Is the instrument tradable?
Does the order violate risk limits?
```

### Step 3 — Order Is Routed

The order is sent toward a trading venue.

### Step 4 — Matching

The venue checks the order book.

### Step 5 — Execution

Suppose:

```text
300 shares @ $199.95
200 shares @ $200.00
```

### Step 6 — Position Update

```text
Position = +500 shares
```

### Step 7 — P&L Calculation

The system continuously calculates unrealized P&L as the market price changes.

This is why trading terminology is not merely theoretical.

These terms appear directly inside real trading applications and backend systems.

---

# 195. Important Trading Terms for Monitoring

If you are working with **monitoring, observability, SRE, or production support for trading systems**, some terminology becomes particularly important.

You may encounter metrics such as:

```text
Order Volume
Trade Volume
Order Rate
Trade Rate
Order Reject Rate
Order Fill Rate
Partial Fill Rate
Execution Latency
Market Data Latency
Order Latency
Exchange Latency
Slippage
Bid-Ask Spread
Position Exposure
P&L
Margin Utilization
Risk Limit Utilization
```

Understanding the trading meaning behind these terms is necessary before interpreting the technical metric.

---

# 196. Order Rate

**Order rate** represents how many orders are being generated during a particular period.

Example:

```text
10,000 orders per second
```

This can be important for capacity monitoring.

---

# 197. Trade Rate

**Trade rate** represents how many trades/executions are occurring during a particular period.

Example:

```text
2,000 trades per second
```

---

# 198. Fill Rate

**Fill rate** measures how much of submitted order quantity is actually executed.

Example:

```text
Ordered = 10,000 shares
Filled = 9,000 shares

Fill Rate = 90%
```

Simplified formula:

```text
Fill Rate
=
Filled Quantity / Ordered Quantity × 100
```

---

# 199. Reject Rate

**Reject rate** measures the percentage of orders rejected by a system or venue.

Example:

```text
Total orders = 10,000
Rejected = 100

Reject Rate = 1%
```

A sudden increase in reject rate can indicate:

* Risk-limit problems
* Connectivity issues
* Invalid orders
* Configuration problems
* Market-status issues
* Exchange problems

---

# 200. Trading Latency

**Latency** is the time taken for information or an action to travel through a system.

Examples:

```text
Market data latency
Order submission latency
Exchange response latency
Execution latency
```

Example:

```text
Order submitted at 10:00:00.000
Exchange receives at 10:00:00.005

Latency = 5 milliseconds
```

---

# 201. Market Data Latency

**Market data latency** is the delay between a market event occurring and the trading system receiving or processing that information.

Example:

```text
Exchange event
      ↓
Market data feed
      ↓
Trading system

Delay = 2 ms
```

Low latency is especially important for latency-sensitive trading strategies.

---

# 202. Order Latency

**Order latency** refers to the time taken for an order to travel from the trading application/system toward the execution venue or through a defined part of the order path.

---

# 203. Execution Latency

**Execution latency** refers to the time between relevant order submission/acceptance events and execution, depending on how the metric is defined.

Always check the exact start and end timestamps used by the system.

---

# 204. Position Reconciliation

**Position reconciliation** means comparing positions between systems to ensure that they agree.

Example:

```text
Trading System:
AAPL = 1,000

Broker:
AAPL = 1,000

Exchange/Clearing:
AAPL = 1,000
```

Everything matches.

If:

```text
Trading System:
AAPL = 1,000

Broker:
AAPL = 900
```

there is a reconciliation discrepancy that requires investigation.

---

# 205. Trade Reconciliation

**Trade reconciliation** compares executed trades between systems.

Example:

```text
Internal System:
Trade ID = T123
Quantity = 500

External System:
Trade ID = T123
Quantity = 500
```

They match.

If they differ, operations or support teams may investigate.

---

# 206. Stale Market Data

**Stale market data** means market information has stopped updating or is older than expected.

Example:

```text
Last update:
10:00:00

Current time:
10:00:30
```

If updates are expected every second, the data may be considered stale.

This is extremely important in automated trading systems.

---

# 207. Market Data Gap

A **market data gap** occurs when expected market data updates are missing.

Example:

```text
10:00:01
10:00:02
10:00:03
10:00:10
```

Updates from:

```text
10:00:04 → 10:00:09
```

are missing.

---

# 208. Trading Halt

A **trading halt** is a temporary suspension of trading in an instrument or market.

Possible reasons include:

* Significant news
* Technical problems
* Regulatory action
* Extreme market conditions
* Corporate events

During a halt, order behavior depends on the market's rules.

---

# 209. Circuit Breaker

A **circuit breaker** is a mechanism designed to temporarily restrict or halt trading when predefined market-movement thresholds are reached.

Its purpose is generally to:

* Reduce extreme volatility
* Give participants time to process information
* Protect orderly market functioning

Rules vary by market.

---

# 210. Auction

An **auction** is a mechanism used to determine an execution price by matching aggregated buy and sell interest according to predefined rules.

Auctions may be used during:

* Market opening
* Market closing
* Volatility interruptions
* Other special trading periods

depending on the venue.

---

# 211. Crossing

A **crossing transaction** generally involves matching buy and sell interests according to applicable market and venue rules.

The exact meaning varies by trading context.

---

# 212. Wash Trade

A **wash trade** generally refers to transactions where there is no genuine change in economic ownership and the activity is designed to create artificial market activity.

Such activity may violate market rules or regulations.

This term is important in market surveillance.

---

# 213. Spoofing

**Spoofing** generally involves placing orders with the intention of cancelling them before execution in order to create a misleading impression of supply or demand.

It is generally prohibited in regulated markets.

---

# 214. Market Manipulation

**Market manipulation** refers to activities intended to artificially influence the price, volume, or appearance of trading activity.

Examples can include:

* Spoofing
* Wash trading
* False signals
* Other deceptive trading practices

Exact legal definitions depend on the jurisdiction and market.

---

# 215. Insider Trading

**Insider trading** generally refers to trading securities while improperly using material non-public information, subject to applicable laws and definitions.

It is an important concept in financial markets and compliance.

---

# 216. Compliance

**Compliance** means ensuring that trading activity follows applicable:

* Laws
* Regulations
* Exchange rules
* Internal policies
* Risk controls

Trading systems may contain automated compliance checks.

---

# 217. Audit Trail

An **audit trail** is a historical record of important system and trading events.

A trading audit trail may contain:

```text
Order ID
Timestamp
User/System
Instrument
Side
Quantity
Price
Order Type
Status
Execution
Modification
Cancellation
```

Audit trails are important for:

* Investigation
* Compliance
* Debugging
* Reconciliation
* Incident analysis

---

# 218. Timestamp

A **timestamp** records when an event occurred.

Trading systems may have many timestamps:

```text
Order Created
Order Sent
Order Received
Order Accepted
Order Matched
Order Executed
Trade Confirmed
```

Accurate timestamps are extremely important when analyzing latency and incidents.

---

# 219. Market Event

A **market event** is an event that changes or provides information about market conditions.

Examples:

* Price update
* Trade execution
* Order book update
* Trading halt
* Market open
* Market close
* Corporate action

---

# 220. Event-Driven Trading

**Event-driven trading** uses events as triggers for trading decisions.

Example:

```text
Company announces earnings
        ↓
System receives event
        ↓
Strategy evaluates event
        ↓
Trading signal generated
```

---

# 221. Common Trading Abbreviations

| Abbreviation | Meaning                               |
| ------------ | ------------------------------------- |
| P&L          | Profit and Loss                       |
| LTP          | Last Traded Price                     |
| LTV          | Loan-to-Value in applicable contexts  |
| OI           | Open Interest                         |
| IV           | Implied Volatility                    |
| TIF          | Time in Force                         |
| IOC          | Immediate or Cancel                   |
| FOK          | Fill or Kill                          |
| GTC          | Good Till Cancelled                   |
| ETF          | Exchange-Traded Fund                  |
| NAV          | Net Asset Value                       |
| IPO          | Initial Public Offering               |
| HFT          | High-Frequency Trading                |
| VWAP         | Volume Weighted Average Price         |
| TWAP         | Time Weighted Average Price           |
| RSI          | Relative Strength Index               |
| MACD         | Moving Average Convergence Divergence |
| ATR          | Average True Range                    |
| AUM          | Assets Under Management               |

---

# 222. VWAP

**VWAP** stands for **Volume Weighted Average Price**.

It represents the average traded price weighted by volume over a specified period.

Simplified formula:

```text
VWAP
=
Σ(Price × Volume) / ΣVolume
```

Example:

```text
Trade 1:
Price = ₹100
Volume = 100

Trade 2:
Price = ₹110
Volume = 300
```

VWAP:

```text
(100×100 + 110×300) / (100+300)

= 43,000 / 400

= ₹107.50
```

VWAP is commonly used as a trading and execution benchmark.

---

# 223. TWAP

**TWAP** stands for **Time Weighted Average Price**.

It calculates an average price based on time intervals rather than weighting each trade by volume in the same way as VWAP.

TWAP can be used as an execution benchmark or strategy.

---

# 224. VWAP vs TWAP

| VWAP                       | TWAP                          |
| -------------------------- | ----------------------------- |
| Volume-weighted            | Time-weighted                 |
| Considers trading volume   | Divides execution across time |
| Common execution benchmark | Common execution benchmark    |

---

# 225. IPO

**IPO** stands for **Initial Public Offering**.

It is the process through which a private company offers shares to the public and becomes publicly traded, subject to applicable regulations and market structure.

---

# 226. AUM

**AUM** stands for **Assets Under Management**.

It represents the value of assets managed by an investment manager or institution.

Example:

```text
AUM = ₹1,000 crore
```

---

# 227. Settlement Price

A **settlement price** is the price used for settlement purposes according to the rules of a particular market or derivative contract.

It may differ from the last traded price.

This distinction is particularly important in derivatives.

---

# 228. Mark-to-Market

**Mark-to-market (MTM)** means valuing a position using current market prices.

Example:

```text
Bought at ₹100
Current market price = ₹110
```

The position is marked at ₹110 for valuation purposes.

MTM is particularly important for:

* Futures
* Margin systems
* Risk systems
* Portfolio valuation

---

# 229. Collateral

**Collateral** is an asset or cash provided to support a financial obligation.

In leveraged trading and derivatives, collateral helps protect against losses and counterparty risk.

---

# 230. Haircut

A **haircut** is a reduction applied to the recognized value of collateral for risk-management purposes.

Example:

```text
Asset market value = ₹100,000
Haircut = 20%

Recognized collateral value = ₹80,000
```

This is a simplified example.

---

# 231. Default

A **default** occurs when a party fails to meet its financial or contractual obligations according to applicable terms.

---

# 232. Settlement Risk

**Settlement risk** is the possibility that a transaction is not properly completed during settlement.

---

# 233. Liquidity Risk

**Liquidity risk** is the risk that a position cannot be bought or sold quickly enough or at a reasonable price.

This can lead to:

* Large spreads
* Slippage
* Market impact
* Difficulty exiting positions

---

# 234. Market Risk

**Market risk** is the possibility of losses caused by unfavorable movements in market prices.

Examples:

* Stock prices fall
* Interest rates change
* Currency rates move
* Commodity prices change

---

# 235. Operational Risk

**Operational risk** arises from failures in:

* Processes
* People
* Systems
* Technology
* Infrastructure

Example:

```text
Trading system failure
→ orders cannot be submitted
→ trading disruption
```

---

# 236. Technology Risk

**Technology risk** is the risk arising from failures in technical infrastructure.

Examples:

* Server failure
* Database failure
* Network failure
* Software bug
* API failure
* Deployment issue
* Message queue failure

This becomes especially important when monitoring trading systems.

---

# 237. Model Risk

**Model risk** is the risk that a mathematical or algorithmic model is incorrect, poorly implemented, or used outside its valid assumptions.

Trading systems may use models for:

* Pricing
* Risk
* Forecasting
* Signal generation
* Portfolio optimization

---

# 238. Latency Risk

**Latency risk** occurs when delays in receiving information, making decisions, routing orders, or executing trades negatively affect trading outcomes.

Example:

```text
Market moves rapidly
        ↓
System receives data late
        ↓
Old price used for decision
        ↓
Order executed at worse price
```

---

# 239. Data Quality

**Data quality** refers to the accuracy, completeness, consistency, timeliness, and validity of market or trading data.

Poor data can cause:

* Incorrect signals
* Incorrect valuation
* Incorrect risk calculations
* Bad trading decisions

---

# 240. Data Integrity

**Data integrity** means ensuring that trading and financial data remains accurate and unaltered inappropriately throughout its lifecycle.

This is extremely important for:

* Orders
* Trades
* Positions
* P&L
* Market data
* Audit records

---

# 241. Reconciliation Break

A **reconciliation break** occurs when two systems report different information for what should be the same transaction, position, cash balance, or other record.

Example:

```text
System A:
Position = 1,000

System B:
Position = 950

Difference = 50
```

This is a reconciliation break.

---

# 242. Trading Book

A **trading book** is a collection of positions held for trading purposes, depending on the institution and regulatory framework.

It may contain:

* Equities
* Bonds
* Derivatives
* Currencies
* Other instruments

---

# 243. Position Book

A **position book** is a record of current positions held by a trader, account, desk, or institution.

---

# 244. Order Management System

An **Order Management System (OMS)** manages the lifecycle of orders.

It may handle:

```text
Order creation
      ↓
Validation
      ↓
Routing
      ↓
Modification
      ↓
Cancellation
      ↓
Execution
      ↓
Status tracking
```

---

# 245. Execution Management System

An **Execution Management System (EMS)** focuses more specifically on execution and routing of orders.

It may help traders:

* Select venues
* Manage execution
* Monitor orders
* Analyze execution quality

---

# 246. Risk Management System

A **Risk Management System** monitors and controls trading risk.

It may check:

```text
Position limits
Exposure
Margin
P&L
Order limits
Credit limits
Market risk
```

A risk system may reject an order before it reaches the market.

---

# 247. Market Data System

A **market data system** receives, processes, stores, and distributes market information.

It may handle:

```text
Quotes
Trades
Order book updates
Reference data
Corporate actions
```

---

# 248. Reference Data

**Reference data** is relatively stable information used to describe financial instruments and entities.

Examples:

```text
Instrument ID
Ticker
Currency
Exchange
Asset class
Contract size
Tick size
Lot size
Expiry date
```

Reference data is essential for correctly processing orders and market data.

---

# 249. Trading Calendar

A **trading calendar** defines:

* Trading days
* Holidays
* Market sessions
* Opening times
* Closing times

Trading systems need accurate calendars to determine whether a market is open.

---

# 250. Why Trading Terminology Matters for Finance + Technology

If you work on a financial trading platform, you may see an alert like:

```text
Order Reject Rate increased to 12%
```

A technical engineer cannot properly investigate this without understanding what an order rejection means.

Similarly:

```text
Market Data Latency increased
```

requires understanding what market data is.

And:

```text
Position reconciliation failed
```

requires understanding positions.

Therefore:

> **Trading terminology is the bridge between financial concepts and technical systems.**

---

# 251. The Most Important Terms to Memorize

If you are starting from zero, prioritize these first.

## Price Terms

```text
Bid
Ask
Spread
Last Traded Price
Quote
```

## Market Terms

```text
Volume
Liquidity
Market Depth
Volatility
Order Book
```

## Trading Direction

```text
Long
Short
Bullish
Bearish
```

## Order Terms

```text
Order
Market Order
Limit Order
Stop Order
Stop-Loss
Take-Profit
Order ID
```

## Execution Terms

```text
Execution
Fill
Partial Fill
Slippage
Market Impact
```

## Risk Terms

```text
Margin
Leverage
Exposure
Risk Limit
Position Limit
Drawdown
```

## P&L Terms

```text
Realized P&L
Unrealized P&L
Mark-to-Market
```

## Derivative Terms

```text
Futures
Options
Call
Put
Strike Price
Expiry
Premium
Open Interest
Underlying
```

## Market Infrastructure

```text
Exchange
Broker
Trading Venue
Matching Engine
Order Management System
Risk Management System
Market Data System
```

---

# 252. One-Line Memory Map

Use this mental map to remember the terminology.

```text
MARKET
│
├── PRICE
│   ├── Bid
│   ├── Ask
│   ├── Spread
│   └── LTP
│
├── ACTIVITY
│   ├── Volume
│   ├── Turnover
│   └── Open Interest
│
├── LIQUIDITY
│   ├── Market Depth
│   ├── Spread
│   └── Market Impact
│
├── DIRECTION
│   ├── Long
│   ├── Short
│   ├── Bullish
│   └── Bearish
│
├── ORDER
│   ├── Market
│   ├── Limit
│   ├── Stop
│   └── Time-in-Force
│
├── EXECUTION
│   ├── Fill
│   ├── Partial Fill
│   ├── Slippage
│   └── Execution Price
│
├── POSITION
│   ├── Long Position
│   ├── Short Position
│   ├── Exposure
│   └── Notional
│
├── RISK
│   ├── Margin
│   ├── Leverage
│   ├── Drawdown
│   └── Risk Limits
│
├── PERFORMANCE
│   ├── Realized P&L
│   ├── Unrealized P&L
│   ├── Alpha
│   └── Sharpe Ratio
│
├── DERIVATIVES
│   ├── Futures
│   ├── Options
│   ├── Strike
│   ├── Expiry
│   ├── Premium
│   └── Open Interest
│
└── INFRASTRUCTURE
    ├── Broker
    ├── Exchange
    ├── Order Book
    ├── Matching Engine
    ├── OMS
    ├── EMS
    └── Risk System
```

---

# 253. Final Mental Model

Do not try to memorize 250+ definitions individually.

Instead, understand the complete chain:

```text
MARKET
   ↓
Market Data
   ↓
Bid / Ask / Price / Volume
   ↓
Trader Decision
   ↓
Buy / Sell
   ↓
Order
   ↓
Market / Limit / Stop
   ↓
Risk Check
   ↓
Broker / Venue
   ↓
Order Book
   ↓
Matching Engine
   ↓
Execution
   ↓
Fill / Partial Fill
   ↓
Slippage
   ↓
Trade
   ↓
Position
   ↓
Exposure
   ↓
P&L
   ↓
Risk Monitoring
   ↓
Clearing
   ↓
Settlement
```

If you understand this flow, a large portion of trading terminology becomes much easier to remember.

---

# 254. Quick Revision Sheet

## Price

```text
Bid = highest current buyer price
Ask = lowest current seller price
Spread = Ask - Bid
LTP = most recent traded price
```

## Market

```text
Volume = amount traded
Liquidity = ease of trading
Depth = available orders at different prices
Volatility = degree of price movement
```

## Trading Direction

```text
Long = benefit generally from price rising
Short = benefit generally from price falling
Bullish = expecting rise
Bearish = expecting fall
```

## Orders

```text
Market = prioritize execution
Limit = prioritize price
Stop = activates at trigger
Stop-loss = intended to limit loss
Take-profit = intended to capture target profit
```

## Execution

```text
Fill = executed quantity
Partial fill = only part executed
Slippage = expected vs actual execution difference
Market impact = order affects market price
```

## Risk

```text
Leverage = increased exposure relative to capital
Margin = collateral/capital supporting position
Exposure = financial sensitivity/amount at risk
Drawdown = decline from previous peak
```

## P&L

```text
Realized P&L = closed-position gain/loss
Unrealized P&L = open-position gain/loss
MTM = valuing position using current market price
```

## Derivatives

```text
Future = derivative with contractual obligation
Option = right, generally without obligation
Call = right to buy
Put = right to sell
Strike = contractual exercise price
Premium = option price
Expiry = contract expiration
OI = outstanding derivative contracts
```

## Infrastructure

```text
Broker = provides market access/services
Exchange = organized trading marketplace
Order Book = outstanding buy/sell orders
Matching Engine = matches compatible orders
OMS = manages orders
EMS = focuses on execution
Risk System = controls trading risk
Market Data System = distributes market information
```

---

# 255. Key Takeaways

1. **Bid** is the highest price a buyer is willing to pay.
2. **Ask** is the lowest price a seller is willing to accept.
3. **Spread** is the difference between bid and ask.
4. **LTP** is the most recent traded price.
5. **Volume** represents trading activity.
6. **Liquidity** describes how easily an asset can be traded.
7. **Volatility** describes the magnitude of price movement.
8. **Long** generally benefits from rising prices.
9. **Short** generally benefits from falling prices.
10. An **order** is an instruction to trade.
11. A **trade** is an executed transaction.
12. A **market order** prioritizes execution.
13. A **limit order** prioritizes price.
14. A **fill** represents executed quantity.
15. A **partial fill** means only part of the order was executed.
16. **Slippage** is the difference between expected and actual execution price.
17. **Leverage** increases exposure relative to capital.
18. **Margin** is collateral/capital required to support certain positions.
19. **Exposure** represents financial sensitivity to an asset or risk factor.
20. **Realized P&L** comes from closed positions.
21. **Unrealized P&L** comes from open positions.
22. **Drawdown** measures decline from a previous peak.
23. **Futures** and **options** are derivatives.
24. **Open interest** represents outstanding derivative contracts.
25. **Order books** contain outstanding buy and sell orders.
26. **Matching engines** match compatible orders.
27. **Market data** provides information about market activity.
28. **Risk systems** protect the trading environment from excessive exposure.
29. **Reconciliation** ensures different systems agree on trades and positions.
30. Trading terminology connects **finance, trading, technology, risk, and operations**.

---

# 256. Final Memory Trick

Remember trading as:

> **PRICE → ORDER → RISK → EXECUTION → POSITION → P&L → SETTLEMENT**

And remember the core vocabulary:

```text
PRICE
Bid / Ask / Spread / LTP

ORDER
Buy / Sell / Market / Limit / Stop

EXECUTION
Fill / Partial Fill / Slippage

POSITION
Long / Short / Exposure

RISK
Margin / Leverage / Limits / Drawdown

PERFORMANCE
Realized P&L / Unrealized P&L

MARKET
Volume / Liquidity / Volatility / Depth

DERIVATIVES
Futures / Options / Strike / Expiry / Premium / OI

INFRASTRUCTURE
Exchange / Broker / Order Book / Matching Engine / OMS / Risk System
```

Once these terms become familiar, reading trading dashboards, alerts, logs, order-management systems, risk systems, and financial-market documentation becomes dramatically easier.
