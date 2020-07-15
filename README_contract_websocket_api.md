# Perpetual Contract
Below info is for Perpetual Contracts V3 WebscoketAPI
-
# Brief Introduction 
WebSocket is a HTML5 new Protocol. It implements full-duplex communication between the client and the server, allowing data to be quickly propagated in both directions. A simple handshake can establish a connection between the client and the server, and the server can actively push information to the client according to business rules. The advantages are as follows:
- When the client and server are transmitting data, the request header information is relatively small, about 2 bytes.
- Both client and server can actively send data to each other.
- In order to save bandwidth and server resource, it is not necessary to create and cancel TCP request in multiple times.
### Strongly recommend developer to use WebSocket API to obtain market info and bid/ask market depth info etc. 
Address：wss://csocketapi.bitget.com/ws/v1
> 
Connection instruction 
> 
System will auto-disconnect while subscription has been done within 30sec or no ping command sent by user after 30sec after ws is connected 

# Order format
### Request format
> {"op": "`<value>`", "args": ["`<value1>`","`<value2>`"]}
- op value is: 1--subscribe ； 2-- unsubscribe；3--login 
- args: value is channel name, can define 1 or more channel 

### Successful response format
> {"event": "`<value>`","channel":"`<value>`"}
> 
> {"table":"channel","data":"[{"`<value1>`","`<value2>`"}]"}
> 
### Failed response format
> {"event":"error","message":"<error_message>","errorCode":"<errorCode>"}
# Trading pair format 
### Reverse contract (example)
> btcusd、ethusd、ltcusd、xrpusd、bchusd、eosusd
> 
### Forward contract (example)
> cmt_bnbusdt、cmt_btcusdt
> 
### Simulation contract (example)
> sbtcusd（Reverse contract simulation）、cmt_btcsusdt（Forward contract simulation）


# Subscribe
User has the opinion to subscribe 1 or more channels, total length of multiple channel can not exceeds 4096 bytes. 
> {"op": "subscribe", "args": ["`<SubscriptionTopic>`"]}
> 
Note: op value is subscribe
> 
args array content is channel name: <channelname>:<filter>
> 
channelname is consist of business/channel
> 
Perpetual push business is swap, channel is the particular name under such business, if the channel name can not be differentiate by alphabet, then they can use " _ " to connect. 
> 
### Example
> "swap/ticker:btcusd" or "swap/price_range:btcusd"
> 
filter is filterable data, refer to channel for detailed instruction 
> 
### Example
> send:
> {"op": "subscribe", "args": ["swap/ticker:btcusd", "swap/candle60s:btcusd"]}
> 
### response:
> {"event": subscribe,"channel":"swap/ticker:btcusd"}
> {"event": subscribe,"channel":"swap/candle60s:btcusd"}
> 
> {"table":"swap/ticker","data":[{"high_24h":"3369","instrument_id":"btcusd","last":"3299",
 "low_24h":"3112","timestamp":"2018-12-09T08:12:04.659Z","volume_24h":"8389225"}]}
 
> {"table":"swap/candle60s","data":[{"instrument_id":"btcusd","candle":["2018-12-09T08:12:00.000Z",
 "3299.8","3299.8","3299","3299","82","2.4854"]}]}
>

# Cancel subscription 

Can cancel 1 or more channels 
> {"op": "unsubscribe", "args": [`<SubscriptionTopic>`]}
> 
### Example
> 
#### Request
> {"op": "unsubscribe", "args": ["swap/ticker:btcusd", "swap/candle60s:btcusd"]}
> 
#### Return
>
> {"event":"unsubscribe","channel":"swap/ticker:btcusd"}
> {"event":"unsubscribe","channel":"swap/candle60s:btcusd"}
>

# Login
Signature method instruction can refer to authentication part of API brief introduction  
### Login subscription format：
> {"op":"login","args":["`<access_key>`","`<timestamp>`","`<sign>`"]}
> 
### Response
> {"event":"login","success":"true"}
> 
### Example
> {"op":"login","args":["985d5b66-57ce-40fb-b714-afc0b9787083","1538054050975","7L+zFQ+CEgGu5rzCj4+BdV2/uUHGqddA9pI6ztsRRPs="]}
> 
**access_key**:is ACCESS_KEY for user
> 
**timestamp**: Timestamp（req_time）Your request sending time (
13-bit timestamp, take an integer)
> 
**sign**: Agreement based on Hash for user signature calculation, it uses the value of encryption result from SHA1’s SecreKey and requested parameter  

> 
**sign example**: 
[api example](https://github.com/BitgetLimited/API_Docs_en/wiki/REST_authentication)
> 
Failed login will cause auto disconnection 
-
# Connection limit
> 
**Connection limit**：1 time/s 
> 
**Subscription limit**：240 times/hr
> 
If no data returned after connecting to ws, connection will auto-disconnect after 30s, suggest user to follow below instruction: 
> 
1. Set a timer for 20 sec after every message received
> 
2. Sending string ‘ping’ is timer is triggered (no new message received after 20 sec)
> 
3. Expect a literal string ‘pong’ as a response. Send out error or re-connect if nothing is received after 20 sec

# Channel description 
> 
**Channels are not required to log in**: Market channel, Candlestick chart channel, Trading data channel, Fund fees channel, Price limit range channel, Market depth data channel, Price tag channel.

> 
**Channels are required to log in**：User account channel, User trade channel, User position channel

> 
**Name of Channels are not required to log in**：
> 
swap/ticker //  ticker data channel
> 
swap/candle60s //  1min Candlestick chart data channel
> 
swap/candle300s //  5min Candlestick chart data channel
> 
swap/candle900s //  15min Candlestick chart data channel
> 
swap/candle1800s // 30min Candlestick chart data channel
> 
swap/candle3600s // 1h Candlestick chart data channel
> 
swap/candle14400s // 4h Candlestick chart data channel
> 
swap/candle43200s // 12h Candlestick chart data channel
> 
swap/candle86400s // 1day Candlestick chart data channel
> 
swap/candle604800s // 1week Candlestick chart data channel
> 
swap/trade // Trade info channel
> 
swap/funding_rate// Fund fees channel
> 
swap/price_range// Price limit range channel
> 
swap/depth // Market depth data channel,first 200 range, subsequent one is increment
> 
swap/depth5 // Market depth data channel, return to first 5 range everytime
> 
swap/mark_price// Price tag channel
> 
**Channels are required for login**：
> 
swap/account // User account info channel
> 
swap/position // User position info channel
> 
swap/order // User trade data channel
> 

# User position channel
**Require user to login to obtain user position info**
### Send example
> {"op": "subscribe", "args": ["swap/position:btcusd"]}
> 
swap / position is channel name, btcusd is instrument_id
### # Return parameter
Parameter name | Parameter type | Description |
----------- | ----------- | ----------- |
liquidation_price | String | Estimated stopped-out price
position | String |  Position cont
avail_position | String  |Close position cont
avg_cost | String   |Open position avg price
instrument_id | String  | Contract name
leverage | String |  Leveraging 
realized_pnl | String |Realized P/L
unrealized_pnl | String  |Unrealized P/L
side | String  | Direction (long/short)
timestamp | String  |Creation time
margin | String  |Margin 
margin_mode | String  |fixed: Isolated margin crossed: Cross margin
### # Return example
> {<br>
    "table": "swap/position",<br>
    "data": [{<br>
        "holding": [{<br>
            "avail_position": "1",<br>
            "avg_cost": "0.2985",<br>
            "leverage": "5",<br>
            "liquidation_price": "0.0136",<br>
            "margin": "6.8129",<br>
            "position": "1",<br>
            "realized_pnl": "-0.0239",<br>
            "unrealized_pnl": "0.0001",<br>
            "side": "long",<br>
            "timestamp": "1559544244016"<br>
        }, {<br>
            "avail_position": "1",<br>
            "avg_cost": "0.2935",<br>
            "leverage": "5",<br>
            "liquidation_price": "0.0136",<br>
            "margin": "6.8129",<br>
            "position": "1",<br>
            "realized_pnl": "-0.0239",<br>
            "unrealized_pnl": "0.0001",<br>
            "side": "short",<br>
            "timestamp": "1559544244016"<br>
        }],<br>
        "instrument_id": "xrpusd",<br>
        "margin_mode": "crossed"<br>
    }]<br>
}<br>

# User account channel 
Need user to log in to obtain account info
### Send example 
> {"op": "subscribe", "args": ["swap/account:btcusd"]}

swap/account is channel name, btcusd is instrument id
### # Return parameter

Parameter name | Parameter type | Description |
----------- | ----------- | ----------- |
equity | String  | Account privilege 
instrument_id | String  | Contract name
margin | String  | Used margin
margin_frozen | String  | Freeze margin for open a position 
realized_pnl | String  | Realized P/L
timestamp | String  | Creation time
total_avail_balance | String  | Account balance
unrealized_pnl | String  | Unrealized P/L
fixed_balance | String  | Isolated margin account balance
margin_mode | String  | Account type: Isolated margin fixed Cross margin crossed |
### # Return example 
> {<br>
    "table": "swap/account",<br>
    "data": [{<br>
        "equity": "21.9334",<br>
        "fixed_balance": "0.0000",<br>
        "instrument_id": "xrpusd",<br>
        "margin": "6.8129",<br>
        "margin_frozen": "6.8143",<br>
        "margin_mode": "crossed",<br>
        "realized_pnl": "0.0000",<br>
        "timestamp": "1559544244016",<br>
        "total_avail_balance": "22.0043",<br>
        "unrealized_pnl": "-0.0710"<br>
    }]<br>
}<br>

# User trade channel 
Need user to log in to obtain user trade data
### Send example 
> {"op": "subscribe", "args": ["swap/order:btcusd"]}

swap/account is channel name, btcusd is instrument id
### # Return example 
Parameter name | Parameter type | Description |
----------- | ----------- | ----------- |
instrument_id | String  | Contract name, e.g. btcusd
size | String  | Order cont
timestamp | String  | Creation time 
filled_qty | String  | Deal cont
fee | String  | Fees 
order_id | String  | Order ID
client_oid | String  | Order ID set by user
price | String  | Order price 
price_avg | String  | Deal avg price 
type | String  | 1:Open long position 2:Open short position 3:Close long position 4.Close short position 
contract_val | String  | Contract value 
order_type | String  |0:Plain order 1:Only Maker(Post only) 2: Fill or Kill (FOK) 3:Deal immediately and cancel the rests (IOC)
last_fill_time | String  | Last deal time
state | String  | Order status(“1”: New order, “2”Partial order, “3”:Completely deal,”5”:Cancel,”6”:Cancal by risk triggered)
### # Return example 
> {<br>
    "table": "swap/order",<br>
    "data": [{<br>
        "last_fill_time": "1559544244016",<br>
        "filled_qty": "0",<br>
        "fee": "0.000000",<br>
        "client_oid": "",<br>
        "last_fill_qty": "0",<br>
        "price_avg": "0.0000",<br>
        "type": "2",<br>
        "instrument_id": "xrpusd",<br>
        "last_fill_px": "0",<br>
        "size": "1",<br>
        "price": "0.2935",<br>
        "error_code": "0",<br>
        "contract_val": "10",<br>
        "state": "0",<br>
        "order_id": "6c-a-625f6f638-0",<br>
        "order_type": "0",<br>
        "status": "0",<br>
        "timestamp": "1559544244016"<br>
    }]<br>
}<br>

# Public-Ticker channel 
Obtain all perpetual contracts latest market price, best bidding price, best asking price and 24h trading volume 
### Send example 
> {"op": "subscribe", "args": ["swap/ticker:btcusd"]}

swap/account is channel name, btcusd is instrument id
### # Return parameter

Parameter name | Parameter type | Description |
----------- | ----------- | ----------- |
instrument_id | String  | Contact name, e.g. btcusd
best_bid | String  | Best bidding price
best_ask | String  | Best asking price
last | String  | Last deal price 
high_24h | String  | Highest price in 24h
low_24h | String  | Lowest price in 24h
volume_24h | String  | 24h volume 
timestamp | String  | System timestamp 
### # Return example 
> {<br>
    "table": "swap/ticker",<br>
    "data": [{<br>
        "best_ask": "5603.5",<br>
        "best_bid": "5600.1",<br>
        "high_24h": "5773.7",<br>
        "instrument_id": "btcusd",<br>
        "last": "5603.3",<br>
        "low_24h": "5566",<br>
        "timestamp": "1559544244016",<br>
        "volume_24h": "1538076"<br>
    }]<br>
}<br>

# Public- Candlestick chart
**Public-cand Channel Public-cand**

**Obtain contract Candlestick chart data**

**Channel list**：

swap/candle60s //  1min Candlestick chart data channel

swap/candle300s //  5min Candlestick chart data channel

swap/candle900s //  15min Candlestick chart data channel

swap/candle1800s //  30min Candlestick chart data channel

swap/candle3600s //  1h Candlestick chart data channel

swap/candle14400s //  4h Candlestick chart data channel

swap/candle43200s //  12h Candlestick chart data channel

swap/candle86400s // 1day Candlestick chart data channel

swap/candle604800s //  1week Candlestick chart data channel
### Send example 
> {"op": "subscribe", "args":["swap/candle60s:btcusd"]}

swap/account is channel name, btcusd is instrument id
### # Return parameter

Parameter name | Parameter type | Description |
----------- | ----------- | ----------- |
timestamp | String  | Start time
open | String  | Opening price 
high | String  | Highest price 
low | String  | Lowest price 
close | String  | Closing price 
volume | String  | Trading volume (cont)
instrument_id | String  | Contract btcusd
### # Return example 
> {<br>
    "table": "swap/candle60s",<br>
    "data": [{<br>
        "instrument_id": "btcusd",<br>
        "candle": ["1559544244016", "5613", "5613", "5611.9", "5611.9", "1218", "21.7009"]<br>
    }]<br>
}<br>

# Public- Trading channel 
**Obtain recent trading data**
### Send example 
> {"op": "subscribe", "args": ["swap/trade:btcusd"]}

swap/account is channel name, btcusd is instrument id
### # Return parameter

Parameter name | Parameter type | Description |
----------- | ----------- | ----------- |
price | String  | Deal price
size | String  | Deal QTY
side | String  | Direction (buy or sell)
timestamp | String  | Deal time
instrument_id | String | btcusd |
### # Return example
> {<br>
     "table": "swap/trade",<br>
    "data": [{<br>
        "instrument_id": "btcusd",<br>
        "price": "5611.9",<br>
        "side": "buy",<br>
        "size": "2",<br>
        "timestamp": "1559544244016",<br>
    }]<br>
}<br>

# Public – Fund fees channel 
**Obtain contract fees**
### Send example 
> {"op": "subscribe", "args": ["swap/funding_rate:btcusd"]}

swap/account is channel name, btcusd is instrument id
### # Return parameter

Parameter name | Parameter type | Description |
----------- | ----------- | ----------- |
instrument_id | String  | Contact name, e.g. btcusd
funding_time | String  | Next settlement time
funding_rate | String  | Current fees
### # Return example 
> {<br>
    "table": "swap/funding_rate",<br>
    "data": [{<br>
        "funding_rate": "-0.00067",<br>
        "funding_time": "1559544244016",<br>
        "instrument_id": "btcusd",<br>
    }]<br>
}<br>

# Public - Price limit channel
**Obtain the best bidding and asking price for contact current position**
### Send example 
> {"op": "subscribe", "args": ["swap/price_range:btcusd"]}

swap/account is channel name, btcusd is instrument id
### # Return parameter

Parameter name | Parameter type | Description |
----------- | ----------- | ----------- |
timestamp | String  | System timestamp 
lowest | String  | Best asking price
instrument_id | String  | Contract name, e.g. btcusd
highest | String  | Best bidding price
### #  Return example 
> {<br>
    "table": "swap/price_range",<br>
    "data": [{<br>
        "highest": "5665.9",<br>
        "instrument_id": "btcusd",<br>
        "lowest": "5553.6",<br>
        "timestamp": "1559544244016"<br>
    }]<br>
}<br>

# Public – Market depth 5 channel 
**Return first 5 range market depth data every time** 
### Send example 
> {"op": "subscribe", "args": ["swap/depth5:btcusd"]}

swap/account is channel name, btcusd is instrument id
### # Return parameter

Parameter name | Parameter type | Description |
----------- | ----------- | ----------- |
asks | String  | Asking market depth
bids | String  | Bidding market depth
timestamp | String  | Timestamp 
instrument_id | String  | Contract ID
["411.8","6"] [String,String]  411.8 is market depth price, 10 is QTY of this price
### # Return example 
> {<br>
    "table": "swap/depth5",<br>
    "data": [{<br>
        "asks": [<br>
            ["5621.7", "58"],<br>
            ["5621.8", "125"],<br>
            ["5622", "84"],<br>
            ["5622.5", "6"],<br>
            ["5623", "1"]<br>
        ],<br>
        "bids": [<br>
            ["5621.3", "287"],<br>
            ["5621.2", "41"],<br>
            ["5621.1", "2"],<br>
            ["5621", "26"],<br>
            ["5620.9", "640"]<br>
        ],<br>
        "instrument_id": "btcusd",<br>
        "timestamp": "1559544244016"<br>
    }]<br>
}<br>

# Public- Market depth channel 
**Return first 200 range for the first time, subsequent one is increment**
### Send example
> {"op": "subscribe", "args": ["swap/depth:btcusd"]}

swap/account is channel name, btcusd is instrument id
### # Return parameter

Parameter name | Parameter type | Description |
----------- | ----------- | ----------- |
asks | String  | Asking market depth
bids | String  | Bidding market depth
action | String  | Full amount increment tag
timestamp | String  | Timestamp 
instrument_id | String  | Contract ID
["411.8","6"] [String,String] 411.8 is market depth price, 10 is QTY of this price

### # Return example 
> <br> First 200 range <br> <br>
{<br>
    "table": "swap/depth",<br>
    "action": "(partial/update)",<br>
    "data": [{<br>
        "asks": [<br>
            ["5621.7", "58"],<br>
            ["5621.8", "125"],<br>
            ["5622", "84"],<br>
            ["5622.5", "6"],<br>
            ["5623", "1"]<br>
        ],<br>
        "bids": [<br>
            ["5621.3", "287"],<br>
            ["5621.2", "41"],<br>
            ["5621.1", "2"],<br>
            ["5621", "26"],<br>
            ["5620.9", "640"]<br>
        ],<br>
        "instrument_id": "btcusd",<br>
        "timestamp": "1559544244016"<br>
    }]<br>
}<br>

# Public – Price tag channel 
**Obtain price tag**
### Send example 
> {"op": "subscribe", "args": ["swap/mark_price:btcusd"]}

swap/account is channel name, btcusd is instrument id
### # Return parameter

Parameter name | Parameter type | Description |
----------- | ----------- | ----------- |
instrument_id | String  | Contact name, e.g. btcusd
mark_price | String  | Tag price
timestamp | String | System timestamp 
### # Return example 
> {<br>
    "table": "swap/mark_price",<br>
    "data": [{<br>
        "instrument_id": "btcusd",<br>
        "mark_price": "5620.9",<br>
        "timestamp": "1559544244016"<br>
    }]<br>
}<br>

