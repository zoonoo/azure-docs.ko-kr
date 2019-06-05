---
title: National 클라우드에서 Azure Active Directory를 사용 하 여 인증
description: 국가별 클라우드의 앱 등록 및 인증 엔드포인트에 관해 알아봅니다.
services: active-directory
documentationcenter: ''
author: negoe
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: negoe
ms.reviewer: negoe,CelesteDG
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: fd37366697a9c1f5019d2864e6d81a4dcd02e3a2
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66235483"
---
# <a name="national-clouds"></a>내셔널 클라우드

내셔널 클라우드는 Azure의 물리적으로 격리 된 인스턴스입니다. 이러한 Azure 지역은 지리적 경계 내에서 데이터 상주, 주권 및 준수 요구 사항이 적용되도록 설계되었습니다.

글로벌 클라우드를 비롯 하 여 Azure Active Directory (Azure AD)는 다음 국가별 클라우드에 배포 됩니다.  

- Azure Government
- Azure Germany
- Azure China 21Vianet

내셔널 클라우드는 고유 하 고 Azure 전역에서 별도 환경입니다. 이러한 환경에 대 한 응용 프로그램을 개발 하는 동안 주요 차이점에 주의 하는 것이 반드시 합니다. 차이점에는 응용 프로그램 등록 토큰을 가져오고 끝점 구성 포함 됩니다.

## <a name="app-registration-endpoints"></a>앱 등록 엔드포인트

내셔널 클라우드 중 각각에 대해 별도 Azure 포털을 있습니다. 국가 클라우드에서 Microsoft id 플랫폼을 사용 하 여 응용 프로그램에 통합 하려면 환경에 관련 된 각 Azure portal에서 응용 프로그램을 별도로 등록 해야 하 고 있습니다.

다음 표에서 각 국가별 클라우드 용 응용 프로그램을 등록 하는 데 사용 하는 Azure AD 끝점에 대 한 기본 Url을 나열 합니다.

| 국가별 클라우드 | Azure AD 포털 엔드포인트 |
|----------------|--------------------------|
| 미국 정부의 Azure AD | `https://portal.azure.us` |
| Azure AD Germany | `https://portal.microsoftazure.de` |
| 21Vianet으로 운영되는 Azure AD China | `https://portal.azure.cn` |
| Azure AD(글로벌 서비스) |`https://portal.azure.com` |

## <a name="azure-ad-authentication-endpoints"></a>Azure AD 인증 엔드포인트

모든 국가별 클라우드는 각 환경의 사용자를 개별적으로 인증하며 별도의 인증 엔드포인트를 포함합니다.

다음 표에서 각 국가 클라우드에 대 한 토큰을 획득 하는 데 사용 하는 Azure AD 끝점에 대 한 기본 Url을 보여 줍니다.

| 국가별 클라우드 | Azure AD 인증 끝점 |
|----------------|-------------------------|
| 미국 정부의 Azure AD | `https://login.microsoftonline.us` |
| Azure AD Germany| `https://login.microsoftonline.de` |
| 21Vianet으로 운영되는 Azure AD China | `https://login.chinacloudapi.cn` |
| Azure AD(글로벌 서비스)| `https://login.microsoftonline.com` |

적절 한 지역별 기본 URL을 사용 하 여 Azure AD 권한 부여 또는 토큰 끝점에 요청을 구성할 수 있습니다. 예를 들어 Azure Germany의 경우

  - 권한 부여 공통 엔드포인트는 `https://login.microsoftonline.de/common/oauth2/authorize`입니다.
  - 토큰 공통 엔드포인트는 `https://login.microsoftonline.de/common/oauth2/token`입니다.

단일 테 넌 트 응용 프로그램의 경우 테 넌 트 ID 또는 이름을 이전 Url에 "common"를 대체 합니다. 예는 `https://login.microsoftonline.de/contoso.com`입니다.

> [!NOTE]
> 합니다 [Azure AD v2.0 인증]( https://docs.microsoft.com/azure/active-directory/develop/active-directory-appmodel-v2-overview) 되며 토큰 끝점 글로벌 서비스에 대해서만 사용할 수 있습니다. 국가별 클라우드 배포에 대 한 지원 되지는 않지만 합니다.

## <a name="microsoft-graph-api"></a>Microsoft Graph API

국가별 클라우드 환경에서 Microsoft Graph Api를 호출 하는 방법을 알아보려면로 이동 [국가별 클라우드 배포에서 Microsoft Graph](https://developer.microsoft.com/graph/docs/concepts/deployments)합니다.

> [!IMPORTANT]
> 특정 서비스 및 글로벌 서비스의 특정 지역에 있는 기능 하지 못할 모든 내셔널 클라우드입니다. 어떤 서비스가 가능한 지를 확인 하려면로 이동 [지역별 사용 가능한 제품](https://azure.microsoft.com/global-infrastructure/services/?products=all&regions=usgov-non-regional,us-dod-central,us-dod-east,usgov-arizona,usgov-iowa,usgov-texas,usgov-virginia,china-non-regional,china-east,china-east-2,china-north,china-north-2,germany-non-regional,germany-central,germany-northeast)합니다.

Microsoft id 플랫폼을 사용 하 여 응용 프로그램을 빌드하는 방법에 알아보려면 다음을 수행 합니다 [Microsoft 인증 라이브러리 (MSAL) 자습서](msal-national-cloud.md)합니다. 특히이 앱 사용자를 로그인 및 Microsoft Graph API를 호출 하는 액세스 토큰 가져오기 됩니다.

## <a name="next-steps"></a>다음 단계

다음에 대해 자세히 알아봅니다.

- [Azure Government](https://docs.microsoft.com/azure/azure-government/)
- [Azure China 21Vianet](https://docs.microsoft.com/azure/china/)
- [Azure 독일](https://docs.microsoft.com/azure/germany/)
- [Azure AD 인증 기본 사항](authentication-scenarios.md)
