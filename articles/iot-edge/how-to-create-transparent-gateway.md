---
title: 투명한 게이트웨이 디바이스 만들기 - Azure IoT Edge | Microsoft Docs
description: Azure IoT Edge 디바이스를 다운스트림 디바이스의 정보를 처리할 수 있는 투명한 게이트웨이로 사용
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/03/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: e563e67b5e951b43e5782f8c845c8ec46ff3e9bb
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687155"
---
# <a name="configure-an-iot-edge-device-to-act-as-a-transparent-gateway"></a>IoT Edge 디바이스를 투명 게이트웨이로 작동하도록 구성

이 문서에서는 다른 장치가 IoT Hub와 통신할 수 있는 투명한 게이트웨이역할을 하도록 IoT Edge 장치를 구성하는 방법에 대한 자세한 지침을 제공합니다. 이 문서에서는 *IoT Edge 게이트웨이라는* 용어를 사용하여 투명 게이트웨이로 구성된 IoT Edge 장치를 참조합니다. 자세한 내용은 [IoT Edge 장치를 게이트웨이로 사용할 수 있는 방법을 참조하세요.](./iot-edge-as-gateway.md)

>[!NOTE]
>현재 상황:
>
> * Edge 가능 디바이스는 IoT Edge 게이트웨이에 연결할 수 없습니다.
> * 다운스트림 디바이스는 파일 업로드를 사용할 수 없습니다.

성공적인 투명 게이트웨이 연결을 설정하는 세 가지 일반적인 단계가 있습니다. 이 문서에서는 첫 번째 단계를 다룹니다.

1. **게이트웨이 장치는 다운스트림 장치에 안전하게 연결하고, 다운스트림 장치에서 통신을 수신하고, 메시지를 적절한 대상으로 라우팅할 수 있어야 합니다.**
2. 다운스트림 장치는 IoT Hub로 인증할 수 있고 게이트웨이 장치를 통해 통신할 수 있는 장치 ID가 있어야 합니다. 자세한 내용은 [Azure IoT Hub에 다운스트림 장치 인증을](how-to-authenticate-downstream-device.md)참조하세요.
3. 다운스트림 장치는 게이트웨이 장치에 안전하게 연결해야 합니다. 자세한 내용은 [다운스트림 디바이스를 Azure IoT Edge 게이트웨이에 연결](how-to-connect-downstream-device.md)을 참조하세요.

장치가 게이트웨이로 작동하려면 다운스트림 장치에 안전하게 연결할 수 있어야 합니다. Azure IoT Edge를 사용하면 PKI(공개 키 인프라)를 사용하여 이러한 디바이스 간에 안전한 연결을 설정할 수 있습니다. 이 경우 다운스트림 장치가 투명한 게이트웨이 역할을 하는 IoT Edge 장치에 연결할 수 있습니다. 적절한 보안을 유지하려면 다운스트림 장치가 게이트웨이 장치의 ID를 확인해야 합니다. 이 ID 검사를 통해 장치가 잠재적으로 악의적인 게이트웨이에 연결되지 않습니다.

투명한 게이트웨이 시나리오의 다운스트림 장치는 [Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub) 클라우드 서비스로 만든 ID가 있는 모든 응용 프로그램 또는 플랫폼일 수 있습니다. 많은 경우에 이러한 애플리케이션은 [Azure IoT 디바이스 SDK](../iot-hub/iot-hub-devguide-sdks.md)를 사용합니다. 모든 실질적인 용도의 경우 다운스트림 디바이스는 IoT Edge 게이트웨이 디바이스 자체에서 실행 중인 애플리케이션일 수 있습니다. 그러나 IoT Edge 장치는 IoT Edge 게이트웨이의 다운스트림일 수 없습니다.

디바이스-게이트웨이 토폴로지에 필요한 신뢰를 설정하는 어떤 인증서 인프라도 만들 수 있습니다. 이 문서에서는 특정 IoT 허브(IoT hub 루트 CA)와 관련된 X.509 CA 인증서, 이 CA에 서명된 일련의 인증서 및 IoT Edge 장치에 대한 CA를 포함하는 IoT Hub에서 [X.509 CA 보안을](../iot-hub/iot-hub-x509ca-overview.md) 사용하도록 설정하는 데 사용할 것과 동일한 인증서 설정을 가정합니다.

![게이트웨이 인증서 설정](./media/how-to-create-transparent-gateway/gateway-setup.png)

>[!NOTE]
>이 문서에서 사용되는 "루트 CA"라는 용어는 PKI 인증서 체인의 최상위 권한 공용 인증서를 나타내며 반드시 신디케이트된 인증 기관의 인증서 루트는 아닙니다. 대부분의 경우 실제로 는 중간 CA 공용 인증서입니다.

IoT Edge 보안 데몬은 IoT Edge 장치 CA 인증서를 사용하여 워크로드 CA 인증서에 서명하고 IoT Edge 허브용 서버 인증서에 서명합니다. 게이트웨이는 연결을 받는 동안 서버 인증서를 다운스트림 장치에 제공합니다. 다운스트림 장치는 서버 인증서가 루트 CA 인증서로 롤업되는 인증서 체인의 일부인지 확인합니다. 이 프로세스를 통해 다운스트림 장치는 게이트웨이가 신뢰할 수 있는 소스에서 왔는지 확인할 수 있습니다. 자세한 내용은 [Azure IoT Edge에서 인증서를 사용하는 방법 이해하기를](iot-edge-certs.md)참조하십시오.

다음 단계에서는 인증서를 만들고 게이트웨이의 올바른 위치에 설치하는 프로세스를 안내합니다. 머신을 사용하여 인증서를 생성한 다음, IoT Edge 디바이스로 복사할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

[프로덕션 인증서로](how-to-manage-device-certificates.md)구성된 Azure IoT Edge 장치입니다.

## <a name="deploy-edgehub-to-the-gateway"></a>게이트웨이에 에지허브 배포

장치에 IoT Edge를 처음 설치하면 하나의 시스템 모듈만 자동으로 시작됩니다. 첫 번째 배포를 더 많은 장치로 만들면 두 번째 시스템 모듈인 IoT Edge 허브도 시작됩니다.

IoT Edge 허브는 다운스트림 장치에서 들어오는 메시지를 수신하고 다음 대상으로 라우팅합니다. **edgeHub** 모듈이 장치에서 실행되고 있지 않으면 장치에 대한 초기 배포를 만듭니다. 모듈을 추가하지 않으므로 배포가 비어 있지만 두 시스템 모듈이 모두 실행되고 있는지 확인합니다.

Azure 포털에서 장치 세부 정보를 확인하거나 Visual Studio 또는 Visual Studio 코드에서 장치 상태를 확인하거나 장치 자체에서 `iotedge list` 명령을 실행하여 장치에서 실행 중인 모듈을 확인할 수 있습니다.

**edgeAgent** 모듈이 **edgeHub** 모듈 없이 실행 중인 경우 다음 단계를 사용합니다.

1. Azure Portal에서 IoT Hub로 이동합니다.

2. **IoT Edge**로 이동하고 게이트웨이로 사용할 IoT Edge 디바이스를 선택합니다.

3. **모듈 설정**선택 .

4. **다음**을 선택합니다.

5. **경로 지정** 페이지에서 모든 메시지를 모든 모듈에서 IoT Hub로 보내는 기본 경로가 있어야 합니다. 그렇지 않은 경우 다음 코드를 추가한 다음, **다음**을 선택합니다.

   ```JSON
   {
       "routes": {
           "route": "FROM /messages/* INTO $upstream"
       }
   }
   ```

6. **템플릿 검토** 페이지에서 **제출**을 선택합니다.

## <a name="open-ports-on-gateway-device"></a>게이트웨이 장치에서 열린 포트

IoT Hub와의 모든 통신은 아웃바운드 연결을 통해 이루어지므로 표준 IoT Edge 장치는 기능에 인바운드 연결이 필요하지 않습니다. 게이트웨이 장치는 다운스트림 장치에서 메시지를 수신해야 하기 때문에 다릅니다. 방화벽이 다운스트림 장치와 게이트웨이 장치 사이에 있는 경우 방화벽을 통해서도 통신이 가능해야 합니다.

게이트웨이 시나리오가 작동하려면 IoT Edge 허브에서 지원되는 프로토콜 중 하나 이상이 다운스트림 장치에서 인바운드 트래픽에 대해 열려 있어야 합니다. 지원되는 프로토콜은 MQTT, AMQP, HTTPS, 웹소켓을 통해 MQTT 및 웹 소켓을 통해 AMQP입니다.

| 포트 | 프로토콜 |
| ---- | -------- |
| 8883 | MQTT |
| 5671 | AMQP |
| 443 | HTTPS <br> MQTT+WS <br> AMQP+WS |

## <a name="route-messages-from-downstream-devices"></a>다운스트림 디바이스에서 메시지 라우팅

IoT Edge 런타임은 모듈에서 전송한 메시지와 같은 다운스트림 디바이스에서 전송된 메시지를 라우팅할 수 있습니다. 이 기능을 사용하면 클라우드로 데이터를 보내기 전에 게이트웨이에서 실행되는 모듈에서 분석을 수행할 수 있습니다.

현재 다운스트림 디바이스에서 보낸 메시지를 라우팅하는 방식은 모듈에 의해 전송 된 메시지와 구별됩니다. 모듈에서 보낸 메시지에는 모두 **connectionModuleId**라는 시스템 속성이 포함되지만 다운스트림 디바이스에서 보낸 메시지에는 포함되지 않습니다. 경로의 WHERE 절을 사용하여 해당 시스템 특성을 포함하는 메시지를 제외할 수 있습니다.

아래 경로는 다운스트림 장치에서 라는 `ai_insights`모듈로 메시지를 보낸 다음 IoT `ai_insights` Hub로 메시지를 보내는 예제입니다.

```json
{
    "routes":{
        "sensorToAIInsightsInput1":"FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO BrokeredEndpoint(\"/modules/ai_insights/inputs/input1\")",
        "AIInsightsToIoTHub":"FROM /messages/modules/ai_insights/outputs/output1 INTO $upstream"
    }
}
```

메시지 라우팅에 대한 자세한 내용은 [모듈 배포 및 경로 설정](./module-composition.md#declare-routes)을 참조하세요.

## <a name="enable-extended-offline-operation"></a>확장 된 오프라인 작업 사용

IoT Edge 런타임의 [v1.0.4 릴리스부터](https://github.com/Azure/azure-iotedge/releases/tag/1.0.4) 연결되는 게이트웨이 장치와 다운스트림 장치를 확장된 오프라인 작업을 위해 구성할 수 있습니다.

이 기능을 통해 로컬 모듈 또는 다운스트림 장치는 필요에 따라 IoT Edge 장치로 다시 인증하고 IoT 허브에서 연결이 끊어진 경우에도 메시지 및 방법을 사용하여 서로 통신할 수 있습니다. 자세한 내용은 [IoT Edge 디바이스, 모듈 및 하위 디바이스용 확장 오프라인 기능 이해](offline-capabilities.md)를 참조하세요.

확장된 오프라인 기능을 사용하려면 IoT Edge 게이트웨이 장치와 연결되는 다운스트림 장치 간에 부모-자식 관계를 설정합니다. 이러한 단계는 [Azure IoT Hub에 다운스트림 장치를 인증할 때](how-to-authenticate-downstream-device.md)자세히 설명합니다.

## <a name="next-steps"></a>다음 단계

투명한 게이트웨이로 작동하는 IoT Edge 디바이스가 생겼습니다. 이제 게이트웨이를 신뢰하고 메시지를 보내도록 다운스트림 디바이스를 구성해야 합니다. 투명한 게이트웨이 시나리오를 설정하는 다음 단계를 [위해 Azure IoT Hub로 다운스트림 장치를 인증합니다.](how-to-authenticate-downstream-device.md)
