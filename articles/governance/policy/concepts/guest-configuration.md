---
title: 가상 시스템의 내용을 감사하는 방법에 대해 알아보기
description: Azure Policy에서 게스트 구성 에이전트를 사용하여 가상 시스템 내부의 설정을 감사하는 방법에 대해 알아봅니다.
ms.date: 11/04/2019
ms.topic: conceptual
ms.openlocfilehash: cc2ba11f75da5f993b99c90e5d0cc1030003203e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80257259"
---
# <a name="understand-azure-policys-guest-configuration"></a>Azure Policy 게스트 구성 이해

Azure Policy는 Azure 리소스를 감사하고 [수정하는](../how-to/remediate-resources.md) 것 외에도 컴퓨터 내부의 설정을 감사할 수 있습니다. 게스트 구성 확장 및 클라이언트가 유효성 검사를 수행합니다. 클라이언트를 통한 확장은 다음과 같은 설정의 유효성을 검사합니다.

- 운영 체제의 구성
- 애플리케이션 구성 또는 현재 상태
- 환경 설정

현재 Azure Policy 게스트 구성은 머신 내의 설정만 감사합니다. 구성은 적용하지 않습니다.

## <a name="extension-and-client"></a>확장 및 클라이언트

컴퓨터 내부의 설정을 감사하려면 [가상 컴퓨터 확장이](../../../virtual-machines/extensions/overview.md) 활성화됩니다. 이 확장은 적용 가능한 정책 할당 및 해당 구성 정의를 다운로드합니다.

### <a name="limits-set-on-the-extension"></a>확장에 설정된 제한

컴퓨터 내에서 실행되는 응용 프로그램에 영향을 미치지 않도록 확장을 제한하기 위해 게스트 구성은 CPU 사용률의 5%를 초과할 수 없습니다. 이 제한은 기본 제공 및 사용자 지정 정의 모두에 대해 존재합니다.

## <a name="register-guest-configuration-resource-provider"></a>게스트 구성 리소스 공급자 등록

게스트 구성을 사용하려면 먼저 리소스 공급자를 등록해야 합니다. 포털 또는 PowerShell을 통해 등록할 수 있습니다. 게스트 구성 정책 할당이 포털을 통해 수행되면 리소스 공급자가 자동으로 등록됩니다.

### <a name="registration---portal"></a>등록 - 포털

Azure Portal을 통해 게스트 구성용 리소스 공급자를 등록하려면 다음 단계를 수행합니다.

1. Azure Portal을 시작하고 **모든 서비스**를 클릭합니다. **구독**을 검색하여 선택합니다.

1. 게스트 구성을 사용하도록 설정할 구독을 찾아서 클릭합니다.

1. **구독** 페이지의 왼쪽 메뉴에서 **리소스 공급자**를 클릭합니다.

1. **Microsoft.GuestConfiguration**이 표시될 때까지 필터링하거나 스크롤한 다음 같은 행에서 **등록**을 클릭합니다.

### <a name="registration---powershell"></a>등록 - PowerShell

PowerShell을 통해 게스트 구성용 리소스 공급자를 등록하려면 다음 명령을 실행합니다.

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell
Register-AzResourceProvider -ProviderNamespace 'Microsoft.GuestConfiguration'
```

## <a name="validation-tools"></a>유효성 검사 도구

시스템 내에서 게스트 구성 클라이언트는 로컬 도구를 사용하여 감사를 실행합니다.

다음 표에는 지원되는 각 운영 체제에서 사용되는 로컬 도구 목록이 나와 있습니다.

|운영 체제|유효성 검사 도구|메모|
|-|-|-|
|Windows|[윈도우 파워쉘 원하는 상태 구성](/powershell/scripting/dsc/overview/overview) v2| |
|Linux|[Chef InSpec](https://www.chef.io/inspec/)| 게스트 구성 확장을 통해 Ruby 및 Python이 설치됩니다. |

### <a name="validation-frequency"></a>유효성 검사 빈도

게스트 구성 클라이언트는 5분마다 새 콘텐츠를 확인합니다. 게스트 할당이 수신되면 15분 간격으로 설정을 확인합니다. 감사가 완료되는 즉시 게스트 구성 리소스 공급자로 결과가 전송됩니다. 정책 [평가 트리거](../how-to/get-compliance-data.md#evaluation-triggers)가 발생하면 컴퓨터 상태가 게스트 구성 리소스 공급자에 기록됩니다. 이 업데이트로 인해 Azure Policy에서 Azure 리소스 관리자 속성을 평가합니다. 온디맨드 Azure 정책 평가는 게스트 구성 리소스 공급자의 최신 값을 검색합니다. 그러나 컴퓨터 내의 구성에 대한 새 감사를 트리거하지는 않습니다.

## <a name="supported-client-types"></a>지원되는 클라이언트 유형

다음 표에는 Azure 이미지에서 지원되는 운영 체제 목록이 나와 있습니다.

|게시자|이름|버전|
|-|-|-|
|Canonical|Ubuntu Server|14.04, 16.04, 18.04|
|Credativ|Debian|8, 9|
|Microsoft|Windows Server|2012 데이터 센터, 2012 R2 데이터 센터, 2016 데이터 센터, 2019 데이터 센터|
|Microsoft|Windows 클라이언트|윈도우 10|
|OpenLogic|CentOS|7.3, 7.4, 7.5|
|Red Hat|Red Hat Enterprise Linux|7.4, 7.5, 7.6|
|Suse|SLES|12 SP3|

> [!IMPORTANT]
> 게스트 구성은 지원되는 OS를 실행하는 노드를 감사할 수 있습니다. 사용자 지정 이미지를 사용하는 가상 컴퓨터를 감사하려면 **DeployIfNotExists** 정의를 복제하고 이미지 속성을 포함하도록 **If** 섹션을 수정해야 합니다.

### <a name="unsupported-client-types"></a>지원되지 않는 클라이언트 유형

Windows 서버 나노 서버는 모든 버전에서 지원되지 않습니다.

## <a name="guest-configuration-extension-network-requirements"></a>게스트 구성 확장 네트워크 요구 사항

Azure에서 게스트 구성 리소스 공급자와 통신하기 위해 컴퓨터는 포트 **443의**Azure 데이터 센터에 대한 아웃바운드 액세스가 필요합니다. 아웃바운드 트래픽을 허용하지 않는 Azure에서 개인 가상 네트워크를 사용하는 경우 네트워크 [보안 그룹](../../../virtual-network/manage-network-security-group.md#create-a-security-rule) 규칙을 사용하여 예외를 구성합니다.
[서비스 태그](../../../virtual-network/service-tags-overview.md) "GuestAndHybridManagement"는 게스트 구성 서비스를 참조하는 데 사용할 수 있습니다.

## <a name="guest-configuration-definition-requirements"></a>게스트 구성 정의 요구 사항

게스트 구성에서 실행되는 각 감사에는 두 가지 정책 정의인 **DeployIfNotExists** 정의와 **AuditIfNotExists** 정의가 필요합니다. **DeployIfNotExists** 정의는 [유효성 검사 도구를](#validation-tools)지원하기 위해 게스트 구성 에이전트 및 기타 구성 요소로 컴퓨터를 준비하는 데 사용됩니다.

**DeployIfNotExists** 정책 정의는 다음 항목의 유효성을 검사하고 수정합니다.

- 컴퓨터가 평가할 구성이 할당되었는지 확인합니다. 현재 할당이 없는 경우 과제를 받고 다음을 통해 컴퓨터를 준비합니다.
  - [관리되는 ID를](../../../active-directory/managed-identities-azure-resources/overview.md) 사용하여 컴퓨터에 인증
  - **Microsoft.GuestConfiguration** 확장의 최신 버전 설치
  - [유효성 검사 도구](#validation-tools) 및 종속성(필요한 경우) 설치

**DeployIfNotExists** 할당이 비준수인 경우 [업데이트 관리 작업을](../how-to/remediate-resources.md#create-a-remediation-task) 사용할 수 있습니다.

**DeployIfNotExists** 할당이 규정을 준수하면 **AuditIfNotExists** 정책 할당은 로컬 유효성 검사 도구를 사용하여 구성 할당이 호환되는지 또는 비준수인지 확인합니다. 유효성 검사 도구는 게스트 구성 클라이언트에 결과를 제공합니다. 클라이언트는 게스트 확장에 결과를 전달하므로 게스트 구성 리소스 공급자를 통해 사용할 수 있습니다.

Azure Policy는 게스트 구성 리소스 공급자 **complianceStatus** 속성을 사용하여 **규정 준수** 노드에서 규정 준수를 보고합니다. 자세한 내용은 [규정 준수 데이터 가져오기](../how-to/get-compliance-data.md)를 참조하세요.

> [!NOTE]
> **[DeployIfNotExists]** **감사IfNotExists** 정책이 결과를 반환하는 데 필요합니다. **DeployIfNotExists가**없는 **경우 AuditIfNotExists** 정책에는 "0/0" 리소스가 상태로 표시됩니다.

할당에 사용할 정의를 그룹화할 수 있도록, 게스트 구성을 위한 모든 기본 제공 정책은 이니셔티브에 포함됩니다. _ \[미리 보기라는\]_ 기본 제공 이니셔티브 : Linux 및 Windows 컴퓨터 내부의 암호 보안 설정 감사에는 18개의 정책이 포함되어 있습니다. 그리고 Window용 **DeployIfNotExists** 및 **AuditIfNotExists** 쌍 6개와 Linux용 쌍 3개가 있습니다. [정책 정의](definition-structure.md#policy-rule) 논리는 대상 운영 체제만 평가되는지 확인합니다.

#### <a name="auditing-operating-system-settings-following-industry-baselines"></a>업계 기준에 따른 운영 체제 설정 감사

Azure Policy에서 사용할 수 있는 이니셔티브 중 하나는 Microsoft의 "기준"에 따라 가상 시스템 내에서 운영 체제 설정을 감사하는 기능을 제공합니다. 정의, _ \[미리\]보기 : Azure 보안 기준 설정과 일치하지 않는 Windows VM 감사에는_ Active Directory 그룹 정책의 설정에 따라 전체 감사 규칙 집합이 포함됩니다.

대부분의 설정은 매개 변수로 사용할 수 있습니다. 이 기능을 사용하면 감사된 내용을 사용자 지정하여 정책을 조직의 요구 사항에 맞게 조정하거나 정책을 업계 규제 표준과 같은 타사 정보에 매핑할 수 있습니다.

일부 매개 변수는 정수 값 범위를 지원합니다. 예를 들어 최대 암호 수지 매개 변수는 범위 연산자를 사용하여 장비 소유자에게 유연성을 제공하도록 설정할 수 있습니다. 사용자가 암호를 변경하도록 요구하는 효과적인 그룹 정책 설정은 70일을 넘지 않아야 하지만 하루도 안 된다는 것을 감사할 수 있습니다. 매개 변수에 대한 정보 버블에 설명된 대로 이 비즈니스 정책을 효과적인 감사 값으로 만들기 위해 값을 "1,70"으로 설정합니다.

Azure Resource Manager 배포 템플릿을 사용하여 정책을 할당하는 경우 매개 변수 파일을 사용하여 소스 제어에서 이러한 설정을 관리할 수 있습니다. Git과 같은 도구를 사용하여 각 체크 인 문서에서 주석이 있는 감사 정책의 변경 사항을 관리하여 할당이 예상 값에 대한 예외가 되어야 하는 이유를 알 수 있습니다.

#### <a name="applying-configurations-using-guest-configuration"></a>게스트 구성을 사용하여 구성 적용

Azure 정책의 최신 기능은 컴퓨터 내부의 설정을 구성합니다. 정의 _Windows 컴퓨터의 표준 시간대를 구성하면_ 표준 시간대를 구성하여 컴퓨터를 변경합니다.

_구성으로_시작하는 정의를 할당할 때 _Windows VM에서 게스트 구성 정책을 사용하도록 설정하려면 정의 배포 필수 구성 요소도_할당해야 합니다. 원하는 경우 이니셔티브에서 이러한 정의를 결합할 수 있습니다.

#### <a name="assigning-policies-to-machines-outside-of-azure"></a>Azure 외부의 컴퓨터에 정책 할당

게스트 구성에 사용할 수 있는 감사 정책에는 **Microsoft.HybridCompute/컴퓨터** 리소스 유형이 포함됩니다. 정책 할당 범위에 있는 [서버에 대해 Azure Arc에](../../../azure-arc/servers/overview.md) 온보온된 모든 컴퓨터가 자동으로 포함됩니다.

### <a name="multiple-assignments"></a>여러 할당

게스트 구성 정책은 현재 정책 할당이 다른 매개 변수를 사용하는 경우에도 시스템당 한 번만 동일한 게스트 할당을 할당하는 것을 지원합니다.

## <a name="built-in-resource-modules"></a>내장 리소스 모듈

게스트 구성 확장을 설치할 때 '게스트 구성' PowerShell 모듈이 최신 버전의 DSC 리소스 모듈에 포함되어 있습니다. 이 모듈은 모듈 페이지 [GuestConfiguration의](https://www.powershellgallery.com/packages/GuestConfiguration/)'수동 다운로드' 링크를 사용하여 PowerShell 갤러리에서 다운로드할 수 있습니다. '.nupkg' 파일 형식의 이름을 '.zip'으로 변경하여 압축해제 및 검토할 수 있습니다.

## <a name="client-log-files"></a>클라이언트 로그 파일

게스트 구성 확장프로그램은 로그 파일을 다음 위치에 씁니다.

Windows: `C:\ProgramData\GuestConfig\gc_agent_logs\gc_agent.log`

Linux: `/var/lib/GuestConfig/gc_agent_logs/gc_agent.log`

Where는 `<version>` 현재 버전 번호를 나타냅니다.

### <a name="collecting-logs-remotely"></a>원격으로 로그 수집

게스트 구성 구성 또는 모듈 문제 해결의 첫 번째 `Test-GuestConfigurationPackage` 단계는 [Windows에 대한 사용자 지정 게스트 구성 감사 정책을 만드는](../how-to/guest-configuration-create.md#step-by-step-creating-a-custom-guest-configuration-audit-policy-for-windows)방법 의 다음 단계를 따르는 cmdlet을 사용하는 것입니다.
성공적이지 않은 경우 클라이언트 로그를 수집하면 문제를 진단하는 데 도움이 될 수 있습니다.

#### <a name="windows"></a>Windows

Azure VM 실행 명령 기능을 사용하여 Windows 컴퓨터의 로그 파일에서 정보를 캡처하려면 다음 예제 PowerShell 스크립트가 유용할 수 있습니다. 자세한 내용은 [실행 명령을 통해 Windows VM의 PowerShell 스크립트 실행을](../../../virtual-machines/windows/run-command.md)참조하십시오.

```powershell
$linesToIncludeBeforeMatch = 0
$linesToIncludeAfterMatch = 10
$logPath = 'C:\ProgramData\GuestConfig\gc_agent_logs\gc_agent.log'
Select-String -Path $logPath -pattern 'DSCEngine','DSCManagedEngine' -CaseSensitive -Context $linesToIncludeBeforeMatch,$linesToIncludeAfterMatch | Select-Object -Last 10
```

#### <a name="linux"></a>Linux

Azure VM Run Command 기능을 사용하여 Linux 컴퓨터의 로그 파일에서 정보를 캡처하려면 다음 예제 Bash 스크립트가 유용할 수 있습니다. 자세한 내용은 [실행 명령이 있는 Linux VM의 셸 스크립트 실행을](../../../virtual-machines/linux/run-command.md) 참조하십시오.

```Bash
linesToIncludeBeforeMatch=0
linesToIncludeAfterMatch=10
logPath=/var/lib/GuestConfig/gc_agent_logs/gc_agent.log
egrep -B $linesToIncludeBeforeMatch -A $linesToIncludeAfterMatch 'DSCEngine|DSCManagedEngine' $logPath | tail
```

## <a name="guest-configuration-samples"></a>게스트 구성 샘플

정책 게스트 구성 기본 제공 이니셔티브의 소스는 다음 위치에서 사용할 수 있습니다.

- [기본 제공 정책 정의 - 게스트 구성](../samples/built-in-policies.md#guest-configuration)
- [기본 제공 이니셔티브 - 게스트 구성](../samples/built-in-initiatives.md#guest-configuration)
- [Azure 정책 샘플 GitHub 리포지토리](https://github.com/Azure/azure-policy/tree/master/built-in-policies/policySetDefinitions/Guest%20Configuration)

## <a name="next-steps"></a>다음 단계

- Azure 정책 [샘플의 예제를 검토합니다.](../samples/index.md)
- [Azure Policy 정의 구조](definition-structure.md)를 검토합니다.
- [정책 효과 이해 검토](effects.md).
- [프로그래밍 방식으로 정책을 만드는](../how-to/programmatically-create.md)방법을 이해합니다.
- [규정 준수 데이터를 얻는](../how-to/get-compliance-data.md)방법에 대해 알아봅니다.
- [비준수 리소스를 수정하는](../how-to/remediate-resources.md)방법에 대해 알아봅니다.
- 관리 그룹이 Azure 관리 그룹으로 리소스 구성을 통해 어떤 내용인지 [검토합니다.](../../management-groups/overview.md)
