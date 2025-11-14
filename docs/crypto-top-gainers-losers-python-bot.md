[

API

](/learn/category/api)

  TABLE OF CONTENTS

# How to Trade Crypto Top Gainers and Losers with a Python Bot

 4.2



| by

  [Cryptomaton](https://www.coingecko.com/author/Cryptomaton)  |

Edited by

  [Julia Ng](https://www.coingecko.com/author/juliang)     - Updated September 28 2025

A trading bot refers to a set of computer programs designed to execute trades in financial markets automatically, based on pre-defined criteria. These criteria can include market conditions, technical indicators, or any other dataset.

In this guide, we’re going to build a trading bot using the **[CoinGecko API](https://www.coingecko.com/en/api)**, designed to capitalize on the top crypto gainers and losers, thus taking advantage of cryptocurrency volatility in either direction.

As always, you’ll find a link to the GitHub repository at the end of the article, allowing you to dive right in and experiment with the tool. Let's read on!

* * *

![Python bot for crypto top gainers](https://assets.coingecko.com/coingecko/public/ckeditor_assets/pictures/22102/content_Develop_a_top_gainers___losers_trading_bot_-_python_crypto_api_guide.webp)

## Prerequisites

Before we can start building our trading bot, we need the following tools:

* Python 3.10+
* An IDE
* A CoinGecko API Key
* A Binance API Key

💡**Tip:** Crypto top gainers and losers is a paid exclusive endpoint. Sign up now for a [paid plan](https://www.coingecko.com/api/pricing)!

## Step 1. Generate API Keys

### CoinGecko API

To obtain a CoinGecko API key, head over to the [Developer’s Dashboard](https://www.coingecko.com/en/developers/dashboard) and click on **+Add New Key** in the top right corner. Follow this guide on how to generate and [set up your API key](http://docs.coingecko.com/reference/setting-up-your-api-key).

For this bot, we’ll be using CoinGecko’s [Top Gainers & Losers](https://docs.coingecko.com/reference/coins-top-gainers-losers) endpoint, available on the Analyst tier and above. Alternatively, you can use the [Coins List with Market Data](https://docs.coingecko.com/reference/coins-markets) endpoint, which is accessible for free on the Demo plan. You may need to do some additional sorting and filtering yourself, but it’s a viable free alternative.

[![Coinmarketcap API vs CoinGecko API](https://assets.coingecko.com/coingecko/public/ckeditor_assets/pictures/22118/content_CoinGecko_Sign_Up_CTA_%288%29.webp)](https://www.coingecko.com/en/api/pricing)

### Binance API

[Binance](https://www.coingecko.com/en/exchanges/binance) has relatively large number of available cryptocurrencies and a robust trading API making it a good choice for our bot. If you prefer to use a different exchange, you’re welcome to do so, but keep in mind that you’ll need to rewrite the exchange service, as this guide uses a Binance API wrapper.

Head over to your account and find the API Management setting under your profile:

![Binance API Management](https://assets.coingecko.com/coingecko/public/ckeditor_assets/pictures/22085/content_API_MANAGEMENT.webp)

Under API Management, hit **Create API**, choose System Generated and then hit next. Once you’ve completed the security checks, you should see something like this:

![Binance API Key Example](https://assets.coingecko.com/coingecko/public/ckeditor_assets/pictures/22086/content_Binance_API_Key_Example.webp)

Go ahead and save your API Key and Secret Key for now, as this is the only time Binance will display the Secret Key. If you’ve refreshed the page before saving it, simply delete this Key and create another one.

With your API credentials saved, it’s time to give your key the correct API restrictions. This tells your key what it can and cannot do and it’s important to only give the minimum amount of permissions necessary for the bot to run.

Since we want our bot to place trades on our behalf, hit **Edit Restrictions** and tick **Enable Spot & Margin Trading**. This is the only required permission alongside **Enable Reading**, so avoid ticking dangerous permissions such as Enable Withdrawals.

For additional security, it’s best to restrict the scope of the API Key to your IP Address only. Under IP access restrictions select Restrict access to trusted IPs only and paste in your IP address.

## Step 2. Configure your Environment

Let’s go ahead and configure our Python application. We’ll start by creating a virtual environment and installing all the project dependencies.

### Installing requirements

In an empty directory, go ahead and run the following commands:

\# Create venv

python -m venv env

\# Activate env on macOS/Linux

source env/bin/activate # For macOS/Linux

\# activate env on Windows

env\\scripts\\activate

 [view raw](https://gist.github.com/CyberPunkMetalHead/223d7d90b93d236c049fad4707c2102a/raw/aa664451c01f273d47e396822fed5eab7e2b6fc7/work.bat)   [work.bat](https://gist.github.com/CyberPunkMetalHead/223d7d90b93d236c049fad4707c2102a#file-work-bat) hosted with ❤ by [GitHub](https://github.com)

With your virtual environment activated, go ahead and install the following requirements. The easiest way to do this is by copying the file below to your root directory in a file called **requirements.txt** and then running **pip install -r requirements.txt**.

aiohappyeyeballs\==2.4.4

aiohttp\==3.11.11

aiosignal\==1.3.2

attrs\==24.3.0

certifi\==2024.12.14

charset-normalizer\==3.4.1

dataclass-wizard\==0.34.0

dateparser\==1.2.0

frozenlist\==1.5.0

idna\==3.10

multidict\==6.1.0

propcache\==0.2.1

pycryptodome\==3.21.0

python-binance\==1.0.27

python-dateutil\==2.9.0.post0

python-dotenv\==1.0.1

pytz\==2024.2

regex\==2024.11.6

requests\==2.32.3

six\==1.17.0

typing\_extensions\==4.12.2

tzdata\==2024.2

tzlocal\==5.2

urllib3\==2.3.0

websockets\==14.1

yarl\==1.18.3

 [view raw](https://gist.github.com/CyberPunkMetalHead/0470943d48888e5994bc8b9361044b79/raw/727d01ff777f31caa963c23e67ec8e633ca89a93/requirements.txt)   [requirements.txt](https://gist.github.com/CyberPunkMetalHead/0470943d48888e5994bc8b9361044b79#file-requirements-txt) hosted with ❤ by [GitHub](https://github.com)

### Create the project structure

Go ahead and create the directory structure for your project. Your initial setup should look like this:

![Project Structure](https://assets.coingecko.com/coingecko/public/ckeditor_assets/pictures/22087/content_structure.webp)

### Define Credentials and Config Settings

Inside your empty **.env** file go ahead and drop your CoinGecko and Binance API keys in the following variables:

CG\_API\_KEY \= "COIN\_GECKO\_API\_KEY"

BINANCE\_API\_KEY \= "BINANCE\_API\_KEY"

BINANCE\_API\_SECRET \= "BINANCE\_API\_SECRET"

 [view raw](https://gist.github.com/CyberPunkMetalHead/0470943d48888e5994bc8b9361044b79/raw/727d01ff777f31caa963c23e67ec8e633ca89a93/.env)   [.env](https://gist.github.com/CyberPunkMetalHead/0470943d48888e5994bc8b9361044b79#file-env) hosted with ❤ by [GitHub](https://github.com)

We’ll use these variables to safely import our API credentials into our application later. Now let’s configure our bot settings. Our trading bot will place orders based on the instructions we give it in **config.json**.

Here is where we’re going to define options such as the amount it should spend per trade, as well as other settings. Let’s define the following settings inside **config.json**:

{

 "amount": 20,

 "stop\_loss": 5,

 "take\_profit": 12,

 "bot\_frequency\_in\_seconds": 10,

 "vs\_currency": "USDT",

 "number\_of\_assets": 5,

 "mode" : "GAINING"

}

 [view raw](https://gist.github.com/CyberPunkMetalHead/0470943d48888e5994bc8b9361044b79/raw/727d01ff777f31caa963c23e67ec8e633ca89a93/config.json)   [config.json](https://gist.github.com/CyberPunkMetalHead/0470943d48888e5994bc8b9361044b79#file-config-json) hosted with ❤ by [GitHub](https://github.com)

For what each of the options mean, see the breakdown below (feel free to skip ahead for now and revisit this section just before running your bot).

* **amount**: The amount the bot will spend on each trade, specified in the vs\_currency. For instance, if vs\_currency is set to USDT, the bot will place trades worth 20 USDT for each asset.
* **stop\_loss**: The percentage at which the bot will close a trade if the price drops to avoid further losses (i.e: 10 for a 10% loss).
* **take\_profit**: The percentage at which the bot will close a trade to lock in gains.
* **bot\_frequency\_in\_seconds**: How often the bot will check for new trading opportunities, in seconds.
* **vs\_currency**: The currency the bot will use for trading.
* **number\_of\_assets**: The maximum number of top assets the bot will buy.
* **mode**: set to "GAINING" to trade top gainers or "LOSING" to focus on top losers.

## Step 3. Build Utilities

In this bot’s context, utilities are a set of helper functions that help us load our API Keys, Configuration, and define a logger.

Inside the **utils** directory, let’s start by creating an empty **load\_env.py** file. Next, let’s define variables for our credentials:

import os

from dotenv import load\_dotenv

load\_dotenv()

cg\_api\_key \= os.getenv("CG\_API\_KEY")

binance\_api\_key \= os.getenv("BINANCE\_API\_KEY")

binance\_api\_secret \= os.getenv("BINANCE\_API\_secret")

 [view raw](https://gist.github.com/CyberPunkMetalHead/0470943d48888e5994bc8b9361044b79/raw/727d01ff777f31caa963c23e67ec8e633ca89a93/load_env.py)   [load\_env.py](https://gist.github.com/CyberPunkMetalHead/0470943d48888e5994bc8b9361044b79#file-load_env-py) hosted with ❤ by [GitHub](https://github.com)

We can now simply import these 3 variables in other parts for our code, to safely get access to our API Keys.

Inside the utils directory, create another empty file called **load\_config.py**. Inside this file, we’re going to define a function called **load\_config()** that returns an object of type **Config**.

import json

from models.config import Config

def load\_config() \-> Config:

 with open("config.json", "r") as file:

 return Config.from\_dict(json.load(file))

config \= load\_config()

 [view raw](https://gist.github.com/CyberPunkMetalHead/0470943d48888e5994bc8b9361044b79/raw/727d01ff777f31caa963c23e67ec8e633ca89a93/load_config.py)   [load\_config.py](https://gist.github.com/CyberPunkMetalHead/0470943d48888e5994bc8b9361044b79#file-load_config-py) hosted with ❤ by [GitHub](https://github.com)

Don’t worry if the IDE warns that the Config object doesn’t exist, we’ll define this in the next step, after configuring our logger.

Next, go ahead and create another file called logger.py, under the same utils directory. Here, we will configure our application logger as follows:

import logging

from datetime import datetime

logging.basicConfig(

 filename\=f"logs/{datetime.today().strftime('%Y-%m-%d')}\_log.log",

 filemode\="a",

 format\="%(asctime)s,%(msecs)d %(name)s %(levelname)s %(message)s",

 datefmt\="%H:%M:%S",

 level\=logging.INFO,

)

 [view raw](https://gist.github.com/CyberPunkMetalHead/0470943d48888e5994bc8b9361044b79/raw/727d01ff777f31caa963c23e67ec8e633ca89a93/logger.py)   [logger.py](https://gist.github.com/CyberPunkMetalHead/0470943d48888e5994bc8b9361044b79#file-logger-py) hosted with ❤ by [GitHub](https://github.com)

The configuration above will save our logs to the following file format: 2025-01-30\_log.log. This will generate a new log file every day. Another setting to keep in mind is the log level. To enable lower severity logging, you may set this to **logging.DEBUG** - however, bear in mind that this option is quite noisy.

## Step 4. Define Models/Objects

This is where we define the shape of the objects that we’ll be working with throughout our application.

### Config Object

Inside the **models** directory, create a new file called **config.py**. Inside, we define a **Config** class.

from dataclasses import dataclass

from typing import Literal

from dataclass\_wizard import JSONWizard

@dataclass

class Config(JSONWizard):

 amount: int

 stop\_loss: int

 take\_profit: int

 bot\_frequency\_in\_seconds: int

 vs\_currency: str

 number\_of\_assets: int

 mode: Literal\["GAINING", "LOSING"\]

 [view raw](https://gist.github.com/CyberPunkMetalHead/0470943d48888e5994bc8b9361044b79/raw/727d01ff777f31caa963c23e67ec8e633ca89a93/config.py)   [config.py](https://gist.github.com/CyberPunkMetalHead/0470943d48888e5994bc8b9361044b79#file-config-py) hosted with ❤ by [GitHub](https://github.com)

Note the **@dataclass** decorator - this simplifies the process of creating classes that are primarily used to store data and reducing the amount of boilerplate code that we have to write.

We’re also using an external library called **JSONWizard** class, which makes it easy to serialize and deserialize JSON data. It also enables dot notation in our code and type hints in our IDE.

### Currencies Enum

Next, let’s create a **currencies.py** file. When requesting coin data from CoinGecko, we need to specify the **vs\_currency** parameter, which determines the currency in which prices and other data are returned.

from enum import Enum

class Currency(Enum):

 USD \= "usd"

 BTC \= "btc"

 ETH \= "eth"

 LTC \= "ltc"

 BCH \= "bch"

 BNB \= "bnb"

 \# \[...\] more options

 [view raw](https://gist.github.com/CyberPunkMetalHead/0470943d48888e5994bc8b9361044b79/raw/727d01ff777f31caa963c23e67ec8e633ca89a93/currencies.py)   [currencies.py](https://gist.github.com/CyberPunkMetalHead/0470943d48888e5994bc8b9361044b79#file-currencies-py) hosted with ❤ by [GitHub](https://github.com)

This is sufficient for our use case since we’re only going to be working with USD, however for more complex requirements, you may call the CoinGecko API's [Supported Currencies List](https://docs.coingecko.com/reference/simple-supported-currencies) endpoint for a complete list of supported currencies and save the response.

### Gainers and Losers Object

In the same models directory, let’s define the shape of the **GainersAndLosers** object from the CoinGecko API response inside a new file called **currency\_performance.py**. CoinGecko’s Top Gainers and Losers endpoint returns an object that consists of two main child objects: **top\_gainers** and **top\_losers**.

Each category contains a list of cryptocurrencies that have either gained or lost the most in terms of price over a given period of time.

To represent this structure, we define two types:

1. **CurrencyPerformance:** This represents the data for each individual cryptocurrency.
2. **GainersAndLosers**: This is a container object that holds two lists - one for the top gainers and one for the top losers. Each list is made up of CurrencyPerformance objects.

from dataclasses import dataclass

from typing import List

from dataclass\_wizard import JSONWizard

@dataclass

class CurrencyPerformance:

 id: str

 symbol: str

 name: str

 image: str

 market\_cap\_rank: int

 usd: float

 usd\_24h\_vol: float

 usd\_24h\_change: float  \# actual percentage change

@dataclass

class GainersAndLosers(JSONWizard):

 top\_gainers: List\[CurrencyPerformance\]

 top\_losers: List\[CurrencyPerformance\]

 [view raw](https://gist.github.com/CyberPunkMetalHead/0470943d48888e5994bc8b9361044b79/raw/727d01ff777f31caa963c23e67ec8e633ca89a93/currency_performance.py)   [currency\_performance.py](https://gist.github.com/CyberPunkMetalHead/0470943d48888e5994bc8b9361044b79#file-currency_performance-py) hosted with ❤ by [GitHub](https://github.com)

Note that for this trading bot we’re working with a 24h price change, so we’ve mapped the property **usd\_24h\_change**. This is where the actual percentage change percentage is returned by the API. You will need to rename this variable if you choose to work with a different time frame.

If you opt to use the free endpoint, you will need to change the shape of this object to match the response. That object could look something like this:

@dataclass

class CurrencyPerformance(JSONWizard):

 id: str

 name: str

 symbol: str

 price\_change\_percentage\_24h: float

 [view raw](https://gist.github.com/CyberPunkMetalHead/0470943d48888e5994bc8b9361044b79/raw/727d01ff777f31caa963c23e67ec8e633ca89a93/free.py)   [free.py](https://gist.github.com/CyberPunkMetalHead/0470943d48888e5994bc8b9361044b79#file-free-py) hosted with ❤ by [GitHub](https://github.com)

### Order Object

Finally, let’s define the shape of the exchange’s order response inside a new file called **order.py**. Upon executing a successful order, the Binance API will return a response of the following shape:

from dataclasses import dataclass

import datetime

from typing import List

from dataclass\_wizard import JSONWizard

@dataclass

class Fill:

 price: str

 qty: str

 commission: str

 commissionAsset: str

 tradeId: int

@dataclass

class OrderResponse(JSONWizard):

 symbol: str

 orderId: int

 orderListId: int

 clientOrderId: str

 transactTime: int

 price: str

 origQty: str

 executedQty: str

 origQuoteOrderQty: str

 cummulativeQuoteQty: str

 status: str

 timeInForce: str

 type: str

 side: str

 workingTime: datetime

 fills: List\[Fill\]

 selfTradePreventionMode: str

 [view raw](https://gist.github.com/CyberPunkMetalHead/0470943d48888e5994bc8b9361044b79/raw/727d01ff777f31caa963c23e67ec8e633ca89a93/order.py)   [order.py](https://gist.github.com/CyberPunkMetalHead/0470943d48888e5994bc8b9361044b79#file-order-py) hosted with ❤ by [GitHub](https://github.com)

Note that each order has an object called **fills** of type **List\[Fill\]**. This is because an order may take multiple fills to complete, depending on the size of the order and the available liquidity in the orderbook.

## Step 5. Build Services

Services are the parts of our code responsible for fetching data, executing and saving trades. For this bot, our services are comprised of 3 components:

* **The CoinGecko service**: responsible for fetching the top gainers and losers.
* **The Exchange service**: responsible for placing orders.
* **The Saving Service**: responsible for saving our trades and managing our portfolio locally in a .json file.

### CoinGecko Service

Under the **services** directory, create a new file called **coingecko\_service.py**. The CoinGecko service needs to do two things: fetch our top gainers and losers, and return a list of available vs\_currencies.

import requests

from models.currencies import Currency

from models.currency\_performance import GainersAndLosers

from utils.load\_env import \*

class CoinGecko:

 def \_\_init\_\_(self):

 self.root \= "https://pro-api.coingecko.com/api/v3"

 self.headers \= {

 "accept": "application/json",

 "x\_cg\_pro\_api\_key": f"{cg\_api\_key}",

}

 def get\_top\_gainers\_and\_losers(

 self, vs\_currency: Currency \= Currency.USD

) \-> GainersAndLosers:

 request\_url \= (

 self.root

 + f"/coins/top\_gainers\_losers?vs\_currency={vs\_currency.value}&top\_coins=300"

)

 return GainersAndLosers.from\_dict(

 requests.get(request\_url, self.headers).json()

)

 def get\_vs\_currencies(self):

 request\_url \= self.root + "/simple/supported\_vs\_currencies"

 return requests.get(request\_url, self.headers).json()

 [view raw](https://gist.github.com/CyberPunkMetalHead/0470943d48888e5994bc8b9361044b79/raw/727d01ff777f31caa963c23e67ec8e633ca89a93/coingecko_service.py)   [coingecko\_service.py](https://gist.github.com/CyberPunkMetalHead/0470943d48888e5994bc8b9361044b79#file-coingecko_service-py) hosted with ❤ by [GitHub](https://github.com)

💡 Pro-tip: If you’re using the Demo API endpoint, you will need to change self.root to be [https://api.coingecko.com/api/v3](https://api.coingecko.com/api/v3) and the self.headers key from **x\_cg\_pro\_api\_key** to **x-cg-demo-api-key**. Remember to use hyphens instead of underscores. You will also need to change the path of the request URL in **get\_top\_gainers\_and\_losers** to **coins/markets** and remove the &top\_coins=300 parameter.

### Exchange Service

Go ahead and create an **exchange\_service.py** file under services. Our exchange service should be able to: place buy and sell orders on our behalf, and fetch the current price of any cryptocurrency on the exchange, so let’s go ahead and build it:

from models.order import OrderResponse

from utils.load\_env import \*

from binance.client import Client

class Binance:

 def \_\_init\_\_(self):

 self.client \= Client(binance\_api\_key, binance\_api\_secret)

 def buy(

 self, coin: str, amount\_in\_vs\_currency: float, vs\_currency: str \= "USDT"

) \-> OrderResponse:

 response \= self.client.order\_market\_buy(

 symbol\=(coin + vs\_currency).upper(),

 quoteOrderQty\=amount\_in\_vs\_currency,

)

 return OrderResponse.from\_dict(response)

 def sell(self, coin: str, amount\_in\_vs\_currency: float) \-> OrderResponse:

 response \= self.client.order\_market\_sell(

 symbol\=(coin).upper(),

 quoteOrderQty\=amount\_in\_vs\_currency,

)

 return OrderResponse.from\_dict(response)

 def get\_current\_price(self, coin: str) \-> float:

 return float(self.client.get\_ticker(symbol\=coin)\["lastPrice"\])

 [view raw](https://gist.github.com/CyberPunkMetalHead/0470943d48888e5994bc8b9361044b79/raw/727d01ff777f31caa963c23e67ec8e633ca89a93/exchange_service.py)   [exchange\_service.py](https://gist.github.com/CyberPunkMetalHead/0470943d48888e5994bc8b9361044b79#file-exchange_service-py) hosted with ❤ by [GitHub](https://github.com)

One thing to note is we’re using the python-binance library to interact with the Binance API. This saves us from having to write our own wrapper.

Another thing to bear in mind is the **vs\_currency** parameter on the buy and sell methods. We’ve named it the same as CoinGecko’s own parameter for consistency, however this refers to the base asset that the exchange itself supports, not CoinGecko. For instance, most of Binance’s pairs are in USDT but this is not a value that CoinGecko’s API returns.

Finally, the reason behind having a method that fetches the current price for a coin, is to help us manage our exit strategy. We’ll use it in order to calculate the current stop loss and take profit for assets the bot is holding.

### Saving Service

This service should be able to write to, read from, local JSON files to help us save and manage our bot portofolio and trade history. Create a new file called **saving\_service.py**, and let’s define a **SavingService** class and its methods:

import json

from pathlib import Path

from typing import List

from models.order import OrderResponse

class SavingService:

 def \_\_init\_\_(self, file\_path: str):

 self.file\_path \= Path(file\_path)

 def \_ensure\_file\_exists(self):

 if not self.file\_path.exists():

 self.file\_path.write\_text("\[\]")

 def save\_order(self, order: OrderResponse):

 self.\_ensure\_file\_exists()

 with self.file\_path.open("r+") as file:

 data \= json.load(file)

 data.append(order.to\_dict())

 file.seek(0)

 json.dump(data, file, indent\=4)

 def load\_orders(self) \-> List\[OrderResponse\]:

 self.\_ensure\_file\_exists()

 with self.file\_path.open("r") as file:

 data \= json.load(file)

 return \[OrderResponse.from\_dict(order) for order in data\]

 def delete\_order(self, order\_id: int):

 self.\_ensure\_file\_exists()

 with self.file\_path.open("r+") as file:

 data \= json.load(file)

 updated\_data \= \[order for order in data if order.get("orderId") != order\_id\]

 file.seek(0)

 file.truncate()

 json.dump(updated\_data, file, indent\=4)

 def clear\_orders(self):

 self.file\_path.write\_text("\[\]")

 [view raw](https://gist.github.com/CyberPunkMetalHead/0470943d48888e5994bc8b9361044b79/raw/727d01ff777f31caa963c23e67ec8e633ca89a93/saving_service.py)   [saving\_service.py](https://gist.github.com/CyberPunkMetalHead/0470943d48888e5994bc8b9361044b79#file-saving_service-py) hosted with ❤ by [GitHub](https://github.com)

With the final service out of the way, it’s time to build our core bot logic in our main executable file.

## Step 6. Create the main executable file and bot logic

Inside **main.py**, let’s start by importing all the necessary requirements and initializing our services:

from services.coingecko\_service import CoinGecko

from services.exchange\_service import Binance

from services.saving\_service import SavingService

from utils.load\_config import config

from utils.logger import logging

import time

\# Initialize Our services

cg \= CoinGecko()

exchange \= Binance()

trades \= SavingService("assets/trades.json")

portfolio \= SavingService("assets/portfolio.json")

print(

 "THIS BOT WILL PLACE LIVE ORDERS. Use the Keyboard interrupt (Ctrl + C or Cmd + C) to cancel execution. Waiting 10s before starting execution..."

)

time.sleep(10)

 [view raw](https://gist.github.com/CyberPunkMetalHead/0470943d48888e5994bc8b9361044b79/raw/727d01ff777f31caa963c23e67ec8e633ca89a93/init.py)   [init.py](https://gist.github.com/CyberPunkMetalHead/0470943d48888e5994bc8b9361044b79#file-init-py) hosted with ❤ by [GitHub](https://github.com)

We’ve also added a live trading warning that will pop up on the console, giving us 10 seconds to change our mind.

Now let’s define our main function, this is where the core logic of our bot lives.

def main():

 \# Set the Trading mode

 if config.mode \== "GAINING":

 all\_assets \= cg.get\_top\_gainers\_and\_losers().top\_gainers

 logging.info(f"Trading the {config.number\_of\_assets} Top Gaining assets")

 elif config.mode \== "LOSING":

 all\_assets \= cg.get\_top\_gainers\_and\_losers().top\_losers

 logging.info(f"Trading the {config.number\_of\_assets} Top Losing assets")

 else:

 logging.error(

 f"Invalid mode, please choose between GAINING and LOSING in config.json "

)

 \# If your number of assets is set to 5, we will trade the 5 most volatilte assets

 top\_assets \= all\_assets\[0 : config.number\_of\_assets\]

 \# BUY Logic

 for asset in top\_assets:

 try:

 res \= exchange.buy(asset.symbol.upper(), config.amount, config.vs\_currency)

 \# Saving the order to Orders and Trades. We log the trade for historical purposes, and we add our order to our portfolio so we know what we hold.

 portfolio.save\_order(res)

 trades.save\_order(res)

 logging.info(

 f"Bought {res.executedQty} {res.symbol} at ${res.fills\[0\].price}. "

)

 except Exception as e:

 logging.error(f"COULD NOT BUY {asset.symbol}: {e} ")

 \# Update TP / SL and SELL Logic

 all\_portfolio\_assets \= portfolio.load\_orders()

 for order in all\_portfolio\_assets:

 original\_price \= float(order.fills\[0\].price)

 current\_price \= exchange.get\_current\_price(order.symbol)

 current\_pnl \= (current\_price \- original\_price) / original\_price \* 100

 if current\_pnl \> config.take\_profit or current\_pnl < config.stop\_loss:

 logging.info(

 f"SELL signal generated for {order.executedQty} {order.symbol} with order Id {order.orderId}"

)

 \# the summed up fees from all fills

 fees \= sum(\[float(item.commission) for item in order.fills\])

 try:

 amount\_in\_vs\_currency \= round(

 current\_price \* (float(order.executedQty) \- fees), 4

)

 res \= exchange.sell(order.symbol.upper(), amount\_in\_vs\_currency)

 trades.save\_order(res)

 portfolio.delete\_order(order.orderId)

 logging.info(

 f"SOLD {res.executedQty} {res.symbol} at ${res.fills\[0\].price}. "

)

 except Exception as e:

 logging.error(

 f"COULD NOT SELL {order.executedQty} {order.symbol}: {e} "

)

 [view raw](https://gist.github.com/CyberPunkMetalHead/0470943d48888e5994bc8b9361044b79/raw/727d01ff777f31caa963c23e67ec8e633ca89a93/main.py)   [main.py](https://gist.github.com/CyberPunkMetalHead/0470943d48888e5994bc8b9361044b79#file-main-py) hosted with ❤ by [GitHub](https://github.com)

The bot starts by checking the configured mode (either "GAINING" or "LOSING"). Based on this setting, it will trade either the top gaining or top losing assets.

Next, the bot selects the top assets based on the number specified in the configuration (config.number\_of\_assets). For instance, if this number is set to 5, the bot will only trade the top 5 assets returned.

The bot then attempts to purchase each selected asset using the configured amount and currency. If the purchase is successful, the order is logged in both **portfolio.json** and **trades.json** for future reference. The reason for saving buys in 2 separate files is because trades represent a historical view of all orders placed, while the portfolio only contains assets that the bot holds. The files are available under the assets directory and are automatically created.

Next, the bot monitors the performance of assets in the portfolio. It checks the current price against the original purchase price and calculates the profit or loss percentage. If the price change exceeds the configured take-profit or stop-loss thresholds, the bot triggers a sell order.

The sell logic also accounts for transaction fees, deducting them from the sale amount before executing the trade. Once the sell is completed, the order is removed from the portfolio and saved in the trades for historical tracking.

To run this logic on a loop, we just need to call the **main()** function inside a loop, and add our sleep timer from the bot’s configuration.

if \_\_name\_\_ \== "\_\_main\_\_":

 while True:

 main()

 logging.info(

 f"Successfully completed bot cycle, waiting {config.bot\_frequency\_in\_seconds} seconds before re-running..."

)

 time.sleep(config.bot\_frequency\_in\_seconds)

 [view raw](https://gist.github.com/CyberPunkMetalHead/0470943d48888e5994bc8b9361044b79/raw/727d01ff777f31caa963c23e67ec8e633ca89a93/exec.py)   [exec.py](https://gist.github.com/CyberPunkMetalHead/0470943d48888e5994bc8b9361044b79#file-exec-py) hosted with ❤ by [GitHub](https://github.com)

All that’s left to do is run the bot and monitor the logs! A healthy log should look like this:

![Log Example](https://assets.coingecko.com/coingecko/public/ckeditor_assets/pictures/22088/content_log.webp)

In case you get an error like the one below:

**Timestamp for this request was 1000ms ahead of the server's time.**

Simply synchronize your device’s time under the time and date settings.

## Considerations & Conclusion

Currently, the take profit and stop loss logic is executed in the same thread as the buy logic. This setup means that stop loss and take profit checks are only conducted once per bot cycle, rather than being continuously monitored. For example, if the bot is configured to trade top gaining coins once a day, it will only evaluate take profit and stop loss conditions once per day. This delay could lead to missed opportunities in volatile markets.

To address this limitation, the bot's take profit, stop loss and sell logic could be comved to a separate thread. This would decouple it from the buy logic, allowing them to run independently and respond more swiftly to market changes.

Additionally, it's worth noting that the bot currently lacks a test mode, meaning it can only operate in live trading.

Here is the link to the **[Github Repository](https://github.com/CyberPunkMetalHead/gainers-losers-sniping-bot)**.

Finally, always ensure your **config.json** is tailored to a trading strategy that aligns with your goals and risk tolerance. As with any trading tool, practice caution and trade responsibly.

* * *

If you enjoyed this article, be sure to check out this guide on how to [build a crypto arbitrage bot](https://www.coingecko.com/learn/crypto-arbitrage-bot-python).

\`\`\`
