---
title: Azure DevTest Labs FAQ | Microsoft 문서
description: 이 문서에서는 Azure DevTest Labs에 대 한 FAQ (질문과 대답)를 제공 합니다.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: afe83109-b89f-4f18-bddd-b8b4a30f11b4
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2020
ms.author: spelluru
ms.openlocfilehash: de99e9b1e4adceaf08beaf8ad3b5ea114b31a586
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/13/2020
ms.locfileid: "79270785"
---
# <a name="azure-devtest-labs-faq"></a>Azure DevTest Labs FAQ
Azure DevTest Labs에 대한 일반적인 질문에 대한 답변을 확인합니다.

## <a name="blog-post"></a>블로그 게시물
DevTest Labs 팀 블로그는 20 년 3 2019 월 20 일에 사용이 중지 되었습니다. 

### <a name="where-can-i-track-feature-updates-from-now-on"></a>지금부터 기능 업데이트를 추적할 수 있는 위치
지금부터 Azure 블로그 및 Azure 업데이트에 기능 업데이트 및 정보 블로그 게시물을 게시할 예정입니다. 이러한 블로그 게시물은 필요한 경우에도 설명서에 연결 됩니다.

[DevTest Labs Azure 블로그](https://azure.microsoft.com/blog/tag/azure-devtest-labs/) 및 [DevTest labs azure 업데이트](https://azure.microsoft.com/updates/?product=devtest-lab) 를 구독 하 여 DevTest labs의 새로운 기능에 대 한 정보를 계속 받을 수 있습니다.

### <a name="what-happens-to-the-existing-blog-posts"></a>기존 블로그 게시물은 어떻게 되나요?
현재 [DevTest Labs 설명서](devtest-lab-overview.md)로 기존 블로그 게시물 (중단 업데이트 제외)을 마이그레이션하는 작업을 진행 하 고 있습니다. MSDN 블로그는 더 이상 사용 되지 않을 경우 DevTest Labs에 대 한 설명서 개요로 리디렉션됩니다. 리디렉션된 후 ' 필터링 기준 ' 제목에서 원하는 문서를 검색할 수 있습니다. 모든 게시물을 아직 마이그레이션하지 않았지만 이번 달의 끝까지이 작업을 수행 해야 합니다. 


### <a name="where-do-i-see-outage-updates"></a>중단 업데이트는 어디에서 볼 까 요?
이제 Twitter 핸들을 사용 하 여 중단 업데이트를 게시할 예정입니다. Twitter에서 팔 로우 하 여 중단 및 알려진 버그에 대 한 최신 업데이트를 받으세요.

### <a name="twitter"></a>Twitter
Twitter 핸들: [@azlabservices](https://twitter.com/azlabservices)

## <a name="general"></a>일반
### <a name="what-if-my-question-isnt-answered-here"></a>여기서 내 질문에 대답하지 않으면 어떻게 하나요?
질문이 여기에 나열 되지 않은 경우 알려 주시기 바랍니다. 답변을 찾을 수 있습니다.

- 이 FAQ의 끝에 질문을 게시합니다.
- 더 광범위한 대상에 도달하기 위해 [Azure DevTest Labs MSDN 포럼](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureDevTestLabs)에 질문을 게시하세요. Azure DevTest Labs 팀과 커뮤니티의 다른 구성원들이 참여합니다.
- 기능 요청의 경우 요청 내용과 아이디어를 [Azure DevTest Labs 사용자 의견](https://feedback.azure.com/forums/320373-azure-devtest-labs)으로 제출해 주세요.

### <a name="what-is-a-microsoft-account"></a>Microsoft 계정이란?
Microsoft 계정이란 Microsoft 디바이스 및 서비스를 가지고 하는 거의 모든 것에 대해 사용하는 계정입니다. Skype, Outlook.com, OneDrive, Windows phone, Azure 및 Xbox Live에 로그인 하는 데 사용 하는 전자 메일 주소 및 암호입니다. 단일 계정은 파일, 사진, 연락처 및 설정이 모든 디바이스에서 사용자를 따를 수 있음을 의미합니다.

> [!NOTE]
> Windows Live ID를 호출 하는 데 사용 되는 Microsoft 계정입니다.

### <a name="why-should-i-use-azure-devtest-labs"></a>Azure DevTest Labs를 사용해야 하는 이유는 무엇인가요?
Azure DevTest Labs를 통해 팀 시간과 비용을 절약할 수 있습니다. 개발자는 여러 다른 기준을 사용하여 자신의 고유한 환경을 만들 수 있습니다. 또한 아티팩트를 사용하여 애플리케이션을 빠르게 배포하고 구성할 수 있습니다. 사용자 지정 이미지 및 수식을 사용하여 템플릿으로 VM(가상 머신)을 저장할 수 있으며 팀 내에서 쉽게 재현할 수 있습니다. 또한 DevTest Labs는 랩 관리자가 낭비를 줄이고 팀의 환경을 관리하도록 사용할 수 있는 몇 가지 구성 가능한 정책을 제공합니다. 이러한 정책에는 자동 종료, 비용 임계값, 사용자당 최대 VM 및 최대 VM 크기가 포함됩니다. DevTest Labs에 대한 자세한 설명은 [개요](devtest-lab-overview.md)를 읽거나 [소개 비디오](https://channel9.msdn.com/Blogs/Azure/what-is-azure-devtest-labs)를 시청하세요.

### <a name="what-does-worry-free-self-service-mean"></a>"걱정할 필요가 없는 셀프 서비스"는 무슨 의미인가요?
걱정할 필요가 없는 셀프 서비스는 개발자와 테스터가 필요에 따라 자신의 고유한 환경을 만드는 것을 의미합니다. 관리자는 DevTest Labs가 적절 한 액세스를 설정 하 고 낭비를 최소화 하 고 비용을 제어 하는 데 도움이 될 수 있음을 알고 있습니다. 관리자는 허용되는 VM 크기, VM의 최대 수 및 VM의 시작 및 종료 시기를 지정할 수 있습니다. 또한 랩 리소스가 사용되는 방식을 인식하기 위해 DevTest Labs를 통해 쉽게 비용을 모니터링하고 경고를 설정합니다.

### <a name="how-can-i-use-devtest-labs"></a>DevTest Labs를 어떻게 사용할 수 있나요?
DevTest Labs는 개발 또는 테스트 환경이 필요할 때마다 유용 하며, 신속 하 게 재현 하거나 비용 절감 정책을 사용 하 여 관리 하려는 경우에 유용 합니다.
고객이 DevTest Labs를 사용하는 몇 가지 시나리오는 다음과 같습니다.

- 한 곳에서 개발 및 테스트 환경을 관리합니다. 정책을 사용하여 비용을 절감하고 사용자 지정 이미지를 만들어 팀 내에서 빌드를 공유합니다.
- 개발 단계에 걸쳐 디스크 상태를 저장하도록 사용자 지정 이미지를 사용하여 애플리케이션을 개발합니다.
- 진행 상태와 관련하여 비용을 추적합니다.
- 품질 보증 테스트에 대한 대량 테스트 환경을 만듭니다.
- 다양한 환경에서 애플리케이션을 쉽게 구성하고 재현하도록 아티팩트 및 수식을 사용합니다.
- 해커톤(공동 개발 또는 테스트 작업)에 대해 VM을 배포한 다음 이벤트가 종료될 때 쉽게 프로비전을 해제합니다.

### <a name="how-am-i-billed-for-devtest-labs"></a>DevTest Labs에 대한 요금은 어떻게 청구되나요?
DevTest Labs는 무료 서비스입니다. 즉 DevTest Labs에서 랩을 만들고 정책, 템플릿 및 아티팩트를 구성하는 것이 무료입니다. VM, 스토리지 계정 및 가상 네트워크와 같은 랩에서 사용하는 Azure 리소스에 대해서만 비용을 지불합니다. 랩 리소스의 비용에 대한 자세한 내용은 [Azure DevTest Labs 가격 책정](https://azure.microsoft.com/pricing/details/devtest-lab/)을 참조하세요.

## <a name="security"></a>보안

### <a name="what-are-the-different-security-levels-in-devtest-labs"></a>DevTest Labs의 다른 보안 수준은 무엇인가요?
보안 액세스는 RBAC (역할 기반 Access Control)에 의해 결정 됩니다. 액세스의 작동 방식을 알아보기 위해 RBAC에 의해 정의된 대로 사용 권한, 역할 및 범위 사이의 차이점을 알아보는 데 도움을 줍니다.

- **사용 권한**: 특정 작업에 대해 정의된 액세스입니다. 예로 모든 VM에 대한 읽기 액세스가 있습니다.
- **역할**: 그룹화되고 사용자에게 할당될 수 있는 사용 권한의 집합입니다. 예를 들어 구독 소유자 역할이 있는 사용자는 구독 내의 모든 리소스에 대한 액세스를 보유합니다.
- **범위**: 범위는 Azure 리소스 계층 구조 내의 수준입니다. 예를 들어 범위는 리소스 그룹, 단일 랩이거나 전체 구독일 수 있습니다.

DevTest Labs의 범위 내에 사용자 사용 권한을 정의하는 두 가지 유형의 역할이 있습니다.

- **랩 소유자**: 랩 소유자는 랩에서 모든 리소스에 대한 액세스 권한을 보유합니다. 랩 소유자는 정책을 수정하고 VM을 읽고 쓰고, 가상 네트워크를 변경하는 등의 작업을 할 수 있습니다.
- **랩 사용자**: 랩 사용자는 VM, 정책 및 가상 네트워크와 같은 모든 랩 리소스를 볼 수 있습니다. 그러나 랩 사용자는 다른 사용자가 만든 정책 또는 Vm을 수정할 수 없습니다.

DevTest Labs에서 사용자 지정 역할을 만들 수도 있습니다. DevTest Labs에서 사용자 지정 역할을 만드는 방법을 알아보려면 [특정 랩 정책에 사용자 권한 부여](devtest-lab-grant-user-permissions-to-specific-lab-policies.md)를 참조하세요.

범위는 계층적이므로 사용자가 특정 범위에서 사용 권한을 가진 경우 범위의 모든 하위 수준 범위에서 해당 사용 권한이 자동으로 부여됩니다. 예를 들어 사용자가 구독 소유자의 역할에 할당되면 사용자는 구독의 모든 리소스에 대한 액세스를 갖게 됩니다. 이러한 리소스에는 Vm, 가상 네트워크 및 랩이 포함 됩니다. 구독 소유자는 자동으로 랩 소유자의 역할을 상속합니다. 그러나 반대의 경우는 적용되지 않습니다. 랩 소유자는 구독 수준보다 낮은 범위인 랩에 대한 액세스를 가집니다. 따라서 랩 소유자는 Vm, 가상 네트워크 또는 랩 외부에 있는 다른 모든 리소스를 볼 수 없습니다.

### <a name="how-do-i-define-role-based-access-control-for-my-devtest-labs-environments-to-ensure-that-it-can-govern-while-developerstest-can-do-their-work"></a>개발자/테스트 담당자가 해당 작업을 수행하는 동안 IT 부서에서 관리할 수 있도록 내 DevTest Labs의 역할 기반 액세스 제어를 정의하려면 어떻게 해야 하나요?
광범위하게 적용되는 한 가지 패턴이 있지만 세부 사항은 조직에 따라 다릅니다.

Central IT는 필요한 사항만 소유 하 고 프로젝트 및 응용 프로그램 팀이 필요한 제어 수준을 갖도록 합니다. 이에 따라 일반적으로 중앙 IT 부서는 구독을 소유하고 네트워킹 구성과 같은 핵심 IT 기능을 처리합니다. 구독의 **소유자** 집합은 소규모인 것이 좋습니다. 이러한 소유자는 필요한 경우 추가 소유자를 추천 하거나 구독 수준 정책을 적용할 수 있습니다 (예: "공용 IP 없음").

계층 1 또는 계층 2 지원과 같이 구독 전반에서 액세스 권한이 필요한 사용자 하위 집합도 있을 수 있습니다. 이 경우 이러한 사용자에게 리소스를 관리할 수 있도록 **참가자** 액세스 권한을 부여하되, 사용자 액세스 또는 정책 조정 권한은 부여하지 않는 것이 좋습니다.

DevTest Labs 리소스는 프로젝트/애플리케이션 팀에 가까운 소유자가 소유하는 것이 좋습니다. 컴퓨터 및 필수 소프트웨어에 대 한 요구 사항을 이해 하 고 있기 때문입니다. 대부분의 조직에서 이 DevTest Labs 리소스의 소유자는 일반적으로 프로젝트/개발 책임자입니다. 이 소유자는 랩 환경 내에서 사용자 및 정책을 관리하고 DevTest Labs 환경에서 모든 VM을 관리할 수 있습니다.

**DevTest Labs Users** 역할에 프로젝트/응용 프로그램 팀 멤버를 추가 해야 합니다. 이러한 사용자는 가상 머신을 만들 수 있습니다(랩 및 구독 수준 정책에 따라). 또한 자신의 가상 머신을 관리할 수도 있습니다. 다른 사용자에게 속하는 가상 머신은 관리할 수 없습니다.

자세한 내용은 [Azure enterprise 스 캐 폴드 – 규범적 구독 거 버 넌 스 설명서](/azure/architecture/cloud-adoption/appendix/azure-scaffold)를 참조 하세요.


### <a name="how-do-i-create-a-role-to-allow-users-to-do-a-specific-task"></a>사용자가 특정 작업을 수행할 수 있도록 역할을 만들 어떻게 할까요? 있나요?
사용자 지정 역할을 만들고 사용 권한을 역할에 할당하는 방법에 대한 포괄적인 문서는 [특정 랩 정책에 사용자 권한 부여](devtest-lab-grant-user-permissions-to-specific-lab-policies.md)를 참조하세요. 랩의 모든 VM을 시작 및 중지하는 권한이 있는 **DevTest Labs 고급 사용자** 역할을 만드는 스크립트의 예는 다음과 같습니다.


```powershell
$policyRoleDef = Get-AzRoleDefinition "DevTest Labs User"
$policyRoleDef.Actions.Remove('Microsoft.DevTestLab/Environments/*')
$policyRoleDef.Id = $null
$policyRoleDef.Name = "DevTest Labs Advanced User"
$policyRoleDef.IsCustom = $true
$policyRoleDef.AssignableScopes.Clear()
$policyRoleDef.AssignableScopes.Add("subscriptions/<subscription Id>")
$policyRoleDef.Actions.Add("Microsoft.DevTestLab/labs/virtualMachines/Start/action")
$policyRoleDef.Actions.Add("Microsoft.DevTestLab/labs/virtualMachines/Stop/action")
$policyRoleDef = New-AzRoleDefinition -Role $policyRoleDef  
```

### <a name="how-can-an-organization-ensure-corporate-security-policies-are-in-place"></a>조직은 회사 보안 정책이 적절히 설정되도록 하기 위해 어떻게 해야 하나요?
조직은 이를 위해 다음 작업을 수행할 수 있습니다.

- 포괄적인 보안 정책 개발 및 게시. 정책은 소프트웨어, 클라우드 자산 사용과 관련해서 허용되는 사용에 대한 규칙을 명시합니다. 또한 정책을 명확히 위반하는 행동도 정의합니다.
- 사용자 지정 이미지, 사용자 지정 아티팩트를 개발하고, Active Directory를 사용하여 정의한 보안 영역 내의 오케스트레이션을 허용하는 배포 프로세스를 개발합니다. 이 방법은 회사 경계를 적용하고 공통된 환경 컨트롤 집합을 설정합니다. 개발자가 전체 프로세스의 일부로 보안 개발 수명 주기를 개발하고 따를 때 고려할 수 있는 환경에 대한 컨트롤입니다. 또한 목표는 개발을 방해할 수 있는 너무 제한적이 지 않은 환경을 제공 하지만 적절 한 컨트롤 집합을 제공 하는 것입니다. 랩 가상 머신을 포함하는 OU(조직 구성 단위)의 그룹 정책은 프로덕션 환경에 있는 전체 그룹 정책의 하위 집합일 수 있습니다. 또는 식별된 위험을 적절히 완화하기 위한 추가 집합일 수 있습니다.

### <a name="how-can-an-organization-ensure-data-integrity-to-ensure-that-remoting-developers-cant-remove-code-or-introduce-malware-or-unapproved-software"></a>조직은 원격 개발자가 코드를 제거하거나 맬웨어 또는 승인되지 않은 소프트웨어를 도입할 수 없도록 하기 위해 어떤 방식으로 데이터 무결성을 보장할 수 있나요?
외부 컨설턴트, 계약자 또는 DevTest Labs에서 공동으로 작업하는 원격지 직원으로 인한 위협을 완화하기 위해 여러 계층의 컨트롤이 사용됩니다.

앞서 설명한 것처럼, 첫 번째 단계는 누군가가 정책을 위반할 때 그 결과를 명확히 설명하는 허용되는 사용 정책을 작성하고 정의하는 것입니다.

원격 액세스를 위한 컨트롤의 첫 번째 계층은 랩에 직접 연결 되지 않은 VPN 연결을 통해 원격 액세스 정책을 적용 하는 것입니다.

두 번째 컨트롤 계층은 원격 데스크톱을 통한 복사 및 붙여넣기를 금지하는 그룹 정책 개체 집합을 적용하는 것입니다. 환경 외부의 RDP 서비스 및 환경의 아웃바운드 서비스(예: FTP)를 허용하지 않도록 네트워크 정책을 구현할 수 있습니다. 사용자 정의 라우팅은 모든 Azure 네트워크 트래픽을 온-프레미스로 다시 적용할 수 있지만, 콘텐츠 및 세션을 검색 하기 위해 프록시를 통해 제어 하지 않는 한, 라우팅에서 데이터 업로드를 허용할 수 있는 모든 Url을 고려 하지 못했습니다. 공용 IP는 외부 네트워크 리소스의 브리징를 허용하지 않도록 DevTest Labs를 지원하는 가상 네트워크 내에서 제한될 수 있습니다.

궁극적으로 동일한 유형의 제한 사항을 조직 전체에 적용 해야 합니다 .이는 모든 가능한 이동식 미디어 또는 콘텐츠 게시물을 허용할 수 있는 외부 Url의 방법을 고려 합니다. 보안 전문가와 함께 보안 정책을 검토하고 구현하세요. 자세한 권장 사항에 대해서는 [Microsoft 사이버 보안](https://www.microsoft.com/security/default.aspx?&WT.srch=1&wt.mc_id=AID623240_SEM_sNYnsZDs)을 참조하세요.

## <a name="lab-configuration"></a>랩 구성

### <a name="how-do-i-create-a-lab-from-a-resource-manager-template"></a>Resource Manager 템플릿에서 어떻게 랩을 만듭니까?
있는 그대로 배포하거나 랩에 대한 사용자 지정 템플릿을 만들기 위해 수정할 수 있는 [랩 Azure Resource Manager 템플릿의 GitHub 리포지토리](https://azure.microsoft.com/resources/templates/101-dtl-create-lab)를 제공합니다. 각 템플릿에는 사용자 고유의 Azure 구독에 있는 것 처럼 랩을 배포 하기 위한 링크가 있습니다. 또는 템플릿을 사용자 지정하고 [PowerShell 또는 Azure CLI를 사용하여 배포](../azure-resource-manager/templates/deploy-powershell.md)할 수 있습니다.


### <a name="can-i-have-all-virtual-machines-to-be-created-in-a-common-resource-group-instead-having-each-machine-in-its-own-resource-group"></a>각 컴퓨터를 자체 리소스 그룹에 포함 하는 대신 공용 리소스 그룹에서 모든 가상 컴퓨터를 만들 수 있나요?
예, 랩 소유자는 랩에서 리소스 그룹 할당을 처리 하도록 하거나 사용자가 지정한 공용 리소스 그룹에서 만든 모든 가상 머신을 사용할 수 있습니다.

별도 리소스 그룹 시나리오:
-   DevTest Labs는 실행 하는 모든 공용/개인 IP 가상 컴퓨터에 대 한 새 리소스 그룹을 만듭니다.
-   DevTest Labs는 동일한 크기에 속하는 공유 IP 컴퓨터에 대 한 리소스 그룹을 만듭니다.

공용 리소스 그룹 시나리오:
-   모든 가상 컴퓨터는 사용자가 지정 하는 공용 리소스 그룹에 분리 됩니다. [랩에 대 한 자세한 리소스 그룹 할당을](https://aka.ms/RGControl)알아보세요.

### <a name="how-do-i-maintain-a-naming-convention-across-my-devtest-labs-environment"></a>DevTest Labs 환경 전반에 걸쳐 명명 규칙을 유지 관리하려면 어떻게 해야 하나요?
현재 엔터프라이즈 명명 규칙을 Azure 작업으로 확장 적용하고 DevTest Labs 환경에서도 일관되게 사용할 수 있습니다. DevTest Labs를 배포할 때는 구체적인 시작 정책을 결정하는 것이 좋습니다. 일관성을 유지하려면 중앙 스크립트와 JSON 템플릿을 사용하여 이러한 정책을 배포합니다. 구독 수준에서 적용된 Azure 정책을 통해 명명 정책을 구현할 수 있습니다. Azure Policy의 JSON 샘플은 [Azure Policy 샘플](../governance/policy/samples/index.md)을 참조하세요.

### <a name="how-many-labs-can-i-create-under-the-same-subscription"></a>동일한 구독에서 얼마나 많은 랩을 만들 수 있습니까?
구독당 만들 수 있는 랩의 수에는 특정 제한이 없습니다. 그러나 사용하는 리소스 양은 구독당 제한됩니다. [Azure 구독에 대한 한도 및 할당량](../azure-resource-manager/management/azure-subscription-service-limits.md) 및 [이러한 한도를 늘리는 방법](https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests)에 대해 알아볼 수 있습니다.


### <a name="how-many-vms-can-i-create-per-lab"></a>랩당 얼마나 많은 VM을 만들 수 있습니까?
랩당 만들 수 있는 VM의 수에는 특정 제한이 없지만 하지만 사용하는 리소스(VM 코어, 공용 IP 주소 등)는 구독당 제한됩니다. [Azure 구독에 대한 한도 및 할당량](../azure-resource-manager/management/azure-subscription-service-limits.md) 및 [이러한 한도를 늘리는 방법](https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests)에 대해 알아볼 수 있습니다.

### <a name="how-do-i-determine-the-ratio-of-users-per-lab-and-the-overall-number-of-labs-that-are-needed-across-an-organization"></a>랩 당 사용자의 비율과 조직 전체에 필요한 전체 랩 수를 결정 어떻게 할까요??
동일 개발 프로젝트와 연결된 사업부와 개발 그룹은 같은 랩에 연결하는 것이 좋습니다. 그러면 같은 유형의 정책, 이미지 및 종료 정책을 두 그룹에 모두 적용할 수 있습니다.

지리적 경계도 고려해야 할 수 있습니다. 예를 들어 미국 동부 미국의 개발자는 동부 미국에서 프로 비전 된 랩을 사용할 수 있습니다. 그리고 텍사스 주 댈러스와 콜로라도 주 덴버의 개발자는 미국 중남부의 리소스를 사용하도록 지정될 수 있습니다. 외부 타사와 공동 작업을 진행하는 경우 내부 개발자가 사용하지 않는 랩에 타사를 할당할 수 있습니다.

Azure DevOps Projects 내의 특정 프로젝트에 대 한 랩을 사용할 수도 있습니다. 그리고 지정된 Azure Active Directory 그룹을 통해 보안을 적용하면 두 리소스 집합에 모두 액세스할 수 있습니다. 랩에 할당된 가상 네트워크가 사용자를 통합하는 또 다른 경계로 사용될 수도 있습니다.

### <a name="how-can-we-prevent-the-deletion-of-resources-within-a-lab"></a>랩 내에서 리소스 삭제를 방지하려면 어떻게 해야 하나요?
권한이 부여된 사용자만 리소스를 삭제하거나 랩 정책을 변경할 수 있도록 랩 수준에서 적절한 권한을 설정하는 것이 좋습니다. 개발자는 **DevTest Labs 사용자** 그룹 내에 포함되어야 합니다. 수석 개발자 또는 인프라 책임자는 **DevTest Labs 소유자**여야 합니다. 랩 소유자는 두 개만 있으면 좋습니다. 손상 방지를 위해 이 정책은 코드 리포지토리로 확대 적용됩니다. 랩 사용자에 게는 리소스를 사용할 수 있는 권한이 있지만 랩 정책을 업데이트할 수 없습니다. 랩 내에서 각 기본 제공 그룹이 소유하는 역할과 권한을 설명하는 [Azure DevTest Labs에서 소유자 및 사용자 추가](devtest-lab-add-devtest-user.md) 문서를 참조하세요.

### <a name="how-do-i-share-a-direct-link-to-my-lab"></a>랩에 대한 직접 링크를 공유하려면 어떻게 합니까?

1. [Azure Portal](https://portal.azure.com)에서 랩으로 이동 합니다.
2. 브라우저에서 **랩 URL** 을 복사한 다음 랩 사용자와 공유 합니다.

> [!NOTE]
> 랩 사용자가 Microsoft 계정 있지만 조직의 Active Directory 인스턴스의 멤버가 아닌 외부 사용자 인 경우 사용자는 공유 링크에 액세스 하려고 할 때 오류 메시지가 표시 될 수 있습니다. 외부 사용자가 오류 메시지를 확인하는 경우 사용자에게 먼저 Azure Portal의 오른쪽 위 모서리에서 해당 이름을 선택하도록 요청하세요. 그런 다음 메뉴의 디렉터리 섹션에서 사용자가 랩을 존재 하는 디렉터리를 선택할 수 있습니다.

## <a name="virtual-machines"></a>가상 머신

### <a name="why-cant-i-see-vms-on-the-virtual-machines-page-that-i-see-in-devtest-labs"></a>DevTest Labs에 표시 되는 Virtual Machines 페이지에 Vm이 표시 되지 않는 이유는 무엇 인가요?
DevTest Labs에서 VM을 만들 때 해당 VM에 액세스할 수 있는 권한이 부여 됩니다. 랩 페이지와 **Virtual Machines** 페이지 모두에서 VM을 볼 수 있습니다. **DevTest Labs 소유자** 역할에 할당 된 사용자는 랩의 **모든 Virtual Machines** 페이지에서 랩에서 만들어진 모든 vm을 볼 수 있습니다. 그러나 **DevTest Labs 사용자** 역할이 있는 사용자에 게는 다른 사용자가 만든 VM 리소스에 대 한 읽기 권한이 자동으로 부여 되지 않습니다. 따라서 이러한 Vm은 **Virtual Machines** 페이지에 표시 되지 않습니다.


### <a name="how-do-i-create-multiple-vms-from-the-same-template-at-once"></a>동일한 템플릿에서 여러 VM을 한 번에 어떻게 만듭니까?
동일한 템플릿에서 여러 VM을 동시에 만들기 위한 두 가지 옵션이 있습니다.

- [Azure DevOps 작업 확장](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks)을 사용할 수 있습니다.
- VM을 만드는 동안 [리소스 관리자 템플릿을 생성](devtest-lab-add-vm.md#save-azure-resource-manager-template) 하 고 [Windows PowerShell에서 리소스 관리자 템플릿을 배포할](../azure-resource-manager/templates/deploy-powershell.md)수 있습니다.
- 가상 컴퓨터를 만드는 동안 만들 컴퓨터의 인스턴스를 둘 이상 지정할 수도 있습니다. 가상 컴퓨터의 여러 인스턴스를 만드는 방법에 대 한 자세한 내용은 [랩 가상 컴퓨터를 만드는](devtest-lab-add-vm.md)방법에 대 한 문서를 참조 하세요.

### <a name="how-do-i-move-my-existing-azure-vms-into-my-devtest-labs-lab"></a>기존 Azure VM을 DevTest Labs 랩으로 어떻게 이동하나요?
기존 VM을 DevTest Labs에 복사하려면:

1.  [Windows PowerShell 스크립트](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/Scripts/CopyVirtualMachines/CopyAzVHDFromVMToLab.ps1)를 사용하여 기존 VM의 VHD 파일을 복사합니다.
2.  DevTest Labs 랩 내에서 [사용자 지정 이미지](devtest-lab-create-template.md) 를 만듭니다.
3.  사용자 지정 이미지에서 랩에 VM을 만듭니다.

### <a name="can-i-attach-multiple-disks-to-my-vms"></a>여러 개의 디스크를 VM에 연결할 수 있습니까?

예, 여러 개의 디스크를 VM에 연결할 수 있습니다.

### <a name="if-i-want-to-use-a-windows-os-image-for-my-testing-do-i-have-to-purchase-an-msdn-subscription"></a>테스트에 Windows OS 이미지를 사용하려면 MSDN 구독을 구매해야 하나요?
Azure에서 개발 이나 테스트를 위해 Windows 클라이언트 OS 이미지 (Windows 7 이상 버전)를 사용 하려면 다음 단계 중 하나를 수행 합니다.

- [MSDN 구독을 구입하세요](https://www.visualstudio.com/products/how-to-buy-vs).
- 기업 계약이 있는 경우 [Enterprise 개발/테스트 제안](https://azure.microsoft.com/offers/ms-azr-0148p)을 사용하여 Azure 구독을 만드세요.

각 MSDN 제품의 Azure 크레딧에 대한 자세한 내용은 [Visual Studio 구독자를 위한 월간 Azure 크레딧](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)을 참조하세요.


### <a name="how-do-i-automate-the-process-of-deleting-all-the-vms-in-my-lab"></a>랩에 있는 모든 VM을 삭제하는 프로세스를 어떻게 자동화하나요?
랩 소유자는 Azure Portal의 랩에서 Vm을 삭제할 수 있습니다. 또한 PowerShell 스크립트를 사용하여 랩에서 모든 VM을 삭제할 수 있습니다. 다음 예제에서 주석을 **변경할 값** 아래에서 매개 변수 값을 수정 합니다. Azure Portal의 랩 창에서 subscriptionId, 연구원 Resourcegroup 및 랩 이름 값을 검색할 수 있습니다.

```powershell
# Delete all the VMs in a lab.

# Values to change:
$subscriptionId = "<Enter Azure subscription ID here>"
$labResourceGroup = "<Enter lab's resource group here>"
$labName = "<Enter lab name here>"

# Sign in to your Azure account.
Connect-AzAccount

# Select the Azure subscription that has the lab. This step is optional
# if you have only one subscription.
Select-AzSubscription -SubscriptionId $subscriptionId

# Get the lab that has the VMs that you want to delete.
$lab = Get-AzResource -ResourceId ('subscriptions/' + $subscriptionId + '/resourceGroups/' + $labResourceGroup + '/providers/Microsoft.DevTestLab/labs/' + $labName)

# Get the VMs from that lab.
$labVMs = Get-AzResource | Where-Object {
          $_.ResourceType -eq 'microsoft.devtestlab/labs/virtualmachines' -and
          $_.Name -like "$($lab.Name)/*"}

# Delete the VMs.
foreach($labVM in $labVMs)
{
    Remove-AzResource -ResourceId $labVM.ResourceId -Force
}
```

## <a name="environments"></a>환경

### <a name="how-can-i-use-resource-manager-templates-in-my-devtest-labs-environment"></a>DevTest Labs 환경 내에서 Resource Manager 템플릿을 사용하려면 어떻게 해야 하나요?
[DevTest labs의 환경 기능](devtest-lab-test-env.md) 문서에 설명 된 단계를 사용 하 여 리소스 관리자 템플릿을 DevTest Labs 환경에 배포할 수 있습니다. 기본적으로는 Git 리포지토리(Azure Repos 또는 GitHub)에 Resource Manager 템플릿을 체크 인하고 랩에 [템플릿용 프라이빗 리포지토리](devtest-lab-test-env.md)를 추가합니다. DevTest Labs를 사용 하 여 개발 컴퓨터를 호스트 하는 경우에는이 시나리오가 유용 하지 않을 수 있지만, 프로덕션의 대표적인 스테이징 환경을 빌드하는 경우 유용할 수 있습니다.

랩 당 가상 머신 수 또는 사용자 단위 옵션은 환경 (리소스 관리자 템플릿)이 아닌 랩 자체에서 기본적으로 생성 되는 컴퓨터 수만 제한 하는 것도 좋습니다.

## <a name="custom-images"></a>사용자 지정 이미지

### <a name="how-can-i-set-up-an-easily-repeatable-process-to-bring-my-custom-organizational-images-into-a-devtest-labs-environment"></a>사용자 지정 조직 이미지를 DevTest Labs 환경으로 가져오기 위해 손쉽게 반복 가능한 프로세스를 설정하려면 어떻게 해야 하나요?
[이미지 팩터리 패턴에 대 한이 비디오를](https://sec.ch9.ms/ch9/8e8a/9ea0b8d4-b803-4f23-bca4-4808d9368e8a/dtlimagefactory_mid.mp4)참조 하세요. 이 시나리오는 고급 방식이며 샘플 스크립트만 제공합니다. 스크립트를 변경해야 하는 경우 환경에서 사용되는 스크립트를 직접 유지하고 관리해야 합니다.

이미지 팩터리를 만드는 방법에 대 한 자세한 내용은 [Azure DevTest Labs에서 사용자 지정 이미지 팩터리 만들기](image-factory-create.md)를 참조 하세요.

### <a name="what-is-the-difference-between-a-custom-image-and-a-formula"></a>사용자 지정 이미지와 수식 간의 차이는 무엇입니까?
사용자 지정 이미지는 관리 되는 이미지입니다. 수식은 추가 설정을 사용하여 구성한 다음 저장하고 재현할 수 있는 이미지입니다. 사용자 지정 이미지는 동일한 기본, 변경할 수 없는 이미지를 사용하여 여러 환경을 신속하게 만들려는 경우 적합할 수 있습니다. 수식은 최신 비트, 가상 네트워크 또는 서브넷의 일부 또는 특정 크기의 VM으로 구성을 재현하려는 경우 더 나을 수 있습니다. 더 자세한 설명은 [DevTest Labs에서 사용자 지정 이미지와 수식 비교](devtest-lab-comparing-vm-base-image-types.md)를 참조하세요.

### <a name="when-should-i-use-a-formula-vs-custom-image"></a>수식과 사용자 지정 이미지는 각각 어떤 경우에 사용해야 하나요?
일반적으로는 비용과 재사용 여부에 따라 사용할 항목을 결정합니다. 많은 사용자/랩에서 기본 이미지 위에 많은 소프트웨어가 있는 이미지가 필요한 시나리오를 사용 하는 경우 사용자 지정 이미지를 만들어 비용을 절감할 수 있습니다. 이는 이미지가 한 번 생성 됨을 의미 합니다. 그러면 가상 머신 설정 시간이 단축되며 설정 수행 시 가상 머신이 실행되어 발생하는 비용이 감소합니다.

하지만 소프트웨어 패키지 변경 빈도도 추가로 고려해야 합니다. 일별 빌드를 실행하며 사용자 가상 머신에 소프트웨어를 설치해야 하는 경우에는 사용자 지정 이미지 대신 수식 사용을 고려하세요.

자세히 설명 하려면 DevTest Labs에서 [사용자 지정 이미지와 수식 비교](devtest-lab-comparing-vm-base-image-types.md) 를 참조 하세요.

### <a name="how-do-i-automate-the-process-of-uploading-vhd-files-to-create-custom-images"></a>사용자 지정 이미지를 만들도록 VHD 파일 업로드 과정을 어떻게 자동화합니까?

사용자 지정 이미지를 만들도록 VHD 파일 업로드를 자동화하기 위해 두 가지 옵션이 있습니다.

- [AzCopy](../storage/common/storage-use-azcopy-v10.md)를 사용하여 랩과 연결된 스토리지 계정에 VHD 파일을 복사 또는 업로드할 수 있습니다.
- [Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md)를 사용합니다. Storage Explorer는 Windows, OS X 및 Linux에서 실행되는 독립 실행형 앱입니다.

랩과 연결된 대상 스토리지 계정을 찾으려면:

1.  [Azure Portal](https://portal.azure.com)에 로그인합니다.
2.  왼쪽 메뉴에서 **리소스 그룹**을 선택합니다.
3.  랩과 연결된 리소스 그룹을 찾고 선택합니다.
4.  **개요** 아래에서 스토리지 계정 중 하나를 선택합니다.
5.  **Blob**을 선택합니다.
6.  목록에서 업로드를 찾습니다. 항목이 없으면 4단계로 돌아가서 다른 스토리지 계정을 시도합니다.
7.  AzCopy 명령에서 대상으로 **URL**을 사용합니다.

Azure Marketplace 이미지와 고유한 사용자 지정 조직 이미지는 각각 어떤 경우에 사용해야 하나요?

### <a name="when-should-i-use-an-azure-marketplace-image-vs-my-own-custom-organizational-image"></a>Azure Marketplace 이미지와 고유한 사용자 지정 조직 이미지는 각각 어떤 경우에 사용해야 하나요?
구체적인 문제나 조직 요구 사항이 없다면 기본적으로 Azure Marketplace 이미지를 사용해야 합니다. 사용자 지정 조직 이미지를 사용해야 하는 몇 가지 일반적인 예는 다음과 같습니다.

- 기본 이미지의 일부분으로 애플리케이션을 포함해야 하는 복잡한 애플리케이션 설정
- 응용 프로그램을 설치 하 고 설치 하는 데는 많은 시간이 걸릴 수 있으며,이는 Azure Marketplace 이미지에 추가 되는 계산 시간을 효율적으로 사용 하지 않습니다.
- 개발자 및 테스터가 가상 머신에 빠르게 액세스해야 하며 새 가상 머신 설정 시간을 최소화하려는 경우
- 모든 시스템에 대해 규정 준수 또는 규제 조건(예: 보안 정책)을 적용해야 하는 경우
- 사용자 지정 이미지를 사용 하는 것은 가볍게 고려 하지 않아야 합니다. 이제 이러한 기본 이미지에 대 한 VHD 파일을 관리 해야 하므로 추가 복잡성이 도입 됩니다. 그리고 소프트웨어 업데이트를 사용하여 해당 기본 이미지를 정기적으로 패치해야 합니다. 이러한 업데이트에는 새 OS(운영 체제) 업데이트와 소프트웨어 패키지 자체에 필요한 업데이트 또는 구성 변경 내용이 포함됩니다.

## <a name="artifacts"></a>아티팩트

### <a name="what-are-artifacts"></a>아티팩트는 무엇입니까?
아티팩트는 VM에 최신 비트 또는 개발 도구를 배포하는 데 사용할 수 있는 사용자 지정 가능한 요소입니다. VM을 만들 때 VM에 아티팩트를 연결합니다. VM이 프로비전되면 아티팩트는 VM을 배포하고 구성합니다. [공용 GitHub 리포지토리에서](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts)다양 한 기존 아티팩트를 사용할 수 있습니다. [사용자 고유의 아티팩트를 작성](devtest-lab-artifact-author.md)할 수도 있습니다.

### <a name="my-artifact-failed-during-vm-creation-how-do-i-troubleshoot-it"></a>VM 생성 도중 아티팩트가 실패했습니다. 어떻게 해결합니까?
실패한 아티팩트에 대한 로그를 얻는 방법을 알아보려면 [DevTest Labs에서 아티팩트 실패를 진단하는 방법](devtest-lab-troubleshoot-artifact-failure.md)을 참조하세요.

### <a name="when-should-an-organization-use-a-public-artifact-repository-vs-private-artifact-repository-in-devtest-labs"></a>조직은 DevTest Labs에서 프라이빗 아티팩트 리포지토리와 공용 아티팩트 리포지토리 중에서 어떤 것을 사용해야 하나요?
[공용 아티팩트 리포지토리](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts)는 가장 일반적으로 사용되는 초기 소프트웨어 패키지 집합을 제공합니다. 일반적인 개발자 도구 및 추가 기능을 재현 하는 데 시간을 투자 하지 않고도 신속 하 게 배포할 수 있습니다. 자신의 개인 리포지토리를 배포 하도록 선택할 수 있습니다. 공용 리포지토리와 프라이빗 리포지토리를 함께 사용할 수 있습니다. 공용 리포지토리를 사용하지 않도록 설정할 수도 있습니다. 프라이빗 리포지토리 배포 기준을 다음 질문 및 고려 사항에 따라 결정하는 것이 좋습니다.

- 조직이 해당 DevTest Labs 제안의 일부로 회사용 라이선스 소프트웨어를 보유해야 하나요? 대답이 예인 경우 프라이빗 리포지토리를 생성해야 합니다.
- 조직이 전체 프로비전 프로세스의 일부로 필요한 특정 작업을 제공하는 사용자 지정 소프트웨어를 개발하고 있나요? 대답이 예인 경우 프라이빗 리포지토리를 배포해야 합니다.
- 조직의 거 버 넌 스 정책에서 격리가 필요 하 고 외부 리포지토리가 조직의 직접 구성 관리를 수행 하지 않는 경우 개인 아티팩트 리포지토리를 배포 해야 합니다. 이 프로세스의 일부로, 공용 리포지토리의 초기 복사본을 복사하고 프라이빗 리포지토리에 통합할 수 있습니다. 그런 다음, 조직의 누구도 더 이상 액세스할 수 없도록 공용 저장소를 사용하지 않도록 설정할 수 있습니다. 이 방법은 조직 내의 모든 사용자가 조직이 승인한 단일 리포지토리만을 강제로 보유하게 하고 구성 드리프트를 최소화합니다.


### <a name="should-an-organization-plan-for-a-single-repository-or-allow-multiple-repositories"></a>조직은 단일 리포지토리를 계획해야 하나요 아니면 여러 리포지토리를 허용해야 하나요?
조직의 전반적인 거버넌스 및 구성 관리 전략의 일환으로, 중앙 집중식 리포지토리를 사용하는 것이 좋습니다. 여러 리포지토리를 사용하면 시간이 지나면서 관리되지 않는 소프트웨어의 사일로가 될 수 있습니다. 중앙 저장소를 사용하면 여러 팀이 프로젝트에서 이 리포지토리의 아티팩트를 사용할 수 있습니다. 또한 표준화, 보안, 관리 용이성이 구현되고, 중복 작업을 수행할 필요가 없어집니다. 중앙 집중화의 일환으로, 장기 관리 및 지속성을 위해 다음 사례가 권장됩니다.

- Azure 구독이 인증 및 권한 부여에 사용하는 동일한 Azure Active Directory 테넌트에 Azure Repos를 연결합니다.
- 중앙에서 관리 되는 Azure Active Directory에 `All DevTest Labs Developers` 라는 그룹을 만듭니다. 아티팩트 개발에 참여하는 모든 개발자는 이 그룹에 배치되어야 합니다.
- 동일한 Azure Active Directory 그룹을 사용하여 Azure Repos 리포지토리 및 랩에 대한 액세스 권한을 부여할 수 있습니다.
- Azure Repos에서 분기 또는 포크를 사용하여 기본 프로덕션 리포지토리에서 개발용 리포지토리를 분리해야 합니다. 콘텐츠는 적절한 코드 검토 후에 끌어오기 요청을 통해서만 마스터 분기에 추가됩니다. 코드 검토자가 변경을 승인하면 마스터 분기의 유지 관리를 담당하는 수석 개발자가 업데이트된 코드를 병합합니다.

## <a name="cicd-integration"></a>CI/CD 통합

### <a name="does-devtest-labs-integrate-with-my-cicd-toolchain"></a>DevTest Labs는 CI/CD 도구 체인과 통합되나요?
Azure DevOps를 사용 하는 경우 [DevTest Labs 작업 확장](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks) 을 사용 하 여 DevTest labs에서 릴리스 파이프라인을 자동화할 수 있습니다. 이 확장으로 수행할 수 있는 작업 중 일부는 다음과 같습니다.

- VM을 만들고 자동으로 배포합니다. Azure File Copy 또는 PowerShell Azure DevOps Services 작업을 사용하여 최신 빌드로 VM을 구성할 수도 있습니다.
- 추가 조사를 위해 동일한 VM에 대한 버그를 재현하는 테스트 후 VM의 상태를 자동으로 캡처합니다.
- 더 이상 필요 하지 않은 경우 릴리스 파이프라인의 끝에서 VM을 삭제 합니다.

다음 블로그 게시물은 Azure DevOps Services를 사용하는 방법에 대한 지침 및 정보를 제공합니다.

- [DevTest Labs 및 Azure DevOps 확장](integrate-environments-devops-pipeline.md)
- [Azure DevOps Services에서 기존 DevTest Labs 랩에 새 VM 배포](https://www.visualstudiogeeks.com/blog/DevOps/Deploy-New-VM-To-Existing-AzureDevTestLab-From-VSTS)
- [DevTest Labs에 대한 연속 배포에 대해 Azure DevOps Services 릴리스 관리 사용](https://www.visualstudiogeeks.com/blog/DevOps/Use-VSTS-ReleaseManagement-to-Deploy-and-Test-in-AzureDevTestLabs)

다른 CI(연속 통합)/CD(지속적인 업데이트) 도구 체인의 경우 [Azure Resource Manager 템플릿](https://azure.microsoft.com/resources/templates/)을 배포하거나 [Azure PowerShell cmdlet](../azure-resource-manager/templates/deploy-powershell.md) 및 [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.DevTestLabs/)를 사용하여 동일한 시나리오를 얻을 수 있습니다. 또한 [DevTest Labs용 REST API](https://aka.ms/dtlrestapis)를 사용하여 도구 체인과 통합할 수 있습니다.

## <a name="networking"></a>네트워킹

### <a name="when-should-i-create-a-new-virtual-network-for-my-devtest-labs-environment-vs-using-an-existing-virtual-network"></a>DevTest Labs 환경용으로 신규 가상 네트워크를 생성해야 하는 경우와 기존 가상 네트워크를 사용해야 하는 경우는 각각 언제인가요?
Vm이 기존 인프라와 상호 작용 해야 하는 경우 DevTest Labs 환경 내에서 기존 가상 네트워크를 사용 하는 것이 좋습니다. Express 경로를 사용 하는 경우 구독에 사용 하도록 할당 되는 IP 주소 공간을 조각화 하지 않도록 Vnet/서브넷의 양을 최소화 하는 것이 좋습니다.

여기에서 VNet 피어 링 패턴 ([허브-스포크 모델](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke))을 사용 하는 것이 좋습니다. 이 방법은 구독에서 vnet/서브넷 통신을 가능 하 게 합니다. 각 DevTest Labs 환경에 자체 가상 네트워크가 있을 수도 있습니다.

구독 당 가상 네트워크 수에는 [제한이](../azure-resource-manager/management/azure-subscription-service-limits.md) 있습니다. 기본 수는 50이지만 100까지 높일 수 있습니다.

### <a name="when-should-i-use-a-shared-ip-vs-public-ip-vs-private-ip"></a>공유 IP, 공용 IP, 프라이빗 IP는 각각 어떠한 경우에 사용해야 하나요?

사이트 간 VPN 또는 Express 경로를 사용하는 경우에는 내부 네트워크에서만 머신에 액세스할 수 있고 공용 인터넷을 통해서는 액세스할 수 없도록 프라이빗 IP 사용을 고려합니다.

> [!NOTE]
> 랩 소유자는 사용자가 자신의 VM에 대해 공용 IP 주소를 실수로 만들지 못하도록 이 서브넷 정책을 변경할 수 있습니다. 구독 소유자는 공용 IP 만들기를 금지하는 구독 정책을 만들어야 합니다.

공유 공용 IP 사용 시에는 랩의 가상 머신이 공용 IP 주소를 공유합니다. 지정된 구독의 공용 IP 주소 제한 위반을 방지해야 하는 경우 이 방식을 사용하면 유용할 수 있습니다.

### <a name="how-do-i-ensure-that-development-and-test-virtual-machines-are-unable-to-reach-the-public-internet-are-there-any-recommended-patterns-to-set-up-network-configuration"></a>개발 및 테스트 가상 머신이 공용 인터넷에 연결하지 못하도록 하려면 어떻게 해야 하나요? 네트워크 구성 설정을 위한 권장 패턴이 있나요?

예. 인바운드 트래픽과 아웃바운드 트래픽의 두 가지 측면을 고려해야 합니다.

- **인바운드 트래픽** – 가상 머신에 공용 IP 주소가 없는 경우 인터넷을 통해 연결할 수 없습니다. 일반적인 방법은 사용자가 공용 IP 주소를 만들 수 없도록 구독 수준 정책이 설정 되었는지 확인 하는 것입니다.
- **아웃 바운드 트래픽** – 가상 컴퓨터가 공용 인터넷에 직접 액세스 하 고 회사 방화벽을 통해 트래픽을 강제로 실행 하지 않도록 하려면 강제 라우팅을 사용 하 여 express 경로 또는 VPN을 통해 온-프레미스에서 트래픽을 라우팅할 수 있습니다.

> [!NOTE]
> 프록시를 설정 하지 않고 트래픽을 차단 하는 프록시 서버가 있는 경우 랩의 아티팩트 저장소 계정에 예외를 추가 하는 것을 잊지 마세요.

가상 머신이나 서브넷용 네트워크 보안 그룹을 사용할 수도 있습니다. 이 단계를 수행하면 트래픽 허용/차단을 위한 보호 계층이 추가됩니다.

## <a name="troubleshooting"></a>문제 해결

### <a name="why-isnt-my-existing-virtual-network-saving-properly"></a>기존 가상 네트워크가 제대로 저장되지 않는 이유는 무엇입니까?
한 가지 가능성은 가상 네트워크 이름에 기간이 포함되어 있는 것입니다. 이 경우 기간을 제거하거나 하이픈으로 바꿔보세요. 그런 다음 가상 네트워크 저장을 다시 시도하세요.

### <a name="why-do-i-get-a-parent-resource-not-found-error-when-i-provision-a-vm-from-powershell"></a>PowerShell에서 VM을 프로비전할 때 "부모 리소스를 찾을 수 없음" 오류가 표시되는 이유는 무엇인가요?
한 리소스가 다른 리소스의 부모이면 부모 리소스는 자식 리소스를 만들기 전에 존재해야 합니다. 부모 리소스가 없으면 **ParentResourceNotFound** 메시지가 표시 됩니다. 부모 리소스에 대한 종속성을 지정하지 않으면 자식 리소스는 부모보다 먼저 배포될 수 있습니다.

리소스 그룹의 랩에서 VM은 자식 리소스입니다. PowerShell을 사용하여 VM을 배포하는 데 Resource Manager 템플릿을 사용할 경우 PowerShell 스크립트에 제공된 리소스 그룹 이름은 랩의 리소스 그룹 이름이어야 합니다. 자세한 내용은 [일반적인 Azure 배포 오류 문제 해결](../azure-resource-manager/templates/common-deployment-errors.md)을 참조하세요.

### <a name="where-can-i-find-more-error-information-if-a-vm-deployment-fails"></a>VM 배포에 실패하면 오류 정보를 어디에서 더 찾을 수 있나요?
VM 배포 오류는 활동 로그에 캡처됩니다. **감사 로그** 또는 **가상 머신 진단** 에서 랩 vm 활동 로그는 랩의 vm 페이지에 있는 리소스 메뉴에서 찾을 수 있습니다 .이 페이지는 내 가상 머신 목록에서 VM을 선택한 후에 표시 됩니다.

경우에 따라 VM 배포가 시작되기 전에 배포 오류가 발생합니다. 예는 VM을 사용하여 만든 리소스에 대한 구독 제한을 초과하는 경우입니다. 이 경우 오류 세부 정보는 랩 수준 활동 로그에 캡처됩니다. 활동 로그는 **구성 및 정책** 설정의 맨 아래에 있습니다. Azure에서 활동 로그를 사용하는 방법에 대한 자세한 내용은 [리소스에 대한 작업을 감사하기 위해 활동 로그 보기](../azure-resource-manager/management/view-activity-logs.md)를 참조하세요.

### <a name="why-do-i-get-location-is-not-available-for-resource-type-error-when-trying-to-create-a-lab"></a>랩을 만들려고 할 때 "리소스 종류에 위치를 사용할 수 없습니다" 오류가 발생 하는 이유는 무엇 인가요?
랩을 만들려고 하면 다음과 유사한 오류 메시지가 표시 될 수 있습니다.

```
The provided location 'australiacentral' is not available for resource type 'Microsoft.KeyVault/vaults'. List of available regions for the resource type is 'northcentralus,eastus,northeurope,westeurope,eastasia,southeastasia,eastus2,centralus,southcentralus,westus,japaneast,japanwest,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia,canadacentral,canadaeast,uksouth,ukwest,westcentralus,westus2,koreacentral,koreasouth,francecentral,southafricanorth
```

다음 단계 중 하나를 수행 하 여이 오류를 해결할 수 있습니다.

#### <a name="option-1"></a>옵션 1
[지역별 사용 가능한 제품](https://azure.microsoft.com/global-infrastructure/services/) 페이지의 Azure 지역에서 리소스 종류의 가용성을 확인 합니다. 리소스 종류를 특정 지역에서 사용할 수 없는 경우 DevTest Labs는 해당 지역에서 랩 만들기를 지원 하지 않습니다. 랩을 만들 때 다른 지역을 선택 합니다.

#### <a name="option-2"></a>옵션 2
리소스 종류를 해당 지역에서 사용할 수 있는 경우 구독에 등록 되었는지 확인 합니다. [이 문서](../azure-resource-manager/management/resource-providers-and-types.md)에 표시 된 것 처럼 구독 소유자 수준에서이 작업을 수행할 수 있습니다.
