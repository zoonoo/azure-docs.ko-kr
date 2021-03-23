---
title: 특정 영역의 장치에 대해 알아보기
description: 온-프레미스 관리 콘솔을 사용 하 여 특정 영역별 포괄적인 보기 정보 가져오기
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 03/21/2021
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 685d7b1df4389c356ee7b531d179919025d298d0
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104776339"
---
# <a name="view-information-per-zone"></a>지역별 정보 보기


## <a name="view-a-device-map-for-a-zone"></a>영역에 대 한 장치 맵 보기

센서에서 선택한 영역에 대 한 장치 맵을 봅니다. 이 보기에는 선택한 영역과 관련 된 모든 네트워크 요소 (센서, 연결 된 장치 및 기타 정보 포함)가 표시 됩니다.

:::image type="content" source="media/how-to-work-with-asset-inventory-information/zone-map-screenshot.png" alt-text="영역 지도의 스크린샷":::


- **사이트 관리** 창의 영역 이름을 포함 하는 표시줄에서 **영역 맵 보기** 를 선택 합니다.

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/default-region-to-default-business-unit-v2.png" alt-text="기본 사업부의 기본 영역입니다.":::

**장치 맵** 창이 표시 됩니다. 지도에서 장치 및 장치 정보를 보는 데 사용할 수 있는 도구는 다음과 같습니다. 이러한 각 기능에 대 한 자세한 내용은 *IoT platform 사용자 가이드* 를 참조 하세요.

- **지도 확대/축소 보기**: 간소화 된 보기, 연결 보기 및 자세히 보기입니다. 표시 된 지도 보기는 지도의 확대/축소 수준에 따라 달라 집니다. 확대/축소 수준을 조정 하 여 지도 보기 간을 전환 합니다.

  :::image type="icon" source="media/how-to-work-with-asset-inventory-information/zoom-icon.png" border="false":::

- **지도 검색 및 레이아웃 도구**: 다양 한 네트워크 세그먼트, 장치, 장치 그룹 또는 레이어를 표시 하는 데 사용 되는 도구입니다.

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/search-and-layout-tools.png" alt-text="검색 및 레이아웃 도구 뷰의 스크린샷":::

- **장치에 대 한 레이블 및 표시기:** 예를 들어 IT 네트워크의 서브넷에 그룹화 된 장치의 수가 있습니다. 이 예제에서는 8입니다.

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/labels-and-indicators.png" alt-text="레이블 및 표시기의 스크린샷":::

- **장치 속성 보기**: 예를 들어 장치를 모니터링 하는 센서 및 기본 장치 속성입니다. 장치를 마우스 오른쪽 단추로 클릭 하 여 장치 속성을 봅니다.

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/asset-properties-v2.png" alt-text="장치 속성 보기의 스크린샷":::

- **장치와 연결 된 경고:** 장치를 마우스 오른쪽 단추로 클릭 하 여 관련 경고를 확인 합니다.

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/show-alerts.png" alt-text="경고 표시 보기의 스크린샷":::

## <a name="view-alerts-associated-with-a-zone"></a>영역과 연결 된 경고 보기

특정 영역과 연결 된 경고를 보려면 다음을 수행 합니다.

- **영역** 창에서 경고 아이콘을 선택 합니다. 

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/business-unit-view-v2.png" alt-text="예제가 포함 된 기본 사업부 뷰입니다.":::

자세한 내용은 [개요: 경고 사용](how-to-work-with-alerts-on-premises-management-console.md)을 참조 하세요.

### <a name="view-the-device-inventory-of-a-zone"></a>영역에 대 한 장치 인벤토리 보기

특정 영역과 연결 된 장치 인벤토리를 보려면 다음을 수행 합니다.

- **영역** 창에서 **장치 인벤토리 보기** 를 선택 합니다.

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/default-business-unit.png" alt-text="장치 인벤토리 화면이 표시 됩니다.":::

자세한 내용은 [장치 인벤토리에서 모든 엔터프라이즈 센서 검색 조사](how-to-investigate-all-enterprise-sensor-detections-in-a-device-inventory.md)를 참조 하세요.

## <a name="view-additional-zone-information"></a>추가 영역 정보 보기

다음과 같은 추가 영역 정보를 사용할 수 있습니다.

- **영역 세부 정보**: 영역에 연결 된 장치, 경고 및 센서의 수를 확인 합니다.

- **센서 세부 정보**: 영역에 할당 된 각 센서의 이름, IP 주소 및 버전을 표시 합니다.

- **연결 상태**: 센서의 연결이 끊어지면 센서에서 연결 합니다. [온-프레미스 관리 콘솔에 센서 연결을](how-to-activate-and-set-up-your-on-premises-management-console.md#connect-sensors-to-the-on-premises-management-console)참조 하세요. 

- **업데이트 진행률**: 연결 된 센서를 업그레이드 하는 경우 업그레이드 상태가 표시 됩니다. 업그레이드 하는 동안 온-프레미스 관리 콘솔은 센서에서 장치 정보를 받지 않습니다.

## <a name="next-steps"></a>다음 단계

[전역, 지역 및 로컬 위협에 대한 인사이트 확보](how-to-gain-insight-into-global-regional-and-local-threats.md)
