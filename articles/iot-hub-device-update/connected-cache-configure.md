---
title: Azure IoT Hub용 디바이스 업데이트의 Microsoft 연결된 캐시 구성 | Microsoft Docs
titleSuffix: Device Update for Azure IoT Hub
description: Azure IoT Hub용 디바이스 업데이트의 Microsoft 연결된 캐시 개요
author: andyriv
ms.author: andyriv
ms.date: 2/16/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: 6e7b8d567034cc9557a2d9fcec4afbffa878cf75
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107811834"
---
# <a name="configure-microsoft-connected-cache-for-device-update-for-azure-iot-hub"></a>Azure IoT Hub용 디바이스 업데이트의 Microsoft 연결된 캐시 구성

Microsoft 연결된 캐시는 Azure IoT Edge 게이트웨이에 Azure IoT Edge 모듈로 배포됩니다. 다른 Azure IoT Edge 모듈과 마찬가지로 MCC 모듈 배포 환경 변수 및 컨테이너 생성 옵션은 Microsoft 연결된 캐시 모듈 구성에 활용됩니다.  본 섹션에서는 고객이 Azure IoT Hub용 디바이스 업데이트를 통해 사용할 Microsoft 연결된 캐시 모듈을 성공적으로 배포하는 데 필요한 환경 변수 및 컨테이너 생성 옵션을 정의합니다.

## <a name="microsoft-connected-cache-azure-iot-edge-module-deployment-details"></a>Microsoft 연결된 캐시 Azure IoT Edge 모듈 배포 세부 정보

Microsoft 연결된 캐시 모듈의 이름은 관리자가 결정합니다. 통신용 모듈의 이름을 활용하는 기타 모듈이나 서비스 상호 작용은 없습니다. 또한 Microsoft 연결된 캐시 서버의 부자 관계는 이러한 모듈 이름이 아니라 앞에서 논의한 대로 구성한 Azure IoT Edge 게이트웨이의 FQDN 또는 IP 주소에 따라 달라집니다.

Microsoft 연결된 캐시 Azure IoT Edge 모듈 환경 변수는 기본 모듈 ID 정보 및 기능 모듈 설정을 컨테이너에 전달하는 데 사용됩니다.

| 변수 이름                 | 값 형식                           | 필수/선택 | 기능                                    |
| ----------------------------- | ---------------------------------------| ----------------- | ------------------------------------------------ |
| CUSTOMER_ID                   | Azure 구독 ID GUID             | 필수          | 고객용 키로서, 캐시 노드의 보안<br>인증을 전송 최적화에 제공합니다.<br>권한 부여.<br>모듈이 작동하기 위해 필요합니다. |
| CACHE_NODE_ID                 | 캐시 노드 ID GUID                     | 필수          | Microsoft 연결된 캐시<br>노드를 전송 최적화 서비스에 고유하게 식별합니다.<br>모듈이<br> 작동하기 위해 필요합니다. |
| CUSTOMER_KEY                  | 고객 키 GUID                     | 필수          | 고객용 키로서, 캐시 노드의 보안<br>인증을 전송 최적화 서비스에 제공합니다.<br>모듈이 작동하기 위해 필요합니다.|
| STORAGE_ *N* _SIZE_GB           | 여기서 N은 캐시 드라이브입니다.   | 필수          | 콘텐츠를 캐시할 최대 9개의 드라이브 지정 및 최대 공간 지정<br>각 캐시 드라이브의 콘텐츠에 할당할 기가바이트입니다. 예:<br>STORAGE_1_SIZE_GB = 150<br>STORAGE_2_SIZE_GB = 50<br>드라이브 번호는<br>컨테이너 만들기 옵션 MicrosoftConnectedCache *N* 값에 지정된 캐시 드라이브 바인딩 값과 일치해야 합니다.<br>캐시의 최소 크기는 10GB입니다.|
| UPSTREAM_HOST                 | FQDN/IP                                | 선택 사항          | 연결된 캐시 노드가 인터넷에서 연결이 끊어진 경우<br>프록시로 작동하는 업스트림 Microsoft 연결된<br> 캐시 노드를 지정할 수 있는 값입니다. 해당 설정은<br> Nested IoT 시나리오 지원에 사용됩니다.<br>**참고:** Microsoft 연결된 캐시는 http 기본 포트 80에서 수신 대기합니다.|
| UPSTREAM_PROXY                | FQDN/IP:PORT                           | 선택 사항          | 아웃바운드 인터넷 프록시입니다.<br>ISA 95 네트워크인 경우 OT DMZ 프록시가 될 수도 있습니다. |
| CACHEABLE_CUSTOM_ *N* _HOST     | HOST/IP<br>FQDN                        | 선택 사항          | 사용자 지정 패키지 리포지토리를 지원하는 데 필요합니다.<br>리포지토리는 로컬로 호스팅될 수도 있고 인터넷으로 호스팅될 수도 있습니다.<br>구성할 수 있는 사용자 지정 호스트의 수는 무제한입니다.<br><br>예:<br>이름 = CACHEABLE_CUSTOM_1_HOST 값 = packages.foo.com<br> 이름 = CACHEABLE_CUSTOM_2_HOST 값 = packages.bar.com    |
| CACHEABLE_CUSTOM_ *N* _CANONICAL| Alias                                  | 선택 사항          | 사용자 지정 패키지 리포지토리를 지원하는 데 필요합니다.<br>해당 값은 별칭으로 사용될 수 있으며 캐시 서버가<br>각기 다른 DNS 이름을 참조할 때 사용합니다. 예를 들어 리포지토리 콘텐츠 호스트 이름이 packages.foo.com일 수 있지만,<br>다른 지역의 경우 호스트 이름에 추가적인 접두사가 붙어<br>westuscdn.packages.foo.com이나 eastuscdn.packages.foo.com일 수 있습니다.<br>정식 별칭을 설정하여 해당 콘텐츠가<br>동일한 호스트의 다른 CDN 원본에서 가져온 것과 중복되지 않는지 확인합니다.<br>정식 값의 형식은 중요하지 않지만 호스트에서 고유해야 합니다.<br>해당 값을 호스트 값에 일치시켜 설정하는 것이 가장 쉬운 방법입니다.<br><br>위의 사용자 지정 호스트 사례를 기반으로 하는 예:<br>이름 = CACHEABLE_CUSTOM_1_CANONICAL 값 = foopackages<br> 이름 = CACHEABLE_CUSTOM_2_CANONICAL 값 = packages.bar.com  |
| IS_SUMMARY_PUBLIC             | True 또는 False                          | 선택 사항          | 로컬 네트워크나 인터넷에서 요약 보고서를 볼 수 있습니다.<br>true로 설정된 경우 요약 보고서를 보려면 API 키(이후 설명)를 사용해야 합니다. |
| IS_SUMMARY_ACCESS_UNRESTRICTED| True 또는 False                          | 선택 사항          | 로컬 네트워크나 인터넷에서 요약 보고서를 볼 때<br>네트워크 디바이스의 API 키를 사용하지 않아도 되게 합니다. 브라우저를 통해 캐시 서버 요약 데이터를 확인하는<br>액세스를 잠그고 싶지 않은 경우 사용합니다. |
            
## <a name="microsoft-connected-cache-azure-iot-edge-module-container-create-options"></a>Microsoft 연결된 캐시 Azure IoT Edge 모듈 컨테이너 제작 옵션

MCC 모듈 배포를 위한 컨테이너 제작 옵션으로 MCC 모듈이 사용하는 스토리지 및 포트와 관련된 설정을 제어할 수 있습니다. MCC를 배포할 때 사용하는 필수 컨테이너 제작 변수 목록입니다.

### <a name="container-to-host-os-drive-mappings"></a>OS 드라이브 매핑을 호스트할 컨테이너

컨테이너 스토리지 위치를 디스크의 스토리지 위치에 매핑할 때 필요합니다. < 위치는 최대 아홉 개까지 지정할 수 있습니다.

>[!Note]
>드라이브 번호는 환경 변수 STORAGE_ *N* _SIZE_GB 값인 ```/MicrosoftConnectedCache*N*/:/nginx/cache*N*/```에 지정된 캐시 드라이브 바인딩 값과 일치해야 합니다.

### <a name="container-to-host-tcp-port-mappings"></a>TCP 포트 매핑을 호스트할 컨테이너

해당 옵션은 MCC가 콘텐츠 요청을 수신 대기하는 외부 머신 http 포트를 지정합니다. 기본 HostPort는 포트 80이며 ADU 클라이언트에서 현재 포트 80에 대해 요청할 때 다른 포트는 동시에 지원하지 않습니다. TCP 포트 8081은 내부 컨테이너 포트로 MCC가 수신 대기할 수는 있지만 변경할 수는 없습니다.

### <a name="container-service-tcp-port-mappings"></a>컨테이너 서비스 TCP 포트 매핑

Microsoft 연결된 캐시 모듈에는 .NET Core 서비스가 있어서 캐싱 엔진이 다양한 용도로 사용합니다.

>[!Note]
>레지스트리 프록시 모듈이 이미 호스트 포트 5000을 수신 대기하고 있으므로 Azure IoT Nested Edge를 지원하기 위해 HostPort를 5000으로 설정하지 않아야 합니다.


샘플 컨테이너 만들기 옵션

```json
{
    "HostConfig": {
        "Binds": [
            "/microsoftConnectedCache1/:/nginx/cache1/"
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

## <a name="microsoft-connected-cache-summary-report"></a>Microsoft 연결된 캐시 요약 보고서

현재는 요약 보고서를 통해서만 고객이 Azure IoT Edge 게이트웨이에 배포된 Microsoft 연결된 캐시 인스턴스 관련 캐싱 데이터를 볼 수 있습니다. 보고서는 15초 간격으로 생성되며 여기에는 시기별 평균 통계 및 해당 모듈의 수명주기 동안 집계된 통계가 포함됩니다. 고객이 관심을 가질 주요 통계는 다음과 같습니다.

* **hitBytes** - 캐시에서 직접 도착한 바이트 합계입니다.
* **missBytes** - Microsoft 연결된 캐시가 캐시 확인을 위해 CDN에서 다운로드해야 했던 바이트 합계입니다.
* **eggressBytes** - hitBytes 및 missBytes의 합계이자 클라이언트에게 전달된 전체 바이트 수입니다.
* **hitRatioBytes** - hitBytes에 대한 egressBytes 비율입니다.  예를 들어 어떤 시기에 전달된 eggressBytes가 100% hitBytes와 동일했다면 1이 됩니다.


요약 보고서는 `http://<FQDN/IP of Azure IoT Edge Gateway hosting MCC>:5001/summary`에서 \<Azure IoT Edge Gateway IP\>를 IoT Edge 게이트웨이의 IP 주소 또는 호스트 이름으로 바꿀 수 있습니다. 이 보고서의 표시 유형에 대한 자세한 내용은 환경 변수 세부 정보를 참조하세요.