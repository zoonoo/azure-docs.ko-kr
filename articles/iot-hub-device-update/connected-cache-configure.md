---
title: Azure IoT Hub에 대 한 장치 업데이트에 대 한 Microsoft 연결 캐시 구성 | Microsoft Docs
titleSuffix: Device Update for Azure IoT Hub
description: Azure IoT Hub에 대 한 장치 업데이트에 대 한 Microsoft 연결 캐시 개요
author: andyriv
ms.author: andyriv
ms.date: 2/16/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: 2903407f88b57a7be948cdeb0610e6d65df975b0
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101663162"
---
# <a name="configure-microsoft-connected-cache-for-device-update-for-azure-iot-hub"></a>Azure IoT Hub에 대 한 장치 업데이트에 대 한 Microsoft 연결 캐시 구성

Microsoft 연결 된 캐시는 게이트웨이를 Azure IoT Edge 모듈로 Azure IoT Edge 배포 됩니다. 다른 Azure IoT Edge 모듈과 마찬가지로, MCC 모듈 배포 환경 변수 및 컨테이너 만들기 옵션을 사용 하 여 Microsoft 연결 된 캐시 모듈을 구성 합니다.  이 섹션에서는 고객이 Azure IoT Hub 용 장치 업데이트에서 사용할 Microsoft 연결 된 캐시 모듈을 성공적으로 배포 하는 데 필요한 환경 변수 및 컨테이너 만들기 옵션을 정의 합니다.

## <a name="microsoft-connected-cache-azure-iot-edge-module-deployment-details"></a>Microsoft 연결 된 캐시 Azure IoT Edge 모듈 배포 세부 정보

Microsoft 연결 캐시 모듈의 이름은 관리자가 결정 합니다. 통신을 위해 모듈 이름을 사용 하는 다른 모듈 또는 서비스 상호 작용은 없습니다. 또한 Microsoft 연결 된 캐시 서버의 부모 자식 관계는이 모듈 이름에 종속 되지 않으며 앞에서 설명한 대로 구성 된 Azure IoT Edge 게이트웨이의 FQDN 또는 Ip 주소입니다.

Microsoft 연결 된 캐시 Azure IoT Edge 모듈 환경 변수는 기본 모듈 id 정보 및 기능 모듈 설정을 컨테이너에 전달 하는 데 사용 됩니다.

| 변수 이름                 | 값 형식                           | 필수/선택 | 기능                                    |
| ----------------------------- | ---------------------------------------| ----------------- | ------------------------------------------------ |
| CUSTOMER_ID                   | Azure 구독 ID GUID             | 필수          | 이는 보안을 제공 하는 고객의 키입니다.<br>배달 최적화에 대 한 캐시 노드 인증<br>권한 부여. 모듈이 작동 하기 위해 필요 합니다. |
| CACHE_NODE_ID                 | 캐시 노드 ID GUID                     | 필수          | Microsoft 연결 캐시를 고유 하 게 식별 합니다.<br>배달 최적화 서비스에 대 한 노드입니다. 순서 대로 필요<br> 모듈이 작동 합니다. |
| CUSTOMER_KEY                  | 고객 키 GUID                     | 필수          | 이는 보안을 제공 하는 고객의 키입니다.<br>배달 최적화 서비스에 대 한 캐시 노드의 인증입니다.<br>모듈이 작동 하기 위해 필요 합니다.|
| STORAGE_ *N* _SIZE_GB           | 여기서 N은 필요한 GB 수입니다.   | 필수          | 콘텐츠를 캐시 하는 데 최대 9 개의 드라이브를 지정 하 고 다음을 지정 합니다.<br>각 캐시 드라이브의 콘텐츠에 할당할 최대 공간 (Gb)입니다. 예:<br>STORAGE_1_SIZE_GB = 150<br>STORAGE_2_SIZE_GB = 50<br>드라이브 번호는 지정 된 캐시 드라이브 바인딩 값과 일치 해야 합니다.<br>컨테이너 만들기 옵션 MicrosoftConnectedCache *N* 값|
| UPSTREAM_HOST                 | FQDN/IP                                | Optional          | 이 값은 업스트림 Microsoft 연결을 지정할 수 있습니다.<br>연결 된 캐시 노드가 있는 경우 프록시 역할을 하는 캐시 노드입니다.<br> 인터넷에서 연결이 끊어졌습니다. 이 설정은를 지 원하는 데 사용 됩니다.<br> 중첩 된 IoT 시나리오입니다.<br>**참고:** Microsoft 연결 된 캐시는 http 기본 포트 80에서 수신 합니다.|
| UPSTREAM_PROXY                | FQDN/IP: 포트                           | Optional          | 아웃 바운드 인터넷 프록시입니다.<br>ISA 95 네트워크 인 경우에도이 프록시를 사용할 수 있습니다. |
| CACHEABLE_CUSTOM_ *N* _HOST     | 호스트/I P<br>FQDN                        | Optional          | 사용자 지정 패키지 리포지토리를 지 원하는 데 필요 합니다.<br>리포지토리는 로컬로 또는 인터넷에서 호스팅될 수 있습니다.<br>구성할 수 있는 사용자 지정 호스트 수에는 제한이 없습니다.<br><br>예:<br>이름 = CACHEABLE_CUSTOM_1_HOST 값 = packages.foo.com<br> 이름 = CACHEABLE_CUSTOM_2_HOST 값 = packages.bar.com    |
| CACHEABLE_CUSTOM_ *N* _CANONICAL| Alias                                  | Optional          | 사용자 지정 패키지 리포지토리를 지 원하는 데 필요 합니다.<br>이 값은 별칭으로 사용할 수 있으며 캐시 서버에서 참조 하는 데 사용 됩니다.<br>DNS 이름이 다릅니다. 예를 들어 리포지토리 콘텐츠 호스트 이름이 packages.foo.com 일 수 있습니다.<br>그러나 다른 지역의 경우 호스트 이름에 추가 되는 추가 접두사가 있을 수 있습니다.<br>예: westuscdn.packages.foo.com 및 eastuscdn.packages.foo.com.<br>정식 별칭을 설정 하 여 콘텐츠가 중복 되지 않도록 합니다.<br>동일한 호스트에서 가져온 콘텐츠는 다른 CDN 원본입니다.<br>정식 값의 형식은 중요 하지 않지만 호스트에서 고유 해야 합니다.<br>호스트 값과 일치 하도록 값을 설정 하는 것이 가장 쉽습니다.<br><br>위의 사용자 지정 호스트 예제를 기반으로 하는 예제:<br>Name = CACHEABLE_CUSTOM_1_CANONICAL Value = opopackages<br> 이름 = CACHEABLE_CUSTOM_2_CANONICAL 값 = packages.bar.com  |
| IS_SUMMARY_PUBLIC             | True 또는 False                          | Optional          | 로컬 네트워크 또는 인터넷에서 요약 보고서를 볼 수 있습니다.<br>True로 설정 된 경우 요약 보고서를 보려면 API 키 (뒷부분에서 설명)를 사용 해야 합니다. |
| IS_SUMMARY_ACCESS_UNRESTRICTED| True 또는 False                          | Optional          | 다음을 사용 하지 않고 로컬 네트워크 또는 인터넷에서 요약 보고서를 볼 수 있습니다.<br>네트워크에 있는 모든 장치에서 API 키를 사용 합니다. 액세스를 잠그지 않으려면 사용<br>브라우저를 통해 캐시 서버 요약 데이터를 볼 수 있습니다. |
            
## <a name="microsoft-connected-cache-azure-iot-edge-module-container-create-options"></a>Microsoft 연결 된 캐시 Azure IoT Edge 모듈 컨테이너 만들기 옵션

MCC 모듈 배포에 대 한 컨테이너 만들기 옵션은 MCC 모듈에서 사용 하는 저장소 및 포트와 관련 된 설정에 대 한 제어를 제공 합니다. MCC를 배포 하는 데 사용 되는 필수 컨테이너 생성 변수의 목록입니다.

### <a name="container-to-host-os-drive-mappings"></a>OS 드라이브 매핑을 호스트할 컨테이너

컨테이너 저장소 위치를 디스크의 저장소 위치에 매핑하는 데 필요 합니다. < 최대 9 개의 위치를 지정할 수 있습니다.

>[!Note]
>드라이브 번호는 환경 변수에 지정 된 캐시 드라이브 바인딩 값 STORAGE_ *N* _SIZE_GB 값과 일치 해야 합니다. ```/MicrosoftConnectedCache*N*/:/nginx/cache*N*/```

### <a name="container-to-host-tcp-port-mappings"></a>TCP 포트 매핑을 호스트할 컨테이너

이 옵션은 MCC가 콘텐츠 요청을 수신 대기 하는 외부 컴퓨터 http 포트를 지정 합니다. ADU 클라이언트가 현재 포트 80에 대 한 요청을 만들 때 기본 HostPort는 포트 80 및 다른 포트는 현재 지원 되지 않습니다. TCP 포트 8081는 MCC에서 수신 대기 하 고 변경할 수 없는 내부 컨테이너 포트입니다.

```markdown
8081/tcp": [
   {
       "HostPort": "80"
   }
]
```

### <a name="container-service-tcp-port-mappings"></a>컨테이너 서비스 TCP 포트 매핑

Microsoft 연결 된 캐시 모듈에는 다양 한 기능을 위해 캐싱 엔진에서 사용 하는 .NET Core 서비스가 있습니다.

>[!Note]
>Azure IoT 중첩 된 Edge를 지원 하려면 레지스트리 프록시 모듈이 호스트 포트 5000에서 이미 수신 하 고 있으므로 HostPort를 5000으로 설정 하지 않아야 합니다.

```markdown
5000/tcp": [
   {
       "HostPort": "5001"
   }
]
```

## <a name="microsoft-connected-cache-summary-report"></a>Microsoft 연결 된 캐시 요약 보고서

현재 요약 보고서는 Azure IoT Edge 게이트웨이에 배포 된 Microsoft 연결 된 캐시 인스턴스에 대 한 캐싱 데이터를 고객이 볼 수 있는 유일한 방법입니다. 보고서는 15 초 간격으로 생성 되며 해당 기간의 평균 통계 뿐만 아니라 모듈의 수명에 대 한 집계 된 통계를 포함 합니다. 고객이 관심을 가질 주요 통계는 다음과 같습니다.

* **hitBytes** -캐시에서 직접 가져온 배달 된 바이트의 합계입니다.
* 잘못 된 **바이트-Microsoft** 연결 된 캐시가 CDN에서 다운로드 하 여 캐시를 확인 해야 하는 배달 된 바이트의 합계입니다.
* **eggressBytes** -HitBytes 및 오 sbytes의 합계 이며, 클라이언트에 전달 되는 총 바이트 수입니다.
* **hitRatioBytes** -hitBytes의 비율입니다.  EggressBytes에서 제공 하는 100% 의%가 hitBytes와 같은 경우 예를 들면 1입니다.

요약 보고서는에서 사용할 수 있습니다 `http://<FQDN/IP of Azure IoT Edge Gateway hosting MCC>:5001/summary` .이 보고서의 표시 유형에 대 한 자세한 내용은 아래의 환경 변수 정보를 참조 하십시오.
