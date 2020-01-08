---
title: 사용자 지정 토큰 캐시 serialization (MSAL Python) | Microsoft
titleSuffix: Microsoft identity platform
description: Python 용 MSAL에 대 한 토큰 캐시를 직렬화 하는 방법 알아보기
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0b5dc00f1aeb6b4fba987295ff5fc7dfd389b790
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75424093"
---
# <a name="custom-token-cache-serialization-in-msal-for-python"></a>Python 용 MSAL의 사용자 지정 토큰 캐시 serialization

MSAL Python에서는 [Clientapplication](https://msal-python.readthedocs.io/en/latest/#confidentialclientapplication)인스턴스를 만들 때 앱 세션 기간 동안 지속 되는 메모리 내 토큰 캐시가 기본적으로 제공 됩니다.

응용 프로그램의 다른 세션에서 액세스할 수 있도록 토큰 캐시를 직렬화 하는 것은 "기본적으로" 제공 되지 않습니다. 이는 MSAL Python은 웹 앱과 같은 파일 시스템에 대 한 액세스 권한이 없는 앱 유형에 서 사용할 수 있기 때문입니다. MSAL Python 앱에 영구 토큰 캐시를 포함 하려면 사용자 지정 토큰 캐시 직렬화를 제공 해야 합니다.

토큰 캐시를 직렬화 하는 전략은 공용 클라이언트 응용 프로그램 (데스크톱)을 작성 하는지 아니면 기밀 클라이언트 응용 프로그램 (웹 앱, Web API 또는 디먼 앱)을 작성 하는지에 따라 달라 집니다.

## <a name="token-cache-for-a-public-client-application"></a>공용 클라이언트 응용 프로그램에 대 한 토큰 캐시

공용 클라이언트 응용 프로그램은 사용자의 장치에서 실행 되 고 단일 사용자에 대 한 토큰을 관리 합니다. 이 경우 전체 캐시를 파일로 직렬화 할 수 있습니다. 앱 또는 다른 앱이 캐시에 동시에 액세스할 수 있는 경우 파일 잠금을 제공 해야 합니다. 잠금 없이 파일에 토큰 캐시를 serialize 하는 방법에 대 한 간단한 예는 [SerializableTokenCache](https://msal-python.readthedocs.io/en/latest/#msal.SerializableTokenCache) 클래스 참조 설명서의 예제를 참조 하세요.

## <a name="token-cache-for-a-web-app-confidential-client-application"></a>웹 앱에 대 한 토큰 캐시 (기밀 클라이언트 응용 프로그램)

Web Apps 또는 웹 Api의 경우 세션 또는 Redis 캐시를 사용 하거나, 데이터베이스를 사용 하 여 토큰 캐시를 저장할 수 있습니다. 사용자 마다 토큰 캐시가 하나씩 있어야 합니다. 따라서 계정 마다 토큰 캐시를 직렬화 해야 합니다.

## <a name="next-steps"></a>다음 단계

Windows 또는 Linux 웹 앱 또는 Web API에 대 한 토큰 캐시를 사용 하는 방법에 대 한 예제는 [webapp](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/master/app.py#L64-L72) 를 참조 하세요. 예제는 Microsoft Graph API를 호출 하는 웹 앱에 대 한 것입니다.
