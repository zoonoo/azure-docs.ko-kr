---
title: Azure Monitor에 Linux 컴퓨터 연결 | Microsoft Docs
description: 이 문서에서는 다른 클라우드 또는 온-프레미스에서 호스트 되는 Linux 컴퓨터를 Linux 용 Log Analytics 에이전트와 Azure Monitor 연결 하는 방법을 설명 합니다.
ms.subservice: logs
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 01/21/2020
ms.openlocfilehash: 40c279a4beee9fbebe2de7f272fe51d9039f071c
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/27/2020
ms.locfileid: "77668709"
---
# <a name="connect-linux-computers-to-azure-monitor"></a>Azure Monitor에 Linux 컴퓨터 연결

Azure Monitor를 사용 하 여 로컬 데이터 센터 또는 다른 클라우드 환경에서 가상 컴퓨터 또는 물리적 컴퓨터를 모니터링 하 고 관리 하려면 Log Analytics 에이전트를 배포 하 고 Log Analytics 작업 영역에 보고 하도록 구성 해야 합니다. 또한 에이전트는 Azure Automation에 대한 Hybrid Runbook Worker 역할을 지원합니다.

다음 방법 중 하나를 사용 하 여 Linux 용 Log Analytics agent를 설치할 수 있습니다. 각 방법에 대한 자세한 내용은 문서의 뒷부분에 제공됩니다.

* 에이전트를 [수동으로 다운로드 하 여 설치](#install-the-agent-manually) 합니다. 이는 Linux 컴퓨터가 인터넷에 액세스할 수 없고 Azure Monitor 또는 Azure Automation [Log Analytics 게이트웨이](gateway.md)를 통해 통신 하는 경우에 필요 합니다. 
* GitHub에 호스트 된 [래퍼 스크립트를 사용 하 여 Linux 용 에이전트를 설치](#install-the-agent-using-wrapper-script) 합니다. 컴퓨터가 인터넷에 직접 연결 되어 있거나 프록시 서버를 통해 연결 되어 있는 경우 에이전트를 설치 하 고 업그레이드 하는 데 권장 되는 방법입니다.

지원되는 구성을 이해하려면 [지원되는 Linux 운영 체제](log-analytics-agent.md#supported-linux-operating-systems) 및 [네트워크 방화벽 구성](log-analytics-agent.md#network-firewall-requirements)을 검토합니다.

>[!NOTE]
>Linux용 Log Analytics 에이전트는 둘 이상의 Log Analytics 작업 영역에 보고하도록 구성할 수 없습니다. System Center Operations Manager 관리 그룹과 Log Analytics 작업 영역을 동시에 보고 하거나 개별적으로 보고 하도록 구성할 수 있습니다.

## <a name="agent-install-package"></a>에이전트 설치 패키지

Linux 용 Log Analytics 에이전트는 여러 패키지로 구성 됩니다. 릴리스 파일에는 `--extract` 매개 변수와 함께 셸 번들을 실행 하 여 사용할 수 있는 다음과 같은 패키지가 포함 되어 있습니다.

**패키지** | **버전** | **설명**
----------- | ----------- | --------------
omsagent | 1.12.15 | Linux 용 Log Analytics 에이전트
omsconfig | 1.1.1 | Log Analytics 에이전트에 대 한 구성 에이전트
omi | 1.6.3 | OMI (Open Management Infrastructure) – 경량 CIM 서버입니다. *OMI에는 서비스의 작동에 필요한 cron 작업을 실행 하는 데 필요한 루트 액세스가 필요 합니다.*
scx | 1.6.3 | 운영 체제 성능 메트릭용 OMI CIM 공급자
apache-cimprov | 1.0.1 | OMI용 Apache HTTP 서버 성능 모니터링 공급자. Apache HTTP 서버가 감지되는 경우에만 설치됨.
mysql-cimprov | 1.0.1 | OMI용 MySQL 서버 성능 모니터링 공급자. MySQL/MariaDB 서버가 감지되는 경우에만 설치됨.
docker-cimprov | 1.0.0 | OMI용 Docker 공급자. Docker가 감지되는 경우에만 설치됨.

### <a name="agent-installation-details"></a>에이전트 설치 세부 정보

Linux 패키지용 Log Analytics 에이전트를 설치한 후 다음과 같은 추가 시스템 차원 구성 변경 내용이 적용 됩니다. 이러한 아티팩트는 omsagent 패키지가 제거되면 제거됩니다.

* `omsagent` 라는 이름의 권한 없는 사용자가 생성됩니다. 데몬이이 자격 증명으로 실행 됩니다. 
* Sudoers *include* 파일은 `/etc/sudoers.d/omsagent`에서 생성 됩니다. 이 `omsagent` syslog 및 omsagent 디먼를 다시 시작할 수 있는 권한을 부여 합니다. Sudo *include* 지시문이 설치 된 버전의 sudo에서 지원 되지 않는 경우 이러한 항목이 `/etc/sudoers`에 기록 됩니다.
* syslog 구성은 이벤트 하위 집합을 에이전트에 전달하도록 수정됩니다. 자세한 내용은 [Syslog 데이터 수집 구성](data-sources-syslog.md)을 참조 하세요.

모니터링 되는 Linux 컴퓨터에서 에이전트는 `omsagent`으로 표시 됩니다. `omsconfig`은 5 분 마다 새 포털 측 구성을 검색 하는 Linux 구성 에이전트의 Log Analytics 에이전트입니다. 새 구성과 업데이트 된 구성은 `/etc/opt/microsoft/omsagent/conf/omsagent.conf`에 있는 에이전트 구성 파일에 적용 됩니다.

## <a name="obtain-workspace-id-and-key"></a>작업 영역 ID 및 키 가져오기

Linux용 Log Analytics 에이전트를 설치하기 전에 Log Analytics 작업 영역에 대한 작업 영역 ID 및 키가 필요합니다. 이 정보는 에이전트를 설치 하 여 올바르게 구성 하 고 Azure Monitor와 성공적으로 통신할 수 있는지 확인 하는 데 필요 합니다.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]  

1. Azure Portal의 왼쪽 위 모서리에서 **모든 서비스**를 선택합니다. 검색 상자에서 **Log Analytics**를 입력합니다. 입력하는 대로 입력 내용에 따라 목록이 필터링됩니다. **Log Analytics 작업 영역**을 선택합니다.

2. Log Analytics 작업 영역 목록에서 이전에 만든 작업 영역을 선택합니다. (이름은 **DefaultLAWorkspace**로 지정했을 수 있습니다.)

3. **고급 설정**을 선택합니다.

    ![Azure Portal의 Log Analytics에 대한 고급 설정 메뉴](../learn/media/quick-collect-azurevm/log-analytics-advanced-settings-azure-portal.png) 
 
4. **연결된 원본**을 선택한 다음 **Linux 서버**를 선택합니다.

5. **작업 영역 ID** 및 **기본 키**의 오른쪽에 값이 있습니다. 두 항목을 복사하여 선호하는 편집기에 붙여넣습니다.

## <a name="install-the-agent-manually"></a>에이전트 수동 설치

Linux 용 Log Analytics 에이전트는 자동 압축 풀기 및 설치 가능한 셸 스크립트 번들로 제공 됩니다. 이 번들은 각 에이전트 구성 요소에 대한 Debian 및 RPM 패키지를 포함하며, 직접 설치되거나 압축이 풀린 후에 개별 패키지를 검색하는 데 사용될 수 있습니다. 하나의 번들은 x64 및 x86 아키텍처용으로 제공 됩니다. 

> [!NOTE]
> Azure Vm의 경우 Linux 용 [azure LOG ANALYTICS VM 확장](../../virtual-machines/extensions/oms-linux.md) 을 사용 하 여 에이전트를 설치 하는 것이 좋습니다. 

1. Scp/sftp를 사용 하 여 적절 한 번들 (x64 또는 x86)을 [다운로드](https://github.com/microsoft/OMS-Agent-for-Linux#azure-install-guide) 하 고 Linux VM 또는 물리적 컴퓨터에 전송 합니다.

2. `--install` 인수를 사용 하 여 번들을 설치 합니다. 설치 하는 동안 Log Analytics 작업 영역에 등록 하려면 앞에서 복사한 `-w <WorkspaceID>` 및 `-s <workspaceKey>` 매개 변수를 제공 합니다.

    >[!NOTE]
    >Linux 용 system Center Operations Manager 에이전트가 이미 설치 되어 있는 경우 처럼 omi, scx, omsconfig 또는 이전 버전과 같은 종속 패키지가 설치 된 경우에는 `--upgrade` 인수를 사용 해야 합니다. 

    ```
    sudo sh ./omsagent-*.universal.x64.sh --install -w <workspace id> -s <shared key>
    ```

3. Log Analytics 게이트웨이를 통해 Log Analytics 작업 영역에 설치 하 고 연결 하도록 Linux 에이전트를 구성 하려면 프록시, 작업 영역 ID 및 작업 영역 키 매개 변수를 제공 하는 다음 명령을 실행 합니다. 이 구성은 `-p [protocol://][user:password@]proxyhost[:port]`를 포함 하 여 명령줄에서 지정할 수 있습니다. *Proxyhost* 속성은 Log Analytics 게이트웨이 서버의 정규화 된 도메인 이름 또는 IP 주소를 허용 합니다.  

    ```
    sudo sh ./omsagent-*.universal.x64.sh --upgrade -p https://<proxy address>:<proxy port> -w <workspace id> -s <shared key>
    ```

    인증이 필요한 경우 사용자 이름 및 암호를 지정 해야 합니다. 다음은 그 예입니다. 
    
    ```
    sudo sh ./omsagent-*.universal.x64.sh --upgrade -p https://<proxy user>:<proxy password>@<proxy address>:<proxy port> -w <workspace id> -s <shared key>
    ```

4. Azure Government 클라우드의 Log Analytics 작업 영역에 연결 하도록 Linux 컴퓨터를 구성 하려면 앞에서 복사한 작업 영역 ID 및 기본 키를 제공 하는 다음 명령을 실행 합니다.

    ```
    sudo sh ./omsagent-*.universal.x64.sh --upgrade -w <workspace id> -s <shared key> -d opinsights.azure.us
    ```

에이전트 패키지를 설치 하 고 나중에 특정 Log Analytics 작업 영역에 보고 하도록 구성 하려면 다음 명령을 실행 합니다.

```
sudo sh ./omsagent-*.universal.x64.sh --upgrade
```

에이전트를 설치 하지 않고 번들에서 에이전트 패키지를 추출 하려면 다음 명령을 실행 합니다.

```
sudo sh ./omsagent-*.universal.x64.sh --extract
```

## <a name="install-the-agent-using-wrapper-script"></a>래퍼 스크립트를 사용 하 여 에이전트 설치

다음 단계는 Azure 및 Azure Government 클라우드에서 Log Analytics에 대 한 에이전트의 설치를 구성 하 고,이를 통해 직접 또는 프록시 서버를 통해 통신 하 여 GitHub에 호스트 된 에이전트를 다운로드 하 고 에이전트를 설치 합니다.  

Linux 컴퓨터가 프록시 서버를 통해 Log Analytics를 통해 통신 해야 하는 경우 `-p [protocol://][user:password@]proxyhost[:port]`을 포함 하 여 명령줄에서이 구성을 지정할 수 있습니다. *프로토콜* 속성은 `http` 또는 `https`를 허용 하 고, *proxyhost* 속성은 프록시 서버의 정규화 된 도메인 이름 또는 IP 주소를 허용 합니다. 

예: `https://proxy01.contoso.com:30443`

두 경우 모두 인증이 필요한 경우 사용자 이름 및 암호를 지정 해야 합니다. 예: `https://user01:password@proxy01.contoso.com:30443`

1. Log Analytics 작업 영역에 연결 하도록 Linux 컴퓨터를 구성 하려면 작업 영역 ID 및 기본 키를 제공 하는 다음 명령을 실행 합니다. 다음 명령은 에이전트를 다운로드하고, 해당 체크섬의 유효성을 검사한 다음, 설치합니다.
    
    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY>
    ```

    다음 명령에는 프록시 서버에서 인증이 필요한 경우 `-p` 프록시 매개 변수와 예제 구문이 포함되어 있습니다.

   ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -p [protocol://]<proxy user>:<proxy password>@<proxyhost>[:port] -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY>
    ```

2. Azure Government 클라우드에서 Linux 컴퓨터를 Log Analytics 작업 영역에 연결하도록 구성하려면 앞에서 복사한 작업 영역 ID 및 기본 키를 제공하는 다음 명령을 실행합니다. 다음 명령은 에이전트를 다운로드하고, 해당 체크섬의 유효성을 검사한 다음, 설치합니다. 

    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY> -d opinsights.azure.us
    ``` 

    다음 명령에는 프록시 서버에서 인증이 필요한 경우 `-p` 프록시 매개 변수와 예제 구문이 포함되어 있습니다.

   ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -p [protocol://]<proxy user>:<proxy password>@<proxyhost>[:port] -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY> -d opinsights.azure.us
    ```
2. 다음 명령을 실행하여 에이전트를 다시 시작합니다. 

    ```
    sudo /opt/microsoft/omsagent/bin/service_control restart [<workspace id>]
    ``` 

## <a name="upgrade-from-a-previous-release"></a>이전 릴리스에서 업그레이드

버전 1.0.0-47부터 이전 버전에서 업그레이드 하는 것은 각 릴리스에서 지원 됩니다. `--upgrade` 매개 변수를 사용 하 여 설치를 수행 하 여 에이전트의 모든 구성 요소를 최신 버전으로 업그레이드 합니다.

## <a name="next-steps"></a>다음 단계

- 가상 머신에서 에이전트를 다시 구성, 업그레이드 또는 제거 하는 방법에 대 한 자세한 내용은 [Windows 및 Linux 용 Log Analytics 에이전트 관리 및 유지](agent-manage.md) 관리를 검토 하세요.

- 에이전트를 설치하거나 관리하는 중에 문제가 발생하면 [Troubleshooting the Linux agent](agent-linux-troubleshoot.md)(Linux 에이전트 문제 해결)를 참조하세요.
