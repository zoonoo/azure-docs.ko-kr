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
ms.date: 06/29/2017
ms.author: banders
ms.translationtype: Human Translation
ms.sourcegitcommit: 1500c02fa1e6876b47e3896c40c7f3356f8f1eed
ms.openlocfilehash: 936064959ac9dd6422619076fabbbba887d17bb6
ms.contentlocale: ko-kr
ms.lasthandoff: 06/30/2017


---
# <a name="containers-preview-solution-in-log-analytics"></a>Log Analytics에서 컨테이너(미리 보기) 솔루션

![컨테이너 기호](./media/log-analytics-containers/containers-symbol.png)

이 문서에서는 단일 위치에서 Docker 및 Windows 컨테이너 호스트를 보고 관리할 수 있게 Log Analytics의 컨테이너 솔루션을 설정 및 사용하는 방법을 설명합니다. Docker는 IT 인프라에 대한 소프트웨어 배포를 자동화하는 컨테이너를 만드는 데 사용되는 소프트웨어 가상화 시스템입니다.

이 솔루션을 통해 컨테이너 호스트에서 어떤 컨테이너가 실행 중이며 컨테이너에서 어떤 이미지가 실행 중인지 확인할 수 있습니다. 컨테이너에 사용하는 명령을 표시하는 상세한 감사 정보를 확인할 수 있습니다. 또한 중앙화된 로그를 보고 검색하면 원격으로 Docker 또는 Windows 호스트를 보지 않고도 컨테이너의 문제를 해결할 수 있습니다. 호스트에서 성가시고 과도한 리소스를 소모하는 컨테이너를 찾을 수 있습니다. 또한 컨테이너에 대해 중앙화된 CPU 메모리, 저장소, 네트워크 사용 및 성능 정보를 확인할 수 있습니다. Windows를 실행하는 컴퓨터에서 Windows Server, Hyper-V, Docker 컨테이너에서 로그를 중앙 집중화 및 비교할 수 있습니다.

다음 다이어그램에서는 OMS에서 다양한 컨테이너 호스트와 에이전트 간의 관계를 보여 줍니다.

![컨테이너 다이어그램](./media/log-analytics-containers/containers-diagram.png)

## <a name="installing-and-configuring-the-solution"></a>솔루션 설치 및 구성
다음 정보를 사용하여 솔루션을 설치하고 구성합니다.

[Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.ContainersOMS?tab=Overview)에서 또는 [솔루션 갤러리에서 Log Analytics 솔루션 추가](log-analytics-add-solutions.md)에서 설명하는 프로세스를 사용하여 OMS 작업 영역에 컨테이너 솔루션을 추가합니다.

다음 몇 가지 방법으로 OMS에서 Docker를 설치 및 사용할 수 있습니다.

* 지원되는 Linux 운영 체제에서 Docker를 설치 및 실행한 다음 OMS Agent for Linux를 설치 및 구성합니다.
* CoreOS에서는 Linux 용 OMS 에이전트를 실행할 수 없습니다. 대신 컨테이너화된 Linux 용 OMS 에이전트 버전을 실행합니다.
* Windows Server 2016 및 Windows 10에서 Docker 엔진 및 클라이언트를 설치한 후 에이전트를 연결하여 정보를 수집하고 Log Analytics에 보냅니다.


[GitHub](https://github.com/Microsoft/OMS-docker)에서 해당 컨테이너 호스트에 지원되는 Docker 및 Linux 운영 체제 버전을 검토할 수 있습니다.

### <a name="container-services"></a>Container Service

- Azure Container Service를 사용하는 Kubernetes 클러스터가 있는 경우 [Microsoft OMS(Operations Management Suite)를 사용하여 Azure Container Service 클러스터 모니터링](../container-service/container-service-kubernetes-oms.md)에서 자세한 내용을 참조하세요.
- Azure Container Service DC/OS 클러스터가 있는 경우 [Operations Management Suite를 사용하여 Azure Container Service DC/OS 클러스터 모니터링](../container-service/container-service-monitoring-oms.md)에서 자세한 내용을 참조하세요.
- Docker Swarm 모드 환경에 있는 경우 [Docker Swarm용 OMS 에이전트 구성](#configure-an-oms-agent-for-docker-swarm)에서 자세히 알아보세요.
- Service Fabric과 함께 컨테이너를 사용하는 경우 [Azure Service Fabric의 개요](../service-fabric/service-fabric-overview.md)에서 자세히 알아봅니다.
- [Windows에서 Docker 엔진](https://docs.microsoft.com/virtualization/windowscontainers/manage-docker/configure-docker-daemon) 문서에서 Windows를 실행하는 컴퓨터에서 Docker 엔진을 설치하고 구성하는 방법에 대한 추가 정보를 확인합니다.

> [!IMPORTANT]
> Docker는 컨테이너 호스트에 [OMS Agent for Linux](log-analytics-linux-agents.md)를 설치하기 **전에** 실행해야 합니다.  Docker 설치에 앞서 에이전트를 설치한 경우 OMS Agent for Linux를 다시 설치해야 합니다.  Docker에 대한 자세한 내용은 [Docker 웹 사이트](https://www.docker.com)를 참조하세요.
>
>

컨테이너를 모니터링하려면 먼저 컨테이너 호스트에 다음 설정을 구성해야 합니다.

## <a name="linux-container-hosts"></a>Linux 컨테이너 호스트

지원되는 Linux 버전:

- 1.13을 통한 Docker 1.11
- Docker CE 및 EE v17.03


다음 x64 Linux 배포는 컨테이너 호스트로 지원됩니다.

- Ubuntu 14.04 LTS, 16.04 LTS
- CoreOS(stable)
- Amazon Linux 2016.09.0
- openSUSE 13.2
- openSUSE LEAP 42.2
- CentOS 7.2, 7.3
- SLES 12
- RHEL 7.2, 7.3


Docker를 설치한 후 컨테이너 호스트에 다음 설정을 사용하여 Docker에 사용할 에이전트를 구성합니다. [OMS 작업 영역 ID 및 키](log-analytics-linux-agents.md)가 필요합니다.


### <a name="for-all-linux-container-hosts-except-coreos"></a>CoreOS를 제외한 모든 Linux 컨테이너 호스트의 경우

- [OMS Agent for Linux 설치 단계](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md)의 지침을 따르세요.

### <a name="for-all-linux-container-hosts-including-coreos"></a>CoreOS를 포함한 모든 Linux 컨테이너 호스트의 경우

모니터링하려는 OMS 컨테이너를 시작합니다. 다음 예제를 수정하여 사용합니다.

```
sudo docker run --privileged -d -v /var/run/docker.sock:/var/run/docker.sock -e WSID="your workspace id" -e KEY="your key" -h=`hostname` -p 127.0.0.1:25225:25225 --name="omsagent" --restart=always microsoft/oms
```

### <a name="for-all-azure-government-linux-container-hosts-including-coreos"></a>CoreOS를 포함한 모든 Azure Government Linux 컨테이너 호스트의 경우

모니터링하려는 OMS 컨테이너를 시작합니다. 다음 예제를 수정하여 사용합니다.

```
sudo docker run --privileged -d -v /var/run/docker.sock:/var/run/docker.sock -v /var/log:/var/log -e WSID="your workspace id" -e KEY="your key" -e DOMAIN="opinsights.azure.us" -p 127.0.0.1:25225:25225 -p 127.0.0.1:25224:25224/udp --name="omsagent" -h=`hostname` --restart=always microsoft/oms
```


### <a name="switching-from-using-an-installed-linux-agent-to-one-in-a-container"></a>설치된 Linux 에이전트에서 컨테이너의 다른 에이전트로 전환
이전에 직접 설치한 에이전트를 사용하였고 이제 실행 중인 에이전트를 사용하려는 경우 먼저 OMSAgent를 제거해야 합니다. [OMS Agent for Linux 설치 단계](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md)를 참조하세요.

### <a name="configure-an-oms-agent-for-docker-swarm"></a>Docker Swarm용 OMS 에이전트 구성

Docker Swarm에서 전역 서비스로 OMS 에이전트를 실행할 수 있습니다. 다음 정보를 사용하여 OMS 에이전트 서비스를 만듭니다. 작업 영역 ID 및 기본 키를 삽입해야 합니다.

- 마스터 노드에서 다음을 실행합니다.

    ```
    sudo docker service create  --name omsagent --mode global  --mount type=bind,source=/var/run/docker.sock,destination=/var/run/docker.sock  -e WSID="<WORKSPACE ID>" -e KEY="<PRIMARY KEY>" -p 25225:25225 -p 25224:25224/udp  --restart-condition=on-failure microsoft/oms
    ```

### <a name="secure-your-secret-information-for-container-services"></a>컨테이너 서비스에 대한 비밀 정보 보호

Docker Swarm 및 Kubernetes에 대한 비밀 OMS 작업 영역 ID 및 기본 키를 보호할 수 있습니다.

#### <a name="secure-secrets-for-docker-swarm"></a>Docker Swarm에 대한 비밀 보호

Docker Swarm의 경우 작업 영역 ID 및 기본 키에 대한 비밀이 생성되면 OMSagent에 대한 Docker 서비스 만들기를 실행할 수 있습니다. 다음 정보를 사용하여 비밀 정보를 만듭니다.

1. 마스터 노드에서 다음을 실행합니다.

    ```
    echo "WSID" | docker secret create WSID -
    echo "KEY" | docker secret create KEY -
    ```

2. 비밀이 제대로 생성되었는지 확인합니다.

    ```
    keiko@swarmm-master-13957614-0:/run# sudo docker secret ls
    ```

    ```
    ID                          NAME                CREATED             UPDATED
    j2fj153zxy91j8zbcitnjxjiv   WSID                43 minutes ago      43 minutes ago
    l9rh3n987g9c45zffuxdxetd9   KEY                 38 minutes ago      38 minutes ago
    ```

3. 다음 명령을 실행하여 비밀을 컨테이너화된 OMS 에이전트에 탑재합니다.

    ```
    sudo docker service create  --name omsagent --mode global  --mount type=bind,source=/var/run/docker.sock,destination=/var/run/docker.sock --secret source=WSID,target=WSID --secret source=KEY,target=KEY  -p 25225:25225 -p 25224:25224/udp --restart-condition=on-failure microsoft/oms
    ```

#### <a name="secure-secrets-for-kubernetes-with-yaml-files"></a>yaml 파일로 Kubernetes에 대한 비밀 보호

Kubernetes의 경우 스크립트를 사용하여 작업 영역 ID 및 기본 키에 대한 비밀 .yaml 파일을 생성합니다. [OMS Docker Kubernetes GitHub](https://github.com/Microsoft/OMS-docker/tree/master/Kubernetes) 페이지에는 비밀 정보를 포함 또는 포함하지 않고 사용할 수 있는 파일이 있습니다.

- 비밀 정보가 없는 기본 OMS 에이전트 DaemonSet(omsagent.yaml)
- 비밀 yaml(omsagentsecret.yaml) 파일을 생성하는 비밀 생성 스크립트에 비밀 정보(omsagent-ds-secrets.yaml)를 사용하는 OMS 에이전트 DaemonSet yaml 파일.

비밀을 포함 또는 포함하지 않고 omsagent DaemonSet를 만들도록 선택할 수 있습니다.

##### <a name="default-omsagent-daemonset-yaml-file-without-secrets"></a>비밀이 없는 기본 OMSagent DaemonSet yaml 파일

- 기본 OMS Agent DaemonSet yaml 파일에서 `<WSID>` 및 `<KEY>`를 사용자의 WSID 및 KEY로 바꿉니다. 파일을 마스터 노드에 복사하고 다음을 실행합니다.

    ```
    sudo kubectl create -f omsagent.yaml
    ```

##### <a name="default-omsagent-daemonset-yaml-file-with-secrets"></a>비밀이 있는 기본 OMSagent DaemonSet yaml 파일

1. 비밀 정보를 사용하여 OMS 에이전트 DaemonSet을 사용하려면 먼저 비밀을 만듭니다.
    1. 스크립트 및 비밀 템플릿 파일을 복사하고 이들이 같은 디렉터리에 있는지 확인합니다.
        - 비밀 생성 스크립트 - secret-gen.sh
        - 비밀 템플릿 - secret-template.yaml
    2. 다음 예제와 같이 스크립트를 실행합니다. 스크립트에서 OMS 작업 영역 ID 및 기본 키를 요청하고 사용자가 이를 입력하면 스크립트에서 비밀 .yaml 파일을 생성하며 사용자가 실행할 수 있습니다.   

        ```
        #> sudo bash ./secret-gen.sh
        ```

    3. 다음을 실행하여 비밀 Pod를 만듭니다.
        ```
        sudo kubectl create -f omsagentsecret.yaml
        ```

    4. 확인하려면 다음을 실행합니다.

        ```
        keiko@ubuntu16-13db:~# sudo kubectl get secrets
        ```

        출력은 다음과 유사합니다.

        ```
        NAME                  TYPE                                  DATA      AGE
        default-token-gvl91   kubernetes.io/service-account-token   3         50d
        omsagent-secret       Opaque                                2         1d
        ```

        ```
        keiko@ubuntu16-13db:~# sudo kubectl describe secrets omsagent-secret
        ```

        출력은 다음과 유사합니다.

        ```
        Name:           omsagent-secret
        Namespace:      default
        Labels:         <none>
        Annotations:    <none>

        Type:   Opaque

        Data
        ====
        WSID:   36 bytes
        KEY:    88 bytes
        ```

    5. ``` sudo kubectl create -f omsagent-ds-secrets.yaml ```을 실행하여 omsagent daemon-set 만들기

2. OMS 에이전트 DaemonSet가 다음과 같이 실행 중인지 확인합니다.

    ```
    keiko@ubuntu16-13db:~# sudo kubectl get ds omsagent
    ```

    ```
    NAME       DESIRED   CURRENT   NODE-SELECTOR   AGE
    omsagent   3         3         <none>          1h
    ```


Kubernetes의 경우 스크립트를 사용하여 작업 영역 ID 및 기본 키에 대한 비밀 yaml 파일을 생성합니다. [omsagent yaml 파일](https://github.com/Microsoft/OMS-docker/blob/master/Kubernetes/omsagent.yaml)에 다음 예제 정보를 사용하여 비밀 정보를 보호합니다.

```
keiko@ubuntu16-13db:~# sudo kubectl describe secrets omsagent-secret
Name:           omsagent-secret
Namespace:      default
Labels:         <none>
Annotations:    <none>

Type:   Opaque

Data
====
WSID:   36 bytes
KEY:    88 bytes
```


## <a name="windows-container-hosts"></a>Windows 컨테이너 호스트

지원되는 Windows 버전:

- Windows Server 2016
- Windows 10 1주년 버전(Professional 또는 Enterprise)

### <a name="docker-versions-supported-on-windows"></a>Windows에서 지원되는 Docker 버전

- Docker 1.12 – 1.13
- Docker 17.03.0[안정화]

### <a name="preparation-before-installing-windows-agents"></a>Windows 에이전트 설치 전 준비

Windows를 실행하는 컴퓨터에 에이전트를 설치하기 전에 Docker 서비스를 구성해야 합니다. 구성을 통해 Windows 에이전트 또는 Log Analytics 가상 컴퓨터 확장에서 Docker TCP 소켓을 사용하도록 하여 에이전트가 Docker 데몬에 원격으로 액세스하고 모니터링할 데이터를 캡처하도록 할 수 있습니다.

#### <a name="to-start-docker-and-verify-its-configuration"></a>Docker를 시작하고 구성을 확인하려면

Windows 서버에 대한 TCP 명명된 파이프를 설정하는 데 필요한 단계입니다.

1. Windows PowerShell에서 TCP 파이프 및 명명된 파이프를 사용하도록 설정합니다.

    ```
    Stop-Service docker
    dockerd --unregister-service
    dockerd --register-service -H npipe:// -H 0.0.0.0:2375  
    Start-Service docker
    ```

2. TCP 파이프 및 명명된 파이프에 대한 구성 파일로 Docker를 구성합니다. 구성 파일은 C:\ProgramData\docker\config\daemon.json에 있습니다.

    daemon.json 파일에서 다음이 필요합니다.

    ```
    {
    "hosts": ["tcp://0.0.0.0:2375", "npipe://"]
    }
    ```

Windows 컨테이너에서 사용하는 Docker 데몬 구성에 대한 자세한 내용은 [Windows에서 Docker 엔진](https://docs.microsoft.com/virtualization/windowscontainers/manage-docker/configure-docker-daemon)을 참조하세요.


### <a name="install-windows-agents"></a>Windows 에이전트 설치

Windows 및 Hyper-V 컨테이너 모니터링을 사용하도록 설정하려면 컨테이너 호스트인 Windows 컴퓨터에 에이전트를 설치합니다. 온-프레미스 환경에서 Windows를 실행하는 컴퓨터는 [Log Analytics에 Windows 컴퓨터 연결](log-analytics-windows-agents.md)을 참조하세요. Azure에서 실행되는 가상 컴퓨터의 경우 [가상 컴퓨터 확장](log-analytics-azure-vm-extension.md)을 사용하여 Log Analytics에 연결합니다.

Service Fabric에서 실행 중인 Windows 컨테이너를 모니터링할 수 있습니다. 그러나 [Azure에서 실행 중인 가상 컴퓨터](log-analytics-azure-vm-extension.md) 및 [온-프레미스 환경에서 Windows를 실행하는 컴퓨터](log-analytics-windows-agents.md)만 현재 Service Fabric에 대해 지원됩니다.

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

다음 테이블은 컨테이너 솔루션에 의해 수집된 데이터 유형, 로그 검색에 사용된 데이터 유형 및 결과의 예를 보여줍니다.

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

