---
title: Microsoft 연결된 캐시 미리 보기 배포 시나리오 샘플 | Microsoft Docs
titleSuffix: Device Update for Azure IoT Hub
description: Microsoft 연결된 캐시 미리 보기 배포 시나리오 샘플 자습서
author: andyriv
ms.author: andyriv
ms.date: 2/16/2021
ms.topic: tutorial
ms.service: iot-hub-device-update
ms.openlocfilehash: c116bbf5ea9f5fc6e58962e02c93c630fc747d9e
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107811727"
---
# <a name="microsoft-connected-cache-preview-deployment-scenario-samples"></a>Microsoft 연결된 캐시 미리 보기 배포 시나리오 샘플

## <a name="single-level-azure-iot-edge-gateway-no-proxy"></a>프록시를 사용하지 않는 단일 수준 Azure IoT Edge 게이트웨이

아래 다이어그램에서는 CDN 리소스에 직접 액세스할 수 있는 Azure IoT Edge 게이트웨이와 이 Azure IoT Edge 게이트웨이의 인터넷 격리된 자식 디바이스인 Azure IoT 리프 디바이스(예: Raspberry PI)가 하나씩 있는 시나리오에 대해 설명합니다. 

  :::image type="content" source="media/connected-cache-overview/disconnected-device-update.png" alt-text="Microsoft 연결된 캐시 연결되지 않은 디바이스 업데이트" lightbox="media/connected-cache-overview/disconnected-device-update.png":::

1. Azure IoT Hub에서 Microsoft 연결된 캐시 모듈을 Azure IoT Edge 게이트웨이 디바이스 배포에 추가합니다(모듈을 가져오는 방법에 대한 자세한 내용은 [연결이 끊긴 디바이스 지원](connected-cache-disconnected-device-update.md) 참조).
2. 배포에 대한 환경 변수를 추가합니다. 다음은 환경 변수의 예입니다.

    **환경 변수**
    
    | 속성                          | 값                                                                 |
    | ----------------------------- | ----------------------------------------------------------------------| 
    | CACHE_NODE_ID                 | [환경 변수](connected-cache-configure.md) 설명을 참조하세요. |
    | CUSTOMER_ID                   | [환경 변수](connected-cache-configure.md) 설명을 참조하세요. |
    | CUSTOMER_KEY                  | [환경 변수](connected-cache-configure.md) 설명을 참조하세요. |
    | STORAGE_1_SIZE_GB             | 10                                                                    |

3. 배포에 대한 컨테이너 만들기 옵션을 추가합니다. 다음은 컨테이너 만들기 옵션의 예입니다.

### <a name="container-create-options"></a>컨테이너 만들기 옵션

```json
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
}
```

Microsoft 연결된 캐시가 제대로 작동하는지 확인하려면 모듈을 호스트하는 IoT Edge 디바이스 또는 네트워크에 연결된 아무 디바이스의 터미널에 다음 명령을 실행합니다. \<Azure IoT Edge Gateway IP\>를 IoT Edge 게이트웨이의 IP 주소나 호스트 이름으로 바꿉니다. 이 보고서의 표시 유형에 대한 자세한 내용은 환경 변수 세부 정보를 참조하세요.

```bash
    wget http://<IoT Edge Gateway IP>/mscomtest/wuidt.gif?cacheHostOrigin=au.download.windowsupdate.com
```

## <a name="single-level-azure-iot-edge-gateway-with-outbound-unauthenticated-proxy"></a>인증되지 않은 아웃바운드 프록시를 사용하는 단일 수준 Azure IoT Edge 게이트웨이

이 시나리오에는 인증되지 않은 아웃바운드 프록시를 통해 CDN 리소스에 액세스하는 Azure IoT Edge 게이트웨이가 있습니다. Microsoft 연결된 캐시는 사용자 지정 리포지토리에서 콘텐츠를 캐시하도록 구성되며 요약 보고서는 네트워크의 모든 사용자에게 표시됩니다. 다음은 설정되는 MCC 환경 변수의 예입니다.

  :::image type="content" source="media/connected-cache-overview/single-level-proxy.png" alt-text="Microsoft 연결된 캐시 단일 수준 프록시" lightbox="media/connected-cache-overview/single-level-proxy.png":::

1. Azure IoT Hub에서 Azure IoT Edge 게이트웨이 디바이스 배포에 Microsoft 연결된 캐시 모듈을 추가합니다.
2. 배포에 대한 환경 변수를 추가합니다. 다음은 환경 변수의 예입니다.

    **환경 변수**

    | 속성                          | 값                                                                 |
    | ----------------------------- | ----------------------------------------------------------------------| 
    | CACHE_NODE_ID                 | [환경 변수](connected-cache-configure.md) 설명을 참조하세요. |
    | CUSTOMER_ID                   | [환경 변수](connected-cache-configure.md) 설명을 참조하세요. |
    | CUSTOMER_KEY                  | [환경 변수](connected-cache-configure.md) 설명을 참조하세요. |
    | STORAGE_1_SIZE_GB             | 10                                                                    |
    | CACHEABLE_CUSTOM_1_HOST       | Packagerepo.com:80                                                    |
    | CACHEABLE_CUSTOM_1_CANONICAL  | Packagerepo.com                                                       |
    | IS_SUMMARY_ACCESS_UNRESTRICTED| true                                                                  |
    | UPSTREAM_PROXY                | 프록시 서버 IP 또는 FQDN                                          |

3. 배포에 대한 컨테이너 만들기 옵션을 추가합니다. MCC 컨테이너 만들기 옵션은 이전 예제와 차이가 없습니다. 다음은 컨테이너 만들기 옵션의 예입니다.

### <a name="container-create-options"></a>컨테이너 만들기 옵션

```json
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
}
```

Microsoft 연결된 캐시가 제대로 작동하는지 확인하려면 모듈을 호스트하는 Azure IoT Edge 디바이스 또는 네트워크에 연결된 아무 디바이스의 터미널에 다음 명령을 실행합니다. \<Azure IoT Edge Gateway IP\>를 IoT Edge 게이트웨이의 IP 주소나 호스트 이름으로 바꿉니다. 이 보고서의 표시 유형에 대한 자세한 내용은 환경 변수 세부 정보를 참조하세요.

```bash
    wget http://<Azure IoT Edge Gateway IP>/mscomtest/wuidt.gif?cacheHostOrigin=au.download.windowsupdate.com 
```
