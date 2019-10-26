---
title: Redis 용 Azure Cache에서 TLS 1.0 및 1.1 사용 제거 | Microsoft Docs
description: Redis 용 Azure Cache와 통신할 때 응용 프로그램에서 TLS 1.0 및 1.1을 제거 하는 방법에 대해 알아봅니다.
services: cache
documentationcenter: ''
author: yegu-ms
manager: maiye
editor: ''
ms.assetid: ''
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache
ms.devlang: na
ms.topic: article
ms.date: 10/22/2019
ms.author: yegu
ms.openlocfilehash: d1d44467d310b87d306ea7401e66784d684a1bf3
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/24/2019
ms.locfileid: "72901899"
---
# <a name="remove-use-of-tls-10-and-11-with-azure-cache-for-redis"></a>Redis 용 Azure Cache에서 TLS 1.0 및 1.1 사용 제거

TLS 1.2 이상을 독점적으로 사용 하는 경우 업계 전체의 푸시가 있습니다. TLS 버전 1.0 및 1.1은 비스 트 및 POODLE 같은 공격에 취약 하 고 다른 일반적인 취약성 및 노출 (CVE) 약점을 보유 하는 것으로 알려져 있습니다. 또한 PCI 준수 표준에서 권장 하는 최신 암호화 방법 및 암호 그룹을 지원 하지 않습니다. 이 [TLS 보안 블로그](https://www.acunetix.com/blog/articles/tls-vulnerabilities-attacks-final-part/) 는 이러한 취약성 중 일부에 대해 자세히 설명 합니다.

이러한 문제를 즉시 해결할 수 있는 것은 아니지만 TLS 1.0 및 1.1을 사용 하는 것에서 최대한 일찍 이동 하는 것도 고려해 야 합니다. Redis 용 Azure Cache는 2020 년 3 월 31 일부 터 이러한 TLS 버전을 지원 하지 않습니다. 응용 프로그램은이 날짜 이후에 캐시와 통신 하기 위해 최소 TLS 1.2 이상을 사용 해야 합니다.

이 문서에서는 응용 프로그램에서 이러한 종속성을 검색 하 고 제거 하는 방법에 대 한 일반적인 지침을 제공 합니다.

## <a name="check-if-your-application-is-already-compliant"></a>응용 프로그램이 이미 호환 되는지 확인

응용 프로그램이 TLS 1.2에서 작동 하는지 여부를 파악 하는 가장 쉬운 방법은 TLS 1.2에 사용 하는 테스트 또는 스테이징 캐시에 최소 TLS 버전을 설정 하는 것입니다. 최소 TLS 버전 설정은 Azure Portal의 캐시 인스턴스 [고급 설정](cache-configure.md#advanced-settings) 에서 찾을 수 있습니다. 이러한 변경 후에도 응용 프로그램이 계속 해 서 작동 하는 경우에는 정책을 준수 하는 것이 가장 쉽습니다. 응용 프로그램에서 사용 하는 일부 Redis 클라이언트 라이브러리는 보안 프로토콜을 통해 Redis 용 Azure 캐시에 연결 하기 위해 TLS 1.2을 사용 하도록 특별히 구성 해야 할 수 있습니다.

## <a name="configure-your-application-to-use-tls-12"></a>TLS 1.2를 사용 하도록 응용 프로그램 구성

대부분의 응용 프로그램은 Redis 클라이언트 라이브러리를 활용 하 여 캐시와의 통신을 처리 합니다. 다음은 TLS 1.2을 사용 하기 위해 다양 한 프로그래밍 언어 및 프레임 워크에서 널리 사용 되는 일부 클라이언트 라이브러리를 구성 하는 방법에 대 한 지침입니다.

### <a name="net-framework"></a>.NET Framework

Redis .NET 클라이언트는 4.6 이상에서 기본적으로 가장 낮은 TLS 버전을 사용 하 고, .NET Framework 4.5.2 이상 및 최고 TLS 버전을 사용 합니다. 이전 버전의 .NET Framework 사용 하는 경우 TLS 1.2을 수동으로 사용 하도록 설정할 수 있습니다.

* Redis: `ssl=true`을 설정 하 고 연결 문자열에 `sslprotocls=tls12` 합니다.
* ServiceStack. Redis: [다음 지침](https://github.com/ServiceStack/ServiceStack.Redis/pull/247)을 따릅니다.

### <a name="net-core"></a>.NET Core

Redis .NET Core 클라이언트는 기본적으로 가장 높은 TLS 버전을 사용 합니다.

### <a name="java"></a>Java

Redis Java 클라이언트는 Java 버전 6 또는 아래에서 TLS 1.0를 사용 합니다. Jedis, Redis 및 Radisson은 캐시에서 TLS 1.0가 사용 되지 않는 경우 Azure Cache for에 연결할 수 없습니다. 현재는 알려진 해결 방법이 없습니다.

Java 7 이상에서는 Redis 클라이언트에서 기본적으로 TLS 1.2을 사용 하지 않지만이에 대해 구성 될 수 있습니다. 현재이 기능을 지원 하지 않습니다. 캐시가 TLS 1.2 연결만 수락 하는 경우에는 중단 됩니다. Jedis를 사용 하면 다음 코드 조각을 사용 하 여 기본 TLS 설정을 지정할 수 있습니다.

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

Predis는 TLS 1.0만 지원 하므로 PHP 7의 경우 작동 하지 않습니다. PHP 7.2.1 또는 아래에서 Predis은 기본적으로 TLS 1.0 또는 1.1를 사용 합니다. 클라이언트를 인스턴스화할 때 TLS 1.2을 지정할 수 있습니다.

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

### <a name="python"></a>파이썬

Redis-py는 기본적으로 TLS 1.2를 사용 합니다.

### <a name="go"></a>이동

Redigo은 기본적으로 TLS 1.2를 사용 합니다.

## <a name="additional-information"></a>추가 정보

- [Redis에 대 한 Azure 캐시를 구성 하는 방법](cache-configure.md)
