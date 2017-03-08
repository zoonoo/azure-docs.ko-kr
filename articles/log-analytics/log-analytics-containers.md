---
title: "Azure Log Analytics의 컨테이너 솔루션 | Microsoft Docs"
description: "Log Analytics의 컨테이너 솔루션을 사용하여 단일 위치에서 Docker 및 Windows 컨테이너 호스트를 보고 관리할 수 있습니다."
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: e1e4b52b-92d5-4bfa-8a09-ff8c6b5a9f78
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2017
ms.author: banders
translationtype: Human Translation
ms.sourcegitcommit: 503cf4afba4575492984891a681c187a8683a553
ms.openlocfilehash: dc76f22214ab8467705b6420e8362a419a468bd6
ms.lasthandoff: 02/23/2017


---
# <a name="containers-preview-solution-log-analytics"></a>컨테이너(미리 보기) 솔루션 Log Analytics
이 문서에서는 단일 위치에서 Docker 및 Windows 컨테이너 호스트를 보고 관리할 수 있게 Log Analytics의 컨테이너 솔루션을 설정 및 사용하는 방법을 설명합니다. Docker는 IT 인프라에 대한 소프트웨어 배포를 자동화하는 컨테이너를 만드는 데 사용되는 소프트웨어 가상화 시스템입니다.

이 솔루션을 통해 컨테이너 호스트에서 어떤 컨테이너가 실행 중이며 컨테이너에서 어떤 이미지가 실행 중인지 확인할 수 있습니다. 컨테이너에 사용하는 명령을 표시하는 상세한 감사 정보를 확인할 수 있습니다. 또한 중앙화된 로그를 보고 검색하면 원격으로 Docker 또는 Windows 호스트를 보지 않고도 컨테이너의 문제를 해결할 수 있습니다. 호스트에서 성가시고 과도한 리소스를 소모하는 컨테이너를 찾을 수 있습니다. 또한 컨테이너에 대해 중앙화된 CPU 메모리, 저장소, 네트워크 사용 및 성능 정보를 확인할 수 있습니다. Windows를 실행하는 컴퓨터에서 Windows Server, Hyper-V, Docker 컨테이너에서 로그를 중앙 집중화 및 비교할 수 있습니다.

다음 다이어그램에서는 OMS에서 다양한 컨테이너 호스트와 에이전트 간의 관계를 보여 줍니다.

![컨테이너 다이어그램](./media/log-analytics-containers/containers-diagram.png)

## <a name="installing-and-configuring-the-solution"></a>솔루션 설치 및 구성
다음 정보를 사용하여 솔루션을 설치하고 구성합니다.

[솔루션 갤러리에서 Log Analytics 솔루션 추가](log-analytics-add-solutions.md)에 설명하는 프로세스를 사용하여 OMS 작업 영역에 컨테이너 솔루션을 추가합니다.

다음 몇 가지 방법으로 OMS에서 Docker를 설치 및 사용할 수 있습니다.

* 지원되는 Linux 운영 체제에서 Docker를 설치 및 실행한 다음 OMS Agent for Linux를 설치 및 구성합니다.
* CoreOS에서는 Linux 용 OMS 에이전트를 실행할 수 없습니다. 대신 컨테이너화된 Linux 용 OMS 에이전트 버전을 실행합니다.
* Windows Server 2016 및 Windows 10에서 Docker 엔진 및 클라이언트를 설치한 후 에이전트를 연결하여 정보를 수집하고 Log Analytics에 보냅니다.


[GitHub](https://github.com/Microsoft/OMS-docker)에서 해당 컨테이너 호스트에 지원되는 Docker 및 Linux 운영 체제 버전을 검토할 수 있습니다.

[Windows에서 Docker 엔진](https://docs.microsoft.com/virtualization/windowscontainers/manage-docker/configure-docker-daemon) 문서에서 Windows를 실행하는 컴퓨터에서 Docker 엔진을 설치하고 구성하는 방법에 대한 추가 정보를 확인합니다.

> [!IMPORTANT]
> Docker는 컨테이너 호스트에 [OMS Agent for Linux](log-analytics-linux-agents.md)를 설치하기 **전에** 실행해야 합니다.  Docker 설치에 앞서 에이전트를 설치한 경우 OMS Agent for Linux를 다시 설치해야 합니다.  Docker에 대한 자세한 내용은 [Docker 웹 사이트](https://www.docker.com)를 참조하세요.
>
>

컨테이너를 모니터링하려면 먼저 컨테이너 호스트에 다음 설정을 구성해야 합니다.

## <a name="configure-settings-for-a-linux-container-host"></a>Linux 컨테이너 호스트에 대한 설정 구성

다음 x64 Linux 배포는 컨테이너 호스트로 지원됩니다.

- Ubuntu 14.04 LTS, 16.04 LTS
- CoreOS(stable)
- Amazon Linux 2016.09.0
- openSUSE 13.2
- CentOS 7
- SLES 12
- RHEL 7.2

Docker를 설치한 후 컨테이너 호스트에 다음 설정을 사용하여 Docker에 사용할 에이전트를 구성합니다. [OMS 작업 영역 ID 및 키](log-analytics-linux-agents.md)가 필요합니다.


### <a name="for-all-linux-container-hosts-except-coreos"></a>CoreOS를 제외한 모든 Linux 컨테이너 호스트의 경우

- [OMS Agent for Linux 설치 단계](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md)의 지침을 따르세요.

### <a name="for-all-linux-container-hosts-including-coreos"></a>CoreOS를 포함한 모든 Linux 컨테이너 호스트의 경우

모니터링하려는 OMS 컨테이너를 시작합니다. 다음 예제를 수정하여 사용합니다.

```
sudo docker run --privileged -d -v /var/run/docker.sock:/var/run/docker.sock -e WSID="your workspace id" -e KEY="your key" -h=`hostname` -p 127.0.0.1:25225:25225 --name="omsagent" --restart=always microsoft/oms
```

### <a name="switching-from-using-an-installed-linux-agent-to-one-in-a-container"></a>설치된 Linux 에이전트에서 컨테이너의 다른 에이전트로 전환
이전에 직접 설치한 에이전트를 사용하였고 이제 실행 중인 에이전트를 사용하려는 경우 먼저 OMSAgent를 제거해야 합니다. [OMS Agent for Linux 설치 단계](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md)를 참조하세요.

## <a name="supported-windows-versions"></a>지원되는 Windows 버전

- Windows Server 2016
- Windows 10 1주년 버전(Professional 또는 Enterprise)

### <a name="docker-versions-supported-on-windows"></a>Windows에서 지원되는 Docker 버전

- Docker 1.12 – 1.13

### <a name="preparation-before-installing-agents"></a>에이전트 설치 전 준비

Windows를 실행하는 컴퓨터에 에이전트를 설치하기 전에 Docker 서비스를 구성해야 합니다. 구성을 통해 Windows 에이전트 또는 Log Analytics 가상 컴퓨터 확장에서 Docker TCP 소켓을 사용하도록 하여 에이전트가 Docker 데몬에 원격으로 액세스하고 모니터링할 데이터를 캡처하도록 할 수 있습니다.

Windows를 실행하는 컴퓨터에서는 성능 데이터가 지원되지 않습니다.

Windows에서 Docker 데몬을 구성하는 방법은 [Windows에서 Docker 엔진](https://docs.microsoft.com/virtualization/windowscontainers/manage-docker/configure-docker-daemon)을 참조하세요.

#### <a name="to-start-docker-and-verify-its-configuration"></a>Docker를 시작하고 구성을 확인하려면

1.    Windows PowerShell에서 TCP 파이프 및 명명된 파이프를 사용하도록 설정합니다.

    ```
    Stop-Service docker
    dockerd --unregister-service
    dockerd -H npipe:// -H 0.0.0.0:2375 --register-service
    Start-Service docker
    ```

2.    netstat를 사용하여 구성을 확인합니다. 포트 2375가 표시되어야 합니다.

    ```
    PS C:\Users\User1> netstat -a | sls 2375

    TCP    127.0.0.1:2375         Win2016TP5:0           LISTENING
    TCP    127.0.0.1:2375         Win2016TP5:49705       ESTABLISHED
    TCP    127.0.0.1:2375         Win2016TP5:49706       ESTABLISHED
    TCP    127.0.0.1:2375         Win2016TP5:49707       ESTABLISHED
    TCP    127.0.0.1:2375         Win2016TP5:49708       ESTABLISHED
    TCP    127.0.0.1:49705        Win2016TP5:2375        ESTABLISHED
    TCP    127.0.0.1:49706        Win2016TP5:2375        ESTABLISHED
    TCP    127.0.0.1:49707        Win2016TP5:2375        ESTABLISHED
    TCP    127.0.0.1:49708        Win2016TP5:2375        ESTABLISHED
    ```

### <a name="install-windows-agents"></a>Windows 에이전트 설치

Windows 및 Hyper-V 컨테이너 모니터링을 사용하도록 설정하려면 컨테이너 호스트인 Windows 컴퓨터에 에이전트를 설치합니다. 온-프레미스 환경에서 Windows를 실행하는 컴퓨터는 [Log Analytics에 Windows 컴퓨터 연결](log-analytics-windows-agents.md)을 참조하세요. Azure에서 실행되는 가상 컴퓨터의 경우 [가상 컴퓨터 확장](log-analytics-azure-vm-extension.md)을 사용하여 Log Analytics에 연결합니다.

컨테이너 솔루션이 올바르게 설정되었는지 확인하려면

- *ContainerManagement.xxx*를 확인하여 관리 팩이 제대로 다운로드되었는지 확인합니다.
    - 파일은 C:\Program Files\Microsoft Monitoring Agent\Agent\Health Service State\Management Packs 폴더에 있어야 합니다.
- **제어판** > **시스템 및 보안**으로 이동하여 OMS 작업 영역 ID가 올바른지 확인합니다.
    - **Microsoft Monitoring Agent**를 열고 작업 영역 정보가 올바른지 확인합니다.


## <a name="containers-data-collection-details"></a>컨테이너 데이터 수집 세부 정보
컨테이너 솔루션은 사용자가 활성화한 에이전트를 사용하여 컨테이너 호스트 및 컨테이너로부터 다양한 성능 메트릭과 로그 데이터를 수집합니다.

다음 표에서는 데이터 수집 방법 및 컨테이너에 대해 데이터가 수집되는 방식에 대한 기타 세부 정보를 보여 줍니다.

| 플랫폼 | [OMS Agent for Linux](log-analytics-linux-agents.md) | SCOM 에이전트 | Azure 저장소 | SCOM 필요? | 관리 그룹을 통해 전송되는 SCOM 에이전트 데이터 | 수집 빈도 |
| --- | --- | --- | --- | --- | --- | --- |
| Linux |![예](./media/log-analytics-containers/oms-bullet-green.png) |![아니요](./media/log-analytics-containers/oms-bullet-red.png) |![아니요](./media/log-analytics-containers/oms-bullet-red.png) |![아니요](./media/log-analytics-containers/oms-bullet-red.png) |![아니요](./media/log-analytics-containers/oms-bullet-red.png) |매 3분 |

| 플랫폼 | [Windows 에이전트](log-analytics-windows-agents.md) | SCOM 에이전트 | Azure 저장소 | SCOM 필요? | 관리 그룹을 통해 전송되는 SCOM 에이전트 데이터 | 수집 빈도 |
| --- | --- | --- | --- | --- | --- | --- |
| Windows |![예](./media/log-analytics-containers/oms-bullet-green.png) |![아니요](./media/log-analytics-containers/oms-bullet-red.png) |![아니요](./media/log-analytics-containers/oms-bullet-red.png) |![아니요](./media/log-analytics-containers/oms-bullet-red.png) |![아니요](./media/log-analytics-containers/oms-bullet-red.png) |매 3분 |

| 플랫폼 | [Log Analytics VM 확장](log-analytics-azure-vm-extension.md) | SCOM 에이전트 | Azure 저장소 | SCOM 필요? | 관리 그룹을 통해 전송되는 SCOM 에이전트 데이터 | 수집 빈도 |
| --- | --- | --- | --- | --- | --- | --- |
| Azure |![예](./media/log-analytics-containers/oms-bullet-green.png) |![아니요](./media/log-analytics-containers/oms-bullet-red.png) |![아니요](./media/log-analytics-containers/oms-bullet-red.png) |![아니요](./media/log-analytics-containers/oms-bullet-red.png) |![아니요](./media/log-analytics-containers/oms-bullet-red.png) |매 3분 |

다음 테이블은 컨테이너 솔루션에 의해 수집된 데이터 유형, 로그 검색에 사용된 데이터 유형 및 결과의 예를 보여줍니다. 하지만 Windows를 실행하는 컴퓨터에서는 성능 데이터가 아직 지원되지 않습니다.

| 데이터 형식 | 로그 검색의 데이터 유형 | 필드 |
| --- | --- | --- |
| 호스트 및 컨테이너에 대한 성능 | `Type=Perf` | 컴퓨터, ObjectName, CounterName &#40;%프로세서 시간, 디스크 읽기 MB, 디스크 쓰기 MB, 메모리 사용 MB, 네트워크 수신 바이트, 네트워크 송신 바이트, 프로세서 사용 초, 네트워크&#41;, CounterValue,TimeGenerated, CounterPath, SourceSystem |
| 컨테이너 인벤토리 | `Type=ContainerInventory` | TimeGenerated, 컴퓨터, 컨테이너 이름, ContainerHostname, 이미지, ImageTag, ContinerState, ExitCode, EnvironmentVar, 명령, CreatedTime, StartedTime, FinishedTime, SourceSystem, ContainerID, ImageID |
| 컨테이너 이미지 인벤토리 | `Type=ContainerImageInventory` | TimeGenerated, 컴퓨터, 이미지, ImageTag, ImageSize, VirtualSize, 실행 중, 일시 중지됨, 중지됨, 실패, SourceSystem, ImageID, TotalContainer |
| 컨테이너 로그 | `Type=ContainerLog` | TimeGenerated, 컴퓨터, 이미지 ID, 컨테이너 이름, LogEntrySource, LogEntry, SourceSystem, ContainerID |
| 컨테이너 서비스 로그 | `Type=ContainerServiceLog`  | TimeGenerated, 컴퓨터, TimeOfCommand, 이미지, 명령, SourceSystem, ContainerID |

## <a name="monitor-containers"></a>모니터 컨테이너
OMS 포털에서 솔루션을 사용하도록 설정한 후에는 컨테이너 호스트와 호스트에서 실행 중인 컨테이너에 대한 요약 정보를 표시하는 **컨테이너** 타일이 나타납니다.

![컨테이너 타일](./media/log-analytics-containers/containers-title.png)

타일은 환경에 있는 컨테이너의 수와, 해당 컨테이너의 실패, 실행 중 또는 중지 여부를 보여 줍니다.

### <a name="using-the-containers-dashboard"></a>컨테이너 대시보드 사용
**컨테이너** 타일을 클릭합니다. 여기에서는 다음에 따라 구성된 보기를 확인할 수 있습니다.

* 컨테이너 이벤트
* 오류
* 컨테이너 상태
* 컨테이너 이미지 인벤토리
* CPU 및 메모리 성능

대시보드의 각 창은 수집된 데이터에서 실행되는 검색을 시각적으로 나타냅니다.

![컨테이너 대시보드 ](./media/log-analytics-containers/containers-dash01.png)

![컨테이너 대시보드 ](./media/log-analytics-containers/containers-dash02.png)

**컨테이너 상태** 블레이드에서 아래 그림과 같이 위쪽 영역을 클릭합니다.

![컨테이너 상태](./media/log-analytics-containers/containers-status.png)

로그 검색이 열리고 호스트와, 호스트에서 실행 중인 컨테이너에 대한 정보를 표시합니다.

![컨테이너에 대한 로그 검색](./media/log-analytics-containers/containers-log-search.png)

여기에서 검색 쿼리를 편집 수정하여 관심 있는 특정 정보를 찾을 수 있습니다. 로그 검색에 대한 자세한 내용은 [Log Analytics의 로그 검색](log-analytics-log-searches.md)을 참조하세요.

예를 들어, 검색 쿼리에서 **Running**을 **Stopped**로 변경하여 실행 중인 컨테이너 대신 중지된 모든 컨테이너를 표시하도록 검색 쿼리를 수정할 수 있습니다.

## <a name="troubleshoot-by-finding-a-failed-container"></a>실패한 컨테이너를 검색하여 문제 해결
0이 아닌 종료 코드로 종료된 경우 OMS는 이 컨테이너를 **Failed**로 표시합니다. **실패한 컨테이너** 블레이드에서 환경의 오류 및 실패 개요를 볼 수 있습니다.

### <a name="to-find-failed-containers"></a>실패한 컨테이너 찾기
1. **컨테이너 이벤트** 블레이드를 클릭합니다.  
   ![컨테이너 이벤트](./media/log-analytics-containers/containers-events.png)
2. 로그 검색이 열리고 다음과 유사한 컨테이너 상태를 표시합니다.  
   ![컨테이너 상태](./media/log-analytics-containers/containers-container-state.png)
3. 다음으로 실패한 값을 클릭하여 이미지 크기, 중지 및 실패한 이미지 수와 같은 추가 정보를 확인합니다. **자세히 표시**를 확장하여 이미지 ID를 봅니다.  
   ![실패한 컨테이너](./media/log-analytics-containers/containers-state-failed.png)
4. 다음으로 이 이미지를 실행 중인 컨테이너를 찾습니다. 검색 쿼리에 다음을 입력합니다.
   `Type=ContainerInventory <ImageID>` 로그를 표시합니다. 스크롤하여 실패한 컨테이너를 볼 수 있습니다.  
   ![실패한 컨테이너](./media/log-analytics-containers/containers-failed04.png)

## <a name="search-logs-for-container-data"></a>컨테이너 데이터에 대한 로그 검색
특정 오류 문제를 해결할 때는 환경 내 발생 위치를 확인하는 것이 도움이 될 수 있습니다. 다음 로그 유형은 원하는 정보를 반환하는 쿼리를 만드는 데 도움이 됩니다.

* **ContainerInventory** – 컨테이너 위치, 해당 컨테이너 이름, 실행 중인 이미지에 대한 정보가 필요할 때 이 유형을 사용합니다.
* **ContainerImageInventory** – 이미지별로 정리하여 정보를 찾고 이미지 ID나 크기 같은 이미지 정보를 보려는 경우 이 유형을 사용합니다.
* **ContainerLog** – 특정 오류 로그 정보와 항목을 찾으려 할 때 이 유형을 사용합니다.
* **ContainerServiceLog** – 시작, 중지, 삭제, 끌어오기 명령 등과 같이 Docker 데몬의 감사 추적 정보를 찾으려 할 때 이 유형을 사용합니다. 

### <a name="to-search-logs-for-container-data"></a>컨테이너 데이터에 대한 로그 검색
* 최근에 실패했다고 알고 있는 이미지를 선택하고 그에 대한 오류 로그를 찾습니다. **ContainerInventory** 검색을 통해 해당 이미지를 실행 중인 컨테이너 이름부터 찾습니다. 예를 들어, `Type=ContainerInventory ubuntu Failed`를 검색합니다.  
    ![Ubuntu 컨테이너에 대한 검색](./media/log-analytics-containers/search-ubuntu.png)

  **이름** 옆에 컨테이너 이름을 확인하고 해당 로그를 검색합니다. 이 예제에서는 `Type=ContainerLog adoring_meitner`입니다.

**성능 정보 보기**

쿼리 작성을 시작할 때는 먼저 가능한 항목을 확인하는 것이 도움이 될 수 있습니다. 예를 들어, 모든 성능 데이터를 보려면 다음 검색 쿼리를 입력하여 광범위 쿼리를 실행합니다.

```
Type=Perf
```

![컨테이너 성능](./media/log-analytics-containers/containers-perf01.png)

결과에서 **메트릭**이라는 단어를 클릭하면 더 그래픽적인 형태로 이 내용을 확인할 수 있습니다.

![컨테이너 성능](./media/log-analytics-containers/containers-perf02.png)

쿼리 오른쪽에 이름을 입력하여 확인하는 성능 데이터의 범위를 특정 데이터로 한정할 수 있습니다.

```
Type=Perf <containerName>
```

그러면 개별 컨테이너에 대해 수집된 성능 메트릭 목록이 표시됩니다.

![컨테이너 성능](./media/log-analytics-containers/containers-perf03.png)

## <a name="example-log-search-queries"></a>로그 검색 쿼리 예제
한두 가지 예제로 쿼리 구성을 시작한 다음 환경에 맞게 수정하는 것이 유용한 경우가 종종 있습니다. 먼저 **주목할 만한 쿼리** 블레이드에서 테스트하면 고급 쿼리를 구성하는 데 도움이 될 수 있습니다.

![컨테이너 쿼리](./media/log-analytics-containers/containers-queries.png)

## <a name="saving-log-search-queries"></a>로그 검색 쿼리 저장
쿼리 저장은 Log Analytics의 표준 기능입니다. 이를 저장해 두면 향후 유용하게 사용할 수 있습니다.

만든 쿼리가 유용하다고 생각되면 로그 검색 페이지 위쪽의 **즐겨찾기**를 클릭하여 저장합니다. 그러면 나중에 **내 대시보드** 페이지에서 간편하게 액세스할 수 있습니다.

## <a name="next-steps"></a>다음 단계
* [로그를 검색](log-analytics-log-searches.md) 하여 자세한 컨테이너 데이터 레코드를 볼 수 있습니다.

