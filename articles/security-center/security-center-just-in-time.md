---
title: Azure Security Center에서 Just-In-Time 가상 머신 액세스 | Microsoft Docs
description: 이 문서에서는 Azure Security Center에서 Just-In-Time VM 액세스가 Azure 가상 머신에 대한 액세스를 제어하는 데 어떻게 도움이 되는지 보여 줍니다.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: memildin
ms.openlocfilehash: cc4e267c6912b8938db1ba5497a27f9c0026bd79
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80887336"
---
# <a name="secure-your-management-ports-with-just-in-time-access"></a>Just-in-time 액세스를 사용 하 여 관리 포트 보호

Security Center의 표준 가격 책정 계층 ( [가격 책정](/azure/security-center/security-center-pricing)참조)을 사용 하는 경우 JIT (JUST-IN-TIME) vm (가상 머신) 액세스를 사용 하 여 Azure vm에 대 한 인바운드 트래픽을 잠글 수 있습니다. 따라서 필요한 경우 Vm에 쉽게 연결할 수 있도록 하는 동시에 공격에 대 한 노출을 줄일 수 있습니다.

> [!NOTE]
> Security Center Just-In-Time VM 액세스는 현재 Azure Resource Manager를 통해 배포된 VM만 지원합니다. 클래식 및 Resource Manager 배포 모델에 대한 자세한 내용은 [Azure Resource Manager 및 클래식 배포](../azure-resource-manager/management/deployment-models.md)를 참조하세요.

[!INCLUDE [security-center-jit-description](../../includes/security-center-jit-description.md)]

## <a name="configure-jit-on-a-vm"></a>VM에서 JIT 구성

VM에서 JIT 정책을 구성 하는 방법에는 다음 세 가지가 있습니다.

- [Azure Security Center에서 JIT 액세스 구성](#jit-asc)
- [Azure VM 페이지에서 JIT 액세스 구성](#jit-vm)
- [프로그래밍 방식으로 VM에서 JIT 정책 구성](#jit-program)

## <a name="configure-jit-in-azure-security-center"></a>Azure Security Center에서 JIT 구성

Security Center에서 jit 정책을 구성 하 고 JIT 정책을 사용 하 여 VM에 대 한 액세스를 요청할 수 있습니다.

### <a name="configure-jit-access-on-a-vm-in-security-center"></a>Security Center에서 VM에 대 한 JIT 액세스 구성<a name="jit-asc"></a>

1. **Security Center** 대시보드를 엽니다.

1. 왼쪽 창에서 **Just-In-Time VM 액세스**를 선택합니다.

    ![Just-In-Time VM 액세스 타일](./media/security-center-just-in-time/just-in-time.png)

    **JUST-IN-TIME vm 액세스** 창이 열리고 vm 상태에 대 한 정보가 표시 됩니다.

    - **구성됨** - Just-In-Time VM 액세스를 지원하도록 구성된 VM입니다. 표시된 데이터는 지난 주에 대한 데이터이며 각 VM에 대해 승인된 요청 수, 마지막 액세스 날짜 및 시간, 마지막 사용자를 포함합니다.
    - **권장** -just-in-time vm 액세스를 지원할 수 있지만로 구성 되지 않은 vm입니다. 이러한 VM에는 Just-In-Time VM 액세스 제어를 사용하도록 설정하는 것이 좋습니다.
    - **권장 사항 없음** - VM이 권장되지 않을 수 있는 이유입니다.
      - 누락된 NSG - Just-In-Time 솔루션을 사용하려면 NSG가 있어야 합니다.
      - 클래식 VM - Security Center Just-In-Time VM 액세스는 현재 Azure Resource Manager를 통해 배포된 VM만 지원합니다. 클래식 배포는 Just-In-Time 솔루션에서 지원되지 않습니다. 
      - 기타-just-in-time 솔루션이 구독 또는 리소스 그룹의 보안 정책에서 해제 된 경우 또는 VM에 공용 IP가 없고 NSG가 없는 경우 VM은이 범주에 있습니다.

1. **권장** 탭을 선택합니다.

1. **가상 컴퓨터**에서 사용 하도록 설정할 vm을 클릭 합니다. 그러면 VM 옆에 있는 확인 표시가 선택됩니다.

      ![Just-In-Time VM 액세스 사용](./media/security-center-just-in-time/enable-just-in-time.png)

1. **Vm에서 JIT 사용**을 클릭 합니다. Azure Security Center에서 권장 하는 기본 포트를 표시 하는 창이 열립니다.
    - 22 - SSH
    - 3389 - RDP
    - 5985 - WinRM 
    - 5986 - WinRM
1. 필요에 따라 목록에 사용자 지정 포트를 추가할 수 있습니다.

      1. **추가**를 클릭합니다. **포트 구성 추가** 창이 열립니다.
      1. 구성 하도록 선택한 각 포트 (기본 및 사용자 지정)에 대해 다음 설정을 사용자 지정할 수 있습니다.
            - **프로토콜 유형** - 요청이 승인될 때 이 포트에서 허용되는 프로토콜입니다.
            - **허용된 원본 IP 주소** - 요청이 승인될 때 이 포트에서 허용되는 IP 범위입니다.
            - **최대 요청 시간** - 특정 포트를 열 수 있는 최대 기간입니다.

     1. **확인**을 클릭합니다.

1. **저장**을 클릭합니다.

> [!NOTE]
>VM에 대 한 JIT VM 액세스를 사용 하도록 설정 하면 연결 된 네트워크 보안 그룹과 연결 된 Azure 방화벽에서 선택한 포트에 대 한 "모든 인바운드 트래픽 거부" 규칙을 Azure Security Center 만들어집니다. 선택한 포트에 대해 다른 규칙을 만든 경우 기존 규칙은 새 "모든 인바운드 트래픽 거부" 규칙 보다 우선적으로 적용 됩니다. 선택한 포트에 기존 규칙이 없는 경우 새 "모든 인바운드 트래픽 거부" 규칙은 네트워크 보안 그룹 및 Azure 방화벽에서 가장 높은 우선 순위를 사용 합니다.


## <a name="request-jit-access-via-security-center"></a>Security Center를 통해 JIT 액세스 요청

Security Center를 통해 VM에 대 한 액세스를 요청 하려면:

1. **Just-In-Time VM 액세스**에서 **구성됨** 탭을 선택합니다.

1. **가상 컴퓨터**에서 액세스를 요청 하려는 vm을 클릭 합니다. 그러면 VM 옆에 확인 표시가 나타납니다.

    - **연결 정보** 열의 아이콘은 nsg 또는 FW에서 JIT를 사용 하도록 설정 했는지 여부를 나타냅니다. 둘 다에서 사용 하도록 설정 된 경우 방화벽 아이콘만 표시 됩니다.

    - **연결 정보** 열은 VM 및 열려 있는 포트를 연결 하는 데 필요한 정보를 제공 합니다.

      ![Just-In-Time 액세스 요청](./media/security-center-just-in-time/request-just-in-time-access.png)

1. **액세스 요청**을 클릭 합니다. **액세스 요청** 창이 열립니다.

      ![JIT 정보](./media/security-center-just-in-time/just-in-time-details.png)

1. **액세스 요청** 아래에서 각 VM에 대해 열려는 포트, 포트가 열려 있는 원본 IP 주소 및 포트를 열어 둘 기간을 구성합니다. Just-in-time 정책에 구성 된 포트에 대 한 액세스를 요청할 수 있습니다. 각 포트에는 Just-In-Time 정책에서 파생된 최대 허용된 시간이 있습니다.

1. **포트 열기**를 클릭 합니다.

> [!NOTE]
> 액세스를 요청하는 사용자가 프록시 뒤에 있으면 **내 IP** 옵션이 작동하지 않습니다. 조직의 전체 IP 주소 범위를 정의해야 할 수 있습니다.



## <a name="edit-a-jit-access-policy-via-security-center"></a>Security Center를 통해 JIT 액세스 정책 편집

해당 VM에 대해 보호할 새 포트를 추가 및 구성하거나 이미 보호된 포트와 관련된 다른 설정을 변경하여 VM의 기존 Just-In-Time 정책을 변경할 수 있습니다.

VM의 기존 Just-In-Time 정책을 편집하려면

1. **구성됨** 탭의 **VM** 아래에서 해당 VM에 대한 행 안의 3개 점을 클릭하여 포트를 추가할 VM을 선택합니다. 

1. **편집**을 선택합니다.

1. **JIT VM 액세스 구성**에서 포트를 클릭하여 이미 보호된 포트의 기존 설정을 편집하거나 새로운 사용자 지정 포트를 추가할 수 있습니다. 
  ![jit vm 액세스](./media/security-center-just-in-time/edit-policy.png)



## <a name="audit-jit-access-activity-in-security-center"></a>Security Center에서 JIT 액세스 작업 감사

로그 검색을 사용하여 VM 활동에 대한 정보를 얻을 수 있습니다. 로그를 보려면

1. **Just-In-Time VM 액세스**에서 **구성됨** 탭을 선택합니다.
2. Vm **에서 해당**vm의 행에 있는 세 개의 점을 클릭 하 고 메뉴에서 **활동 로그** 를 선택 하 여 정보를 볼 vm을 선택 합니다. **활동 로그가** 열립니다.

   ![활동 로그 선택](./media/security-center-just-in-time/select-activity-log.png)

   **활동 로그**는 시간, 날짜 및 구독과 함께 해당 VM에 대한 이전 작업의 필터링된 보기를 제공합니다.

**여기를 클릭하여 모든 항목을 CSV로 다운로드하세요.** 를 선택하여 로그 정보를 다운로드할 수 있습니다.

필터를 수정 하 고 **적용** 을 클릭 하 여 검색 및 로그를 만듭니다.



## <a name="configure-jit-access-from-an-azure-vms-page"></a>Azure VM의 페이지에서 JIT 액세스 구성<a name="jit-vm"></a>

편의상 Security Center의 VM 페이지 내에서 직접 JIT를 사용 하 여 VM에 연결할 수 있습니다.

### <a name="configure-jit-access-on-a-vm-via-the-azure-vm-page"></a>Azure VM 페이지를 통해 VM에 대 한 JIT 액세스 구성

VM에서 Just-In-Time 액세스를 쉽게 롤아웃할 수 있도록 하려면 VM 내에서 직접 Just-In-Time 액세스만 허용하도록 VM을 설정하면 됩니다.

1. [Azure Portal](https://ms.portal.azure.com)에서 **가상 컴퓨터**를 검색 하 고 선택 합니다. 
2. Just-in-time 액세스로 제한 하려는 가상 머신을 선택 합니다.
3. 메뉴에서 **구성**을 선택 합니다.
4. Just-in-time **액세스**에서 **just-in-time 사용**을 선택 합니다. 

이렇게 하면 다음 설정을 사용한 VM Just-In-Time 액세스를 설정합니다.

- Windows 서버:
    - RDP 포트 3389
    - 허용 되는 최대 액세스 3 시간
    - 허용 되는 원본 IP 주소는 Any로 설정 됩니다.
- Linux 서버:
    - SSH 포트 22
    - 허용 되는 최대 액세스 3 시간
    - 허용 되는 원본 IP 주소는 Any로 설정 됩니다.
     
VM에 이미 Just-In-Time이 사용 설정된 경우 구성 페이지로 이동하면 Just-In-Time이 사용하도록 설정되었으며 링크를 사용해 Azure Security Center에서 정책을 열어 설정을 보고 변경할 수 있음을 확인할 수 있습니다.

![vm의 jit 구성](./media/security-center-just-in-time/jit-vm-config.png)

### <a name="request-jit-access-to-a-vm-via-an-azure-vms-page"></a>Azure VM의 페이지를 통해 VM에 대 한 JIT 액세스 요청

Azure Portal에서 VM에 연결하려고 할 때 Azure는 해당 VM에 구성된 Just-In-Time 액세스 정책이 있는지 확인합니다.

- VM에 JIT 정책이 구성 되어 있는 경우 **액세스 요청** 을 클릭 하 여 vm에 설정 된 jit 정책에 따라 액세스 권한을 부여할 수 있습니다. 

  >![jit 요청](./media/security-center-just-in-time/jit-request.png)

  다음 기본 매개 변수를 사용 하 여 액세스를 요청 합니다.

  - **원본 IP**: ' Any ' (*) (변경할 수 없음)
  - **시간 범위**: 3 시간 (변경할 수 없음) <!--Isn't this set in the policy-->
  - **포트 번호** Windows/포트 22 (Linux의 경우)에 대 한 RDP 포트 3389 (변경 가능)

    > [!NOTE]
    > Azure 방화벽에 의해 보호 되는 VM에 대 한 요청이 승인 되 면 Security Center는 사용자에 게 VM에 연결 하는 데 사용할 적절 한 연결 정보 (예를 들어 DNAT 테이블의 포트 매핑)를 제공 합니다.

- VM에 JIT를 구성 하지 않은 경우 jit 정책을 구성 하 라는 메시지가 표시 됩니다.

  ![jit 프롬프트](./media/security-center-just-in-time/jit-prompt.png)

## <a name="configure-a-jit-policy-on-a-vm-programmatically"></a>프로그래밍 방식으로 VM에서 JIT 정책 구성<a name="jit-program"></a>

REST API 및 PowerShell을 통해 Just-In-Time을 설정하고 사용할 수 있습니다.

### <a name="jit-vm-access-via-rest-apis"></a>REST Api를 통한 JIT VM 액세스

Just-In-Time VM 액세스 기능은 Azure Security Center API를 통해 사용할 수 있습니다. 이 API를 통해 구성된 VM에 대한 정보를 가져오고 새로 추가하고 VM에 대한 액세스를 요청하는 등을 할 수 있습니다. Just-In-Time REST API에 대한 자세한 내용은 [JIT 네트워크 액세스 정책](https://docs.microsoft.com/rest/api/securitycenter/jitnetworkaccesspolicies)을 참조하세요.

### <a name="jit-vm-access-via-powershell"></a>PowerShell을 통한 JIT VM 액세스

PowerShell을 통해 Just-In-Time VM 액세스 솔루션을 사용하려면 공식 Azure Security Center PowerShell cmdlet, 특히 `Set-AzJitNetworkAccessPolicy`를 사용하세요.

다음 예제에서는 특정 VM의 Just-In-Time VM 액세스 정책을 설정하고 다음 내용을 설정합니다.

1.    포트 22 및 3389를 닫습니다.

2.    승인된 요청에 대해 열 수 있도록 최대 기간을 3시간으로 설정합니다.
3.    액세스를 요청하는 사용자가 소스 IP 주소를 제어하고 승인된 Just-In-Time 액세스 요청 시 성공적인 세션을 설정할 수 있도록 허용합니다.

이 작업을 완수하려면 PowerShell에서 다음을 실행하세요.

1.    VM에 대한 Just-In-Time VM 액세스 정책을 보유하는 변수를 할당합니다.

        $JitPolicy = (@ {id = "/subscriptions/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Compute/virtualMachines/VMNAME" ports = (@ {number = 22;        protocol = "\*";        allowedSourceAddressPrefix = @ ("\*");        maxRequestAccessDuration = "PT3H"}, @ {number = 3389;        protocol = "\*";        allowedSourceAddressPrefix = @ ("\*");        maxRequestAccessDuration = "PT3H"})})

2.    VM Just-In-Time VM 액세스 정책을 배열에 삽입합니다.
    
        $JitPolicyArr = @ ($JitPolicy)

3.    선택한 VM에서 Just-In-Time VM 액세스 정책을 구성합니다.
    
        AzJitNetworkAccessPolicy-Kind "Basic"-Location "LOCATION"-Name "default"-ResourceGroupName "RESOURCEGROUP"-VirtualMachine $JitPolicyArr 

### <a name="request-access-to-a-vm-via-powershell"></a>PowerShell을 통해 VM에 대 한 액세스 요청

다음 예제에서는 포트 22를 특정 IP 주소 및 특정 시간 동안 열도록 요청된 특정 VM에 대한 Just-In-Time VM 액세스 요청을 확인할 수 있습니다.

PowerShell에서 다음 내용을 실행하세요.
1.    VM 요청 액세스 속성을 구성합니다.

        $JitPolicyVm 1 = (@ {id = "/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Compute/virtualMachines/VMNAME" ports = (@ {number = 22;      endTimeUtc = "2018-09-17T17:00:00.3658798 Z";      allowedSourceAddressPrefix = @ ("IPV4ADDRESS")})})
2.    VM 액세스 요청 매개 변수를 배열에 삽입합니다.

        $JitPolicyArr = @ ($JitPolicyVm 1)
3.    액세스 요청을 보냅니다(1단계에서 얻은 리소스 ID 사용).

        AzJitNetworkAccessPolicy-ResourceId "/subscriptions/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Security/locations/LOCATION/jitNetworkAccessPolicies/default"-VirtualMachine $JitPolicyArr

자세한 내용은 [PowerShell cmdlet 설명서](https://docs.microsoft.com/powershell/scripting/developer/cmdlet/cmdlet-overview)를 참조 하세요.


## <a name="automatic-cleanup-of-redundant-jit-rules"></a>중복 JIT 규칙 자동 정리 

JIT 정책을 업데이트할 때마다 전체 규칙 집합의 유효성을 검사 하기 위해 정리 도구가 자동으로 실행 됩니다. 이 도구는 정책의 규칙과 NSG의 규칙 간 불일치를 검색 합니다. 정리 도구에서 불일치를 발견 하면 원인을 확인 하 고이에 대 한 안전한 경우 더 이상 필요 하지 않은 기본 제공 규칙을 제거 합니다. 클리너는 만든 규칙을 삭제 하지 않습니다.

클리너를 통해 기본 제공 규칙을 제거할 수 있는 예제 시나리오는 다음과 같습니다.

- 정의가 동일한 두 개의 규칙이 있고 하나에 다른 규칙이 우선 순위가 높은 경우 (즉, 우선 순위가 낮은 규칙은 사용 되지 않습니다.)
- 규칙 설명에 규칙의 대상 IP와 일치 하지 않는 VM의 이름이 포함 된 경우 


## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure Security Center에서 Just-In-Time VM 액세스가 Azure Virtual Machines에 대한 액세스를 제어하는 데 어떻게 도움이 되는지 알아보았습니다.

보안 센터에 대한 자세한 내용은 다음을 참조하세요.

- Microsoft Learn 모듈은 [Azure Security Center를 사용 하 여 무차별 암호 대입 및 맬웨어 공격 으로부터 서버와 vm을 보호](https://docs.microsoft.com/learn/modules/secure-vms-with-azure-security-center/) 합니다.
- [보안 정책 설정](tutorial-security-policy.md) -Azure 구독 및 리소스 그룹에 대 한 보안 정책을 구성 하는 방법을 알아봅니다.
- [보안 권장 사항 관리](security-center-recommendations.md) -권장 사항이 Azure 리소스를 보호 하는 데 도움이 되는 방법을 알아봅니다.
- [보안 상태 모니터링](security-center-monitoring.md) - Azure 리소스의 상태를 모니터링하는 방법을 알아봅니다.
