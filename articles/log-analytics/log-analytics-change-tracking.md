---
title: "Azure Log Analytics로 변경 추적 | Microsoft Docs"
description: "Log Analytics에서 변경 내용 추적 솔루션을 사용하여 사용자 환경에서 발생하는 소프트웨어 및 Windows Services 변경 내용을 식별할 수 있습니다."
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: f8040d5d-3c89-4f0c-8520-751c00251cb7
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/11/2017
ms.author: banders
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 81cc7f78ef777e02b195422a81d9a9f15cb63564
ms.sourcegitcommit: 922687d91838b77c038c68b415ab87d94729555e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/13/2017
---
# <a name="track-software-changes-in-your-environment-with-the-change-tracking-solution"></a>변경 내용 추적 솔루션으로 사용자 환경에서 소프트웨어 변경 추적

![변경 내용 추적 기호](./media/log-analytics-change-tracking/change-tracking-symbol.png)

이 문서를 통해 Log Analytics에서 변경 내용 추적 솔루션을 사용하여 사용자 환경의 변경 내용을 쉽게 식별할 수 있습니다. 이 솔루션은 Windows 및 Linux 소프트웨어, Windows 파일 및 레지스트리 키, Windows 서비스 및 Linux 데몬의 변경 내용을 추적합니다. 구성 변경 내용을 식별하면 운영 문제를 쉽게 특정할 수 있습니다.

설치된 에이전트의 유형을 업데이트하려면 이 솔루션을 설치합니다. 모니터링되는 서비스에서 설치된 소프트웨어, Windows 서비스 및 Linux 디먼에 대한 변경 내용은 읽혀집니다. 그런 다음 데이터는 처리를 위해 클라우드의 Log Analytics 서비스에 전송됩니다. 논리는 수신된 데이터에 적용되며 클라우드 서비스는 데이터를 기록합니다. 변경 내용 추적 대시보드의 정보를 사용하여 서버 인프라에서 수행한 변경 내용을 쉽게 확인할 수 있습니다.

## <a name="installing-and-configuring-the-solution"></a>솔루션 설치 및 구성
다음 정보를 사용하여 솔루션을 설치하고 구성합니다.

* 변경 내용을 모니터링할 각 컴퓨터에 [Windows](log-analytics-windows-agent.md), [Operations Manager](log-analytics-om-agents.md) 또는 [Linux](log-analytics-linux-agents.md) 에이전트가 있어야 합니다.
* 변경 내용 추적 솔루션을 [Azure 마켓플레이스](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.ChangeTrackingOMS?tab=Overview)의 OMS 작업 영역에 추가합니다. 또는 [솔루션 갤러리에서 Log Analytics 솔루션 추가](log-analytics-add-solutions.md)에서 정보를 사용하여 솔루션을 추가할 수 있습니다. 추가 구성은 필요하지 않습니다.

### <a name="configure-linux-files-to-track"></a>추적할 Linux 파일 구성
다음 단계를 사용하여 Linux 컴퓨터에서 추적할 파일을 구성합니다.

1. OMS 포털에서 **설정**을 클릭합니다(기어 기호).
2. **설정** 페이지에서 **데이터**를 클릭한 다음 **Linux 파일 추적**을 클릭합니다.
3. Linux 파일 변경 내용 추적에서 추적하려는 파일의 파일 이름을 포함한 전체 경로를 입력한 다음 **추가** 기호를 클릭합니다. 예: "/etc/* .conf"
4. **Save**를 클릭합니다.  

> [!NOTE]
> Linux 파일 추적에는 디렉터리 추적, 디렉터리를 통한 재귀 및 와일드 카드 추적과 같은 추가적인 기능이 포함됩니다.

### <a name="configure-windows-files-to-track"></a>추적할 Windows 파일 구성
다음 단계를 사용하여 Windows 컴퓨터에서 추적할 파일을 구성합니다.

1. OMS 포털에서 **설정**을 클릭합니다(기어 기호).
2. **설정** 페이지에서 **데이터**를 클릭한 다음 **Windows 파일 추적**을 클릭합니다.
3. Windows 파일 변경 내용 추적에서 추적하려는 파일의 파일 이름을 포함한 전체 경로를 입력한 다음 **추가** 기호를 클릭합니다. 예: C:\Program Files (x86)\Internet Explorer\iexplore.exe or C:\Windows\System32\drivers\etc\hosts
4. **Save**를 클릭합니다.  
   ![Windows 파일 변경 내용 추적](./media/log-analytics-change-tracking/windows-file-change-tracking.png)

### <a name="configure-windows-registry-keys-to-track"></a>추적할 Windows 레지스트리 키 구성
다음 단계를 사용하여 Windows 컴퓨터에서 추적할 레지스트리 키를 구성합니다.

1. OMS 포털에서 **설정**을 클릭합니다(기어 기호).
2. **설정** 페이지에서 **데이터**를 클릭한 다음 **Windows 레지스트리 추적**을 클릭합니다.
3. Windows 레지스트리 변경 내용 추적에서 추적하려는 전체 키를 입력한 다음 **추가** 기호를 클릭합니다.
4. **Save**를 클릭합니다.  
   ![Windows 레지스트리 변경 내용 추적](./media/log-analytics-change-tracking/windows-registry-change-tracking.png)

### <a name="explanation-of-linux-file-collection-properties"></a>Linux 파일 컬렉션 속성 설명
1. **형식**
   * **File**(보고서 파일 메타데이터 - 크기, 수정 날짜, 해시 등)
   * **Directory**(보고서 디렉터리 메타데이터 - 크기, 수정 날짜 등)
2. **Links**(다른 파일 또는 디렉터리에 대한 Linux symlink 참조 처리)
   * **Ignore**(참조된 파일/디렉터리를 포함하지 않도록 재귀 중에 symlink 무시)
   * **Follow**(참조된 파일/디렉터리를 포함하도록 재귀 중에 symlink 따라 이동)
   * **Manage**(symlink 따라 이동 및 반환된 콘텐츠 처리 변경)

   > [!NOTE]   
   > "Manage" 링크 옵션은 권장되지 않습니다. 파일 콘텐츠 검색은 지원되지 않습니다.

3. **Recurse**(폴더 수준 재귀 및 경로 문을 충족하는 모든 파일 추적)
4. **Sudo**(sudo 권한이 필요한 액세스 파일 또는 디렉터리 사용)

### <a name="limitations"></a>제한 사항
현재 변경 내용 추적 솔루션은 다음 항목을 지원하지 않습니다.

* Windows 파일 추적을 위한 폴더(디렉터리)
* Windows 파일 추적을 위한 재귀
* Windows 파일 추적을 위한 와일드 카드
* 경로 변수
* 네트워크 파일 시스템
* File Content(파일 내용)

기타 제한 사항은 다음과 같습니다.

* **최대 파일 크기** 열과 값은 현재 구현에서 사용되지 않습니다.
* 30분 수집 주기에서 2500개 이상의 파일을 수집하는 경우 솔루션 성능이 저하될 수 있습니다.
* 네트워크 트래픽이 많을 경우 변경 레코드를 표시하는 데 최대 6시간이 걸릴 수 있습니다.
* 컴퓨터를 종료하는 동안 구성을 수정하면 컴퓨터는 이전 구성에 속한 파일 변경 내용을 게시할 수 있습니다.

### <a name="known-issues"></a>알려진 문제
변경 내용 추적 솔루션에 현재 다음과 같은 문제가 있습니다.
* 핫픽스 업데이트가 Windows 10 크리에이터 업데이트 및 Windows Server 2016 Core RS3 컴퓨터에 대해 수집되지 않습니다.

## <a name="change-tracking-data-collection-details"></a>변경 내용 추적 데이터 수집 정보
변경 내용 추적 기능은 설정된 에이전트를 사용하여 소프트웨어 인벤토리 및 Windows 서비스 메타데이터를 수집합니다.

다음 표에서는 데이터 수집 방법 및 변경 내용 추적에 대해 데이터가 수집되는 방식에 대한 기타 세부 정보를 보여 줍니다.

| 플랫폼 | 직접 에이전트 | Operations Manager 에이전트 | Linux 에이전트 | Azure Storage | Operations Manager 필요 여부 | 관리 그룹을 통해 전송되는 Operations Manager 에이전트 데이터 | 수집 빈도 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Windows 및 Linux | &#8226; | &#8226; | &#8226; |  |  | &#8226; | 변경 유형에 따라 5분부터 50분 자세한 내용은 다음 테이블을 참조하세요. |


다음 테이블에서는 변경 형식에 대한 데이터 컬렉션 빈도를 보여 줍니다.

| **change type** | **frequency** | **에이전트****가** **차이를 발견한 경우 전송하나요?** |
| --- | --- | --- |
| Windows 레지스트리 | 50분 | 아니요 |
| Windows 파일 | 30분 | 예. 24시간 동안 변경 사항이 없는 경우 스냅숏이 전송됩니다. |
| Linux 파일 | 15분 | 예. 24시간 동안 변경 사항이 없는 경우 스냅숏이 전송됩니다. |
| Windows 서비스 | 30분 | 예, 변경 내용이 발견되는 경우 30분마다 전송됩니다. 변경에 관계없이 24시간마다 스냅숏이 전송됩니다. 따라서 변경 사항이 없는 경우에도 스냅숏이 전송됩니다. |
| Linux 데몬 | 5분 | 예. 24시간 동안 변경 사항이 없는 경우 스냅숏이 전송됩니다. |
| Windows 소프트웨어 | 30분 | 예, 변경 내용이 발견되는 경우 30분마다 전송됩니다. 변경에 관계없이 24시간마다 스냅숏이 전송됩니다. 따라서 변경 사항이 없는 경우에도 스냅숏이 전송됩니다. |
| Linux 소프트웨어 | 5분 | 예. 24시간 동안 변경 사항이 없는 경우 스냅숏이 전송됩니다. |

### <a name="registry-key-change-tracking"></a>레지스트리 키 변경 내용 추적

Log Analytics는 변경 내용 추적 솔루션을 사용하여 Windows 레지스트리를 모니터링 및 추적을 수행합니다. 레지스트리 키의 변경 내용을 모니터링하는 목적은 타사 코드 및 맬웨어가 활성화될 수 있는 확장성 지점을 정확하게 찾기 위한 것입니다. 다음 목록에서는 솔루션에서 추적되는 기본 레지스트리 키 및 각 키가 추적되는 이유를 보여 줍니다.

- HKEY\_LOCAL\_MACHINE\Software\Microsoft\Windows\CurrentVersion\Group Policy\Scripts\Startup
    - 시작 시 실행되는 스크립트를 모니터링합니다.
- HKEY\_LOCAL\_MACHINE\Software\Microsoft\Windows\CurrentVersion\Group Policy\Scripts\Shutdown
    - 종료 시 실행되는 스크립트를 모니터링합니다.
- HKEY\_LOCAL\_MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Run
    - 사용자가 Windows 계정에 로그인하기 전에 로드되는 키를 모니터링합니다. 키는 64비트 컴퓨터에서 실행되는 32비트 프로그램에 사용됩니다.
- HKEY\_LOCAL\_MACHINE\SOFTWARE\Microsoft\Active Setup\Installed Components
    - 응용 프로그램 설정의 변경 내용을 모니터링합니다.
- HKEY\_LOCAL\_MACHINE\Software\Classes\Directory\ShellEx\ContextMenuHandlers
    - Windows 탐색기에 직접 연결되고 일반적으로 Explorer.exe를 사용하여 In Process에서 실행되는 일반적인 자동 시작 항목을 모니터링합니다.
- HKEY\_LOCAL\_MACHINE\Software\Classes\Directory\Shellex\CopyHookHandlers
    - Windows 탐색기에 직접 연결되고 일반적으로 Explorer.exe를 사용하여 In Process에서 실행되는 일반적인 자동 시작 항목을 모니터링합니다.
- HKEY\_LOCAL\_MACHINE\Software\Classes\Directory\Background\ShellEx\ContextMenuHandlers
    - Windows 탐색기에 직접 연결되고 일반적으로 Explorer.exe를 사용하여 In Process에서 실행되는 일반적인 자동 시작 항목을 모니터링합니다.
- HKEY\_LOCAL\_MACHINE\Software\Microsoft\Windows\CurrentVersion\Explorer\ShellIconOverlayIdentifiers
    - 아이콘 오버레이 처리기 등록을 모니터링합니다.
- HKEY\_LOCAL\_MACHINE\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\ShellIconOverlayIdentifiers
    - 64비트 컴퓨터에서 실행되는 32비트 프로그램에 대한 아이콘 오버레이 처리기 등록을 모니터링합니다.
- HKEY\_LOCAL\_MACHINE\Software\Microsoft\Windows\CurrentVersion\Explorer\Browser Helper Objects
    - Internet Explorer에 대한 새 브라우저 도우미 개체 플러그 인을 모니터링합니다. 현재 페이지의 DOM(문서 개체 모델)에 액세스하고 탐색을 제어하는 데 사용됩니다.
- HKEY\_LOCAL\_MACHINE\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\Browser Helper Objects
    - Internet Explorer에 대한 새 브라우저 도우미 개체 플러그 인을 모니터링합니다. 64비트 컴퓨터에서 실행되는 32비트 프로그램에 대해 현재 페이지의 DOM(문서 개체 모델)에 액세스하고 탐색을 제어하는 데 사용됩니다.
- HKEY\_LOCAL\_MACHINE\Software\Microsoft\Internet Explorer\Extensions
    - 사용자 지정 도구 메뉴 및 사용자 지정 도구 모음 단추와 같은 새로운 Internet Explorer 확장을 모니터링합니다.
- HKEY\_LOCAL\_MACHINE\Software\Wow6432Node\Microsoft\Internet Explorer\Extensions
    - 64비트 컴퓨터에서 실행되는 32비트 프로그램에 대해 사용자 지정 도구 메뉴 및 사용자 지정 도구 모음 단추와 같은 새로운 Internet Explorer 확장을 모니터링합니다.
- HKEY\_LOCAL\_MACHINE\Software\Microsoft\Windows NT\CurrentVersion\Drivers32
    - Wavemapper, wave1 및 wave2, msacm.imaadpcm, .msadpcm, .msgsm610 및 vidc와 관련된 32비트 드라이버를 모니터링합니다. SYSTEM.INI 파일의 [drivers] 섹션과 유사합니다.
- HKEY\_LOCAL\_MACHINE\Software\Wow6432Node\Microsoft\Windows NT\CurrentVersion\Drivers32
    - 64비트 컴퓨터에서 실행되는 32비트 프로그램에 대해 Wavemapper, wave1 및 wave2, msacm.imaadpcm, .msadpcm, .msgsm610 및 vidc와 관련된 32비트 드라이버를 모니터링합니다. SYSTEM.INI 파일의 [drivers] 섹션과 유사합니다.
- HKEY\_LOCAL\_MACHINE\System\CurrentControlSet\Control\Session Manager\KnownDlls
    - 알려졌거나 자주 사용되는 시스템 DLL 목록을 모니터링합니다. 트로이 목마 버전의 시스템 DLL을 삭제하여 사용자들이 약한 응용 프로그램 디렉터리 권한을 악용하지 못하게 합니다.
- HKEY\_LOCAL\_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon\Notify
    - Windows 운영 체제에 대한 대화형 로그온 지원 모델인 Winlogon에서 이벤트 알림을 수신할 수 있는 패키지 목록을 모니터링합니다.


## <a name="use-change-tracking"></a>변경 내용 추적 사용
솔루션을 설치한 후, OMS의 **개요** 페이지에 있는 **변경 내용 추적** 타일을 사용하여 모니터링되는 서버에 대한 변경 내용 요약을 볼 수 있습니다.

![변경 내용 추적 타일의 이미지](./media/log-analytics-change-tracking/change-tracking-tile.png)

인프라 에 대한 변경 내용을 본 후 다음 범주에 대한 세부 정보를 드릴인투할 수 있습니다.

* 소프트웨어 및 Windows 서비스에 대한 구성 유형별 변경 내용
* 개별 서버에 대한 응용 프로그램 및 업데이트에 대한 소프트웨어 변경 내용
* 각 응용 프로그램에 대한 전체 소프트웨어 변경 내용 수
* Linux 패키지
* 개별 서버에 대한 Windows 서비스 변경 내용
* Linux 데몬 변경 내용

![변경 내용 추적 대시보드의 이미지](./media/log-analytics-change-tracking/change-tracking-dash01.png)

![변경 내용 추적 대시보드의 이미지](./media/log-analytics-change-tracking/change-tracking-dash02.png)

### <a name="to-view-changes-for-any-change-type"></a>변경 내용 유형의 변경 내용을 보려면
1. **개요** 페이지에서 **변경 내용 추적** 타일을 클릭합니다.
2. **변경 내용 추적** 대시보드에서, 변경 유형 블레이드 중 하나에서 요약 정보를 검토한 다음 하나를 클릭하여 **로그 검색** 페이지에서 항목에 대한 세부 정보를 봅니다.
3. 로그 검색 페이지에서, 시간별 결과, 자세한 결과 및 로그 검색 기록을 볼 수 있습니다. 패싯으로 필터링하여 결과 범위를 좁힐 수 있습니다.

## <a name="next-steps"></a>다음 단계
* [Log Analytics에서 로그 검색](log-analytics-log-searches.md) 을 사용하여 자세한 변경 내용 추적 데이터를 볼 수 있습니다.
