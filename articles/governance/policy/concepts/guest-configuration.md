---
title: 컴퓨터의 콘텐츠를 감사 하는 방법 이해
description: Azure Policy 게스트 구성을 사용 하 여 Azure 컴퓨터 내에서 설정을 감사 하는 방법을 알아봅니다.
author: DCtheGeek
ms.author: dacoulte
ms.date: 09/04/2019
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.openlocfilehash: bfa7f7486a9fa5ef62e8bf9e01dbe39d675d8d27
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70308564"
---
# <a name="understand-azure-policys-guest-configuration"></a>Azure Policy 게스트 구성 이해

감사 및 [수정](../how-to/remediate-resources.md) Azure 리소스 외에도 Azure Policy는 컴퓨터 내의 설정을 감사할 수 있습니다. 게스트 구성 확장 및 클라이언트가 유효성 검사를 수행합니다. 확장은 클라이언트를 통해 다음과 같은 설정의 유효성을 검사 합니다.

- 운영 체제의 구성
- 응용 프로그램 구성 또는 상태
- 환경 설정

지금은 게스트 구성 Azure Policy 컴퓨터 내의 설정만 감사 합니다. 구성은 적용 되지 않습니다.

## <a name="extension-and-client"></a>확장 및 클라이언트

컴퓨터 내의 설정을 감사 하려면 [가상 머신 확장](../../../virtual-machines/extensions/overview.md) 을 사용 하도록 설정 합니다. 이 확장은 적용 가능한 정책 할당 및 해당 구성 정의를 다운로드합니다.

### <a name="limits-set-on-the-extension"></a>확장에 설정 된 제한

컴퓨터 내부에서 실행 되는 응용 프로그램에 영향을 주지 않도록 확장을 제한 하기 위해 게스트 구성은 CPU 사용률의 5%를 초과할 수 없습니다. 기본 제공 및 사용자 지정 정의 모두에 대해 이러한 제한 사항이 존재 합니다.

## <a name="register-guest-configuration-resource-provider"></a>게스트 구성 리소스 공급자 등록

게스트 구성을 사용하려면 먼저 리소스 공급자를 등록해야 합니다. 포털 또는 PowerShell을 통해 등록할 수 있습니다. 포털을 통해 게스트 구성 정책의 할당을 완료 하면 리소스 공급자가 자동으로 등록 됩니다.

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

컴퓨터 내부에서 게스트 구성 클라이언트는 로컬 도구를 사용 하 여 감사를 실행 합니다.

다음 표에는 지원되는 각 운영 체제에서 사용되는 로컬 도구 목록이 나와 있습니다.

|운영 체제|유효성 검사 도구|참고|
|-|-|-|
|Windows|[Microsoft Desired State Configuration](/powershell/dsc) v2| |
|Linux|[Chef InSpec](https://www.chef.io/inspec/)| 게스트 구성 확장을 통해 Ruby 및 Python이 설치됩니다. |

### <a name="validation-frequency"></a>유효성 검사 빈도

게스트 구성 클라이언트는 5분마다 새 콘텐츠를 확인합니다. 게스트 할당이 수신되면 15분 간격으로 설정을 확인합니다. 감사가 완료되는 즉시 게스트 구성 리소스 공급자로 결과가 전송됩니다. 정책 [평가 트리거](../how-to/get-compliance-data.md#evaluation-triggers)가 발생하면 컴퓨터 상태가 게스트 구성 리소스 공급자에 기록됩니다. 이 업데이트를 실행 하면 Azure Policy에서 Azure Resource Manager 속성을 평가 합니다. 주문형 Azure Policy 평가판은 게스트 구성 리소스 공급자에서 최신 값을 검색 합니다. 그러나 컴퓨터 내에서 구성의 새 감사를 트리거하지 않습니다.

## <a name="supported-client-types"></a>지원되는 클라이언트 유형

다음 표에는 Azure 이미지에서 지원되는 운영 체제 목록이 나와 있습니다.

|게시자|이름|버전|
|-|-|-|
|정식|Ubuntu Server|14.04, 16.04, 18.04|
|Credativ|Debian|8, 9|
|Microsoft|Windows Server|2012 datacenter, 2012 R2 Datacenter, 2016 Datacenter, 2019 Datacenter|
|Microsoft|Windows 클라이언트|Windows 10|
|OpenLogic|CentOS|7.3, 7.4, 7.5|
|Red Hat|Red Hat Enterprise Linux|7.4, 7.5|
|Suse|SLES|12 SP3|

> [!IMPORTANT]
> 게스트 구성은 지원 되는 OS를 실행 하는 노드를 감사할 수 있습니다. 사용자 지정 이미지를 사용 하는 가상 머신을 감사 하려는 경우 **Deployifnotexists** 정의를 복제 하 고 이미지 속성을 포함 하도록 **if** 섹션을 수정 해야 합니다.

### <a name="unsupported-client-types"></a>지원되지 않는 클라이언트 유형

Windows Server Nano Server는 어떤 버전 에서도 지원 되지 않습니다.

## <a name="guest-configuration-extension-network-requirements"></a>게스트 구성 확장 네트워크 요구 사항

Azure에서 게스트 구성 리소스 공급자와 통신 하려면 컴퓨터에 포트 **443**에서 azure 데이터 센터에 대 한 아웃 바운드 액세스가 필요 합니다. 아웃 바운드 트래픽을 허용 하지 않는 Azure에서 개인 가상 네트워크를 사용 하는 경우 [네트워크 보안 그룹](../../../virtual-network/manage-network-security-group.md#create-a-security-rule) 규칙을 사용 하 여 예외를 구성 합니다. Azure Policy 게스트 구성에 대 한 서비스 태그가 현재 존재 하지 않습니다.

IP 주소 목록 [Microsoft Azure 데이터 센터 Ip 범위](https://www.microsoft.com/download/details.aspx?id=41653)를 다운로드할 수 있습니다. 이 파일은 매주 업데이트되고 현재 배포된 범위와 향후 예정된 IP 범위 변경 내용을 포함합니다. Vm이 배포 된 지역에서 Ip에 대 한 아웃 바운드 액세스를 허용 하기만 하면 됩니다.

> [!NOTE]
> Azure 데이터 센터 IP 주소 XML 파일은 Microsoft Azure 데이터 센터에서 사용되는 IP 주소 범위를 나열합니다. 이 파일에는 컴퓨팅, SQL 및 스토리지 범위가 포함되어 있습니다. 업데이트된 파일이 매주 게시됩니다. 이 파일에는 현재 배포된 범위 및 IP 범위에 대해 예정된 변경 내용이 반영됩니다. 파일에 제시된 새 범위는 데이터 센터에서 적어도 한 주 동안 사용되지 않습니다. 새 XML 파일을 매주 다운로드하는 것이 좋습니다. 그런 다음 Azure에서 실행되는 서비스를 올바르게 식별하도록 사이트를 업데이트합니다. Azure ExpressRoute 사용자는 이 파일을 사용해 Azure 공간에 대한 BGP(Border Gateway Protocol) 공지 사항을 매월 첫 주에 업데이트하고 있음에 유의해야 합니다.

## <a name="guest-configuration-definition-requirements"></a>게스트 구성 정의 요구 사항

게스트 구성에 의해 실행 되는 각 감사에는 **Deployifnotexists** 정의 및 **AuditIfNotExists** 정의의 두 정책 정의가 필요 합니다. **Deployifnotexists** 정의는 [유효성 검사 도구](#validation-tools)를 지원 하기 위해 게스트 구성 에이전트 및 기타 구성 요소를 사용 하 여 컴퓨터를 준비 하는 데 사용 됩니다.

**DeployIfNotExists** 정책 정의는 다음 항목의 유효성을 검사하고 수정합니다.

- 컴퓨터에 평가할 구성이 할당 되었는지 확인 합니다. 현재 할당이 없는 경우 다음을 수행 하 여 할당을 가져오고 컴퓨터를 준비 합니다.
  - [관리 id](../../../active-directory/managed-identities-azure-resources/overview.md) 를 사용 하 여 컴퓨터 인증
  - **Microsoft.GuestConfiguration** 확장의 최신 버전 설치
  - [유효성 검사 도구](#validation-tools) 및 종속성(필요한 경우) 설치

**Deployifnotexists** 할당이 비규격 이면 [수정 작업](../how-to/remediate-resources.md#create-a-remediation-task) 을 사용할 수 있습니다.

**Deployifnotexists** 할당이 규격을 준수 하는 경우 **AuditIfNotExists** 정책 할당은 로컬 유효성 검사 도구를 사용 하 여 구성 할당이 규격 인지 아니면 비규격 인지를 확인 합니다. 유효성 검사 도구는 게스트 구성 클라이언트에 결과를 제공합니다. 클라이언트는 게스트 확장에 결과를 전달하므로 게스트 구성 리소스 공급자를 통해 사용할 수 있습니다.

Azure Policy는 게스트 구성 리소스 공급자 **complianceStatus** 속성을 사용하여 **규정 준수** 노드에서 규정 준수를 보고합니다. 자세한 내용은 [규정 준수 데이터 가져오기](../how-to/getting-compliance-data.md)를 참조하세요.

> [!NOTE]
> **AuditIfNotExists** 정책에서 결과를 반환 하려면 **Deployifnotexists** 정책이 필요 합니다. **Deployifnotexists**가 없으면 **AuditIfNotExists** 정책에 "0/0" 리소스가 상태로 표시 됩니다.

할당에 사용할 정의를 그룹화할 수 있도록, 게스트 구성을 위한 모든 기본 제공 정책은 이니셔티브에 포함됩니다. *[미리 보기]: Linux 및 Windows 컴퓨터* 내의 암호 보안 설정 감사는 18 개의 정책을 포함 합니다. 그리고 Window용 **DeployIfNotExists** 및 **AuditIfNotExists** 쌍 6개와 Linux용 쌍 3개가 있습니다. [정책 정의](definition-structure.md#policy-rule) 논리는 대상 운영 체제만 평가 되는지 확인 합니다.

### <a name="multiple-assignments"></a>여러 할당

게스트 구성 정책은 현재 정책 할당에서 다른 매개 변수를 사용 하는 경우에도 컴퓨터 마다 한 번 동일한 게스트 할당 할당을 지원 합니다.

## <a name="built-in-resource-modules"></a>기본 제공 리소스 모듈

게스트 구성 확장을 설치 하는 경우 ' GuestConfiguration ' PowerShell 모듈은 최신 버전의 DSC 리소스 모듈에 포함 됩니다. 이 모듈은 모듈 페이지 [GuestConfiguration/](https://www.powershellgallery.com/packages/GuestConfiguration/)에서 ' 수동 다운로드 ' 링크를 사용 하 여 PowerShell 갤러리에서 다운로드할 수 있습니다.
압축을 풀고 검토할 수 있도록 '. nupkg ' 파일 형식의 이름을 '. p s '로 바꿀 수 있습니다.

## <a name="client-log-files"></a>클라이언트 로그 파일

게스트 구성 확장은 다음 위치에 로그 파일을 기록 합니다.

Windows: `C:\Packages\Plugins\Microsoft.GuestConfiguration.ConfigurationforWindows\<version>\dsc\logs\dsc.log`

Linux: `/var/lib/waagent/Microsoft.GuestConfiguration.ConfigurationforLinux-<version>/GCAgent/logs/dsc.log`

여기서 `<version>` 는 현재 버전 번호를 참조 합니다.

### <a name="collecting-logs-remotely"></a>원격으로 로그 수집

게스트 구성 구성 또는 모듈 문제를 해결 하는 첫 번째 단계는 `Test-GuestConfigurationPackage` [게스트 구성 패키지 테스트](../how-to/guest-configuration-create.md#test-a-guest-configuration-package)의 단계에 따라 cmdlet을 사용 하는 것입니다.
실패 한 경우 클라이언트 로그를 수집 하면 문제를 진단 하는 데 도움이 될 수 있습니다.

#### <a name="windows"></a>Windows

Azure VM 실행 명령 기능을 사용 하 여 Windows 컴퓨터의 로그 파일에서 정보를 캡처하려면 다음과 같은 PowerShell 스크립트를 사용 하는 것이 도움이 될 수 있습니다. 자세한 내용은 [Run 명령을 사용 하 여 WINDOWS VM에서 PowerShell 스크립트 실행](../../../virtual-machines/windows/run-command.md)을 참조 하세요.

```powershell
$linesToIncludeBeforeMatch = 0
$linesToIncludeAfterMatch = 10
$latestVersion = Get-ChildItem -Path 'C:\Packages\Plugins\Microsoft.GuestConfiguration.ConfigurationforWindows\' | ForEach-Object {$_.FullName} | Sort-Object -Descending | Select-Object -First 1
Select-String -Path "$latestVersion\dsc\logs\dsc.log" -pattern 'DSCEngine','DSCManagedEngine' -CaseSensitive -Context $linesToIncludeBeforeMatch,$linesToIncludeAfterMatch | Select-Object -Last 10
```

#### <a name="linux"></a>Linux

Azure VM 실행 명령 기능을 사용 하 여 Linux 컴퓨터의 로그 파일에서 정보를 캡처하는 경우 다음 예의 Bash 스크립트를 사용 하는 것이 도움이 될 수 있습니다. 자세한 내용은 [Run 명령을 사용 하 여 LINUX VM에서 셸 스크립트 실행](../../../virtual-machines/linux/run-command.md) 을 참조 하세요.

```Bash
linesToIncludeBeforeMatch=0
linesToIncludeAfterMatch=10
latestVersion=$(find /var/lib/waagent/ -type d -name "Microsoft.GuestConfiguration.ConfigurationforLinux-*" -maxdepth 1 -print | sort -z | sed -n 1p)
egrep -B $linesToIncludeBeforeMatch -A $linesToIncludeAfterMatch 'DSCEngine|DSCManagedEngine' "$latestVersion/GCAgent/logs/dsc.log" | tail
```

## <a name="guest-configuration-samples"></a>게스트 구성 샘플

정책 게스트 구성에 대 한 샘플은 다음 위치에서 사용할 수 있습니다.

- [샘플 인덱스-게스트 구성](../samples/index.md#guest-configuration)
- [Azure Policy GitHub 리포지토리를 샘플](https://github.com/Azure/azure-policy/tree/master/samples/GuestConfiguration)합니다.

## <a name="next-steps"></a>다음 단계

- [Azure Policy 샘플](../samples/index.md)에서 예제를 검토 합니다.
- [Azure Policy 정의 구조](definition-structure.md)를 검토합니다.
- [정책 효과 이해](effects.md)를 검토합니다.
- [프로그래밍 방식으로 정책을 만드는](../how-to/programmatically-create.md)방법을 알아봅니다.
- [준수 데이터를 가져오는](../how-to/getting-compliance-data.md)방법에 대해 알아봅니다.
- [비준수 리소스](../how-to/remediate-resources.md)를 수정 하는 방법에 대해 알아봅니다.
- [Azure 관리 그룹으로 리소스 구성](../../management-groups/index.md)을 포함하는 관리 그룹을 검토합니다.