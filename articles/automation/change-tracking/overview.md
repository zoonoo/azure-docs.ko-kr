---
title: Azure Automation 변경 내용 추적 및 인벤토리 개요
description: 이 문서에서는 사용자 환경에서 소프트웨어 및 Microsoft 서비스 변경 내용을 식별 하는 데 도움이 되는 변경 내용 추적 및 인벤토리 기능을 설명 합니다.
services: automation
ms.subservice: change-inventory-management
ms.date: 10/26/2020
ms.topic: conceptual
ms.openlocfilehash: 35b1b07bd4d9eef555be26e1c24356b7c352bf38
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93100697"
---
# <a name="change-tracking-and-inventory-overview"></a>변경 내용 추적 및 인벤토리 개요

이 문서에서는 Azure Automation의 변경 내용 추적 및 인벤토리 기능을 소개합니다. 이 기능은 Azure, 온-프레미스 및 기타 클라우드 환경에서 호스트 되는 가상 컴퓨터의 변경 내용을 추적 하 여 배포 패키지 관리자에서 관리 하는 소프트웨어의 운영 및 환경 문제를 쉽게 파악할 수 있도록 합니다. 다음은 변경 내용 추적 및 인벤토리에 의해 추적되는 항목입니다.

- Windows 소프트웨어
- Linux 소프트웨어(패키지)
- Windows 및 Linux 파일
- Windows 레지스트리 키
- Microsoft 서비스
- Linux 데몬

> [!NOTE]
> Azure Resource Manager 속성 변경 내용을 추적하려면 Azure Resource Graph [변경 기록](../../governance/resource-graph/how-to/get-resource-changes.md)을 참조하세요.

변경 내용 추적 및 인벤토리는 [AZURE SECURITY CENTER FIM (파일 무결성 모니터링)](../../security-center/security-center-file-integrity-monitoring.md) 을 사용 하 여 운영 체제 및 응용 프로그램 파일과 Windows 레지스트리를 검사 합니다. FIM은 이러한 엔터티, 변경 내용 추적 및 인벤토리를 기본적으로 추적 합니다.

- 소프트웨어 변경 내용
- Microsoft 서비스
- Linux 데몬

변경 내용 추적 및 인벤토리에 포함 된 모든 기능을 사용 하도록 설정 하면 추가 요금이 발생할 수 있습니다. 계속 하기 전에 [Automation 가격](https://azure.microsoft.com/pricing/details/automation/) 책정 및 [Azure Monitor 가격 책정](https://azure.microsoft.com/pricing/details/monitor/)을 검토 하세요. 

변경 내용 추적 및 인벤토리는 데이터를 Azure Monitor 로그에 전달 하 고 수집 된 데이터는 Log Analytics 작업 영역에 저장 됩니다. FIM (파일 무결성 모니터링) 기능은 **서버에 대 한 Azure Defender** 를 사용 하도록 설정한 경우에만 사용할 수 있습니다. 자세히 알아보려면 Azure Security Center [가격 책정](../../security-center/security-center-pricing.md) 을 참조 하세요. FIM은 변경 내용 추적 및 인벤토리의 데이터를 저장 하기 위해 만든 것과 동일한 Log Analytics 작업 영역에 데이터를 업로드 합니다. 연결 된 Log Analytics 작업 영역을 모니터링 하 여 정확한 사용량을 추적 하는 것이 좋습니다. Azure Monitor 로그 데이터 사용량을 분석 하는 방법에 대 한 자세한 내용은 [사용량 및 비용 관리](../../azure-monitor/platform/manage-cost-storage.md)를 참조 하세요.

Log Analytics 작업 영역에 연결 된 컴퓨터는 [Log Analytics 에이전트](../../azure-monitor/platform/log-analytics-agent.md) 를 사용 하 여 모니터링 되는 서버에서 설치 된 소프트웨어, Microsoft 서비스, Windows 레지스트리 및 파일, Linux 디먼의 변경 내용에 대 한 데이터를 수집 합니다. 데이터를 사용할 수 있는 경우 에이전트는 처리를 위해 Azure Monitor 로그에 데이터를 보냅니다. Azure Monitor 로그는 수신 된 데이터에 논리를 적용 하 고 기록 하 여 분석에 사용할 수 있도록 합니다.

> [!NOTE]
> 변경 내용 추적 및 인벤토리를 사용 하려면 Log Analytics 작업 영역을 Automation 계정에 연결 해야 합니다. 지원되는 지역 목록은 [Azure 작업 영역 매핑](../how-to/region-mappings.md)을 참조하세요. 지역 매핑은 Automation 계정과 별도의 지역에 있는 VM을 관리하는 기능에 영향을 주지 않습니다.

## <a name="current-limitations"></a>현재 제한 사항

변경 내용 추적 및 인벤토리는를 지원 하지 않거나 다음과 같은 제한 사항이 있습니다.

- Windows 레지스트리 추적을 위한 재귀
- 네트워크 파일 시스템
- 다른 설치 방법
- *Windows에 저장 된 *_.exe_* 파일
- **최대 파일 크기** 열과 값은 현재 구현에서 사용되지 않습니다.
- 30 분 컬렉션 주기에서 2500 개 이상의 파일을 수집 하려고 하면 변경 내용 추적 및 재고 성능이 저하 될 수 있습니다.
- 네트워크 트래픽이 높으면 변경 레코드를 표시 하는 데 최대 6 시간이 걸릴 수 있습니다.
- 컴퓨터 또는 서버를 종료 하는 동안 구성을 수정 하는 경우 이전 구성에 속하는 변경 내용을 게시할 수 있습니다.
- Windows Server 2016 Core RS3 컴퓨터에서 핫픽스 업데이트를 수집 하는 중입니다.
- 변경 내용이 없더라도 Linux 디먼에서 변경됨 상태를 표시할 수 있습니다. 이 문제는 `SvcRunLevels` Azure Monitor [configurationchange](/azure/azure-monitor/reference/tables/configurationchange) 테이블의 데이터가 기록 되는 방식 때문에 발생 합니다.

## <a name="supported-operating-systems"></a>지원되는 운영 체제

변경 내용 추적 및 인벤토리는 Log Analytics 에이전트 요구 사항을 충족하는 모든 운영 체제에서 지원됩니다. 현재 Log Analytics 에이전트에서 지 원하는 Windows 및 Linux 운영 체제 버전 목록은 [지원 되는 운영 체제](../../azure-monitor/platform/agents-overview.md#supported-operating-systems) 를 참조 하세요.

TLS 1.2에 대 한 클라이언트 요구 사항을 이해 하려면 [Azure Automation에 대 한 tls 1.2 적용](../automation-managing-data.md#tls-12-enforcement-for-azure-automation)을 참조 하세요.

## <a name="network-requirements"></a>네트워크 요구 사항

다음 주소는 변경 내용 추적 및 인벤토리에 특히 필요 합니다. 이러한 주소에 대한 통신은 443 포트를 통해 발생합니다.

|Azure 공용  |Azure Government  |
|---------|---------|
|*.ods.opinsights.azure.com    | *.ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *.oms.opinsights.azure.us        |
|\*.blob.core.windows.net | *.blob.core.usgovcloudapi.net|
|\* .azure-automation.net | *.azure-automation.us|

Automation 서비스 및 Log Analytics 작업 영역에 대 한 트래픽을 허용 하도록 네트워크 그룹 보안 규칙을 만들거나 Azure 방화벽을 구성 하는 경우 [서비스 태그](../../virtual-network/service-tags-overview.md#available-service-tags) **GuestAndHybridManagement** 및 **azuremonitor** 를 사용 합니다. 네트워크 보안 규칙의 지속적인 관리를 간소화 합니다. Azure Vm에서 자동화 서비스에 안전 하 고 개인적으로 연결 하려면 [Azure 개인 링크 사용](../how-to/private-link-security.md)을 검토 하세요. 온-프레미스 방화벽 구성의 일부로 포함할 현재 서비스 태그 및 범위 정보를 가져오려면 [다운로드 가능한 JSON 파일](../../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files)을 참조 하세요.

## <a name="enable-change-tracking-and-inventory"></a>변경 내용 추적 및 인벤토리 사용

다음과 같은 방법으로 변경 내용 추적 및 인벤토리를 사용 하도록 설정할 수 있습니다.

- 하나 이상의 Azure 및 비 Azure 컴퓨터에 대 한 [Automation 계정](enable-from-automation-account.md) 에서.

- [Azure Arc 사용 서버](../../azure-arc/servers/overview.md)에 등록 된 컴퓨터 또는 서버를 포함 하 여 비 azure 컴퓨터의 경우 수동으로. 하이브리드 컴퓨터의 경우 먼저 컴퓨터를 [Azure Arc 사용 서버](../../azure-arc/servers/overview.md)에 연결한 다음 Azure Policy를 사용 하 여 [ *Linux* 또는 *Windows* Azure arc 컴퓨터](../../governance/policy/samples/built-in-policies.md#monitoring) 기본 제공 정책에 Log Analytics 에이전트 배포를 할당 하 여 Windows 용 Log Analytics 에이전트를 설치 하는 것이 좋습니다. VM용 Azure Monitor를 사용 하 여 컴퓨터를 모니터링 하려는 경우에는 대신 [VM용 Azure Monitor](../../governance/policy/samples/built-in-initiatives.md#monitoring) 이니셔티브 사용을 사용 합니다.

- Azure Portal의 [가상 컴퓨터 페이지](enable-from-vm.md) 에서 단일 Azure VM의 경우 이 시나리오는 Linux VM과 Windows VM에서 지원됩니다.

- [여러 Azure VM](enable-from-portal.md)의 경우 Azure Portal의 가상 머신 페이지에서 해당 VM을 선택.

## <a name="tracking-file-changes"></a>파일 변경 내용 추적

Windows 및 Linux 모두에서 파일의 변경 내용을 추적하기 위해 변경 내용 추적 및 인벤토리는 파일의 MD5 해시를 사용합니다. 이 기능은 해시를 사용하여 마지막 인벤토리 이후 변경 내용이 있는지 탐지합니다.

## <a name="tracking-file-content-changes"></a>파일 콘텐츠 변경 내용 추적

변경 내용 추적 및 인벤토리를 사용 하면 Windows 또는 Linux 파일의 내용을 볼 수 있습니다. 파일의 각 변경 내용에 대해 변경 내용 추적 및 인벤토리는 [Azure Storage 계정](/azure/storage/common/storage-account-create)에 파일 내용을 저장합니다. 파일을 추적 하는 경우 변경 전후에 해당 내용을 볼 수 있습니다. 파일 콘텐츠는 인라인 또는 side-by-side로 볼 수 있습니다.

![파일의 변경 내용 보기](./media/overview/view-file-changes.png)

## <a name="tracking-of-registry-keys"></a>레지스트리 키 추적

변경 내용 추적 및 인벤토리를 사용 하 여 Windows 레지스트리 키에 대 한 변경 내용을 모니터링할 수 있습니다. 모니터링을 통해 타사 코드 및 맬웨어가 활성화될 수 있는 확장성 지점을 정확하게 찾을 수 있습니다. 다음 표에는 미리 구성되어 있지만 사용 설정되지 않은 레지스트리 키가 나열되어 있습니다. 이러한 키를 추적하려면 각 키를 사용하도록 설정해야 합니다.

> [!div class="mx-tdBreakAll"]
> |레지스트리 키 | 목적 |
> | --- | --- |
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Group Policy\Scripts\Startup` | 시작 시 실행되는 스크립트를 모니터링합니다.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Group Policy\Scripts\Shutdown` | 종료 시 실행되는 스크립트를 모니터링합니다.
> |`HKEY\LOCAL\MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Run` | 사용자가 Windows 계정에 로그인하기 전에 로드되는 키를 모니터링합니다. 키는 64비트 컴퓨터에서 실행되는 32비트 애플리케이션에 사용됩니다.
> |`HKEY\LOCAL\MACHINE\SOFTWARE\Microsoft\Active Setup\Installed Components` | 애플리케이션 설정의 변경 내용을 모니터링합니다.
> |`HKEY\LOCAL\MACHINE\Software\Classes\Directory\ShellEx\ContextMenuHandlers` | Windows 탐색기에 직접 연결 되 고 일반적으로 **explorer.exe** 를 사용 하 여 in-process로 실행 되는 상황에 맞는 메뉴 처리기를 모니터링 합니다.
> |`HKEY\LOCAL\MACHINE\Software\Classes\Directory\Shellex\CopyHookHandlers` | Windows 탐색기에 직접 연결 되 고 일반적으로 **explorer.exe** 를 사용 하 여 in-process로 실행 되는 복사 후크 처리기를 모니터링 합니다.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Explorer\ShellIconOverlayIdentifiers` | 아이콘 오버레이 처리기 등록을 모니터링합니다.
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\ShellIconOverlayIdentifiers` | 64비트 컴퓨터에서 실행되는 32비트 애플리케이션에 대한 아이콘 오버레이 처리기 등록을 모니터링합니다.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Explorer\Browser Helper Objects` | Internet Explorer에 대한 새 브라우저 도우미 개체 플러그 인을 모니터링합니다. 현재 페이지의 DOM(문서 개체 모델)에 액세스하고 탐색을 제어하는 데 사용됩니다.
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\Browser Helper Objects` | Internet Explorer에 대한 새 브라우저 도우미 개체 플러그 인을 모니터링합니다. 64비트 컴퓨터에서 실행되는 32비트 애플리케이션에 대해 현재 페이지의 DOM(문서 개체 모델)에 액세스하고 탐색을 제어하는 데 사용됩니다.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Internet Explorer\Extensions` | 사용자 지정 도구 메뉴 및 사용자 지정 도구 모음 단추와 같은 새로운 Internet Explorer 확장을 모니터링합니다.
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Internet Explorer\Extensions` | 64비트 컴퓨터에서 실행되는 32비트 애플리케이션에 대한 사용자 지정 도구 메뉴 및 사용자 지정 도구 모음 단추와 같은 새 Internet Explorer 확장을 모니터링합니다.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows NT\CurrentVersion\Drivers32` | wavemapper, wave1 및 wave2, msacm.imaadpcm, .msadpcm, .msgsm610 및 vidc와 관련된 32비트 드라이버를 모니터링합니다. **system.ini** 파일의 [drivers] 섹션과 비슷합니다.
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Windows NT\CurrentVersion\Drivers32` | 64비트 컴퓨터에서 실행되는 32비트 애플리케이션에 대한 wavemapper, wave1 및 wave2, msacm.imaadpcm, .msadpcm, .msgsm610 및 vidc와 관련된 32비트 드라이버를 모니터링합니다. **system.ini** 파일의 [drivers] 섹션과 비슷합니다.
> |`HKEY\LOCAL\MACHINE\System\CurrentControlSet\Control\Session Manager\KnownDlls` | 알려진 또는 일반적으로 사용되는 시스템 DLL 목록을 모니터링합니다. 모니터링을 통해 사용자는 트로이 목마 버전의 시스템 Dll을 삭제 하 여 취약 한 응용 프로그램 디렉터리 권한을 악용할 수 없습니다.
> |`HKEY\LOCAL\MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon\Notify` | Windows에 대한 대화형 로그온 지원 모델인 **winlogon.exe** 에서 이벤트 알림을 받을 수 있는 패키지 목록을 모니터링합니다.

## <a name="recursion-support"></a>재귀 지원

변경 내용 추적 및 인벤토리는 재귀를 지원하므로 디렉터리 간 추적을 단순화하기 위해 와일드카드를 지정할 수 있습니다. 또한 재귀는 여러 또는 동적 드라이브 이름의 환경에서 파일을 추적할 수 있도록 환경 변수를 제공합니다. 다음 목록에는 재귀를 구성할 때 알아야 하는 일반적인 정보가 포함 되어 있습니다.

- 와일드 카드는 여러 파일을 추적하는 데 필요합니다.

- 파일 경로의 마지막 세그먼트에만 와일드 카드를 사용할 수 있습니다 (예: **: c:\folder \\ file** _ 또는 _ */etc/* * *).

- 환경 변수에 잘못된 경로가 있는 경우 유효성 검사는 성공 하지만 실행 중 경로가 실패합니다.

- 경로를 설정할 때 일반적인 경로 이름을 사용 하지 않아야 합니다 .이 유형의 설정으로 인해 너무 많은 폴더가 트래버스 될 수 있습니다.

## <a name="change-tracking-and-inventory-data-collection"></a>변경 내용 추적 및 인벤토리 데이터 수집

다음 표에서는 변경 내용 추적 및 인벤토리에서 지원하는 변경 유형에 대한 데이터 수집 빈도를 보여줍니다. 모든 유형에 대해 현재 상태의 데이터 스냅샷은 최소한 24시간마다 새로 고쳐집니다.

| **변경 유형** | **빈도** |
| --- | --- |
| Windows 레지스트리 | 50분 |
| Windows 파일 | 30분 |
| Linux 파일 | 15분 |
| Microsoft 서비스 | 10초에서 30분</br> Default: 30분 |
| Linux 데몬 | 5분 |
| Windows 소프트웨어 | 30분 |
| Linux 소프트웨어 | 5분 |

다음 표에서는 변경 내용 추적 및 인벤토리에 대한 머신당 추적된 항목 한도를 보여줍니다.

| **리소스** | **제한** |
|---|---|---|
|파일|500|
|레지스트리|250|
|Windows 소프트웨어(핫픽스 포함 안 함) |250|
|Linux 패키지|1250|
|Services|250|
|디먼|250|

변경 내용 추적 및 인벤토리를 사용하는 머신의 평균 Log Analytics 데이터 사용량은 환경에 따라 월별 약 40MB입니다. Log Analytics 작업 영역의 사용량 및 예상 비용 기능을 사용 하면 사용 현황 차트에서 변경 내용 추적 및 인벤토리에 따라 데이터 수집을 볼 수 있습니다. 이 데이터 뷰를 사용 하 여 데이터 사용량을 평가 하 고 청구에 미치는 영향을 결정 합니다. [사용량 파악 및 비용 추정](/azure-monitor/platform/manage-cost-storage#understand-your-usage-and-estimate-costs)을 참조하세요.

### <a name="microsoft-service-data"></a>Microsoft 서비스 데이터

Microsoft 서비스에 대한 기본 수집 빈도는 30분입니다. **Microsoft 서비스** 탭의 **설정 편집** 아래에서 슬라이더를 사용하여 빈도를 구성할 수 있습니다.

![Microsoft 서비스 슬라이더](./media/overview/windowservices.png)

성능 최적화를 위해 Log Analytics 에이전트는 변경 내용만 추적 합니다. 서비스가 원래 상태로 반환 되는 경우 높은 임계값을 설정 하면 변경 내용이 누락 될 수 있습니다. 빈도를 더 작은 값으로 설정하면 누락될 수 있는 변경 내용을 파악할 수 있습니다.

> [!NOTE]
> 에이전트가 10초 간격으로 변경 내용을 추적하는 동안 데이터를 Microsoft Azure Portal에 표시하는 데 몇 분 정도 걸립니다. 포털에 표시하는 시간 동안에도 변경 내용이 추적되고 기록됩니다.

## <a name="support-for-alerts-on-configuration-state"></a>구성 상태에 대한 경고 지원

변경 내용 추적 및 인벤토리의 주요 기능은 하이브리드 환경의 구성 상태 변경 내용에 대해 경고를 표시하는 것입니다. 많은 유용한 작업을 경고에 대 한 응답으로 트리거할 수 있습니다. 예를 들어 Azure 함수, 자동화 runbook, 웹 후크 및 이와 같은 작업을 수행 합니다. 컴퓨터에 대 한 **c:\windows\system32\drivers\etc\hosts** 파일 변경에 대 한 경고는 변경 내용 추적 및 인벤토리 데이터에 대 한 경고의 좋은 적용입니다. 다음 표에 정의된 쿼리 시나리오를 비롯하여 경고에 대한 더 많은 시나리오가 있습니다.

|쿼리  |Description  |
|---------|---------|
|ConfigurationChange <br>&#124; where ConfigChangeType == "Files" and FileSystemPath contains " c:\\windows\\system32\\drivers\\"|시스템에 중요한 파일의 변경 내용을 추적하는 데 유용합니다.|
|ConfigurationChange <br>&#124; where FieldsChanged contains "FileContentChecksum" and FileSystemPath == "c:\\windows\\system32\\drivers\\etc\\hosts"|주요 구성 파일의 수정 내용을 추적하는 데 유용합니다.|
|ConfigurationChange <br>&#124; where ConfigChangeType == "WindowsServices" and SvcName contains "w3svc" and SvcState == "Stopped"|시스템에 중요한 서비스의 변경 내용을 추적하는 데 유용합니다.|
|ConfigurationChange <br>&#124; where ConfigChangeType == "Daemons" and SvcName contains "ssh" and SvcState!= "Running"|시스템에 중요한 서비스의 변경 내용을 추적하는 데 유용합니다.|
|ConfigurationChange <br>&#124; where ConfigChangeType == "Software" and ChangeCategory == "Added"|잠긴 소프트웨어 구성이 필요한 환경에 유용합니다.|
|ConfigurationData <br>&#124; where SoftwareName contains "Monitoring Agent" and CurrentVersion!= "8.0.11081.0"|오래되었거나 규정을 준수하지 않는 소프트웨어 버전이 설치된 머신을 확인하는 데 유용합니다. 이 쿼리는 변경 내용이 아닌 마지막으로 보고된 구성 상태를 보고합니다.|
|ConfigurationChange <br>&#124; where RegistryKey == @"HKEY_LOCAL_MACHINE\\SOFTWARE\\Microsoft\\Windows\\CurrentVersion\\QualityCompat"| 중요한 바이러스 백신 키의 변경 내용을 추적하는 데 유용합니다.|
|ConfigurationChange <br>&#124; where RegistryKey contains @"HKEY_LOCAL_MACHINE\\SYSTEM\\CurrentControlSet\\Services\\SharedAccess\\Parameters\\FirewallPolicy"| 방화벽 설정의 변경 내용을 추적하는 데 유용합니다.|

## <a name="next-steps"></a>다음 단계

- Automation 계정에서를 사용 하도록 설정 하려면 [automation 계정에서 변경 내용 추적 및 인벤토리 사용](enable-from-automation-account.md)을 참조 하세요.

- Azure Portal에서 사용 하도록 설정 하려면 [Azure Portal에서 변경 내용 추적 및 인벤토리 사용](enable-from-portal.md)을 참조 하세요.

- Runbook에서를 사용 하도록 설정 하려면 [runbook에서 변경 내용 추적 및 인벤토리 사용](enable-from-runbook.md)을 참조 하세요.

- Azure VM에서를 사용 하도록 설정 하려면 [AZURE vm에서 변경 내용 추적 및 인벤토리 사용](enable-from-vm.md)을 참조 하세요.
