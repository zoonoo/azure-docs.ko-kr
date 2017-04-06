---
title: "Azure AD 디렉터리 관리 | Microsoft Docs"
description: "Azure AD 테넌트의 정의 및 Azure Active Directory를 통해 Azure를 관리하는 방법을 설명합니다."
services: active-directory
documentationcenter: 
author: curtand
writer: markvi
manager: femila
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 04/06/2017
ms.author: curtand
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 001ffc0f9c7465552392a9848ef1487a4c0eafce
ms.lasthandoff: 12/08/2016


---
# <a name="administer-your-azure-ad-directory"></a>Azure AD 디렉터리 관리
## <a name="what-is-an-azure-ad-tenant"></a>Azure AD 테넌트란?
실제 작업 공간에서 테넌트라는 단어는 특정 건물을 사용하는 그룹이나 회사로 정의할 수 있습니다. 예를 들어 조직은 특정 건물에 있는 사무실 공간을 소유할 수 있습니다. 이 건물은 다른 여러 조직과 같은 거리에 있을 수 있습니다. 이 경우 조직은 해당 건물의 테넌트로 간주됩니다. 이 건물은 조직의 자산이며 보안을 제공하여 안전하게 비즈니스를 수행할 수 있도록 합니다. 또한 같은 거리에 있는 다른 회사와 구분됩니다. 따라서 조직과 조직 내 자산이 다른 조직으로부터 격리됩니다.

클라우드 기반 작업 공간에서 테넌트는 해당 클라우드 서비스의 특정 인스턴스를 소유하고 관리하는 클라이언트나 조직으로 정의할 수 있습니다. Microsoft Azure에서 제공하는 ID 플랫폼을 사용하는 테넌트는 조직이 Azure 또는 Office 365와 같은 Microsoft 클라우드 서비스에 등록할 때 받고 소유하는 Azure AD(Azure Active Directory)의 전용 인스턴스일 뿐입니다.

각 Azure AD 디렉터리는 고유하며 다른 Azure AD 디렉터리와 구분됩니다. 회사 사무실 건물이 해당 조직에게만 속하는 안전한 자산인 것처럼 Azure AD 디렉터리도 조직에서만 사용하기 위해 자산을 보호하도록 설계되었습니다. Azure AD 아키텍처는 고객 데이터 및 ID 정보가 함께 혼합되지 않도록 격리합니다. 즉, 한 Azure AD 디렉터리의 사용자 및 관리자가 실수로 또는 악의적으로 다른 디렉터리의 데이터에 액세스할 수 없습니다.

![Azure Active Directory 관리][1]

## <a name="how-can-i-get-an-azure-ad-directory"></a>Azure AD 디렉터리를 받으려면 어떻게 하나요?
Azure AD는 다음을 포함하여 대부분의 Microsoft 클라우드 서비스 뒤에 핵심 디렉터리 및 ID 관리 기능을 제공합니다.

* Azure
* Microsoft Office 365
* Microsoft Dynamics CRM Online
* Microsoft Intune

Azure AD 디렉터리는 이러한 Microsoft 클라우드 서비스에 등록하면 제공됩니다. 필요에 따라 추가 디렉터리를 만들 수 있습니다. 예를 들어 첫 번째 디렉터리는 프로덕션 디렉터리로 유지 관리하고 테스트나 준비용으로 다른 디렉터리를 만들 수 있습니다.

> [!NOTE]
> 첫 번째 서비스에 등록한 후 다른 Microsoft 클라우드 서비스에 등록할 때 조직과 연결된 동일한 관리자 계정을 사용하는 것이 좋습니다.
> 
> 

처음으로 Microsoft 클라우드 서비스에 등록하면 조직 및 조직의 인터넷 도메인 이름 등록에 대한 정보를 제공하라는 메시지가 표시됩니다. 이 정보는 조직의 새 Azure AD 디렉터리 인스턴스를 만드는 데 사용됩니다. 이 동일한 디렉터리는 여러 Microsoft 클라우드 서비스를 구독하는 경우 로그인 시도를 인증하는 데 사용됩니다.

추가 서비스에서는 구성된 기존 사용자 계정, 정책, 설정 또는 온-프레미스 디렉터리 통합을 완벽하게 활용하여 조직의 ID 인프라 온-프레미스와 Azure AD 간의 효율성을 향상시킬 수 있습니다.

예를 들어 처음에 Microsoft Intune 구독에 등록하고 디렉터리 동기화 및/또는  single sign-on 서버를 배포하여 Azure AD 디렉터리와 온-프레미스 Active Directory를 추가로 통합하는 데 필요한 단계를 완료한 경우 Office 365와 같은 다른 Microsoft 클라우드 서비스에 등록하여 Microsoft Intune에서 사용하는 것과 동일한 디렉터리 통합을 활용할 수도 있습니다.

Azure AD와 온-프레미스 디렉터리 통합에 대한 자세한 내용은 [디렉터리 통합](active-directory-aadconnect.md)을 참조하세요.

### <a name="associate-an-azure-ad-directory-with-a-new-azure-subscription"></a>새 Azure 구독과 Azure AD 디렉터리 연결
기존 Office 365 또는 Microsoft Intune 구독에 대한 로그인을 인증하는 동일한 디렉터리와 새 Azure 구독을 연결할 수 있습니다. 회사 또는 학교 계정을 사용하여 Azure 관리 포털에 로그인합니다. 관리 포털에서 해당 계정에 대한 구독을 찾을 수 없다는 메시지를 반환합니다. **Azure에 등록**을 선택하면 포털 내에서 디렉터리를 관리할 수 있습니다. 자세한 내용은 [Azure에서 Office 365 구독의 디렉터리 관리](active-directory-how-subscriptions-associated-directory.md#manage-the-directory-for-your-office-365-subscription-in-azure)를 참조하세요.

일반적인 Azure AD 사용 관련 질문에 대한 비디오는 [Azure Active Directory - 일반적인 등록, 로그인 및 사용 관련 질문](http://channel9.msdn.com/Series/Windows-Azure-Active-Directory/WAADCommonSignupsigninquestions)(영문)을 참조하세요.

### <a name="create-an-azure-ad-directory-by-signing-up-for-a-microsoft-cloud-service-as-an-organization"></a>조직으로 Microsoft 클라우드 서비스에 등록하여 Azure AD 디렉터리 만들기
아직 Microsoft 클라우드 서비스에 대한 구독이 없는 경우 다음 링크 중 하나를 사용하여 등록합니다. 첫 번째 서비스에 등록하면 Azure AD 디렉터리가 자동으로 만들어집니다.

* [Microsoft Azure](https://account.windowsazure.com/organization)
* [Office 365](http://products.office.com/business/compare-office-365-for-business-plans/)
* [Microsoft Intune](https://account.manage.microsoft.com/Signup/MainSignUp.aspx?OfferId=40BE278A-DFD1-470a-9EF7-9F2596EA7FF9&ali=1)

### <a name="manage-an-azure-provisioned-default-directory"></a>Azure 프로비전 기본 디렉터리 관리
지금은 Azure에 등록하면 디렉터리가 자동으로 만들어지고 구독이 해당 디렉터리에 연결됩니다. 그러나 2013년 10월 이전에는 Azure에 처음 등록해도 디렉터리가 자동으로 만들어지지 않았습니다. 이 경우 Azure에서 기본 디렉터리를 프로비전하여 계정을 “백필"했을 수 있습니다. 그런 다음 해당 기본 디렉터리에 구독을 연결했습니다.

2013년 10 월 디렉터리 백필은 Azure에 대한 보안 모델의 전체적인 기능 향상의 일부로 수행되었습니다. 이를 통해 모든 Azure 고객에게 조직 ID 기능을 제공할 수 있으며 디렉터리의 사용자 컨텍스트에서 모든 Azure 리소스에 액세스할 수 있습니다. 디렉터리가 없으면 Azure를 사용할 수 없습니다. 따라서 2013년 7월 7일 이전에 등록했지만 디렉터리가 없는 사용자는 디렉터리를 만들어야 했습니다. 디렉터리를 이미 만든 경우에는 구독을 해당 디렉터리에 연결했습니다.

Azure AD 사용에 따른 비용은 없습니다. 디렉터리는 무료 리소스입니다. 별도로 라이선스가 제공되고 회사 브랜딩 및 셀프 서비스 암호 재설정과 같은 추가 기능을 제공하는 Azure Active Directory Premium 계층이 있습니다.

디렉터리의 표시 이름을 변경하려면 포털에서 디렉터리를 클릭하고 **구성**을 클릭합니다. 이 항목의 뒷부분에서 설명한 대로 새 디렉터리를 추가하거나 더 이상 필요 없는 디렉터리를 삭제할 수 있습니다. 구독을 다른 디렉터리와 연결하려면 왼쪽 탐색 창에서 **설정** 확장을 클릭하고 **구독** 페이지의 맨 아래에서 **디렉터리 편집**을 클릭합니다. 또한 기본 *.onmicrosoft.com 도메인 대신 등록한 DNS 이름을 사용하여 사용자 지정 도메인을 만들 수 있으며, SharePoint Online과 같은 서비스에 적합할 수 있습니다.

## <a name="how-can-i-manage-directory-data"></a>디렉터리 데이터를 관리하는 방법
여러 Microsoft 클라우드 서비스 구독의 관리자는 Azure 관리 포털, Microsoft Intune 계정 포털 또는 Office 365 관리 센터를 사용하여 조직 디렉터리 데이터를 관리할 수 있습니다. 또한 [Windows PowerShell용 Microsoft Azure Active Directory 모듈](https://msdn.microsoft.com/library/azure/jj151815.aspx) cmdlet을 다운로드하고 실행하여 Azure AD에 저장된 데이터를 관리할 수 있습니다.

이러한 포털(또는 cmdlet)에서는 다음을 수행할 수 있습니다.

* 사용자 및 그룹 계정 만들기 및 관리
* 조직이 구독하는 관련 클라우드 서비스 관리
* 디렉터리 서비스와 온-프레미스 통합 설정

Azure 관리 포털, Office 365 관리 센터, Microsoft Intune 계정 포털 및 Azure AD cmdlet은 모두 다음 그림에 표시된 대로 조직의 디렉터리와 연결된 Azure AD의 단일 공유 인스턴스에서 읽고 씁니다. 이러한 방식으로 포털(또는 cmdlet)은 디렉터리 데이터를 가져오거나 수정하는 프런트 엔드 인터페이스 역할을 합니다.

![][2]

사용자 및 그룹을 관리하는 데 사용되는 이러한 계정 포털 및 관련 Azure AD PowerShell cmdlet은 Azure AD 플랫폼 위에 구축됩니다.

이러한 서비스 중 하나의 컨텍스트에서 로그인한 동안 포털(또는 cmdlet)을 사용하여 조직 데이터를 변경할 경우 이 변경 내용은 다음에 해당 서비스 컨텍스트에서 로그인하면 다른 포털에도 표시되는데, 이 데이터는 구독하는 Microsoft 클라우드 서비스 전체에서 공유되기 때문입니다.
예를 들어 Office 365 관리 센터를 사용하여 사용자의 로그인을 차단한 경우 조직에서 현재 구독하는 다른 모든 서비스에 대한 사용자의 로그인도 차단됩니다. Microsoft Intune 계정 포털 컨텍스트에서 동일한 사용자의 계정을 가져오려고 하면 사용자가 차단되었다고 표시됩니다.

## <a name="how-can-i-add-and-manage-multiple-directories"></a>여러 디렉터리를 추가하고 관리하려면 어떻게 하나요?
Azure 관리 포털에서 Azure AD 디렉터리를 추가할 수 있습니다. 왼쪽에서 **Active Directory** 확장을 선택하고 **추가**를 클릭합니다.

각 디렉터리를 완전히 독립된 리소스로 관리할 수 있습니다. 각 디렉터리는 관리하는 다른 디렉터리와 논리적으로 독립된 완전한 기능을 갖춘 피어이며, 디렉터리 간에 부모-자식 관계가 없습니다. 디렉터리 간 독립성에는 리소스 독립성, 관리 독립성 및 동기화 독립성이 포함됩니다.

* **리소스 독립성**. 한 디렉터리에서 리소스를 만들거나 삭제해도 다른 디렉터리의 리소스에 영향을 주지 않습니다. 단, 아래에 설명된 외부 사용자는 부분적으로 제외됩니다. 한 디렉터리에서 사용자 지정 도메인 'contoso.com'을 사용하는 경우 다른 디렉터리에서 사용할 수 없습니다.
* **관리 독립성**.  'Contoso' 디렉터리의 관리자가 아닌 사용자가 테스트 디렉터리 ‘Test’를 만들면 다음과 같이 수행됩니다.
  
  * 데이터를 단일 AD 포리스트와 동기화하는 디렉터리 동기화 도구.
  * 'Contoso' 디렉터리의 관리자는 'Test'의 관리자가 'Test' 디렉터리에 대한 관리 권한을 특별히 부여하지 않는 한 이 디렉터리에 대한 직접 관리 권한이 없습니다. 'Contoso'의 관리자는 'Test'를 만든 사용자 계정에 대한 제어 권한을 통해 'Test' 디렉터리에 대한 액세스를 제어할 수 있습니다.
    
    또한 한 디렉터리의 사용자에 대한 관리자 역할을 변경(추가 또는 제거)하는 경우 이 변경 내용은 사용자가 다른 디렉터리에 대해 가질 수 있는 관리자 역할에 영향을 주지 않습니다.
* **동기화 독립성**. 각 Azure AD를 독립적으로 구성하여 다음 중 하나의 단일 인스턴스에서 데이터를 동기화할 수 있습니다.
  
  * 데이터를 단일 AD 포리스트와 동기화하는 디렉터리 동기화 도구
  * 데이터를 하나 이상의 온-프레미스 포리스트 및/또는 AD 이외의 데이터 소스와 동기화하는 Forefront Identity Manager용 Azure Active Directory 커넥터.

또한 다른 Azure 리소스와 달리 디렉터리는 Azure 구독의 자식 리소스가 아닙니다. 따라서 Azure 구독을 취소하거나 만료를 허용하는 경우에도 Azure AD PowerShell, Azure Graph API 또는 Office 365 관리 센터와 같은 다른 인터페이스를 사용하여 디렉터리 데이터에 계속 액세스할 수 있습니다. 다른 구독을 디렉터리와 연결할 수도 있습니다.

## <a name="how-can-i-delete-an-azure-ad-directory"></a>Azure AD 디렉터리를 삭제하려면 어떻게 하나요?
전역 관리자는 포털에서 Azure AD 디렉터리를 삭제할 수 있습니다. 디렉터리가 삭제되면 디렉터리에 포함된 모든 리소스도 삭제됩니다. 따라서 디렉터리를 삭제하기 전에 필요 없는 디렉터리인지 확인해야 합니다.

> [!NOTE]
> 사용자가 회사 또는 학교 계정을 사용하여 로그인하는 경우 사용자는 자신의 홈 디렉터리를 삭제하려고 하면 안 됩니다. 예를 들어 사용자가 joe@contoso.onmicrosoft.com,으로 로그인하는 경우 해당 사용자는 contoso.onmicrosoft.com을 기본 도메인으로 사용하는 디렉터리를 삭제할 수 없습니다.
> 
> 

### <a name="conditions-that-must-be-met-to-delete-an-azure-ad-directory"></a>Azure AD 디렉터리를 삭제하려면 충족해야 하는 조건
Azure AD에서 디렉터리를 삭제하려면 특정 조건을 충족해야 합니다. 이렇게 하면 Office 365에 로그인하거나 Azure의 리소스에 액세스하는 사용자의 기능 등 디렉터리 삭제가 사용자나 응용 프로그램에 부정적인 영향을 주는 위험을 줄일 수 있습니다. 예를 들어 구독에 대한 디렉터리가 의도하지 않게 삭제된 경우 사용자가 해당 구독에 대한 Azure 리소스에 액세스할 수 없습니다.

다음과 같은 조건을 확인합니다.

* 디렉터리의 유일한 사용자가 디렉터리를 삭제할 전역 관리자입니다. 디렉터리를 삭제하려면 먼저 다른 모든 사용자를 삭제해야 합니다. 사용자가 온-프레미스에서 동기화된 경우 동기화를 해제해야 하며 관리 포털 또는 Windows PowerShell용 Azure 모듈을 사용하여 클라우드 디렉터리에서 사용자를 삭제해야 합니다. Office 365 관리 센터에서 추가된 연락처와 같은 그룹이나 연락처를 삭제하는 요구 사항은 없습니다.
* 디렉터리에 응용 프로그램이 없을 수 있습니다. 디렉터리를 삭제하려면 먼저 응용 프로그램을 모두 삭제해야 합니다.
* Microsoft Azure, Office 365 또는 Azure AD Premium 등 Microsoft Online Services에 대한 구독이 디렉터리에 연결되어 있지 않습니다. 예를 들어 Azure에서 직접 기본 디렉터리를 만든 경우 Azure 구독에서 인증에 이 디렉터리를 계속 사용하면 이 디렉터리를 삭제할 수 없습니다. 마찬가지로 다른 사용자가 구독을 연결한 경우 디렉터리를 삭제할 수 없습니다. 구독을 다른 디렉터리와 연결하려면 Azure 관리 포털에 로그인하고 왼쪽 탐색 영역에서 **설정** 을 클릭합니다. 그런 다음 **구독** 페이지의 맨 아래에서 **디렉터리 편집**을 클릭합니다. Azure 구독에 대한 자세한 내용은 [Azure 구독과 Azure AD의 연관 관계](active-directory-how-subscriptions-associated-directory.md)를 참조하세요.

> [!NOTE]
> 사용자가 회사 또는 학교 계정을 사용하여 로그인하는 경우 사용자는 자신의 홈 디렉터리를 삭제하려고 하면 안 됩니다. 예를 들어 사용자가 joe@contoso.onmicrosoft.com,으로 로그인하는 경우 해당 사용자는 contoso.onmicrosoft.com을 기본 도메인으로 사용하는 디렉터리를 삭제할 수 없습니다.
> 
> 

* 디렉터리에 연결된 Multi-Factor Authentication 공급자가 없습니다.

## <a name="additional-resources"></a>추가 리소스
* [Azure AD 포럼](https://social.msdn.microsoft.com/Forums/home?forum=WindowsAzureAD)
* [Azure Multi-Factor Authentication Forum](https://social.msdn.microsoft.com/Forums/home?forum=windowsazureactiveauthentication)
* [Stackoverflow](http://stackoverflow.com/questions/tagged/azure)
* [조직으로 Azure에 등록](sign-up-organization.md)
* [Windows PowerShell을 사용하여 Azure AD 관리](https://msdn.microsoft.com/library/azure/jj151815.aspx)
* [Azure AD에서 관리자 역할 할당](active-directory-assign-admin-roles.md)

<!--Image references-->
[1]: ./media/active-directory-administer/aad_portals.png
[2]: ./media/active-directory-administer/azure_tenants.png

