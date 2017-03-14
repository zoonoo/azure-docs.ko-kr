---

title: "Azure Active Directory 미리 보기에서 라이선스 시작 | Microsoft Docs"
description: "Office 365, Microsoft Intune 및 Azure Active Directory Premium 및 Basic 버전을 포함하여 Azure Active Directory 라이선스에 대한 설명, 작동 방법, 시작하는 방법 및 모범 사례"
services: active-directory
keywords: "Azure AD 라이선스"
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/27/2017
ms.author: curtand
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 09f0ed3f7624bb242c40868710fb3eae49cda906
ms.openlocfilehash: a798de49ed08eedaf27267c6bac12f8b0b64739f
ms.lasthandoff: 03/01/2017


---

# <a name="license-yourself-and-your-users-in-azure-active-directory-preview"></a>Azure Active Directory 미리 보기에서 사용자 본인 및 사용자의 사용자 라이선스

> [!div class="op_single_selector"]
> * [Azure Portal](active-directory-licensing-get-started-azure-portal.md)
> * [Azure 클래식 포털](active-directory-licensing-what-is.md)
>
>

Azure AD(Azure Active Directory)는 Microsoft의 IDaaS(Identity as a Service) 솔루션 및 플랫폼입니다. Azure AD는 Office 365, Dynamics, Microsoft Intune 및 Azure 등과 같은 Microsoft 서비스와 함께 사용할 수 있는 Azure AD 무료 버전부터, Azure Multi-Factor Authentication(MFA) 뿐만 아니라 Enterprise Mobility Suite(EMS), Azure AD Premium(P1 및 P2) 및 Azure AD Basic 등과 같은 Azure AD 유료 버전에 이르기까지 다양한 기능적, 기술적 버전으로 제공됩니다. 많은 Microsoft 온라인 서비스와 마찬가지로 대부분의 Azure AD 유료 버전은 Office 365, Microsoft Intune 및 Azure AD에서 사용자별 권한 부여를 통해 제공됩니다. 이런 경우 서비스 구매는 하나 이상의 구독으로 표시되며 각 구독에는 테넌트의 사전 구매 라이선스 수가 포함됩니다. 사용자별 권한 부여는 사용자와 제품 간에 연결을 생성하고 사용자가 서비스 구성 요소를 사용하도록 설정하고 선불 라이선스 중 하나를 사용하는 라이선스 할당을 통해 수행됩니다.

[이제 Azure AD premium을 시도합니다.](https://portal.office.com/Signup/Signup.aspx?OfferId=01824d11-5ad8-447f-8523-666b0848b381&ali=1#0)

Azure AD 서비스 기능에 대한 광범위한 개요는 [Azure AD란?](https://azure.microsoft.com/en-us/documentation/articles/active-directory-whatis/)을 참조하세요. 자세한 내용은 Service Level Agreements(서비스 수준 약정) 페이지를 참조하세요.

> [!NOTE]
> Azure 종량제 구독은 다릅니다. 디렉터리에 표시되기도 하지만 이러한 구독을 통해 Azure 리소스를 만들 수 있고 지불 방법에 매핑할 수도 있습니다. 이 경우 구독과 연결된 라이선스 수가 없습니다. 사용자와 구독과의 연결, 관리되는 구독 리소스에 대한 사용자 액세스는 구독에 매핑된 Azure 리소스에 대해 작동하는 권한을 부여하여 수행됩니다.

## <a name="how-does-azure-ad-licensing-work"></a>Azure AD 라이선스 작동 방식

라이선스 기반 Azure AD 서비스는 Azure AD 디렉터리/서비스 테넌트에서 구독을 활성화하면 작동됩니다. 구독이 활성화되면 디렉터리/서비스 관리자가 서비스 기능을 관리하고 허가된 사용자가 사용할 수 있습니다.

Enterprise Mobility Suite, Azure AD Premium 또는 Azure AD Basic을 구매 또는 활성화하면 디렉터리의 유효 기간 및 선불 라이선스를 포함하여 디렉터리가 구독으로 업데이트됩니다. 할당되거나 사용 가능한 라이선스 수를 포함한 구독 정보는 특정 디렉터리에 대한 Azure Active Directory &gt; **라이선스** 타일 아래에서 Azure Portal을 통해 사용할 수 있습니다. 또한 이 탭은 사용자 라이선스 할당을 관리하기에 가장 적합한 위치이기도 합니다.

각 구독은 Azure AD, Azure MFA, Microsoft Intune, Exchange Online 또는 SharePoint Online 등과 같은 서비스 유형의 포함된 기능적 수준을 매핑하는 하나 이상의 서비스 계획으로 구성됩니다.  Azure AD 라이선스 관리에는 서비스 계획 수준 관리가 필요하지 않습니다. 포함된 서비스에 대한 액세스를 관리하기 위해 이 고급 구성 모드에 의존하는 Office 365와는 다릅니다. Azure AD는 서비스 구성에 의존하여 기능을 활성화하고 개별 사용 권한을 관리합니다.

> [!IMPORTANT]
> Enterprise Mobility Suite 구독뿐만 아니라 Azure AD Premium 및 Basic도 프로비전된 해당 디렉터리/테넌트로 제한됩니다. 구독은 디렉터리 간에 분할되거나 다른 디렉터리의 사용자에게 자격을 부여하는 데 사용될 수 없습니다. 구독을 디렉터리 간에 이동하는 것은 가능하지만 지원 티켓을 제출하거나, 직접 구매의 경우 취소 후 다시 구매해야 합니다.
>
> 볼륨 라이선싱을 통해 Azure AD 또는 Enterprise Mobility Suite를 구입하는 경우 규약에 다른 Microsoft Online 서비스(예: Office 365)가 포함되어 있으면 자동으로 구독 활성화가 발생합니다.

### <a name="assigning-licenses"></a>라이선스 할당

유료 기능을 구성하기 위해서는 구독을 얻기만 하면 되는 반면, Azure AD 유료 기능을 사용하려면 적절한 개인에게 라이선스를 배포해야 합니다. 일반적으로 액세스 권한이 있어야 하거나 Azure AD 유료 기능을 통해 관리되는 모든 사용자에게 라이선스가 할당되어야 합니다. 라이선스 할당은 Azure AD Premium, Basic 또는 Enterprise Mobility Suite 등의 구매한 서비스와 사용자 간의 매핑입니다.

디렉터리의 어떤 사용자에게 라이선스가 있어야 하는지 관리하는 것은 간단합니다. Azure Portal에서 그룹에 라이선스를 할당하거나 포털, PowerShell 또는 API를 통해 적절한 개인에게 직접 라이선스를 할당하여 수행할 수 있습니다. 라이선스를 그룹에 할당하면 라이선스가 모든 그룹 구성원에게 할당됩니다. 사용자를 그룹에 추가하거나 그룹에서 제거하면 적절한 라이선스가 추가 또는 제거됩니다. 그룹 할당에는 사용자가 사용 가능한 모든 그룹 관리를 이용할 수 있으며 응용 프로그램에 대한 그룹 기반 할당과 일치합니다. 이 방법을 사용하면 디렉터리의 모든 사용자가 자동으로 할당되도록 규칙을 설정하거나 적절한 직책을 가진 모든 사람에게 라이선스가 있는지 확인하거나 조직의 다른 관리자에게 의사 결정을 위임할 수도 있습니다. 고급 시나리오 및 Office 365 라이선스 시나리오 등 그룹에 라이선스 할당에 대한 자세한 내용은 다음 문서를 참조하세요.

## <a name="getting-started-with-azure-ad-licensing"></a>Azure AD 라이선스 시작

Azure AD를 시작하는 것은 간단합니다. 항상 무료 Azure 평가판에 대한 등록의 일부로 디렉터리를 만들 수 있습니다. [조직으로 등록하는 방법에 대해 자세히 알아보세요](https://azure.microsoft.com/en-us/documentation/articles/sign-up-organization/). 다음은 사용자가 사용 중이거나 사용할 예정일 수 있는 다른 Microsoft 서비스 및 서비스를 가져오려는 목표와 디렉터리가 최적으로 연결되도록 하는 데 도움을 될 수 있습니다.

몇 가지 모범 사례가 있습니다.

- Microsoft의 조직 서비스를 이미 사용 중이면 이미 Azure AD 디렉터리가 있는 것입니다. 이 경우 프로비전 및 하이브리드 SSO(Single Sign-On)를 포함하여 핵심 ID 관리를 서비스 전체에서 활용할 수 있도록 다른 서비스에도 동일한 디렉터리를 계속 사용해야 합니다. 사용자는 단일 로그인 경험을 갖게 되며 서비스 전반에서 더욱 다양한 기능을 통해 이점을 얻을 수 있습니다. 따라서 직원을 위해 Azure AD 유료 서비스를 구입하기로 결정하는 경우 동일한 디렉터리를 사용하여 이 작업을 수행하는 것이 좋습니다.

- 다른 사용자 집합(파트너, 고객 등)에서 Azure AD를 사용할 계획이거나, 프로덕션 서비스와 격리된 환경에서 Azure AD 서비스를 평가하려는 경우 또는 서비스에 대한 샌드박스 환경을 설치하려는 경우, 먼저 Azure 클래식 포털을 통해 새 디렉터리를 만드는 것이 좋습니다. Azure 클래식 포털에서 새 Azure AD 디렉터리를 만드는 방법에 대해 알아보세요. 새 디렉터리는 사용자 계정을 전역 관리자 권한이 있는 외부 사용자로 사용하여 만들어집니다. 이 계정으로 Azure Portal에 로그인하면 이 디렉터리가 표시되며 모든 디렉터리 관리 작업에 액세스할 수 있습니다.

> [!NOTE]
> Azure AD는 "외부 사용자"를 지원하는데, 이 외부 사용자는 Microsoft 계정(MSA) 또는 다른 디렉터리의 Azure AD ID 중 하나를 사용하여 생성된 Azure AD 인스턴스의 사용자 계정입니다. 이 기능을 모든 Microsoft 조직 서비스로 확장하기 위해 노력하고 있지만 지금 현재 이러한 계정이 일부 서비스 환경에서 지원되지 않습니다. 예를 들어 Office 365 관리 포털은 현재 이러한 사용자를 지원하지 않습니다. 따라서 Microsoft 계정을 가진 외부 사용자가 Office 365 관리 포털에 전혀 액세스할 수 없으며 다른 Azure AD 디렉터리의 외부 사용자는 무시됩니다. 후자의 경우, 사용자의 로컬 계정, 사용자가 원래 만든 Azure AD 또는 Office 365 디렉터리만 이러한 환경을 통해 액세스할 수 있습니다.

표시된 대로 Azure AD에는 다른 유료 버전이 있습니다. 이러한 버전은 구매 가용성에 약간의 차이가 있습니다.

|  제품       |              EA/VL  | 열기  | CSP |  MPN 사용 권한  | 직접 구매 |  평가판 |
|  -------------- | ------------- | ------- | ------ | ----- | ---------------- | ----------------- | ------- |
|  Enterprise Mobility Suite  | X   |    X  |    X  |     X | &nbsp;  | X |
|  Azure AD Premium P2     |    X    |   X   |   X   | &nbsp;  |  X  |   X  
|  Azure AD Premium P1     |    X   |    X    |  X   |  &nbsp; |  X  |  &nbsp; |             
|  Azure AD Basic          |    X   |    X   |  X  |  X  | &nbsp; | &nbsp;  |

### <a name="select-one-or-more-license-trials"></a>하나 이상의 라이선스 평가판 선택

Azure Active Directory &gt; **빠른 시작** 아래에서 Azure AD Premium 또는 Enterprise Mobility Suite 평가판 구독을 활성화할 수 있습니다.

![라이선스 평가판 선택](media/active-directory-licensing-get-started-azure-portal/select-a-license-trial.png)

현재 평가판 라이선스는 **라이선스** 블레이드에서 제공됩니다.

### <a name="assign-licenses-to-users-and-groups"></a>사용자 및 그룹에 라이선스 할당

구독이 활성화되면 사용자가 자신에게 라이선스를 할당하고 브라우저를 새로 고쳐야 모든 기능을 볼 수 있습니다. 다음 단계는 유료 Azure AD 기능에 액세스하거나 포함시켜야 할 사용자에게 라이선스를 할당하는 것입니다. 위의 [라이선스 할당](#assigning-licenses)에서 설명한 것처럼, 이 작업을 수행하는 가장 좋은 방법은 원하는 대상 그룹을 식별하여 라이선스에 할당하는 것입니다. 이 방법을 사용하면 그룹에 추가된 사용자를 라이선스에 할당하거나 수명 주기가 지난 그룹에서 제거된 사용자를 라이선스에서 제거할 수 있습니다.

> [!NOTE]
> 일부 Microsoft 서비스는 특정 위치에서만 사용 가능합니다. 라이선스를 사용자에게 할당할 수 있으려면 관리자가 사용자의 "사용 위치" 속성을 지정해야 합니다. 이 작업은 Azure Portal의 사용자 &gt; 프로필 &gt; 설정에서 수행할 수 있습니다. 그룹 라이선스 할당을 사용할 때 사용 위치가 지정되지 않은 사용자는 디렉터리의 위치를 상속합니다.

그룹 또는 개별 사용자에게 라이선스를 할당하려면 Azure Active Directory &gt; 라이선스 &gt; 모든 제품 아래에서 제품을 하나 이상 선택하고 명령 모음에서 **할당** 단추를 클릭합니다.

![할당할 라이선스 선택](media/active-directory-licensing-get-started-azure-portal/select-license-to-assign.png)

그러면 여러 사용자 또는 그룹을 선택할 수 있는 새 블레이드가 표시되고 필요에 따라 제품에서 서비스 계획을 사용하지 않도록 설정합니다. 맨 위에 있는 검색을 사용하여 사용자 및 그룹 이름을 검색할 수 있습니다.

![라이선스 할당을 위한 사용자 또는 그룹 선택](media/active-directory-licensing-get-started-azure-portal/select-user-for-license-assignment.png)

라이선스를 그룹에 할당할 때는 모든 사용자가 라이선스를 상속할 때까지, 그룹에 있는 사용자 수에 따라 다소 시간이 소요될 수 있습니다. 그룹 블레이드의 **라이선스** 타일 아래에서 처리 상태를 확인할 수 있습니다.

![라이선스 할당 상태](media/active-directory-licensing-get-started-azure-portal/license-assignment-status.png)

Azure AD 라이선스 할당 중에 할당 오류가 발생할 수 있지만 Azure AD 및 EMS 제품을 관리할 경우 상대적으로 드문 경우입니다. 잠재적 할당 오류는 다음으로 제한됩니다.
- 할당 충돌: 사용자에게 이전에 할당된 라이선스가 현재 라이선스와 호환되지 않는 경우. 이런 경우, 새 라이선스를 할당하려면 현재 라이선스를 제거해야 합니다.
- 사용 가능한 라이선스 수 초과: 할당된 그룹의 사용자 수가 사용 가능한 라이선스 수를 초과한 경우 없는 라이선스로 인해 사용자의 할당 상태가 할당 실패로 표시됩니다.

그룹 라이선스 할당에 대한 자세한 정보는 이 문서에서 다룹니다.

### <a name="view-assigned-licenses"></a>할당된 라이선스 보기

할당되어 사용 가능한 라이선스에 대한 요약 보기는 Azure Active Directory &gt; **라이선스** &gt; **모든 제품** 아래에 표시됩니다.

![라이선스 요약 보기](media/active-directory-licensing-get-started-azure-portal/view-license-summary.png)

특정 제품을 클릭할 때 사용 가능한 할당된 사용자 및 그룹의 자세한 목록입니다. **허가된 사용자**에는 라이선스가 사용자에게 직접 할당되었는지, 그룹에서 상속되는지를 비롯하여 현재 라이선스를 사용 중인 모든 사용자가 표시됩니다.

![라이선스 세부 정보 보기](media/active-directory-licensing-get-started-azure-portal/view-license-detail.png)

마찬가지로 **허가된 그룹**은 라이선스가 할당된 모든 그룹을 보여 줍니다. 해당 보기에서 사용자 또는 그룹을 클릭하면 해당 프로젝트에 할당된 모든 라이선스를 보여 주는 **라이선스** 블레이드가 열립니다.

### <a name="removing-a-license"></a>라이선스 제거

라이선스를 제거하려면 사용자 또는 그룹으로 이동하고 **라이선스** 타일이 열립니다. 라이선스를 선택하고 **제거**를 클릭합니다.

![라이선스 제거](media/active-directory-licensing-get-started-azure-portal/remove-license.png)

그룹에서 사용자에 상속된 라이선스는 직접 제거할 수 없습니다. 대신, 라이선스를 상속하는 그룹에서 사용자를 제거합니다.

### <a name="extending-trials"></a>평가판 확장

Office 365 포털을 통해 평가판을 셀프 서비스로 확장할 수 있습니다. 고객 관리자가 Office 포털(Office 포털에 대한 사용 권한에 따라 액세스 권한이 달라짐)로 이동하여 사용자의 Azure AD Premium 평가판을 선택합니다. **평가판 확장** 링크를 클릭하면 확장 프로세스가 시작됩니다. 신용 카드 번호가 필요하지만 요금이 부과되지는 않습니다.

![Azure Portal에서 평가판 확장](media/active-directory-licensing-get-started-azure-portal/extend-trial-beginning.png)

## <a name="next-steps"></a>다음 단계

그룹을 통해 라이선스 관리를 위한 고급 시나리오에 대해 자세히 알아보려면 이 문서를 읽어 보세요.

이제 일부 Azure AD Premium 기능을 구성하여 사용할 준비가 되었을 것입니다.

* [셀프 서비스 암호 재설정](active-directory-manage-passwords.md)
* [셀프 서비스 그룹 관리](active-directory-accessmanagement-self-service-group-management.md)
* [Azure AD Connect 상태](active-directory-aadconnect-health.md)
* [응용 프로그램에 그룹 할당](active-directory-manage-groups.md)
* [Azure Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication.md)
* [Azure AD Premium 라이선스 직접 구매](http://aka.ms/buyaadp)

