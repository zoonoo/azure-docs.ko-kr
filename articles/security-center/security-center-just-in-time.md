---
title: Azure Security Center에서 Just-In-Time 가상 머신 액세스 | Microsoft Docs
description: 이 문서에서는 Azure 가상 컴퓨터에 대 한 액세스를 제어 하는 데 도움이 되 Azure Security Center JIT (just-in-time VM access)를 보여 줍니다.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 07/12/2020
ms.author: memildin
ms.openlocfilehash: 4a709527c0de2e092bcca2bbd9bc596aa0eb4cc0
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91440731"
---
# <a name="secure-your-management-ports-with-just-in-time-access"></a>Just-in-time 액세스를 사용 하 여 관리 포트 보호

Azure Security Center의 JIT (just-in-time) 가상 머신 (VM) 액세스 기능을 사용 하 여 Azure Virtual Machines에 대 한 인바운드 트래픽을 잠급니다. 이렇게 하면 VM에 연결 해야 할 때 쉽게 액세스할 수 있도록 하 여 공격에 대 한 노출을 줄일 수 있습니다.

JIT 작동 방식 및 기본 논리에 대 한 전체 설명은 [just-in-time 설명](just-in-time-explained.md)을 참조 하세요.

이 페이지에서는 보안 프로그램에 JIT를 포함 하는 방법을 설명 합니다. 이 문서에서 배울 내용은 다음과 같습니다. 

- **Vm에서 Jit 사용** -Security Center, PowerShell 또는 REST API를 사용 하 여 하나 이상의 vm에 대 한 사용자 지정 옵션으로 jit를 사용 하도록 설정할 수 있습니다. 또는 Azure virtual machines에서 하드 코딩 된 기본 매개 변수를 사용 하 여 JIT를 사용 하도록 설정할 수 있습니다. 사용 하도록 설정 되 면 JIT는 네트워크 보안 그룹에 규칙을 만들어 Azure Vm에 대 한 인바운드 트래픽을 잠급니다.
- **Jit를 사용 하는 VM에 대 한 액세스 요청** -jit의 목표는 인바운드 트래픽이 Security Center 잠겨 있는 경우에도 필요한 경우 vm에 대 한 연결에 쉽게 액세스할 수 있도록 하는 것입니다. Security Center, Azure virtual machines, PowerShell 또는 REST API에서 JIT 사용 VM에 대 한 액세스를 요청할 수 있습니다.
- **활동 감사** -vm의 보안을 적절 하 게 유지 하려면 일반 보안 검사의 일부로 JIT 지원 vm에 대 한 액세스를 검토 합니다.   



## <a name="availability"></a>가용성

|양상|세부 정보|
|----|:----|
|릴리스 상태:|GA(일반 공급)|
|결정|[서버용 Azure Defender](defender-for-servers-introduction.md) 가 필요 합니다.|
|지원 되는 Vm:|![](./media/icons/yes-icon.png)Azure Resource Manager를 통해 배포 되는 vm입니다.<br>![](./media/icons/no-icon.png)클래식 배포 모델을 사용 하 여 vm을 배포 하지 않았습니다. [이러한 배포 모델에 대해 자세히 알아보세요](../azure-resource-manager/management/deployment-models.md).<br>![Azure ](./media/icons/no-icon.png) [방화벽 관리자](https://docs.microsoft.com/azure/firewall-manager/overview) 에서 제어 하는 azure 방화벽으로 보호 되는 vm 없음|
|필요한 역할 및 사용 권한:|**판독기** 및 **securityreader** 역할은 둘 다 JIT 상태와 매개 변수를 볼 수 있습니다.<br>JIT에서 사용할 수 있는 사용자 지정 역할을 만들려면 [jit를 구성 하 고 사용 하는 데 필요한 권한은 무엇 인가요?](just-in-time-explained.md#what-permissions-are-needed-to-configure-and-use-jit)를 참조 하세요.<br>VM에 대 한 JIT 액세스를 요청 해야 하는 사용자에 게 최소 권한 역할을 만들고 다른 JIT 작업을 수행 하지 않으려면 Security Center GitHub 커뮤니티 페이지에서 [JitLeastPrivilegedRole 스크립트](https://github.com/Azure/Azure-Security-Center/tree/master/Powershell%20scripts/JIT%20Custom%20Role) 를 사용 합니다.|
|클라우드:|![예](./media/icons/yes-icon.png) 상용 클라우드<br>![예](./media/icons/yes-icon.png) 국가/소 버린 (US Gov, 중국 .Gov, 기타 .Gov)|
|||


## <a name="enable-jit-vm-access"></a>JIT VM 액세스 사용 <a name="jit-configure"></a>

Security Center를 사용 하거나 프로그래밍 방식으로 하나 이상의 Vm에 대 한 사용자 지정 옵션을 사용 하 여 JIT VM 액세스를 사용 하도록 설정할 수 있습니다. 

또는 Azure Virtual machines에서 하드 코딩 된 기본 매개 변수를 사용 하 여 JIT를 사용 하도록 설정할 수 있습니다.

이러한 각 옵션은 아래 별도의 탭에 설명 되어 있습니다.

### <a name="azure-security-center"></a>[**Azure Security Center**](#tab/jit-config-asc)

### <a name="enable-jit-on-your-vms-from-azure-security-center"></a>Azure Security Center에서 Vm에 대 한 JIT 사용 <a name="jit-asc"></a>

:::image type="content" source="./media/security-center-just-in-time/jit-config-security-center.gif" alt-text="Azure Security Center에서 JIT VM 액세스 구성":::

Security Center에서 JIT VM 액세스를 사용 하도록 설정 하 고 구성할 수 있습니다.

1. Azure Defender 대시보드를 열고 고급 보호 영역에서 **JUST-IN-TIME VM 액세스**를 선택 합니다.

    Vm이 다음 탭으로 그룹화 되 면 **JUST-IN-TIME vm 액세스** 페이지가 열립니다.

    - **구성** 됨-just-in-time vm 액세스를 지원 하도록 이미 구성 된 vm입니다. 각 VM에 대해 구성 됨 탭에는 다음이 표시 됩니다.
        - 지난 7 일간 승인 된 JIT 요청 수
        - 마지막으로 액세스 한 날짜와 시간입니다.
        - 구성 된 연결 정보
        - 마지막 사용자
    - **구성 되지 않음** -jit를 사용 하도록 설정 하지 않은 VM 이지만 jit를 지원할 수 있습니다. 이러한 Vm에 대해 JIT를 사용 하도록 설정 하는 것이 좋습니다.
    - **지원 되지** 않음-JIT를 사용 하지 않고 기능을 지원 하지 않는 vm입니다. 다음과 같은 이유로 VM이이 탭에 있을 수 있습니다.
      - 누락 된 NSG (네트워크 보안 그룹)-JIT를 구성 하려면 NSG가 필요 합니다.
      - 클래식 VM-JIT는 ' 클래식 배포 '가 아닌 Azure Resource Manager를 통해 배포 되는 Vm을 지원 합니다. [클래식 vs Azure Resource Manager 배포 모델에 대해 자세히 알아보세요](../azure-resource-manager/management/deployment-models.md).
      - 기타-JIT 솔루션을 구독 또는 리소스 그룹의 보안 정책에서 사용 하지 않도록 설정한 경우에는 VM이이 탭에 있을 수 있습니다.

1. **구성 되지 않음** 탭에서 VM을 jit로 보호 하도록 표시 하 고 **Vm에서 jit 사용**을 선택 합니다. 

    JIT VM 액세스 페이지가 열리고 Security Center 보호를 권장 하는 포트가 나열 됩니다.
    - 22 - SSH
    - 3389 - RDP
    - 5985 - WinRM 
    - 5986 - WinRM

    기본 설정을 적용 하려면 **저장**을 선택 합니다.

1. JIT 옵션을 사용자 지정 하려면:

    - **추가** 단추를 사용 하 여 사용자 지정 포트를 추가 합니다. 
    - 목록에서 선택 하 여 기본 포트 중 하나를 수정 합니다.

    포트 **추가 구성** 창에는 각 포트 (사용자 지정 및 기본값)에 대해 다음 옵션이 제공 됩니다.

    - **프로토콜**-요청이 승인 될 때이 포트에서 허용 되는 프로토콜입니다.
    - **허용 된 원본 ip**-요청이 승인 될 때이 포트에서 허용 되는 IP 범위
    - **최대 요청 시간**-특정 포트를 열 수 있는 최대 시간 창

     1. 포트 보안을 필요에 따라 설정 합니다.

     1. **확인**을 선택합니다.

1. **저장**을 선택합니다.



### <a name="edit-the-jit-configuration-on-a-jit-enabled-vm-using-security-center"></a>Security Center를 사용 하 여 JIT 사용 VM에서 JIT 구성 편집 <a name="jit-modify"></a>

Vm의 보호를 위해 새 포트를 추가 및 구성 하거나 이미 보호 된 포트와 관련 된 다른 설정을 변경 하 여 VM의 just-in-time 구성을 수정할 수 있습니다.

VM에 대 한 기존 JIT 규칙을 편집 하려면:

1. Azure Defender 대시보드를 열고 고급 보호 영역에서 **적응 응용 프로그램 제어**를 선택 합니다.

1. **구성 됨** 탭에서 포트를 추가 하려는 VM을 마우스 오른쪽 단추로 클릭 하 고 편집을 선택 합니다. 

    ![Azure Security Center에서 JIT VM 액세스 구성 편집](./media/security-center-just-in-time/jit-policy-edit-security-center.png)

1. **JIT VM 액세스 구성**에서 포트를 클릭하여 이미 보호된 포트의 기존 설정을 편집하거나 새로운 사용자 지정 포트를 추가할 수 있습니다.

1. 포트 편집을 완료 하면 **저장**을 선택 합니다.
 


### <a name="azure-virtual-machines"></a>[**Azure virtual machines**](#tab/jit-config-avm)

### <a name="enable-jit-on-your-vms-from-azure-virtual-machines"></a>Azure virtual machines에서 Vm에 대 한 JIT 사용

Azure Portal의 Azure virtual machines 페이지에서 VM에 대해 JIT를 사용 하도록 설정할 수 있습니다.

![Azure virtual machines에서 JIT VM 액세스 구성](./media/security-center-just-in-time/jit-config-virtual-machines.gif)

> [!TIP]
> VM에 이미 just-in-time이 활성화 되어 있는 경우 해당 구성 페이지로 이동 하면 just-in-time이 활성화 된 것을 확인할 수 있으며 링크를 사용 하 여 Security Center에서 just-in-time VM 액세스 페이지를 열고 설정을 확인 하 고 변경할 수 있습니다.

1. [Azure Portal](https://ms.portal.azure.com)에서 **가상 컴퓨터**를 검색 하 고 선택 합니다. 

1. JIT를 사용 하 여 보호 하려는 가상 머신을 선택 합니다.

1. 메뉴에서 **구성**을 선택 합니다.

1. Just-in-time **액세스**에서 **just-in-time 사용**을 선택 합니다. 

    이렇게 하면 다음 기본 설정을 사용 하 여 VM에 대 한 just-in-time 액세스를 사용할 수 있습니다.

    - Windows 컴퓨터:
        - RDP 포트 3389
        - 허용 되는 최대 액세스 3 시간
        - 허용 되는 원본 IP 주소는 Any로 설정 됩니다.
    - Linux 컴퓨터:
        - SSH 포트 22
        - 허용 되는 최대 액세스 3 시간
        - 허용 되는 원본 IP 주소는 Any로 설정 됩니다.

1. 이러한 값을 편집 하거나 JIT 구성에 포트를 더 추가 하려면 Azure Security Center의 just-in-time 페이지를 사용 합니다.

    1. Security Center의 메뉴에서 **JUST-IN-TIME VM 액세스**를 선택 합니다.

    1. **구성 됨** 탭에서 포트를 추가 하려는 VM을 마우스 오른쪽 단추로 클릭 하 고 편집을 선택 합니다. 

        ![Azure Security Center에서 JIT VM 액세스 구성 편집](./media/security-center-just-in-time/jit-policy-edit-security-center.png)

    1. **JIT VM 액세스 구성**에서 포트를 클릭하여 이미 보호된 포트의 기존 설정을 편집하거나 새로운 사용자 지정 포트를 추가할 수 있습니다.

    1. 포트 편집을 완료 하면 **저장**을 선택 합니다.


### <a name="powershell"></a>[**PowerShell**](#tab/jit-config-powershell)

### <a name="enable-jit-on-your-vms-using-powershell"></a>PowerShell을 사용 하 여 Vm에서 JIT 사용

PowerShell에서 just-in-time VM 액세스를 사용 하도록 설정 하려면 공식 Azure Security Center PowerShell cmdlet을 사용 합니다 `Set-AzJitNetworkAccessPolicy` .

**예** -다음 규칙을 사용 하 여 특정 vm에서 just-in-time vm 액세스를 사용 하도록 설정 합니다.

* 22 및 3389 포트를 닫습니다.
* 승인 된 요청당 열 수 있도록 최대 3 시간 기간을 설정 합니다.
* 액세스를 요청 하는 사용자가 원본 IP 주소를 제어할 수 있도록 허용
* 승인 된 just-in-time 액세스 요청에 대 한 액세스를 요청 하는 사용자가 성공한 세션을 설정 하도록 허용

다음 PowerShell 명령은이 JIT 구성을 만듭니다.

1. VM에 대 한 just-in-time VM 액세스 규칙을 보유 하는 변수를 할당 합니다.

    ```azurepowershell
    $JitPolicy = (@{
        id="/subscriptions/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Compute/virtualMachines/VMNAME";
        ports=(@{
             number=22;
             protocol="\*";
             allowedSourceAddressPrefix=@("\*");
             maxRequestAccessDuration="PT3H"},
             @{
             number=3389;
             protocol="\*";
             allowedSourceAddressPrefix=@("\*");
             maxRequestAccessDuration="PT3H"})})
    ```

1. VM just-in-time VM 액세스 규칙을 배열에 삽입 합니다.
    
    ```azurepowershell
    $JitPolicyArr=@($JitPolicy)
    ```

1. 선택한 VM에서 just-in-time VM 액세스 규칙을 구성 합니다.
    
    ```azurepowershell
    Set-AzJitNetworkAccessPolicy -Kind "Basic" -Location "LOCATION" -Name "default" -ResourceGroupName "RESOURCEGROUP" -VirtualMachine $JitPolicyArr
    ```

    -Name 매개 변수를 사용 하 여 VM을 지정 합니다. 예를 들어, VM1 및 V M 2의 서로 다른 두 Vm에 대 한 JIT 구성을 설정 하려면 ```Set-AzJitNetworkAccessPolicy -Name VM1``` 및을 사용 ```Set-AzJitNetworkAccessPolicy -Name VM2``` 합니다.


### <a name="rest-api"></a>[**REST API**](#tab/jit-config-api)

### <a name="enable-jit-on-your-vms-using-the-rest-api"></a>REST API를 사용 하 여 Vm에서 JIT 사용

Just-In-Time VM 액세스 기능은 Azure Security Center API를 통해 사용할 수 있습니다. 이 API를 사용 하 여 구성 된 Vm에 대 한 정보를 가져오고, 새 vm을 추가 하 고, VM에 대 한 액세스를 요청 합니다. 

[JIT 네트워크 액세스 정책](https://docs.microsoft.com/rest/api/securitycenter/jitnetworkaccesspolicies)에 대해 자세히 알아보세요.


--- 










## <a name="request-access-to-a-jit-enabled-vm"></a>JIT 사용 VM에 대 한 액세스 요청

Azure Portal (Security Center 또는 Azure Virtual machines)에서 또는 프로그래밍 방식으로 JIT 사용 VM에 대 한 액세스를 요청할 수 있습니다.

이러한 각 옵션은 아래 별도의 탭에 설명 되어 있습니다.

### <a name="azure-security-center"></a>[**Azure Security Center**](#tab/jit-request-asc)

### <a name="request-access-to-a-jit-enabled-vm-from-azure-security-center"></a>Azure Security Center에서 JIT 사용 VM에 대 한 액세스 요청 

VM에 JIT를 사용 하는 경우 연결에 대 한 액세스를 요청 해야 합니다. JIT를 사용 하도록 설정 하는 방법에 관계 없이 지원 되는 방법 중 하나를 사용 하 여 액세스를 요청할 수 있습니다.

:::image type="content" source="./media/security-center-just-in-time/jit-request-security-center.gif" alt-text="Azure Security Center에서 JIT VM 액세스 구성":::

1. **JUST-IN-TIME VM 액세스** 페이지에서 **구성 됨** 탭을 선택 합니다.

1. 액세스 하려는 Vm을 표시 합니다.

    - **연결 정보** 열의 아이콘은 네트워크 보안 그룹 또는 방화벽에서 JIT가 사용 되는지 여부를 나타냅니다. 둘 다에서 사용 하도록 설정 된 경우 방화벽 아이콘만 표시 됩니다.

    - **연결 정보** 열은 VM 및 열려 있는 포트를 연결 하는 데 필요한 정보를 제공 합니다.

1. **액세스 요청**을 선택합니다. **액세스 요청** 창이 열립니다.

1. **액세스 요청** 아래에서 각 VM에 대해 열려는 포트, 포트가 열려 있는 원본 IP 주소 및 포트를 열어 둘 기간을 구성합니다. 구성 된 포트에 대 한 액세스를 요청할 수 있습니다. 각 포트에는 사용자가 만든 JIT 구성에서 파생 된 최대 허용 시간이 있습니다.

1. **포트 열기**를 선택합니다.

> [!NOTE]
> 액세스를 요청하는 사용자가 프록시 뒤에 있으면 **내 IP** 옵션이 작동하지 않습니다. 조직의 전체 IP 주소 범위를 정의해야 할 수 있습니다.



### <a name="azure-virtual-machines"></a>[**Azure virtual machines**](#tab/jit-request-avm)

### <a name="request-access-to-a-jit-enabled-vm-from-the-azure-virtual-machines-connect-page"></a>Azure 가상 머신의 연결 페이지에서 JIT 사용 VM에 대 한 액세스 요청

VM에 JIT를 사용 하는 경우 연결에 대 한 액세스를 요청 해야 합니다. JIT를 사용 하도록 설정 하는 방법에 관계 없이 지원 되는 방법 중 하나를 사용 하 여 액세스를 요청할 수 있습니다.

  >![jit just-in-time 요청](./media/security-center-just-in-time/jit-request-vm.png)


Azure virtual machines에서 액세스를 요청 하려면:

1. Azure Portal에서 가상 컴퓨터 페이지를 엽니다.

1. 연결 하려는 VM을 선택 하 고 **연결** 페이지를 엽니다.

    Azure는 해당 VM에서 JIT가 사용 하도록 설정 되어 있는지 확인 합니다.

    - VM에 대해 JIT를 사용 하도록 설정 하지 않은 경우에는 사용 하도록 설정 하 라는 메시지가 표시 됩니다.

    - JIT를 사용 하도록 설정한 경우 **액세스 요청** 을 선택 하 여 요청 하는 IP, 시간 범위 및 해당 VM에 대해 구성 된 포트를 사용 하 여 액세스 요청을 전달 합니다.

> [!NOTE]
> Azure 방화벽에 의해 보호 되는 VM에 대 한 요청이 승인 되 면 Security Center는 사용자에 게 VM에 연결 하는 데 사용할 적절 한 연결 정보 (예를 들어 DNAT 테이블의 포트 매핑)를 제공 합니다.



### <a name="powershell"></a>[**PowerShell**](#tab/jit-request-powershell)

### <a name="request-access-to-a-jit-enabled-vm-using-powershell"></a>PowerShell을 사용 하 여 JIT 사용 VM에 대 한 액세스 요청

다음 예제에서는 포트 22를 특정 IP 주소 및 특정 시간 동안 열도록 요청된 특정 VM에 대한 Just-In-Time VM 액세스 요청을 확인할 수 있습니다.

PowerShell에서 다음 내용을 실행하세요.

1. VM 요청 액세스 속성을 구성 합니다.

    ```azurepowershell
    $JitPolicyVm1 = (@{
        id="/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Compute/virtualMachines/VMNAME";
        ports=(@{
           number=22;
           endTimeUtc="2020-07-15T17:00:00.3658798Z";
           allowedSourceAddressPrefix=@("IPV4ADDRESS")})})
    ```

1. VM 액세스 요청 매개 변수를 배열에 삽입합니다.

    ```azurepowershell
    $JitPolicyArr=@($JitPolicyVm1)
    ```
        
1. 요청 액세스 보내기 (1 단계의 리소스 ID 사용)

    ```azurepowershell
    Start-AzJitNetworkAccessPolicy -ResourceId "/subscriptions/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Security/locations/LOCATION/jitNetworkAccessPolicies/default" -VirtualMachine $JitPolicyArr
    ```

[PowerShell cmdlet 설명서](https://docs.microsoft.com/powershell/scripting/developer/cmdlet/cmdlet-overview)에서 자세히 알아보세요.



### <a name="rest-api"></a>[**REST API**](#tab/jit-request-api)

### <a name="request-access-to-a-jit-enabled-vms-using-the-rest-api"></a>REST API를 사용 하 여 JIT 지원 Vm에 대 한 액세스 요청

Just-In-Time VM 액세스 기능은 Azure Security Center API를 통해 사용할 수 있습니다. 이 API를 사용 하 여 구성 된 Vm에 대 한 정보를 가져오고, 새 vm을 추가 하 고, VM에 대 한 액세스를 요청 합니다. 

[JIT 네트워크 액세스 정책](https://docs.microsoft.com/rest/api/securitycenter/jitnetworkaccesspolicies)에 대해 자세히 알아보세요.

---








## <a name="audit-jit-access-activity-in-security-center"></a>Security Center에서 JIT 액세스 작업 감사

로그 검색을 사용하여 VM 활동에 대한 정보를 얻을 수 있습니다. 로그를 보려면:

1. **JUST-IN-TIME VM 액세스**에서 **구성 됨** 탭을 선택 합니다.

1. 감사 하려는 VM의 경우 행의 끝에 있는 줄임표 메뉴를 엽니다.
 
1. 메뉴에서 **활동 로그** 를 선택 합니다.

   ![Just-in-time JIT 활동 로그 선택](./media/security-center-just-in-time/jit-select-activity-log.png)

   활동 로그는 시간, 날짜 및 구독과 함께 해당 VM에 대 한 이전 작업의 필터링 된 보기를 제공 합니다.

1. 로그 정보를 다운로드 하려면 **CSV로 다운로드**를 선택 합니다.








## <a name="next-steps"></a>다음 단계

이 문서에서는 just-in-time VM 액세스를 설정 하 고 사용 하는 방법을 알아보았습니다. JIT를 사용 해야 하는 이유를 알아보려면 방어 중인 위협에 대해 설명 하는 개념 문서를 참조 하세요.

> [!div class="nextstepaction"]
> [JIT 설명](just-in-time-explained.md)