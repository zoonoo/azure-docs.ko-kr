---
title: Redis에 대한 Azure 캐시와 함께 사용할 TLS 1.0 및 1.1 제거
description: Redis에 대한 Azure 캐시와 통신할 때 응용 프로그램에서 TLS 1.0 및 1.1을 제거하는 방법에 대해 알아봅니다.
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: yegu
ms.openlocfilehash: 809fbe85a9783777d5dbef86357bd5a386bd6f81
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/13/2020
ms.locfileid: "81261246"
---
# <a name="remove-tls-10-and-11-from-use-with-azure-cache-for-redis"></a>Redis에 대한 Azure 캐시와 함께 사용할 TLS 1.0 및 1.1 제거

TLS(전송 계층 보안) 버전 1.2 이상전용을 향한 업계 전반의 압박이 있습니다. TLS 버전 1.0과 1.1은 BEAST 및 POODLE과 같은 공격에 취약하고 다른 일반적인 취약성 및 노출(CVE) 취약점이 있는 것으로 알려져 있습니다. 또한 PCI(결제 카드 산업) 규정 준수 표준에서 권장하는 최신 암호화 방법 및 암호 제품군을 지원하지 않습니다. 이 [TLS 보안 블로그에서는](https://www.acunetix.com/blog/articles/tls-vulnerabilities-attacks-final-part/) 이러한 취약점 중 일부를 자세히 설명합니다.

이러한 노력의 일환으로 Redis에 대한 Azure 캐시를 다음과 같이 변경합니다.

* **1단계:** 새로 만든 캐시 인스턴스에 대해 기본 최소 TLS 버전을 1.2로 구성합니다. (이 TLS 1.0.로 사용) 기존 캐시 인스턴스는 현재 업데이트되지 않습니다. 필요한 경우 이전 버전과의 호환성을 위해 [최소 TLS 버전을](cache-configure.md#access-ports) 다시 1.0 또는 1.1로 변경할 수 있습니다. 이 변경은 Azure 포털 또는 기타 관리 API를 통해 수행할 수 있습니다.
* **2단계:** TLS 버전 1.0 및 1.1에 대한 지원은 중지됩니다. 이 변경 후 응용 프로그램은 TLS 1.2 이상을 사용하여 캐시와 통신해야 합니다.

또한 이 변경의 일환으로 안전하지 않은 이전 cypher 제품군에 대한 지원이 제거됩니다.  지원되는 Cypher 제품군은 캐시가 최소 TLS 버전인 1.2로 구성될 때 다음으로 제한됩니다.

* TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384_P384
* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256_P256

이 문서에서는 이러한 이전 TLS 버전에 대한 종속성을 검색하고 응용 프로그램에서 제거하는 방법에 대한 일반적인 지침을 제공합니다.

이러한 변경 사항이 적용되는 날짜는 다음과 같습니다.

| 클라우드               | 1단계 시작 날짜 | 2단계 시작 날짜      |
|---------------------|--------------------|-------------------------|
| Azure(전역)      |  2020년 1월 13일  | 2020년 5월 11일(연장) |
| Azure Government    |  2020년 3월 13일    | 2020년 5월 11일            |
| Azure Germany       |  2020년 3월 13일    | 2020년 5월 11일            |
| Azure 중국         |  2020년 3월 13일    | 2020년 5월 11일            |

## <a name="check-whether-your-application-is-already-compliant"></a>응용 프로그램이 이미 규정을 준수하는지 확인

응용 프로그램이 TLS 1.2에서 작동하는지 여부를 확인하는 가장 쉬운 방법은 테스트 또는 스테이징 캐시에서 **최소 TLS 버전** 값을 TLS 1.2로 설정하는 것입니다. **최소 TLS 버전** 설정은 Azure 포털에서 캐시 인스턴스의 고급 [설정에](cache-configure.md#advanced-settings) 있습니다. 이 변경 후에도 응용 프로그램이 예상대로 계속 작동하면 호환될 수 있습니다. TLS 1.2를 사용하도록 설정하기 위해 응용 프로그램에서 사용하는 일부 Redis 클라이언트 라이브러리를 구성해야 해당 보안 프로토콜을 통해 Redis에 대한 Azure 캐시에 연결할 수 있습니다.

## <a name="configure-your-application-to-use-tls-12"></a>TLS 1.2를 사용하도록 응용 프로그램 구성

대부분의 응용 프로그램은 Redis 클라이언트 라이브러리를 사용하여 캐시와의 통신을 처리합니다. 다음은 TLS 1.2를 사용하기 위해 다양한 프로그래밍 언어 및 프레임워크에서 인기 있는 클라이언트 라이브러리 중 일부를 구성하는 방법에 대한 지침입니다.

### <a name="net-framework"></a>.NET Framework

Redis .NET 클라이언트는 .NET Framework 4.5.2 이상에서 기본적으로 초기 TLS 버전을 사용하고 .NET Framework 4.6 이상에서 최신 TLS 버전을 사용합니다. 이전 버전의 .NET Framework를 사용하는 경우 TLS 1.2를 수동으로 활성화할 수 있습니다.

* **스택 익스체인지.재배포:** 설정 `ssl=true` `sslprotocols=tls12` 및 연결 문자열에 있습니다.
* **서비스 스택.재배포:** [ServiceStack.Redis](https://github.com/ServiceStack/ServiceStack.Redis#servicestackredis-ssl-support) 지침을 따르고 최소한 ServiceStack.Redis v5.6이 필요합니다.

### <a name="net-core"></a>.NET Core

Redis .NET Core 클라이언트는 기본적으로 최신 TLS 버전을 사용합니다.

### <a name="java"></a>Java

Redis Java 클라이언트는 Java 버전 6 또는 이전 버전에서 TLS 1.0을 사용합니다. 캐시에서 TLS 1.0을 사용하지 않도록 설정한 경우 제디스, 양상추 및 Redisson은 Redis용 Azure 캐시에 연결할 수 없습니다. Java 프레임워크를 업그레이드하여 새 TLS 버전을 사용합니다.

Java 7의 경우 Redis 클라이언트는 기본적으로 TLS 1.2를 사용하지 않지만 구성할 수 있습니다. 제디스는 다음과 같은 코드 스니펫을 사용하여 기본 TLS 설정을 지정할 수 있습니다.

``` Java
SSLSocketFactory sslSocketFactory = (SSLSocketFactory) SSLSocketFactory.getDefault();
SSLParameters sslParameters = new SSLParameters();
sslParameters.setEndpointIdentificationAlgorithm("HTTPS");
sslParameters.setProtocols(new String[]{"TLSv1.2"});
 
URI uri = URI.create("rediss://host:port");
JedisShardInfo shardInfo = new JedisShardInfo(uri, sslSocketFactory, sslParameters, null);
 
shardInfo.setPassword("cachePassword");
 
Jedis jedis = new Jedis(shardInfo);
```

상추와 Redisson 클라이언트는 아직 TLS 버전 지정을 지원하지 않으므로 캐시가 TLS 1.2 연결만 허용하면 중단됩니다. 이러한 클라이언트에 대한 수정 사항을 검토 중이므로 해당 패키지에서 이 지원으로 업데이트된 버전을 확인하십시오.

Java 8에서 TLS 1.2는 기본적으로 사용되며 대부분의 경우 클라이언트 구성에 대한 업데이트가 필요하지 않습니다. 안전을 위해 응용 프로그램을 테스트합니다.

### <a name="nodejs"></a>Node.js

노드 Redis 및 IORedis는 기본적으로 TLS 1.2를 사용합니다.

### <a name="php"></a>PHP

#### <a name="predis"></a>프레디스 ()프레스티스
 
* PHP 7 보다 이전 버전: Predis만 TLS 1.0을 지원합니다. 이러한 버전은 TLS 1.2에서 작동하지 않습니다. TLS 1.2를 사용하려면 업그레이드해야 합니다.
 
* PHP 7.0 ~ PHP 7.2.1: Predis는 기본적으로 TLS 1.0 또는 1.1만 사용합니다. 다음 해결 방법을 사용하여 TLS 1.2를 사용할 수 있습니다. 클라이언트 인스턴스를 만들 때 TLS 1.2를 지정합니다.

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

* PHP 7.3 이상 버전: Predis는 최신 TLS 버전을 사용합니다.

#### <a name="phpredis"></a>프프리디스

PhpRedis는 PHP 버전에서 TLS를 지원하지 않습니다.

### <a name="python"></a>Python

Redis-py는 기본적으로 TLS 1.2를 사용합니다.

### <a name="go"></a>이동

Redigo는 기본적으로 TLS 1.2를 사용합니다.

## <a name="additional-information"></a>추가 정보

- [Azure Cache for Redis를 구성하는 방법](cache-configure.md)
