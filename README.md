[![Build Status](https://travis-ci.com/yaroslavche/phptdlib.svg?branch=master)](https://travis-ci.com/yaroslavche/phptdlib)

[Documentation](https://yaroslavche.github.io/phptdlib/)

## example
[https://github.com/tdlib/td/blob/master/example/cpp/tdjson_example.cpp](https://github.com/tdlib/td/blob/master/example/cpp/tdjson_example.cpp)
```php
<?php
$client = td_json_client_create();
while(true)
{
    $result = td_json_client_receive($client, 10);
    if(!empty($result)) {
        echo $result;
        break;
    }
}
```

```php
<?php
Error_Reporting(E_ALL);
ini_set('display_errors', 1);

$api_id = 11111; // must be integer
$api_hash = 'abcdef1234567890abcdef1234567890';
$phone_number = '+380991234567';

try {
    TDApi\LogConfiguration::setLogVerbosityLevel(\TDApi\LogConfiguration::LVL_ERROR);
    
    $client = new TDLib\JsonClient();
    $client->getAuthorizationState();
    
    $tdlibParams = new TDApi\TDLibParameters();
    $tdlibParams
        ->setParameter(TDApi\TDLibParameters::USE_TEST_DC, true)
        ->setParameter(TDApi\TDLibParameters::DATABASE_DIRECOTRY, '/var/tmp/tdlib')
        ->setParameter(TDApi\TDLibParameters::FILES_DIRECTORY, '/var/tmp/tdlib')
        ->setParameter(TDApi\TDLibParameters::USE_FILE_DATABASE, false)
        ->setParameter(TDApi\TDLibParameters::USE_CHAT_INFO_DATABASE, false)
        ->setParameter(TDApi\TDLibParameters::USE_MESSAGE_DATABASE, false)
        ->setParameter(TDApi\TDLibParameters::USE_SECRET_CHATS, false)
        ->setParameter(TDApi\TDLibParameters::API_ID, $api_id)
        ->setParameter(TDApi\TDLibParameters::API_HASH, $api_hash)
        ->setParameter(TDApi\TDLibParameters::SYSTEM_LANGUAGE_CODE, 'en')
        ->setParameter(TDApi\TDLibParameters::DEVICE_MODEL, php_uname('s'))
        ->setParameter(TDApi\TDLibParameters::SYSTEM_VERSION, php_uname('v'))
        ->setParameter(TDApi\TDLibParameters::APPLICATION_VERSION, '0.0.8')
        ->setParameter(TDApi\TDLibParameters::ENABLE_STORAGE_OPTIMIZER, true)
        ->setParameter(TDApi\TDLibParameters::IGNORE_FILE_NAMES, false);
    $result = $client->setTdlibParameters($tdlibParams);
    $result = $client->setDatabaseEncryptionKey(); // checkDatabaseEncryptionKey(key) or DESTROY
    
    $result = $client->setAuthenticationPhoneNumber($phone_number, 3); // wait response 3 seconds. default - 1.
    // UNCOMMENT NEXT and COMMENT PREVIOUS LINES WHEN RECEIVE SMS. Set your data
    // $result = $client->query(json_encode(['@type' => 'checkAuthenticationCode', 'code' => 'xxxxx', 'first_name' => 'dummy', 'last_name' => 'dummy']), 10);
    
    $result = $client->getAuthorizationState();
    $result = $client->query(json_encode(['@type' => 'searchPublicChat', 'username' => 'telegram']), 10);
    var_dump($result);
    
    $allNotifications = $client->getReceivedResponses();
    var_dump($allNotifications);
} catch (\Exception $exception) {
    echo sprintf('something goes wrong: %s', $exception->getMessage());
}
```

## Required

The most of dependencies are installed via git submodules currently.

But PHP-CPP currently has to be built separately.
 
 - [PHP-CPP v2.0.0][2]
 
```bash
git clone https://github.com/CopernicaMarketingSoftware/PHP-CPP.git
cd PHP-CPP
make
sudo make install
```

If you want to link other dependencies as dynamic libraries, you can pass
 some options to cmake. Look through CMakeLists.txt comments to find out how.

## install extension
```bash
git clone --recurse-submodules https://github.com/yaroslavche/phptdlib.git
cd phptdlib && mkdir build && cd build
cmake ..
make
sudo make install
```
### check
```bash
php -i | grep tdlib
php ../php_examples/func.php
```


## Docker

```
docker build -t phptdlib .
docker run -it phptdlib

php -i | grep tdlib

cd $HOME/phptdlib/php_examples

php func.php

cp credentials.php.dist credentials.php
php client.php
```

### yaroslavche/phptdlib
[docker image][phptdlib_docker_image]

```
docker build -f Dockerfile.phptdlib_env -t yaroslavche/phptdlib .
```

`OpenSUSE Leap` with installed `sudo` `which` `gcc` `gcc-c++` `zlib` `gperf` `openssl` `openssl-devel` `cmake` `git` `php7` `php7-devel` `php7-ctype` `php7-json`. Needs min [4GB RAM][td_ram_issue] and must wait a while.
1.25Gb
needs: g++ -> clang (RAM issue) and opensuse -> alpine (size), docker-compose

[1]: https://github.com/tdlib/td
[2]: https://github.com/CopernicaMarketingSoftware/PHP-CPP/
[3]: https://github.com/nlohmann/json
[td_dependencies]: https://github.com/tdlib/td#dependencies
[td_ram_issue]: https://github.com/tdlib/td/issues/67
[phptdlib_docker_image]: https://hub.docker.com/r/yaroslavche/phptdlib/
