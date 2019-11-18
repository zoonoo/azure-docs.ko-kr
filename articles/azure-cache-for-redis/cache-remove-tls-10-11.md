---
title: Redis 용 Azure Cache를 사용 하 여 TLS 1.0 및 1.1을 제거 합니다.
description: Redis 용 Azure Cache와 통신할 때 응용 프로그램에서 TLS 1.0 및 1.1을 제거 하는 방법에 대해 알아봅니다.
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: yegu
ms.openlocfilehash: 585828855cdbd3a585ced1aa6803482c3f26f0f4
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74121586"
---
# <a name="remove-tls-10-and-11-from-use-with-azure-cache-for-redis"></a>Redis 용 Azure Cache를 사용 하 여 TLS 1.0 및 1.1을 제거 합니다.

TLS (전송 계층 보안) 버전 1.2 이상에 대 한 독점 사용에 대 한 업계 전체의 푸시가 있습니다. TLS 버전 1.0 및 1.1은 비스 트 및 POODLE 같은 공격에 취약 하 고 다른 일반적인 취약성 및 노출 (CVE) 약점을 보유 하는 것으로 알려져 있습니다. 또한 PCI (결제 카드 산업) 규정 준수 표준에 의해 권장 되는 최신 암호화 방법 및 암호 그룹을 지원 하지 않습니다. 이 [TLS 보안 블로그](https://www.acunetix.com/blog/articles/tls-vulnerabilities-attacks-final-part/) 는 이러한 취약성에 대해 자세히 설명 합니다.

이러한 고려 사항은 즉각적인 문제를 일으키지 않지만 TLS 1.0 및 1.1을 곧 사용 하지 않는 것이 좋습니다. Redis 용 Azure Cache는 2020 년 3 월 31 일에 이러한 TLS 버전을 지원 하지 않습니다. 이 날짜 이후에 응용 프로그램은 TLS 1.2 이상 버전을 사용 하 여 캐시와 통신 해야 합니다.

이 문서에서는 이러한 이전 TLS 버전에 대 한 종속성을 검색 하 고 응용 프로그램에서 제거 하는 방법에 대 한 일반적인 지침을 제공 합니다.

## <a name="check-whether-your-application-is-already-compliant"></a>응용 프로그램이 이미 호환 되는지 확인

응용 프로그램이 TLS 1.2에서 작동 하는지 여부를 확인 하는 가장 쉬운 방법은 사용 하는 테스트 또는 스테이징 캐시에서 **최소 tls 버전** 값을 tls 1.2로 설정 하는 것입니다. **최소 TLS 버전** 설정은 Azure Portal의 캐시 인스턴스에 대 한 [고급 설정](cache-configure.md#advanced-settings) 에 있습니다. 이러한 변경 후 응용 프로그램이 계속 해 서 작동 하는 경우에는 정책을 준수 하는 것입니다. 응용 프로그램에서 특별히 TLS 1.2을 사용 하도록 설정 하기 위해 사용 하는 일부 Redis 클라이언트 라이브러리를 구성 해야 할 수도 있으므로 해당 보안 프로토콜을 통해 Redis 용 Azure 캐시에 연결할 수 있습니다.

## <a name="configure-your-application-to-use-tls-12"></a>TLS 1.2를 사용 하도록 응용 프로그램 구성

대부분의 응용 프로그램은 Redis 클라이언트 라이브러리를 사용 하 여 해당 캐시와의 통신을 처리 합니다. 다음은 다양 한 프로그래밍 언어와 프레임 워크에서 TLS 1.2을 사용 하기 위해 널리 사용 되는 일부 클라이언트 라이브러리를 구성 하는 지침입니다.

### <a name="net-framework"></a>.NET Framework

Redis .NET 클라이언트는 .NET Framework 4.5.2 이전 버전에서 기본적으로 가장 이른 TLS 버전을 사용 하 고 .NET Framework 4.6 이상에서 최신 TLS 버전을 사용 합니다. 이전 버전의 .NET Framework 사용 하는 경우 TLS 1.2을 수동으로 사용 하도록 설정할 수 있습니다.

* **Stackexchange:** 연결 문자열에 `ssl=true` 및 `sslprotocls=tls12`를 설정 합니다.
* **Servicestack. Redis:** [Servicestack 지침](https://github.com/ServiceStack/ServiceStack.Redis/pull/247)을 따릅니다.

### <a name="net-core"></a>.NET Core

Redis .NET Core 클라이언트는 기본적으로 최신 TLS 버전을 사용 합니다.

### <a name="java"></a>Java

Redis Java 클라이언트는 Java 버전 6 또는 이전 버전에서 TLS 1.0를 사용 합니다. 캐시에서 TLS 1.0을 사용 하지 않도록 설정 된 경우 jedis, Redis 및 Radisson은 Azure Cache for에 연결할 수 없습니다. 현재 알려진 해결 방법이 없습니다.

Java 7 이상에서는 Redis 클라이언트에서 기본적으로 TLS 1.2을 사용 하지 않지만이에 대해 구성할 수 있습니다. 현재이 구성을 지원 하지 않습니다. 캐시에서 TLS 1.2 연결만 수락 하는 경우에는 중단 됩니다. Jedis를 사용 하면 다음 코드 조각을 사용 하 여 기본 TLS 설정을 지정할 수 있습니다.

``` Java
SSLSocketFactory sslSocketFactory = (SSLSocketFactory) SSLSocketFactory.getDefault();
SSLParameters sslParameters = new SSLParameters();
sslParameters.setEndpointIdentificationAlgorithm("HTTPS");
sslParameters.setProtocols(new String[]{"TLSv1", "TLSv1.1", "TLSv1.2"});
 
URI uri = URI.create("rediss://host:port");
JedisShardInfo shardInfo = new JedisShardInfo(uri, sslSocketFactory, sslParameters, null);
 
shardInfo.setPassword("cachePassword");
 
Jedis jedis = new Jedis(shardInfo);
```

### <a name="nodejs"></a>Node.js

Redis 및 IORedis 노드는 기본적으로 TLS 1.2를 사용 합니다.

### <a name="php"></a>PHP

Php 7은 TLS 1.0만 지원 하기 때문에 PHP 7의 Predis 작동 하지 않습니다. PHP 7.2.1 또는 이전 버전에서 Predis는 기본적으로 TLS 1.0 또는 1.1를 사용 합니다. 클라이언트 인스턴스를 만들 때 TLS 1.2을 지정할 수 있습니다.

``` PHP
$redis=newPredis\Client([
    'scheme'=>'tls',
    'host'=>'host',
    'port'=>6380,
    'password'=>'password',
    'ssl'=>[
        'crypto_type'=>STREAM_CRYPTO_METHOD_TLSv1_2_CLIENT,
    ],
]);
```

PHP 7.3 이상에서 Predis은 최신 TLS 버전을 사용 합니다.

PhpRedis는 PHP 버전에서 TLS를 지원 하지 않습니다.

### <a name="python"></a>Python

Redis-py는 기본적으로 TLS 1.2를 사용 합니다.

### <a name="go"></a>GO

Redigo은 기본적으로 TLS 1.2를 사용 합니다.

## <a name="additional-information"></a>추가 정보

- [Redis에 대 한 Azure 캐시를 구성 하는 방법](cache-configure.md)
