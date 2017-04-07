---
title: "Azure Active Directory Premium 시작하기"
description: "볼륨 라이선스 웹 사이트를 통해 Azure Active Directory Premium Edition을 등록하는 방법을 설명하는 항목입니다."
services: active-directory
documentationcenter: 
author: curtand
manager: femila
ms.assetid: 391764e5-c4eb-4ae6-b8a9-3d09f0de04a6
ms.service: active-directory
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/24/2017
ms.author: curtand
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 73cbbce9ae3d727632b8cac2a29c2b1b92332ae7
ms.lasthandoff: 12/08/2016


---
# <a name="getting-started-with-azure-active-directory-premium"></a>Azure Active Directory Premium 시작하기
Active Directory Premium에 등록하는 데는 몇 가지 옵션이 있습니다. 

**Azure 또는 Office 365** - Azure 또는 Office 365 구독자는 Active Directory Premium을 온라인으로 구입할 수 있습니다. 자세한 단계는 [Azure Active Directory Premium을 구입하는 방법 - 기존 고객](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/How-to-Purchase-Azure-Active-Directory-Premium-Existing-Customer) 또는 [Azure Active Directory Premium을 구입하는 방법 - 새로운 고객](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/How-to-Purchase-Azure-Active-Directory-Premium-New-Customers)을 참조하세요.  

**Enterprise Mobility + Security** - Enterprise Mobility + Security(이전의 Enterprise Mobility Suite)는 조직이 Active Directory Premium, Azure Rights Management, Microsoft Intune 서비스를 하나의 라이선싱 계획으로 함께 사용할 수 있는 비용 효율적인 방법입니다. 자세한 내용은 [Enterprise Mobility + Security](https://www.microsoft.com/en-us/server-cloud/enterprise-mobility/overview.aspx) 웹 사이트를 참조하세요. 무료 30일 평가판을 얻으려면 [여기](https://portal.office.com/Signup/Signup.aspx?OfferId=2E63A04D-BE0B-4A0F-A8CF-407C1C299221&dl=EMS&ali=1#0)를 클릭합니다.

**Microsoft 볼륨 라이선스** - Azure Active Directory Premium은 [Microsoft 기업 계약](https://www.microsoft.com/en-us/licensing/licensing-programs/enterprise.aspx)(250개 이상의 라이선스) 또는 [오픈 볼륨 라이선스](https://www.microsoft.com/en-us/licensing/licensing-programs/open-license.aspx)(5–250개 라이선스) 프로그램을 통해 사용할 수 있습니다.

이 항목에서는 볼륨 라이선스 프로그램을 통해 구입한 Azure Active Directory Premium을 시작하는 방법을 보여 줍니다. 아직 다른 버전의 Azure Active Directory에 익숙하지 않은 경우 [Azure Active Directory Edition](active-directory-editions.md)을 참조하세요.  

> [!NOTE]
> 중국 고객의 경우 전 세계의 Azure Active Directory 인스턴스를 사용하여 Azure Active Directory Premium 및 Basic 버전을 사용할 수 있습니다. Azure Active Directory Premium 및 Basic 버전은 현재 중국 21Vianet이 운영하는 Microsoft Azure에서 지원되지 않습니다. 자세한 내용은 [Azure Active Directory 포럼](https://feedback.azure.com/forums/169401-azure-active-directory/)을 통해 문의하세요.
> 
> 

## <a name="step-1-sign-up-for-active-directory-premium"></a>1단계: Active Directory Premium 등록
등록하려면 [볼륨 라이선스를 통해 구입하는 방법](http://www.microsoft.com/en-us/licensing/how-to-buy/how-to-buy.aspx)을 참조하세요.

## <a name="step-2-activate-your-license-plan"></a>2단계: 라이선스 계획 활성화
Microsoft의 엔터프라이즈 볼륨 라이선스 프로그램을 통해 라이선스 계획을 처음으로 구입하셨나요?
이 경우 구입을 완료하면 확인 전자 메일을 받습니다.
첫 번째 라이선스 계획을 활성화하려면 이 전자 메일이 필요합니다.

향후 이 디렉터리를 구입하면, 라이선스가 동일한 디렉터리에서 자동으로 활성화됩니다.

**라이선스 계획을 활성화하려면 다음 단계 중 하나를 수행합니다.**

1. 활성화를 시작하려면 **로그인** 또는 **등록**을 클릭합니다.
   
    ![로그인][1]

    - 기존 테넌트가 있으면 **로그인** 을 클릭하여 기존 관리자 계정으로 로그인합니다. 반드시 라이선스가 활성화된 디렉터리에서 전역 관리자 자격 증명으로 로그인해야 합니다.

    - 라이선싱 계획을 사용하기 위해 새 Azure Active Directory 테넌트를 만들려는 경우에는 **등록**을 클릭하고 **계정 프로필 만들기** 대화 상자를 엽니다.

        ![계정 프로필 만들기][2]

완료한 후에는 테넌트에 대한 라이선스 계획의 활성화에 대한 확인으로 다음과 같은 대화 상자가 표시됩니다.

![확인][3]

## <a name="step-3-activate-your-azure-active-directory-access"></a>3단계: Azure Active Directory 액세스 활성화
이전에 Microsoft Azure를 사용한 적이 있는 경우 [4단계](#step-4-assign-license-to-user-accounts)를 진행하면 됩니다. 

라이선스가 사용자의 디렉터리로 프로비전되면 **환영 전자 메일** 이 사용자에게 전송됩니다. 전자 메일은 Azure Active Directory Premium 또는 Enterprise Mobility Suite 라이선스 및 기능에 대한 관리를 시작할 수 있다는 것을 확인해 줍니다. 

환영 전자 메일을 받기 전에 Azure Active Directory에 액세스를 활성화하려고 하면 다음과 같은 오류 메시지가 나타납니다. 

![액세스를 사용할 수 없음][9]

메일을 받으면 잠시 후에 다시 시도해 보세요.

구독의 새 관리자도 이 링크를 통해 Azure 클래식 포털에 대한 자신의 액세스 권한을 활성화할 수 있습니다.

**Azure Active Directory 액세스를 활성화하려면 다음 단계를 수행합니다.**

1. **환영 전자 메일**에서 **로그인**을 클릭합니다. 
   
    ![환영 전자 메일][4]
2. 로그인한 후에는 모바일 확인 형태로 두 번째 단계 인증을 완료해야 합니다.
   
    ![모바일 확인][5]

활성화는 몇 분 정도 걸릴 수 있습니다. 액세스가 활성화되면, 밤색 표시줄이 사라지고 **포털**을 클릭할 수 있게 됩니다.

![설정하는 동안 잠시 기다려 주십시오.][6]

이 경우 Azure 액세스 권한은 Azure Active Directory로 제한됩니다.

![Azure 기능][7]

이미 이전에 사용할 때부터 Azure에 대한 액세스 권한이 있었을 수 있습니다. 또한 추가 Azure 구독을 활성화하여 액세스 권한을 Azure Active Directory에서 전체 Azure로 업그레이드할 수도 있습니다. 이 경우 Azure 클래식 포털에 더 많은 기능이 있습니다.

![Azure 기능][8]

## <a name="step-4-assign-license-to-user-accounts"></a>4단계: 사용자 계정에 라이선스 할당
구입한 계획을 사용하려면 먼저 Premium에서 제공하는 다양한 기능을 사용할 수 있도록 조직 내에서 사용자 계정에 라이선스를 수동으로 할당해야 합니다. Azure Active Directory Premium 기능을 사용할 수 있도록 사용자에게 라이선스를 할당하려면 다음 단계를 따르세요.

**사용자에게 라이선스를 할당하려면 다음 단계를 수행합니다.**

1. 사용자 지정할 디렉터리의 전역 관리자로 Azure 클래식 포털에 로그인합니다.
2. **Active Directory**를 클릭한 후 라이선스를 할당하려는 디렉터리를 선택합니다.
3. **라이선스** 탭을 선택하고 **Active Directory Premium**이나 **Enterprise Mobility Suite**를 선택한 후 **할당**을 클릭합니다.
   
    ![라이선스 계획][10]
4. 대화 상자에서 라이선스를 할당하려는 사용자를 선택 하고 확인 표시 아이콘을 클릭하여 변경 내용을 저장합니다.
   
    ![라이선스 할당][11]

### <a name="license-restrictions"></a>라이선스 제한 사항
일부 라이선스 계획은 다른 라이선스 계획의 하위 집합이거나 상위 집합입니다. 일반적으로, 사용자에게 이미 할당된 라이선스 계획은 사용자에게 할당할 수 없습니다. 상위 집합 라이선스 계획을 할당하려는 경우 먼저 하위 집합 라이선스 계획을 제거해야 합니다.

### <a name="license-requirements"></a>라이선스 요구 사항
사용자에게 라이선스를 할당할 때, 계정 속성에서 기본 사용 위치를 지정할 수 있습니다. 사용 위치를 지정하지 않으면 테넌트의 위치가 사용자에게 자동으로 할당됩니다.

![사용자 위치][12]

Microsoft 클라우드 서비스의 서비스 및 기능에 대한 가용성은 국가 또는 지역에 따라 다릅니다. VoIP(Voice over Internet Protocol) 등의 서비스는 일부 국가나 지역에서만 사용할 수 있습니다. 서비스 내의 기능이 특정 국가 또는 지역에서는 법적인 이유로 제한될 수 있습니다. 서비스나 기능을 제한 없이 또는 제한적으로 사용할 수 있는지 알아보려면 서비스에 대한 라이선스 제한 사항 사이트에서 해당 국가나 지역을 확인하세요.

## <a name="whats-next"></a>다음 단계
* [로그인 및 액세스 패널 페이지에 회사 브랜딩 추가하기](active-directory-add-company-branding.md)
* [액세스 및 사용 보고서 보기](active-directory-view-access-usage-reports.md)

<!--Image references-->
[1]: ./media/active-directory-get-started-premium/MOLSEmail.png
[2]: ./media/active-directory-get-started-premium/MOLSAccountProfile.png
[3]: ./media/active-directory-get-started-premium/MOLSThankYou.png
[4]: ./media/active-directory-get-started-premium/AADEmail.png
[5]: ./media/active-directory-get-started-premium/SignUppage.png
[6]: ./media/active-directory-get-started-premium/Subscriptionspage.png
[7]: ./media/active-directory-get-started-premium/Premiuminportal.png
[8]: ./media/active-directory-get-started-premium/Premiuminportal_large.png
[9]: ./media/active-directory-get-started-premium/Signuppage_oops.png
[10]: ./media/active-directory-get-started-premium/contosolicenseplan.png
[11]: ./media/active-directory-get-started-premium/Assignlicensespicker.png
[12]: ./media/active-directory-get-started-premium/Usagelocation.png

