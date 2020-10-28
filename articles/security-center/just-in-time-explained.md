---
title: Azure Security Center에서 just-in-time virtual machine 액세스 이해
description: 이 문서에서는 Azure Security Center의 just-in-time VM 액세스를 사용 하 여 Azure 가상 머신에 대 한 액세스를 제어 하는 방법을 설명 합니다.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 07/12/2020
ms.author: memildin
ms.openlocfilehash: fe358e35f2d68a3e55e9d9bb4ac57a13f42085e3
ms.sourcegitcommit: 3e8058f0c075f8ce34a6da8db92ae006cc64151a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/27/2020
ms.locfileid: "92629241"
---
# <a name="understanding-just-in-time-jit-vm-access"></a>JIT(Just-In-Time) VM 액세스 이해

이 페이지에서는 Azure Security Center의 JIT (just-in-time) VM 액세스 기능 및 권장 구성의 논리에 대 한 원칙을 설명 합니다.

Azure Portal (Security Center 또는 Azure Virtual Machines)를 사용 하 여 Vm에 JIT를 적용 하거나 프로그래밍 방식으로 관리 하는 방법을 알아보려면 JIT를 사용 하 여 [관리 포트를 보호 하는 방법](security-center-just-in-time.md)을 참조 하세요.


## <a name="the-risk-of-open-management-ports-on-a-virtual-machine"></a>가상 머신에서 열린 관리 포트의 위험

위협 행위자는 RDP 또는 SSH와 같은 개방형 관리 포트를 사용 하 여 액세스 가능한 컴퓨터를 적극적으로 사냥 합니다. 모든 가상 머신은 공격의 대상이 될 수 있습니다. VM이 손상되면 사용자 환경 내 추가 리소스를 공격하는 진입점으로 사용됩니다.



## <a name="why-jit-vm-access-is-the-solution"></a>JIT VM 액세스가 솔루션인 이유 

모든 사이버 보안 방지 기술 처럼 목표는 공격 노출 영역을 줄이는 것입니다. 이 경우에는 열려 있는 포트, 특히 관리 포트의 수를 줄일 수 있습니다.

합법적인 사용자는 이러한 포트를 사용 하기 때문에 이러한 포트를 닫지 않는 것은 실용적이 지 않습니다.

이 딜레마를 해결 하기 위해 Azure Security Center는 JIT를 제공 합니다. JIT를 사용 하면 Vm에 대 한 인바운드 트래픽을 잠글 수 있으며, 필요할 때 Vm에 연결 하는 데 편리한 액세스를 제공 하는 동시에 공격에 대 한 노출을 줄일 수 있습니다.



## <a name="how-jit-operates-with-network-security-groups-and-azure-firewall"></a>네트워크 보안 그룹 및 Azure 방화벽을 사용 하 여 JIT가 작동 하는 방법

Just-in-time VM 액세스를 사용 하도록 설정 하면 인바운드 트래픽이 차단 될 VM의 포트를 선택할 수 있습니다. Security Center nsg ( [네트워크 보안 그룹](../virtual-network/network-security-groups-overview.md#security-rules) ) 및 [Azure 방화벽 규칙](../firewall/rule-processing.md)에서 선택한 포트에 대해 "모든 인바운드 트래픽 거부" 규칙이 있는지 확인 합니다. 이러한 규칙은 Azure Vm의 관리 포트에 대 한 액세스를 제한 하 고 공격 으로부터 보호 합니다. 

선택한 포트에 대 한 다른 규칙이 이미 존재 하는 경우 기존 규칙은 새 "모든 인바운드 트래픽 거부" 규칙 보다 우선적으로 적용 됩니다. 선택한 포트에 기존 규칙이 없는 경우 새 규칙은 NSG 및 Azure 방화벽에서 가장 높은 우선 순위를 사용 합니다.

사용자가 VM에 대 한 액세스를 요청 하면 Security Center 사용자에 게 해당 VM에 대 한 azure [RBAC (역할 기반 액세스 제어)](../role-based-access-control/role-assignments-portal.md) 권한이 있는지 확인 합니다. 요청이 승인 되 면 지정 된 시간 동안 관련 IP 주소 또는 범위에서 선택한 포트에 대 한 인바운드 트래픽을 허용 하도록 NSGs와 Azure 방화벽을 구성 Security Center 합니다. 시간이 만료되면 Security Center에서 NSG를 이전 상태로 복원합니다. 이미 설정 된 연결은 중단 되지 않습니다.

> [!NOTE]
> JIT는 [Azure 방화벽 관리자](../firewall-manager/overview.md)에서 제어 하는 azure 방화벽으로 보호 되는 vm을 지원 하지 않습니다.




## <a name="how-security-center-identifies-which-vms-should-have-jit-applied"></a>Security Center에서 JIT를 적용 해야 하는 Vm을 식별 하는 방법

아래 다이어그램은 지원 되는 Vm을 범주화 하는 방법을 결정할 때 Security Center 적용 되는 논리를 보여 줍니다. 

[![JIT (just-in-time) VM (가상 컴퓨터) 논리 흐름](media/just-in-time-explained/jit-logic-flow.png)](media/just-in-time-explained/jit-logic-flow.png#lightbox)

JIT를 활용 하 여 이점을 누릴 수 있는 컴퓨터를 찾을 Security Center 경우 해당 컴퓨터를 권장 구성의 **비정상 리소스** 탭에 추가 합니다. 

![JIT (just-in-time) VM (가상 컴퓨터) 액세스 권장 사항](./media/just-in-time-explained/unhealthy-resources.png)


## <a name="faq---questions-about-just-in-time-virtual-machine-access"></a>FAQ-just-in-time virtual machine access에 대 한 질문

### <a name="what-permissions-are-needed-to-configure-and-use-jit"></a>JIT를 구성 하 고 사용 하는 데 필요한 권한은 무엇 인가요?

JIT를 사용 하려면 구독에서 서버를 사용 하도록 설정 하기 [위해 Azure Defender](defender-for-servers-introduction.md) 가 필요 합니다. 

**판독기** 및 **securityreader** 역할은 둘 다 JIT 상태와 매개 변수를 볼 수 있습니다.

JIT를 사용할 수 있는 사용자 지정 역할을 만들려면 아래 표의 세부 정보가 필요 합니다.

> [!TIP]
> VM에 대 한 JIT 액세스를 요청 해야 하는 사용자에 게 최소 권한 역할을 만들고 다른 JIT 작업을 수행 하지 않으려면 Security Center GitHub 커뮤니티 페이지에서 [JitLeastPrivilegedRole 스크립트](https://github.com/Azure/Azure-Security-Center/tree/master/Powershell%20scripts/JIT%20Custom%20Role) 를 사용 합니다.

| 사용자가 다음을 수행할 수 있도록 설정: | 설정할 권한|
| --- | --- |
| VM에 대 한 JIT 정책 구성 또는 편집 | *역할에 다음 작업을 할당합니다.*  <ul><li>VM과 연결 된 구독 또는 리소스 그룹의 범위:<br/> `Microsoft.Security/locations/jitNetworkAccessPolicies/write` </li><li> VM의 구독 또는 리소스 그룹의 범위: <br/>`Microsoft.Compute/virtualMachines/write`</li></ul> | 
|VM에 대한 JIT 액세스 요청 | *사용자에게 다음 작업을 할당합니다.*  <ul><li>VM과 연결 된 구독 또는 리소스 그룹의 범위:<br/>  `Microsoft.Security/locations/jitNetworkAccessPolicies/initiate/action` </li><li>VM과 연결 된 구독 또는 리소스 그룹의 범위:<br/>  `Microsoft.Security/locations/jitNetworkAccessPolicies/*/read` </li><li>  구독 또는 리소스 그룹 또는 VM의 범위에서 다음을 수행 합니다.<br/> `Microsoft.Compute/virtualMachines/read` </li><li>  구독 또는 리소스 그룹 또는 VM의 범위에서 다음을 수행 합니다.<br/> `Microsoft.Network/networkInterfaces/*/read` </li></ul>|
|JIT 정책 읽기| *사용자에게 다음 작업을 할당합니다.*  <ul><li>`Microsoft.Security/locations/jitNetworkAccessPolicies/read`</li><li>`Microsoft.Security/locations/jitNetworkAccessPolicies/initiate/action`</li><li>`Microsoft.Security/policies/read`</li><li>`Microsoft.Compute/virtualMachines/read`</li><li>`Microsoft.Network/*/read`</li>|
|||





## <a name="next-steps"></a>다음 단계

이 페이지에서는 JIT (just-in-time) VM (가상 컴퓨터) 액세스를 사용 해야 하는 _이유_ 를 설명 했습니다. 

Jit를 사용 하도록 설정 하 고 JIT 사용 Vm에 대 한 액세스를 요청 하는 방법에 대 한 자세한 내용은 방법 문서로 이동 합니다.

> [!div class="nextstepaction"]
> [JIT를 사용 하 여 관리 포트를 보호 하는 방법](security-center-just-in-time.md)