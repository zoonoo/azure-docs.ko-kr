---
title: 변경 내용 추적 및 인벤토리 개요 Azure Automation
description: 변경 내용 추적 및 인벤토리를 사용 하면 사용자 환경에서 발생 하는 소프트웨어 및 Microsoft 서비스 변경 내용을 식별할 수 있습니다.
services: automation
ms.subservice: change-inventory-management
ms.date: 01/28/2019
ms.topic: conceptual
ms.openlocfilehash: 1208e08f7b85e893ba754bdbdf71a2da4f68c90a
ms.sourcegitcommit: eaec2e7482fc05f0cac8597665bfceb94f7e390f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/29/2020
ms.locfileid: "82509071"
---
# <a name="overview-of-change-tracking-and-inventory"></a>변경 내용 추적 및 인벤토리 개요

이 문서에서는 Azure Automation의 변경 내용 추적 및 인벤토리를 소개 합니다. 이 기능은 배포 패키지 관리자에서 관리 하는 소프트웨어의 운영 및 환경 문제를 쉽게 파악할 수 있도록 가상 컴퓨터 및 서버 인프라의 변경 내용을 추적 합니다. 변경 내용 추적 및 인벤토리에 의해 추적 되는 항목은 다음과 같습니다. 

- Windows 소프트웨어
- Linux 소프트웨어 (패키지)
- Windows 및 Linux 파일
- Windows 레지스트리 키
- Microsoft 서비스
- Linux 데몬

변경 내용 추적 및 인벤토리는 클라우드의 Azure Monitor 서비스에서 데이터를 가져옵니다. Azure는 모니터링 되는 서버에서 설치 된 소프트웨어, Microsoft 서비스, Windows 레지스트리 및 파일 및 Linux 디먼에 대 한 변경 내용을 처리를 위해 Azure Monitor 보냅니다. 클라우드 서비스는 수신 된 데이터에 논리를 적용 하 고 기록 하 여 사용할 수 있도록 합니다. 

> [!NOTE]
> Azure Resource Manager 속성 변경 내용을 추적 하려면 Azure 리소스 그래프 [변경 기록](../governance/resource-graph/how-to/get-resource-changes.md)을 참조 하세요.

현재 변경 내용 추적 및 인벤토리는 다음 항목을 지원 하지 않습니다.

* Windows 레지스트리 추적을 위한 재귀
* 네트워크 파일 시스템
* 다른 설치 방법
* *Windows 용 **.exe** 파일

기타 제한 사항은 다음과 같습니다.

* **최대 파일 크기** 열과 값은 현재 구현에서 사용되지 않습니다.
* 30 분 컬렉션 주기에서 2500 개 이상의 파일을 수집 하는 경우 솔루션 성능이 저하 될 수 있습니다.
* 네트워크 트래픽이 높으면 변경 레코드를 표시 하는 데 최대 6 시간이 걸릴 수 있습니다.
* 컴퓨터를 종료 하는 동안 구성을 수정 하는 경우 컴퓨터에서 이전 구성에 속하는 변경 내용을 게시할 수 있습니다.

현재 변경 내용 추적 및 인벤토리에는 다음과 같은 문제가 발생 합니다.

* Windows Server 2016 Core RS3 컴퓨터에서 핫픽스 업데이트가 수집되지 않습니다.
* 변경이 발생 하지 않았더라도 Linux 디먼에서 변경 된 상태를 표시할 수 있습니다. 이 문제는 Azure Monitor [Configurationchange](https://docs.microsoft.com/azure/azure-monitor/reference/tables/configurationchange) 로그의 `SvcRunLevels` 데이터를 캡처하는 방법 때문에 발생 합니다.

## <a name="supported-operating-systems"></a>지원되는 운영 체제

변경 내용 추적 및 인벤토리 및 Azure Monitor Log Analytics 에이전트는 Windows 및 Linux 운영 체제에서 지원 됩니다.

### <a name="windows-operating-systems"></a>Windows 운영 체제

공식적으로 지원 되는 Windows 운영 체제 버전은 Windows Server 2008 R2 이상 버전입니다.

### <a name="linux-operating-systems"></a>Linux 운영 체제

아래에서 설명 하는 Linux 배포판은 Linux 용 Log Analytics 에이전트에 공식적으로 지원 됩니다. 하지만 Linux 에이전트는 나열되지 않은 그 밖의 배포에서 실행이 가능할 수 있습니다. 다른 설명이 없는 한, 나열된 각 주 버전의 모든 부 버전이 지원됩니다.

#### <a name="64-bit-linux-operating-systems"></a>64 비트 Linux 운영 체제

* CentOS 6 및 7
* Amazon Linux 2017.09
* Oracle Linux 6 및 7
* Red Hat Enterprise Linux Server 6 및 7
* Debian GNU/Linux 8 및 9
* Ubuntu Linux 14.04 LTS, 16.04 LTS, 18.04 LTS
* SUSE Linux Enterprise Server 12

#### <a name="32-bit-linux-operating-systems"></a>32 비트 Linux 운영 체제

* CentOS 6
* Oracle Linux 6
* Red Hat Enterprise Linux Server 6
* Debian GNU/Linux 8 및 9
* Ubuntu Linux 14.04 LTS 및 16.04 LTS

## <a name="network-requirements"></a>네트워크 요구 사항

특히 변경 내용 추적 및 인벤토리에는 다음 표에 나열 된 네트워크 주소가 필요 합니다. 이러한 주소에 대 한 통신은 포트 443를 사용 합니다.

|Azure 공용  |Azure Government  |
|---------|---------|
|*.ods.opinsights.azure.com     |*.ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *.oms.opinsights.azure.us        |
|\*.blob.core.windows.net|*.blob.core.usgovcloudapi.net|
|* .azure-automation.net|*.azure-automation.us|

## <a name="change-tracking-and-inventory-user-interface"></a>변경 내용 추적 및 인벤토리 사용자 인터페이스

Azure Portal에서 변경 내용 추적 및 인벤토리를 사용 하 여 모니터링 되는 컴퓨터의 변경 내용에 대 한 요약을 볼 수 있습니다. Automation 계정의 **구성 관리** 아래에서 **변경 내용 추적** 을 선택 하 여이 기능을 사용할 수 있습니다. 

![변경 내용 추적 대시보드](./media/change-tracking/change-tracking-dash01.png)

드롭다운은 변경 유형 및 시간 범위에 따라 변경 내용 추적 차트 및 자세한 정보를 제한 하기 위해 대시보드의 맨 위에서 사용할 수 있습니다. 또한 차트를 클릭하고 끌어서 사용자 지정 시간 범위를 선택할 수도 있습니다. 

변경 또는 이벤트를 클릭 하 여 세부 정보를 표시할 수 있습니다. 사용 가능한 변경 유형은 다음과 같습니다.

* 이벤트
* 디먼
* 파일
* 레지스트리
* 소프트웨어
* Microsoft 서비스

각 변경 내용을 추가, 수정 또는 제거할 수 있습니다. 아래 예제에서는 서비스의 시작 유형이 수동에서 자동으로 변경 되는 것을 볼 수 있습니다.

![변경 내용 추적 세부 정보](./media/change-tracking/change-tracking-details.png)

## <a name="tracking-of-file-changes"></a>파일 변경 내용 추적

Windows와 Linux 모두에서 파일의 변경 내용을 추적 하기 위해 변경 내용 추적 및 인벤토리는 파일의 MD5 해시를 사용 합니다. 이 기능은 해시를 사용 하 여 마지막 인벤토리 이후 변경 된 내용을 검색 합니다.

## <a name="tracking-of-file-content-changes"></a>파일 콘텐츠 변경 내용 추적

변경 내용 추적 및 인벤토리를 사용 하면 파일 변경 전후에 Windows 또는 Linux 파일의 내용을 볼 수 있습니다. 파일에 대 한 각 변경 내용에 대해 변경 내용 추적 및 인벤토리는 [Azure Storage 계정](../storage/common/storage-create-storage-account.md)에 파일 내용을 저장 합니다. 파일을 추적 하는 경우 변경 전후에 해당 내용을 볼 수 있습니다. 인라인 또는 side-by-side로 콘텐츠를 볼 수 있습니다. 

![파일의 변경 내용 보기](./media/change-tracking/view-file-changes.png)

## <a name="tracking-of-registry-keys"></a>레지스트리 키 추적

변경 내용 추적 및 인벤토리를 사용 하 여 레지스트리 키에 대 한 변경 내용을 모니터링할 수 있습니다. 모니터링을 통해 타사 코드와 맬웨어가 활성화 될 수 있는 확장성 지점을 파악할 수 있습니다. 다음 표에서는 미리 구성 되어 있지만 사용할 수 없는 레지스트리 키를 나열 합니다. 이러한 키를 추적 하려면 각 키를 사용 하도록 설정 해야 합니다.

> [!div class="mx-tdBreakAll"]
> |레지스트리 키 | 목적 |
> | --- | --- |
> |`HKEY\LOCAL\MACHINE\Software\Classes\Directory\ShellEx\ContextMenuHandlers` | Windows 탐색기에 직접 연결 되 고 일반적 **으로 explorer.exe를 사용 하**여 in-process로 실행 되는 일반적인 자동 시작 항목을 모니터링 합니다.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Group Policy\Scripts\Startup` | 시작 시 실행되는 스크립트를 모니터링합니다.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Group Policy\Scripts\Shutdown` | 종료 시 실행되는 스크립트를 모니터링합니다.
> |`HKEY\LOCAL\MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Run` | 사용자가 Windows 계정에 로그인 하기 전에 로드 되는 키를 모니터링 합니다. 키는 64 비트 컴퓨터에서 실행 되는 32 비트 응용 프로그램에 사용 됩니다.
> |`HKEY\LOCAL\MACHINE\SOFTWARE\Microsoft\Active Setup\Installed Components` | 애플리케이션 설정의 변경 내용을 모니터링합니다.
> |`HKEY\LOCAL\MACHINE\Software\Classes\Directory\ShellEx\ContextMenuHandlers` | Windows 탐색기에 직접 연결 되 고 일반적 **으로 explorer.exe를 사용 하**여 in-process로 실행 되는 일반적인 자동 시작 항목을 모니터링 합니다.
> |`HKEY\LOCAL\MACHINE\Software\Classes\Directory\Shellex\CopyHookHandlers` | Windows 탐색기에 직접 연결 되 고 일반적 **으로 explorer.exe를 사용 하**여 in-process로 실행 되는 일반적인 자동 시작 항목을 모니터링 합니다.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Explorer\ShellIconOverlayIdentifiers` | 아이콘 오버레이 처리기 등록을 모니터링합니다.
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\ShellIconOverlayIdentifiers` | 64 비트 컴퓨터에서 실행 되는 32 비트 응용 프로그램에 대 한 아이콘 오버레이 처리기 등록을 모니터링 합니다.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Explorer\Browser Helper Objects` | Internet Explorer에 대한 새 브라우저 도우미 개체 플러그 인을 모니터링합니다. 현재 페이지의 DOM(문서 개체 모델)에 액세스하고 탐색을 제어하는 데 사용됩니다.
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\Browser Helper Objects` | Internet Explorer에 대한 새 브라우저 도우미 개체 플러그 인을 모니터링합니다. 현재 페이지의 DOM (문서 개체 모델)에 액세스 하 고 64 비트 컴퓨터에서 실행 되는 32 비트 응용 프로그램에 대 한 탐색을 제어 하는 데 사용 됩니다.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Internet Explorer\Extensions` | 사용자 지정 도구 메뉴 및 사용자 지정 도구 모음 단추와 같은 새로운 Internet Explorer 확장을 모니터링합니다.
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Internet Explorer\Extensions` | 64 비트 컴퓨터에서 실행 되는 32 비트 응용 프로그램에 대 한 사용자 지정 도구 메뉴 및 사용자 지정 도구 모음 단추와 같은 새 Internet Explorer 확장을 모니터링 합니다.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows NT\CurrentVersion\Drivers32` | Wavemapper, wave1 및 wave2, msacm.imaadpcm, msgsm610 및 vidc와 연결 된 32 비트 드라이버를 모니터링 합니다. **System.ini** 파일의 [drivers] 섹션과 유사 합니다.
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Windows NT\CurrentVersion\Drivers32` | 64 비트 컴퓨터에서 실행 되는 32 비트 응용 프로그램에 대 한 wavemapper, wave1 및 wave2, msacm.imaadpcm, msgsm610 및 vidc와 연결 된 32 비트 드라이버를 모니터링 합니다. **System.ini** 파일의 [drivers] 섹션과 유사 합니다.
> |`HKEY\LOCAL\MACHINE\System\CurrentControlSet\Control\Session Manager\KnownDlls` | 알려진 또는 일반적으로 사용 되는 시스템 Dll 목록을 모니터링 합니다. 이 시스템은 사용자가 트로이 목마 버전의 시스템 Dll을 삭제 하 여 취약 한 응용 프로그램 디렉터리 권한을 악용 하지 못하게 합니다.
> |`HKEY\LOCAL\MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon\Notify` | Windows에 대 한 대화형 로그온 지원 모델인 **winlogon**에서 이벤트 알림을 받을 수 있는 패키지 목록을 모니터링 합니다.

## <a name="support-for-file-integrity-monitoring-in-azure-security-center"></a>Azure Security Center에서 파일 무결성 모니터링 지원

변경 내용 추적 및 인벤토리는 [AZURE SECURITY CENTER FIM (파일 무결성 모니터링)](https://docs.microsoft.com/azure/security-center/security-center-file-integrity-monitoring)을 사용 합니다. FIM은 파일 및 레지스트리만 모니터링 하지만 전체 변경 내용 추적 및 인벤토리 기능에는 다음에 대 한 추적도 포함 됩니다.

- 소프트웨어 변경
- Microsoft 서비스
- Linux 데몬

## <a name="recursion-support"></a>재귀 지원

변경 내용 추적 및 인벤토리는 재귀를 지원 하 여 디렉터리에서 추적을 간소화 하는 와일드 카드를 지정할 수 있습니다. 또한 재귀는 여러 또는 동적 드라이브 이름이 있는 환경에서 파일을 추적할 수 있는 환경 변수를 제공 합니다. 다음 목록에는 재귀를 구성할 때 알아야 하는 일반적인 정보가 포함 되어 있습니다.

* 와일드 카드는 여러 파일을 추적하는 데 필요합니다.
* 와일드 카드는 경로의 마지막 세그먼트 (예: **: c:\folder\\file*** 또는 **/etc/*.**) 에서만 사용할 수 있습니다.
* 환경 변수에 잘못 된 경로가 있는 경우 유효성 검사가 성공 하지만 실행 중에 경로가 실패 합니다.
* 경로를 설정할 때 일반 경로 이름을 사용 하지 마십시오 .이 설정으로 인해 너무 많은 폴더가 트래버스 될 수 있습니다.

## <a name="change-tracking-and-inventory-data-collection"></a>변경 내용 추적 및 인벤토리 데이터 수집

다음 표에서는 변경 내용 추적 및 인벤토리에 의해 지원 되는 변경 유형에 대 한 데이터 수집 빈도를 보여 줍니다. 모든 형식에 대해 적어도 24 시간 마다 현재 상태의 데이터 스냅숏이 새로 고쳐집니다.

| **형식 변경** | **빈도** |
| --- | --- |
| Windows 레지스트리 | 50분 |
| Windows 파일 | 30분 |
| Linux 파일 | 15분 |
| Microsoft 서비스 | 10초에서 30분</br> 기본값: 30분 |
| Linux 데몬 | 5분 |
| Windows 소프트웨어 | 30분 |
| Linux 소프트웨어 | 5분 |

다음 표에서는 변경 내용 추적 및 인벤토리의 컴퓨터 당 추적 된 항목 제한을 보여 줍니다.

| **리소스** | **제한** |
|---|---|---|
|파일|500|
|레지스트리|250|
|Windows 소프트웨어 (핫픽스 포함 안 함) |250|
|Linux 패키지|1250|
|Services|250|
|디먼|250|

변경 내용 추적 및 인벤토리를 사용 하는 컴퓨터의 평균 Log Analytics 데이터 사용량은 매월 약 40 MB입니다. 이 값은 근사값이며, 사용자 환경에 따라 변경될 수 있습니다. 사용자 환경을 모니터링하여 정확한 사용량을 확인하는 것이 좋습니다.

### <a name="microsoft-service-data"></a>Microsoft 서비스 데이터

Microsoft 서비스의 기본 수집 빈도는 30 분입니다. **설정 편집**의 **Microsoft 서비스** 탭에서 슬라이더를 사용 하 여 빈도를 구성할 수 있습니다. 

![Microsoft 서비스 슬라이더](./media/change-tracking/windowservices.png)

성능을 최적화 하기 위해 Log Analytics 에이전트는 변경 내용만 추적 합니다. 서비스를 원래 상태로 되돌리는 경우 높은 임계값을 설정 하면 변경 내용이 누락 될 수 있습니다. 빈도를 더 작은 값으로 설정 하면 누락 될 수 있는 변경 내용을 catch 할 수 있습니다.

> [!NOTE]
> 에이전트가 10 초 간격으로 변경 내용을 추적 하는 동안에도 데이터를 Azure Portal 표시 하는 데 몇 분 정도 걸립니다. 포털에 표시 되는 시간 동안 발생 하는 변경 내용은 계속 추적 되 고 기록 됩니다.

## <a name="support-for-alerts-on-configuration-state"></a>구성 상태에 대 한 경고 지원

하이브리드 환경의 구성 상태에 대 한 변경 내용에 대 한 경고를 변경 내용 추적 및 인벤토리의 핵심 기능입니다. 경고에 대 한 응답으로 (예: Azure 함수, 자동화 runbook, 웹 후크 등에 대 한 작업) 여러 가지 유용한 작업을 트리거할 수 있습니다. 컴퓨터에 대 한 **C:\windows\system32\drivers\etc\hosts** 파일 변경에 대 한 경고는 변경 내용 추적 및 인벤토리 데이터에 대 한 경고의 좋은 적용입니다. 다음 표에 정의 된 쿼리 시나리오를 비롯 하 여 경고에 대 한 더 많은 시나리오가 있습니다. 

|쿼리  |Description  |
|---------|---------|
|ConfigurationChange <br>&#124; where ConfigChangeType == "Files" and FileSystemPath contains " c:\\windows\\system32\\drivers\\"|시스템에 중요 한 파일에 대 한 변경 내용을 추적 하는 데 유용 합니다.|
|ConfigurationChange <br>&#124; where FieldsChanged contains "FileContentChecksum" and FileSystemPath == "c:\\windows\\system32\\drivers\\etc\\hosts"|키 구성 파일에 대 한 수정 내용을 추적 하는 데 유용 합니다.|
|ConfigurationChange <br>ConfigChangeType = = "Microsoft services" 및 SvcName에 "w3svc" 및 = = "중지 됨"이 포함 된 &#124;|시스템에 중요 한 서비스에 대 한 변경 내용을 추적 하는 데 유용 합니다.|
|ConfigurationChange <br>ConfigChangeType = = "" 및 SvcName에 "ssh" 및 SvcState! = "Running"이 포함 되어 있는 &#124;|시스템에 중요 한 서비스에 대 한 변경 내용을 추적 하는 데 유용 합니다.|
|ConfigurationChange <br>&#124; where ConfigChangeType == "Software" and ChangeCategory == "Added"|잠긴 소프트웨어 구성이 필요한 환경에 유용 합니다.|
|ConfigurationData <br>SoftwareName에 "Monitoring Agent" 및 CurrentVersion! = "8.0.11081.0"가 포함 된 &#124;|이전 또는 비규격 소프트웨어 버전이 설치 된 컴퓨터를 보는 데 유용 합니다. 이 쿼리는 마지막으로 보고 된 구성 상태를 보고 하지만 변경 내용을 보고 하지 않습니다.|
|ConfigurationChange <br>&#124;에서 RegistryKey = = @ "HKEY_LOCAL_MACHINE\\SOFTWARE\\Microsoft\\Windows\\CurrentVersion\\QualityCompat"| 중요 한 바이러스 백신 키의 변경 내용을 추적 하는 데 유용 합니다.|
|ConfigurationChange <br>&#124;에서 RegistryKey에 @ "HKEY_LOCAL_MACHINE\\SYSTEM\\CurrentControlSet\\Services\\SharedAccess\\Parameters\\FirewallPolicy"가 포함 되어 있습니다.| 방화벽 설정에 대 한 변경 내용을 추적 하는 데 유용 합니다.|

## <a name="next-steps"></a>다음 단계

* Runbook에서 변경 내용 추적 및 인벤토리를 사용 하려면 [변경 내용 추적 및 인벤토리 관리](change-tracking-file-contents.md)를 참조 하세요.
* 변경 내용 추적 및 인벤토리를 사용 하 여 오류를 해결 하려면 [변경 내용 추적 및 인벤토리 문제 해결](automation-tutorial-troubleshoot-changes.md)을 참조 하세요.
* [Azure Monitor 로그의 로그 검색](../log-analytics/log-analytics-log-searches.md) 을 사용 하 여 자세한 변경 내용 추적 데이터를 볼 수 있습니다.
