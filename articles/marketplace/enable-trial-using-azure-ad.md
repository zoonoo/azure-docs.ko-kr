---
title: Azure Active Directory를 사용하여 Azure Marketplace에서 평가판을 사용할 수 있도록 설정 | Azure
description: 앱 및 서비스 게시자에 대한 Azure Marketplace와 AppSource에서 Azure Active Directory를 사용하여 평가판 목록 형식을 사용하도록 설정합니다.
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
documentationcenter: ''
author: jm-aditi-ms
manager: pabutler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 06/04/2018
ms.author: ellacroi
ms.openlocfilehash: c5b7b4967c1acef733d366e651d50706db42aace
ms.sourcegitcommit: 1478591671a0d5f73e75aa3fb1143e59f4b04e6a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/19/2018
ms.locfileid: "39160470"
---
# <a name="enable-a-trial-listing-by-using-azure-active-directory"></a>Azure Active Directory를 사용하여 평가판 목록 사용

Azure AD(Azure Active Directory)는 업계 표준 프레임워크를 사용하여 Microsoft 회사 또는 학교 계정으로 인증할 수 있도록 하는 클라우드 ID 서비스입니다. Azure AD는 OAuth 및 OpenID Connect 인증을 지원합니다. [Azure Marketplace](https://azuremarketplace.microsoft.com)는 Azure AD를 사용하여 개발자와 고객을 인증합니다.

Azure AD에 대한 자세한 내용은 [Azure Active Directory](https://azure.microsoft.com/services/active-directory)를 참조하세요.

고객이 Marketplace에 올라온 평가판을 선택하면 고객은 해당 평가판 환경으로 리디렉션됩니다. 개발자는 평가판 환경에서 추가 로그인 단계를 수행할 필요가 없도록 직접 고객을 설정할 수 있습니다. 개발자의 앱 또는 제품은 인증 과정에서 Azure AD로부터 토큰을 수신합니다. 이 토큰에는 앱 또는 제품에서 사용자 계정을 만드는 데 사용되는 중요한 사용자 정보가 포함되어 있습니다. 고객 설정을 자동화하여 변환 가능성을 높일 수 있습니다.

인증 과정에서 Azure AD로부터 전송되는 토큰에 대한 자세한 내용은 [토큰 샘플](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims#sample-tokens)을 참조하세요.

Azure AD를 사용하면 앱 또는 평가판에 대한 원클릭 인증이 가능해집니다. Azure AD는 다음과 같은 이점을 제공합니다. 
*   고객 환경을 Marketplace에서 평가판으로 간단히 전환할 수 있습니다.
*   사용자가 Marketplace에서 도메인 또는 체험 환경으로 리디렉션되더라도 제품 내 환경을 사용한다는 느낌을 유지할 수 있습니다.
*   추가 로그인 단계가 없으므로 리디렉션 중에 중단될 가능성이 줄어듭니다.
*   대규모 Azure AD 사용자에 대한 배포 장애물이 줄어듭니다.

## <a name="verify-your-azure-ad-integration-in-the-marketplace-multitenant-apps"></a>Marketplace에서 Azure AD 통합 확인: 다중 테넌트 앱
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

## <a name="verify-your-azure-ad-integration-in-the-marketplace-single-tenant-apps"></a>Marketplace에서 Azure AD 통합 확인: 단일 테넌트 앱
Azure AD를 사용하여 단일 테넌트 솔루션에서 다음 옵션 중 하나를 지원합니다. 
*   Azure AD B2B(Azure Active Directory B2B)를 사용하여 디렉터리에 게스트 사용자로 사용자를 추가합니다.
    
    Azure AD B2B에 대한 자세한 내용은 [Azure AD B2B 공동 작업이란?](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)을 참조하세요.
*   연락처 게시 옵션을 사용하여 고객을 위한 평가판을 수동으로 설정합니다.
*   고객별로 시험 사용을 개발합니다.
*   SSO를 사용하는 다중 테넌트 샘플 데모 앱을 빌드합니다.

## <a name="next-steps"></a>다음 단계
*   [Azure Marketplace 및 AppSource 게시 가이드](./marketplace-publishers-guide.md)를 검토합니다.
