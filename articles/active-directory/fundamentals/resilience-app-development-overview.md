---
title: 개발 하는 인증 및 권한 부여 응용 프로그램의 복원 력 향상
titleSuffix: Microsoft identity platform
description: Azure Active Directory 및 Microsoft id 플랫폼을 사용 하 여 응용 프로그램을 개발 하기 위한 복원 력 지침 개요
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
author: knicholasa
ms.author: nichola
manager: martinco
ms.date: 11/23/2020
ms.openlocfilehash: f6078ed96106d6a53b55195ee950e2165334221d
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/25/2020
ms.locfileid: "95920076"
---
# <a name="increase-resilience-of-authentication-and-authorization-applications-you-develop"></a>개발 하는 인증 및 권한 부여 응용 프로그램의 복원 력 향상

Microsoft Id는 최신 토큰 기반 인증 및 권한 부여를 사용 합니다. 즉, 응용 프로그램은 Id 공급자 로부터 토큰을 획득 하 여 사용자를 인증 하 고 응용 프로그램에서 보호 된 Api를 호출할 수 있도록 권한을 부여 합니다.

![Microsoft Id를 호출 하는 응용 프로그램 이미지](media/resilience-app-development-overview/app-overview.png)

토큰은 특정 기간 동안 유효 하며 앱에서 새 토큰을 획득 해야 합니다. 드물지만, 토큰을 검색 하는 호출은 네트워크 또는 인프라 오류 또는 인증 서비스 중단 같은 문제로 인해 실패할 수 있습니다. 이 문서에서는 개발자가 토큰 획득 실패가 발생할 경우 응용 프로그램의 복원 력을 높이기 위해 수행할 수 있는 단계를 간략하게 설명 합니다.

이러한 문서에서는 Microsoft id 플랫폼과 Azure Active Directory를 사용 하 여 앱의 복원 력을 높이는 방법에 대 한 지침을 제공 합니다. 로그인 한 사용자를 대신 하 여 작동 하는 클라이언트 응용 프로그램과 사용자를 대신 하 여 작동 하는 디먼 응용 프로그램에 대 한 지침이 있습니다. 토큰 사용에 대 한 모범 사례와 리소스 호출을 포함 합니다.

- [사용자가 로그인 하는 응용 프로그램에 복원 력 빌드](resilience-client-app.md)
- [사용자 없이 응용 프로그램에 복원 력을 빌드](resilience-daemon-app.md)
- [Id 및 액세스 관리 인프라의 빌드 복원 력](resilience-in-infrastructure.md)
- [Azure Active Directory B2C를 사용 하 여 고객 id 및 액세스 관리에서 복원 력 빌드](resilience-b2c.md)