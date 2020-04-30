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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81687155"
---
# <a name="configure-an-iot-edge-device-to-act-as-a-transparent-gateway"></a>IoT Edge 디바이스를 투명 게이트웨이로 작동하도록 구성

이 문서에서는 다른 장치에서 IoT Hub와 통신할 수 있도록 투명 한 게이트웨이로 작동 하도록 IoT Edge 장치를 구성 하는 방법에 대 한 자세한 지침을 제공 합니다. 이 문서에서는 *IoT Edge 게이트웨이* 라는 용어를 사용 하 여 투명 게이트웨이로 구성 된 IoT Edge 장치를 참조 합니다. 자세한 내용은 [IoT Edge 장치를 게이트웨이로 사용 하는 방법](./iot-edge-as-gateway.md)을 참조 하세요.

>[!NOTE]
>현재 상황:
>
> * Edge 가능 디바이스는 IoT Edge 게이트웨이에 연결할 수 없습니다.
> * 다운스트림 디바이스는 파일 업로드를 사용할 수 없습니다.

성공적인 투명 게이트웨이 연결을 설정 하는 세 가지 일반적인 단계가 있습니다. 이 문서에서는 첫 번째 단계에 대해 설명 합니다.

1. **게이트웨이 장치는 다운스트림 장치에 안전 하 게 연결 하 고, 다운스트림 장치에서 통신을 수신 하 고, 메시지를 적절 한 대상으로 라우팅할 수 있어야 합니다.**
2. 다운스트림 장치는 IoT Hub을 사용 하 여 인증 하 고 게이트웨이 장치를 통해 통신 하는 것을 알 수 있도록 장치 id가 있어야 합니다. 자세한 내용은 [Azure IoT Hub에 대 한 다운스트림 장치 인증](how-to-authenticate-downstream-device.md)을 참조 하세요.
3. 다운스트림 장치는 게이트웨이 장치에 안전 하 게 연결 해야 합니다. 자세한 내용은 [다운스트림 디바이스를 Azure IoT Edge 게이트웨이에 연결](how-to-connect-downstream-device.md)을 참조하세요.

장치가 게이트웨이로 작동 하려면 다운스트림 장치에 안전 하 게 연결할 수 있어야 합니다. Azure IoT Edge를 사용하면 PKI(공개 키 인프라)를 사용하여 이러한 디바이스 간에 안전한 연결을 설정할 수 있습니다. 이 경우 다운스트림 장치는 투명 게이트웨이 역할을 하는 IoT Edge 장치에 연결할 수 있습니다. 적절 한 보안을 유지 하기 위해 다운스트림 장치는 게이트웨이 장치의 id를 확인 해야 합니다. 이 id 검사는 장치에서 잠재적으로 악성 게이트웨이에 연결 하는 것을 방지 합니다.

투명 게이트웨이 시나리오의 다운스트림 장치는 [Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub) 클라우드 서비스를 사용 하 여 만든 id가 있는 모든 응용 프로그램 또는 플랫폼이 될 수 있습니다. 많은 경우에 이러한 애플리케이션은 [Azure IoT 디바이스 SDK](../iot-hub/iot-hub-devguide-sdks.md)를 사용합니다. 모든 실질적인 용도의 경우 다운스트림 디바이스는 IoT Edge 게이트웨이 디바이스 자체에서 실행 중인 애플리케이션일 수 있습니다. 그러나 IoT Edge 장치는 IoT Edge 게이트웨이의 다운스트림 일 수 없습니다.

디바이스-게이트웨이 토폴로지에 필요한 신뢰를 설정하는 어떤 인증서 인프라도 만들 수 있습니다. 이 문서에서는 IoT Hub에서 [X.509 ca 보안](../iot-hub/iot-hub-x509ca-overview.md) 을 사용 하도록 설정 하는 데 사용 하는 것과 동일한 인증서 설정을 가정 합니다. 여기에는 특정 iot Hub (iot HUB 루트 CA)와 연결 된 x.509 ca 인증서,이 ca로 서명 된 일련의 인증서 및 IOT EDGE 장치용 ca가 포함 됩니다.

![게이트웨이 인증서 설정](./media/how-to-create-transparent-gateway/gateway-setup.png)

>[!NOTE]
>이 문서 전체에서 사용 되는 "루트 CA" 라는 용어는 게시 된 인증 기관의 인증서 루트가 아닌 PKI 인증서 체인의 최상위 인증 기관 공용 인증서를 나타냅니다. 대부분의 경우에는 실제로 중간 CA 공용 인증서입니다.

IoT Edge 보안 디먼은 IoT Edge 장치 CA 인증서를 사용 하 여 워크 로드 CA 인증서에 서명 합니다 .이 인증서는 IoT Edge 허브에 대 한 서버 인증서를 서명 합니다. 게이트웨이는 연결을 시작 하는 동안 다운스트림 장치에 해당 서버 인증서를 제공 합니다. 다운스트림 장치에서 서버 인증서가 루트 CA 인증서로 롤업되는 인증서 체인의 일부 인지 확인 합니다. 이 프로세스를 통해 다운스트림 장치는 게이트웨이가 신뢰할 수 있는 소스에서 온 것임을 확인할 수 있습니다. 자세한 내용은 [Azure IoT Edge에서 인증서를 사용 하는 방법 이해](iot-edge-certs.md)를 참조 하세요.

다음 단계는 인증서를 만들어 게이트웨이의 올바른 위치에 설치 하는 과정을 안내 합니다. 머신을 사용하여 인증서를 생성한 다음, IoT Edge 디바이스로 복사할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

[프로덕션 인증서](how-to-manage-device-certificates.md)로 구성 된 Azure IoT Edge 장치

## <a name="deploy-edgehub-to-the-gateway"></a>게이트웨이에 edgeHub 배포

장치에 IoT Edge를 처음 설치 하는 경우에는 IoT Edge 에이전트와 같은 한 시스템 모듈만 자동으로 시작 됩니다. 장치를 추가 하 여 첫 번째 배포를 만든 후에는 두 번째 시스템 모듈인 IoT Edge 허브가 시작 됩니다.

IoT Edge 허브는 다운스트림 장치에서 들어오는 메시지를 수신 하 고 다음 대상으로 라우팅하는 역할을 담당 합니다. **EdgeHub** 모듈이 장치에서 실행 되 고 있지 않은 경우 장치에 대 한 초기 배포를 만듭니다. 모듈을 추가 하지 않으므로 배포는 비어 있지만 두 시스템 모듈이 모두 실행 되 고 있는지 확인 합니다.

Azure Portal에서 장치 세부 정보를 확인 하 고, Visual Studio 또는 Visual Studio Code에서 장치 상태를 확인 하거나, 장치 자체에서 명령을 `iotedge list` 실행 하 여 장치에서 실행 되는 모듈을 확인할 수 있습니다.

**EdgeHub** 모듈 없이 **edgeAgent** 모듈을 실행 하는 경우 다음 단계를 사용 합니다.

1. Azure Portal에서 IoT Hub로 이동합니다.

2. **IoT Edge**로 이동하고 게이트웨이로 사용할 IoT Edge 디바이스를 선택합니다.

3. **모듈 설정**을 선택 합니다.

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

## <a name="open-ports-on-gateway-device"></a>게이트웨이 장치에서 포트 열기

IoT Hub와의 모든 통신은 아웃 바운드 연결을 통해 수행 되므로 표준 IoT Edge 장치는 작동 하기 위해 인바운드 연결이 필요 하지 않습니다. 게이트웨이 장치는 다운스트림 장치에서 메시지를 수신 해야 하기 때문에 다릅니다. 다운스트림 장치와 게이트웨이 장치 사이에 방화벽이 있는 경우 방화벽을 통해 통신도 가능 해야 합니다.

게이트웨이 시나리오가 작동 하려면 다운스트림 장치에서 인바운드 트래픽에 대해 IoT Edge 허브의 지원 되는 프로토콜 중 하나 이상을 열어야 합니다. 지원 되는 프로토콜은 MQTT, AMQP, HTTPS, Websocket을 통한 MQTT, Websocket을 통한 AMQP입니다.

| 포트 | 프로토콜 |
| ---- | -------- |
| 8883 | MQTT |
| 5671 | AMQP |
| 443 | HTTPS <br> MQTT + WS <br> AMQP + WS |

## <a name="route-messages-from-downstream-devices"></a>다운스트림 디바이스에서 메시지 라우팅

IoT Edge 런타임은 모듈에서 전송한 메시지와 같은 다운스트림 디바이스에서 전송된 메시지를 라우팅할 수 있습니다. 이 기능을 사용 하면 클라우드에서 데이터를 전송 하기 전에 게이트웨이에서 실행 되는 모듈에서 분석을 수행할 수 있습니다.

현재 다운스트림 디바이스에서 보낸 메시지를 라우팅하는 방식은 모듈에 의해 전송 된 메시지와 구별됩니다. 모듈에서 보낸 메시지에는 모두 **connectionModuleId**라는 시스템 속성이 포함되지만 다운스트림 디바이스에서 보낸 메시지에는 포함되지 않습니다. 경로의 WHERE 절을 사용하여 해당 시스템 특성을 포함하는 메시지를 제외할 수 있습니다.

아래 경로는 다운스트림 장치에서 이라는 `ai_insights`모듈에 메시지를 보낸 다음에서 `ai_insights` IoT Hub 하는 예제입니다.

```json
{
    "routes":{
        "sensorToAIInsightsInput1":"FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO BrokeredEndpoint(\"/modules/ai_insights/inputs/input1\")",
        "AIInsightsToIoTHub":"FROM /messages/modules/ai_insights/outputs/output1 INTO $upstream"
    }
}
```

메시지 라우팅에 대한 자세한 내용은 [모듈 배포 및 경로 설정](./module-composition.md#declare-routes)을 참조하세요.

## <a name="enable-extended-offline-operation"></a>확장 된 오프 라인 작업 사용

IoT Edge 런타임의 [v 1.0.4 릴리스부터](https://github.com/Azure/azure-iotedge/releases/tag/1.0.4) 는 장치에 연결 하는 게이트웨이 장치와 다운스트림 장치를 확장 된 오프 라인 작업에 대해 구성할 수 있습니다.

이 기능을 사용 하면 로컬 모듈 또는 다운스트림 장치에서 필요에 따라 IoT Edge 장치를 다시 인증 하 고 IoT hub와의 연결을 끊은 경우에도 메시지 및 메서드를 사용 하 여 서로 통신할 수 있습니다. 자세한 내용은 [IoT Edge 디바이스, 모듈 및 하위 디바이스용 확장 오프라인 기능 이해](offline-capabilities.md)를 참조하세요.

확장 된 오프 라인 기능을 사용 하도록 설정 하려면 연결 하는 IoT Edge 게이트웨이 장치와 다운스트림 장치 간에 부모-자식 관계를 설정 합니다. 이러한 단계는 [Azure IoT Hub에 대 한 다운스트림 장치 인증](how-to-authenticate-downstream-device.md)에 자세히 설명 되어 있습니다.

## <a name="next-steps"></a>다음 단계

투명한 게이트웨이로 작동하는 IoT Edge 디바이스가 생겼습니다. 이제 게이트웨이를 신뢰하고 메시지를 보내도록 다운스트림 디바이스를 구성해야 합니다. 투명 게이트웨이 시나리오를 설정 하는 다음 단계에 대 한 [Azure IoT Hub 위해 다운스트림 장치를 인증](how-to-authenticate-downstream-device.md) 하려면 계속 진행 합니다.
