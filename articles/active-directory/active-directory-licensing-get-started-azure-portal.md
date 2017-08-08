---
title: "Azure Active Directory에서 라이선스 시작 | Microsoft Docs"
description: "Azure Active Directory 라이선스 작동 원리, 모범 사례를 시작하는 방법"
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
ms.date: 07/26/2017
ms.author: curtand
ms.reviewer: piotrci
ms.custom: H1Hack27Feb2017;it-pro
ms.translationtype: Human Translation
ms.sourcegitcommit: 6efa2cca46c2d8e4c00150ff964f8af02397ef99
ms.openlocfilehash: b62571e446aa0680c653d0a9d109207af26ad786
ms.contentlocale: ko-kr
ms.lasthandoff: 07/01/2017

---

# <a name="license-yourself-and-your-users-in-azure-active-directory"></a>Azure Active Directory에서 사용자 본인 및 사용자의 사용자 라이선스

> [!div class="op_single_selector"]
> * [Azure Portal 지침](active-directory-licensing-get-started-azure-portal.md)
> * [Azure 클래식 포털 정보](active-directory-licensing-what-is.md)
>
>

Azure AD(Azure Active Directory)는 Microsoft의 IDaaS(Identity as a Service) 솔루션 및 플랫폼입니다. Azure AD는 다양한 서비스 버전으로 제공됩니다.

* Azure Active Directory Free - Office 365, Dynamics, Microsoft Intune 또는 Azure와 같은 Microsoft 서비스에서 사용할 수 있습니다. Azure AD는 이 모드에서 사용량 요금을 생성하지 않습니다.

* Azure AD 유료 버전은 다음과 같습니다.
  - Enterprise Mobility + Security 
  - Azure AD Premium(P1 및 P2)
  - Azure AD Basic
  - Azure Multi-Factor Authentication

많은 Microsoft 온라인 서비스와 마찬가지로 대부분의 Azure AD 유료 버전은 Office 365, Microsoft Intune 및 Azure AD에서 사용자별 권한 부여를 통해 제공됩니다. 이런 경우 서비스 구매는 하나 이상의 구독으로 표시되며 각 구독에는 테넌트의 일부 사전 구매 라이선스가 포함됩니다. 사용자 단위 자격은 다음을 통해 취득합니다.

* 라이선스 할당. 
* 사용자와 제품 간 링크 만들기.
* 사용자에 대한 서비스 구성 요소 사용.
* 선불 라이선스 중 하나 사용.

[이제 Azure AD premium을 시도합니다.](https://portal.office.com/Signup/Signup.aspx?OfferId=01824d11-5ad8-447f-8523-666b0848b381&ali=1#0)

Azure AD 서비스 기능에 대한 광범위한 개요는 [Azure AD란?](active-directory-whatis.md)을 참조하세요. 자세한 내용은 [Azure Active Directory에 대한 SLA](https://azure.microsoft.com/support/legal/sla/active-directory/v1_0/) 페이지를 참조하세요.

> [!NOTE]
> Azure 종량제 구독을 통해 Azure 리소스를 만들고 이 리소스를 결제 방법에 매핑할 수 있습니다. 구독과 연결된 라이선스 수가 없습니다. 구독에 매핑된 Azure 리소스에 적용할 사용자 권한을 부여하면 사용자는 구독과 연결되고 구독 리소스를 관리할 수 있습니다.

## <a name="how-does-azure-active-directory-licensing-work"></a>Azure Active Directory 라이선스는 어떻게 작동하나요?

라이선스 기반 Azure AD 서비스는 Azure AD 서비스 테넌트에서 구독을 활성화하면 작동됩니다. 구독이 활성화된 후 서비스 기능은 Azure AD 관리자가 관리하고 사용이 허가된 사용자가 사용합니다.

### <a name="manage-subscription-information"></a>구독 정보 관리

Enterprise Mobility + Security, Azure AD Premium 또는 Azure AD Basic을 구매하면 디렉터리의 유효 기간 및 선불 라이선스를 포함하여 테넌트가 구독으로 업데이트됩니다. 할당되거나 사용 가능한 라이선스 수를 포함한 구독 정보는 Azure Portal을 통해 사용할 수 있습니다. **Azure Active Directory** 아래에서 특정 디렉터리에 대한 **라이선스** 타일을 엽니다. **라이선스** 블레이드는 사용자 라이선스 할당을 관리하기에 가장 적합한 위치이기도 합니다.

각 구독은 Azure AD, Multi-Factor Authentication, Intune, Exchange Online 또는 SharePoint Online과 같은 하나 이상의 서비스 계획으로 구성됩니다.  Azure AD 라이선스 관리에는 서비스 계획 수준 관리가 필요하지 *않습니다*. Office 365는 포함된 서비스에 대한 액세스를 관리하기 위해 이 고급 구성 모드에 의존하므로 다릅니다. Azure AD는 서비스 구성에 의존하여 기능을 활성화하고 개별 사용 권한을 관리합니다.

> [!IMPORTANT]
> Azure AD Premium, Azure AD Basic 및 Enterprise Mobility + Security 구독은 프로비전된 해당 디렉터리/테넌트로 제한됩니다. 구독은 디렉터리 간에 분할되거나 다른 디렉터리의 사용자에게 자격을 부여하는 데 사용될 수 없습니다. 구독을 디렉터리 간에 이동하는 것은 가능하지만 지원 티켓을 제출하거나, 직접 구매의 경우 취소 후 다시 구매해야 합니다.
>
> 볼륨 라이선싱 구독을 통해 Azure AD 또는 Enterprise Mobility + Security를 구매하는 경우 규약에 다른 Microsoft Online Services(예: Office 365)가 포함되어 있으면 구독이 자동으로 활성화됩니다. 

### <a name="assign-licenses"></a>라이선스 할당

유료 기능을 구성하기 위해서는 구독을 얻기만 하면 되지만, Azure AD 유료 기능에 대한 라이선스를 사용자에게 배포해야 합니다. 액세스 권한이 있어야 하거나 Azure AD 유료 기능을 통해 관리되는 모든 사용자에게 라이선스가 할당되어야 합니다. 라이선스 할당은 Azure AD Premium, Basic 또는 Enterprise Mobility + Security 등의 구매한 서비스와 사용자 간의 매핑입니다.


디렉터리의 어떤 사용자에게 라이선스가 있어야 하는지 관리하려면 다음 작업을 수행하면 됩니다. 

* [Azure Portal](active-directory-licensing-whatis-azure-portal.md)에서 그룹에게 라이선스를 할당합니다.
* 포털, PowerShell 또는 API를 통해 사용자에게 직접 라이선스를 할당합니다. 

그룹에게 라이선스를 할당하면 모든 그룹 구성원에게 라이선스가 할당됩니다. 사용자가 그룹에서 추가 또는 제거될 경우 해당 라이선스가 할당 또는 제거됩니다. 그룹 할당에는 사용자가 사용 가능한 모든 그룹 관리를 이용할 수 있으며 응용 프로그램에 대한 그룹 기반 할당과 일치합니다.

[그룹 기반 라이선스 할당](active-directory-licensing-whatis-azure-portal.md)을 사용하여 다음과 같은 규칙을 설정할 수 있습니다.
* 디렉터리의 모든 사용자가 자동으로 라이선스를 얻습니다.
* 해당하는 직위를 가진 모든 사용자가 라이선스를 얻습니다.
* [셀프 서비스 그룹](active-directory-accessmanagement-self-service-group-management.md)을 사용하여 조직의 다른 관리자에게 의사 결정을 위임할 수 있습니다.

고급 시나리오 및 Office 365 라이선스 시나리오를 비롯한 그룹에 대한 라이선스 할당의 자세한 내용은 [Azure Active Directory에서 그룹 멤버 자격에 따라 사용자에게 라이선스 할당](active-directory-licensing-group-assignment-azure-portal.md)을 참조하세요.

## <a name="get-started-with-azure-ad-licensing"></a>Azure AD 라이선스 시작

Azure AD를 시작하는 것은 쉽습니다. 언제든지 [Azure 평가판](https://azure.microsoft.com/offers/ms-azr-0044p/) 등록의 일부로 디렉터리를 만들 수 있습니다.

다음 모범 사례를 통해 사용 중인 다른 Microsoft 서비스 및 서비스 목표에 테넌트가 적합한지 확인할 수 있습니다.

- Microsoft의 조직 서비스를 사용 중이면 이미 Azure AD 테넌트가 있는 것입니다. 프로비전 및 하이브리드 SSO(Single Sign-On)를 포함하여 핵심 ID 관리를 서비스 전체에서 사용할 수 있도록 다른 서비스에도 동일한 테넌트를 사용하는 것이 유용합니다. Single Sign-On을 통해 사용자는 서비스 전체에서 다양한 기능을 활용할 수 있습니다. 따라서 직원을 위해 Azure AD 유료 서비스를 구매하기로 결정하는 경우 동일한 테넌트를 다시 사용하는 것이 좋습니다.

- 다음을 계획 중인 경우 Azure Portal에 새 테넌트를 사용하는 것이 좋습니다.
  - 다른 사용자 집합(예: 파트너 또는 고객)에 대해 Azure AD를 사용합니다.
  - 프로덕션 서비스와 별개로 Azure AD 서비스를 평가합니다.
  - 서비스에 대한 샌드박스 환경을 설정합니다.

  새 디렉터리는 사용자 계정을 전역 관리자 권한이 있는 외부 사용자로 사용하여 만들어집니다. 이 계정으로 Azure Portal에 로그인하면 이 테넌트가 표시되며 모든 관리 작업에 액세스할 수 있습니다.

> [!NOTE]
> Azure AD는 “게스트 사용자”를 지원하는데, 이 외부 사용자는 Microsoft 계정 또는 다른 테넌트의 Azure AD ID 중 하나를 통해 생성된 Azure AD 테넌트의 사용자 계정입니다. Office 365 관리 포털은 현재 이러한 사용자를 지원하지 않습니다. Microsoft 계정을 가진 게스트 사용자가 Office 365 관리 포털에 전혀 액세스할 수 없으며 다른 Azure AD 테넌트의 게스트 사용자는 무시됩니다. 후자의 경우, 사용자의 로컬 계정만 사용자가 원래 만들어진 Azure AD 또는 Office 365 테넌트에서 액세스할 수 있습니다.

### <a name="select-one-or-more-license-trials"></a>하나 이상의 라이선스 평가판 선택

**Azure Active Directory** &gt; **빠른 시작** 아래에서 Azure AD Premium 또는 Enterprise Mobility + Security 평가판 구독을 활성화할 수 있습니다.

![라이선스 평가판 선택](media/active-directory-licensing-get-started-azure-portal/select-a-license-trial.png)

평가판 라이선스는 **라이선스** 블레이드에서 제공됩니다.

### <a name="assign-licenses-to-users-and-groups"></a>사용자 및 그룹에 라이선스 할당

구독이 활성화된 후 사용자가 자신에게 라이선스를 할당합니다. 그다음에 브라우저를 새로 고쳐야 모든 기능을 볼 수 있습니다. 다음 단계는 유료 Azure AD 기능에 액세스해야 할 사용자에게 라이선스를 할당하는 것입니다. [라이선스 할당](#assign-licenses)에서 설명된 대로 라이선스를 할당하는 한 가지 간편한 방법은 원하는 대상 그룹을 식별하여 라이선스를 할당하는 것입니다. 이 방법으로 수명 주기 동안 그룹에서 추가 또는 제거되는 사용자는 각각 라이선스를 할당받거나 라이선스에서 제거됩니다.

> [!NOTE]
> 일부 Microsoft 서비스는 모든 위치에서 사용할 수 없습니다. 사용자에게 라이선스를 할당하려면 먼저 관리자가 해당 사용자에 대해 “**사용 위치**” 속성을 지정해야 합니다. Azure Portal의 **사용자** &gt; **프로필** &gt; **설정**에서 이 속성을 설정할 수 있습니다. 그룹 라이선스 할당을 사용할 때 사용 위치가 지정되지 않은 사용자는 디렉터리의 위치를 상속합니다.

라이선스를 할당하려면 **Azure Active Directory** &gt; **라이선스** &gt; **모든 제품** 아래에서 제품을 하나 이상 선택하고 명령 모음에서 **할당** 단추를 선택합니다.

![할당할 라이선스 선택](media/active-directory-licensing-get-started-azure-portal/select-license-to-assign.png)

**사용자 및 그룹** 블레이드를 사용하여 여러 사용자나 그룹을 선택하거나 제품의 서비스 계획을 사용하지 않도록 설정할 수 있습니다. 맨 위에 있는 검색 상자를 사용하여 사용자 및 그룹 이름을 검색합니다.

![라이선스 할당을 위한 사용자 또는 그룹 선택](media/active-directory-licensing-get-started-azure-portal/select-user-for-license-assignment.png)

라이선스를 그룹에 할당할 때는 모든 사용자가 라이선스를 상속하기 전에 그룹에 있는 사용자 수에 따라 다소 시간이 소요될 수 있습니다. **그룹** 블레이드의 **라이선스** 타일 아래에서 처리 상태를 확인할 수 있습니다.

![라이선스 할당 상태](media/active-directory-licensing-get-started-azure-portal/license-assignment-status.png)

Azure AD 라이선스 할당 중에 할당 오류가 발생할 수 있지만 Azure AD 및 Enterprise Mobility + Security 제품을 관리할 경우에는 상대적으로 드물게 오류가 발생합니다. 잠재적 할당 오류는 다음으로 제한됩니다.
- 할당 충돌: 사용자에게 이전에 할당된 라이선스가 현재 라이선스와 호환되지 않는 경우. 이런 경우, 새 라이선스를 할당하려면 현재 라이선스를 제거해야 합니다.
- 사용 가능한 라이선스 수 초과: 할당된 그룹의 사용자 수가 사용 가능한 라이선스 수를 초과한 경우 없는 라이선스로 인해 사용자의 할당 상태가 할당 실패로 표시됩니다.

#### <a name="azure-ad-b2b-collaboration-licensing"></a>Azure AD B2B 공동 작업 라이선스

B2B 공동 작업을 통해 게스트 사용자를 Azure AD 테넌트로 초대해서 Azure AD 서비스 및 사용 가능하게 설정할 Azure 리소스에 대한 액세스 권한을 제공할 수 있습니다.  

B2B 사용자를 초대한 후 Azure AD에서 응용 프로그램에 할당하는 과정은 무료입니다. 게스트 사용자당 최대 10개 앱과 3개의 기본 보고서도 B2B 공동 작업 사용자에게 무료로 제공됩니다. 파트너의 Azure AD 테넌트에서 게스트 사용자에게 적절한 라이선스가 할당되어 있으면 여러분의 테넌트에서도 게스트 사용자에게 사용이 허가됩니다.

필수는 아니지만 유료 Azure AD 기능에 대한 액세스 권한을 제공하려면 해당 B2B 게스트 사용자는 적절한 Azure AD 라이선스의 사용 허가를 받아야 합니다. Azure AD 유료 라이선스가 있는 초대하는 테넌트는 테넌트에 초대된 5명의 추가 게스트 사용자에게 B2B 공동 작업 사용자 권한을 할당할 수 있습니다. 시나리오와 정보는 [B2B 공동 작업 라이선스 지침](active-directory-b2b-licensing.md)을 참조하세요.

### <a name="view-assigned-licenses"></a>할당된 라이선스 보기

할당된 라이선스 및 사용 가능한 라이선스에 대한 요약 보기는 **Azure Active Directory** &gt; **라이선스** &gt; **모든 제품** 아래 표시됩니다.

![라이선스 요약 보기](media/active-directory-licensing-get-started-azure-portal/view-license-summary.png)

특정 제품을 선택할 때 사용 가능한 할당된 사용자 및 그룹의 자세한 목록입니다. **허가된 사용자**에는 현재 라이선스를 사용 중인 모든 사용자와 함께 라이선스가 사용자에게 직접 할당되었는지, 그룹에서 상속되는지 여부가 표시됩니다.

![라이선스 세부 정보 보기](media/active-directory-licensing-get-started-azure-portal/view-license-detail.png)

마찬가지로 **허가된 그룹**은 라이선스가 할당된 모든 그룹을 보여 줍니다. 사용자 또는 그룹을 선택하면 해당 개체에 할당된 모든 라이선스를 보여 주는 **라이선스** 블레이드가 열립니다.

### <a name="remove-a-license"></a>라이선스 제거

라이선스를 제거하려면 사용자 또는 그룹으로 이동하고 **라이선스** 타일이 열립니다. 라이선스를 선택하고 **제거**를 클릭합니다.

![라이선스 제거](media/active-directory-licensing-get-started-azure-portal/remove-license.png)

그룹에서 사용자에 상속된 라이선스는 직접 제거할 수 없습니다. 대신, 라이선스를 상속하는 그룹에서 사용자를 제거합니다.

### <a name="extend-trials"></a>평가판 연장

고객에 대한 평가판 연장은 Office 365 포털을 통해 셀프 서비스 등록으로 사용할 수 있습니다. 고객 관리자가 Office 포털(Office 포털에 대한 사용 권한에 따라 액세스 권한이 달라짐)로 이동하여 사용자의 Azure AD Premium 평가판을 선택합니다. **평가판 확장** 링크를 클릭하면 확장 프로세스가 시작됩니다. 신용 카드가 필요하지만 요금이 부과되지는 않습니다.

![Azure Portal에서 평가판 연장](media/active-directory-licensing-get-started-azure-portal/extend-trial-beginning.png)

## <a name="next-steps"></a>다음 단계

그룹을 통해 라이선스를 관리하는 고급 시나리오에 대해 자세히 알아보려면 [그룹에 라이선스 할당](active-directory-licensing-group-assignment-azure-portal.md) 문서를 읽어 보세요.

다른 Azure AD 유료 기능을 구성 및 사용하는 방법에 대한 설명은 다음과 같습니다.

* [셀프 서비스 암호 재설정](active-directory-manage-passwords.md)
* [셀프 서비스 그룹 관리](active-directory-accessmanagement-self-service-group-management.md)
* [Azure AD Connect Health](active-directory-aadconnect-health.md)
* [Azure Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication.md)
* [Azure AD Premium 라이선스 직접 구매](http://aka.ms/buyaadp)

