---
title: MSAL4j (사용자 지정 토큰 캐시 serialization)
titleSuffix: Microsoft identity platform
description: Java 용 MSAL에 대 한 토큰 캐시를 직렬화 하는 방법 알아보기
services: active-directory
author: sangonzal
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 11/07/2019
ms.author: sagonzal
ms.reviewer: nacanuma
ms.custom: aaddev, devx-track-java
ms.openlocfilehash: e85cfb8c03111b889a9f95509f337d415c517163
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87312370"
---
# <a name="custom-token-cache-serialization-in-msal-for-java"></a>Java 용 MSAL의 사용자 지정 토큰 캐시 serialization

응용 프로그램 인스턴스 간에 토큰 캐시를 유지 하려면 serialization을 사용자 지정 해야 합니다. 토큰 캐시 serialization과 관련 된 Java 클래스 및 인터페이스는 다음과 같습니다.

- [ITokenCache](https://static.javadoc.io/com.microsoft.azure/msal4j/0.5.0-preview/com/microsoft/aad/msal4j/ITokenCache.html): 보안 토큰 캐시를 나타내는 인터페이스입니다.
- [ITokenCacheAccessAspect](https://static.javadoc.io/com.microsoft.azure/msal4j/0.5.0-preview/com/microsoft/aad/msal4j/ITokenCacheAccessAspect.html): 액세스 전후에 코드를 실행 하는 작업을 나타내는 인터페이스입니다. @Override캐시 직렬화 및 역직렬화를 담당 하는 논리를 사용 하 여 *BeforeCacheAccess* 및 *aftercacheaccess* 를 실행 합니다.
- [Itokencachecontext](https://static.javadoc.io/com.microsoft.azure/msal4j/0.5.0-preview/com/microsoft/aad/msal4j/ITokenCacheAccessContext.html): 토큰 캐시가 액세스 되는 컨텍스트를 나타내는 인터페이스입니다. 

다음은 토큰 캐시 serialization/deserialization의 사용자 지정 serialization의 naive 구현입니다. 이를 복사 하 여 프로덕션 환경에 붙여넣지 마십시오.

```Java
static class TokenPersistence implements ITokenCacheAccessAspect {
String data;

TokenPersistence(String data) {
        this.data = data;
}

@Override
public void beforeCacheAccess(ITokenCacheAccessContext iTokenCacheAccessContext) {
        iTokenCacheAccessContext.tokenCache().deserialize(data);
}

@Override
public void afterCacheAccess(ITokenCacheAccessContext iTokenCacheAccessContext) {
        data = iTokenCacheAccessContext.tokenCache().serialize();
}
```

```Java
// Loads cache from file
String dataToInitCache = readResource(this.getClass(), "/cache_data/serialized_cache.json");

ITokenCacheAccessAspect persistenceAspect = new TokenPersistence(dataToInitCache);

// By setting *TokenPersistence* on the PublicClientApplication, MSAL will call *beforeCacheAccess()* before accessing the cache and *afterCacheAccess()* after accessing the cache. 
PublicClientApplication app = 
PublicClientApplication.builder("my_client_id").setTokenCacheAccessAspect(persistenceAspect).build();
```

## <a name="learn-more"></a>자세한 정보

[Java 용 MSAL을 사용 하 여 토큰 캐시에서 계정을 가져오고 제거 하는](msal-java-get-remove-accounts-token-cache.md)방법에 대해 알아봅니다.
