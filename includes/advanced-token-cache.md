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
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94628023"
---
MSAL의 토큰 캐시 구현을 사용하면 백그라운드 앱, API 및 서비스에서 액세스 토큰 캐시를 사용하여 부재 중에도 사용자를 대신하여 계속 작업할 수 있습니다. 이렇게 하면 사용자가 프런트 엔드 웹앱을 종료한 후에도 백그라운드 앱 및 서비스가 사용자를 대신하여 계속 작업해야 하는 경우에 특히 유용합니다.

오늘날 대부분의 백그라운드 프로세스는 사용자의 데이터를 인증하거나 재인증하기 위해 사용자의 데이터를 사용하지 않고 작업해야 하는 경우 [애플리케이션 권한](/graph/auth/auth-concepts#microsoft-graph-permissions)을 사용합니다. 애플리케이션 권한에는 권한 상승이 필요한 관리자 동의가 필요한 경우가 많기 때문에 개발자가 해당 앱에 대해 사용자가 원래 동의한 권한 이상의 권한을 얻지 않으려는 경우에 불필요한 마찰이 발생합니다.

GitHub의 이 코드 샘플은 백그라운드 앱에서 MSAL의 토큰 캐시에 액세스하여 이러한 불필요한 마찰을 방지하는 방법을 보여줍니다.

 [백그라운드 앱, API 및 서비스에서 로그인한 사용자의 토큰 캐시에 액세스](https://github.com/Azure-Samples/ms-identity-dotnet-advanced-token-cache)