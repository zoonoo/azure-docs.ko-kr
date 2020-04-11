---
title: 가상 시스템의 내용을 감사하는 방법에 대해 알아보기
description: Azure Policy에서 게스트 구성 에이전트를 사용하여 가상 시스템 내부의 설정을 감사하는 방법에 대해 알아봅니다.
ms.date: 11/04/2019
ms.topic: conceptual
ms.openlocfilehash: 4a2989badc099a199bf21f7e020ca8e6256ddaf0
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/10/2020
ms.locfileid: "81113427"
---
# <a name="understand-azure-policys-guest-configuration"></a>Azure Policy 게스트 구성 이해

Azure Policy는 Azure 리소스를 감사하고 [수정하는](../how-to/remediate-resources.md) 것 외에도 컴퓨터 내부의 설정을 감사할 수 있습니다. 게스트 구성 확장 및 클라이언트가 유효성 검사를 수행합니다. 클라이언트를 통한 확장은 다음과 같은 설정의 유효성을 검사합니다.

- 운영 체제의 구성
- 애플리케이션 구성 또는 현재 상태
- 환경 설정

현재 대부분의 Azure 정책 게스트 구성 정책은 컴퓨터 내부의 설정만 감사합니다. 구성은 적용되지 않습니다. 예외는 [아래에 참조된](#applying-configurations-using-guest-configuration)기본 제공 정책 중 하나입니다.

## <a name="extension-and-client"></a>확장 및 클라이언트

컴퓨터 내부의 설정을 감사하려면 [가상 컴퓨터 확장이](../../../virtual-machines/extensions/overview.md) 활성화됩니다. 이 확장은 적용 가능한 정책 할당 및 해당 구성 정의를 다운로드합니다.

### <a name="limits-set-on-the-extension"></a>확장에 설정된 제한

컴퓨터 내에서 실행되는 응용 프로그램에 영향을 미치지 않도록 확장을 제한하기 위해 게스트 구성은 CPU의 5% 이상을 초과할 수 없습니다. 이 제한은 기본 제공 및 사용자 지정 정의 모두에 대해 존재합니다.

## <a name="register-guest-configuration-resource-provider"></a>게스트 구성 리소스 공급자 등록

게스트 구성을 사용하려면 먼저 리소스 공급자를 등록해야 합니다. [포털](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal), [Azure PowerShell](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-powershell)또는 [Azure CLI를](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-cli)통해 등록할 수 있습니다. 게스트 구성 정책 할당이 포털을 통해 수행되면 리소스 공급자가 자동으로 등록됩니다.

## <a name="validation-tools"></a>유효성 검사 도구

시스템 내에서 게스트 구성 클라이언트는 로컬 도구를 사용하여 감사를 실행합니다.

다음 표에는 지원되는 각 운영 체제에서 사용되는 로컬 도구 목록이 나와 있습니다.

|운영 체제|유효성 검사 도구|메모|
|-|-|-|
|Windows|[윈도우 파워쉘 원하는 상태 구성](/powershell/scripting/dsc/overview/overview) v2| |
|Linux|[Chef InSpec](https://www.chef.io/inspec/)| 루비와 파이썬이 컴퓨터에 없는 경우 게스트 구성 확장에 의해 설치됩니다. |

### <a name="validation-frequency"></a>유효성 검사 빈도

게스트 구성 클라이언트는 5분마다 새 콘텐츠를 확인합니다. 게스트 할당이 수신되면 해당 구성에 대한 설정이 15분 간격으로 다시 확인됩니다.
감사가 완료되면 결과가 게스트 구성 리소스 공급자에게 전송됩니다. 정책 [평가 트리거](../how-to/get-compliance-data.md#evaluation-triggers)가 발생하면 컴퓨터 상태가 게스트 구성 리소스 공급자에 기록됩니다. 이 업데이트로 인해 Azure Policy에서 Azure 리소스 관리자 속성을 평가합니다. 온디맨드 Azure 정책 평가는 게스트 구성 리소스 공급자의 최신 값을 검색합니다. 그러나 컴퓨터 내의 구성에 대한 새 감사를 트리거하지는 않습니다.

## <a name="supported-client-types"></a>지원되는 클라이언트 유형

다음 표에는 Azure 이미지에서 지원되는 운영 체제 목록이 나와 있습니다.

|게시자|이름|버전|
|-|-|-|
|Canonical|Ubuntu Server|14.04, 16.04, 18.04|
|Credativ|Debian|8, 9|
|Microsoft|Windows Server|2012 데이터 센터, 2012 R2 데이터 센터, 2016 데이터 센터, 2019 데이터 센터|
|Microsoft|Windows 클라이언트|Windows 10|
|OpenLogic|CentOS|7.3, 7.4, 7.5, 7.6, 7.7|
|Red Hat|Red Hat Enterprise Linux|7.4, 7.5, 7.6, 7.7|
|Suse|SLES|12 SP3|

### <a name="unsupported-client-types"></a>지원되지 않는 클라이언트 유형

Windows 서버 나노 서버는 모든 버전에서 지원되지 않습니다.

## <a name="guest-configuration-extension-network-requirements"></a>게스트 구성 확장 네트워크 요구 사항

Azure에서 게스트 구성 리소스 공급자와 통신하기 위해 컴퓨터는 포트 **443의**Azure 데이터 센터에 대한 아웃바운드 액세스가 필요합니다. Azure의 네트워크에서 아웃바운드 트래픽을 허용하지 않는 경우 [네트워크 보안 그룹](../../../virtual-network/manage-network-security-group.md#create-a-security-rule) 규칙을 사용하여 예외를 구성합니다.
[서비스 태그](../../../virtual-network/service-tags-overview.md) "GuestAndHybridManagement"는 게스트 구성 서비스를 참조하는 데 사용할 수 있습니다.

## <a name="azure-managed-identity-requirements"></a>Azure 관리 ID 요구 사항

가상 시스템에 확장을 추가하는 **DeployIfNotExists** 정책은 존재하지 않는 경우 관리되는 ID가 할당된 시스템도 사용하도록 설정합니다.

> [!WARNING]
> 시스템이 할당된 관리 ID를 사용하도록 설정하는 정책에 대한 범위에서 가상 시스템에 관리되는 ID를 할당하는 사용자를 사용하지 마십시오. 할당된 ID가 대체되고 컴퓨터가 응답하지 않을 수 있습니다.

## <a name="guest-configuration-definition-requirements"></a>게스트 구성 정의 요구 사항

게스트 구성에서 실행되는 각 감사에는 두 가지 정책 정의인 **DeployIfNotExists** 정의와 **AuditIfNotExists** 정의가 필요합니다. 

**DeployIfNotExists** 정책 정의는 다음 항목의 유효성을 검사하고 수정합니다.

- 컴퓨터가 평가할 구성이 할당되었는지 확인합니다. 현재 할당이 없는 경우 과제를 받고 다음을 통해 컴퓨터를 준비합니다.
  - [관리되는 ID를](../../../active-directory/managed-identities-azure-resources/overview.md) 사용하여 컴퓨터에 인증
  - **Microsoft.GuestConfiguration** 확장의 최신 버전 설치
  - [유효성 검사 도구](#validation-tools) 및 종속성(필요한 경우) 설치

**DeployIfNotExists** 할당이 비준수인 경우 [업데이트 관리 작업을](../how-to/remediate-resources.md#create-a-remediation-task) 사용할 수 있습니다.

**DeployIfNotExists** 할당이 규정을 준수하면 **AuditIfNotExists** 정책 할당이 게스트 할당이 규정을 준수하는지 또는 비준수인지 결정합니다. 유효성 검사 도구는 게스트 구성 클라이언트에 결과를 제공합니다. 클라이언트는 게스트 확장에 결과를 전달하므로 게스트 구성 리소스 공급자를 통해 사용할 수 있습니다.

Azure Policy는 게스트 구성 리소스 공급자 **complianceStatus** 속성을 사용하여 **규정 준수** 노드에서 규정 준수를 보고합니다. 자세한 내용은 [규정 준수 데이터 가져오기](../how-to/get-compliance-data.md)를 참조하세요.

> [!NOTE]
> **[DeployIfNotExists]** **감사IfNotExists** 정책이 결과를 반환하는 데 필요합니다. **DeployIfNotExists가**없는 **경우 AuditIfNotExists** 정책에는 "0/0" 리소스가 상태로 표시됩니다.

할당에 사용할 정의를 그룹화할 수 있도록, 게스트 구성을 위한 모든 기본 제공 정책은 이니셔티브에 포함됩니다. _ \[미리 보기라는\]_ 기본 제공 이니셔티브 : Linux 및 Windows 컴퓨터 내부의 암호 보안 감사에는 18개의 정책이 포함되어 있습니다. 그리고 Window용 **DeployIfNotExists** 및 **AuditIfNotExists** 쌍 6개와 Linux용 쌍 3개가 있습니다. [정책 정의](definition-structure.md#policy-rule) 논리는 대상 운영 체제만 평가되는지 확인합니다.

#### <a name="auditing-operating-system-settings-following-industry-baselines"></a>업계 기준에 따른 운영 체제 설정 감사

Azure Policy의 한 가지 이니셔티브는 "기준"에 따라 운영 체제 설정을 감사하는 기능을 제공합니다. 정의, _ \[미리\]보기 : Azure 보안 기준 설정과 일치하지 않는 Windows VM 감사에는_ Active Directory 그룹 정책에 기반한 규칙 집합이 포함됩니다.

대부분의 설정은 매개 변수로 사용할 수 있습니다. 매개 변수를 사용하면 감사된 내용을 사용자 지정할 수 있습니다. 정책을 요구 사항에 맞게 조정하거나 업계 규제 표준과 같은 타사 정보에 정책을 매핑합니다.

일부 매개 변수는 정수 값 범위를 지원합니다. 예를 들어 최대 암호 사용 시간 설정은 효과적인 그룹 정책 설정을 감사할 수 있습니다. "1,70" 범위는 사용자가 적어도 70일마다 암호를 변경해야 하지만 하루 이상 변경해야 한다는 것을 확인합니다.

Azure Resource Manager 배포 템플릿을 사용하여 정책을 할당하는 경우 매개 변수 파일을 사용하여 예외를 관리합니다. Git과 같은 버전 제어 시스템에 파일을 체크 인합니다. 파일 변경에 대한 주석은 할당이 예상 값에 대한 예외인 이유를 증명합니다.

#### <a name="applying-configurations-using-guest-configuration"></a>게스트 구성을 사용하여 구성 적용

Azure 정책의 최신 기능은 컴퓨터 내부의 설정을 구성합니다. 정의 _Windows 컴퓨터의 표준 시간대를 구성하면_ 표준 시간대를 구성하여 컴퓨터를 변경합니다.

_구성으로_시작하는 정의를 할당할 때 _Windows VM에서 게스트 구성 정책을 사용하도록 설정하려면 정의 배포 필수 구성 요소도_할당해야 합니다. 원하는 경우 이니셔티브에서 이러한 정의를 결합할 수 있습니다.

#### <a name="assigning-policies-to-machines-outside-of-azure"></a>Azure 외부의 컴퓨터에 정책 할당

게스트 구성에 사용할 수 있는 감사 정책에는 **Microsoft.HybridCompute/컴퓨터** 리소스 유형이 포함됩니다. 정책 할당 범위에 있는 [서버에 대해 Azure Arc에](../../../azure-arc/servers/overview.md) 온보온된 모든 컴퓨터가 자동으로 포함됩니다.

### <a name="multiple-assignments"></a>여러 할당

게스트 구성 정책은 현재 정책 할당이 다른 매개 변수를 사용하는 경우에도 시스템당 한 번만 동일한 게스트 할당을 할당하는 것을 지원합니다.

## <a name="client-log-files"></a>클라이언트 로그 파일

게스트 구성 확장프로그램은 로그 파일을 다음 위치에 씁니다.

Windows: `C:\ProgramData\GuestConfig\gc_agent_logs\gc_agent.log`

Linux: `/var/lib/GuestConfig/gc_agent_logs/gc_agent.log`

Where는 `<version>` 현재 버전 번호를 나타냅니다.

### <a name="collecting-logs-remotely"></a>원격으로 로그 수집

게스트 구성 구성 또는 모듈 문제 해결의 첫 번째 `Test-GuestConfigurationPackage` 단계는 [Windows에 대한 사용자 지정 게스트 구성 감사 정책을 만드는](../how-to/guest-configuration-create.md#step-by-step-creating-a-custom-guest-configuration-audit-policy-for-windows)방법 의 다음 단계를 따르는 cmdlet을 사용하는 것입니다.
성공적이지 않은 경우 클라이언트 로그를 수집하면 문제를 진단하는 데 도움이 될 수 있습니다.

#### <a name="windows"></a>Windows

[Azure VM 실행 명령을](../../../virtual-machines/windows/run-command.md)사용하여 로그 파일에서 정보를 캡처하는 경우 PowerShell 스크립트가 유용할 수 있습니다.

```powershell
$linesToIncludeBeforeMatch = 0
$linesToIncludeAfterMatch = 10
$logPath = 'C:\ProgramData\GuestConfig\gc_agent_logs\gc_agent.log'
Select-String -Path $logPath -pattern 'DSCEngine','DSCManagedEngine' -CaseSensitive -Context $linesToIncludeBeforeMatch,$linesToIncludeAfterMatch | Select-Object -Last 10
```

#### <a name="linux"></a>Linux

[Azure VM 실행 명령을](../../../virtual-machines/linux/run-command.md)사용하여 로그 파일에서 정보를 캡처하는 경우 다음 예제 Bash 스크립트가 유용할 수 있습니다.

```Bash
linesToIncludeBeforeMatch=0
linesToIncludeAfterMatch=10
logPath=/var/lib/GuestConfig/gc_agent_logs/gc_agent.log
egrep -B $linesToIncludeBeforeMatch -A $linesToIncludeAfterMatch 'DSCEngine|DSCManagedEngine' $logPath | tail
```

## <a name="guest-configuration-samples"></a>게스트 구성 샘플

게스트 구성 기본 제공 정책 샘플은 다음 위치에서 사용할 수 있습니다.

- [기본 제공 정책 정의 - 게스트 구성](../samples/built-in-policies.md#guest-configuration)
- [기본 제공 이니셔티브 - 게스트 구성](../samples/built-in-initiatives.md#guest-configuration)
- [Azure 정책 샘플 GitHub 리포지토리](https://github.com/Azure/azure-policy/tree/master/built-in-policies/policySetDefinitions/Guest%20Configuration)

## <a name="next-steps"></a>다음 단계

- [게스트 구성 준수 보기에서](../how-to/determine-non-compliance.md#compliance-details-for-guest-configuration) 각 설정의 세부 정보를 보는 방법 알아보기
- Azure 정책 [샘플의 예제를 검토합니다.](../samples/index.md)
- [Azure Policy 정의 구조](definition-structure.md)를 검토합니다.
- [정책 효과 이해](effects.md)를 검토합니다.
- [프로그래밍 방식으로 정책을 만드는](../how-to/programmatically-create.md)방법을 이해합니다.
- [규정 준수 데이터를 얻는](../how-to/get-compliance-data.md)방법에 대해 알아봅니다.
- [비준수 리소스를 수정하는](../how-to/remediate-resources.md)방법에 대해 알아봅니다.
- 관리 그룹이 Azure 관리 그룹으로 리소스 구성을 통해 어떤 내용인지 [검토합니다.](../../management-groups/overview.md)
