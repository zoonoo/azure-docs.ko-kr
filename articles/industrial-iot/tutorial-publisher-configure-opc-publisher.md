---
title: Microsoft OPC 게시자 구성
description: 이 자습서에서는 OPC 게시자를 독립 실행형 모드로 구성하는 방법을 알아봅니다.
author: jehona-m
ms.author: jemorina
ms.service: industrial-iot
ms.topic: tutorial
ms.date: 3/22/2021
ms.openlocfilehash: 3667a201e293a844fbcd60b4f00c43aee03ca550
ms.sourcegitcommit: 7f3ed8b29e63dbe7065afa8597347887a3b866b4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122015652"
---
# <a name="tutorial-configure-the-opc-publisher"></a>자습서: OPC 게시자 구성

이 자습서에는 OPC 게시자의 구성에 대한 정보가 포함되어 있습니다. 이는 여러 인터페이스를 사용하여 구성할 수 있습니다.

이 자습서에서는 다음과 같은 작업을 수행하는 방법을 살펴봅니다.

> [!div class="checklist"]
> * 구성 파일을 통해 OPC 게시자 구성
> * 명령줄 인수를 통해 OPC 게시자 구성
> * IoT Hub 직접 메서드를 통해 OPC 게시자 구성
> * 클라우드 기반 동반 REST 마이크로서비스를 통해 OPC 게시자 구성

## <a name="configuring-security"></a>보안 구성

IoT Edge는 IoT Hub에 자동으로 액세스하기 위한 보안 구성을 OPC 게시자에 제공합니다. OPC 게시자는 `dc` 명령줄 매개 변수를 통해 IoT Hub에 액세스하기 위한 디바이스 연결 문자열을 지정하여 독립 실행형 Docker 컨테이너로 실행할 수도 있습니다. IoT Hub에 대한 디바이스를 만들고, Azure Portal을 통해 해당 연결 문자열을 검색할 수 있습니다.

OPC UA 사용 자산에 액세스하기 위해 OPC UA에서 X.509 인증서 및 해당 연결된 프라이빗 키를 사용합니다. 이를 OPC UA 사용자 인증에 더하여 OPC UA 애플리케이션 인증이라고도 합니다. OPC 게시자는 파일 시스템 기반 인증서 저장소를 사용하여 모든 애플리케이션 인증서를 관리합니다. 시작하는 동안 OPC 게시자는 이 인증서 저장소에서 사용할 수 있는 인증서가 있는지 확인하고, 없는 경우 새 자체 서명된 인증서 및 새 연결된 프라이빗 키를 만듭니다. 자체 서명된 인증서는 신뢰할 수 있는 인증 기관에서 서명하지 않으므로 약한 인증을 제공하지만, 적어도 OPC UA 사용 자산에 대한 통신은 이러한 방식으로 암호화할 수 있습니다.

보안은 `"UseSecurity": true,` 플래그를 통해 구성 파일에서 사용하도록 설정됩니다. OPC 게시자에서 연결해야 하는 OPC UA 서버에서 사용할 수 있는 가장 안전한 엔드포인트가 자동으로 선택됩니다.
기본적으로 OPC 게시자는 위에서 설명한 애플리케이션 인증 외에도 익명 사용자 인증을 사용합니다. 그러나 OPC 게시자는 사용자 이름과 암호를 사용하는 사용자 인증도 지원합니다. 이는 REST API 구성 인터페이스(아래 설명 참조) 또는 구성 파일을 통해 다음과 같이 지정할 수 있습니다.
```
"OpcAuthenticationMode": "UsernamePassword",
"OpcAuthenticationUsername": "usr",
"OpcAuthenticationPassword": "pwd",
```
또한 OPC 게시자 버전 2.5 이하에서는 구성 파일의 사용자 이름과 암호를 암호화합니다. 버전 2.6 이상은 일반 텍스트의 사용자 이름과 암호만 지원합니다. 이는 다음 버전의 OPC 게시자에서 향상됩니다.

다시 시작할 때에도 OPC 게시자의 보안 구성을 유지하려면 인증서 저장소 디렉터리에 있는 인증서와 프라이빗 키를 IoT Edge 호스트 OS 파일 시스템에 매핑해야 합니다. 위의 "Azure Portal에서 컨테이너 만들기 옵션 지정"을 참조하세요.

## <a name="configuration-via-configuration-file"></a>구성 파일을 통한 구성

OPC 게시자를 구성하는 가장 간단한 방법은 구성 파일을 사용하는 것입니다. 예제 구성 파일 및 해당 형식과 관련된 설명서는 이 리포지토리의 [`publishednodes.json`](https://raw.githubusercontent.com/Azure/Industrial-IoT/main/components/opc-ua/src/Microsoft.Azure.IIoT.OpcUa.Edge.Publisher/tests/Engine/publishednodes.json) 파일을 통해 제공됩니다.
구성 파일 구문은 시간이 지남에 따라 변경되었으며, OPC 게시자는 여전히 이전 형식을 읽을 수 있지만 정기적으로 자동 방식으로 수행되는 구성을 유지하는 경우 이를 최신 형식으로 변환합니다.

기본 구성 파일은 다음과 같습니다.
```
[
  {
    "EndpointUrl": "opc.tcp://testserver:62541/Quickstarts/ReferenceServer",
    "UseSecurity": true,
    "OpcNodes": [
      {
        "Id": "i=2258",
        "OpcSamplingInterval": 2000,
        "OpcPublishingInterval": 5000,
        "DisplayName": "Current time"
      }
    ]
  }
]
```

OPC UA 자산은 데이터가 변경된 경우에만 데이터 변경 내용을 OPC 게시자에 보냄으로써 네트워크 대역폭을 최적화합니다. 데이터 변경 내용을 더 자주 또는 정기적으로 게시해야 하는 경우 OPC 게시자는 데이터 항목 구성에서 HeartbeatInterval 키를 추가로 지정하여 사용하도록 설정할 수 있는 모든 구성된 데이터 항목에 대해 "하트비트"를 지원합니다. 간격은 초 단위로 지정됩니다.
```
 "HeartbeatInterval": 3600,
```

OPC UA 자산은 OPC 게시자에서 데이터 항목에 처음 연결할 때 항상 해당 데이터 항목의 현재 값을 보냅니다. 이 데이터가 IoT Hub에 게시되지 않도록 데이터 항목의 구성에서 SkipFirst 키를 추가로 지정할 수 있습니다.
```
 "SkipFirst": true,
```

두 설정은 모두 명령줄 옵션을 통해 전역적으로 사용하도록 설정할 수도 있습니다.

## <a name="configuration-via-command-line-arguments"></a>명령줄 인수를 통한 구성

OPC 게시자의 전역 설정을 지정하는 데 사용할 수 있는 몇 가지 명령줄 인수가 있습니다. 이에 대해 [여기](reference-command-line-arguments.md)를 참조하세요.


## <a name="configuration-via-the-built-in-opc-ua-server-interface"></a>기본 제공 OPC UA 서버 인터페이스를 통한 구성

>[!NOTE] 
> 이 기능은 OPC 게시자 버전 2.5 이하에서만 사용할 수 있습니다.

OPC 게시자에는 62222 포트에서 실행되는 기본 제공 OPC UA 서버가 있습니다. 다음 세 가지 OPC UA 메서드를 구현합니다.

  - PublishNode
  - UnpublishNode
  - GetPublishedNodes

이 인터페이스는 OPC UA 클라이언트 애플리케이션(예: [UA Expert](https://www.unified-automation.com/products/development-tools/uaexpert.html))을 사용하여 액세스할 수 있습니다.

## <a name="configuration-via-iot-hub-direct-methods"></a>IoT Hub 직접 메서드를 통한 구성

>[!NOTE] 
> 이 기능은 OPC 게시자 버전 2.5 이하에서만 사용할 수 있습니다.

OPC 게시자는 다음과 같은 [IoT Hub 직접 메서드](../iot-hub/iot-hub-devguide-direct-methods.md)를 구현하며, 이는 [IoT Hub 디바이스 SDK](../iot-hub/iot-hub-devguide-sdks.md)를 활용하여 전 세계 어디서나 애플리케이션에서 호출할 수 있습니다.

  - PublishNodes
  - UnpublishNodes
  - UnpublishAllNodes
  - GetConfiguredEndpoints
  - GetConfiguredNodesOnEndpoint
  - GetDiagnosticInfo
  - GetDiagnosticLog
  - GetDiagnosticStartupLog
  - ExitApplication
  - GetInfo

이 인터페이스를 활용하여 OPC 게시자 오픈 소스에서 [샘플 구성 애플리케이션](https://github.com/Azure-Samples/iot-edge-opc-publisher-nodeconfiguration) 및 [진단 정보 읽기 애플리케이션](https://github.com/Azure-Samples/iot-edge-opc-publisher-diagnostics)을 제공했습니다.

## <a name="configuration-via-cloud-based-companion-rest-microservice"></a>클라우드 기반 동반 REST 마이크로서비스를 통한 구성

>[!NOTE] 
> 이 기능은 OPC 게시자 버전 2.6 이상에서만 사용할 수 있습니다.

REST 인터페이스를 사용하는 클라우드 기반 동반 마이크로서비스는 [여기](https://github.com/Azure/Industrial-IoT/blob/master/docs/services/publisher.md)서 설명하고 사용할 수 있습니다. OpenAPI 호환 인터페이스(예: Swagger)를 통해 OPC 게시자를 구성하는 데 사용할 수 있습니다.

## <a name="configuration-of-the-simple-json-telemetry-format-via-separate-configuration-file"></a>별도의 구성 파일을 통한 간단한 JSON 원격 분석 형식 구성

>[!NOTE] 
> 이 기능은 OPC 게시자 버전 2.5 이하에서만 사용할 수 있습니다.

OPC 게시자를 사용하면 tc 명령줄 옵션을 통해 지정할 수 있는 별도의 구성 파일을 통해 표준화되지 않은 간단한 원격 분석 형식의 일부를 필터링할 수 있습니다. 구성 파일이 지정되지 않으면 전체 JSON 원격 분석 형식을 IoT Hub에 보냅니다. 별도의 원격 분석 구성 파일에 대한 형식은 [여기](reference-opc-publisher-telemetry-format.md#opc-publisher-telemetry-configuration-file-format)를 참조하세요.

## <a name="next-steps"></a>다음 단계
이제 OPC 게시자를 구성했으므로 다음 단계에서는 Edge 모듈의 성능과 메모리를 튜닝하는 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [성능 및 메모리 튜닝](tutorial-publisher-performance-memory-tuning-opc-publisher.md)
