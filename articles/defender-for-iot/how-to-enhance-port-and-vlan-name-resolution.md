---
title: 포트 및 VLAN 이름 확인 향상
description: 센서에서 포트 및 VLAN 이름을 사용자 지정 하 여 장치 해상도를 보강 합니다.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/13/2020
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 1747af77468ebe41f59892dea4bb49ce24757f37
ms.sourcegitcommit: 8be279f92d5c07a37adfe766dc40648c673d8aa8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/31/2020
ms.locfileid: "97840238"
---
# <a name="enhance-port-and-vlan-name-resolution"></a>포트 및 VLAN 이름 확인 향상

센서에서 포트 및 VLAN 이름을 사용자 지정 하 여 장치 해상도를 보강할 수 있습니다.

## <a name="customize-port-names"></a>포트 이름 사용자 지정

IoT 용 Azure Defender는 DHCP 또는 HTTP와 같은 가장 일반적으로 예약 된 포트에 이름을 자동으로 할당 합니다. IoT 용 Defender에서 검색 하는 다른 포트에 대 한 포트 이름을 사용자 지정할 수 있습니다. 예를 들어 해당 포트에 비정상적으로 높은 활동이 표시 되므로 예약 되지 않은 포트에 이름을 할당 합니다.

이러한 이름은 다음과 같은 경우에 나타납니다.

  - 장치 맵에서 **장치 그룹** 을 선택 합니다.

  - 포트 정보를 제공 하는 위젯을 만듭니다.

### <a name="view-custom-port-names-in-the-device-map"></a>장치 맵에서 사용자 지정 포트 이름 보기

사용자가 정의한 이름이 포함 된 포트는 장치 맵에 **알려진 응용 프로그램** 그룹에 나타납니다.

:::image type="content" source="media/how-to-enrich-asset-information/applications-v2.png" alt-text="알려진 응용 프로그램 그룹을 표시 하는 장치 맵의 스크린샷":::

### <a name="view-custom-port-names-in-widgets"></a>위젯에 대 한 사용자 지정 포트 이름 보기

정의한 포트 이름은 포트 별로 트래픽을 포함 하는 위젯에 표시 됩니다.

:::image type="content" source="media/how-to-enrich-asset-information/traffic-v2.png" alt-text="트래픽이 포함 됩니다.":::

사용자 지정 포트 이름을 정의 하려면:

1. **시스템 설정** 을 선택한 다음 **표준 별칭** 을 선택 합니다.

2. **포트 별칭 추가** 를 선택 합니다.

    :::image type="content" source="media/how-to-enrich-asset-information/edit-aliases.png" alt-text="포트 별칭을 추가 합니다.":::

3. 포트 번호를 입력 하 고, **TCP/UDP** 를 선택 하거나, **둘 다** 선택 하 고 이름을 추가 합니다.

4. **저장** 을 선택합니다.

## <a name="configure-vlan-names"></a>VLAN 이름 구성

장치 VLAN 번호 및 태그를 사용 하 여 장치 인벤토리 데이터를 보강할 수 있습니다. 데이터 보강 외에 VLAN 당 장치 수를 확인 하 고 VLAN 위젯 별 대역폭을 볼 수 있습니다.

Vlan 지원은 802.1 q (최대 VLAN ID 4094)를 기반으로 합니다.

VLAN 정보를 검색 하는 데 사용할 수 있는 두 가지 방법은 다음과 같습니다.

- **자동으로 검색** 됨: 기본적으로 센서가 vlan을 자동으로 검색 합니다. 트래픽을 통해 검색 된 Vlan은 VLAN 구성 화면, 데이터 마이닝 보고서 및 VLAN 정보를 포함 하는 다른 보고서에 표시 됩니다. 사용 하지 않은 Vlan은 표시 되지 않습니다. 이러한 Vlan은 편집 하거나 삭제할 수 없습니다. 

  각 VLAN에 고유한 이름을 추가 해야 합니다. 이름을 추가 하지 않으면 vlan 번호가 VLAN이 보고 되는 모든 위치에 표시 됩니다.

- **수동으로 추가**: vlan을 수동으로 추가할 수 있습니다. 수동으로 추가한 각 VLAN에 대해 고유한 이름을 추가 해야 하며, 이러한 Vlan을 편집 하거나 삭제할 수 있습니다.

VLAN 이름은 최대 50 자의 ASCII 문자를 포함할 수 있습니다.

> [!NOTE]
> VLAN 이름은 센서와 관리 콘솔 간에 동기화 되지 않습니다. 관리 콘솔 에서도 이름을 정의 해야 합니다.  
Cisco 스위치의 경우 범위 구성에 다음 줄을 추가 합니다. `monitor session 1 destination interface XX/XX encapsulation dot1q` . 이 명령에서 *xx/xx* 는 포트의 이름 및 번호입니다.

Vlan을 구성 하려면:

1. 측면 메뉴에서 **시스템 설정** 을 선택 합니다.

2. **시스템 설정** 창에서 **VLAN** 을 선택 합니다.

    :::image type="content" source="media/how-to-enrich-asset-information/edit-vlan.png" alt-text="시스템 설정을 사용 하 여 Vlan을 편집 합니다.":::

3. 각 VLAN ID 옆에 고유한 이름을 추가 합니다.

## <a name="next-steps"></a>다음 단계

장치 인벤토리 및 데이터 마이닝에 대 한 보고서에서 보강 장치 정보 보기:

- [장치 인벤토리에서 센서 검색 조사](how-to-investigate-sensor-detections-in-a-device-inventory.md)
- [보고서 생성](how-to-generate-reports.md)
