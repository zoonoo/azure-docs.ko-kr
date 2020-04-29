---
title: 가상 컴퓨터의 콘텐츠를 감사 하는 방법 알아보기
description: Azure Policy 게스트 구성 에이전트를 사용 하 여 가상 컴퓨터 내에서 설정을 감사 하는 방법을 알아봅니다.
ms.date: 11/04/2019
ms.topic: conceptual
ms.openlocfilehash: 89f7cc3931971d70b441490f77b67ace89434c2b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82025223"
---
# <a name="understand-azure-policys-guest-configuration"></a>Azure Policy 게스트 구성 이해

감사 및 [수정](../how-to/remediate-resources.md) Azure 리소스 외에도 Azure Policy는 컴퓨터 내의 설정을 감사할 수 있습니다. 게스트 구성 확장 및 클라이언트가 유효성 검사를 수행합니다. 클라이언트를 통한 확장은 다음과 같은 설정의 유효성을 검사합니다.

- 운영 체제의 구성
- 애플리케이션 구성 또는 현재 상태
- 환경 설정

이 시점에서 대부분의 Azure Policy 게스트 구성 정책은 컴퓨터 내의 설정만 감사 합니다. 구성은 적용 되지 않습니다. 단, [아래에서 참조](#applying-configurations-using-guest-configuration)되는 기본 제공 정책은 한 가지입니다.

## <a name="resource-provider"></a>리소스 공급자

게스트 구성을 사용하려면 먼저 리소스 공급자를 등록해야 합니다. 포털을 통해 게스트 구성 정책의 할당을 완료 하면 리소스 공급자가 자동으로 등록 됩니다. [포털](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal), [Azure PowerShell](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-powershell)또는 [Azure CLI](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-cli)를 통해 수동으로 등록할 수 있습니다.

## <a name="extension-and-client"></a>확장 및 클라이언트

컴퓨터 내의 설정을 감사 하려면 [가상 머신 확장](../../../virtual-machines/extensions/overview.md) 을 사용 하도록 설정 합니다. 이 확장은 적용 가능한 정책 할당 및 해당 구성 정의를 다운로드합니다.

> [!Important]
> 게스트 구성 확장은 Azure virtual machines에서 감사를 수행 하는 데 필요 합니다.
> 확장을 대규모로 배포 하려면 다음 정책 정의를 할당 합니다.
>   - [Windows VM에서 게스트 구성 정책을 사용하도록 설정하기 위한 필수 조건 배포](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0ecd903d-91e7-4726-83d3-a229d7f2e293)
>   - [Linux VM에서 게스트 구성 정책을 사용하도록 설정하기 위한 필수 조건 배포](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffb27e9e0-526e-4ae1-89f2-a2a0bf0f8a50)

### <a name="limits-set-on-the-extension"></a>확장에 설정 된 제한

컴퓨터 내부에서 실행 되는 응용 프로그램에 영향을 주지 않도록 확장을 제한 하기 위해 게스트 구성은 CPU의 5%를 초과할 수 없습니다. 기본 제공 및 사용자 지정 정의 모두에 대해 이러한 제한 사항이 존재 합니다.

### <a name="validation-tools"></a>유효성 검사 도구

컴퓨터 내부에서 게스트 구성 클라이언트는 로컬 도구를 사용 하 여 감사를 실행 합니다.

다음 표에는 지원되는 각 운영 체제에서 사용되는 로컬 도구 목록이 나와 있습니다.

|운영 체제|유효성 검사 도구|메모|
|-|-|-|
|Windows|[Windows PowerShell 필요한 상태 구성](/powershell/scripting/dsc/overview/overview) v2| |
|Linux|[Chef InSpec](https://www.chef.io/inspec/)| Ruby 및 Python이 컴퓨터에 없는 경우 게스트 구성 확장에 의해 설치 됩니다. |

### <a name="validation-frequency"></a>유효성 검사 빈도

게스트 구성 클라이언트는 5분마다 새 콘텐츠를 확인합니다. 게스트 할당을 받은 후 15 분 간격으로 해당 구성에 대 한 설정을 다시 확인 합니다.
감사를 완료 하면 결과가 게스트 구성 리소스 공급자에 게 전송 됩니다. 정책 [평가 트리거](../how-to/get-compliance-data.md#evaluation-triggers)가 발생하면 컴퓨터 상태가 게스트 구성 리소스 공급자에 기록됩니다. 이 업데이트를 실행 하면 Azure Policy에서 Azure Resource Manager 속성을 평가 합니다. 주문형 Azure Policy 평가판은 게스트 구성 리소스 공급자에서 최신 값을 검색 합니다. 그러나 컴퓨터 내에서 구성의 새 감사를 트리거하지 않습니다.

## <a name="supported-client-types"></a>지원되는 클라이언트 유형

게스트 구성 정책은 새 버전을 포함 합니다. 게스트 구성 에이전트가 호환 되지 않는 경우 Azure marketplace에서 사용할 수 있는 이전 버전의 운영 체제가 제외 됩니다. 다음 표에는 Azure 이미지에서 지원 되는 운영 체제의 목록이 나와 있습니다.

|게시자|속성|버전|
|-|-|-|
|Canonical|Ubuntu Server|14.04 이상|
|Credativ|Debian|8 이상|
|Microsoft|Windows Server|2012 이상|
|Microsoft|Windows 클라이언트|Windows 10|
|OpenLogic|CentOS|7.3 이상|
|Red Hat|Red Hat Enterprise Linux|7.4 이상|
|Suse|SLES|12 SP3 이상|

사용자 지정 가상 머신 이미지는 위의 표에 있는 운영 체제 중 하나인 경우에만 게스트 구성 정책에서 지원 됩니다.

### <a name="unsupported-client-types"></a>지원되지 않는 클라이언트 유형

Windows Server Nano Server는 어떤 버전 에서도 지원 되지 않습니다.

## <a name="guest-configuration-extension-network-requirements"></a>게스트 구성 확장 네트워크 요구 사항

Azure에서 게스트 구성 리소스 공급자와 통신 하려면 컴퓨터에 포트 **443**에서 azure 데이터 센터에 대 한 아웃 바운드 액세스가 필요 합니다. Azure의 네트워크에서 아웃 바운드 트래픽을 허용 하지 않는 경우 [네트워크 보안 그룹](../../../virtual-network/manage-network-security-group.md#create-a-security-rule) 규칙을 사용 하 여 예외를 구성 합니다.
[서비스 태그](../../../virtual-network/service-tags-overview.md) "GuestAndHybridManagement"는 게스트 구성 서비스를 참조 하는 데 사용할 수 있습니다.

## <a name="azure-managed-identity-requirements"></a>Azure 관리 되는 id 요구 사항

가상 컴퓨터에 확장을 추가 하는 **Deployifnotexists** 정책은 시스템 할당 관리 id가 없는 경우에도 사용 하도록 설정 합니다.

> [!WARNING]
> 시스템 할당 관리 id를 사용 하는 정책에 대해 범위 내에서 사용자가 관리 하는 관리 id를 사용 하지 않도록 합니다. 사용자 할당 id가 바뀌고 시스템이 응답 하지 않을 수 있습니다.

## <a name="guest-configuration-definition-requirements"></a>게스트 구성 정의 요구 사항

게스트 구성에 의해 실행 되는 각 감사에는 **Deployifnotexists** 정의 및 **AuditIfNotExists** 정의의 두 정책 정의가 필요 합니다. 

**DeployIfNotExists** 정책 정의는 다음 항목의 유효성을 검사하고 수정합니다.

- 컴퓨터에 평가할 구성이 할당 되었는지 확인 합니다. 현재 할당이 없는 경우 다음을 수행 하 여 할당을 가져오고 컴퓨터를 준비 합니다.
  - [관리 id](../../../active-directory/managed-identities-azure-resources/overview.md) 를 사용 하 여 컴퓨터 인증
  - **Microsoft.GuestConfiguration** 확장의 최신 버전 설치
  - [유효성 검사 도구](#validation-tools) 및 종속성(필요한 경우) 설치

**Deployifnotexists** 할당이 비규격 이면 [수정 작업](../how-to/remediate-resources.md#create-a-remediation-task) 을 사용할 수 있습니다.

**Deployifnotexists** 할당이 규격을 준수 하는 경우 **AuditIfNotExists** 정책 할당은 게스트 할당이 규격 인지 아니면 비규격 인지를 결정 합니다. 유효성 검사 도구는 게스트 구성 클라이언트에 결과를 제공합니다. 클라이언트는 게스트 확장에 결과를 전달하므로 게스트 구성 리소스 공급자를 통해 사용할 수 있습니다.

Azure Policy는 게스트 구성 리소스 공급자 **complianceStatus** 속성을 사용하여 **규정 준수** 노드에서 규정 준수를 보고합니다. 자세한 내용은 [규정 준수 데이터 가져오기](../how-to/get-compliance-data.md)를 참조하세요.

> [!NOTE]
> **AuditIfNotExists** 정책에서 결과를 반환 하려면 **Deployifnotexists** 정책이 필요 합니다. **Deployifnotexists**가 없으면 **AuditIfNotExists** 정책에 "0/0" 리소스가 상태로 표시 됩니다.

할당에 사용할 정의를 그룹화할 수 있도록, 게스트 구성을 위한 모든 기본 제공 정책은 이니셔티브에 포함됩니다. _ \[Preview\]: Linux 및 Windows 컴퓨터 내에서 암호 보안 감사_ 라는 기본 제공 이니셔티브는 18 개의 정책을 포함 합니다. 그리고 Window용 **DeployIfNotExists** 및 **AuditIfNotExists** 쌍 6개와 Linux용 쌍 3개가 있습니다. [정책 정의](definition-structure.md#policy-rule) 논리는 대상 운영 체제만 평가 되는지 확인 합니다.

#### <a name="auditing-operating-system-settings-following-industry-baselines"></a>업계 기준에 따라 운영 체제 설정 감사

Azure Policy의 한 이니셔티브는 "기준"에 따라 운영 체제 설정을 감사 하는 기능을 제공 합니다. _미리 보기\]: Azure 보안 기준 설정과 일치 하지 않는 Windows vm 감사 정의는 Active Directory 그룹 정책를 기반으로 하는 규칙 집합을 포함 \[_ 합니다.

대부분의 설정은 매개 변수로 사용할 수 있습니다. 매개 변수를 사용 하 여 감사할 항목을 사용자 지정할 수 있습니다. 정책을 요구 사항에 맞게 정렬 하거나 업계 규정 표준 같은 타사 정보에 정책을 매핑합니다.

일부 매개 변수는 정수 값 범위를 지원 합니다. 예를 들어 최대 암호 사용 기간 설정은 유효 그룹 정책 설정을 감사할 수 있습니다. "1, 70" 범위는 사용자가 최소 70 일 마다 암호를 변경 해야 하지만 1 일이 넘지 않는지 확인 합니다.

Azure Resource Manager 배포 템플릿을 사용 하 여 정책을 할당 하는 경우 매개 변수 파일을 사용 하 여 예외를 관리 합니다. Git와 같은 버전 제어 시스템에 파일을 체크 인 합니다. 파일 변경 내용에 대 한 설명은 예상 값에 대 한 할당이 예외 인 증명 정보를 제공 합니다.

#### <a name="applying-configurations-using-guest-configuration"></a>게스트 구성을 사용 하 여 구성 적용

Azure Policy의 최신 기능은 컴퓨터 내에서 설정을 구성 합니다. 정의에서 _Windows 컴퓨터의 표준 시간대를 구성_ 하면 표준 시간대를 구성 하 여 컴퓨터를 변경 합니다.

_구성_으로 시작 하는 정의를 할당할 때 _Windows Vm에서 게스트 구성 정책을 사용 하려면 정의 배포 필수 구성 요소를_할당 해야 합니다. 선택 하는 경우 이니셔티브에서 이러한 정의를 조합할 수 있습니다.

#### <a name="assigning-policies-to-machines-outside-of-azure"></a>Azure 외부의 컴퓨터에 정책 할당

게스트 구성에 사용할 수 있는 감사 정책에는 **HybridCompute/machines** 리소스 종류가 포함 됩니다. 정책 할당 범위 내에 있는 [서버에 대해 Azure Arc](../../../azure-arc/servers/overview.md) 로 등록 모든 컴퓨터는 자동으로 포함 됩니다.

### <a name="multiple-assignments"></a>여러 할당

게스트 구성 정책은 현재 정책 할당에서 다른 매개 변수를 사용 하는 경우에도 컴퓨터 마다 한 번 동일한 게스트 할당 할당을 지원 합니다.

## <a name="client-log-files"></a>클라이언트 로그 파일

게스트 구성 확장은 다음 위치에 로그 파일을 기록 합니다.

Windows: `C:\ProgramData\GuestConfig\gc_agent_logs\gc_agent.log`

Linux: `/var/lib/GuestConfig/gc_agent_logs/gc_agent.log`

여기서 `<version>` 는 현재 버전 번호를 참조 합니다.

### <a name="collecting-logs-remotely"></a>원격으로 로그 수집

게스트 구성 구성 또는 모듈 문제를 해결 하는 첫 번째 단계는 `Test-GuestConfigurationPackage` [Windows에 대 한 사용자 지정 게스트 구성 감사 정책을 만드는](../how-to/guest-configuration-create.md#step-by-step-creating-a-custom-guest-configuration-audit-policy-for-windows)방법 단계에 따라 cmdlet을 사용 하는 것입니다.
실패 한 경우 클라이언트 로그를 수집 하면 문제를 진단 하는 데 도움이 될 수 있습니다.

#### <a name="windows"></a>Windows

[AZURE VM Run 명령을](../../../virtual-machines/windows/run-command.md)사용 하 여 로그 파일에서 정보를 수집 합니다. 다음 예제 PowerShell 스크립트를 사용 하면 도움이 될 수 있습니다.

```powershell
$linesToIncludeBeforeMatch = 0
$linesToIncludeAfterMatch = 10
$logPath = 'C:\ProgramData\GuestConfig\gc_agent_logs\gc_agent.log'
Select-String -Path $logPath -pattern 'DSCEngine','DSCManagedEngine' -CaseSensitive -Context $linesToIncludeBeforeMatch,$linesToIncludeAfterMatch | Select-Object -Last 10
```

#### <a name="linux"></a>Linux

[AZURE VM Run 명령을](../../../virtual-machines/linux/run-command.md)사용 하 여 로그 파일에서 정보 캡처 다음 예 Bash 스크립트를 사용 하는 것이 도움이 될 수 있습니다.

```Bash
linesToIncludeBeforeMatch=0
linesToIncludeAfterMatch=10
logPath=/var/lib/GuestConfig/gc_agent_logs/gc_agent.log
egrep -B $linesToIncludeBeforeMatch -A $linesToIncludeAfterMatch 'DSCEngine|DSCManagedEngine' $logPath | tail
```

## <a name="guest-configuration-samples"></a>게스트 구성 샘플

게스트 구성 기본 제공 정책 샘플은 다음 위치에서 사용할 수 있습니다.

- [기본 제공 정책 정의-게스트 구성](../samples/built-in-policies.md#guest-configuration)
- [기본 제공 이니셔티브-게스트 구성](../samples/built-in-initiatives.md#guest-configuration)
- [Azure Policy 샘플 GitHub 리포지토리](https://github.com/Azure/azure-policy/tree/master/built-in-policies/policySetDefinitions/Guest%20Configuration)

## <a name="next-steps"></a>다음 단계

- [게스트 구성 호환성 보기](../how-to/determine-non-compliance.md#compliance-details-for-guest-configuration) 에서 각 설정에 대 한 세부 정보를 보는 방법에 대해 알아봅니다.
- [Azure Policy 샘플](../samples/index.md)에서 예제를 검토 합니다.
- [Azure Policy 정의 구조](definition-structure.md)를 검토합니다.
- [정책 효과 이해](effects.md)를 검토합니다.
- [프로그래밍 방식으로 정책을 만드는](../how-to/programmatically-create.md)방법을 알아봅니다.
- [준수 데이터를 가져오는](../how-to/get-compliance-data.md)방법에 대해 알아봅니다.
- [비준수 리소스](../how-to/remediate-resources.md)를 수정 하는 방법에 대해 알아봅니다.
- [Azure 관리 그룹을 사용](../../management-groups/overview.md)하 여 리소스를 구성 하는 관리 그룹을 검토 합니다.
