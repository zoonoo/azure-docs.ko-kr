---
title: 국가별 클라우드에서 Azure AD를 사용하여 인증
description: 국가별 클라우드의 앱 등록 및 인증 엔드포인트에 관해 알아봅니다.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/24/2018
ms.author: negoe
ms.reviewer: negoe,andret,saeeda
ms.custom: aaddev
ms.openlocfilehash: 866a86178d66b7b4af069d684e4eb56c12db47ca
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46982004"
---
# <a name="national-clouds"></a>국가별 클라우드

국가별 클라우드(소버린 클라우드라고도 함)는 물리적으로 분리된 Azure 인스턴스입니다. 이러한 Azure 지역은 지리적 경계 내에서 데이터 상주, 주권 및 준수 요구 사항이 적용되도록 설계되었습니다.

글로벌 클라우드를 포함하여 Azure Active Directory는 다음 국가별 클라우드에 배포됩니다.  

- Azure 미국 정부
- Azure Germany
- Azure China 21Vianet

## <a name="app-registration-endpoints"></a>앱 등록 엔드포인트

다음 표에는 각 국가별 클라우드에 대한 응용 프로그램을 등록하는 데 사용되는 Azure AD(Azure Active Directory) 엔드포인트에 대한 기본 URL이 나열되어 있습니다.

| 국가별 클라우드 | Azure AD 포털 엔드포인트
| --- | --- |
| 미국 정부의 Azure AD |https://portal.azure.us
|Azure AD Germany |https://portal.microsoftazure.de
|21Vianet으로 운영되는 Azure AD China |https://portal.azure.cn
|Azure AD(글로벌 서비스)|https://portal.azure.com

## <a name="azure-ad-authentication-endpoints"></a>Azure AD 인증 엔드포인트

다음 표에는 각 국가별 클라우드에 대한 Microsoft Graph의 토큰을 확보하는 데 사용되는 Azure AD(Azure Active Directory) 엔드포인트에 대한 기본 URL이 나열되어 있습니다.

| 국가별 클라우드 | Azure AD 인증 엔드포인트
| --- | --- |
| 미국 정부의 Azure AD |`https://login.microsoftonline.us`
|Azure AD Germany| `https://login.microsoftonline.de`
|21Vianet으로 운영되는 Azure AD China | `https://login.chinacloudapi.cn`
|Azure AD(글로벌 서비스)|`https://login.microsoftonline.com`

Azure AD 권한 부여 또는 토큰 엔드포인트에 대한 요청은 적절한 지역별 기본 URL을 사용하여 구성할 수 있습니다. 예를 들어 독일의 경우 다음과 같습니다.

- 권한 부여 공통 엔드포인트는 `https://login.microsoftonline.de/common/oauth2/authorize`입니다.
- 토큰 공통 엔드포인트는 `https://login.microsoftonline.de/common/oauth2/token`입니다. 

단일 테넌트 응용 프로그램의 경우 위의 URL에서 공통을 테넌트 ID 또는 이름(예: `https://login.microsoftonline.de/contoso.com`)으로 바꿉니다.

>[!NOTE]
> [Azure AD v2.0 권한 부여]( https://docs.microsoft.com/azure/active-directory/develop/active-directory-appmodel-v2-overview) 및 토큰 엔드포인트는 글로벌 서비스에만 사용할 수 있습니다. 아직 국가별 클라우드 배포에는 지원되지 않습니다.

## <a name="next-steps"></a>다음 단계

- [Azure Government](https://docs.microsoft.com/azure/azure-government/)에 대해 자세히 알아보기
- [Azure China 21Vianet](https://docs.microsoft.com/azure/china/)에 대해 자세히 알아보기
- [Azure Germany](https://docs.microsoft.com/azure/germany/)에 대해 자세히 알아보기
- [Azure AD 인증 기본 사항](authentication-scenarios.md)에 대해 자세히 알아보기
- [국가별 클라우드에 Microsoft Graph 배치](https://developer.microsoft.com/graph/docs/concepts/deployments)에 대해 자세히 알아보기