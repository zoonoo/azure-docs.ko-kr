---
title: Azure AD를 사용하여 평가판 사용 | Azure
description: 앱 및 서비스 게시자에 대한 Azure Marketplace와 AppSource에서 Azure AD(Azure Active Directory)를 사용하여 평가판 목록 형식을 사용하도록 설정
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
ms.openlocfilehash: 4140ba98c0c65c22674c61dc7266818af904e777
ms.sourcegitcommit: 3017211a7d51efd6cd87e8210ee13d57585c7e3b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/06/2018
ms.locfileid: "34826064"
---
# <a name="enable-trial-using-azure-ad"></a>Azure AD를 사용하여 평가판 사용  
Azure AD(Azure Active Directory)는 업계 표준 프레임워크인 OAuth 및 OpenID Connect를 사용하여 Microsoft 회사 또는 학교 계정으로 인증할 수 있도록 하는 클라우드 ID 서비스입니다.  
*   Azure AD에 대한 자세한 내용은 [azure.microsoft.com/services/active-directory](https://azure.microsoft.com/services/active-directory)에 있는 Azure Active Directory 페이지를 방문하세요.  

개발자와 고객은 Azure AD를 사용하여 Marketplace에서 인증됩니다. 고객이 Marketplace에 올라온 평가판을 선택하면 고객은 평가판 환경으로 리디렉션됩니다.  평가판 환경에서 추가 로그인 단계를 수행하지 않도록 직접 고객을 설정할 수 있습니다. 앱 또는 제품은 인증하는 동안 Azure AD에서 토큰을 받게 됩니다. 여기에는 앱 또는 제품에서 사용자 계정을 만드는 데 필요한 중요 사용자 정보가 포함됩니다. 설치 프로그램을 자동화하고 변환의 가능성을 높일 수 있습니다.  
*   인증하는 동안 Azure AD에서 보낸 토큰에 대한 자세한 내용은 [docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims#sample-tokens](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims#sample-tokens)에 있는 샘플 토큰 섹션을 방문하세요.

Azure AD를 사용하면 앱 또는 평가판에 대한 원클릭 인증이 가능해집니다.  
*   고객 환경을 Marketplace에서 평가판으로 간단히 전환할 수 있습니다.  
*   사용자가 Marketplace에서 도메인 또는 체험 환경으로 리디렉션되더라도 제품 내 환경을 사용한다는 느낌을 유지할 수 있습니다.  
*   추가 로그인 단계가 없으므로 리디렉션 중에 중단될 가능성이 줄어듭니다.  
*   대규모 Azure AD 사용자에 대한 배포 장애물이 줄어듭니다.  

## <a name="verify-your-azure-ad-integration-on-the-marketplace-multitenant-apps"></a>Marketplace에 대한 Azure AD 통합 확인: 다중 테넌트 앱  
Azure AD를 사용하는 솔루션에 대해 다음 옵션을 지원합니다.  
*   Marketplace의 상점에 앱을 등록합니다.  
*   Azure AD의 다중 테넌트 지원 기능으로 원클릭 체험 환경을 사용할 수 있습니다.  
    *   앱 등록에 대한 자세한 내용은 [docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)에 있는 Azure Active Directory와 응용 프로그램 통합을 방문하세요.  

Azure AD 페더레이션 SSO(Single Sign-On)를 처음 사용하는 경우 다음 단계를 수행합니다.  
1.  Marketplace에서 앱을 등록합니다. 
2.  OAuth 2.0 또는 OpenID Connect을 사용하여 Azure AD를 통해 SSO를 개발합니다.  
    *   OAuth 2.0에 대한 자세한 내용은 [docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code)에 있는 OAuth 2.0 페이지를 방문하세요.  
    *   Open ID Connect에 대한 자세한 내용은 [docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-openid-connect-code](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-openid-connect-code)에 있는 Open ID Connect 페이지를 방문하세요.  
3.  Azure AD의 다중 테넌트 지원 기능으로 원클릭 체험 환경을 제공할 수 있습니다.  
    *   AppSource 인증에 대한 자세한 내용은 [docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-appsource-certified](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-appsource-certified)에 있는 AppSource 인증 페이지를 방문하세요. 

## <a name="verify-your-azure-ad-integration-on-the-marketplace-single-tenant-apps"></a>Marketplace에 대한 Azure AD 통합 확인: 단일 테넌트 앱  
단일 테넌트 솔루션에 대해 다음 옵션 중 하나를 지원합니다.  
*   Azure AD B2B를 사용하여 디렉터리에 게스트 사용자로 사용자를 추가합니다.  
    *   Azure AD B2B에 대한 자세한 내용은 [docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)에 있는 Azure AD B2B 공동 작업이란? 페이지를 방문하세요.
*   연락처를 사용하여 고객을 위한 평가판을 수동으로 프로비전합니다.  
*   고객별로 시험 사용을 개발합니다.  
*   SSO를 통해 다중 테넌트 샘플 데모 앱을 빌드합니다.  

## <a name="next-steps"></a>다음 단계
*   [Azure Marketplace 및 AppSource 게시자 가이드](./marketplace-publishers-guide.md) 페이지를 방문합니다.  
 
---  

