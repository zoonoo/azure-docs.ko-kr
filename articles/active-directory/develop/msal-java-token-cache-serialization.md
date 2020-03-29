---
title: 사용자 지정 토큰 캐시 직렬화(MSAL4j)
titleSuffix: Microsoft identity platform
description: Java용 MSAL용 토큰 캐시를 직렬화하는 방법 알아보기
services: active-directory
author: sangonzal
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/07/2019
ms.author: sagonzal
ms.reviewer: nacanuma
ms.custom: aaddev
ms.openlocfilehash: bcb34d83365112b97769186ad74dfd762b05c2e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76696166"
---
# <a name="custom-token-cache-serialization-in-msal-for-java"></a>Java용 MSAL의 사용자 지정 토큰 캐시 직렬화

응용 프로그램의 인스턴스 간에 토큰 캐시를 유지하려면 직렬화를 사용자 지정해야 합니다. 토큰 캐시 직렬화와 관련된 Java 클래스 및 인터페이스는 다음과 같습니다.

- [ITokenCache](https://static.javadoc.io/com.microsoft.azure/msal4j/0.5.0-preview/com/microsoft/aad/msal4j/ITokenCache.html): 보안 토큰 캐시를 나타내는 인터페이스입니다.
- [ITokenCacheAccessAspect](https://static.javadoc.io/com.microsoft.azure/msal4j/0.5.0-preview/com/microsoft/aad/msal4j/ITokenCacheAccessAspect.html): 액세스 전후에 코드를 실행하는 작업을 나타내는 인터페이스입니다. @Override *캐시를* 직렬화하고 역직해제하는 논리를 통해 CacheAccess 및 *afterCacheAccess* 를 사용합니다.
- [ITokenCacheContext](https://static.javadoc.io/com.microsoft.azure/msal4j/0.5.0-preview/com/microsoft/aad/msal4j/ITokenCacheAccessContext.html): 토큰 캐시에 액세스하는 컨텍스트를 나타내는 인터페이스입니다. 

다음은 토큰 캐시 직렬화/직렬화의 사용자 지정 직렬화에 대한 순진한 구현입니다. 복사하여 프로덕션 환경에 붙여넣기 마십시오.

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

[Java용 MSAL을 사용하여 토큰 캐시에서 계정 가져옵니다 및 제거에](msal-java-get-remove-accounts-token-cache.md)대해 알아봅니다.
