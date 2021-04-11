---
title: 인증되지 않은 아웃바운드 프록시를 사용하는 Microsoft 연결된 캐시 2수준 중첩 Azure IoT Edge 게이트웨이 | Microsoft Docs
titleSuffix: Device Update for Azure IoT Hub
description: 인증되지 않은 아웃바운드 프록시를 사용하는 Microsoft 연결된 캐시 2수준 중첩 Azure IoT Edge 게이트웨이 자습서
author: andyriv
ms.author: andyriv
ms.date: 2/16/2021
ms.topic: tutorial
ms.service: iot-hub-device-update
ms.openlocfilehash: 7facb74cd407c576b2a7b119f19427dcd185f04e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105568820"
---
# <a name="microsoft-connected-cache-preview-deployment-scenario-sample-two-level-nested-azure-iot-edge-gateway-with-outbound-unauthenticated-proxy"></a>Microsoft 연결된 캐시 미리 보기 배포 시나리오 샘플: 인증되지 않은 아웃바운드 프록시를 사용하는 2수준 중첩 Azure IoT Edge 게이트웨이

아래 다이어그램을 보면 이 시나리오에는 Azure IoT Edge 게이트웨이 및 다운스트림 Azure IoT Edge 디바이스, 다른 Azure IoT Edge 게이트웨이의 자식인 Azure IoT Edge 게이트웨이, IT DMZ의 프록시 서버가 하나씩 있습니다. 다음은 Azure Portal UX에서 Azure IoT Edge 게이트웨이에 배포된 두 MCC 모듈 모두에 대해 설정되는 Microsoft 연결된 캐시 환경 변수의 예입니다. 표시된 예제에서는 2수준의 Azure IoT Edge 게이트웨이를 구성하는 방법을 보여 주지만 Microsoft 연결된 캐시가 지원할 업스트림 호스트의 깊이에는 제한이 없습니다. MCC 컨테이너 만들기 옵션은 이전 예제와 차이가 없습니다.

Azure IoT Edge 게이트웨이의 계층화된 배포를 구성하는 방법에 대한 자세한 내용은 [다운스트림 IoT Edge 디바이스 연결 - Azure IoT Edge](../iot-edge/how-to-connect-downstream-iot-edge-device.md?preserve-view=true&tabs=azure-portal&view=iotedge-2020-11) 설명서를 참조하세요. 또한 Azure IoT Edge, Microsoft 연결된 캐시 및 사용자 지정 모듈을 배포할 때 모든 모듈은 동일한 컨테이너 레지스트리에 있어야 합니다.

아래 다이어그램에서는 CDN 리소스에 직접 액세스할 수 있는 한 Azure IoT Edge 게이트웨이가 Raspberry Pi 같은 Azure IoT 리프 디바이스의 부모 역할을 하는 다른 Azure IoT Edge 게이트웨이의 부모 역할을 하는 시나리오를 설명합니다. Azure IoT Edge 게이트웨이 부모만 인터넷으로 CDN 리소스에 연결되고 Azure IoT Edge 자식 및 Azure IoT 디바이스는 모두 인터넷에서 격리됩니다. 

  :::image type="content" source="media/connected-cache-overview/nested-level-proxy.png" alt-text="중첩된 Microsoft 연결된 캐시" lightbox="media/connected-cache-overview/nested-level-proxy.png":::

## <a name="parent-gateway-configuration"></a>부모 게이트웨이 구성

1. Azure IoT Hub에서 Azure IoT Edge 게이트웨이 디바이스 배포에 Microsoft 연결된 캐시 모듈을 추가합니다.
2. 배포에 대한 환경 변수를 추가합니다. 다음은 환경 변수의 예입니다.

    **환경 변수**

    | 속성                 | 값                                       |
    | ----------------------------- | --------------------------------------------| 
    | CACHE_NODE_ID                 | 위의 환경 변수 설명을 참조하세요. |
    | CUSTOMER_ID                   | 위의 환경 변수 설명을 참조하세요. |
    | CUSTOMER_KEY                  | 위의 환경 변수 설명을 참조하세요. |
    | STORAGE_ *N* _SIZE_GB           | N = 5                                       |
    | CACHEABLE_CUSTOM_1_HOST       | Packagerepo.com:80                          |
    | CACHEABLE_CUSTOM_1_CANONICAL  | Packagerepo.com                             |
    | IS_SUMMARY_ACCESS_UNRESTRICTED| true                                        |
    | UPSTREAM_PROXY                | 프록시 서버 IP 또는 FQDN                     |

3. 배포에 대한 컨테이너 만들기 옵션을 추가합니다. MCC 컨테이너 만들기 옵션은 이전 예제와 차이가 없습니다. 다음은 컨테이너 만들기 옵션의 예입니다.

### <a name="container-create-options"></a>컨테이너 만들기 옵션

```markdown
{
    "HostConfig": {
        "Binds": [
            "/MicrosoftConnectedCache1/:/nginx/cache1/"
        ],
        "PortBindings": {
            "8081/tcp": [
                {
                    "HostPort": "80"
                }
            ],
            "5000/tcp": [
                {
                    "HostPort": "5100"
                }
            ]
        }
    }
```

## <a name="child-gateway-configuration"></a>자식 게이트웨이 구성

>[!Note]
>사용자 고유의 프라이빗 레지스트리의 구성에 사용된 컨테이너를 복제한 경우 모듈 배포에서 config.toml 설정 및 런타임 설정을 수정해야 합니다. 자세한 내용은 [자습서 -IoT Edge 디바이스 계층 구조 만들기 - Azure IoT Edge](../iot-edge/tutorial-nested-iot-edge.md?preserve-view=true&tabs=azure-portal&view=iotedge-2020-11#deploy-modules-to-the-lower-layer-device)를 참조하세요.

1. 아래 예제에 표시된 대로 Edge 에이전트의 이미지 경로를 수정합니다.

```markdown
[agent]
name = "edgeAgent"
type = "docker"
env = {}
[agent.config]
image = "<parent_device_fqdn_or_ip>:8000/iotedge/azureiotedge-agent:1.2.0-rc2"
auth = {}
```
2. 이 예제에 표시된 대로 Azure IoT Edge 배포에서 Edge Hub 및 Edge 에이전트 런타임 설정을 수정합니다.
    
    * Edge 허브 아래 이미지 필드에 ```$upstream:8000/iotedge/azureiotedge-hub:1.2.0-rc2```를 입력합니다.
    * Edge 에이전트 아래 이미지 필드에 ```$upstream:8000/iotedge/azureiotedge-agent:1.2.0-rc2```를 입력합니다.

3. Azure IoT Hub에서 Azure IoT Edge 게이트웨이 디바이스 배포에 Microsoft 연결된 캐시 모듈을 추가합니다.

   * 모듈의 이름을 선택합니다. ```ConnectedCache```
   * 이미지 URI를 수정합니다. ```$upstream:8000/mcc/linux/iot/mcc-ubuntu-iot-amd64:latest```

4. 부모 배포에 사용되는 것과 동일한 환경 변수 및 컨테이너 만들기 옵션을 추가합니다.

Microsoft 연결된 캐시가 제대로 작동하는지 확인하려면 모듈을 호스트하는 IoT Edge 디바이스 또는 네트워크에 연결된 아무 디바이스의 터미널에 다음 명령을 실행합니다.

```bash
    wget "http://<CHILD Azure IoT Edge Gateway IP>/mscomtest/wuidt.gif?cacheHostOrigin=au.download.windowsupdate.com
```