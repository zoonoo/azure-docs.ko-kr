---
title: 사용자 지정 토큰 캐시 직렬화(MSAL 파이썬) | Azure
titleSuffix: Microsoft identity platform
description: 파이썬에 대한 MSAL에 대한 토큰 캐시를 직렬화하는 방법에 대해 알아보십시오.
services: active-directory
author: rayluo
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/13/2019
ms.author: rayluo
ms.reviewer: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 2593cc856afb98cf5186c4e33032c5e9151614f0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76704394"
---
# <a name="custom-token-cache-serialization-in-msal-for-python"></a>파이썬에 대한 MSAL의 사용자 정의 토큰 캐시 직렬화

MSAL Python에서 앱 세션 동안 지속되는 메모리 내 토큰 캐시는 [ClientApplication](https://msal-python.readthedocs.io/en/latest/#confidentialclientapplication)의 인스턴스를 만들 때 기본적으로 제공됩니다.

앱의 다른 세션이 액세스할 수 있도록 토큰 캐시의 직렬화는 "즉시" 제공되지 않습니다. MSAL Python은 웹 앱과 같은 파일 시스템에 액세스할 수 없는 앱 유형에서 사용할 수 있기 때문입니다. MSAL Python 앱에 영구 토큰 캐시를 사용하려면 사용자 지정 토큰 캐시 직렬화를 제공해야 합니다.

토큰 캐시를 직렬화하기 위한 전략은 공용 클라이언트 응용 프로그램(데스크톱) 또는 기밀 클라이언트 응용 프로그램(웹 앱, 웹 API 또는 Daemon 앱)을 작성하는지 여부에 따라 다릅니다.

## <a name="token-cache-for-a-public-client-application"></a>공용 클라이언트 응용 프로그램에 대한 토큰 캐시

공용 클라이언트 응용 프로그램은 사용자의 장치에서 실행되고 단일 사용자에 대한 토큰을 관리합니다. 이 경우 전체 캐시를 파일로 직렬화할 수 있습니다. 앱 또는 다른 앱이 동시에 캐시에 액세스할 수 있는 경우 파일 잠금을 제공해야 합니다. 잠금 없이 파일에 토큰 캐시를 직렬화하는 방법에 대한 간단한 예제는 [SerializableTokenCache](https://msal-python.readthedocs.io/en/latest/#msal.SerializableTokenCache) 클래스 참조 설명서의 예제를 참조하십시오.

## <a name="token-cache-for-a-web-app-confidential-client-application"></a>웹 앱의 토큰 캐시(기밀 클라이언트 응용 프로그램)

웹 앱 또는 웹 API의 경우 세션 또는 Redis 캐시 또는 데이터베이스를 사용하여 토큰 캐시를 저장할 수 있습니다. 계정당 토큰 캐시를 직렬화하도록 사용자당 하나의 토큰 캐시가 있어야 합니다.

## <a name="next-steps"></a>다음 단계

Windows 또는 Linux 웹 앱 또는 웹 API에 토큰 캐시를 사용하는 방법에 대한 예는 [ms-IDENTITY-python-webapp을](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/master/app.py#L64-L72) 참조하십시오. 예를 들어 Microsoft 그래프 API를 호출하는 웹 앱입니다.
