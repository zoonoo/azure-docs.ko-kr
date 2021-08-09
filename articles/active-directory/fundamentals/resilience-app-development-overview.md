---
title: 개발한 인증 및 권한 부여 애플리케이션의 복원력 향상
titleSuffix: Microsoft identity platform
description: Azure Active Directory와 Microsoft ID 플랫폼을 사용한 애플리케이션 개발을 위한 복원력 지침 개요
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
author: knicholasa
ms.author: nichola
manager: martinco
ms.date: 11/23/2020
ms.openlocfilehash: 2076b2087c7cd90c83e1fe31c024d2c253109ee3
ms.sourcegitcommit: 62e800ec1306c45e2d8310c40da5873f7945c657
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108162188"
---
# <a name="increase-resilience-of-authentication-and-authorization-applications-you-develop"></a>개발한 인증 및 권한 부여 애플리케이션의 복원력 향상

Microsoft ID는 최신형 토큰 기반 인증 및 권한 부여를 사용합니다. 즉, 클라이언트 애플리케이션은 ID 공급자한테서 토큰을 획득하여 사용자를 인증하고 보호된 API를 호출할 수 있도록 애플리케이션에 권한을 부여합니다. 서비스에서 토큰의 유효성을 검사합니다.

토큰은 앱에서 새 토큰을 획득해야 하기 전까지 특정 기간 동안 유효합니다. 드물게, 토큰 검색 호출은 네트워크 또는 인프라 오류, 또는 인증 서비스 중단 같은 문제로 실패할 수 있습니다. 이 문서에서는 토큰 취득 실패가 발생할 경우 개발자가 애플리케이션의 복원력을 높이기 위해 수행할 수 있는 단계를 간략하게 설명합니다.

이 문서에서는 Microsoft ID 플랫폼과 Azure Active Directory를 사용하여 앱의 복원력을 높이는 지침을 제공합니다. 로그인한 사용자를 대신하여 작동하는 클라이언트 및 서비스 애플리케이션과 사용자를 대신하여 작동하는 디먼 애플리케이션에 대한 지침이 있습니다. 여기에는 토큰 사용 모범 사례와 리소스 호출이 포함됩니다.

- [사용자가 로그인하는 애플리케이션의 복원력 빌드](resilience-client-app.md)
- [사용자 없이 애플리케이션의 복원력 빌드](resilience-daemon-app.md)
- [ID 및 액세스 관리 인프라의 복원력 빌드](resilience-in-infrastructure.md)
- [CIAM 시스템에서 복원력 빌드](resilience-b2c.md)
- [메타데이터 새로 고침에 탄력적으로 대응하는 서비스 빌드](../develop/howto-build-services-resilient-to-metadata-refresh.md)
