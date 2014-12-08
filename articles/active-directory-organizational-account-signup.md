<properties umbracoNaviHide="0" pageTitle="How To Configure Cloud Services" metaKeywords="Azure cloud services, cloud service, configure cloud service" description="Learn how to configure Azure cloud services." linkid="manage-windows-how-to-guide-storage-accounts" urlDisplayName="How to: storage accounts" headerExpose="" footerExpose="" disqusComments="1" title="Sign up for Azure as an organization" authors="terrylan" manager="terrylan" />

<tags ms.service="active-directory" ms.workload="identity" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="terrylan" />

# 조직으로 Azure 등록

[WACOM.INCLUDE [disclaimer](../includes/disclaimer.md)]

얼마 전까지만 해도 Microsoft 계정(Windows Live ID)을 사용해서만 새 Azure 구독에 등록할 수 있었습니다. 이제 Azure에서는 다음 두 계정 방법 중 하나를 사용하여 등록할 수 있습니다.

-   **Microsoft 계정**(개인 용도로 직접 생성함) - 모든 소비자 지향 Microsoft 제품 및 클라우드 서비스(예: Outlook(Hotmail), Messenger, OneDrive, MSN, Xbox LIVE 또는 Office Live)에 액세스할 수 있습니다. Outlook에 등록하면 @Outlook.com 주소로 지정된 Microsoft 계정이 자동으로 만들어집니다. 이 Microsoft 계정을 사용하여 소비자 관련 Microsoft 클라우드 서비스 및/또는 Azure에 액세스할 수 있습니다. [자세한 정보][자세한 정보]

-   **조직 계정**(업무/교육 기관용으로 관리자가 생성함) - 중소기업 및 엔터프라이즈 비즈니스 수준의 Microsoft 클라우드 서비스(예: Azure, Windows Intune 또는 Office 365)에 액세스할 수 있습니다. 이러한 서비스 중 하나에 조직으로 등록하면 클라우드 기반 테넌트가 자동으로 Azure Active Directory에서 프로비전되어 조직을 대표합니다. [자세한 정보][1]

    이 테넌트가 생성되면 관리자가 각 직원/학생에게 조직 계정을 발급하고 각자가 액세스해야 하는 클라우드 서비스(예: Azure) 구독에 따라 조직 계정에 라이선스를 할당할 수 있습니다.

    *조직으로 Azure를 등록하려면* **지금 등록하십시오.**

## Azure Active Directory 정의

Active Directory가 온-프레미스 디렉터리 관리를 위해 Windows Server 운영 체제를 통해 고객에게 제공되는 서비스인 것과 유사하게 Azure AD(Azure Active Directory)는 조직의 클라우드 디렉터리를 관리할 수 있도록 Azure를 통해 제공되는 서비스입니다. [자세한 정보][2]

조직의 클라우드 디렉터리이기 때문에 대상 사용자, 클라우드에 보관할 정보, 정보를 사용하거나 관리할 수 있는 사람 및 정보에 액세스할 수 있는 응용 프로그램 또는 서비스를 직접 결정합니다. Azure AD를 사용하는 경우 조직 정보의 개인 정보 보호 및 보안에 대한 요구 사항을 준수하는 동시에 고확장성, 고가용성 및 통합된 재해 복구로 클라우드에서 Active Directory를 실행하는 것은 Microsoft의 책임입니다.

### 온-프레미스 Active Directory와 통합

조직에서 온-프레미스 Active Directory를 이미 사용하는 경우 Azure AD의 디렉터리 통합 기능(예: 디렉터리 동기화 및 Single Sign-On)을 사용하여 기존 온-프레미스 ID 범위를 클라우드로 확장함으로써 관리자 및 최종 사용자 환경을 개선할 수 있습니다. [자세한 정보][3]

  [자세한 정보]: http://windows.microsoft.com/ko-kr/windows-live/sign-in-what-is-microsoft-account
  [1]: http://technet.microsoft.com/ko-KR/library/jj573650
  [2]: http://technet.microsoft.com/ko-KR/library/hh967619
  [3]: http://technet.microsoft.com/ko-KR/library/jj573653
