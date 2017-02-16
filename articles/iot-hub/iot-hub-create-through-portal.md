---
title: "Azure Portal을 사용하여 IoT Hub 만들기 | Microsoft 문서"
description: "Azure Portal을 통해 Azure IoT Hub를 만들고 관리하고 삭제하는 방법입니다. 가격 책정 계층, 보안, 배율 및 메시징 구성에 대한 정보가 포함됩니다."
services: iot-hub
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 0909cd2b-4c1e-49e0-b68a-75532caf0a6a
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/05/2017
ms.author: dobett
translationtype: Human Translation
ms.sourcegitcommit: d4eb942db51af9c8136e9e0f5f8683cc15679d08
ms.openlocfilehash: 5a0cd9ac88f30bb5f1ccba43260b86392dba6d7b


---
# <a name="create-an-iot-hub-using-the-azure-portal"></a>Azure Portal을 사용하여 IoT Hub 만들기
[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## <a name="introduction"></a>소개
이 문서에서는 Azure Portal에서 IoT Hub 서비스를 찾고 IoT Hub를 만들고 관리하는 방법에 대해 설명합니다.

## <a name="where-to-find-iot-hubs"></a>IoT Hub를 찾을 위치
IoT Hub를 찾을 수 있는 다양한 위치가 있습니다.

1. **+ 새로 만들기**: **Azure IoT Hub**는 IoT 서비스이며 다른 서비스와 마찬가지로 **+ 새로 만들기** 아래의 **사물 인터넷** 범주에서 찾을 수 있습니다.
2. Marketplace를 통해 **사물 인터넷**아래 영웅 서비스로 IoT Hub에 액세스할 수도 있습니다.

## <a name="create-an-iot-hub"></a>IoT Hub 만들기
다음 메서드를 통해 IoT Hub를 만들 수 있습니다.

* **+ 새로 만들기** 옵션을 통해 IoT hub를 만들면 다음 스크린샷에 표시된 블레이드로 연결됩니다. 이 메서드는 마켓플레이스를 통해 IoT Hub를 만드는 단계와 동일합니다.
* Marketplace를 통해 IoT hub 만들기: **만들기**를 클릭하면 **+ 새로 만들기** 환경에 대한 이전 블레이드와 동일한 블레이드가 열립니다. 다음 섹션에서는 IoT Hub를 만드는 방법에 포함되는 여러 단계를 나열합니다.

### <a name="choose-the-name-of-the-iot-hub"></a>IoT Hub 이름 선택
IoT Hub를 만들려면 IoT Hub의 이름을 지정해야 합니다. 이 이름은 IoT Hubs에서 고유해야 합니다. 솔루션 백 엔드에서 허브의 중복이 허용되지 않으므로 허브의 이름을 가능한 고유하게 지정하는 것이 좋습니다.

### <a name="choose-the-pricing-tier"></a>가격 책정 계층 선택
**무료**, **표준 1**, **표준 2**, **표준 S3**라는 네 개의 계층 중에서 선택할 수 있습니다. 무료 계층에서는 IoT Hub에 500개 장치만 연결할 수 있으며 하루에 8,000개 메시지까지 허용합니다.

**표준 S1**: IoT Hubs S1 버전은 장치당 상대적으로 적은 양의 데이터를 생성하는 장치 수가 많은 IoT 솔루션을 위해 설계되었습니다. S1 버전의 각 단위는 연결된 모든 장치에서 하루에 최대 400,000개의 메시지를 전송할 수 있습니다.

**표준 S2**: IoT Hub S2 버전은 장치가 대량의 데이터를 생성하는 IoT 솔루션을 위해 설계되었습니다. S2 버전의 각 단위를 통해 연결된 모든 장치 간에 하루에 최대 600만 개의 메시지를 전송할 수 있습니다.

**표준 S3**: IoT Hub S3 버전은 대량의 데이터를 생성하는 IoT 솔루션용으로 설계되었습니다. S3 버전의 각 단위를 통해 연결된 모든 장치 간에 하루에 최대 300만 개의 메시지를 전송할 수 있습니다.

![][4]

> [!NOTE]
> IoT Hub는 Azure 구독당 하나의 무료 허브만 허용합니다.
> 
> 

### <a name="iot-hub-units"></a>IoT Hub 단위
하루 단위당 허용되는 메시지의 수는 허브의 가격 책정 계층에 따라 다릅니다. 예를 들어 IoT Hub가 700,000개의 메시지 수신을 지원하려면 S1 계층 단위 2개를 선택합니다.

### <a name="device-to-cloud-partitions-and-resource-group"></a>클라우드 파티션 및 리소스 그룹에 대한 장치
IoT Hub에 대한 파티션 수를 변경할 수 있습니다. 기본 파티션은 4로 설정되지만 드롭다운 목록에서 다른 파티션 수를 선택할 수 있습니다.

리소스 그룹의 경우 빈 리소스 그룹을 명시적으로 만들지 않아도 됩니다. 리소스를 만들 때 새 리소스 그룹을 만들거나 기존 리소스 그룹을 사용하도록 선택할 수 있습니다.

![][5]

### <a name="choose-subscriptions"></a>구독 선택
Azure IoT Hub에 사용자 계정을 연결할 Azure 구독 목록이 자동으로 표시됩니다. 해당 Azure 구독에 IoT Hub를 연결할 옵션 중 하나를 선택할 수 있습니다.

### <a name="choose-the-location"></a>위치 선택
위치 옵션은 IoT Hub가 제공되는 지역 목록을 제공합니다. IoT Hub는 오스트레일리아 동부, 오스트레일리아 남동부, 동아시아, 동남아시아, 북유럽, 서유럽, 일본 동부, 일본 서부, 미국 동부, 미국 서부에 배포할 수 있습니다.

### <a name="create-the-iot-hub"></a>IoT Hub 만들기
이전 단계를 모두 완료하면 IoT Hub를 만들 준비가 된 것입니다. **만들기** 를 클릭하면 특정 옵션과 함께 이 IoT Hub를 만드는 백 엔드 프로세스가 시작되고 지정된 위치에 배포됩니다.

해당 위치 서버에서 백 엔드 배포가 이루어지는 데 시간이 소요되므로 IoT Hub를 만드는 데 몇 분이 소요될 수 있습니다.

## <a name="change-the-settings-of-the-iot-hub"></a>IoT Hub의 설정 변경
IoT Hub 블레이드에서 IoT Hub를 만든 후에 기존 IoT Hub의 설정을 변경할 수 있습니다.

![][8]

**공유 액세스 정책**: IoT Hub에 연결할 장치 및 서비스에 대한 권한을 정의합니다. **일반**에서 **공유 액세스 정책**을 클릭하여 이러한 정책에 액세스할 수 있습니다. 이 블레이드에서 기존 정책을 수정하거나 새 정책을 추가할 수 있습니다.

### <a name="create-a-policy"></a>정책 만들기
* **추가**를 클릭하여 블레이드를 엽니다. 여기에서 다음 그림에 표시된 것처럼 새 정책 이름과 이 정책과 연결할 권한을 입력할 수 있습니다.
  
    이러한 공유 정책과 연결할 수 있는 몇 개의 권한이 있습니다. 처음 두 정책인 **레지스트리 읽기** 및 **레지스트리 쓰기**는 장치 ID 저장소 또는 ID 레지스트리에 대한 읽기 및 쓰기 액세스 권한을 부여합니다. 쓰기 옵션을 선택하면 읽기 옵션도 자동으로 선택됩니다.
  
     **서비스 연결** 정책은 IoT Hub에 연결된 서비스에 대해 소비자 그룹 등의 클라우드 쪽 끝점에 대한 액세스 권한을 부여합니다. **장치 연결** 정책은 IoT Hub의 장치 쪽 끝점에서 메시지를 주고받기 위한 권한을 부여합니다.
* **만들기** 를 클릭하여 새로 만들어진 이 정책을 기존 목록에 추가합니다.

![][10]

## <a name="endpoints"></a>끝점
**끝점**을 클릭하여 수정할 IoT Hub에 대한 끝점 목록을 표시합니다. 끝점의 두 가지 주요 유형은 IoT Hub에 기본 제공된 끝점 및 끝점을 만든 후 IoT Hub에 추가한 끝점입니다.

### <a name="built-in-endpoints"></a>기본 제공 끝점
두 가지 주요 기본 제공 끝점인 **클라우드-장치 피드백** 및 **이벤트**가 있습니다.

* **클라우드-장치 피드백** 설정: 이 설정에는 메시지에 대한 **클라우드-장치 TTL**(time-to-live) 및 **보존 시간**의 두 가지 하위 설정이 포함됩니다. IoT Hub를 처음 만들면 이러한 두 설정이&1;시간이라는 기본값으로 생성됩니다. 이러한 값을 조정하려면 슬라이더를 사용하거나 값을 입력합니다.
* **이벤트** 설정: 이 설정에는 여러 하위 설정이 포함되며 이 중 일부는 IoT Hub가 만들어질 때 명명/할당되며 사용자 지정 가능한 기타 하위 설정으로 복사할 수만 있습니다. 이들 설정은 다음 섹션에 나열됩니다.

**파티션**: IoT Hub가 만들어질 때 이 값이 설정되고 이 설정을 통해 변경할 수 있습니다.

**이벤트 허브 호환 이름 및 끝점**: IoT Hub가 만들어질 때 특정 상황에서 사용자가 액세스해야 하는 이벤트 허브가 내부적으로 만들어집니다. 이 이벤트 허브 호환 이름 및 끝점은 사용자 지정할 수 없지만 **복사** 단추를 통해 사용할 수 있습니다.

**보존 시간**: 기본적으로&1;일로 설정되지만 드롭다운 목록을 사용하여 다른 값으로 사용자 지정할 수 있습니다. 이 값은 클라우드-장치에 대한 유사한 설정처럼 시간 단위가 아니라 장치-클라우드에 대한 일 단위입니다.

**소비자 그룹**: 소비자 그룹은 기타 응용 프로그램 또는 서비스를 IoT Hub에 연결하기 위해 데이터를 특정 방식으로 끌어오는 데 사용할 수 있는 다른 메시징 시스템과 유사한 설정입니다. 모든 IoT Hub가 기본 소비자 그룹으로 만들어집니다. 그러나 소비자 그룹을 IoT Hub에 추가 또는 삭제할 수 있습니다.

> [!NOTE]
> 기본 소비자 그룹을 편집하거나 삭제할 수 없습니다.
> 
> 

![][11]

### <a name="custom-endpoints"></a>사용자 지정 끝점
포털을 통해 IoT Hub에 사용자 지정 끝점을 추가할 수 있습니다. 끝점 블레이드에서 블레이드 맨 위의 **추가**를 클릭하여 **끝점 추가** 블레이드를 엽니다. 블레이드에 필요한 정보를 입력한 다음 **확인**을 클릭합니다. 사용자 지정 끝점이 기본 끝점 블레이드에 표시됩니다.

![][13]

[참조 - IoT Hub 끝점][lnk-devguide-endpoints]에서 사용자 지정 끝점에 대해 자세히 알아볼 수 있습니다.

## <a name="routes"></a>경로
**경로**를 클릭하여 IoT Hub가 장치를 클라우드 메시지에 디스패치하는 방법을 관리할 수 있습니다.

![][14]

블레이드 맨 위의 **추가**를 클릭하고 블레이드에 필요한 정보를 입력하고 **확인**을 클릭하여 IoT Hub에 추가 경로를 추가할 수 있습니다. 경로가 기본 끝점 블레이드에 표시됩니다. 경로 목록에서 클릭한 후 수정하여 경로를 편집할 수 있습니다. 경로를 사용하려면 경로 목록에서 클릭하고 활성화/비활성화 토글을 **사용 안함**으로 설정합니다. 블레이드의 맨 아래에서 **확인**을 클릭하여 변경 내용을 저장합니다.

![][15]

## <a name="pricing-and-scale"></a>가격 및 크기 조정
기존 IoT Hub의 가격 책정을 **가격 책정** 설정을 통해 변경할 수 있으며 단, 다음과 같은 예외가 적용됩니다.

* 현재 구현에서는, 무료 SKU를 포함하는 IoT Hub는 계층을 유료 SKU 중 하나로 변경할 수 없으며 또는 그 반대도 불가능합니다.
* Azure 구독에는 하나의 무료 계층 IoT Hub만 포함될 수 있습니다.

![][12]

상위 계층(S2 또는 S3)에서 하위 계층(S1 또는 S2)으로 이동하는 것은 해당 날짜에 전송된 메시지 수가 충돌하지 않는 경우에만 허용됩니다. 예를 들어 하루당 메시지 수가 400,000개를 초과하면 IoT Hub에 대한 계층을 변경할 수 있습니다. 그러나 S1 계층으로 변경하면 해당 일에 IoT Hub가 제한됩니다.

## <a name="delete-the-iot-hub"></a>IoT Hub 삭제
**찾아보기**를 클릭한 후 삭제할 해당 허브를 선택하여 삭제할 IoT Hub를 찾아볼 수 있습니다. IoT Hub 이름 아래의 **삭제** 단추를 클릭하여 IoT Hub를 삭제합니다.

## <a name="next-steps"></a>다음 단계
Azure IoT Hub를 관리하는 방법에 대한 자세한 내용을 알아보려면 다음 링크를 따라가세요.

* [IoT 장치 대량 관리][lnk-bulk]
* [IoT Hub 메트릭][lnk-metrics]
* [작업 모니터링][lnk-monitor]

IoT Hub의 기능을 추가로 탐색하려면 다음을 참조하세요.

* [IoT Hub 개발자 가이드][lnk-devguide]
* [IoT Gateway SDK를 사용하는 장치 시뮬레이션][lnk-gateway]
* [처음부터 IoT 솔루션 보안 유지][lnk-securing]

[4]: ./media/iot-hub-create-through-portal/create-iothub.png
[5]: ./media/iot-hub-create-through-portal/location1.png
[8]: ./media/iot-hub-create-through-portal/portal-settings.png
[10]: ./media/iot-hub-create-through-portal/shared-access-policies.png
[11]: ./media/iot-hub-create-through-portal/messaging-settings.png
[12]: ./media/iot-hub-create-through-portal/pricing-error.png
[13]: ./media/iot-hub-create-through-portal/endpoint-creation.png
[14]: ./media/iot-hub-create-through-portal/routes-list.png
[15]: ./media/iot-hub-create-through-portal/route-edit.png

[lnk-bulk]: iot-hub-bulk-identity-mgmt.md
[lnk-metrics]: iot-hub-metrics.md
[lnk-monitor]: iot-hub-operations-monitoring.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
[lnk-securing]: iot-hub-security-ground-up.md
[lnk-devguide-endpoints]: iot-hub-devguide-endpoints.md



<!--HONumber=Jan17_HO1-->


