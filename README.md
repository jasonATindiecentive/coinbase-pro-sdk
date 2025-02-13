![Coinbase LOGO](https://github.com/MockingMagician/coinbase-pro-sdk/raw/master/docs/assets/coinbase-pro-sdk-min.png "Coinbase LOGO")

# This package is designed to communicate easily with the Coinbase Pro API in PHP.

![GitHub release (latest SemVer)](https://img.shields.io/github/v/release/MockingMagician/coinbase-pro-sdk) ![GitHub Workflow Status (branch)](https://img.shields.io/github/workflow/status/MockingMagician/coinbase-pro-sdk/Testing%20suite/master?label=tests) ![PHPStan level](https://img.shields.io/badge/phpstan-level%207-success) ![](https://img.shields.io/badge/coverage-93%25-green) ![Code Climate maintainability](https://img.shields.io/codeclimate/maintainability-percentage/MockingMagician/coinbase-pro-sdk?label=code%20climate) ![LICENSE BADGE](https://img.shields.io/packagist/l/mocking-magician/coinbase-pro-sdk?color=blue) ![Packagist PHP Version Support](https://img.shields.io/packagist/php-v/mocking-magician/coinbase-pro-sdk) 

## Install the package

```bash

composer require mocking-magician/coinbase-pro-sdk

```

***Please take the time to read the documentation below carefully.***

## How do I access the features?

All the main and necessary functionalities to use this library are grouped in the CoinbaseFacade class.

### 1 : Create an CoinbaseApi object.

#### 1.1 : Full API access with default features

```php

use MockingMagician\CoinbaseProSdk\CoinbaseFacade;

$api = CoinbaseFacade::createDefaultCoinbaseApi(
    'API_ENDPOINT',
    'API_KEY',
    'API_SECRET',
    'API_PASSPHRASE'
);

```

#### 1.2 : Customize API

```php

use MockingMagician\CoinbaseProSdk\CoinbaseFacade;

$config = CoinbaseFacade::createDefaultCoinbaseConfig(
    'API_ENDPOINT',
    'API_KEY',
    'API_SECRET',
    'API_PASSPHRASE'
);

// Choose to activate or deactivate some of the API endpoints
$config->getConnectivityConfig()
    ->activateAccounts(false)
    ->activateOrders(false)
    ->activateFills(false)
    ->activateLimits(false)
    ->activateDeposits(false)
    ->activateWithdrawals(false)
    ->activateStablecoinConversions(false)
    ->activatePaymentMethods(false)
    ->activateCoinbaseAccounts(false)
    ->activateFees(false)
    ->activateReports(false)
    ->activateProfiles(false)
    ->activateMargin(false)
    ->activateOracle(false)
    ->activateProducts(false)
    ->activateCurrencies(false)
    ->activateTime(false)
;

// Enable or disable the use of remote time during the requests, default to false
$config->setUseCoinbaseRemoteTime(true);
// Enable or disable management of rate limits imposed by remote API, default to true
$config->setManageRateLimits(true);
// Enable or disable the use of a security layer that encrypt parameters used each time by the requests, default to true
$config->setUseSecurityLayerForParams(true);

$api = CoinbaseFacade::createCoinbaseApi($config);

```

#### 1.3 : Create from yaml config file

```php

use MockingMagician\CoinbaseProSdk\CoinbaseFacade;

$api = CoinbaseFacade::createCoinbaseApiFromYaml('path/to/config.yaml');

```

Example of a complete configuration file :

```yaml

params: 
    # endpoint: '${SOME_ENV}' <= formatting as is autoload SOME_ENV (if exist)
    endpoint: '${API_ENDPOINT}'
    key: '${API_KEY}'
    secret: '${API_SECRET}'
    passphrase: '${API_PASSPHRASE}'

connectivity:
    accounts: true # default
    coinbase_accounts: true # default
    currencies: true # default
    deposits: true # default
    fees: true # default
    fills: true # default
    limits: true # default
    margin: true # default
    oracle: true # default
    orders: true # default
    payment_methods: true # default
    products: true # default
    profiles: true # default
    reports: true # default
    stablecoin_conversions: true # default
    time: true # default
    withdrawals: true # default

remote_time: false # default

manage_rate_limits: true # default

secure_params: true # default

```

Minimal configuration file :

```yaml

params: 
    endpoint: '${API_ENDPOINT}'
    key: '${API_KEY}'
    secret: '${API_SECRET}'
    passphrase: '${API_PASSPHRASE}'

```

#### 1.4 : Time in API request

According to documentation :

>Selecting a Timestamp
>
>The CB-ACCESS-TIMESTAMP header MUST be number of seconds since Unix Epoch in UTC. Decimal values are allowed.
>
>Your timestamp must be within 30 seconds of the api service time or your request will be considered expired and rejected. We recommend using the time endpoint to query for the API server time if you believe there many be time skew between your server and the API servers.

The package is designed to handle this situation. 

In order to ensure good timestamp in requests, you just need to activate the functionality and the requests will use the timestamp provided by the Coinbase server.

***This feature makes network calls in order to retrieve the timestamp, thus increasing the number of requests made.***

***The feature is disabled by default.***

Example 1 :

```php

use MockingMagician\CoinbaseProSdk\CoinbaseFacade;

$config = CoinbaseFacade::createDefaultCoinbaseConfig(
    'API_ENDPOINT',
    'API_KEY',
    'API_SECRET',
    'API_PASSPHRASE'
);

$config->setUseCoinbaseRemoteTime(true); // pass true here to enable the remote timestamp provided by the remote Coinbase server

$api = CoinbaseFacade::createCoinbaseApi($config);

```

Example 2 :

```php

use MockingMagician\CoinbaseProSdk\CoinbaseFacade;

$api = CoinbaseFacade::createCoinbaseApiFromYaml('path/to/config.yaml');

```

Config file :

```yaml

params: 
    endpoint: '${API_ENDPOINT}'
    key: '${API_KEY}'
    secret: '${API_SECRET}'
    passphrase: '${API_PASSPHRASE}'

remote_time: true # pass true here to enable the remote timestamp provided by the remote Coinbase server

```

#### 1.5 : API rate limits

According to documentation :

>When a rate limit is exceeded, a status of 429 Too Many Requests will be returned.
>
>PUBLIC ENDPOINTS
>
>We throttle public endpoints by IP: 3 requests per second, up to 6 requests per second in bursts. Some endpoints may have custom rate limits.
>
>PRIVATE ENDPOINTS
>
>We throttle private endpoints by profile ID: 5 requests per second, up to 10 requests per second in bursts. Some endpoints may have custom rate limits.

***The package has a mechanism to respect the established limits.***

***This parameter is active by default but can be disabled if necessary.***

Example 1 :

```php

use MockingMagician\CoinbaseProSdk\CoinbaseFacade;

$config = CoinbaseFacade::createDefaultCoinbaseConfig(
    'API_ENDPOINT',
    'API_KEY',
    'API_SECRET',
    'API_PASSPHRASE'
);

$config->setManageRateLimits(false); // pass false here to disable rate limit managing

$api = CoinbaseFacade::createCoinbaseApi($config);

```

Example 2 :

```php

use MockingMagician\CoinbaseProSdk\CoinbaseFacade;

$api = CoinbaseFacade::createCoinbaseApiFromYaml('path/to/config.yaml');

```

Config file :

```yaml

params: 
    endpoint: '${API_ENDPOINT}'
    key: '${API_KEY}'
    secret: '${API_SECRET}'
    passphrase: '${API_PASSPHRASE}'

manage_rate_limits: false # pass false here to disable rate limit managing

```

### 2 : Using the ApiConnectivity object

***In the rest of the documentation, we will assume that you know how to create an CoinbaseApi object and the variable $api will refer to this object.***

#### 2.1 : List of features 

All features described in the [documentation](https://docs.pro.coinbase.com) are implemented :

- Accounts
- Orders
- Fills
- Limits
- Deposits
- Withdrawals
- Stablecoin Conversions
- Payment Methods
- Coinbase Accounts
- Fees
- Reports
- Profiles
- Margin (Limited on remote)
- Oracle
- Market Data
- Products
- Currencies
- Time

```php

use MockingMagician\CoinbaseProSdk\Contracts\Api\ApiInterface;

/** @var ApiInterface $api */

$api->accounts();
$api->orders();
$api->fills();
$api->limits();
$api->deposits();
$api->withdrawals();
$api->stablecoinConversions();
$api->paymentMethods();
$api->coinbaseAccounts();
$api->fees();
$api->reports();
$api->profiles();
$api->margin();
$api->oracle();
$api->products();
$api->currencies();
$api->time();

```

#### 2.2 : Details by category

##### 2.2.1 : Accounts methods

```php

use MockingMagician\CoinbaseProSdk\Contracts\Api\ApiInterface;

/** @var ApiInterface $api */

$api->accounts()->list();
$api->accounts()->getAccount('132fb6ae-456b-4654-b4e0-d681ac05cea1');
$api->accounts()->getAccountHistory('132fb6ae-456b-4654-b4e0-d681ac05cea1');
$api->accounts()->getHolds('132fb6ae-456b-4654-b4e0-d681ac05cea1');

```

##### 2.2.2 : Orders methods

```php

use MockingMagician\CoinbaseProSdk\Contracts\Api\ApiInterface;

/** @var ApiInterface $api */

$api->orders()->getOrderById('132fb6ae-456b-4654-b4e0-d681ac05cea1');
$api->orders()->listOrders();
$api->orders()->cancelOrderById('132fb6ae-456b-4654-b4e0-d681ac05cea1');
$api->orders()->cancelAllOrders();
$api->orders()->cancelOrderByClientOrderId('132fb6ae-456b-4654-b4e0-d681ac05cea1');
$api->orders()->getOrderByClientOrderId('132fb6ae-456b-4654-b4e0-d681ac05cea1');

```

***How to place an order ?***

###### 2.2.2.1 : Place market order

```php

use MockingMagician\CoinbaseProSdk\CoinbaseFacade;
use MockingMagician\CoinbaseProSdk\Contracts\Api\ApiInterface;
use MockingMagician\CoinbaseProSdk\Functional\Build\MarketOrderToPlace;

/** @var ApiInterface $api */

$marketOrder = CoinbaseFacade::createMarketOrderToPlace(MarketOrderToPlace::SIDE_BUY, 'BTC-USD', 0.0001);
$api->orders()->placeOrder($marketOrder);

```

###### 2.2.2.2 : Place limit order

```php

use MockingMagician\CoinbaseProSdk\CoinbaseFacade;
use MockingMagician\CoinbaseProSdk\Contracts\Api\ApiInterface;
use MockingMagician\CoinbaseProSdk\Functional\Build\LimitOrderToPlace;

/** @var ApiInterface $api */

$limitOrder = CoinbaseFacade::createLimitOrderToPlace(LimitOrderToPlace::SIDE_BUY, 'BTC-USD', 10000, 0.0001);

$api->orders()->placeOrder($limitOrder);

```

###### 2.2.2.3 : Understanding orders

***For a good understanding of the orders and parameters available for each order, please refer to the Coinbase Pro documentation, as well as the documentation available in the interfaces of this package.***

Example of complex orders :

- Market

```php

use MockingMagician\CoinbaseProSdk\CoinbaseFacade;
use MockingMagician\CoinbaseProSdk\Contracts\Api\ApiInterface;
use MockingMagician\CoinbaseProSdk\Functional\Build\MarketOrderToPlace;

/** @var ApiInterface $api */

$marketOrder = CoinbaseFacade::createMarketOrderToPlace(
    MarketOrderToPlace::SIDE_BUY,
    'BTC-USD',
    0.0001, // null if funds is set and "vice versa"
    null, // null if size is set and "vice versa"
    MarketOrderToPlace::SELF_TRADE_PREVENTION_DECREASE_AND_CANCEL, // The self trade prevention flag,
    MarketOrderToPlace::STOP_LOSS, // Direction flag for stop
    1000, // Stop price
    '132fb6ae-456b-4654-b4e0-d681ac05cea1' // A custom id that you defined (UUID format only)
);

$api->orders()->placeOrder($marketOrder);

```

- Limit

```php

use MockingMagician\CoinbaseProSdk\CoinbaseFacade;
use MockingMagician\CoinbaseProSdk\Contracts\Api\ApiInterface;
use MockingMagician\CoinbaseProSdk\Functional\Build\LimitOrderToPlace;

/** @var ApiInterface $api */

$limitOrder = CoinbaseFacade::createLimitOrderToPlace(
    LimitOrderToPlace::SIDE_BUY,
    'BTC-USD',
    10000, // Price
    0.0001, // Sice
LimitOrderToPlace::TIME_IN_FORCE_GOOD_TILL_CANCELED, // The TIF flag
    LimitOrderToPlace::CANCEL_AFTER_DAY, // A cancel after time
    true, // Define if post-only or taker allowed, post_only is disabled by default
    LimitOrderToPlace::SELF_TRADE_PREVENTION_DECREASE_AND_CANCEL, // The self trade prevention flag,
    LimitOrderToPlace::STOP_LOSS, // Direction flag for stop
    1000, // Stop price
    '132fb6ae-456b-4654-b4e0-d681ac05cea1' // A custom id that you defined (UUID format only)
);

$api->orders()->placeOrder($limitOrder);

```

##### 2.2.3 : Fills methods

```php

use MockingMagician\CoinbaseProSdk\Contracts\Api\ApiInterface;

/** @var ApiInterface $api */

$api->fills()->listFills();

```

##### 2.2.4 : Limits methods

```php

use MockingMagician\CoinbaseProSdk\Contracts\Api\ApiInterface;

/** @var ApiInterface $api */

$api->limits()->getCurrentExchangeLimits();

```

##### 2.2.5 : Deposits methods

```php

use MockingMagician\CoinbaseProSdk\Contracts\Api\ApiInterface;

/** @var ApiInterface $api */

$api->deposits()->listDeposits();
$api->deposits()->getDeposit('132fb6ae-456b-4654-b4e0-d681ac05cea1');
$api->deposits()->doDeposit(100, 'USD', '132fb6ae-456b-4654-b4e0-d681ac05cea1');
$api->deposits()->doDepositFromCoinbase(100, 'USD', '132fb6ae-456b-4654-b4e0-d681ac05cea1');
$api->deposits()->generateCryptoDepositAddress('132fb6ae-456b-4654-b4e0-d681ac05cea1');

```

##### 2.2.6 : Withdrawals methods

```php

use MockingMagician\CoinbaseProSdk\Contracts\Api\ApiInterface;

/** @var ApiInterface $api */

$api->withdrawals()->listWithdrawals();
$api->withdrawals()->getWithdrawal('132fb6ae-456b-4654-b4e0-d681ac05cea1');
$api->withdrawals()->doWithdraw(100, 'USD', '132fb6ae-456b-4654-b4e0-d681ac05cea1');
$api->withdrawals()->doWithdrawToCoinbase(100, 'USD', '132fb6ae-456b-4654-b4e0-d681ac05cea1');
$api->withdrawals()->doWithdrawToCryptoAddress(0.1, 'BTC', 'bc1qar0srrr7xfkvy5l643lydnw9re59gtzzwf5mdq');

```

##### 2.2.7 : Stablecoin Conversions methods

```php

use MockingMagician\CoinbaseProSdk\Contracts\Api\ApiInterface;

/** @var ApiInterface $api */

$api->stablecoinConversions()->createConversion('USD', 'USDC', 100);

```

##### 2.2.8 : Payment Methods methods

```php

use MockingMagician\CoinbaseProSdk\Contracts\Api\ApiInterface;

/** @var ApiInterface $api */

$api->paymentMethods()->listPaymentMethods();

```

##### 2.2.9 : Coinbase Accounts methods

```php

use MockingMagician\CoinbaseProSdk\Contracts\Api\ApiInterface;

/** @var ApiInterface $api */

$api->coinbaseAccounts()->listCoinbaseAccounts();

```

##### 2.2.10 : Fees methods

```php

use MockingMagician\CoinbaseProSdk\Contracts\Api\ApiInterface;

/** @var ApiInterface $api */

$api->fees()->getCurrentFees();

```

##### 2.2.11 : Reports methods

```php

use MockingMagician\CoinbaseProSdk\Contracts\Api\ApiInterface;
use MockingMagician\CoinbaseProSdk\Contracts\Connectivity\ReportsInterface;

/** @var ApiInterface $api */

$api->reports()->createNewReport(
    ReportsInterface::TYPE_FILLS,
    (new DateTime())->modify('-1 year'),
    new DateTime(),
    null, // Optional
    '', // Optional
    ReportsInterface::FORMAT_PDF,
    null // Optional, if filled report will be sent to this email
);
$api->reports()->getReportStatus('132fb6ae-456b-4654-b4e0-d681ac05cea1');

```

##### 2.2.12 : Profiles methods

```php

use MockingMagician\CoinbaseProSdk\Contracts\Api\ApiInterface;

/** @var ApiInterface $api */

$api->profiles()->listProfiles(true /* list active only or not */);
$api->profiles()->getProfile('132fb6ae-456b-4654-b4e0-d681ac05cea1');
$api->profiles()->createProfileTransfer('132fb6ae-456b-4654-b4e0-d681ac05cea1', '742fb6ae-145f-8954-b4e0-d681ac05cea1', 'USD', 500);

```

##### 2.2.13 : User Account methods - Deprecated

__DEPRECATED__ : According to coinbase api documentation changelog (04/02/2021) this endpoint has been deprecated in favor of the Fees endpoint to get the latest volumes. 25/02/2021: Endpoint is already disabled.

__This method and userAccount endpoint are removed from 7.0__

```php

use MockingMagician\CoinbaseProSdk\Contracts\Api\ApiInterface;

/** @var ApiInterface $api */

$api->userAccount()->getTrailingVolume(); // userAccount method removed from 7.0

```

##### 2.2.14 : Margin methods

```php

use MockingMagician\CoinbaseProSdk\Contracts\Api\ApiInterface;

/** @var ApiInterface $api */

$api->margin()->getMarginStatus(); // Returns the status of margin API

/*

As long as the margin functionality is not enabled, the methods below could not be fully tested. 

A decorator protects the method call as long as the status of the API on the coinbase side does not return an active and eligible status.

$api->margin()->getExitPlan();
$api->margin()->getBuyingPower();
$api->margin()->getWithdrawalPower();
$api->margin()->listLiquidationHistory();
$api->margin()->getAllWithdrawalPowers();
$api->margin()->getPositionsRefreshAmount();
$api->margin()->getMarginProfileInformation();

*/

```

##### 2.2.15 : Oracle methods

```php

use MockingMagician\CoinbaseProSdk\Contracts\Api\ApiInterface;

/** @var ApiInterface $api */

$api->oracle()->getCryptographicallySignedPrices();

```

##### 2.2.16 : Products methods

```php

use MockingMagician\CoinbaseProSdk\Contracts\Api\ApiInterface;

/** @var ApiInterface $api */

$api->products()->getProducts();
$api->products()->get24hrStats('132fb6ae-456b-4654-b4e0-d681ac05cea1');
$api->products()->getProductTicker('132fb6ae-456b-4654-b4e0-d681ac05cea1');
$api->products()->getProductOrderBook('132fb6ae-456b-4654-b4e0-d681ac05cea1');
$api->products()->getTrades('132fb6ae-456b-4654-b4e0-d681ac05cea1');
$api->products()->getSingleProduct('132fb6ae-456b-4654-b4e0-d681ac05cea1');
$api->products()->getHistoricRates(
    '132fb6ae-456b-4654-b4e0-d681ac05cea1',
    (new DateTime())->modify('-1 year'),
    new DateTime(),
    3600
);

```

##### 2.2.17 : Currencies methods

```php

use MockingMagician\CoinbaseProSdk\Contracts\Api\ApiInterface;

/** @var ApiInterface $api */

$api->currencies()->getCurrencies();

```

##### 2.2.18 : Time methods

```php

use MockingMagician\CoinbaseProSdk\Contracts\Api\ApiInterface;

/** @var ApiInterface $api */

$api->time()->getTime();

```

### 3 : Pagination

***Some queries are paginated. A complete mechanism is provided to manage this pagination with the Pagination object.***

According to Coinbase Pro documentation :

>Cursor pagination can be unintuitive at first. before and after cursor arguments should not be confused with before and after in chronological time. Most paginated requests return the latest information (newest) as the first page sorted by newest (in chronological time) first. To get older information you would request pages after the initial page. To get information newer, you would request pages before the first page.

In order to manage the non-intuitive side of the "before" and "after" fields in the query. The package has been normalized around the more classical concept of "descending" and "ascending".

* The "DESC" direction will retrieve the elements of the most recent direction or the highest value, to the oldest or the smallest value.

* While the "ASC" direction will retrieve the elements of the oldest direction or of the smallest value, to the newer or the highest value.

This standardization allows a better understanding of the path taken by the pagination.

#### 3.1 : List of paginated features

```php

use MockingMagician\CoinbaseProSdk\Contracts\Api\ApiInterface;

/** @var ApiInterface $api */

$api->accounts()->getAccountHistoryRaw('132fb6ae-456b-4654-b4e0-d681ac05cea1'); // Paginated request
$api->accounts()->getHolds('132fb6ae-456b-4654-b4e0-d681ac05cea1'); // Paginated request
$api->fills()->listFills(); // Paginated request
$api->deposits()->listDeposits(); // Paginated request
$api->withdrawals()->listWithdrawals(); // Paginated request

```

#### 3.2 : How pagination works

Example : 

```php

use MockingMagician\CoinbaseProSdk\CoinbaseFacade;
use MockingMagician\CoinbaseProSdk\Contracts\Api\ApiInterface;

/** @var ApiInterface $api */

$accountId = '132fb6ae-456b-4654-b4e0-d681ac05cea1';

$pagination = CoinbaseFacade::createPagination(); // The pagination object

while ($pagination->hasNext()) { // Fetch new page while has next
    $history = $api->accounts()->getAccountHistory($accountId, $pagination);
}

```

Pagination Settings :

```php

use MockingMagician\CoinbaseProSdk\CoinbaseFacade;
use MockingMagician\CoinbaseProSdk\Contracts\Api\ApiInterface;
use MockingMagician\CoinbaseProSdk\Functional\Build\Pagination;

/** @var ApiInterface $api */

$accountId = '132fb6ae-456b-4654-b4e0-d681ac05cea1';

$pagination = CoinbaseFacade::createPagination( // The pagination object
    Pagination::DIRECTION_DESC, // DESC is default value
    1547662, // An offset cursor value
    25 // The limit on the number of results to bring back per query (max 100)
);

while ($pagination->hasNext()) { // Fetch new page while has next
    $history = $api->accounts()->getAccountHistory($accountId, $pagination);
}

```

### 4 : Executing tests

For any test execution the following environment variables must be available:
* API_KEY=my-api-key
* API_SECRET=my-secret
* API_PASSPHRASE=my-passphrase

***For security reasons, API_ENDPOINT has been hard-coded at the test level on the url https://api-public.sandbox.pro.coinbase.com.***

The only recommended way to provide these variables in the test set is to provide the root of the project with an .env file containing these variables.

.env file example :

```dotenv
API_KEY=my-api-key
API_SECRET=my-secret
API_PASSPHRASE=my-passphrase
```

The vast majority of the test set is composed of functional call and response tests of the API.

In order for these tests to pass, your test account on Coinbase Pro must be funded with cash.

***The currency in the test account is not real money. You can deposit it without limit using the deposit functions provided on the interface.***

It is intended that the set of tests will fund your account from virtual Coinbase accounts. At the first use, it is possible that the tests may not pass if the funds are insufficient, restart the tests.

#### 4.1 : Running tests methods

A Makefile is present in the project and provides shortcuts to run the tests. Run the ***make help*** command for the complete list of shortcuts :

```bash
docker-test-php-71             Docker test on PHP 7.1
docker-test-php-72             Docker test on PHP 7.2
docker-test-php-73             Docker test on PHP 7.3
docker-test-php-74             Docker test on PHP 7.4
tests-in-all-php-versions      Run tests in all PHP versions through containers
tests                          Launch PHPUnit test suite locally
phpstan                        Static analysis
```

## Issues

***Please, in case of discovery of any bug or security issues related to the package. Please launch an issue describing the problem and how to reproduce it.***
