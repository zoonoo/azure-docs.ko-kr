---
title: Microsoft Graph API
description: Microsoft Graph API는 Microsoft 클라우드 서비스 리소스에 액세스할 수 있도록 하는 RESTful 웹 API입니다.
author: davidmu1
services: active-directory
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 02/13/2020
ms.author: davidmu
ms.custom: aaddev
ms.openlocfilehash: fb6f5c4840f127ac174b6b60359dd52a4797b3ec
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/11/2020
ms.locfileid: "88119677"
---
# <a name="microsoft-graph-api"></a>Microsoft Graph API

Microsoft Graph API는 Microsoft 클라우드 서비스 리소스에 액세스할 수 있도록 하는 RESTful 웹 API입니다. 앱을 등록 하 고 사용자 또는 서비스에 대 한 인증 토큰을 가져온 후 Microsoft Graph API에 대 한 요청을 수행할 수 있습니다. 자세한 내용은 [Microsoft Graph 개요](/graph/overview)를 참조 하세요.

Microsoft Graph는 REST Api 및 클라이언트 라이브러리를 노출 하 여 다음 Microsoft 365 서비스의 데이터에 액세스 합니다.
- Office 365 서비스: Delve, Excel, Microsoft 예약, Microsoft 팀, OneDrive, OneNote, Outlook/Exchange, Planner 및 SharePoint
- Enterprise Mobility and Security services: Advanced Threat Analytics, Advanced Threat Protection, Azure Active Directory, Identity Manager 및 Intune
- Windows 10 서비스: 활동, 장치, 알림
- Dynamics 365 Business Central

## <a name="versions"></a>버전

현재 Microsoft Graph는 두 가지 버전인 v1.0과 베타를 지원 합니다. V1.0 버전에는 일반적으로 사용할 수 있는 Api가 포함 되어 있습니다. 모든 프로덕션 앱에 대해 v 1.0 버전을 사용 합니다. 베타에는 현재 미리 보기로 제공 되는 Api가 포함 되어 있습니다. 베타 Api에 대 한 주요 변경 내용을 도입할 수 있으므로 개발 중인 앱을 테스트 하는 데만 베타 버전을 사용 하는 것이 좋습니다. 프로덕션 앱에서 베타 Api를 사용 하지 마세요. 자세한 내용은 [Microsoft Graph에 대 한 버전 관리, 지원 및 주요 변경 정책](/graph/versioning-and-support)(영문)을 참조 하세요.

베타 Api 사용을 시작 하려면 [Microsoft Graph 베타 끝점 참조](/graph/api/overview?view=graph-rest-beta) 를 참조 하세요.

V1.0 Api 사용을 시작 하려면 [Microsoft Graph REST API v1.0 참조](/graph/api/overview?view=graph-rest-1.0) 를 참조 하세요.

## <a name="get-started"></a>시작하기

사용자 또는 전자 메일 메시지와 같은 리소스를 읽거나 쓰려면 다음과 같은 요청을 생성 합니다.

`{HTTP method} https://graph.microsoft.com/{version}/{resource}?{query-parameters}`

생성 된 요청의 요소에 대 한 자세한 내용은 [MICROSOFT GRAPH API 사용](/graph/use-the-api) 을 참조 하세요.

Microsoft Graph API의 기능에 액세스 하는 방법을 보여 주는 빠른 시작 샘플을 사용할 수 있습니다. 사용 가능한 샘플은 인증을 사용 하 여 두 서비스에 액세스할 수 있습니다 (Microsoft 계정 및 Outlook). 각 빠른 시작은 Microsoft 계정 사용자의 프로필의 정보에 액세스 하 여 일정의 이벤트를 표시 합니다.
퀵 스타트에는 네 단계가 포함 됩니다.
- 플랫폼 선택
- 앱 ID 가져오기 (클라이언트 ID)
- 샘플 빌드
- 로그인 및 일정의 이벤트 보기

빠른 시작을 완료 하면 실행할 준비가 된 앱이 있습니다. 자세한 내용은 [Microsoft Graph 빠른 시작 FAQ](/graph/quick-start-faq)를 참조 하세요. 예제를 시작 하려면 [Microsoft Graph 빠른](https://developer.microsoft.com/graph/quick-start)시작을 참조 하세요.

## <a name="tools"></a>도구

Microsoft Graph 탐색기는 Microsoft Graph Api를 사용 하 여 요청을 빌드 및 테스트 하는 데 사용할 수 있는 웹 기반 도구입니다. 에서 Microsoft Graph 탐색기에 액세스할 수 있습니다 `https://developer.microsoft.com/graph/graph-explorer` .

Postman은 Microsoft Graph Api를 사용 하 여 요청을 빌드 및 테스트 하는 데 사용할 수 있는 도구입니다. Postman은에서 다운로드할 수 있습니다 `https://www.getpostman.com/` . Postman의 Microsoft Graph 상호 작용 하려면 Postman에서 Microsoft Graph 컬렉션을 사용 합니다. 자세한 내용은 [MICROSOFT GRAPH API를 사용 하 여 Postman 사용](/graph/use-postman?context=graph%2Fapi%2Fbeta&view=graph-rest-beta)을 참조 하세요.