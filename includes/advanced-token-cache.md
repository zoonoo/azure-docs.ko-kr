---
title: 포함 파일
description: 포함 파일
services: active-directory
author: kalyankrishna1
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: include
ms.date: 11/05/2020
ms.author: kkrishna
ms.openlocfilehash: 174946667885debc348370ef2c6f93206890e9c1
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/14/2020
ms.locfileid: "94628023"
---
MSAL의 토큰 캐시 구현을 사용 하면 백그라운드 앱, Api 및 서비스에서 액세스 토큰 캐시를 사용 하 여 사용자를 대신 하 여 계속 작동할 수 있습니다. 이렇게 하면 사용자가 프런트 엔드 웹 앱을 종료 한 후 백그라운드 앱 및 서비스가 사용자를 대신 하 여 계속 작업 해야 하는 경우에 특히 유용 합니다.

현재 대부분의 백그라운드 프로세스는 사용자의 데이터를 인증 또는 다시 인증에 제공 하지 않고 작업 해야 하는 경우 [응용 프로그램 사용 권한을](/graph/auth/auth-concepts#microsoft-graph-permissions) 사용 합니다. 응용 프로그램 사용 권한에는 권한 상승이 필요한 관리자 동의가 필요 하기 때문에 개발자가 자신의 앱에 대해 원래 동의한 된 권한 이상의 권한을 얻지 않으려는 경우 불필요 한 충돌이 발생 합니다.

GitHub의이 코드 샘플에서는 백그라운드 앱에서 MSAL의 토큰 캐시에 액세스 하 여 불필요 한 마찰을 방지 하는 방법을 보여 줍니다.

 [백그라운드 앱, Api 및 서비스에서 로그인 한 사용자의 토큰 캐시에 액세스](https://github.com/Azure-Samples/ms-identity-dotnet-advanced-token-cache)