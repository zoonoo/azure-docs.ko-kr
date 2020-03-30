---
author: memildin
ms.author: memildin
manager: rkarlin
ms.date: 02/24/2020
ms.topic: include
ms.openlocfilehash: c77849b2285283a34e6adf84dc3845a4076407af
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77597946"
---
## <a name="attack-scenario"></a>공격 시나리오

무차별 암호 대입 공격(brute force attack)은 일반적으로 VM에 대한 액세스 권한을 얻는 방법으로 관리 포트를 대상으로 합니다. 성공하면 공격자가 VM을 제어할 수 있으며 사용자 환경으로의 발판을 마련하게 됩니다.

무차별 암호 대입 공격을 줄이기 위한 한 가지 방법은 포트가 열려 있는 시간의 양을 제한하는 것입니다. 관리 포트를 항상 열 필요는 없습니다. 예를 들어 관리 또는 유지 관리 작업을 수행하기 위해 VM에 연결된 상태에서만 열어야 합니다. 적시에 사용하도록 설정하면 보안 센터는 네트워크 보안 그룹(NSG) 및 Azure 방화벽 규칙을 사용하여 관리 포트에 대한 액세스를 제한하여 공격자의 대상이 될 수 없도록 합니다. [network security group](../articles/virtual-network/security-overview.md#security-rules)

![Just-In-Time 시나리오](../articles/security-center/media/security-center-just-in-time/just-in-time-scenario.png)

## <a name="how-does-jit-access-work"></a>JIT 액세스는 어떻게 작동하나요?

Just-In-Time을 사용하도록 설정하면 Security Center는 NSG 규칙을 만들어 Azure VM으로의 인바운드 트래픽을 잠급니다. VM에서 인바운드 트래픽을 잠글 포트를 선택합니다. 이러한 포트는 Just-In-Time 솔루션에 의해 제어됩니다.

사용자가 VM에 대한 액세스를 요청하면 보안 센터는 사용자에게 해당 VM에 대한 [RBAC(역할 기반 액세스 제어)](../articles/role-based-access-control/role-assignments-portal.md) 권한이 있는지 확인합니다. 요청이 승인되면 보안 센터는 지정된 시간 동안 선택한 포트 및 요청된 소스 IP 주소 또는 범위에 대한 인바운드 트래픽을 허용하도록 NSG(네트워크 보안 그룹) 및 Azure 방화벽을 자동으로 구성합니다. 시간이 만료되면 Security Center에서 NSG를 이전 상태로 복원합니다. 단, 이미 설정된 연결은 중단되지 않습니다.

 > [!NOTE]
 > Azure 방화벽 뒤에 있는 VM에 대한 JIT 액세스 요청이 승인되면 보안 센터에서 NSG 및 방화벽 정책 규칙을 자동으로 변경합니다. 지정된 시간 동안 규칙은 선택한 포트에 대한 인바운드 트래픽을 허용하고 요청된 소스 IP 주소 또는 범위입니다. 시간이 끝나면 보안 센터에서 방화벽 및 NSG 규칙을 이전 상태로 복원합니다.


## <a name="permissions-needed-to-configure-and-use-jit"></a>JIT를 구성 및 사용하는 데 필요한 권한

| 사용자가 다음을 수행할 수 있도록 하려면 다음을 수행합니다. | 설정할 권한|
| --- | --- |
| VM에 대한 JIT 정책 구성 또는 편집 | *역할에 다음 작업을 할당합니다.*  <ul><li>VM과 연결된 구독 또는 리소스 그룹의 범위에서 다음을 수행합니다.<br/> `Microsoft.Security/locations/jitNetworkAccessPolicies/write` </li><li> VM의 구독 또는 리소스 그룹의 범위에서: <br/>`Microsoft.Compute/virtualMachines/write`</li></ul> | 
|VM에 대한 JIT 액세스 요청 | *사용자에게 다음 작업을 할당합니다.*  <ul><li>VM과 연결된 구독 또는 리소스 그룹의 범위에서 다음을 수행합니다.<br/>  `Microsoft.Security/locations/jitNetworkAccessPolicies/initiate/action` </li><li>VM과 연결된 구독 또는 리소스 그룹의 범위에서 다음을 수행합니다.<br/>  `Microsoft.Security/locations/jitNetworkAccessPolicies/*/read` </li><li>  구독 또는 리소스 그룹 또는 VM의 범위에서:<br/> `Microsoft.Compute/virtualMachines/read` </li><li>  구독 또는 리소스 그룹 또는 VM의 범위에서:<br/> `Microsoft.Network/networkInterfaces/*/read` </li></ul>|