---
title: Java 용 MSAL을 사용 하 여 토큰 캐시에서 계정 가져오기 및 제거 (MSAL4j)
titleSuffix: Microsoft identity platform
description: Java 용 Microsoft 인증 라이브러리를 사용 하 여 토큰 캐시에서 계정을 보고 제거 하는 방법에 대해 알아봅니다.
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
ms.openlocfilehash: fa6c4c53d04f227db7a9a83946182c109dc06d39
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2019
ms.locfileid: "74452489"
---
# <a name="get-and-remove-accounts-from-the-token-cache-using-msal-for-java"></a>Java 용 MSAL을 사용 하 여 토큰 캐시에서 계정 가져오기 및 제거

Java 용 MSAL은 기본적으로 메모리 내 토큰 캐시를 제공 합니다. 메모리 내 토큰 캐시는 응용 프로그램 인스턴스 기간 동안 지속 됩니다.

## <a name="see-which-accounts-are-in-the-cache"></a>캐시에 있는 계정 확인

다음 예제와 같이 `PublicClientApplication.getAccounts()`를 호출 하 여 캐시에 있는 계정을 확인할 수 있습니다.

```java
PublicClientApplication pca = new PublicClientApplication.Builder(
                labResponse.getAppId()).
                authority(TestConstants.ORGANIZATIONS_AUTHORITY).
                build();

Set<IAccount> accounts = pca.getAccounts().join();
```

## <a name="remove-accounts-from-the-cache"></a>캐시에서 계정 제거

캐시에서 계정을 제거 하려면 제거 해야 하는 계정을 찾은 후 다음 예제와 같이 `PublicClientApplicatoin.removeAccount()`를 호출 합니다.

```java
Set<IAccount> accounts = pca.getAccounts().join();

IAccount accountToBeRemoved = accounts.stream().filter(
                x -> x.username().equalsIgnoreCase(
                        UPN_OF_USER_TO_BE_REMOVED)).findFirst().orElse(null);

pca.removeAccount(accountToBeRemoved).join();
```

## <a name="learn-more"></a>자세한 정보

Java 용 MSAL을 사용 하는 경우 [java 용 msal의 사용자 지정 토큰 캐시 serialization](msal-java-token-cache-serialization.md)에 대해 알아봅니다.
