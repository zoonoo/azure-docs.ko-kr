---
title: 투명한 게이트웨이 디바이스 만들기 - Azure IoT Edge | Microsoft Docs
description: Azure IoT Edge 디바이스를 다운스트림 디바이스의 정보를 처리할 수 있는 투명한 게이트웨이로 사용
author: kgremban
ms.author: kgremban
ms.date: 03/01/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 76d04b8d55b25c3fd5c96f34ad38088472c504b5
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122536352"
---
# <a name="configure-an-iot-edge-device-to-act-as-a-transparent-gateway"></a>IoT Edge 디바이스를 투명 게이트웨이로 작동하도록 구성

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

이 문서에서는 다른 디바이스가 IoT Hub와 통신하는 투명한 게이트웨이로 작동하도록 IoT Edge 디바이스를 구성하기 위한 자세한 지침을 제공합니다. 이 문서에서 *IoT Edge 게이트웨이* 라는 용어는 투명한 게이트웨이로 구성된 IoT Edge 디바이스를 의미합니다. 자세한 내용은 [IoT Edge 디바이스를 게이트웨이로 사용하는 방법](./iot-edge-as-gateway.md)을 참조하세요.

<!-- 1.1 -->
::: moniker range="iotedge-2018-06"

>[!NOTE]
>IoT Edge 버전 1.1 이상에서 IoT Edge 디바이스는 IoT Edge 게이트웨이의 다운스트림일 수 없습니다.
>
>다운스트림 디바이스는 파일 업로드를 사용할 수 없습니다.

::: moniker-end

<!-- 1.2.0 -->
::: moniker range=">=iotedge-2020-11"

>[!NOTE]
>다운스트림 디바이스는 파일 업로드를 사용할 수 없습니다.

::: moniker-end

성공적인 투명 게이트웨이 연결을 설정하기 위한 세 가지 일반적인 단계가 있습니다. 이 문서는 첫 번째 단계에 대해 설명합니다.

1. **다운스트림 디바이스를 안전하게 연결할 수 있도록 게이트웨이 디바이스를 서버로 구성합니다. 다운스트림 디바이스에서 메시지를 수신하고 적절한 대상으로 라우팅하도록 게이트웨이를 설정합니다.**
2. IoT Hub를 사용하여 인증할 수 있도록 다운스트림 디바이스에 대한 디바이스 ID를 만듭니다. 게이트웨이 디바이스를 통해 메시지를 보내도록 다운스트림 디바이스를 구성합니다. 이러한 단계는 [Azure IoT Hub에 대한 다운스트림 디바이스 인증](how-to-authenticate-downstream-device.md)을 참조하세요.
3. 다운스트림 디바이스를 게이트웨이 디바이스에 연결하고 메시지 보내기를 시작합니다. 이 단계에 대해서는 [다운스트림 디바이스를 Azure IoT Edge 게이트웨이에 연결](how-to-connect-downstream-device.md)을 참조하세요.

디바이스가 게이트웨이로 작동하려면 다운스트림 디바이스에 안전하게 연결되어야 합니다. Azure IoT Edge를 사용하면 PKI(공개 키 인프라)를 사용하여 이러한 디바이스 간에 안전한 연결을 설정할 수 있습니다. 이 경우, 투명한 게이트웨이로 작동하는 IoT Edge 디바이스에 다운스트림 디바이스를 연결할 수 있습니다. 적절한 보안을 유지하기 위해 다운스트림 디바이스는 게이트웨이 디바이스의 ID를 확인해야 합니다. 이 ID 검사는 디바이스가 잠재적으로 악성 게이트웨이에 연결하는 것을 방지합니다.

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"
다운스트림 디바이스는 [Azure IoT Hub](../iot-hub/index.yml) 클라우드 서비스를 사용하여 생성된 ID가 있는 애플리케이션 또는 플랫폼이 될 수 있습니다. 이러한 애플리케이션은 [Azure IoT 디바이스 SDK](../iot-hub/iot-hub-devguide-sdks.md)를 사용하는 경우가 많습니다. 다운스트림 디바이스는 IoT Edge 게이트웨이 디바이스 자체에서 실행 중인 애플리케이션일 수도 있습니다. 그러나 IoT Edge 디바이스는 IoT Edge 게이트웨이의 다운스트림일 수 없습니다.
:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"
다운스트림 디바이스는 [Azure IoT Hub](../iot-hub/index.yml) 클라우드 서비스를 사용하여 생성된 ID가 있는 애플리케이션 또는 플랫폼이 될 수 있습니다. 이러한 애플리케이션은 [Azure IoT 디바이스 SDK](../iot-hub/iot-hub-devguide-sdks.md)를 사용하는 경우가 많습니다. 다운스트림 디바이스는 IoT Edge 게이트웨이 디바이스 자체에서 실행 중인 애플리케이션일 수도 있습니다.
:::moniker-end
<!-- end 1.2 -->

디바이스-게이트웨이 토폴로지에 필요한 신뢰를 설정하는 어떤 인증서 인프라도 만들 수 있습니다. 이 문서에서는 IoT Hub에서 [X.509 CA 보안](../iot-hub/iot-hub-x509ca-overview.md)을 사용하도록 설정하는 데 사용하는 것과 동일한 인증서 설정을 가정합니다. 여기에는 특정 IoT Hub에 연결된 X.509 CA 인증서(IoT Hub 루트 CA), 이 CA로 서명된 일련의 인증서, IoT Edge 디바이스를 위한 CA가 포함됩니다.

>[!NOTE]
>이러한 문서 전체에서 사용되는 *루트 CA 인증서* 라는 용어는 PKI 인증서 체인의 최상위 인증 기관 공용 인증서를 나타냅니다. 반드시 신디케이티드 인증 기관의 인증서 루트는 아닙니다. 대부분의 경우 실제로 중간 CA 공용 인증서입니다.

다음 단계에서는 인증서를 만들고 게이트웨이의 적절한 위치에 설치하는 프로세스를 설명합니다. 머신을 사용하여 인증서를 생성한 다음, IoT Edge 디바이스로 복사할 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

IoT Edge가 설치된 Linux 또는 Windows 디바이스입니다.

디바이스가 준비되지 않은 경우 Azure 가상 머신에서 만들 수 있습니다. [가상 Linux 디바이스에 첫 번째 IoT Edge 모듈 배포](quickstart-linux.md)의 단계에 따라 IoT Hub를 만들고 가상 머신을 만들고 IoT Edge 런타임을 구성합니다.

## <a name="set-up-the-device-ca-certificate"></a>디바이스 CA 인증서 설정

모든 IoT Edge 게이트웨이에는 장치 CA 인증서가 설치되어 있어야 합니다. IoT Edge 보안 디먼은 IoT Edge 디바이스 CA 인증서를 사용하여 워크로드 CA 인증서에 서명합니다. 그 다음 이 인증서는 IoT Edge 허브에 대한 서버 인증서를 서명합니다. 게이트웨이는 연결을 시작할 때 다운스트림 디바이스에 해당하는 서버 인증서를 나타냅니다. 다운스트림 디바이스에서 서버 인증서가 루트 CA 인증서로 롤업되는 인증서 체인의 일부인지 확인합니다. 이 프로세스를 사용하면 다운스트림 디바이스는 게이트웨이가 신뢰할 수 있는 원본에서 나왔는지 확인할 수 있습니다. 자세한 내용은 [Azure IoT Edge에서 인증서를 사용하는 방법 이해](iot-edge-certs.md)를 참조하십시오.

![게이트웨이 인증서 설정](./media/how-to-create-transparent-gateway/gateway-setup.png)

루트 CA 인증서와 디바이스 CA 인증서 (프라이빗 키 포함)가 IoT Edge 게이트웨이 디바이스에 있어야 하고 IoT Edge 구성 파일에 구성되어야 합니다. 이 경우 *루트 CA 인증서* 는 이 IoT Edge 시나리오에 대한 최상위 인증 기관을 의미합니다. 게이트웨이 디바이스 CA 인증서와 다운스트림 디바이스 인증서가 동일한 루트 CA 인증서로 롤업되어야 합니다.

>[!TIP]
>IoT Edge 디바이스에 루트 CA 인증서 및 디바이스 CA 인증서를 설치하는 프로세스는 [IoT Edge 디바이스에서 인증서 관리](how-to-manage-device-certificates.md)에도 자세히 설명되어 있습니다.

다음 파일을 준비해야 합니다.

* 루트 CA 인증서
* 디바이스 CA 인증서
* 디바이스 CA 프라이빗 키

프로덕션 시나리오의 경우, 사용자 고유의 인증 기관을 통해 이러한 파일을 생성해야 합니다. 개발 및 테스트 시나리오에서는 데모 인증서를 사용할 수 있습니다.

자신의 인증 기관이 없고 데모 인증서를 사용하려는 경우 [데모 인증서를 만들어 IoT Edge 디바이스 기능 테스트](how-to-create-test-certificates.md)의 지침에 따라 파일을 만듭니다. 해당 페이지에서 다음 단계를 수행해야 합니다.

   1. 시작하려면 디바이스에서 인증서를 생성하기 위해 스크립트를 설정합니다.
   2. 루트 CA 인증서를 만듭니다. 해당 지침이 끝나는 부분에 루트 CA 인증서 파일이 있습니다.
      * `<path>/certs/azure-iot-test-only.root.ca.cert.pem`.
   3. IoT Edge 디바이스 CA 인증서를 만듭니다. 해당 지침이 끝나는 부분에 디바이스 CA 인증서와 해당하는 프라이빗 키가 제공됩니다.
      * `<path>/certs/iot-edge-device-<cert name>-full-chain.cert.pem` 또는
      * `<path>/private/iot-edge-device-<cert name>.key.pem`

다른 컴퓨터에서 인증서를 만든 경우 IoT Edge 디바이스에 복사하고 다음 단계를 진행합니다.

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

1. IoT Edge 디바이스에서 보안 디먼 구성 파일을 엽니다.

   * Windows: `C:\ProgramData\iotedge\config.yaml`
   * Linux: `/etc/iotedge/config.yaml`

1. 파일의 **인증서 설정** 섹션을 찾습니다. **인증서** 로 시작하는 네 줄의 주석 처리를 제거하고 다음 속성에 대한 값으로 세 개 파일에 파일 URI를 제공합니다.
   * **device_ca_cert**: 디바이스 CA 인증서
   * **device_ca_pk**: 디바이스 CA 프라이빗 키
   * **trusted_ca_certs**: 루트 CA 인증서

   **인증서** 줄에는 앞에 공백이 없고 다른 줄은 두 개의 공백으로 들여쓰기되었는지 확인합니다.

1. 파일을 저장하고 닫습니다.

1. IoT Edge를 다시 시작합니다.
   * Windows: `Restart-Service iotedge`
   * Linux: `sudo systemctl restart iotedge`
:::moniker-end
<!-- end 1.1 -->

<!--1.2 -->
:::moniker range=">=iotedge-2020-11"

1. IoT Edge 디바이스에서 구성 파일을 엽니다. `/etc/aziot/config.toml`

   >[!TIP]
   >구성 파일이 디바이스에 아직 없는 경우 `/etc/aziot/config.toml.edge.template`을(를) 템플릿으로 사용하여 구성 파일을 만듭니다.

1. `trust_bundle_cert` 매개 변수를 찾습니다. 이 줄의 주석 처리를 제거하고 디바이스의 루트 CA 인증서 파일에 대한 파일 URI를 제공합니다.

1. 파일의 `[edge_ca]` 섹션을 찾습니다. 이 섹션에서 세 줄의 주석 처리를 제거하고 다음 속성의 값으로 인증서 및 키 파일에 대한 파일 URI를 제공합니다.
   * **cert**: 디바이스 CA 인증서
   * **pk**: 디바이스 CA 프라이빗 키

1. 파일을 저장하고 닫습니다.

1. 변경 내용을 적용합니다.

   ```bash
   sudo iotedge config apply
   ```

:::moniker-end
<!-- end 1.2 -->

## <a name="deploy-edgehub-and-route-messages"></a>edgeHub 배포 및 메시지 라우팅

다운스트림 디바이스는 게이트웨이 디바이스에 원격 분석 및 메시지를 보냅니다. 여기에서 IoT Edge 허브 모듈은 정보를 다른 모듈 또는 IoT Hub로 라우팅해야 합니다. 이 기능을 위해 게이트웨이 디바이스를 준비하려면 다음을 확인합니다.

* IoT Edge 허브 모듈이 디바이스에 배포됩니다.

  IoT Edge를 디바이스에 처음으로 설치하는 경우 IoT Edge 에이전트라는 하나의 시스템 모듈만 자동으로 시작합니다. 디바이스에 대한 첫 번째 배포를 만든 후에는 두 번째 시스템 모듈인 IoT Edge 허브도 시작됩니다. **edgeHub** 모듈이 디바이스에서 실행되고 있지 않은 경우 디바이스에 대한 배포를 만듭니다.

* IoT Edge 허브 모듈은 다운스트림 디바이스에서 들어오는 메시지를 처리하도록 설정된 경로를 포함합니다.

  게이트웨이 디바이스는 다운스트림 디바이스의 메시지를 처리할 수 있는 경로가 있어야 합니다. 그렇지 않으면 해당 메시지가 처리되지 않습니다. 게이트웨이 디바이스의 모듈에 메시지를 보내거나 IoT Hub에 직접 보낼 수 있습니다.

IoT Edge 허브 모듈을 배포하고 다운스트림 디바이스에서 들어오는 메시지를 처리하는 경로로 구성하려면 다음 단계를 수행합니다.

1. Azure Portal에서 IoT Hub로 이동합니다.

2. **IoT Edge** 로 이동하고 게이트웨이로 사용할 IoT Edge 디바이스를 선택합니다.

3. **모듈 설정** 을 선택합니다.

4. **모듈** 페이지에서 게이트웨이 디바이스에 배포하려는 모듈을 추가할 수 있습니다. 이 문서에서는 이 페이지에서 명시적으로 설정하지 않아도 되는 edgeHub 모듈을 구성하고 배포하는 방법을 집중적으로 설명합니다.

5. **다음: 경로** 를 선택합니다.

6. **경로** 페이지에서 다운스트림 디바이스에서 오는 메시지를 처리할 경로가 있는지 확인합니다. 예를 들면 다음과 같습니다.

   * 모듈이나 다운스트림 디바이스에서 IoT Hub로 모든 메시지를 보내는 경로입니다.
       * **이름**: `allMessagesToHub`
       * **값**: `FROM /messages/* INTO $upstream`

   * 모든 다운스트림 디바이스에서 IoT Hub로 모든 메시지를 보내는 경로입니다.
      * **이름**: `allDownstreamToHub`
      * **값**: `FROM /messages/* WHERE NOT IS_DEFINED ($connectionModuleId) INTO $upstream`

      이 경로는 IoT Edge 모듈의 메시지와는 달리 다운스트림 디바이스의 메시지에 연결된 모듈 ID가 없기 때문에 작동합니다. 경로의 **WHERE** 절을 사용하면 해당 시스템 속성을 사용하여 메시지를 필터링할 수 있습니다.

      메시지 라우팅에 대한 자세한 내용은 [모듈 배포 및 경로 설정](./module-composition.md#declare-routes)을 참조하세요.

7. 경로를 만든 후 **검토 + 만들기** 를 선택합니다.

8. **리뷰 + 생성** 페이지에서 **생성** 를 선택합니다.

## <a name="open-ports-on-gateway-device"></a>게이트웨이 디바이스에서 포트 열기

IoT Hub와의 모든 통신은 아웃바운드 연결을 통해 수행되므로 표준 IoT Edge 디바이스는 작동하기 위해 인바운드 연결이 필요하지 않습니다. 게이트웨이 디바이스는 다운스트림 디바이스에서 메시지를 수신해야 하기 때문에 다릅니다. 다운스트림 디바이스와 게이트웨이 디바이스 사이에 방화벽이 있는 경우 방화벽을 통해서도 통신이 가능해야 합니다.

게이트웨이 시나리오가 작동하려면 다운스트림 디바이스의 인바운드 트래픽에 대해 IoT Edge 허브의 지원되는 프로토콜 중 하나 이상을 열어야 합니다. 지원되는 프로토콜은 MQTT, AMQP, HTTPS, Websockets을 통한 MQTT, Websockets을 통한 AMQP입니다.

| 포트 | 프로토콜 |
| ---- | -------- |
| 8883 | MQTT |
| 5671 | AMQP |
| 443 | HTTPS <br> MQTT+WS <br> AMQP+WS |

## <a name="next-steps"></a>다음 단계

이제 투명한 게이트웨이로 설정된 IoT Edge 디바이스가 있으므로 게이트웨이를 신뢰하고 게이트웨이에 메시지를 보내도록 다운스트림 디바이스를 구성해야 합니다. 투명한 게이트웨이 설정 시나리오의 다음 단계를 위해 [Azure IoT Hub에 대해 다운스트림 디바이스 인증](how-to-authenticate-downstream-device.md)으로 계속 진행합니다.