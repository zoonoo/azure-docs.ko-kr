---
title: "Microsoft Azure에서 PerfInsights를 사용하는 방법 | Microsoft Docs"
description: "PerfInsights를 사용하여 Windows VM 성능 문제를 해결하는 방법을 설명합니다."
services: virtual-machines-windows'
documentationcenter: 
author: genlin
manager: cshepard
editor: na
tags: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: troubleshooting
ms.date: 11/03/2017
ms.author: genli
ms.openlocfilehash: bb4c21456643532df040df4fcd5f4fa1a4f48d2c
ms.sourcegitcommit: 80eb8523913fc7c5f876ab9afde506f39d17b5a1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2017
---
# <a name="how-to-use-perfinsights"></a>PerfInsights를 사용하는 방법 

[PerfInsights](http://aka.ms/perfinsightsdownload)는 유용한 진단 정보를 수집하고, I/O 스트레스 부하를 실행하며, Microsoft Azure에서 Windows VM 성능 문제를 해결하는 데 도움이 되는 분석 보고서를 제공하는 자동화된 스크립트입니다. 이 스크립트는 [Azure Performance Diagnostics VM 확장](performance-diagnostics-vm-extension.md)을 설치하여 포털에서 직접 또는 독립 실행형 스크립트로 가상 컴퓨터에서 실행될 수 있습니다.

VM 성능 문제로 Microsoft 지원 티켓을 열기 전에 이 스크립트를 실행하는 것이 좋습니다.

## <a name="supported-troubleshooting-scenarios"></a>지원되는 문제 해결 시나리오

PerfInsights에서는 고유한 시나리오로 그룹화된 여러 종류의 정보를 수집하고 분석할 수 있습니다.

### <a name="collect-basic-configuration"></a>기본 구성 수집 

이 시나리오에서는 다음 항목을 포함하여 디스크 구성 및 기타 중요한 정보를 수집합니다.

-   이벤트 로그

-   모든 들어오는 연결과 나가는 연결에 대한 네트워크 상태

-   네트워크 및 방화벽 구성 설정

-   현재 시스템에서 실행 중인 모든 응용 프로그램의 작업 목록

-   msinfo32에서 VM(가상 컴퓨터)에 대해 만든 정보 파일

-   Microsoft SQL Server 데이터베이스 구성 설정(VM이 SQL Server를 실행하는 서버로 식별된 경우)

-   저장소 안정성 카운터

-   중요한 Windows 핫픽스

-   설치된 필터 드라이버

이 모음은 시스템에 영향을 주지 않아야 하는 수동적인 정보 모음입니다. 

>[!Note]
>이 시나리오는 다음과 같은 각각의 시나리오에 자동으로 포함됩니다.

### <a name="benchmarking"></a>벤치마킹

이 시나리오에서는 VM에 연결된 모든 드라이브에 대해 [diskspd](https://github.com/Microsoft/diskspd) 벤치마크 테스트(IOPS 및 MBPS)를 실행합니다. 

> [!Note]
> 이 시나리오는 시스템에 영향을 줄 수 있으므로 라이브 프로덕션 시스템에서 실행하면 안됩니다. 필요한 경우 문제가 발생하지 않도록 전용 유지 관리 창에서 이 시나리오를 실행합니다. 추적 또는 벤치마크 테스트로 인해 워크로드가 증가하면 VM 성능이 저하될 수 있습니다.
>

### <a name="slow-vm-analysis"></a>느린 VM 분석 

이 시나리오에서는 Generalcounters.txt 파일에 지정된 카운터를 사용하여 [성능 카운터](https://msdn.microsoft.com/library/windows/desktop/aa373083(v=vs.85).aspx) 추적을 실행합니다. VM이 SQL Server를 실행하는 서버로 식별되면 Sqlcounters.txt 파일에 있는 카운터를 사용하여 성능 카운터 추적을 실행합니다. 또한 성능 진단 데이터도 포함됩니다.

### <a name="slow-vm-analysis-and-benchmarking"></a>VM 저속 분석 및 벤치마킹

이 시나리오에서는 [성능 카운터](https://msdn.microsoft.com/library/windows/desktop/aa373083(v=vs.85).aspx) 추적을 실행한 다음 [diskspd](https://github.com/Microsoft/diskspd) 벤치마크 테스트를 수행합니다. 

> [!Note]
> 이 시나리오는 시스템에 영향을 줄 수 있으므로 라이브 프로덕션 시스템에서 실행하면 안됩니다. 필요한 경우 문제가 발생하지 않도록 전용 유지 관리 창에서 이 시나리오를 실행합니다. 추적 또는 벤치마크 테스트로 인해 워크로드가 증가하면 VM 성능이 저하될 수 있습니다.
>

### <a name="azure-files-analysis"></a>Azure Files 분석 

이 시나리오에서는 네트워크 추적과 함께 특별한 성능 카운터 캡처를 실행합니다. 캡처에는 모든 "SMB 클라이언트 공유" 카운터가 포함됩니다. 다음은 캡처에 포함되는 몇 가지 주요 SMB 클라이언트 공유 성능 카운터입니다.

| **형식**     | **SMB 클라이언트 공유 카운터** |
|--------------|-------------------------------|
| IOPS         | 데이터 요청 수/초             |
|              | 읽기 요청 수/초             |
|              | 쓰기 요청 수/초            |
| 대기 시간      | 평균 초/데이터 요청         |
|              | 평균 초/읽기                 |
|              | 평균 초/쓰기                |
| IO 크기      | 평균 바이트 수/데이터 요청       |
|              | 평균 바이트 수/읽기               |
|              | 평균 바이트 수/쓰기              |
| 처리량   | 데이터 바이트 수/쓰기                |
|              | 읽기 바이트 수/초                |
|              | 쓰기 바이트 수/초               |
| 큐 길이 | 평균 읽기 큐 길이        |
|              | 평균 쓰기 큐 길이       |
|              | 평균 데이터 큐 길이        |

### <a name="custom-slow-vm-analysis"></a>사용자 지정 느린 VM 분석 

사용자 지정 느린 VM 분석을 실행하면 선택한 추적 수에 따라 모든 추적(성능 카운터, xperf, 네트워크, storport)이 병렬로 실행됩니다. 추적이 완료되면 도구에서 diskspd 벤치마크(선택한 경우)를 실행합니다. 

> [!Note]
> 이 시나리오는 시스템에 영향을 줄 수 있으므로 라이브 프로덕션 시스템에서 실행하면 안됩니다. 필요한 경우 문제가 발생하지 않도록 전용 유지 관리 창에서 이 시나리오를 실행합니다. 추적 또는 벤치마크 테스트로 인해 워크로드가 증가하면 VM 성능이 저하될 수 있습니다.
>

## <a name="what-kind-of-information-is-collected-by-the-script"></a>스크립트에서 수집하는 정보 유형

사용된 성능 시나리오에 따라 Windows VM, 디스크 또는 저장소 풀 구성, 성능 카운터, 로그 및 다양한 추적과 관련된 정보가 수집됩니다.

|수집되는 데이터                              |  |  | 성능 시나리오 |  |  | |
|----------------------------------|----------------------------|------------------------------------|--------------------------|--------------------------------|----------------------|----------------------|
|                              | 기본 구성 수집 | 벤치마킹 | 느린 VM 분석 | VM 저속 분석 및 벤치마킹 | Azure Files 분석 | 사용자 지정 느린 VM 분석 |
| 이벤트 로그의 정보      | 예                        | 예                                | 예                      | 예                            | 예                  | 예                  |
| 시스템 정보               | 예                        | 예                                | 예                      | 예                            | 예                  | 예                  |
| 볼륨 매핑                       | 예                        | 예                                | 예                      | 예                            | 예                  | 예                  |
| 디스크 매핑                         | 예                        | 예                                | 예                      | 예                            | 예                  | 예                  |
| 실행 중인 작업                    | 예                        | 예                                | 예                      | 예                            | 예                  | 예                  |
| 저장소 안정성 카운터     | 예                        | 예                                | 예                      | 예                            | 예                  | 예                  |
| 저장소 정보              | 예                        | 예                                | 예                      | 예                            | 예                  | 예                  |
| Fsutil 출력                    | 예                        | 예                                | 예                      | 예                            | 예                  | 예                  |
| 필터 드라이버 정보               | 예                        | 예                                | 예                      | 예                            | 예                  | 예                  |
| Netstat 출력                   | 예                        | 예                                | 예                      | 예                            | 예                  | 예                  |
| 네트워크 구성            | 예                        | 예                                | 예                      | 예                            | 예                  | 예                  |
| 방화벽 구성           | 예                        | 예                                | 예                      | 예                            | 예                  | 예                  |
| SQL Server 구성         | 예                        | 예                                | 예                      | 예                            | 예                  | 예                  |
| 성능 진단 추적 * | 예                        | 예                                | 예                      |                                | 예                  | 예                  |
| 성능 카운터 추적 **     |                            |                                    |                          |                                |                      | 예                  |
| SMB 카운터 추적 **             |                            |                                    |                          |                                | 예                  |                      |
| SQL Server 카운터 추적 **      |                            |                                    |                          |                                |                      | 예                  |
| XPerf 추적                      |                            |                                    |                          |                                |                      | 예                  |
| StorPort 추적                   |                            |                                    |                          |                                |                      | 예                  |
| 네트워크 추적                    |                            |                                    |                          |                                | 예                  | 예                  |
| Diskspd 벤치마크 추적 ***      |                            | 예                                |                          | 예                            |                      |                      |
|       |                            |                         |                                                   |                      |                      |

### <a name="performance-diagnostics-trace-"></a>성능 진단 추적 (*)

백그라운드에서 규칙 기반 엔진을 실행하여 데이터를 수집하고 지속적인 성능 문제를 진단합니다. 현재 지원되는 규칙은 다음과 같습니다.

- HighCpuUsage 규칙: 높은 CPU 사용량 기간을 검색하고 해당 기간 동안의 최고 CPU 사용량 소비자를 표시합니다.
- HighDiskUsage 규칙: 실제 디스크에 대한 높은 디스크 사용량 기간을 검색하고 해당 기간 동안의 최고 디스크 사용량 소비자를 표시합니다.
- HighResolutionDiskMetric 규칙: 각 실제 디스크에 대한 50밀리초당 IOPS, 처리량 및 IO 대기 시간 메트릭을 표시합니다. 디스크 제한 기간을 빠르게 식별하는 데 유용합니다.
- HighMemoryUsage 규칙: 높은 메모리 사용 기간을 검색하고 해당 기간 동안의 최고 메모리 사용량 소비자를 표시합니다.

> [!NOTE] 
> 현재 .NET Framework 3.5 이상 버전이 포함된 Windows 버전이 지원됩니다.

### <a name="performance-counter-trace-"></a>성능 카운터 추적 (**)

다음 성능 카운터를 수집합니다.

- \Process, \Processor, \Memory, \Thread, \PhysicalDisk, \LogicalDisk
- \Cache\Dirty Pages, \Cache\Lazy Write Flushes/sec, \Server\Pool Nonpaged, Failures, \Server\Pool Paged Failures
- \Network Interface, \IPv4\Datagrams, \IPv6\Datagrams, \TCPv4\Segments, \TCPv6\Segments, \Network Adapter, \WFPv4\Packets, \WFPv6\Packets, \UDPv4\Datagrams, \UDPv6\Datagrams, \TCPv4\Connection, \TCPv6\Connection, \Network QoS Policy\Packets, \Per Processor Network Interface Card Activity, \Microsoft Winsock BSP 중에서 선택한 카운터

#### <a name="for-sql-server-instances"></a>SQL Server 인스턴스의 경우
- \SQL Server:Buffer Manager, \SQLServer:Resource Pool Stats, \SQLServer:SQL Statistics\
- \SQLServer:Locks, \SQLServer:General, Statistics
- \SQLServer:Access Methods

#### <a name="for-azure-files"></a>Azure Files의 경우
\SMB Client Shares

### <a name="diskspd-benchmark-trace-"></a>Diskspd 벤치마크 추적 (***)
Diskspd IO 워크로드 테스트[OS 디스크(쓰기) 및 풀 드라이브(읽기/쓰기)]

## <a name="run-the-perfinsights-on-your-vm"></a>VM에서 PerfInsights 실행

### <a name="what-do-i-have-to-know-before-i-run-the-script"></a>스크립트를 실행하기 전에 알고 있어야 하는 사항 

**스크립트 요구 사항**

1.  이 스크립트는 성능 문제가 있는 VM에서 실행해야 합니다. 

2.  지원되는 OS: Windows Server 2008 R2, 2012, 2012 R2, 2016; Windows 8.1 및 Windows 10

**프로덕션 VM에서 스크립트 실행 시 발생할 수 있는 문제**

1.  XPerf 또는 DiskSpd를 사용하도록 구성된 벤치마킹 시나리오 또는 "사용자 지정 느린 VM 분석" 시나리오를 사용하는 경우 스크립트로 인해 VM의 성능이 저하될 수 있습니다. CSS 엔지니어의 감독 없이 프로덕션 환경에서 이러한 시나리오를 실행하지 않는 것이 좋습니다.

2.  DiskSpd를 사용하도록 구성된 벤치마킹 시나리오 또는 "사용자 지정 느린 VM 분석" 시나리오를 사용하는 경우 다른 백그라운드 작업이 테스트된 디스크의 I/O 워크로드를 방해하지 않는지 확인합니다.

3.  기본적으로 스크립트는 임시 저장소 드라이브를 사용하여 데이터를 수집합니다. 추적을 더 오랫동안 사용하도록 유지하면 수집되는 데이터의 양이 적절할 수 있습니다. 이렇게 하면 임시 디스크의 공간 가용성을 낮출 수 있으므로 이 드라이브를 사용하는 모든 응용 프로그램에 영향을 미칩니다.

### <a name="how-do-i-run-perfinsights"></a>PerfInsights를 실행하는 방법 

PerfInsights는 [Azure Performance Diagnostics VM 확장](performance-diagnostics-vm-extension.md)을 설치하거나 독립 실행형 스크립트로 가상 컴퓨터에서 실행할 수 있습니다. 

**Azure Portal에서 PerfInsights 설치 및 실행**

이제 Azure Performance Diagnostics 확장이라고 하는 VM 확장을 사용하여 PerfInsights를 실행할 수 있습니다. 자세한 내용은 [Azure Performance Diagnostics 확장 설치](performance-diagnostics-vm-extension.md#install-the-extension)를 참조하세요.  

**독립 실행형 모드에서 PerfInsights 스크립트 실행**

PerfInsights 스크립트를 실행하려면 다음 단계를 수행합니다.


1. [PerfInsights.zip](http://aka.ms/perfinsightsdownload)을 다운로드합니다.

2. PerfInsights.zip 파일의 차단을 해제합니다. 이렇게 하려면 PerfInsights.zip 파일을 마우스 오른쪽 단추로 클릭하고 **속성**을 선택합니다. **일반** 탭에서 **차단 해제**를 선택한 다음 **확인**을 선택합니다. 이렇게 하면 추가 보안을 요구하는 메시지가 표시되지 않고 스크립트가 실행됩니다.  

    ![zip 파일 잠금 해제](media/how-to-use-perfInsights/unlock-file.png)

3.  압축된 PerfInsights.zip 파일을 임시 드라이브(기본적으로 D 드라이브)로 확장합니다. 압축된 파일에는 다음 파일과 폴더가 있어야 합니다.

    ![zip 폴더에 포함된 파일](media/how-to-use-perfInsights/file-folder.png)

4.  Windows PowerShell을 관리자로 연 다음 PerfInsights.ps1 스크립트를 실행합니다.

    ```
    cd <the path of PerfInsights folder >
    Powershell.exe -ExecutionPolicy UnRestricted -NoProfile -File .\\PerfInsights.ps1
    ```

    실행 정책을 변경할지 묻는 메시지가 표시되면 "y"를 입력해야 합니다.

    [고지 사항] 대화 상자에서는 Microsoft 지원 서비스와 진단 정보를 공유하는 옵션이 제공됩니다. 계속하려면 사용권 계약에도 동의해야 합니다. 선택한 다음 **스크립트 실행**을 클릭합니다.

    ![고지 사항 상자](media/how-to-use-perfInsights/disclaimer.png)

5.  스크립트를 실행할 때 사례 번호(통계 용도)를 제출합니다. 그런 다음 **확인**을 클릭합니다.
    
    ![지원 ID 입력](media/how-to-use-perfInsights/enter-support-number.png)

6.  임시 저장소 드라이브를 선택합니다. 스크립트는 드라이브의 드라이브 문자를 자동으로 검색할 수 있습니다. 이 단계에서 문제가 발생하면 드라이브를 선택하라는 메시지가 표시될 수 있습니다(기본 드라이브: D). 생성된 로그는 log\_collection 폴더에 저장됩니다. 드라이브 문자를 입력하거나 그대로 둔 다음 **확인**을 클릭합니다.

    ![드라이브 입력](media/how-to-use-perfInsights/enter-drive.png)

7.  제공된 목록에서 문제 해결 시나리오를 선택합니다.

       ![지원 시나리오 선택](media/how-to-use-perfInsights/select-scenarios.png)

8.  UI 없이 PerfInsights를 실행할 수도 있습니다.

    다음 명령에서는 UI 프롬프트 없이 30초 동안 "느린 VM 분석" 문제 해결 시나리오를 실행합니다. 4단계에서 언급한 동일한 고지 사항 및 EULA에 동의하도록 요구하는 메시지가 표시됩니다.

        powershell.exe -ExecutionPolicy UnRestricted -NoProfile -Command ".\\PerfInsights.ps1 -NoGui -Scenario vmslow -TracingDuration 30"

    PerfInsights를 자동 모드로 실행하려면 **-AcceptDisclaimerAndShareDiagnostics** 매개 변수를 사용합니다. 예를 들어 다음 명령을 사용합니다.

        powershell.exe -ExecutionPolicy UnRestricted -NoProfile -Command ".\\PerfInsights.ps1 -NoGui -Scenario vmslow -TracingDuration 30 -AcceptDisclaimerAndShareDiagnostics"

### <a name="how-do-i-troubleshoot-issues-while-running-the-script"></a>스크립트를 실행하는 동안 문제를 해결하는 방법

스크립트가 비정상적으로 종료되면 다음과 같이 -Cleanup 스위치와 함께 스크립트를 실행하여 일관성이 없는 상태를 정리할 수 있습니다.

    powershell.exe -ExecutionPolicy UnRestricted -NoProfile -Command ".\\PerfInsights.ps1 -Cleanup"

임시 드라이브를 자동 검색하는 중에 문제가 발생하면 드라이브를 선택하라는 메시지가 표시될 수 있습니다(기본 드라이브: D).

![드라이브 입력](media/how-to-use-perfInsights/enter-drive.png)

스크립트에서 유틸리티 도구를 제거하고 임시 폴더를 제거합니다.

### <a name="troubleshooting-other-script-issues"></a>기타 스크립트 문제 해결 

스크립트를 실행할 때 문제가 발생하면 Ctrl+C를 눌러 스크립트 실행을 중단합니다. 임시 개체를 제거하려면 "비정상 종료 후 정리" 섹션을 참조하세요.

여러 번 시도한 후에도 스크립트 오류가 계속 발생하면 시작할 때 "-Debug" 매개 변수 옵션을 사용하여 "디버그 모드"에서 스크립트를 실행하는 것이 좋습니다.

오류가 발생한 후에는 PowerShell 콘솔의 전체 출력을 복사하여 문제를 해결하기 위해 도움을 주는 Microsoft 지원 담당자에게 보내주세요.

### <a name="how-do-i-run-the-script-in-custom-slow-vm-analysis-mode"></a>스크립트를 사용자 지정 느린 VM 분석 모드로 실행하는 방법

**사용자 지정 느린 VM 분석**을 선택하여 여러 추적을 동시에 사용할 수 있습니다(Shift 키로 다중 선택).

![시나리오 선택](media/how-to-use-perfInsights/select-scenario.png)

성능 카운터 추적, XPerf 추적, 네트워크 추적 또는 Storport 추적 시나리오를 선택하는 경우 대화 상자의 지침을 따르고, 추적을 시작한 후에 성능 저하 문제를 재현합니다.

다음 대화 상자에서 추적을 시작할 수 있습니다.

![추적 시작](media/how-to-use-perfInsights/start-trace-message.png)

추적을 중지하려면 두 번째 대화 상자에서 명령을 확인해야 합니다.

![추적 중지](media/how-to-use-perfInsights/stop-trace-message.png)
![추적 중지](media/how-to-use-perfInsights/ok-trace-message.png)

추적 또는 작업이 완료되면 D:\\log\_collection(또는 임시 드라이브)에 **CollectedData\_yyyy-MM-dd\_hh\_mm\_ss.zip**이라는 새 파일이 생성됩니다. 이 파일은 분석을 위해 지원 담당자에게 보낼 수 있습니다.

## <a name="review-the-diagnostics-report-created-by-perfinsights"></a>PerfInsights에서 만든 진단 보고서 검토

PerfInsights에서 생성된 **CollectedData\_yyyy-MM-dd\_hh\_mm\_ss.zip 파일** 내에서 PerfInsights의 결과를 자세히 설명하는 HTML 보고서를 찾을 수 있습니다. . 보고서를 검토하려면 **CollectedData\_yyyy-MM-dd\_hh\_mm\_ss.zip** 파일을 펼친 다음 **PerfInsights Report.html** 파일을 엽니다.

**검색 결과** 탭을 선택합니다.

![find tab](media/how-to-use-perfInsights/findingtab.png)
![findings](media/how-to-use-perfInsights/findings.PNG)

**참고 사항**

-   위험으로 분류된 검색 결과는 성능 문제를 일으킬 수 있는 알려진 문제입니다.

-   중요로 분류된 검색 결과는 반드시 성능 문제를 일으키지는 않지만 최적이 아닌 구성을 나타냅니다.

-   정보로 분류되는 검색 결과는 정보만 제공합니다.

모든 위험 및 중요 검색 결과에 대한 권장 사항 및 링크를 검토하여 검색 결과 및 성능 최적화 구성에 대한 성능 또는 모범 사례에 미칠 수 있는 영향에 대한 자세한 정보를 알아보세요.

### <a name="storage-tab"></a>저장소 탭

**검색 결과** 섹션은 저장소와 관련된 다양한 검색 결과 및 권장 사항을 표시합니다.

**DiskMap** 및 **VolumeMap** 섹션에서는 논리 볼륨과 실제 디스크가 서로 관련된 방식을 이중 관점에서 설명합니다.

PhysicalDisk 관점(DiskMap)에서 테이블은 디스크에서 실행 중인 모든 논리 볼륨을 표시합니다. 다음 예에서 PhysicalDrive2는 여러 파티션(J 및 H)에서 만든 2개의 논리 볼륨을 실행합니다.

![데이터 탭](media/how-to-use-perfInsights/disktab.png)

볼륨 관점(*VolumeMap*)에서 테이블은 각 논리 볼륨 아래의 모든 실제 디스크를 표시합니다. RAID/동적 디스크의 경우 여러 실제 디스크에서 논리 볼륨을 실행할 수 있습니다. 다음 샘플에서 *C:\\mount*는 PhysicalDisks \#2 및 \#3에 *SpannedDisk*로 구성된 탑재 지점입니다.

![볼륨 탭](media/how-to-use-perfInsights/volumetab.png)

### <a name="sql-tab"></a>SQL 탭

대상 VM에서 SQL Server 인스턴스를 호스팅하는 경우 **SQL**이라는 보고서에 추가 탭이 표시됩니다.

![sql 탭](media/how-to-use-perfInsights/sqltab.png)

이 섹션에는 VM에 호스팅된 SQL Server 인스턴스 각각에 대한 "검색 결과" 탭 및 추가 하위 탭이 포함되어 있습니다.

"검색 결과" 탭에는 권장 사항과 함께 확인할 수 있는 모든 SQL 관련 성능 문제의 목록이 포함됩니다.

다음 예에서는 *modeldev* 및 *modellog* 파일이 모두 C 드라이브에 있으므로 *PhysicalDrive0*(C 드라이브 실행 중)이 표시되며, 이 파일들은 서로 다른 형식의 파일입니다(예: 데이터 파일 및 트랜잭션 로그).

![로그 정보](media/how-to-use-perfInsights/loginfo.png)

SQL Server 인스턴스별 탭에는 선택한 인스턴스에 대한 기본 정보와 [설정], [구성] 및 [사용자 옵션]을 포함하여 고급 정보를 위한 추가 섹션이 표시되는 일반 섹션이 있습니다.

### <a name="diagnostic-tab"></a>진단 탭
진단 탭에는 PerfInsights 실행 기간 동안 상자에 상위 CPU, 디스크 및 메모리 소비자에 대한 정보가 포함됩니다. 또한 시스템이 누락할 수 있는 위험 패치, 작업 목록 및 중요한 시스템 이벤트 등 기타 유용한 정보도 확인할 수 있습니다. 

## <a name="references-to-the-external-tools-used"></a>사용되는 외부 도구에 대한 참조

### <a name="diskspd"></a>Diskspd

Diskspd는 Windows, Windows Server 및 클라우드 서버 인프라 엔지니어링 팀의 저장소 로드 생성기 및 성능 테스트 도구입니다. 자세한 내용은 [Diskspd](https://github.com/Microsoft/diskspd)를 참조하세요.

### <a name="xperf"></a>XPerf

Xperf는 Windows 성능 도구 키트에서 추적을 캡처하는 명령줄 도구입니다.

자세한 내용은 [Windows 성능 도구 키트 - Xperf(영문)](https://blogs.msdn.microsoft.com/ntdebugging/2008/04/03/windows-performance-toolkit-xperf/)를 참조하세요.

## <a name="next-steps"></a>다음 단계

### <a name="upload-diagnostics-logs-and-reports-to-microsoft-support-for-further-review"></a>추가 검토를 위해 Microsoft 지원 서비스에 진단 로그 및 보고서 업로드

Microsoft 지원 담당자와 협력할 때 문제 해결 프로세스를 지원하기 위해 PerfInsights에서 생성된 출력을 전송하도록 요청받을 수 있습니다.

지원 담당자는 DTM 작업 영역을 만들고 [DTM 포털](https://filetransfer.support.microsoft.com/EFTClient/Account/Login.htm)과 고유한 사용자 ID 및 암호에 대한 링크가 포함된 전자 메일 메시지를 받게 됩니다.

이 메시지는 **CTS 자동 진단 서비스**(ctsadiag@microsoft.com)에서 보냅니다.

![메시지 샘플](media/how-to-use-perfInsights/supportemail.png)

보안을 강화하기 위해 처음 사용할 때 암호를 변경해야 합니다.

DTM에 로그인하면 PerfInsights에서 수집한 **CollectedData\_yyyy-MM-dd\_hh\_mm\_ss.zip** 파일을 업로드하는 대화 상자가 표시됩니다.
