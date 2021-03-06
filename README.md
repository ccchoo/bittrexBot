ALERT: BREAKING CHANGE INTRODUCED!!
---

If you're using docker, there are now 2 tagged versions of the bot.

__legacy__: use this tag if you want to continue to use the buyVolumePercent and sellVolumePercent configurations
__latest__: this tag is for the new volume model

Questions feel free to ask

Here is a blog entry that acts as a walkthrough for setting up the bot:

https://www.thecryptoaddiction.com/technical/2017/12/25/gfz3kshscew7mrfgywzjjro9h3nczc

# bittrexBot
This is an experimental bot for swing trading against the bittrex exchange. Set an upper and lower percentage that will place buy / sell orders. When an order triggers, the bracket will shift basing off the last order.

**This bot is designed to trade coins you currently have! At current, it does not support trying to trade on a currency of which you have a 0 balance.**

**Example: if you try to execute it on ZEC but have no ZEC coins, it will not work**

# DISCLAIMER

### I am not responsible for anything done with this bot. You use it at your own risk. There are no warranties or guarantees expressed or implied. You assume all responsibility and liability.

## Configuration

#### Note: An api key will need to be created with "Trade Limit" permissions

| Config Parameter  |  Type |  Default | Required  |  Description |
|---|---|---|---|---|
| apiKey  |  String |  none | Yes  |  API key for account access |
| apiSecret  | String  |  none |  Yes |  Secret key for account access |
| trade  | String  |  none | Yes | Base token used for exchange (example: BTC)  |
| currency | String  | none  |  Yes | Token to be traded (example: STRAT) |
| sellValuePercent  | Integer/Float  | 4  |  No | Difference in sell price of the previous successful order or market (on startup)  | 
| buyValuePercent  |  Integer/FLoat |  4 |  No | Difference in buy price of the previous successful order or market (on startup)   | 
| volumePercent  | Integer/Float  | 4  | No  |  Percent of your tokens to be placed leveraged | 
| buyDifference  |  Integer/Float |  0 |  No |  Percent adjustment to buy orders. Positive values buy more than sells. Negative values buys less than sell | 
| extCoinBalance | Integer/Float | 0| No | Off exchange balance|
| checkInterval | Integer | 30| No | How often the bot checks state|
| tradeAmount | Integer/Float | 0| No | Manually set token amounts for buy / sell **Overrides percent parameters**|
| initialSellPrice | Integer | 0 | No | Price set on first startup (example: 0.00095)|


*Specal Options*

* Leaving sell value percent out of the config will disable placement of sell orders. Buys will still place and when they trigger, the bracket will still shift.
* Leaving buy value percent out of the config will disable placement of buy orders. Sells will still place and when they trigger, the bracket will still shift.

The percentage values are actual percentages...not decimals. So if you want to trade 3.25% you would input 3.25 in that value. I would also not recommend going below 10 seconds for the checkInterval. Otherwise, it's possible to induce a race condition with bittrex.

## buyDifference explanation

In my opinion, the previous configuration model with individual buyVolume and sellVolume parameters ended up being fairly unpredictable. If the price went up, so did the amount the bot would end up buying. If it went down, it ended up selling more. The model was unsustainable over a long period of time and required a LOT of rebasing. 

To resolve this problem, I've done away with the individual settings for a global setting. In short, setting the 'volumePercent' param with a 'buyDifference' of zero places matching buys / sells. Adjusting the 'buyDifference' changes only the buying behavior. It's been much more predictable and less prone to erroneous buys / sells.

To be transparent, here's the forumla being used to calculate the buy amount:

(balance + externalCoinBalance) * volumePercent * (1/(1 - volumePercent) * 1 + buyDifference)

#### Example file 

```json
{
  "apiKey": "34234898u9rghk",
  "apiSecret": "238ryfiuahskuqh4ri",
  "trade": "BTC",
  "currency": "WAVES",
  "sellValuePercent": 4,
  "buyValuePercent": 7,
  "volumePercent": 3,
  "buyDifference": 0,
  "extCoinBalance": 0,
  "checkInterval": 30,
  "initialSellValue": 0
}
```
__the config file MUST be named botConfig.json__

## Usage
The bot is designed to trade a single token at a time. It's recommended to run it in the docker container. 
Docker will need to be installed prior to trying to run this. To install Docker, see their installation guide:
https://docs.docker.com/engine/installation/ 
The docker image can be found at __jufkes/bittrexBot__

<b><font color="red"> ALERT: BREAKING CHANGE INTRODUCED!! </font></b>

If you're using docker, there are now 2 tagged versions of the bot. 

* legacy: use this tag if you want to continue to use the buyVolumePercent and sellVolumePercent configurations
* latest: this tag is for the new volume model

To run:
docker run -d --name <name> -v /path/to/directory_containing_config_file:/opt/bittrexBot/config jufkes/bittrexbot:latest

Example:
docker run -d --name waves -v /opt/botdefs/waves:/opt/bittrexBot/config --restart always jufkes/bittrexbot:latest

-- The waves directory, in this case, contains the botConfig.json

## Utilities
Bots run without your intervention. It is recommended that you have a means to track your trades ergo, track the trades the bot is making for you. That is the same for this bot as well as any other bots you may try.

I track my trades using [CryptoNotify](http://cryptonotify.com). This tool can be setup to email executed trades or, as I prefer, send a message to a Slack channel.

## Donations
If you'd like to donate a little BTC as a thank you, you can do so here: 19h1hgS2iSBReUaEo8oVkxkQdUDGwSuKCH

## License
Code released under the [MIT License](https://github.com/jufkes/bittrexBot/master/LICENSE).
