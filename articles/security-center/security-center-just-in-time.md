---
title: Azure Security Center에서 Just-In-Time 가상 머신 액세스 | Microsoft Docs
description: 이 문서에서는 Azure Security Center에서 Just-In-Time VM 액세스가 Azure 가상 머신에 대한 액세스를 제어하는 데 어떻게 도움이 되는지 보여 줍니다.
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: 671930b1-fc84-4ae2-bf7c-d34ea37ec5c7
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 6/17/2019
ms.author: v-mohabe
ms.openlocfilehash: eb9366acf82c94bdf99c4d4f0c7c6bdf4f51e06d
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/20/2019
ms.locfileid: "67294999"
---
# <a name="manage-virtual-machine-access-using-just-in-time"></a>Just-In-Time을 사용하여 가상 머신 액세스 관리

JIT(Just-In-Time) VM(가상 머신) 액세스를 사용하면 Azure VM으로의 인바운드 트래픽을 잠글 수 있어 필요할 때 VM 연결을 위한 간편한 액세스를 제공하면서도 공격에 대한 노출을 줄일 수 있습니다.

> [!NOTE]
> Just-In-Time 기능은 Security Center의 표준 계층에서 사용할 수 있습니다.  Security Center의 가격 책정 계층에 대해 자세히 알아보려면 [가격 책정](security-center-pricing.md)을 참조하세요.


> [!NOTE]
> Security Center Just-In-Time VM 액세스는 현재 Azure Resource Manager를 통해 배포된 VM만 지원합니다. 클래식 및 Resource Manager 배포 모델에 대한 자세한 내용은 [Azure Resource Manager 및 클래식 배포](../azure-resource-manager/resource-manager-deployment-model.md)를 참조하세요.

## <a name="attack-scenario"></a>공격 시나리오

무차별 암호 대입 공격(brute force attack)은 일반적으로 VM에 대한 액세스 권한을 얻는 방법으로 관리 포트를 대상으로 합니다. 성공하면 공격자가 VM을 제어할 수 있으며 사용자 환경으로의 발판을 마련하게 됩니다.

무차별 암호 대입 공격을 줄이기 위한 한 가지 방법은 포트가 열려 있는 시간의 양을 제한하는 것입니다. 관리 포트는 항상 열려 있을 필요가 없습니다. 예를 들어 관리 또는 유지 관리 작업을 수행하기 위해 VM에 연결되는 동안에만 열려 있어야 합니다. 보안 센터는 적시에 설정 되 면 [네트워크 보안 그룹](../virtual-network/security-overview.md#security-rules) (NSG) 및 공격자에 의해 대상으로 지정할 수 있도록 관리 포트에 대 한 액세스를 제한 하는 Azure 방화벽 규칙을 합니다.

![Just-In-Time 시나리오](./media/security-center-just-in-time/just-in-time-scenario.png)

## <a name="how-does-jit-access-work"></a>JIT 액세스는 어떻게 작동하나요?

Just-In-Time을 사용하도록 설정하면 Security Center는 NSG 규칙을 만들어 Azure VM으로의 인바운드 트래픽을 잠급니다. VM에서 인바운드 트래픽을 잠글 포트를 선택합니다. 이러한 포트는 Just-In-Time 솔루션에 의해 제어됩니다.

사용자가 VM에 액세스를 요청하면 Security Center는 해당 사용자에게 VM에 대한 액세스 권한을 성공적으로 요청하도록 허용하는 [RBAC(역할 기반 액세스 제어)](../role-based-access-control/role-assignments-portal.md) 권한이 있는지 확인합니다. 요청이 승인 되 면 Security Center 선택된 된 포트 및 요청 된 원본 IP 주소 또는 범위를 지정 된 시간에 인바운드 트래픽을 허용 하도록 네트워크 보안 그룹 (Nsg) 및 Azure 방화벽 자동으로 구성 합니다. 시간이 만료되면 Security Center에서 NSG를 이전 상태로 복원합니다. 단, 이미 설정된 연결은 중단되지 않습니다.

 > [!NOTE]
 > 방화벽 뒤에 있는 Azure VM에 대 한 JIT 액세스 요청이 승인 되 면 Security Center은 자동으로 정책 규칙을 NSG와 방화벽을 변경 합니다. 지정 된 시간, 규칙에는 선택한 포트와 요청 된 원본 IP 주소 또는 범위에 인바운드 트래픽을 허용 합니다. 시간을 통해 되 면 Security Center 방화벽 및 NSG 규칙을 이전 상태로 복원 합니다.


## <a name="permissions-needed-to-configure-and-use-jit"></a>JIT를 구성 및 사용하는 데 필요한 권한

| 사용자가 사용할 수 있도록 합니다. | 사용 권한을 설정 하려면|
| --- | --- |
| 구성 또는 VM에 대 한 JIT 정책 편집 | *역할에 이러한 작업을 할당 합니다.*  VM에 연결된 구독 또는 리소스 그룹의 범위에서 ```Microsoft.Security/locations/jitNetworkAccessPolicies/write``` 구독 또는 리소스 그룹 또는 VM의 범위. ```Microsoft.Compute/virtualMachines/write``` | 
| ||
|VM에 대한 JIT 액세스 요청 | *사용자에 게 이러한 작업을 할당 합니다.*  VM에 연결된 구독 또는 리소스 그룹의 범위에서  ```Microsoft.Security/locations/{the_location_of_the_VM}/jitNetworkAccessPolicies/initiate/action``` 구독 또는 리소스 그룹 또는 VM의 범위. ```Microsoft.Compute/virtualMachines/read``` |


## <a name="configure-jit-on-a-vm"></a>VM에서 JIT 구성

세 가지 방법을 사용 하 여 VM에서 JIT 정책을 구성할 수 있습니다.

- [Azure Security Center에서 JIT 액세스를 구성 합니다.](#jit-asc)
- [Azure VM 블레이드에서 JIT 액세스를 구성 합니다.](#jit-vm)
- [프로그래밍 방식으로 VM에서 JIT 정책 구성](#jit-program)

## <a name="configure-jit-in-asc"></a>ASC에서 JIT 구성

ASC에서 JIT 정책을 사용 하 여 VM에 JIT 정책 및 요청 액세스를 구성할 수 있습니다.


### Vm ASC에서 JIT 액세스를 구성 합니다. <a name="jit-asc"></a>

1. **Security Center** 대시보드를 엽니다.

2. 왼쪽된 창에서 선택 **Just in time VM 액세스**합니다.

    ![Just-In-Time VM 액세스 타일](./media/security-center-just-in-time/just-in-time.png)

    **Just-In-Time VM 액세스** 창이 열립니다.

      ![Just-In-Time 액세스 사용](./media/security-center-just-in-time/enable-just-in-time.png)

    **Just-In-Time VM 액세스**는 VM 상태에 대한 정보를 제공합니다.

    - **구성됨** - Just-In-Time VM 액세스를 지원하도록 구성된 VM입니다. 표시된 데이터는 지난 주에 대한 데이터이며 각 VM에 대해 승인된 요청 수, 마지막 액세스 날짜 및 시간, 마지막 사용자를 포함합니다.
    - **권장** - Just-In-Time VM 액세스를 지원할 수 있지만 구성되지 않은 VM입니다. 이러한 VM에는 Just-In-Time VM 액세스 제어를 사용하도록 설정하는 것이 좋습니다. 
    - **권장 사항 없음** - VM이 권장되지 않을 수 있는 이유입니다.
      - 누락된 NSG - Just-In-Time 솔루션을 사용하려면 NSG가 있어야 합니다.
      - 클래식 VM - Security Center Just-In-Time VM 액세스는 현재 Azure Resource Manager를 통해 배포된 VM만 지원합니다. 클래식 배포는 Just-In-Time 솔루션에서 지원되지 않습니다. 
      - Just-in-time 솔루션이 구독 또는 리소스 그룹의 보안 정책에서 해제 된 경우 또는 VM 공용 IP가 없는 곳에서 NSG를 포함 하지 않는 경우이 범주에는 다른-VM.

3. **권장** 탭을 선택합니다.

4. 아래 **가상 머신**를 사용 하도록 설정 하려는 Vm을 클릭 합니다. 그러면 VM 옆에 있는 확인 표시가 선택됩니다.

5. 클릭 **Vm에서 JIT 사용**합니다.
   -. 이 블레이드는 Azure Security Center에서 권장하는 기본 포트를 표시합니다.
      - 22 - SSH
      - 3389 - RDP
      - 5985 - WinRM 
      - 5986 - WinRM
6. 또한 사용자 지정 포트를 구성할 수 있습니다.

      1. **추가**를 클릭합니다. 합니다 **포트 추가 구성** 창이 열립니다.
      2. 모두 기본 구성 하려는 각 포트에 대 한 사용자 지정 사용자 지정할 수 있습니다 다음 설정 및:

    - **프로토콜 유형** - 요청이 승인될 때 이 포트에서 허용되는 프로토콜입니다.
    - **허용된 원본 IP 주소** - 요청이 승인될 때 이 포트에서 허용되는 IP 범위입니다.
    - **최대 요청 시간** - 특정 포트를 열 수 있는 최대 기간입니다.

     3. **확인**을 클릭합니다.

1. **저장**을 클릭합니다.

> [!NOTE]
>JIT VM 액세스를 VM에 대 한 사용 Azure Security Center는 선택한 포트에 대 한 "모든 인바운드 트래픽을 거부" 규칙와 연결 된 네트워크 보안 그룹 및 Azure 방화벽에 만듭니다. 선택된 된 포트에 대 한 다른 규칙 작성 된 경우 다음 기존 규칙 보다 우선적으로 적용 새 "모든 인바운드 트래픽을 거부" 규칙입니다. 선택된 된 포트에서 기존 규칙이 없습니다, 다음 새 "모든 인바운드 트래픽을 거부" 규칙을 네트워크 보안 그룹 및 Azure 방화벽에서 상위 우선적으로 적용 합니다.


## <a name="request-jit-access-via-asc"></a>ASC 통해 JIT 액세스를 요청 합니다.

ASC 통해 VM에 대 한 액세스를 요청 합니다.

1. **Just-In-Time VM 액세스**에서 **구성됨** 탭을 선택합니다.

2. 아래 **가상 머신**, Vm에 대 한 액세스를 요청 하려면을 클릭 합니다. 그러면 VM 옆에 있는 확인 표시 합니다.


    - 에 있는 아이콘을 **연결 세부 정보** 열 NSG 또는 FW JIT 사용 되는지 여부를 나타냅니다. 둘 다에서 사용 하는 경우 방화벽 아이콘만 표시 됩니다.

    - 합니다 **연결 정보** 열 VM을 연결 하는 데 필요한 올바른 정보를 제공 합니다. 뿐만 아니라 열린된 포트를 나타냅니다.

      ![Just-In-Time 액세스 요청](./media/security-center-just-in-time/request-just-in-time-access.png)

3. 클릭 **액세스를 요청**합니다. 합니다 **액세스를 요청** 창이 열립니다.

      ![JIT 세부 정보](./media/security-center-just-in-time/just-in-time-details.png)

4. **액세스 요청** 아래에서 각 VM에 대해 열려는 포트, 포트가 열려 있는 원본 IP 주소 및 포트를 열어 둘 기간을 구성합니다. Just-In-Time 정책에 구성된 포트에만 액세스를 요청할 수 있습니다. 각 포트에는 Just-In-Time 정책에서 파생된 최대 허용된 시간이 있습니다.

5. 클릭 **포트를 열어야**합니다.

> [!NOTE]
> 액세스를 요청하는 사용자가 프록시 뒤에 있으면 **내 IP** 옵션이 작동하지 않습니다. 조직의 전체 IP 주소 범위를 정의해야 할 수 있습니다.

## <a name="edit-a-jit-access-policy-via-asc"></a>ASC 통해 JIT 액세스 정책 편집

해당 VM에 대해 보호할 새 포트를 추가 및 구성하거나 이미 보호된 포트와 관련된 다른 설정을 변경하여 VM의 기존 Just-In-Time 정책을 변경할 수 있습니다.

VM의 기존 Just-In-Time 정책을 편집하려면
1. **구성됨** 탭의 **VM** 아래에서 해당 VM에 대한 행 안의 3개 점을 클릭하여 포트를 추가할 VM을 선택합니다. 

1. **편집**을 선택합니다.
1. **JIT VM 액세스 구성**에서 포트를 클릭하여 이미 보호된 포트의 기존 설정을 편집하거나 새로운 사용자 지정 포트를 추가할 수 있습니다. 
  ![jit vm 액세스](./media/security-center-just-in-time/edit-policy.png)

## <a name="audit-jit-access-activity-in-asc"></a>ASC에서 JIT 액세스 활동 감사

로그 검색을 사용하여 VM 활동에 대한 정보를 얻을 수 있습니다. 로그를 보려면

1. **Just-In-Time VM 액세스**에서 **구성됨** 탭을 선택합니다.
2. 아래 **Vm**, 해당 VM에 대 한 행 안의 3 개 점을 클릭 하 여에 대 한 정보를 보려는 VM을 선택 하 고 선택 **활동 로그** 메뉴에서. 합니다 **활동 로그** 열립니다.

   ![활동 로그 선택](./media/security-center-just-in-time/select-activity-log.png)

   **활동 로그**는 시간, 날짜 및 구독과 함께 해당 VM에 대한 이전 작업의 필터링된 보기를 제공합니다.

**여기를 클릭하여 모든 항목을 CSV로 다운로드하세요.** 를 선택하여 로그 정보를 다운로드할 수 있습니다.

필터를 수정 하 고 클릭 **적용** 만드는 로그를 검색 합니다.



## Azure VM 블레이드에서 JIT 액세스를 구성 합니다. <a name="jit-vm"></a>

편의를 위해 Azure의 VM 블레이드 내에서 직접 JIT를 사용하여 VM에 연결할 수 있습니다.

### <a name="configure-jit-access-on-a-vm-via-the-azure-vm-blade"></a>Azure VM 블레이드를 통해 VM에서 JIT 액세스를 구성 합니다.

VM에서 Just-In-Time 액세스를 쉽게 롤아웃할 수 있도록 하려면 VM 내에서 직접 Just-In-Time 액세스만 허용하도록 VM을 설정하면 됩니다.

1. Azure Portal에서 **가상 머신**을 선택합니다.
2. Just-In-Time 액세스로 제한하려는 가상 머신을 클릭합니다.
3. 메뉴에서 **구성**을 클릭합니다.
4. **Just-In-Time 액세스**에서 **Just-In-Time 정책 사용**을 클릭합니다. 

이렇게 하면 다음 설정을 사용한 VM Just-In-Time 액세스를 설정합니다.

- Windows 서버:
    - RDP 포트 3389
    - 허용되는 최대 액세스: 3시간
    - 허용 되는 원본 IP 주소에 설정 됩니다.
- Linux 서버:
    - SSH 포트 22
    - 허용되는 최대 액세스: 3시간
    - 허용 되는 원본 IP 주소에 설정 됩니다.
     
VM에 이미 Just-In-Time이 사용 설정된 경우 구성 페이지로 이동하면 Just-In-Time이 사용하도록 설정되었으며 링크를 사용해 Azure Security Center에서 정책을 열어 설정을 보고 변경할 수 있음을 확인할 수 있습니다.

![vm의 jit 구성](./media/security-center-just-in-time/jit-vm-config.png)

### <a name="request-jit-access-to-a-vm-via-the-azure-vm-blade"></a>Azure VM 블레이드를 통해 VM에 대 한 JIT 액세스를 요청 합니다.

Azure Portal에서 VM에 연결하려고 할 때 Azure는 해당 VM에 구성된 Just-In-Time 액세스 정책이 있는지 확인합니다.

- VM에 JIT 정책을 구성한 경우 **액세스 요청**을 클릭하여 VM에 대해 설정된 JIT 정책에 따라 액세스 권한을 얻을 수 있습니다. 

  >![jit 요청](./media/security-center-just-in-time/jit-request.png)

  다음 기본 매개 변수를 사용 하 여 액세스가 필요 합니다.

  - **원본 IP**: 'Any' (*) (변경할 수 없음)
  - **시간 범위**: 3 시간 (변경할 수 없음)  <!--Isn't this set in the policy-->
  - **포트 번호** RDP 포트 3389에 대 한 Windows / Linux (변경할 수 있음)에 대 한 포트 22

    > [!NOTE]
    > Azure 방화벽에 의해 보호 된 VM에 대 한 요청이 승인 되 면 Security Center는 VM에 연결 하는 데 적절 한 연결 세부 정보 (DNAT 테이블에서 포트 매핑)를 사용 하 여 사용자를 제공 합니다.

- VM에 JIT를 구성하지 않은 경우 JIT 정책을 구성하라는 메시지가 표시됩니다.

  ![jit 프롬프트](./media/security-center-just-in-time/jit-prompt.png)

## 프로그래밍 방식으로 VM에서 JIT 정책 구성  <a name="jit-program"></a>

REST API 및 PowerShell을 통해 Just-In-Time을 설정하고 사용할 수 있습니다.

## <a name="jit-vm-access-via-rest-apis"></a>REST Api를 통해 JIT VM 액세스

Just-In-Time VM 액세스 기능은 Azure Security Center API를 통해 사용할 수 있습니다. 이 API를 통해 구성된 VM에 대한 정보를 가져오고 새로 추가하고 VM에 대한 액세스를 요청하는 등을 할 수 있습니다. Just-In-Time REST API에 대한 자세한 내용은 [JIT 네트워크 액세스 정책](https://docs.microsoft.com/rest/api/securitycenter/jitnetworkaccesspolicies)을 참조하세요.

## <a name="jit-vm-access-via-powershell"></a>PowerShell 통해 JIT VM 액세스

PowerShell을 통해 Just-In-Time VM 액세스 솔루션을 사용하려면 공식 Azure Security Center PowerShell cmdlet, 특히 `Set-AzJitNetworkAccessPolicy`를 사용하세요.

다음 예제에서는 특정 VM의 Just-In-Time VM 액세스 정책을 설정하고 다음 내용을 설정합니다.

1.  포트 22 및 3389를 닫습니다.

2.  승인된 요청에 대해 열 수 있도록 최대 기간을 3시간으로 설정합니다.
3.  액세스를 요청하는 사용자가 소스 IP 주소를 제어하고 승인된 Just-In-Time 액세스 요청 시 성공적인 세션을 설정할 수 있도록 허용합니다.

이 작업을 완수하려면 PowerShell에서 다음을 실행하세요.

1.  VM에 대한 Just-In-Time VM 액세스 정책을 보유하는 변수를 할당합니다.

        $JitPolicy = (@{
         id="/subscriptions/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Compute/virtualMachines/VMNAME"
        ports=(@{
             number=22;
             protocol="*";
             allowedSourceAddressPrefix=@("*");
             maxRequestAccessDuration="PT3H"},
             @{
             number=3389;
             protocol="*";
             allowedSourceAddressPrefix=@("*");
             maxRequestAccessDuration="PT3H"})})

2.  VM Just-In-Time VM 액세스 정책을 배열에 삽입합니다.
    
        $JitPolicyArr=@($JitPolicy)

3.  선택한 VM에서 Just-In-Time VM 액세스 정책을 구성합니다.
    
        Set-AzJitNetworkAccessPolicy -Kind "Basic" -Location "LOCATION" -Name "default" -ResourceGroupName "RESOURCEGROUP" -VirtualMachine $JitPolicyArr 

#### <a name="request-access-to-a-vm-via-powershell"></a>PowerShell 통해 VM에 대 한 액세스를 요청 합니다.

다음 예제에서는 포트 22를 특정 IP 주소 및 특정 시간 동안 열도록 요청된 특정 VM에 대한 Just-In-Time VM 액세스 요청을 확인할 수 있습니다.

PowerShell에서 다음 내용을 실행하세요.
1.  VM 요청 액세스 속성을 구성합니다.

        $JitPolicyVm1 = (@{
          id="/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Compute/virtualMachines/VMNAME"
        ports=(@{
           number=22;
           endTimeUtc="2018-09-17T17:00:00.3658798Z";
           allowedSourceAddressPrefix=@("IPV4ADDRESS")})})
2.  VM 액세스 요청 매개 변수를 배열에 삽입합니다.

        $JitPolicyArr=@($JitPolicyVm1)
3.  액세스 요청을 보냅니다(1단계에서 얻은 리소스 ID 사용).

        Start-AzJitNetworkAccessPolicy -ResourceId "/subscriptions/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Security/locations/LOCATION/jitNetworkAccessPolicies/default" -VirtualMachine $JitPolicyArr

자세한 내용은 PowerShell cmdlet 설명서를 참조하세요.

## <a name="next-steps"></a>다음 단계
이 문서에서는 Azure Security Center에서 Just-In-Time VM 액세스가 Azure Virtual Machines에 대한 액세스를 제어하는 데 어떻게 도움이 되는지 알아보았습니다.

보안 센터에 대한 자세한 내용은 다음을 참조하세요.

- [보안 정책 설정](tutorial-security-policy.md) - Azure 구독 및 리소스 그룹에 대해 보안 정책을 구성하는 방법을 알아봅니다.
- [보안 권장 사항 관리](security-center-recommendations.md) - 권장 사항이 Azure 리소스 보호에 어떤 도움이 되는지를 알아봅니다.
- [보안 상태 모니터링](security-center-monitoring.md) - Azure 리소스의 상태를 모니터링하는 방법을 알아봅니다.
- [보안 경고 관리 및 대응](security-center-managing-and-responding-alerts.md) - 보안 경고를 관리하고 대응하는 방법을 알아봅니다.
- [파트너 솔루션 모니터링](security-center-partner-solutions.md) - 파트너 솔루션의 상태를 모니터링하는 방법을 알아봅니다.
- [보안 센터 FAQ](security-center-faq.md) - 서비스 사용에 관한 질문과 대답을 찾습니다.
- [Azure 보안 블로그](https://blogs.msdn.microsoft.com/azuresecurity/) - Azure 보안 및 규정 준수에 관한 블로그 게시물을 찾습니다.

