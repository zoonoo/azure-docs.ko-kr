---
title: "Azure IoT Suite 연결 팩터리 OPC 게시자 사용 | Microsoft Docs"
description: "연결된 팩터리 OPC 게시자 참조 구현을 빌드하여 배포하는 방법"
services: 
suite: iot-suite
documentationcenter: na
author: dominicbetts
manager: timlt
editor: 
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/14/2017
ms.author: dobett
ms.openlocfilehash: 934f4deb3139c136c871ab0125ba45267e1d1b05
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/14/2017
---
# <a name="opc-publisher-for-azure-iot-edge"></a>Azure IoT Edge용 OPC 게시자

이 문서에서는 OPC 게시자 참조 구현을 사용하는 방법을 설명합니다. 참조 구현은 Azure IoT Edge를 사용하여 다음을 수행하는 방법을 보여 줍니다.

- 기존 OPC UA 서버에 연결합니다.
- JSON 페이로드를 사용하여 이러한 서버로부터 JSON 인코딩 원격 분석 데이터를 OPC UA *Pub/Sub* 형식으로  Azure IoT Hub에 게시합니다. Azure IoT Edge가 지원하는 모든 전송 프로토콜을 사용할 수 있습니다.

이 참조 응용 프로그램에는 다음이 포함됩니다.

- 네트워크의 기존 OPC UA 서버에 연결하기 위한 OPC UA *클라이언트* 
- 게시된 항목을 관리하는 데 사용할 수 있는 포트 62222에서 수신 대기하는 OPC UA *서버*.

이 응용 프로그램은 .NET Core를 통해 구현되며 .NET Core에서 지원되는 플랫폼에서 실행할 수 있습니다.

게시자는 끝점에 대한 연결을 수립할 때 재시도 논리를 구현합니다. 게시자는 지정된 유지 요청 수 범위 안에서 끝점이 응답할 것이라 예상합니다. 이 재시도 논리를 통해 게시자는 OPC UA 서버 정전 등의 상황을 탐지할 수 있습니다.

OPC UA 서버에 대한 각각의 고유 게시 간격에 대해 이 게시 간격을 갖는 모든 노드가 업데이트되는 별도의 구독을 만듭니다.

네트워크 부하를 줄이기 위해 게시자는 IoT Hub로의 일괄 처리 데이터 전송을 지원합니다. 일괄 처리는 구성된 패키지 크기에 도달해야만 IoT Hub로 전송됩니다.

이 응용 프로그램에서는 OPC Foundations의 OPC UA 참조 스택을 사용하므로 라이선싱 제한이 적용됩니다. OPC UA 설명서 및 라이선싱 조건은 http://opcfoundation.github.io/UA-.NETStandardLibrary/에서 확인하세요.

OPC 게시자 소스 코드는 [Azure IoT Edge용 OPC 게시자](https://github.com/Azure/iot-edge-opc-publisher) GitHub 리포지토리에 있습니다.

## <a name="prerequisites"></a>필수 조건

응용 프로그램을 빌드하려면 운영 체제에 해당하는 [.NET Core SDK 1.1.](https://docs.microsoft.com/dotnet/core/sdk)이  있어야 합니다.

## <a name="build-the-application"></a>응용 프로그램 빌드

### <a name="as-native-windows-application"></a>원시 Windows 응용 프로그램

Visual Studio 2017에서 `OpcPublisher.sln` 프로젝트를 열고 F7 키를 눌러 솔루션을 빌드합니다.

### <a name="as-docker-container"></a>Docker 컨테이너 사용

Windows Docker 컨테이너로 응용 프로그램을 빌드하려면 `Dockerfile.Windows` 구성 파일을 사용합니다.

Linux Docker 컨테이너로 응용 프로그램을 빌드하려면 `Dockerfile` 구성 파일을 사용합니다.

개발 컴퓨터의 리포지토리 루트에서 콘솔 창에 다음 명령을 입력합니다.

`docker build -f <docker-configfile-to-use> -t <your-container-name> .`

`docker build`에 대한 `-f` 옵션은 선택 사항이며 기본값은 `Dockerfile` 구성 파일을 사용하는 것입니다.

Docker에서도 git 리포지토리로부터 직접 빌드할 수 있습니다. 다음 명령을 사용하여 Linux Docker 컨테이너를 빌드할 수 있습니다.

`docker build -t <your-container-name> .https://github.com/Azure/iot-edge-opc-publisher`

## <a name="configure-the-opc-ua-nodes-to-publish"></a>게시할 OPC UA 노드 구성

값을 Azure IoT Hub에 게시해야 하는 OPC UA 노드를 구성하려면 JSON 형식의 구성 파일을 만듭니다. 이 구성 파일의 기본 이름은 `publishednodes.json`입니다. OPC UA 서버 메서드 **PublishNode** 또는 **UnpublishNode**를 사용할 때 응용 프로그램이 업데이트되고 이 구성 파일을 저장합니다. 

구성 파일의 구문은 다음과 같습니다.

```json
[
    {
        // example for an EnpointUrl is: opc.tcp://win10iot:51210/UA/SampleServer
        "EndpointUrl": "opc.tcp://<your_opcua_server>:<your_opcua_server_port>/<your_opcua_server_path>",
        "OpcNodes": [
            // Publisher will request the server at EndpointUrl to sample the node with the OPC sampling interval specified on command line (or the default value: OPC publishing interval)
            // and the subscription will publish the node value with the OPC publishing interval specified on command line (or the default value: server revised publishing interval).
            {
                // The identifier specifies the NamespaceUri and the node identifier in XML notation as specified in Part 6 of the OPC UA specification in the XML Mapping section.
                "ExpandedNodeId": "nsu=http://opcfoundation.org/UA/;i=2258"
            },
            // Publisher will request the server at EndpointUrl to sample the node with the OPC sampling interval specified on command line (or the default value: OPC publishing interval)
            // and the subscription will publish the node value with an OPC publishing interval of 4 seconds.
            // Publisher will use for each dinstinct publishing interval (of nodes on the same EndpointUrl) a separate subscription. All nodes without a publishing interval setting,
            // will be on the same subscription and the OPC UA stack will publish with the lowest sampling interval of a node.
            {
                "ExpandedNodeId": "nsu=http://opcfoundation.org/UA/;i=2258",
                "OpcPublishingInterval": 4000
            },
            // Publisher will request the server at EndpointUrl to sample the node with the given sampling interval of 1 second
            // and the subscription will publish the node value with the OPC publishing interval specified on command line (or the default value: server revised interval).
            // If the OPC publishing interval is set to a lower value, Publisher will adjust the OPC publishing interval of the subscription to the OPC sampling interval value.
            {
                "ExpandedNodeId": "nsu=http://opcfoundation.org/UA/;i=2258",
                // the OPC sampling interval to use for this node.
                "OpcSamplingInterval": 1000
            }
        ]
    },

    // the format below is only supported for backward compatibility. you need to ensure that the
    // OPC UA server on the configured EndpointUrl has the namespaceindex you expect with your configuration.
    // please use the ExpandedNodeId syntax instead.
    {
        "EndpointUrl": "opc.tcp://<your_opcua_server>:<your_opcua_server_port>/<your_opcua_server_path>",
        "NodeId": {
            "Identifier": "ns=0;i=2258"
        }
    }
    // please consult the OPC UA specification for details on how OPC monitored node sampling interval and OPC subscription publishing interval settings are handled by the OPC UA stack.
    // the publishing interval of the data to Azure IoT Hub is controlled by the command line settings (or the default: publish data to IoT Hub at least each 1 second).
]
```

## <a name="run-the-application"></a>응용 프로그램 실행

### <a name="command-line-options"></a>명령줄 옵션

응용 프로그램의 전체 사용을 보려면 `--help` 명령줄 옵션을 사용합니다. 다음 예제에서는 명령의 구조를 보여줍니다.

```cmd/sh
OpcPublisher.exe <applicationname> [<IoT Hubconnectionstring>] [<options>]
```

`applicationname`은 사용할 OPC UA 응용 프로그램 이름입니다. 이 매개 변수는 필수입니다. 응용 프로그램 이름은 IoT Hub 장치 레지스트리에 게시자를 등록하는 데도 사용됩니다.

`IoT Hubconnectionstring`은 IoT Hub 소유자 연결 문자열입니다. 이 매개 변수는 선택 사항입니다.

다음과 같은 옵션이 지원됩니다.

```cmd/sh
--pf, --publishfile=VALUE
  the filename to configure the nodes to publish.
    Default: './publishednodes.json'
--sd, --shopfloordomain=VALUE
  the domain of the shopfloor. if specified this
    domain is appended (delimited by a ':' to the '
    ApplicationURI' property when telemetry is
    sent to IoT Hub.
    The value must follow the syntactical rules of a
    DNS hostname.
    Default: not set
--sw, --sessionconnectwait=VALUE
  specify the wait time in seconds publisher is
    trying to connect to disconnected endpoints and
    starts monitoring unmonitored items
    Min: 10
    Default: 10
--vc, --verboseconsole=VALUE
  the output of publisher is shown on the console.
    Default: False
--ih, --IoT Hubprotocol=VALUE
  the protocol to use for communication with Azure
    IoT Hub (allowed values: Amqp, Http1, Amqp_
    WebSocket_Only, Amqp_Tcp_Only, Mqtt, Mqtt_
    WebSocket_Only, Mqtt_Tcp_Only).
    Default: Mqtt
--ms, --IoT Hubmessagesize=VALUE
  the max size of a message which can be send to
    IoT Hub. when telemetry of this size is available
    it will be sent.
    0 will enforce immediate send when telemetry is
    available
    Min: 0
    Max: 256 * 1024
    Default: 4096
--si, --IoT Hubsendinterval=VALUE
  the interval in seconds when telemetry should be
    send to IoT Hub. If 0, then only the
    IoT Hubmessagesize parameter controls when
    telemetry is sent.
    Default: '1'
--lf, --logfile=VALUE
  the filename of the logfile to use.
    Default: './Logs/<applicationname>.log.txt'
--pn, --portnum=VALUE
  the server port of the publisher OPC server
    endpoint.
    Default: 62222
--pa, --path=VALUE
  the endpoint URL path part of the publisher OPC
    server endpoint.
    Default: '/UA/Publisher'
--lr, --ldsreginterval=VALUE
  the LDS(-ME) registration interval in ms. If 0,
    then the registration is disabled.
    Default: 0
--ot, --operationtimeout=VALUE
  the operation timeout of the publisher OPC UA
    client in ms.
    Default: 120000
--oi, --opcsamplinginterval=VALUE
  the publisher is using this as default value in
    milliseconds to request the servers to sample
    the nodes with this interval
    this value might be revised by the OPC UA
    servers to a supported sampling interval.
    please check the OPC UA specification for
    details how this is handled by the OPC UA stack.
    a negative value will set the sampling interval
    to the publishing interval of the subscription
    this node is on.
    0 will configure the OPC UA server to sample in
    the highest possible resolution and should be
    taken with care.
    Default: 1000
--op, --opcpublishinginterval=VALUE
  the publisher is using this as default value in
    milliseconds for the publishing interval setting
    of the subscriptions established to the OPC UA
    servers.
    please check the OPC UA specification for
    details how this is handled by the OPC UA stack.
    a value less than or equal zero will let the
    server revise the publishing interval.
    Default: 0
--ct, --createsessiontimeout=VALUE
  specify the timeout in seconds used when creating
    a session to an endpoint. On unsuccessful
    connection attemps a backoff up to 5 times the
    specified timeout value is used.
    Min: 1
    Default: 10
--ki, --keepaliveinterval=VALUE
  specify the interval in seconds the publisher is
    sending keep alive messages to the OPC servers
    on the endpoints it is connected to.
    Min: 2
    Default: 2
--kt, --keepalivethreshold=VALUE
  specify the number of keep alive packets a server
    can miss, before the session is disconneced
    Min: 1
    Default: 5
--st, --opcstacktracemask=VALUE
  the trace mask for the OPC stack. See github OPC .
    NET stack for definitions.
    To enable IoT Hub telemetry tracing set it to 711.
    Default: 285  (645)
--as, --autotrustservercerts=VALUE
  the publisher trusts all servers it is
    establishing a connection to.
    Default: False
--tm, --trustmyself=VALUE
  the publisher certificate is put into the trusted
    certificate store automatically.
    Default: True
--fd, --fetchdisplayname=VALUE
  enable to read the display name of a published
    node from the server. this will increase the
    runtime.
    Default: False
--at, --appcertstoretype=VALUE
  the own application cert store type.
    (allowed values: Directory, X509Store)
    Default: 'X509Store'
--ap, --appcertstorepath=VALUE
  the path where the own application cert should be
    stored
    Default (depends on store type):
    X509Store: 'CurrentUser\UA_MachineDefault'
    Directory: 'CertificateStores/own'
--tt, --trustedcertstoretype=VALUE
  the trusted cert store type.
    (allowed values: Directory, X509Store)
    Default: Directory
--tp, --trustedcertstorepath=VALUE
  the path of the trusted cert store
    Default (depends on store type):
    X509Store: 'CurrentUser\UA_MachineDefault'
    Directory: 'CertificateStores/trusted'
--rt, --rejectedcertstoretype=VALUE
  the rejected cert store type.
    (allowed values: Directory, X509Store)
    Default: Directory
--rp, --rejectedcertstorepath=VALUE
  the path of the rejected cert store
    Default (depends on store type):
    X509Store: 'CurrentUser\UA_MachineDefault'
    Directory: 'CertificateStores/rejected'
--it, --issuercertstoretype=VALUE
  the trusted issuer cert store type.
    (allowed values: Directory, X509Store)
    Default: Directory
--ip, --issuercertstorepath=VALUE
  the path of the trusted issuer cert store
    Default (depends on store type):
    X509Store: 'CurrentUser\UA_MachineDefault'
    Directory: 'CertificateStores/issuers'
--dt, --devicecertstoretype=VALUE
  the IoT Hub device cert store type.
    (allowed values: Directory, X509Store)
    Default: X509Store
--dp, --devicecertstorepath=VALUE
  the path of the iot device cert store
    Default Default (depends on store type):
    X509Store: 'My'
    Directory: 'CertificateStores/IoT Hub'
-h, --help
  show this message and exit
```

다음 환경 변수를 사용하여 응용 프로그램을 제어할 수 있습니다.
- `_HUB_CS`: IoT Hub 소유자 연결 문자열 설정 
- `_GW_LOGP`: 사용할 로그 파일의 파일 이름 설정
- `_TPC_SP`: 신뢰할 수 있는 스테이션의 인증서 저장 경로 설정
- `_GW_PNFP`: 게시하는 구성 파일의 파일 이름 설정

명령줄 인수는 환경 변수 설정을 취소합니다.

일반적으로 응용 프로그램의 처음 시작할 때 IoT Hub 소유자 연결 문자열을 지정합니다. 연결 문자열은 암호화되어 플랫폼의 인증서 저장소에 저장됩니다.

이후의 호출에서 연결 문자열을 플랫폼의 인증서 저장소에서 읽어 들여 다시 사용합니다. 시작할 때마다 연결 문자열을 지정할 경우 매번 IoT Hub 장치 레지스트리의 장치가 제거되고 다시 만들어집니다.

### <a name="native-on-windows"></a>Windows 네이티브

Windows에서 응용 프로그램을 기본적으로 실행하려면 Visual Studio 2017에서 `OpcPublisher.sln` 프로젝트를 열고 솔루션을 빌드하여 게시합니다. 다음을 통해 게시한 **대상 디렉터리**에서 응용 프로그램을 시작할 수 있습니다.

```cmd
dotnet OpcPublisher.dll <applicationname> [<IoT Hubconnectionstring>] [options]
```

### <a name="use-a-self-built-container"></a>자체 기본 제공 컨테이너 사용

자체 기본 제공된 컨테이너에서 응용 프로그램을 실행하려면 자체 컨테이너를 빌드한 다음 시작합니다.

```cmd/sh
docker run <your-container-name> <applicationname> [<IoT Hubconnectionstring>] [options]
```

### <a name="use-a-container-from-hubdockercom"></a>hub.docker.com의 컨테이너 사용

DockerHub에서는 미리 빌드된 컨테이너를 제공합니다. 이를 시작하려면 다음 명령을 수행합니다.

```cmd/sh
docker run microsoft/iot-edge-opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

### <a name="important-when-using-a-container"></a>컨테이너 사용 시 유의 사항

#### <a name="access-to-the-publisher-opc-ua-server"></a>게시자 OPC UA 서버에 대한 액세스

기본적으로 게시자 OPC UA 서버는 포트 62222에서 수신 대기합니다. 컨테이너에서 이 인바운드 포트를 노출하려면 `docker run` 옵션 `-p`를 사용해야 합니다.

```cmd/sh
docker run -p 62222:62222 microsoft/iot-edge-opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

#### <a name="enable-intercontainer-name-resolution"></a>컨테이너 간 이름 확인 사용

컨테이너에서 다른 컨테이너로의 이름 확인을 사용하려면 다음을 수행해야 합니다. 

- 사용자 정의 도커 브리지 네트워크를 만듭니다.
- `--network` 옵션을 사용하여 컨테이너를 네트워크에 연결합니다.
- `--name` 옵션을 사용하여 컨테이너에 이름을 할당합니다.

다음 예제에서는 이러한 구성 옵션을 보여 줍니다.

```cmd/sh
docker network create -d bridge iot_edge
docker run --network iot_edge --name publisher microsoft/iot-edge-opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

이제 `publisher` 이름을 사용하여 네트워크를 토애 다른 컨테이너에서 컨테이너에 연결할 수 있습니다.

#### <a name="assign-a-hostname"></a>호스트 이름 할당

게시자는 실행 중인 컴퓨터의 호스트 이름을 인증서와 끝점 생성에 사용합니다. 사용자가 `-h` 옵션으로 설정하지 않은 한 Docker는 임의의 호스트 이름을 선택합니다. 다음은 컨테이너의 내부 호스트 이름을 `publisher`로 설정하는 예제입니다.

```cmd/sh
docker run -h publisher microsoft/iot-edge-opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

#### <a name="using-bind-mounts-shared-filesystem"></a>바인드 바운트 사용(공유 파일 시스템)

일부 시나리오에서는 컨테이너 파일 시스템 대신 호스트의 위치에서 구성 정보를 읽어오거나 로그 파일을 쓰고자 할 수 있습니다. 이러한 동작을 구성하려면 바인드 마운트 모드의 `docker run`에서 `-v` 옵션을 사용합니다. 예:

```cmd/sh
-v //D/docker:/build/out/Logs
-v //D/docker:/build/out/CertificateStores
-v //D/docker:/shared
-v //D/docker:/root/.dotnet/corefx/cryptography/x509stores
```

#### <a name="store-for-x509-certificates"></a>X509 인증서의 저장소

X509 인증서 저장은 바인드 바운트에서 작동하지 않습니다. 소유자에 대해 저장소 경로의 권한이 `rw`여야 하기 때문입니다. 대신 볼륨 모드에서 `docker run`의 `-v` 옵션을 사용해야 합니다.

## <a name="debug-the-application"></a>응용 프로그램 디버그

### <a name="native-on-windows"></a>Windows 네이티브

Visual Studio 2017에서 `OpcPublisher.sln` 프로젝트를 열고 F5를 눌러 디버그를 시작합니다.

### <a name="in-a-docker-container"></a>Docker 컨테이너 

Visual Studio 2017은 `docker-compose`를 사용한 Docker 컨테이너에서의 응용 프로그램 디버그를 지원합니다. 그러나 이 방법에서는 명령줄 매개 변수 전달이 허용되지 않습니다.

VS2017이 지원하는 다른 디버그 옵션은 `ssh`를 통한 디버그입니다. 리포지토리의 루트에서 Docker 빌드 구성 파일 `Dockerfile.ssh`를 사용하여 SSH 지원 컨테이너를 만들 수 있습니다.

```cmd/sh
docker build -f .\Dockerfile.ssh -t publisherssh .
```

이제 컨테이너를 시작하여 게시자를 디버그할 수 있습니다.

```cmd/sh
docker run -it publisherssh
```

컨테이너에서 **ssh** 데몬을 수동으로 시작해야 합니다.

```cmd/sh
service ssh start
```

이 시점에서 사용자 `root`, 암호 `Passw0rd`로 **ssh** 세션을 만들 수 있습니다. 

컨테이너에서 응용 프로그램의 디버그를 준비하려면 다음 추가 단계를 완료합니다.

1. 호스트 측에서 `launch.json` 파일을 만듭니다.

    ```json
    {
      "version": "0.2.0",
      "adapter": "<path>\\plink.exe",
      "adapterArgs": "root@localhost -pw Passw0rd -batch -T ~/vsdbg/vsdbg --interpreter=vscode",
      "languageMappings": {
        "C#": {
          "languageId": "3F5162F8-07C6-11D3-9053-00C04FA302A1",
          "extensions": [ "*" ]
        }
      },
      "exceptionCategoryMappings": {
        "CLR": "449EC4CC-30D2-4032-9256-EE18EB41B62B",
        "MDA": "6ECE07A9-0EDE-45C4-8296-818D8FC401D4"
      },
      "configurations": [
        {
          "name": ".NET Core Launch",
          "type": "coreclr",
          "cwd": "~/publisher",
          "program": "Opc.Ua.Publisher.dll",
          "args": "<put-the-publisher-command-line-options-here>",

          "request": "launch"
        }
      ]
    }
    ```

1. 프로젝트를 빌드하고 원하는 디렉터리에 게시합니다.

1. `WinSCP` 등의 도구를 사용하여 게시된 파일을 컨테이너의 `/root/publisher` 디렉터리에 복사합니다. 다른 디렉터리를 사용하려는 경우 `launch.json` 파일에서 `cdw` 속성을 업데이트합니다.

이제 Visual Studio의 명령 창에서 다음 명령을 사용하여 디버그를 시작할 수 있습니다.

```cmd
DebugAdapterHost.Launch /LaunchJson:"<path-to-the-launch.json-file-you-saved>"
```

## <a name="next-steps"></a>다음 단계

다음으로 [연결된 팩터리의 미리 구성된 솔루션을 위해 Windows 또는 Linux에 게이트웨이 배포](iot-suite-connected-factory-gateway-deployment.md)를 학습하는 것이 좋습니다.