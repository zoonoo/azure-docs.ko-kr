---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 07/26/2019
ms.author: alkohli
ms.openlocfilehash: f3bb391dceb1948820d00c0d09229f2c106ffc0b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "68601356"
---
계산 역할이 구성된 Data Box Edge 장치에서 는 모듈을 모니터링하거나 문제를 해결하는 데 docker 명령의 하위 집합을 사용할 수 있습니다. 사용 가능한 명령 목록을 보려면 [PowerShell 인터페이스에 연결하고](#connect-to-the-powershell-interface) `dkrdbe` 이 기능을 사용합니다.

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
다음 표에는 다음에 사용할 수 `dkrdbe`있는 명령에 대한 간략한 설명이 있습니다.

|command  |설명 |
|---------|---------|
|`image`     | 이미지를 관리합니다. 사용하지 않는 이미지를 제거하려면 다음을 사용합니다.`dkrdbe image prune -a -f`       |
|`images`     | 이미지 나열         |
|`inspect`     | Docker 개체에 대한 하위 수준 정보 반환         |
|`login`     | Docker 레지스트리에 로그인         |
|`logout`     | Docker 레지스트리에서 로그아웃         |
|`logs`     | 컨테이너의 로그 가져오기        |
|`port`     | 컨테이너에 대한 포트 매핑 또는 특정 매핑 목록        |
|`ps`     | 컨테이너 나열        |
|`pull`     | 레지스트리에서 이미지 또는 리포지토리 를 가져옵니다.         |
|`start`     | 하나 이상의 중지된 컨테이너 시작         |
|`stats`     | 컨테이너 의 리소스 사용 통계의 라이브 스트림 표시         |
|`stop`     | 하나 이상의 실행 중인 컨테이너 중지        |
|`system`     | 도커 관리         |
|`top`     | 컨테이너의 실행 중인 프로세스 표시         |

사용 가능한 명령에 대한 도움말을 얻으려면 을 사용합니다. `dkrdbe <command-name> --help`

예를 들어 명령의 사용을 `port` 이해하려면 다음을 입력합니다.

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

함수에 `dkrdbe` 사용할 수 있는 명령은 일반 docker 명령에 사용되는 매개 변수와 동일한 매개 변수를 사용합니다. docker 명령과 함께 사용되는 옵션 및 매개 변수의 경우 [Docker 명령선 사용으로](https://docs.docker.com/engine/reference/commandline/docker/)이동하십시오.

### <a name="to-check-if-the-module-deployed-successfully"></a>모듈이 성공적으로 배포되었는지 확인하려면

계산 모듈은 비즈니스 논리가 구현된 컨테이너입니다. 계산 모듈이 성공적으로 배포되었는지 확인하려면 `ps` 명령을 실행하고 컨테이너(계산 모듈에 해당하는)가 실행되고 있는지 확인합니다.

일시 중지된 컨테이너를 포함하여 모든 컨테이너 목록을 얻으려면 명령을 `ps -a` 실행합니다.

```powershell
[10.100.10.10]: P> dkrdbe ps -a
CONTAINER ID        IMAGE                                                COMMAND                   CREATED             STATUS              PORTS                                                                  NAMES
d99e2f91d9a8        edgecompute.azurecr.io/filemovemodule2:0.0.1-amd64   "dotnet FileMoveModuâ€¦"    2 days ago          Up 2 days                                                                                  movefile
0a06f6d605e9        edgecompute.azurecr.io/filemovemodule2:0.0.1-amd64   "dotnet FileMoveModuâ€¦"    2 days ago          Up 2 days                                                                                  filemove
2f8a36e629db        mcr.microsoft.com/azureiotedge-hub:1.0               "/bin/sh -c 'echo \"$â€¦"   2 days ago          Up 2 days           0.0.0.0:443->443/tcp, 0.0.0.0:5671->5671/tcp, 0.0.0.0:8883->8883/tcp   edgeHub
acce59f70d60        mcr.microsoft.com/azureiotedge-agent:1.0             "/bin/sh -c 'echo \"$â€¦"   2 days ago          Up 2 days                                                                                  edgeAgent
[10.100.10.10]: PS>
```

컨테이너 이미지를 만들거나 이미지를 당기는 동안 오류가 있는 경우 `logs edgeAgent`을 실행합니다.  `EdgeAgent`은 다른 컨테이너를 프로비전하는 IoT Edge 런타임 컨테이너입니다.

모든 `logs edgeAgent` 로그를 덤프하기 때문에 최근 오류를 확인하는 좋은 방법은 옵션을 `--tail 20`사용하는 것입니다.


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

### <a name="to-get-container-logs"></a>컨테이너 로그를 얻으려면

특정 컨테이너에 대한 로그를 얻으려면 먼저 컨테이너를 나열한 다음 관심 있는 컨테이너에 대한 로그를 가져옵니다.

1. [PowerShell 인터페이스에 연결합니다.](#connect-to-the-powershell-interface)
2. 실행 중인 컨테이너 목록을 얻으려면 `ps` 명령을 실행합니다.

    ```powershell
    [10.100.10.10]: P> dkrdbe ps
    CONTAINER ID        IMAGE                                                COMMAND                   CREATED             STATUS              PORTS                                                                  NAMES
    d99e2f91d9a8        edgecompute.azurecr.io/filemovemodule2:0.0.1-amd64   "dotnet FileMoveModuâ€¦"    2 days ago          Up 2 days                                                                                  movefile
    0a06f6d605e9        edgecompute.azurecr.io/filemovemodule2:0.0.1-amd64   "dotnet FileMoveModuâ€¦"    2 days ago          Up 2 days                                                                                  filemove
    2f8a36e629db        mcr.microsoft.com/azureiotedge-hub:1.0               "/bin/sh -c 'echo \"$â€¦"   2 days ago          Up 2 days           0.0.0.0:443->443/tcp, 0.0.0.0:5671->5671/tcp, 0.0.0.0:8883->8883/tcp   edgeHub
    acce59f70d60        mcr.microsoft.com/azureiotedge-agent:1.0             "/bin/sh -c 'echo \"$â€¦"   2 days ago          Up 2 days                                                                                  edgeAgent
    ```

3. 로그가 필요한 컨테이너의 컨테이너 ID를 기록합니다.

4. 특정 컨테이너에 대한 로그를 얻으려면 `logs` 컨테이너 ID를 제공하는 명령을 실행합니다.

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

### <a name="to-monitor-the-usage-statistics-of-the-device"></a>장치의 사용 통계를 모니터링하려면

장치의 메모리, CPU 사용량 및 IO를 모니터링하려면 `stats` 명령을 사용합니다.

1. [PowerShell 인터페이스에 연결합니다.](#connect-to-the-powershell-interface)
2. 라이브 `stats` 스트림을 비활성화하고 첫 번째 결과만 당기도록 명령을 실행합니다.

   ```powershell
   dkrdbe stats --no-stream
   ```

   다음 예제에서는 이 cmdlet의 사용을 보여 주며 있습니다.

    ```
    [10.100.10.10]: P> dkrdbe stats --no-stream
    CONTAINER ID        NAME          CPU %         MEM USAGE / LIMIT     MEM %         NET I/O             BLOCK I/O           PIDS
    d99e2f91d9a8        movefile      0.0           24.4MiB / 62.89GiB    0.04%         751kB / 497kB       299kB / 0B          14
    0a06f6d605e9        filemove      0.00%         24.11MiB / 62.89GiB   0.04%         679kB / 481kB       49.5MB / 0B         14
    2f8a36e629db        edgeHub       0.18%         173.8MiB / 62.89GiB   0.27%         4.58MB / 5.49MB     25.7MB / 2.19MB     241
    acce59f70d60        edgeAgent     0.00%         35.55MiB / 62.89GiB   0.06%         2.23MB / 2.31MB     55.7MB / 332kB      14
    [10.100.10.10]: PS>
    ```

