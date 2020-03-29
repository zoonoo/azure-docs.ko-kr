---
title: 토큰 캐시 (MSAL4j)에서 계정을 제거하기 & | Azure
titleSuffix: Microsoft identity platform
description: Java용 Microsoft 인증 라이브러리를 사용하여 토큰 캐시에서 계정을 보고 제거하는 방법을 알아봅니다.
services: active-directory
author: sangonzal
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/07/2019
ms.author: sagonzal
ms.reviewer: navyasri.canumalla
ms.custom: aaddev
ms.openlocfilehash: 2b138678b186cc41b76254658ad604c2da2d76c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76696200"
---
# <a name="get-and-remove-accounts-from-the-token-cache-using-msal-for-java"></a>Java용 MSAL을 사용하여 토큰 캐시에서 계정 가져옵니다.

Java용 MSAL은 기본적으로 메모리 내 토큰 캐시를 제공합니다. 메모리 내 토큰 캐시는 응용 프로그램 인스턴스의 지속 기간을 유지합니다.

## <a name="see-which-accounts-are-in-the-cache"></a>캐시에 있는 계정 확인

다음 예제와 같이 호출하여 `PublicClientApplication.getAccounts()` 캐시에 있는 계정을 확인할 수 있습니다.

```java
PublicClientApplication pca = new PublicClientApplication.Builder(
                labResponse.getAppId()).
                authority(TestConstants.ORGANIZATIONS_AUTHORITY).
                build();

Set<IAccount> accounts = pca.getAccounts().join();
```

## <a name="remove-accounts-from-the-cache"></a>캐시에서 계정 제거

캐시에서 계정을 제거하려면 제거해야 하는 계정을 찾은 다음 `PublicClientApplicatoin.removeAccount()` 예제와 같이 호출합니다.

```java
Set<IAccount> accounts = pca.getAccounts().join();

IAccount accountToBeRemoved = accounts.stream().filter(
                x -> x.username().equalsIgnoreCase(
                        UPN_OF_USER_TO_BE_REMOVED)).findFirst().orElse(null);

pca.removeAccount(accountToBeRemoved).join();
```

## <a name="learn-more"></a>자세한 정보

Java용 MSAL을 사용하는 경우 [JAVA에 대한 MSAL의 사용자 지정 토큰 캐시 직렬화에 대해](msal-java-token-cache-serialization.md)알아봅니다.
