---
title: 국가별 클라우드에서 Azure AD를 사용하여 인증
description: 국가별 클라우드의 앱 등록 및 인증 엔드포인트에 관해 알아봅니다.
services: active-directory
documentationcenter: ''
author: negoe
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/20/2018
ms.author: negoe
ms.reviewer: negoe,andret,saeeda,CelesteDG
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: a487c233e7bb71f05c6f2181d9c822ca508a583c
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56192951"
---
# <a name="national-clouds"></a>국가별 클라우드

국가별 클라우드(소버린 클라우드라고도 함)는 물리적으로 분리된 Azure 인스턴스입니다. 이러한 Azure 지역은 지리적 경계 내에서 데이터 상주, 주권 및 준수 요구 사항이 적용되도록 설계되었습니다.

글로벌 클라우드를 포함하여 Azure Active Directory는 다음 국가별 클라우드에 배포됩니다.  

- Azure 미국 정부
- Azure Germany
- Azure China 21Vianet

국가별 클라우드는 Azure 글로벌과는 다른 고유한 환경입니다. 그러므로 이러한 환경용으로 애플리케이션을 개발할 때는 애플리케이션 등록, 토큰 받기, 엔드포인트 구성 등의 몇 가지 주요 차이점을 고려해야 합니다.

## <a name="app-registration-endpoints"></a>앱 등록 엔드포인트

각 국가별 클라우드용 개별 Azure Portal이 제공됩니다. 국가별 클라우드에서 Microsoft Identity 플랫폼과 애플리케이션을 통합하려면 환경별 각 Azure Portal에서 애플리케이션을 개별적으로 등록해야 합니다.

다음 표에는 각 국가별 클라우드에 대한 애플리케이션을 등록하는 데 사용되는 Azure AD(Azure Active Directory) 엔드포인트에 대한 기본 URL이 나열되어 있습니다.

| 국가별 클라우드 | Azure AD 포털 엔드포인트
| --- | --- |
| 미국 정부의 Azure AD |https://portal.azure.us
|Azure AD Germany |https://portal.microsoftazure.de
|21Vianet으로 운영되는 Azure AD China |https://portal.azure.cn
|Azure AD(글로벌 서비스)|https://portal.azure.com 

## <a name="azure-ad-authentication-endpoints"></a>Azure AD 인증 엔드포인트

모든 국가별 클라우드는 각 환경의 사용자를 개별적으로 인증하며 별도의 인증 엔드포인트를 포함합니다.

다음 표에는 각 국가별 클라우드용 토큰을 얻는 데 사용되는 Azure AD(Azure Active Directory) 엔드포인트의 기본 URL이 나와 있습니다.

| 국가별 클라우드 | Azure AD 인증 엔드포인트
| --- | --- |
| 미국 정부의 Azure AD |`https://login.microsoftonline.us`
|Azure AD Germany| `https://login.microsoftonline.de`
|21Vianet으로 운영되는 Azure AD China | `https://login.chinacloudapi.cn`
|Azure AD(글로벌 서비스)|`https://login.microsoftonline.com`

- Azure AD 권한 부여 또는 토큰 엔드포인트에 대한 요청은 적절한 지역별 기본 URL을 사용하여 구성할 수 있습니다. 예를 들어 Azure Germany의 경우

  - 권한 부여 공통 엔드포인트는 `https://login.microsoftonline.de/common/oauth2/authorize`입니다.
  - 토큰 공통 엔드포인트는 `https://login.microsoftonline.de/common/oauth2/token`입니다.

- 단일 테넌트 애플리케이션의 경우 위의 URL에서 common을 테넌트 ID 또는 이름(예: `https://login.microsoftonline.de/contoso.com`)으로 바꿉니다.

>[!NOTE]
> [Azure AD v2.0 권한 부여]( https://docs.microsoft.com/azure/active-directory/develop/active-directory-appmodel-v2-overview) 및 토큰 엔드포인트는 글로벌 서비스에만 사용할 수 있습니다. 아직 국가별 클라우드 배포에는 지원되지 않습니다.

## <a name="microsoft-graph-api"></a>Microsoft Graph API

국가별 클라우드 환경에서 Microsoft Graph API를 호출하는 방법을 알아보려면 [국가별 클라우드의 Microsoft Graph](https://developer.microsoft.com/graph/docs/concepts/deployments)를 참조하세요.



>[!IMPORTANT]
글로벌 서비스의 특정 지역에서 제공되는 특정 서비스와 기능이 모든 국가별 클라우드에서 제공되지는 않을 수도 있습니다. 사용 가능한 서비스를 확인하려면 [지역별 사용 가능한 제품](https://azure.microsoft.com/global-infrastructure/services/?products=all&regions=usgov-non-regional,us-dod-central,us-dod-east,usgov-arizona,usgov-iowa,usgov-texas,usgov-virginia,china-non-regional,china-east,china-east-2,china-north,china-north-2,germany-non-regional,germany-central,germany-northeast)을 참조하세요.

## <a name="next-steps"></a>다음 단계

- [Azure Government](https://docs.microsoft.com/azure/azure-government/)에 대해 자세히 알아봅니다.
- [Azure China 21Vianet](https://docs.microsoft.com/azure/china/)에 대해 자세히 알아봅니다.
- [Azure Germany](https://docs.microsoft.com/azure/germany/)에 대해 자세히 알아봅니다.
- Azure AD [인증 기본 사항](authentication-scenarios.md)에 대해 자세히 알아봅니다.
