---
title: Java 용 MSAL의 사용자 지정 토큰 캐시 serialization
titleSuffix: Microsoft identity platform
description: Java 용 MSAL에 대 한 토큰 캐시를 직렬화 하는 방법 알아보기
services: active-directory
documentationcenter: dev-center-name
author: sangonzal
manager: henrikm
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/07/2019
ms.author: sagonzal
ms.reviewer: navyasri.canumalla
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7274a1b6b5ebf5b55c2cad4b52dfe4c997e26314
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74145894"
---
# <a name="custom-token-cache-serialization-in-msal-for-java-msal4j"></a>Java 용 MSAL의 사용자 지정 토큰 캐시 serialization (MSAL4J)

응용 프로그램 인스턴스 간에 토큰 캐시를 유지 하려면 serialization을 사용자 지정 해야 합니다. 토큰 캐시 serialization과 관련 된 Java 클래스 및 인터페이스는 다음과 같습니다.

- [ITokenCache](https://static.javadoc.io/com.microsoft.azure/msal4j/0.5.0-preview/com/microsoft/aad/msal4j/ITokenCache.html): 보안 토큰 캐시를 나타내는 인터페이스입니다.
- [ITokenCacheAccessAspect](https://static.javadoc.io/com.microsoft.azure/msal4j/0.5.0-preview/com/microsoft/aad/msal4j/ITokenCacheAccessAspect.html): 액세스 전후에 코드를 실행 하는 작업을 나타내는 인터페이스입니다. 캐시 직렬화 및 역직렬화를 담당 하는 논리를 사용 하 여 *beforeCacheAccess* 및 *aftercacheaccess* 를 @Override 합니다.
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
