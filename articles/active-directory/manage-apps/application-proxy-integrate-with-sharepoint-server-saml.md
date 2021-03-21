---
title: 응용 프로그램 프록시를 사용 하 여 온-프레미스 SharePoint에 게시-Azure AD
description: 온-프레미스 SharePoint 서버를 SAML 용 Azure AD 응용 프로그램 프록시와 통합 하는 방법에 대 한 기본 사항을 다룹니다.
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 10/02/2019
ms.author: kenwith
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7cadf5b7d92e26e561e570f824295e69ca421e16
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "101644523"
---
# <a name="integrate-with-sharepoint-saml"></a>SharePoint와 통합(SAML)

이 단계별 가이드에서는 Azure AD 응용 프로그램 프록시를 사용 하 여 [통합 된 온-프레미스 sharepoint (SAML) Azure Active Directory](../saas-apps/sharepoint-on-premises-tutorial.md) 에 대 한 액세스를 보호 하는 방법을 설명 합니다. 여기서 조직의 사용자 (azure AD, B2B)는 인터넷을 통해 Sharepoint에 연결 합니다.

> [!NOTE] 
> Azure AD 응용 프로그램 프록시를 처음 사용 하 고 자세히 알아보려면 [azure AD 응용 프로그램 프록시를 통해 온-프레미스 응용 프로그램에 원격 액세스를](./application-proxy.md)참조 하세요.

이 설정에는 세 가지 주요 이점이 있습니다.

- Azure AD 응용 프로그램 프록시는 인증 된 트래픽이 내부 네트워크와 Sharepoint 서버에 연결할 수 있는지 확인 합니다.
- 사용자는 VPN을 사용 하지 않고 일반적인 방법으로 Sharepoint 사이트에 액세스할 수 있습니다.
- Azure AD 응용 프로그램 프록시 수준에서 사용자 할당을 통해 액세스를 제어할 수 있으며, MFA (조건부 액세스 및 Multi-Factor Authentication)와 같은 Azure AD 기능을 사용 하 여 보안을 강화할 수 있습니다.

이 프로세스에는 두 가지 엔터프라이즈 응용 프로그램이 필요 합니다. 하나는 갤러리에서 관리 되는 SaaS 앱 목록에 게시 하는 SharePoint 온-프레미스 인스턴스입니다. 두 번째는 첫 번째 엔터프라이즈 갤러리 응용 프로그램을 게시 하는 데 사용할 온-프레미스 응용 프로그램 (비 갤러리 응용 프로그램)입니다.

## <a name="prerequisites"></a>필수 구성 요소

이 구성을 완료 하려면 다음 리소스가 필요 합니다.
 - SharePoint 2013 팜 이상 Sharepoint 팜은 [AZURE AD와 통합](../saas-apps/sharepoint-on-premises-tutorial.md)되어야 합니다.
 - 응용 프로그램 프록시를 포함 하는 계획을 포함 하는 Azure AD 테 넌 트. [AZURE AD 요금제 및 가격 책정](https://azure.microsoft.com/pricing/details/active-directory/)에 대해 자세히 알아보세요.
 - Azure AD 테 넌 트에서 [확인 된 사용자 지정 도메인](../fundamentals/add-custom-domain.md) 입니다. 확인 된 도메인은 SharePoint URL 접미사와 일치 해야 합니다.
 - SSL 인증서가 필요 합니다. [사용자 지정 도메인 게시](./application-proxy-configure-custom-domain.md)의 세부 정보를 참조 하세요.
 - 온-프레미스 Active Directory 사용자는 Azure AD Connect와 동기화 해야 하며 [Azure에 로그인](../hybrid/plan-connect-user-signin.md)하도록 구성 해야 합니다. 
 - 클라우드 전용 및 B2B 게스트 사용자의 경우 [Azure Portal에서 SharePoint 온-프레미스에 게스트 계정에 대 한 액세스 권한을 부여](../saas-apps/sharepoint-on-premises-tutorial.md#grant-access-to-a-guest-account-to-sharepoint-on-premises-in-the-azure-portal)해야 합니다.
 - 회사 도메인 내의 컴퓨터에 설치 되어 실행 되는 응용 프로그램 프록시 커넥터입니다.


## <a name="step-1-integrate-sharepoint-on-premises-with-azure-ad"></a>1 단계: Azure AD와 온-프레미스에서 SharePoint 통합 

1. SharePoint 온-프레미스 앱을 구성 합니다. 자세한 내용은 [자습서: Azure Active Directory Single Sign-On SharePoint와 온-프레미스 통합](../saas-apps/sharepoint-on-premises-tutorial.md)을 참조 하세요.
2. 다음 단계로 이동 하기 전에 구성의 유효성을 검사 합니다. 유효성을 검사 하려면 내부 네트워크에서 SharePoint 온-프레미스에 액세스를 시도 하 고 내부적으로 액세스할 수 있는지 확인 합니다. 


## <a name="step-2-publish-the-sharepoint-on-premises-application-with-application-proxy"></a>2 단계: 응용 프로그램 프록시를 사용 하 여 Sharepoint 온-프레미스 응용 프로그램 게시

이 단계에서는 응용 프로그램 프록시를 사용 하는 Azure AD 테 넌 트에서 응용 프로그램을 만듭니다. 외부 URL을 설정 하 고 내부 URL을 지정 합니다. 둘 다 SharePoint에서 나중에 사용 됩니다.

> [!NOTE] 
> 내부 및 외부 Url은 1 단계의 SAML 기반 응용 프로그램 구성에 있는 로그온 **URL** 과 일치 해야 합니다.

   ![로그온 URL 값을 보여 주는 스크린샷](./media/application-proxy-integrate-with-sharepoint-server/sso-url-saml.png)


 1. 사용자 지정 도메인을 사용 하 여 새 Azure AD 응용 프로그램 프록시 응용 프로그램을 만듭니다. 단계별 지침은 [Azure AD 응용 프로그램 프록시의 사용자 지정 도메인](./application-proxy-configure-custom-domain.md)을 참조 하세요.

    - 내부 URL: https://portal.contoso.com/
    - 외부 URL: https://portal.contoso.com/
    - 사전 인증: Azure Active Directory
    - 헤더에서 Url 변환: 아니요
    - 응용 프로그램 본문에서 Url 변환: 아니요

        ![앱을 만드는 데 사용 하는 옵션을 보여 주는 스크린샷](./media/application-proxy-integrate-with-sharepoint-server/create-application-azure-active-directory.png)

2. 온-프레미스 SharePoint 갤러리 응용 프로그램에 할당 한 [동일한 그룹](../saas-apps/sharepoint-on-premises-tutorial.md#create-an-azure-ad-security-group-in-the-azure-portal) 을 할당 합니다.

3. 마지막으로 **속성** 섹션으로 이동 하 여 **사용자에 게 표시** 를 **아니요** 로 설정 합니다. 이 옵션은 첫 번째 응용 프로그램의 아이콘만 내 앱 포털 (에 표시 되도록 https://myapplications.microsoft.com) 합니다.

   ![사용자에 게 표시 되는 위치를 보여 주는 스크린샷 option.](./media/application-proxy-integrate-with-sharepoint-server/configure-properties.png)
 
## <a name="step-3-test-your-application"></a>3 단계: 응용 프로그램 테스트

외부 네트워크의 컴퓨터에서 브라우저를 사용 하 여 https://portal.contoso.com/) 게시 단계 중에 구성한 URL로 이동 합니다. 설정한 테스트 계정으로 로그인 할 수 있는지 확인 합니다.