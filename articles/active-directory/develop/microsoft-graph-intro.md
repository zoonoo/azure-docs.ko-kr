---
title: 마이크로소프트 그래프 API | 마이크로 소프트 문서
description: Microsoft 그래프 API는 Microsoft 클라우드 서비스 리소스에 액세스할 수 있는 RESTful 웹 API입니다.
author: davidmu1
services: active-directory
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 02/13/2020
ms.author: davidmu
ms.custom: aaddev
ms.openlocfilehash: 6c1b4390282f6d54178365714b1e2e665b4cf061
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79136503"
---
# <a name="microsoft-graph-api"></a>Microsoft Graph API

Microsoft 그래프 API는 Microsoft 클라우드 서비스 리소스에 액세스할 수 있는 RESTful 웹 API입니다. 앱을 등록하고 사용자 또는 서비스에 대한 인증 토큰을 받은 후 Microsoft Graph API에 요청할 수 있습니다. 자세한 내용은 [Microsoft 그래프 의 개요를](https://docs.microsoft.com/graph/overview)참조하십시오.

Microsoft 그래프는 REST API 및 클라이언트 라이브러리를 노출하여 다음 Microsoft 365 서비스에서 데이터에 액세스합니다.
- 사무실 365 서비스: 델브, 엑셀, 마이크로소프트 예약, 마이크로소프트 팀, 원 드라이브, 원 노트, 아웃룩/교환, 플래너, 그리고 공유 점
- 엔터프라이즈 모빌리티 및 보안 서비스: 고급 위협 분석, 고급 위협 보호, Azure Active Directory, ID 관리자 및 Intune
- Windows 10 서비스: 활동, 장치, 알림
- Dynamics 365 Business Central

## <a name="versions"></a>버전

Microsoft 그래프는 현재 v1.0 과 베타 버전의 두 가지 버전을 지원합니다. v1.0 버전에는 일반적으로 사용 가능한 API가 포함되어 있습니다. 모든 프로덕션 앱에 v1.0 버전을 사용합니다. 베타 버전에는 현재 미리 보기 중인 API가 포함되어 있습니다. 베타 API에 주요 변경 사항이 도입될 수 있으므로 베타 버전을 사용하여 개발 중인 앱을 테스트하는 것이 좋습니다. 프로덕션 앱에서 베타 API를 사용하지 마십시오. 자세한 내용은 [Microsoft Graph의 버전 검토, 지원 및 주요 변경 정책을](https://docs.microsoft.com/graph/versioning-and-support)참조하십시오.

베타 API 사용을 시작하려면 [Microsoft 그래프 베타 끝점 참조를](https://docs.microsoft.com/graph/api/overview?view=graph-rest-beta) 참조하십시오.

v1.0 API 사용을 시작하려면 [Microsoft 그래프 REST API v1.0 참조를](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0) 참조하십시오.

## <a name="get-started"></a>시작

사용자 또는 전자 메일 메시지와 같은 리소스에서 읽거나 쓰기하려면 다음과 같은 요청을 생성합니다.

`{HTTP method} https://graph.microsoft.com/{version}/{resource}?{query-parameters}`

생성된 요청의 요소에 대한 자세한 내용은 [Microsoft 그래프 API 사용을](https://docs.microsoft.com/graph/use-the-api) 참조하십시오.

빠른 시작 샘플을 사용하여 Microsoft 그래프 API의 전원에 액세스하는 방법을 보여 줄 수 있습니다. 사용할 수 있는 샘플은 하나의 인증을 통해 두 서비스에 액세스합니다: Microsoft 계정 및 Outlook. 각 빠른 시작은 Microsoft 계정 사용자의 프로필에서 정보에 액세스하고 일정의 이벤트를 표시합니다.
빠른 시작에는 다음 네 단계가 포함됩니다.
- 플랫폼 선택
- 앱 ID(클라이언트 ID) 받기
- 샘플 빌드
- 로그인하고 캘린더에서 일정을 볼 수 있습니다.

빠른 시작을 완료하면 실행할 준비가 된 앱이 있습니다. 자세한 내용은 Microsoft [그래프 빠른 시작 FAQ를](https://docs.microsoft.com/graph/quick-start-faq)참조하십시오. 샘플을 시작하려면 Microsoft 그래프 [빠른 시작을](https://developer.microsoft.com/graph/quick-start)참조하십시오.

## <a name="tools"></a>도구

Microsoft 그래프 탐색기는 Microsoft 그래프 API를 사용하여 요청을 빌드하고 테스트하는 데 사용할 수 있는 웹 기반 도구입니다. 다음에서 Microsoft 그래프 탐색기에 액세스할 수 `https://developer.microsoft.com/graph/graph-explorer`있습니다.

우체부또한 마이크로 소프트 그래프 API를 사용하여 요청을 구축하고 테스트하는 데 사용할 수있는 도구입니다. 우체부에서 다운로드할 `https://www.getpostman.com/`수 있습니다. 우체부에서 마이크로소프트 그래프와 상호 작용 하려면, 우체부에서 마이크로소프트 그래프 컬렉션을 사용 하 여. 자세한 내용은 [Microsoft 그래프 API를 사용하여 우체부 사용을](/graph/use-postman?context=graph%2Fapi%2Fbeta&view=graph-rest-beta)참조하십시오.
