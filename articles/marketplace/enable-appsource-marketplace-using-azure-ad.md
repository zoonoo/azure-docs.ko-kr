---
title: Azure Active Directory를 사용하여 Microsoft AppSource와 Azure Marketplace 목록을 사용하도록 설정 | Azure
description: 앱 및 서비스 게시자의 Azure Marketplace와 AppSource에서 Azure Active Directory를 사용하여 열거 형식을 사용하도록 설정합니다.
services: Azure, AppSource, Marketplace, Compute, Storage, Networking, Blockchain, Security
documentationcenter: ''
author: qianw211
manager: pabutler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 09/12/2018
ms.author: qianw211
ms.openlocfilehash: d7fd09928c0a687755d216e7f10f7eac23677c63
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/18/2018
ms.locfileid: "45987344"
---
# <a name="enable-a-microsoft-appsource-and-azure-marketplace-listing-by-using-azure-active-directory"></a>Azure Active Directory를 사용하여 Microsoft AppSource와 Azure Marketplace 목록을 사용하도록 설정

Microsoft Azure AD(Azure Active Directory)는 업계 표준 프레임워크를 사용하여 Microsoft 계정으로 인증할 수 있도록 하는 클라우드 ID 서비스입니다.  Azure AD에 대한 자세한 내용은 [Azure Active Directory](https://azure.microsoft.com/services/active-directory)를 참조하세요.

## <a name="benefits-of-using-azure-active-directory"></a>Azure Active Directory 사용의 이점

Microsoft AppSource 및 Azure Marketplace 고객은 제품 내 환경을 사용하여 제품에 로그인하는 데 필요한 목록 카탈로그를 검색합니다.  Azure AD와 응용 프로그램을 통합하면 참여를 촉진하고 고객 환경을 최적화할 수 있습니다. Azure AD:

- 수백만 명의 엔터프라이즈 사용자에 SSO(Single Sign-On)를 사용할 수 있습니다.
- 여러 파트너가 게시한 전체 응용 프로그램에서 일관된 사용자 로그온 환경을 사용할 수 있습니다.
- 모바일 및 클라우드 앱의 확장성 있는 플랫폼 간 인증을 제공합니다.

아래 섹션에 자세히 설명된 대로 Marketplace에 게시할 Azure AD를 구현하려면 특정 제품이 필요합니다.

## <a name="azure-active-directory-requirements"></a>Azure Active Directory 요구 사항

Microsoft AppSource 및 Azure Marketplace의 여러 가지 [목록 옵션 및 제품 유형](https://docs.microsoft.com/azure/marketplace/determine-your-listing-type)이 있습니다.  이러한 목록 옵션 및 제품 유형의 Azure AD 요구 사항은 다음과 같습니다.

| **제품 유형**    | **AAD SSO 필요?**  |  |   |  |
| :------------------- | :-------------------|:-------------------|:-------------------|:-------------------|
|  | 연락처 | 평가판 | 시험 사용 | 거래 |
| Virtual Machine | 해당 없음 | 아니요 | 아니요 | 아니요 |
| Azure 앱(솔루션 템플릿)  | 해당 없음 | 해당 없음 | 해당 없음 | 해당 없음 |
| 관리되는 앱  | 해당 없음 | 해당 없음 | 해당 없음 | 아니요 |
| SaaS  | 아니요 | 예 | 예 | yes |
| 컨테이너  | 해당 없음 | 해당 없음 | 해당 없음 | 아니요 |
| 컨설팅 서비스  | 아니요 | 해당 없음 | 해당 없음 | 해당 없음 |

SaaS 기술 요구 사항에 대한 자세한 내용은 [SaaS 응용 프로그램 제품 게시 가이드](https://docs.microsoft.com/azure/marketplace/marketplace-saas-applications-technical-publishing-guide)를 참조하세요.

## <a name="integration-with-azure-active-directory"></a>Azure Active Directory와 통합

Azure AD와 통합하여 SSO를 사용하는 방법에 대한 자세한 내용을 보려면 https://aka.ms/aaddev를 방문하세요.

Azure AD SSO에 대한 자세한 내용은 [Azure Active Directory를 사용한 응용 프로그램 액세스 및 Single Sign-On이란 무엇인가요?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)를 참조하세요.

## <a name="enable-a-trial-listing-by-using-azure-active-directory"></a>Azure Active Directory를 사용하여 평가판 목록 사용

고객이 Marketplace에 올라온 평가판을 선택하면 고객은 해당 평가판 환경으로 리디렉션됩니다. 개발자는 평가판 환경에서 추가 로그인 단계를 수행할 필요가 없도록 직접 고객을 설정할 수 있습니다. 개발자의 앱 또는 제품은 인증 과정에서 Azure AD로부터 토큰을 수신합니다. 이 토큰에는 앱 또는 제품에서 사용자 계정을 만드는 데 사용되는 중요한 사용자 정보가 포함되어 있습니다. 고객 설정을 자동화하여 변환 가능성을 높일 수 있습니다.

인증 과정에서 Azure AD로부터 전송되는 토큰에 대한 자세한 내용은 [토큰 샘플](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims#sample-tokens)을 참조하세요.

Azure AD를 사용하면 앱 또는 평가판에 대한 원클릭 인증이 가능해집니다. Azure AD는 다음과 같은 이점을 제공합니다. 
*   고객 환경을 Marketplace에서 평가판으로 간단히 전환할 수 있습니다.
*   사용자가 Marketplace에서 도메인 또는 체험 환경으로 리디렉션되더라도 제품 내 환경을 사용한다는 느낌을 유지할 수 있습니다.
*   추가 로그인 단계가 없으므로 리디렉션 중에 중단될 가능성이 줄어듭니다.
*   대규모 Azure AD 사용자에 대한 배포 장애물이 줄어듭니다.

### <a name="verify-your-azure-ad-integration-in-the-marketplace-multitenant-apps"></a>Marketplace에서 Azure AD 통합 확인: 다중 테넌트 앱
Azure AD를 사용하여 솔루션에서 다음 옵션을 지원합니다.
*   Marketplace의 상점에 앱을 등록합니다.
*   Azure AD의 다중 테넌트 지원 기능으로 원클릭 체험 환경을 사용할 수 있습니다.

앱 등록에 대한 자세한 내용은 [Azure Active Directory와 응용 프로그램 통합](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)을 참조하세요.

Azure AD 페더레이션 SSO(Single Sign-On)를 처음 사용하는 경우 다음 단계를 완료하세요.
1.  Marketplace에서 앱을 등록합니다. 
2.  OAuth 2.0 또는 OpenID Connect을 사용하여 Azure AD를 통해 SSO를 개발합니다.
    *   OAuth 2.0에 대한 자세한 내용은 [OAuth 2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code)을 참조하세요.
    *   Open ID Connect에 대한 자세한 내용은 [OpenID Connect](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-openid-connect-code)를 참조하세요.
3.  Azure AD의 다중 테넌트 지원 기능으로 원클릭 체험 환경을 제공할 수 있습니다.
    
    AppSource 인증에 대한 자세한 내용은 [AppSource 인증](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-appsource-certified)을 참조하세요. 

### <a name="verify-your-azure-ad-integration-in-the-marketplace-single-tenant-apps"></a>Marketplace에서 Azure AD 통합 확인: 단일 테넌트 앱
Azure AD를 사용하여 단일 테넌트 솔루션에서 다음 옵션 중 하나를 지원합니다. 
*   Azure AD B2B(Azure Active Directory B2B)를 사용하여 디렉터리에 게스트 사용자로 사용자를 추가합니다. Azure AD B2B에 대한 자세한 내용은 [Azure AD B2B 공동 작업이란?](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)을 참조하세요.
*   연락처 게시 옵션을 사용하여 고객을 위한 평가판을 수동으로 설정합니다.
*   고객별로 시험 사용을 개발합니다.
*   SSO를 사용하는 다중 테넌트 샘플 데모 앱을 빌드합니다.

## <a name="next-steps"></a>다음 단계

아직 수행하지 않았다면 
- Marketplace에 [등록](https://azuremarketplace.microsoft.com/sell).

새 제품을 등록하고 만들거나 기존에서 작업하는 경우
- 제품을 만들거나 완료하기 위해 [Cloud 파트너 포털에 로그인](https://cloudpartner.azure.com/).

