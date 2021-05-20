---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 08/30/2020
ms.author: alkohli
ms.openlocfilehash: 92ccb6127e624ace9e719ffd23324b3a1b971f72
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "89272159"
---
컴퓨팅 역할이 구성된 Azure Stack Edge 디바이스에서 두 가지 다른 명령 세트를 사용하여 문제를 해결하거나 디바이스를 모니터링할 수 있습니다.

- `iotedge` 명령 사용. 이러한 명령은 디바이스에 대한 기본 작업에 사용할 수 있습니다.
- `dkrdbe` 명령 사용. 이러한 명령은 디바이스에 대한 광범위한 작업에 사용할 수 있습니다.

위의 명령 세트 중 하나를 실행하려면 [PowerShell 인터페이스에 연결](#connect-to-the-powershell-interface)해야 합니다.

### <a name="use-iotedge-commands"></a>`iotedge` 명령 사용

사용 가능한 명령 목록을 보려면 [PowerShell 인터페이스에 연결](#connect-to-the-powershell-interface)하고 `iotedge` 함수를 사용합니다.

```powershell
[10.100.10.10]: PS>iotedge -?                                                                                                                                                                                                 Usage: iotedge COMMAND

Commands:
   check
   list
   logs
   restart

[10.100.10.10]: PS>
```

다음 표에서는 `iotedge`에 사용할 수 있는 명령에 대해 간략히 설명합니다.

|명령을 사용합니다.  |설명 |
|---------|---------|
|`check`     | 일반적인 구성 및 연결 문제에 대한 자동화된 검사 수행       |
|`list`     | 모듈 목록 표시         |
|`logs`     | 모듈 로그 가져오기        |
|`restart`     | 모듈 중지 및 다시 시작         |


### <a name="use-dkrdbe-commands"></a>`dkrdbe` 명령 사용

사용 가능한 명령 목록을 보려면 [PowerShell 인터페이스에 연결](#connect-to-the-powershell-interface)하고 `dkrdbe` 함수를 사용합니다.

```powershell
[10.100.10.10]: PS>dkrdbe -?
Usage: dkrdbe COMMAND

Commands:
   image [prune]
   images
   inspect
   login
   logout
   logs
   port
   ps
   pull
   start
   stats
   stop
   system [df]
   top

[10.100.10.10]: PS>
```
다음 표에서는 `dkrdbe`에 사용할 수 있는 명령에 대해 간략히 설명합니다.

|명령을 사용합니다.  |설명 |
|---------|---------|
|`image`     | 이미지 관리. 사용하지 않는 이미지를 제거하려면 `dkrdbe image prune -a -f`를 사용합니다.       |
|`images`     | 이미지 나열         |
|`inspect`     | Docker 개체에 대한 하위 수준 정보 반환         |
|`login`     | Docker 레지스트리에 로그인         |
|`logout`     | Docker 레지스트리에서 로그아웃         |
|`logs`     | 컨테이너 로그 가져오기        |
|`port`     | 컨테이너에 대한 포트 매핑 또는 특정 매핑 나열        |
|`ps`     | 컨테이너 나열        |
|`pull`     | 레지스트리에서 이미지 또는 리포지토리 끌어오기         |
|`start`     | 하나 이상의 중지된 컨테이너 시작         |
|`stats`     | 컨테이너 리소스 사용량 통계의 라이브 스트림 표시         |
|`stop`     | 하나 이상의 실행 중인 컨테이너 중지        |
|`system`     | Docker 관리         |
|`top`     | 실행 중인 컨테이너 프로세스 표시         |

사용 가능한 명령에 대한 도움말을 보려면 `dkrdbe <command-name> --help`를 사용합니다.

예를 들어 `port` 명령을 사용하는 방법을 이해하려면 다음을 입력합니다.

```powershell
[10.100.10.10]: P> dkrdbe port --help

Usage:  dkr port CONTAINER [PRIVATE_PORT[/PROTO]]

List port mappings or a specific mapping for the container
[10.100.10.10]: P> dkrdbe login --help

Usage:  docker login [OPTIONS] [SERVER]

Log in to a Docker registry.
If no server is specified, the default is defined by the daemon.

Options:
  -p, --password string   Password
      --password-stdin    Take the password from stdin
  -u, --username string   Username
[10.100.10.10]: PS>
```

`dkrdbe` 함수에 사용할 수 있는 명령은 일반 docker 명령에 사용되는 것과 동일한 매개 변수를 사용합니다. docker 명령과 함께 사용되는 옵션 및 매개 변수를 보려면 [Docker 명령줄 사용](https://docs.docker.com/engine/reference/commandline/docker/)으로 이동합니다.

### <a name="to-check-if-the-module-deployed-successfully"></a>모듈이 성공적으로 배포되었는지 확인

컴퓨팅 모듈은 비즈니스 논리가 구현된 컨테이너입니다. 컴퓨팅 모듈이 성공적으로 배포되었는지 확인하려면 `ps` 명령을 실행하고 컴퓨팅 모듈에 해당하는 컨테이너가 실행 중인지 확인합니다.

일시 중지된 컨테이너를 포함하여 모든 컨테이너의 목록을 가져오려면 `ps -a` 명령을 실행합니다.

```powershell
[10.100.10.10]: P> dkrdbe ps -a
CONTAINER ID        IMAGE                                                COMMAND                   CREATED             STATUS              PORTS                                                                  NAMES
d99e2f91d9a8        edgecompute.azurecr.io/filemovemodule2:0.0.1-amd64   "dotnet FileMoveModuâ€¦"    2 days ago          Up 2 days                                                                                  movefile
0a06f6d605e9        edgecompute.azurecr.io/filemovemodule2:0.0.1-amd64   "dotnet FileMoveModuâ€¦"    2 days ago          Up 2 days                                                                                  filemove
2f8a36e629db        mcr.microsoft.com/azureiotedge-hub:1.0               "/bin/sh -c 'echo \"$â€¦"   2 days ago          Up 2 days           0.0.0.0:443->443/tcp, 0.0.0.0:5671->5671/tcp, 0.0.0.0:8883->8883/tcp   edgeHub
acce59f70d60        mcr.microsoft.com/azureiotedge-agent:1.0             "/bin/sh -c 'echo \"$â€¦"   2 days ago          Up 2 days                                                                                  edgeAgent
[10.100.10.10]: PS>
```

컨테이너 이미지를 만들 때 또는 이미지를 끌어오는 동안 오류가 발생한 경우 `logs edgeAgent`를 실행합니다.  `EdgeAgent`는 다른 컨테이너를 프로비전하는 IoT Edge 런타임 컨테이너입니다.

`logs edgeAgent`는 모든 로그를 덤프하므로 최근 오류를 확인할 수 있는 좋은 방법은 `--tail 20` 옵션을 사용하는 것입니다.


```powershell
[10.100.10.10]: PS>dkrdbe logs edgeAgent --tail 20
2019-02-28 23:38:23.464 +00:00 [DBG] [Microsoft.Azure.Devices.Edge.Util.Uds.HttpUdsMessageHandler] - Connected socket /var/run/iotedge/mgmt.sock
2019-02-28 23:38:23.464 +00:00 [DBG] [Microsoft.Azure.Devices.Edge.Util.Uds.HttpUdsMessageHandler] - Sending request http://mgmt.sock/modules?api-version=2018-06-28
2019-02-28 23:38:23.464 +00:00 [DBG] [Microsoft.Azure.Devices.Edge.Agent.Core.Agent] - Getting edge agent config...
2019-02-28 23:38:23.464 +00:00 [DBG] [Microsoft.Azure.Devices.Edge.Agent.Core.Agent] - Obtained edge agent config
2019-02-28 23:38:23.469 +00:00 [DBG] [Microsoft.Azure.Devices.Edge.Agent.Edgelet.ModuleManagementHttpClient] - Received a valid Http response from unix:///var/run/iotedge/mgmt.soc
k for List modules
--------------------CUT---------------------
--------------------CUT---------------------
08:28.1007774+00:00","restartCount":0,"lastRestartTimeUtc":"2019-02-26T20:08:28.1007774+00:00","runtimeStatus":"running","version":"1.0","status":"running","restartPolicy":"always
","type":"docker","settings":{"image":"edgecompute.azurecr.io/filemovemodule2:0.0.1-amd64","imageHash":"sha256:47778be0602fb077d7bc2aaae9b0760fbfc7c058bf4df192f207ad6cbb96f7cc","c
reateOptions":"{\"HostConfig\":{\"Binds\":[\"/home/hcsshares/share4-dl460:/home/input\",\"/home/hcsshares/share4-iot:/home/output\"]}}"},"env":{}}
2019-02-28 23:38:28.480 +00:00 [DBG] [Microsoft.Azure.Devices.Edge.Agent.Core.Planners.HealthRestartPlanner] - HealthRestartPlanner created Plan, with 0 command(s).
```

### <a name="to-get-container-logs"></a>컨테이너 로그 가져오기

특정 컨테이너에 대한 로그를 가져오려면 먼저 컨테이너를 나열한 다음, 관심 있는 컨테이너에 대한 로그를 가져옵니다.

1. [PowerShell 인터페이스에 연결하세요](#connect-to-the-powershell-interface).
2. 실행 중인 컨테이너 목록을 가져오려면 `ps` 명령을 실행합니다.

    ```powershell
    [10.100.10.10]: P> dkrdbe ps
    CONTAINER ID        IMAGE                                                COMMAND                   CREATED             STATUS              PORTS                                                                  NAMES
    d99e2f91d9a8        edgecompute.azurecr.io/filemovemodule2:0.0.1-amd64   "dotnet FileMoveModuâ€¦"    2 days ago          Up 2 days                                                                                  movefile
    0a06f6d605e9        edgecompute.azurecr.io/filemovemodule2:0.0.1-amd64   "dotnet FileMoveModuâ€¦"    2 days ago          Up 2 days                                                                                  filemove
    2f8a36e629db        mcr.microsoft.com/azureiotedge-hub:1.0               "/bin/sh -c 'echo \"$â€¦"   2 days ago          Up 2 days           0.0.0.0:443->443/tcp, 0.0.0.0:5671->5671/tcp, 0.0.0.0:8883->8883/tcp   edgeHub
    acce59f70d60        mcr.microsoft.com/azureiotedge-agent:1.0             "/bin/sh -c 'echo \"$â€¦"   2 days ago          Up 2 days                                                                                  edgeAgent
    ```

3. 로그가 필요한 컨테이너의 컨테이너 ID를 적어 둡니다.

4. 특정 컨테이너에 대한 로그를 가져오려면 컨테이너 ID를 제공하는 `logs` 명령을 실행합니다.

    ```powershell
    [10.100.10.10]: PS>dkrdbe logs d99e2f91d9a8
    02/26/2019 18:21:45: Info: Opening module client connection.
    02/26/2019 18:21:46: Info: Initializing with input: /home/input, output: /home/output.
    02/26/2019 18:21:46: Info: IoT Hub module client initialized.
    02/26/2019 18:22:24: Info: Received message: 1, SequenceNumber: 0 CorrelationId: , MessageId: 081886a07e694c4c8f245a80b96a252a Body: [{"ChangeType":"Created","ShareRelativeFilePath":"\\__Microsoft Data Box Edge__\\Upload\\Errors.xml","ShareName":"share4-dl460"}]
    02/26/2019 18:22:24: Info: Moving input file: /home/input/__Microsoft Data Box Edge__/Upload/Errors.xml to /home/output/__Microsoft Data Box Edge__/Upload/Errors.xml
    02/26/2019 18:22:24: Info: Processed event.
    02/26/2019 18:23:38: Info: Received message: 2, SequenceNumber: 0 CorrelationId: , MessageId: 30714d005eb048e7a4e7e3c22048cf20 Body: [{"ChangeType":"Created","ShareRelativeFilePath":"\\f [10]","ShareName":"share4-dl460"}]
    02/26/2019 18:23:38: Info: Moving input file: /home/input/f [10] to /home/output/f [10]
    02/26/2019 18:23:38: Info: Processed event.
    ```

### <a name="to-monitor-the-usage-statistics-of-the-device"></a>디바이스의 사용량 통계 모니터링

디바이스의 메모리, CPU 사용량 및 I/O를 모니터링하려면 `stats` 명령을 사용합니다.

1. [PowerShell 인터페이스에 연결하세요](#connect-to-the-powershell-interface).
2. `stats` 명령을 실행하여 라이브 스트림을 사용하지 않도록 설정하고 첫 번째 결과만 끌어옵니다.

   ```powershell
   dkrdbe stats --no-stream
   ```

   다음 예제에서는 이 cmdlet의 사용법을 보여 줍니다.

    ```
    [10.100.10.10]: P> dkrdbe stats --no-stream
    CONTAINER ID        NAME          CPU %         MEM USAGE / LIMIT     MEM %         NET I/O             BLOCK I/O           PIDS
    d99e2f91d9a8        movefile      0.0           24.4MiB / 62.89GiB    0.04%         751kB / 497kB       299kB / 0B          14
    0a06f6d605e9        filemove      0.00%         24.11MiB / 62.89GiB   0.04%         679kB / 481kB       49.5MB / 0B         14
    2f8a36e629db        edgeHub       0.18%         173.8MiB / 62.89GiB   0.27%         4.58MB / 5.49MB     25.7MB / 2.19MB     241
    acce59f70d60        edgeAgent     0.00%         35.55MiB / 62.89GiB   0.06%         2.23MB / 2.31MB     55.7MB / 332kB      14
    [10.100.10.10]: PS>
    ```

