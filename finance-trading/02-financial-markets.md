# Financial Markets

## Introduction

In the previous chapter, we learned the foundations of finance:

* Money
* Assets
* Investment
* Trading
* Risk
* Liquidity
* Volume
* Price
* Buyers and sellers

Now we need to understand the environment in which all of this happens:

> **The financial market.**

If you are completely new to finance, do not think of a financial market as simply a website or a physical building where people buy stocks.

A financial market is better understood as a **system that allows participants to buy, sell, transfer, finance, and manage financial assets and risk.**

For a Trading DevOps/SRE engineer, understanding financial markets is important because the behavior of trading systems depends heavily on:

* Which market is being traded
* Which asset is being traded
* Which participants are active
* Which trading venue is being used
* Whether the market is open
* How much activity is occurring
* How liquid the market is
* What market conditions exist

---

# 1. What Is a Financial Market?

At the simplest level:

> **A financial market is a system where people and organizations can buy and sell financial assets or financial contracts.**

The participants can include:

* Individuals
* Companies
* Banks
* Investment firms
* Asset managers
* Hedge funds
* Brokers
* Dealers
* Market makers
* Governments
* Exchanges
* Other financial institutions

A very simple model is:

```text
Buyer
  │
  │ Wants to buy
  ▼
FINANCIAL MARKET
  ▲
  │ Wants to sell
  │
Seller
```

The market provides mechanisms that allow these participants to interact.

---

# 2. A Market Is More Than a Place

When people hear "market", they may imagine a physical location.

For example:

```text
A marketplace
     ↓
People meet
     ↓
People buy and sell
```

Financial markets can work very differently.

Modern financial markets are largely electronic.

A transaction may involve:

```text
Trader
  ↓
Trading Application
  ↓
Network
  ↓
Trading System
  ↓
Exchange / Trading Venue
  ↓
Matching
  ↓
Execution
```

There may be no physical interaction between the buyer and seller.

Therefore:

> **A financial market is primarily a mechanism and infrastructure for financial transactions, not necessarily a physical location.**

---

# 3. Why Do Financial Markets Exist?

Financial markets perform several important functions.

The most important ones are:

1. Connecting buyers and sellers
2. Enabling trading
3. Providing liquidity
4. Helping discover prices
5. Moving capital
6. Enabling risk management
7. Providing market information

Let's understand each one.

---

# 4. Function 1 — Connecting Buyers and Sellers

Imagine:

```text
Person A:
"I want to buy 100 shares."
```

and:

```text
Person B:
"I want to sell 100 shares."
```

The market provides a mechanism for these intentions to meet.

```text
Buyer
  │
  │ Buy 100
  ▼
Market
  ▲
  │ Sell 100
  │
Seller
```

If the requirements match, a transaction can occur.

This is one of the fundamental purposes of a market.

---

# 5. Function 2 — Enabling Trading

A market provides rules and infrastructure that allow participants to trade.

This may involve:

* Order submission
* Order validation
* Order matching
* Execution
* Trade reporting
* Clearing
* Settlement

A simplified lifecycle is:

```text
Order
  ↓
Market
  ↓
Matching
  ↓
Execution
  ↓
Trade
```

Later, we will study this lifecycle in much greater detail.

---

# 6. Function 3 — Providing Liquidity

A market works better when participants can buy or sell relatively easily.

Suppose you want to buy something.

If there are many sellers:

```text
Buyer
  ↓
Many Sellers
  ↓
Easy to Trade
```

The market is generally more liquid.

If there are very few sellers:

```text
Buyer
  ↓
Only One Seller
  ↓
Harder to Trade
```

The market may be less liquid.

Remember:

> **Liquidity is about how easily you can transact without significantly affecting the price.**

---

# 7. Function 4 — Price Discovery

One of the most important functions of a financial market is **price discovery**.

Price discovery means that market activity helps determine the current price of an asset.

For example:

```text
Many buyers
      +
Fewer sellers
      ↓
Buying pressure
      ↓
Price may increase
```

Conversely:

```text
Many sellers
      +
Fewer buyers
      ↓
Selling pressure
      ↓
Price may decrease
```

Real markets are much more complex than this simplified example.

However, the fundamental idea is:

> **Market participants continuously interact, and their orders and trades contribute to determining market prices.**

---

# 8. Function 5 — Capital Formation

Financial markets allow companies and other organizations to obtain capital.

Suppose a company wants money to expand.

It could raise capital by issuing securities.

For example:

```text
Investors
   │
   │ Capital
   ▼
Company
   │
   │ Securities
   ▼
Investors
```

The company gets funding.

Investors receive financial instruments representing ownership or claims.

This helps capital move from those who have available funds to those who need funding.

---

# 9. Function 6 — Risk Management

Financial markets also help participants manage risk.

Suppose a company is exposed to changes in:

* Currency prices
* Interest rates
* Commodity prices
* Stock prices

It may use financial instruments to reduce or manage that exposure.

This is one reason derivatives markets exist.

For now, remember:

> **Financial markets are not only about making money; they are also about transferring and managing risk.**

---

# 10. Function 7 — Providing Information

Markets continuously generate information.

Examples:

* Prices
* Bid prices
* Ask prices
* Trading volume
* Trades
* Market depth
* Volatility

This information is distributed through **market data systems**.

A simplified flow is:

```text
Market Activity
      ↓
Trades / Quotes / Orders
      ↓
Market Data
      ↓
Traders / Applications / Systems
```

For a Trading SRE, this is extremely important.

If market data becomes delayed or stale, the trading application may technically be running while the business is still impaired.

---

# 11. The Major Types of Financial Markets

Financial markets can be categorized in many ways.

At a high level, we commonly encounter:

```text
Financial Markets
│
├── Equity Markets
├── Bond / Fixed Income Markets
├── Foreign Exchange Markets
├── Commodity Markets
└── Derivatives Markets
```

We will study the underlying asset classes in the next chapter.

For now, understand what each broadly represents.

---

# 12. Equity Market

The equity market is where ownership interests in companies are bought and sold.

The most familiar example is a stock.

Suppose:

```text
Company X
```

has shares available for trading.

An investor may:

```text
Buy 100 shares
```

Another participant may:

```text
Sell 100 shares
```

The market facilitates the transaction.

A simplified flow:

```text
Investor
   ↓
Broker / Trading System
   ↓
Exchange / Venue
   ↓
Other Market Participant
```

---

# 13. Bond / Fixed Income Market

The fixed-income market includes debt instruments.

A simple example is a bond.

Conceptually:

```text
Investor
   │
   │ Money
   ▼
Issuer
   │
   │ Debt obligation
   ▼
Investor
```

The issuer generally agrees to make specified payments according to the terms of the instrument.

Examples include:

* Government bonds
* Corporate bonds
* Other debt securities

The technology and market structure can differ significantly from equity markets.

This matters because monitoring requirements can also differ.

---

# 14. Foreign Exchange Market

The foreign exchange market, commonly called **FX** or **Forex**, involves currencies.

For example:

```text
EUR/USD
```

This represents one currency relative to another.

A participant may want to exchange:

```text
EUR ↔ USD
```

FX markets are particularly important for global organizations.

From an SRE perspective, FX systems can involve:

* High transaction volumes
* Real-time pricing
* Market data
* Low-latency execution
* Global time zones
* Multiple trading sessions

---

# 15. Commodity Market

Commodity markets involve goods or contracts related to commodities.

Examples:

* Crude oil
* Natural gas
* Gold
* Silver
* Agricultural products

Commodity trading can involve physical commodities or financial contracts based on them.

---

# 16. Derivatives Market

Derivatives are financial contracts whose value is derived from another asset, index, rate, or variable.

The underlying thing is sometimes called the:

> **Underlying asset**

Examples include:

* Futures
* Options
* Forwards
* Swaps

For example:

```text
Underlying:
Oil price

Derivative:
Contract whose value depends on oil price
```

Derivatives can be used for:

* Hedging
* Risk management
* Speculation
* Exposure management

We will study these later.

---

# 17. Primary Market vs Secondary Market

This distinction is extremely important.

Financial markets can be broadly understood as having **primary** and **secondary** market activity.

---

# 18. Primary Market

The primary market is where securities are issued to investors for the first time.

For example, suppose a company wants to raise money.

It issues new shares.

```text
Company
   │
   │ New shares
   ▼
Investors
   │
   │ Money
   ▼
Company
```

The company receives the capital.

This is primary-market activity.

A common example is an **IPO — Initial Public Offering**.

---

# 19. What Is an IPO?

IPO stands for:

> **Initial Public Offering**

It is the process through which a private company offers shares to the public and becomes publicly traded, subject to the applicable regulatory and market process.

Simplified:

```text
Private Company
       ↓
IPO
       ↓
Publicly Traded Company
```

The IPO itself is part of the primary-market process.

---

# 20. Secondary Market

After securities have been issued, investors can trade them with other investors.

Suppose:

```text
Investor A owns shares
```

and:

```text
Investor B wants those shares
```

Investor A can sell them to Investor B.

```text
Investor A
    │
    │ Shares
    ▼
Investor B

Investor B
    │
    │ Money
    ▼
Investor A
```

The company generally does not receive the money from this secondary-market transaction.

This is secondary-market activity.

---

# 21. Primary vs Secondary Market

Remember this simple distinction:

```text
PRIMARY MARKET

Company
   ↕
Investor
```

Money flows to the issuer when securities are issued.

---

```text
SECONDARY MARKET

Investor
   ↕
Investor
```

Existing securities are traded among market participants.

### Memory Trick

> **Primary = First issue**

> **Secondary = Subsequent trading**

---

# 22. Why Does the Secondary Market Matter to Us?

Most everyday trading activity that people associate with stock markets happens in the secondary market.

For example:

```text
Investor buys shares today
        ↓
Another investor sells shares
        ↓
Trade executes
```

The trading systems we monitor may process huge numbers of these transactions.

Therefore, concepts such as:

* Order volume
* Trade volume
* Execution latency
* Market data
* Exchange connectivity

become extremely important.

---

# 23. Market Participants

A financial market contains many different participants.

Some important ones are:

* Retail investors
* Institutional investors
* Traders
* Brokers
* Dealers
* Market makers
* Investment banks
* Asset managers
* Hedge funds
* Exchanges
* Clearing organizations
* Custodians

These participants have different objectives.

---

# 24. Retail Investor

A retail investor is generally an individual investor.

For example:

```text
Individual Person
      ↓
Broker
      ↓
Market
```

Retail investors may use:

* Mobile applications
* Web platforms
* Brokerage platforms
* APIs

---

# 25. Institutional Investor

Institutional investors are organizations that invest significant amounts of capital.

Examples include:

* Pension funds
* Insurance companies
* Mutual funds
* Asset managers
* Endowments
* Sovereign wealth funds

Their transactions can be much larger and more complex than those of individual investors.

---

# 26. Investment Bank

An investment bank can perform many financial activities.

Depending on the organization, these can include:

* Capital raising
* Trading
* Market making
* Advisory
* Research
* Risk management
* Institutional execution

For our purposes, remember that large financial institutions often operate complex technology ecosystems supporting these activities.

---

# 27. Asset Manager

An asset manager manages investments on behalf of clients or funds.

For example:

```text
Clients
   ↓
Asset Manager
   ↓
Portfolio
   ↓
Financial Markets
```

The asset manager may make investment and trading decisions according to the fund's strategy.

---

# 28. Hedge Fund

A hedge fund is an investment vehicle that can use a variety of strategies, subject to its mandate and applicable regulations.

Strategies can involve:

* Equities
* Bonds
* Derivatives
* Arbitrage
* Long/short strategies
* Other instruments

The exact strategy varies greatly by fund.

For an SRE, the important point is that different strategies can produce very different workloads and system requirements.

---

# 29. Broker

A broker facilitates access to financial markets.

A simplified model:

```text
Client
  ↓
Broker
  ↓
Trading Venue
```

A broker may provide:

* Market access
* Order routing
* Execution services
* Account services
* Market information

---

# 30. Dealer

A dealer may trade financial instruments for its own account and can also facilitate transactions with clients, depending on its role and market.

The important distinction is that a dealer can act as a principal in transactions rather than simply acting as an intermediary.

---

# 31. Market Maker

A market maker provides liquidity by quoting prices at which it is willing to buy and sell.

For example:

```text
Market Maker Quote

Bid = ₹99
Ask = ₹101
```

A participant may sell to the market maker at the bid or buy from the market maker at the ask, subject to market conditions and execution rules.

Market makers can be especially important in maintaining liquidity.

---

# 32. Exchange

An exchange is an organized trading venue operating under defined rules.

It can provide:

* Trading infrastructure
* Order matching
* Market data
* Trading rules
* Market access
* Trade reporting

A simplified model:

```text
Trader
  ↓
Broker / Trading System
  ↓
Exchange
  ↓
Matching
  ↓
Execution
```

From a technology perspective:

> **An exchange can be a critical external dependency.**

If connectivity to the exchange fails, the trading business may be unable to execute orders.

---

# 33. Trading Venue

Not every trade necessarily occurs on a traditional exchange.

A **trading venue** is a broader concept describing a place or system where trading can occur.

Depending on the market, venues can include:

* Exchanges
* Electronic communication networks
* Alternative trading systems
* Other electronic marketplaces
* Dealer networks

The exact terminology and regulatory structure vary by market and jurisdiction.

For our purposes:

> **Exchange is one type of trading venue.**

---

# 34. Centralized vs Decentralized Market Structures

Different markets can operate using different structures.

## Centralized

A central venue organizes trading.

Conceptually:

```text
Buyer ──┐
        ├──► Central Venue
Seller ─┘
```

The venue may perform matching and publish market information.

---

## Decentralized / Distributed

Trading can occur through multiple participants or venues.

Conceptually:

```text
Buyer
 │
 ├──► Venue A
 │
 ├──► Venue B
 │
 └──► Venue C
```

This can make routing and monitoring more complex.

---

# 35. Order Book

One of the most important concepts in electronic trading is the **order book**.

An order book contains buy and sell interest for an instrument.

A simplified example:

```text
SELL ORDERS
----------------
₹105   200 shares
₹104   300 shares
₹103   500 shares
----------------
₹102   ← Best Ask


₹101   ← Best Bid
----------------
₹101   400 shares
₹100   600 shares
₹99    800 shares
----------------
BUY ORDERS
```

The top buy price is the best bid.

The top sell price is the best ask.

Therefore:

```text
Best Bid = ₹101
Best Ask = ₹102
```

Spread:

```text
₹102 - ₹101 = ₹1
```

---

# 36. Why Is the Order Book Important?

The order book helps show available buying and selling interest.

It can provide information about:

* Liquidity
* Bid/ask spread
* Market depth
* Potential execution availability

From a technology perspective, order-book systems can generate extremely high message volumes.

This creates monitoring requirements around:

* Message rate
* Processing latency
* Memory
* CPU
* Queue depth
* Data freshness

---

# 37. Market Depth

Market depth refers broadly to the quantity of buy and sell interest available at different price levels.

For example:

```text
Price     Quantity

₹105        100
₹104        200
₹103        500
₹102        700
----------------
₹101        800
₹100        600
₹99         400
```

A deeper market generally has more available liquidity across price levels.

---

# 38. Matching

Electronic trading systems often use matching mechanisms to determine when buy and sell orders can trade.

Suppose:

```text
Buyer:
"I will buy at ₹100"

Seller:
"I will sell at ₹100"
```

The prices are compatible.

A simplified representation:

```text
Buy ₹100
   +
Sell ₹100
   ↓
Match
   ↓
Execution
   ↓
Trade
```

The exact matching rules depend on the market and venue.

---

# 39. Price-Time Priority

One common matching principle is **price-time priority**.

Very simply:

### Price priority

Better prices generally receive priority.

For buyers:

```text
Higher bid = Better price
```

For sellers:

```text
Lower ask = Better price
```

### Time priority

If two orders have the same price, the earlier order may receive priority.

Example:

```text
Order A
Price = ₹100
Time = 10:00:01

Order B
Price = ₹100
Time = 10:00:02
```

Order A may have priority.

The exact rules vary by venue.

---

# 40. Why Matching Matters to an SRE

Matching systems can be extremely latency-sensitive.

Imagine:

```text
100,000 messages/sec
```

A small processing delay can create:

```text
Queue Growth
    ↓
Latency Increase
    ↓
Delayed Execution
    ↓
Potential Business Impact
```

Therefore, monitoring may include:

* Message throughput
* Matching latency
* Queue depth
* CPU
* Memory
* Network latency
* Error rate

---

# 41. Market Data

Markets generate large amounts of information.

Examples:

```text
Bid
Ask
Trade
Price
Volume
Order Book
```

This information is distributed as **market data**.

A simplified flow:

```text
Exchange
   ↓
Market Data
   ↓
Feed
   ↓
Feed Handler
   ↓
Trading Applications
```

Market data will have its own dedicated chapters later.

---

# 42. Trading Venue Connectivity

Trading systems need to communicate with external venues.

Conceptually:

```text
Internal Trading System
          │
          ▼
Connectivity Layer
          │
          ▼
External Venue
```

Connectivity can use:

* FIX
* APIs
* Specialized protocols
* Network connections

Failures can include:

* Connection loss
* Timeout
* Authentication failure
* Message rejection
* Sequence problems
* Network latency

These are highly relevant to monitoring.

---

# 43. Market Hours

Financial markets are generally not active in exactly the same way all the time.

Depending on the market, there can be:

* Pre-market
* Opening phase
* Regular session
* Closing phase
* After-hours
* Overnight trading

This creates predictable workload patterns.

For example:

```text
Activity
  │
  │             /\        /\
  │            /  \      /  \
  │___________/    \____/    \____
  └───────────────────────────────► Time
       Open       Midday      Close
```

The exact pattern varies by market.

---

# 44. Trading Calendar

A trading calendar defines when a market is expected to be open or closed.

It may include:

* Normal trading days
* Holidays
* Special sessions
* Early closes
* Market closures

This is extremely important for monitoring.

Suppose:

```text
Expected Orders:
10,000/min
```

and:

```text
Current Orders:
0/min
```

You cannot immediately conclude:

> "Trading system is broken."

First ask:

> **Is the market supposed to be open?**

---

# 45. Time Zones

Global financial markets operate across different time zones.

For example:

```text
Asia
Europe
North America
```

A global trading ecosystem may therefore operate continuously across multiple market sessions.

This creates monitoring challenges.

A system may be:

```text
Active for Market A
Inactive for Market B
```

at the same time.

Therefore monitoring needs to understand the relevant market calendar and timezone.

---

# 46. Market Open and Market Close

Market open and market close can produce unusual workload patterns.

For example:

```text
Before Open
    ↓
Preparation

Market Open
    ↓
Large Order / Market Data Spike

Mid Session
    ↓
Different Activity Level

Market Close
    ↓
Potential Volume Spike

After Close
    ↓
Reduced / Different Activity
```

Therefore:

> **A static monitoring threshold is often insufficient for trading systems.**

---

# 47. Business Context and Monitoring

This is one of the most important lessons in this chapter.

Suppose monitoring says:

```text
Order volume dropped 50%
```

A technical monitoring system sees:

```text
Metric ↓
```

A business-aware monitoring system asks:

```text
Why?

Market open?
Market close?
Holiday?
Early close?
Normal intraday pattern?
System failure?
Exchange issue?
```

That is the difference between:

> **Metric-based monitoring**

and:

> **Context-aware monitoring.**

---

# 48. Example: A Trading Incident

Imagine this situation:

```text
09:30
Market Opens

09:31
Order volume increases 400%

09:32
OMS queue begins growing

09:33
CPU reaches 90%

09:34
Order latency increases

09:35
Orders begin timing out
```

From a business perspective:

```text
Market Activity
      ↓
Trading Volume Spike
      ↓
System Overload
      ↓
Order Delays
      ↓
Failed / Delayed Trading
```

From a monitoring perspective:

```text
Order Rate ↑
Queue Depth ↑
CPU ↑
Latency ↑
Timeouts ↑
```

From an SRE perspective:

```text
Early Signal
     ↓
Capacity Pressure
     ↓
Predictive Alert
     ↓
Preventive Action
```

This is exactly the type of connection we will build throughout this repository.

---

# 49. The Financial Market Mental Model

Remember:

```text
                 FINANCIAL MARKET
                        │
          ┌─────────────┼─────────────┐
          ▼             ▼             ▼
        BUYERS        SELLERS       VENUES
          │             │             │
          └─────────────┼─────────────┘
                        ▼
                     ORDERS
                        │
                        ▼
                    ORDER BOOK
                        │
                        ▼
                    MATCHING
                        │
                        ▼
                   EXECUTION
                        │
                        ▼
                      TRADE
                        │
                        ▼
               CLEARING / SETTLEMENT
```

At the same time:

```text
MARKET
  │
  ▼
MARKET DATA
  │
  ▼
TRADING SYSTEMS
  │
  ▼
OBSERVABILITY
  │
  ├── Metrics
  ├── Logs
  ├── Traces
  └── Events
```

---

# 50. What a Trading SRE Should Think About

Whenever you hear:

> "The market is behaving differently."

Think:

```text
Does system workload change?
```

Whenever you hear:

> "Trading volume increased."

Think:

```text
Can the system handle the increased throughput?
```

Whenever you hear:

> "The market is closed."

Think:

```text
Should alerts be suppressed or thresholds changed?
```

Whenever you hear:

> "Liquidity decreased."

Think:

```text
Could execution behavior or latency change?
```

Whenever you hear:

> "Market data is delayed."

Think:

```text
Are trading applications receiving stale information?
```

Whenever you hear:

> "Exchange connectivity failed."

Think:

```text
Can orders still reach the market?
```

This is the beginning of **business-aware SRE thinking**.

---

# 51. Quick Revision

| Concept             | Simple Meaning                                                        |
| ------------------- | --------------------------------------------------------------------- |
| Financial Market    | System where financial assets/contracts are traded                    |
| Market Participant  | Person or organization participating in a market                      |
| Primary Market      | Market where securities are issued                                    |
| Secondary Market    | Market where existing securities are traded                           |
| Equity Market       | Market for ownership interests such as stocks                         |
| Bond Market         | Market for debt instruments                                           |
| FX Market           | Market for currencies                                                 |
| Commodity Market    | Market related to commodities                                         |
| Derivatives Market  | Market for contracts whose value depends on an underlying             |
| Exchange            | Organized trading venue                                               |
| Trading Venue       | Place/system where trading occurs                                     |
| Broker              | Facilitates market access and transactions                            |
| Dealer              | May trade as principal and facilitate transactions                    |
| Market Maker        | Provides liquidity through buy/sell quotes                            |
| Order Book          | Collection of buy and sell orders/interest                            |
| Bid                 | Highest/current buying price offered                                  |
| Ask                 | Lowest/current selling price offered                                  |
| Spread              | Difference between ask and bid                                        |
| Market Depth        | Available buy/sell quantity across price levels                       |
| Matching            | Process of determining compatible orders                              |
| Price-Time Priority | Matching principle based on price and then time in applicable markets |
| Market Data         | Information about prices, quotes, trades, etc.                        |
| Trading Calendar    | Defines expected market trading days/sessions                         |
| Market Session      | Specific period during which trading activity occurs                  |

---

# 52. Final Memory Trick

Remember the financial market as a **city**.

Imagine a huge city where:

```text
People = Market Participants

Shops = Trading Venues

Products = Financial Assets

Buyers = Buy Orders

Sellers = Sell Orders

Shop Board = Order Book

Negotiation / Matching = Matching Engine

Purchase = Trade

Receipts = Trade Reports

Roads = Network Connectivity

News = Market Data

Traffic = Trading Volume

Rush Hour = Market Open / Close

Traffic Monitoring = System Monitoring
```

Now the whole thing becomes easier to visualize.

A financial market is essentially a sophisticated environment where:

> **Participants use infrastructure and rules to exchange financial assets, while technology continuously moves orders, market information, executions, and trade data between systems.**

And for an SRE:

> **Your job is to make sure that technology supporting this financial activity remains available, fast, reliable, observable, and predictable.**

---

# 53. What Comes Next?

Now that we understand what a financial market is, the next question is:

> **What exactly are people buying and selling in these markets?**

That brings us to:

```text
03-basic-asset-classes.md
```

There we will learn:

* Equities / Stocks
* Bonds / Fixed Income
* Cash
* Foreign Exchange
* Commodities
* Mutual Funds
* ETFs
* Futures
* Options
* Forwards
* Swaps
* Underlying assets

And for every asset class, we will connect:

```text
What is it?
      ↓
Why does it exist?
      ↓
How is it traded?
      ↓
Who trades it?
      ↓
What systems handle it?
      ↓
What can fail?
      ↓
What should an SRE monitor?
```
