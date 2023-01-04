# Terra Oracle Feeder - 
Forked from Terra Rebels

This contains the Oracle feeder software that is used internally by Terraform Labs' validator nodes for periodically submitting oracle votes for the exchange rate of LUNC (LUNA Classic). This implementation can be used as-is, but also can serve as a reference for creating your own custom oracle feeder. For more information regarding the oracle process, please refer to the [oracle module specs](https://docs.terra.money/dev/spec-oracle).

## Overview

This solution has 2 components:

1. [`price-server`](price-server/)

   - Obtain information from various data sources (exchanges, forex data, etc)
   - Use data to compute the exchange rates of LUNC for a given set of fiat denominations
   - Most recent LUNC exchange rates are available through HTTP endpoint

2. [`feeder`](feeder/)

   - Reads LUNC exchange rates from a data source (`price-server`)
   - Periodically submits vote and prevote messages following the oracle voting procedure

You can easily modify the logic for how LUNC exchange rates are computed by either directly modifying `price-server` or replacing the input stream for `feeder`.

## Prerequisites

- Install [Node.js version 12 or greater](https://nodejs.org/)

## Instructions

1. Clone this repository

```sh
git clone https://github.com/CatalystixCC/oracle-feeder.git
cd oracle-feeder
```

2. Configure and launch `price-server`, following instructions [here](price-server/).

```sh
cp ./config/tr-price-server-default.js ./config/default.js
cd price-server
npm install

# price is available at `http://127.0.0.1:8532/latest`
npm start
```

3. Configure and launch `feeder`, following instructions [here](feeder/).

```sh
cd feeder
npm install

# configure to use feeder account
npm start update-key

#update terra.js
npm install @terra-money/terra.js@3.0.4

# start voting
npm start vote -- \
   --source http://localhost:8532/latest \
   --lcd http://localhost:1317 \
   --lcd https://terra-classic-lcd.publicnode.com \
   --chain-id columbus-5 \
   --validator terravaloper1xx \
   --validator terravaloper1yy \
   --password "<password>"
```
