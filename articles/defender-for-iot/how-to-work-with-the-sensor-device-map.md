---
title: 센서 디바이스 맵 작업
description: 장치 맵은 검색 된 네트워크 장치를 그래픽으로 표시 합니다. 지도를 사용 하 여 장치 정보, 네트워크 조각을 분석 하 고 관리 하며 보고서를 생성 합니다.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 1/7/2021
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: fd1721060bdc4b18f324a94f7c367bacde6ed4e8
ms.sourcegitcommit: 8f0803d3336d8c47654e119f1edd747180fe67aa
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/07/2021
ms.locfileid: "97976761"
---
# <a name="investigate-sensor-detections-in-the-device-map"></a>장치 맵의 센서 검색 조사

장치 맵은 검색 된 네트워크 장치를 그래픽으로 표시 합니다. 맵을 사용 하 여 다음을 수행 합니다.

  - 장치 정보를 검색, 분석 및 관리 합니다.

  - 네트워크 조각 (예: 관심 있는 특정 그룹 또는 Purdue 계층)을 분석 합니다.

  - 보고서를 생성 합니다 (예: 장치 세부 정보 및 요약 내보내기).

:::image type="content" source="media/how-to-work-with-maps/device-map-v2.png" alt-text="장치 맵의 스크린샷.":::

맵에 액세스 하려면:

  - 콘솔의 주 화면에서 **장치 맵** 을 선택 합니다.

## <a name="map-search-and-layout-tools"></a>지도 검색 및 레이아웃 도구

지도에서 작업 하는 데 사용 되는 도구는 다음과 같습니다.

사용자 역할은 장치 맵 창에서 사용할 수 있는 도구를 결정 합니다. 사용자 역할에 대 한 자세한 내용은 [사용자 만들기 및 관리](how-to-create-and-manage-users.md) 를 참조 하세요.

| Symbol | 설명 |
|---|---|
| :::image type="icon" source="media/how-to-work-with-maps/search-bar-icon-v2.png" border="false":::| 특정 장치에 대 한 IP 주소 또는 MAC 주소를 검색 합니다. 텍스트 상자에 IP 주소 또는 MAC 주소를 입력 합니다. 지도에는 연결 된 장치로 검색 한 장치가 표시 됩니다. |
| 그룹 강조 표시 및 필터 <br /> :::image type="content" source="media/how-to-work-with-maps/group-highlight-and-filters-v2.png" alt-text="그룹이 강조 표시 되 고 필터가 강조 표시 됩니다."::: | 기본 및 사용자 지정 장치 그룹을 기준으로 맵을 필터링 하거나 강조 표시 합니다. |
| :::image type="icon" source="media/how-to-work-with-maps/collapse-view-icon.png" border="false"::: | 보기를 축소 하 고, OT 장치에서 포커스가 있는 뷰를 사용 하도록 설정 하 고, IT 장치를 그룹화 합니다.  |
| :::image type="icon" source="media/how-to-work-with-maps/device-management-icon.png" border="false"::: | 지도에서 현재 장치 정렬을 유지 합니다. 예를 들어 지도의 새 위치로 장치를 끌어 오면, 지도를 종료할 때 장치가 이러한 위치에 그대로 유지 됩니다. |
| :::image type="icon" source="media/how-to-work-with-maps/fit-to-screen-icon.png" border="false"::: | 화면에 맞춤 |
| :::image type="icon" source="media/how-to-work-with-maps/layer-icon.png" border="false"::: :::image type="icon" source="media/how-to-work-with-maps/layouts-icon-v2.png" border="false"::: | -자동, 프로세스 제어, 감독 기관과 이러한 및 엔터프라이즈를 포함 하 여이 장치에 대해 식별 된 Purdue 계층을 봅니다. <br /> -장치 간의 연결을 확인 합니다.|
| :::image type="icon" source="media/how-to-work-with-maps/broadcast-icon.png" border="false"::: | 브로드캐스트와 멀티 캐스트 사이에 표시 하거나 숨깁니다. |
| :::image type="icon" source="media/how-to-work-with-maps/time-icon.png" border="false"::: | 다른 장치와 마지막으로 통신 하는 시간에 따라 맵의 장치를 필터링 합니다. |
| :::image type="icon" source="media/how-to-work-with-maps/notifications-icon.png" alt-text="알림을" border="false"::: | 장치에 대 한 알림을 봅니다. 예를 들어 기존 MAC 주소를 사용 하 여 장치에 대 한 새 IP가 검색 된 경우 |
| :::image type="icon" source="media/how-to-work-with-maps/export-import.png" alt-text="내보내기" border="false"::: | 장치 정보를 내보내거나 가져옵니다. |
| :::image type="icon" source="media/how-to-work-with-maps/properties-icon.png" alt-text="properties" border="false"::: | 선택한 장치에 대 한 기본 장치 속성을 봅니다. |
| :::image type="icon" source="media/how-to-work-with-maps/zoom-in-icon-v2.png" alt-text="확대" border="false"::: 또는 :::image type="icon" source="media/how-to-work-with-maps/zoom-out-icon-v2.png" alt-text="축소" border="false"::: | 맵에서 장치를 확대 하거나 축소 합니다. |

## <a name="view-ot-elements-only"></a>OT 요소만 표시

기본적으로이 장치는 서브넷에 의해 자동으로 집계 되므로 지도 보기가 OT 및 ICS 네트워크에 집중 됩니다. IT 네트워크 요소의 프레젠테이션은 최소한으로 축소 되어 맵에 표시 되는 장치의 총 수를 줄이고 OT 및 ICS 네트워크 요소를 명확 하 게 파악할 수 있도록 합니다.

각 서브넷은 IT 서브넷의 세부 정보를 볼 수 있는 대화형 축소 및 확장 기능을 포함 하 여 장치 맵에 단일 엔터티로 표시 됩니다.

아래 그림은 27 개의 IT 네트워크 요소를 포함 하는 축소 된 IT 서브넷을 보여 줍니다.

:::image type="content" source="media/how-to-work-with-maps/shrunk-it-subnet-v2.png" alt-text="27 IT 네트워크 요소를 포함 하는 축소 된 IT 서브넷":::

IT 네트워크 축소 기능을 사용 하려면 다음을 수행 합니다.

- 시스템 설정 창에서 IT 네트워크 기능을 사용 하도록 설정 했는지 확인 합니다.

:::image type="content" source="media/how-to-work-with-maps/shrunk-it-subnet-v2.png" alt-text="시스템 설정 창":::

IT 서브넷을 확장 하려면:

1. 네트워크와 네트워크를 구분 하려면 시스템 설정 화면에서 **서브넷** 을 선택 합니다.

   > [!NOTE]
   > 사용자가 의미 있는 이름을 사용 하 여 각 서브넷의 이름을 쉽게 식별 하 여 네트워크와 네트워크를 구분할 수 있도록 하는 것이 좋습니다.

   :::image type="content" source="media/how-to-work-with-maps/subnet-list.png" alt-text="서브넷 구성":::

2. 서브넷 구성 편집 창에서 IT 서브넷으로 정의 하려는 각 서브넷에 대 한 ICS 서브넷을 선택 취소 합니다. It 서브넷은 IT 네트워크에서 컨트롤러 또는 PLC 같은 ICS 장치에 대 한 알림과 함께 장치 맵에 축소 된 상태로 표시 됩니다.

   :::image type="content" source="media/how-to-work-with-maps/edit-config.png" alt-text="서브넷 구성 편집":::

3. 지도에서 IT 네트워크를 확장 하려면 장치 창에서 해당 네트워크를 마우스 오른쪽 단추로 클릭 하 고 **네트워크 확장** 을 선택 합니다.

   :::image type="content" source="media/how-to-work-with-maps/expand-network.png" alt-text="네트워크 보기를 확장 합니다.":::

4. 레이아웃 변경을 다시 실행할 수 없음을 알리는 확인 상자가 표시 됩니다.

5. **확인** 을 선택합니다. 지도에 IT 서브넷 요소가 표시 됩니다.

   :::image type="content" source="media/how-to-work-with-maps/fixed-map.png" alt-text="확인":::

IT 서브넷을 축소 하려면:

1.  왼쪽 창에서 **장치** 를 선택 합니다.

2. 장치 창에서 축소 아이콘을 선택 합니다. 빨간색으로 표시 되는 숫자는 현재 맵에 표시 되는 확장 된 IT 서브넷 수를 나타냅니다.

   :::image type="content" source="media/how-to-work-with-maps/devices-notifications.png" alt-text="디바이스 창":::

3. 축소 하려는 서브넷을 선택 하거나 **모두 축소** 를 선택 합니다. 선택한 서브넷은 지도에 축소 된 상태로 나타납니다.

   :::image type="content" source="media/how-to-work-with-maps/close-all-subnets.png" alt-text="모두 축소":::

축소 아이콘은 확장 된 IT 서브넷의 업데이트 된 수로 업데이트 됩니다.

## <a name="view-or-highlight-device-groups"></a>장치 그룹 보기 또는 강조 표시

장치 그룹을 기반으로 맵 표시를 사용자 지정할 수 있습니다. 예를 들어 특정 VLAN 또는 서브넷과 연결 된 장치 그룹이 있습니다. 미리 정의 된 그룹을 사용할 수 있으며 사용자 지정 그룹을 만들 수 있습니다.

그룹 보기 기준:

  - **강조 표시:** 파란색의 특정 그룹에 속한 장치를 강조 표시 합니다.

  - **필터링:** 특정 그룹에 속하는 장치만 지도에 표시 합니다.

:::image type="content" source="media/how-to-work-with-maps/port-standard.png" alt-text="포트의 표준 보기":::

다음과 같은 미리 정의 된 그룹을 사용할 수 있습니다.

| 그룹 이름 | 설명 |
|--|--|
| **알려진 응용 프로그램 또는 비표준 포트 (기본값)** | TCP와 같은 예약 된 포트를 사용 하는 장치 비표준 포트 또는 별칭이 할당 되지 않은 포트를 사용 하는 장치입니다. |
| **OT 프로토콜 (기본값)** | OT 트래픽을 처리 하는 장치입니다. |
| **권한 부여 (기본값)** | 학습 프로세스 중에 네트워크에서 검색 된 장치 또는 공식적으로 네트워크에 추가 된 장치 |
| **장치 인벤토리 필터** | 필터에 따라 그룹화 된 장치는 장치 인벤토리 테이블에 저장 됩니다. |
| **폴링 간격** | 폴링 간격을 기준으로 그룹화 된 장치입니다. 폴링 간격은 순환 채널 또는 기간에 따라 자동으로 생성 됩니다. 예를 들면 15.0 초, 3.0 초, 1.5 초 또는 모든 간격입니다. 이 정보를 검토 하면 시스템이 너무 신속 하 게 폴링을 느리게 폴링하는 지 알 수 있습니다. |
| **프로그래밍** | 엔지니어링 스테이션 및 프로그래밍 컨트롤러 |
| **서브넷** | 특정 서브넷에 속하는 장치입니다. |
| **VLAN** | 특정 VLAN ID와 연결 된 장치입니다. |
| **서브넷 간 연결** | 교차 서브넷 연결과 관련 된 장치입니다. |
| **고정 된 경고** | 사용자가 경고를 고정 한 장치입니다. |
| **공격 벡터 시뮬레이션** | 공격에 취약 한 장치가 공격 벡터 보고서에서 감지 되었습니다. 지도에서 이러한 장치를 보려면 공격 벡터를 생성할 때 **장치 맵에 표시** 확인란을 선택 합니다. :::image type="content" source="media/how-to-work-with-maps/add-attack-v2.png" alt-text="공격 벡터 시뮬레이션 추가"::: |
| **마지막 확인 날짜** | 마지막으로 표시 된 시간 프레임을 기준으로 그룹화 된 장치 (예: 1 시간, 6 시간, 1 일, 7 일) |
| **Active Directory에 없음** | Active Directory와 통신 하지 않는 모든 비 PLC 장치입니다. |

장치를 강조 표시 하거나 필터링 하려면:

1. 측면 메뉴에서 **장치 맵** 을 선택 합니다.

2. 필터 아이콘을 선택 합니다. :::image type="content" source="media/how-to-work-with-maps/menu-icon.png" alt-text="메뉴":::

3. 그룹 창에서 장치를 강조 표시 하거나 필터링 할 그룹을 선택 합니다.

4. **강조 표시** 또는 **필터** 를 선택 합니다.

## <a name="define-custom-groups"></a>사용자 지정 그룹 정의

미리 정의 된 그룹을 보는 것 외에도 사용자 지정 그룹을 정의할 수 있습니다. 장치 맵, 장치 인벤토리 및 데이터 마이닝 보고서에 그룹이 표시 됩니다.

> [!NOTE]
> 장치 인벤토리에서 그룹을 만들 수도 있습니다.

그룹 만들기:

1. 측면 메뉴에서 **장치** 를 선택 합니다. 장치 지도가 표시 됩니다.

2. :::image type="content" source="media/how-to-work-with-maps/menu-icon.png" alt-text="그룹"::: 설정을 선택 하 여 그룹 설정을 표시 합니다.

3. :::image type="content" source="media/how-to-work-with-maps/create-group-v2.png" alt-text="그룹"::: 을 선택 하 여 새 사용자 지정 그룹을 만듭니다.

:::image type="content" source="media/how-to-work-with-maps/custom-group-v2.png" alt-text="사용자 지정 그룹 화면 만들기":::

4. 그룹 이름을 추가 하 고 최대 30 자를 사용 합니다.

5. 다음과 같이 관련 장치를 선택 합니다.

   - 목록에서 선택 하 여이 메뉴에서 장치를 추가 합니다 (화살표 단추에서 선택).<br /> 또는 <br /> 
   - 선택한 그룹에서 장치를 복사 하 여이 메뉴에서 장치를 추가 합니다 (화살표 단추에서 선택).

6. **그룹 추가** 를 선택합니다.

### <a name="add-devices-to-a-custom-group"></a>사용자 지정 그룹에 장치 추가

사용자 지정 그룹에 장치를 추가 하거나 새 사용자 지정 그룹 및 장치를 만들 수 있습니다.

1. 지도에서 장치를 마우스 오른쪽 단추로 클릭 합니다.

2. **그룹에 추가를** 선택 합니다.

3. 그룹 필드에 그룹 이름을 입력 하 고 +를 선택 합니다. 새 그룹이 나타납니다. 그룹이 이미 있는 경우 기존 사용자 지정 그룹에 추가 됩니다.

   :::image type="content" source="media/how-to-work-with-maps/groups-section-v2.png" alt-text="그룹 이름":::

4. 1-3 단계를 반복 하 여 그룹에 장치를 추가 합니다.

## <a name="map-zoom-views"></a>지도 확대/축소 뷰

지도 보기를 사용 하면 많은 네트워크를 분석할 때 법적 정보를 신속 하 게 분석할 수 있습니다.

다음 세 가지 장치 세부 정보 보기를 표시할 수 있습니다.

  - [Bird 's 눈동자 보기](#birds-eye-view)

  - [장치 유형 및 연결 보기](#device-type-and-connection-view)

  - [상세 보기](#detailed-view)

### <a name="birds-eye-view"></a>Bird 's 눈동자 보기

이 보기는 다음과 같이 표시 되는 장치를 한눈에 볼 때 제공 합니다.

  - 경고가 있는 장치를 나타내는 빨간색 점

  - 별표 표시 점이 중요 한 것으로 표시 된 장치를 나타냄

  - 검은색 점은 경고가 없는 장치를 나타냄

:::image type="content" source="media/how-to-work-with-maps/colored-dots-v2.png" alt-text="눈에 보기":::

### <a name="device-type-and-connection-view"></a>장치 유형 및 연결 보기 

이 보기는 경고, 장치 유형 및 연결 된 장치를 사용 하 여 장치를 강조 표시 하기 위해 맵에 아이콘으로 표시 된 장치를 표시 합니다.

  - 경고가 있는 장치에 빨간색 링이 표시 됩니다.

  - 경고가 없는 장치는 회색 링으로 표시 됩니다.

  - Star로 표시 되는 장치는 중요 한 것으로 표시 되었습니다.

장치 유형 아이콘은 연결 된 장치로 표시 됩니다.

:::image type="content" source="media/how-to-work-with-maps/colored-rings.png" alt-text="연결 보기":::

### <a name="detailed-view"></a>상세 보기 

자세히 보기에는 다음 정보를 포함 하는 장치 및 장치 레이블과 표시기가 표시 됩니다.

:::image type="content" source="media/how-to-work-with-maps/device-map-v2.png" alt-text="상세 보기":::

### <a name="control-the-zoom-view"></a>확대/축소 뷰 제어

지도 보기는 지도 확대/축소 수준에 따라 달라 집니다. 지도 보기 간의 전환은 확대/축소 수준을 변경 하 여 수행 됩니다.

:::image type="content" source="media/how-to-work-with-maps/zoom-in-out.png" alt-text="확대/축소 뷰 제어":::

### <a name="enable-simplified-zoom-views"></a>단순화 된 확대/축소 보기 사용

보안 분석가와 RO 사용자에 게 Bird 및 장치 및 유형 연결 보기에 대 한 액세스 권한을 원하는 관리자는 단순화 된 보기 옵션을 사용 하도록 설정 해야 합니다.

단순화 된 지도 보기를 사용 하려면 다음을 수행 합니다.

  - **시스템 설정** 을 선택 하 고 **단순화 된 지도 보기** 옵션을 설정/해제 합니다.

:::image type="content" source="media/how-to-work-with-maps/simplify-view-v2.png" alt-text="지도 보기 간소화":::

## <a name="learn-more-about-devices"></a>장치에 대 한 자세한 정보

장치 맵을 구성 하는 장치에 대 한 자세한 내용은 다양 한 도구를 사용할 수 있습니다.

- [장치 레이블 및 표시기](#device-labels-and-indicators)

- [장치 빠른 보기](#device-quick-views)

- [장치 속성 보기 및 관리](#view-and-manage-device-properties)

- [장치 유형 보기](#view-device-types)

- [후면판](#backplane-properties)

- [장치에 대 한 이벤트의 타임 라인 보기](#view-a-timeline-of-events-for-the-device)

- [프로그래밍 세부 정보 및 변경 내용 분석](#analyze-programming-details-and-changes)

### <a name="device-labels-and-indicators"></a>장치 레이블 및 표시기

맵의 장치에 다음 레이블과 표시기가 나타날 수 있습니다.

| 장치 레이블 | 설명 |
|--|--|
| :::image type="content" source="media/how-to-work-with-maps/host-v2.png" alt-text="IP 호스트 이름"::: | IP 주소 호스트 이름 및 IP 주소 또는 서브넷 주소 |
| :::image type="content" source="media/how-to-work-with-maps/amount-alerts-v2.png" alt-text="경고 수"::: | 장치와 연결 된 경고 수 |
| :::image type="icon" source="media/how-to-work-with-maps/type-v2.png" border="false"::: | 장치 유형 아이콘 (예: storage, PLC 또는 historian). |
| :::image type="content" source="media/how-to-work-with-maps/grouped-v2.png" alt-text="그룹화 된 장치"::: | IT 네트워크의 서브넷에 그룹화 된 장치 수입니다. 이 예에서는 8입니다. |
| :::image type="content" source="media/how-to-work-with-maps/not-authorized-v2.png" alt-text="장치 학습 기간"::: | 학습 기간 후에 검색 되었으며 네트워크 장치로 권한이 부여 되지 않은 장치입니다. |
| 실선 | 장치 간 논리적 연결 |
| :::image type="content" source="media/how-to-work-with-maps/new-v2.png" alt-text="새 장치"::: | 학습을 완료 한 후 새 장치를 검색 했습니다. |

### <a name="device-quick-views"></a>장치 빠른 보기

맵에서 장치 속성 및 연결에 액세스 합니다.

빠른 속성 메뉴를 열려면 다음을 수행 합니다.

  - 빠른 속성 메뉴 :::image type="content" source="media/how-to-work-with-maps/properties.png" alt-text="빠른 속성 메뉴":::를 선택 합니다.

#### <a name="quick-device-properties"></a>빠른 장치 속성

빠른 속성 화면이 열려 있는 동안 장치 또는 여러 장치를 선택 하 여 해당 장치의 하이라이트를 확인 합니다.

:::image type="content" source="media/how-to-work-with-maps/device-information.png" alt-text="빠른 장치 속성":::

#### <a name="quick-connection-properties"></a>빠른 연결 속성

빠른 속성 화면이 열려 있는 상태에서 연결을 선택 하 여이 연결에서 사용 되는 프로토콜과 마지막으로 표시 된 시간을 확인 합니다.

:::image type="content" source="media/how-to-work-with-maps/connection-details-v2.png" alt-text="빠른 연결 속성":::

## <a name="view-and-manage-device-properties"></a>장치 속성 보기 및 관리

지도에 표시 되는 각 장치에 대 한 장치 속성를 볼 수 있습니다. 예를 들어 장치 이름, 유형 또는 OS, 펌웨어 또는 공급 업체 등이 있습니다.

:::image type="content" source="media/how-to-work-with-maps/device-properties-v2.png" alt-text="장치 속성 보기 및 관리":::

다음 정보를 수동으로 업데이트할 수 있습니다. 수동으로 입력 한 정보는 IoT 용 Defender에서 검색 된 정보를 재정의 합니다.

  - Name

  - 유형

  - OS

  - Purdue 계층

  - Description

| 항목 | 설명 |
|--|--|
| 기본 정보 | 필요한 기본 정보입니다. |
| Name | 디바이스 이름. <br /> 기본적으로 센서는 네트워크에 정의 된 대로 장치 이름을 검색 합니다. 예를 들어 DNS 서버에 정의 된 이름이 있습니다. <br /> 이러한 이름을 정의 하지 않으면 장치 IP 주소가이 필드에 표시 됩니다. <br /> 장치 이름을 수동으로 변경할 수 있습니다. 장치에 해당 기능을 반영 하는 의미 있는 이름을 지정 합니다. |
| 형식 | 센서에서 감지한 장치 유형입니다. <br /> 자세한 내용은 [장치 형식 보기](#view-device-types)를 참조 하세요. |
| Vendor | 장치 공급 업체입니다. |
| 운영 체제 | 장치 OS입니다. |
| Purdue 계층 | 다음을 포함 하 여이 장치에 대 한 센서에 의해 식별 되는 Purdue 계층입니다. <br /> -자동 <br /> -Process 컨트롤 <br /> -감독 기관과 이러한 <br /> - 엔터프라이즈 |
| 설명 | 자유 텍스트 필드입니다. <br /> 장치에 대 한 추가 정보를 추가 합니다. |
| 특성 | 학습 기간 동안 장치에 대해 검색 되었으며 다른 범주에 속하지 않는 추가 정보는 특성 섹션에 표시 됩니다. <br /> 정보는 RO입니다. |
| 설정 | 가양성을 방지 하기 위해 장치 설정을 수동으로 변경할 수 있습니다. <br /> - **권한 있는 장치**: 학습 기간 동안 네트워크에서 검색 된 모든 장치가 승인 된 장치로 식별 됩니다. 학습 기간 후에 장치가 검색 되 면 기본적으로 권한이 없는 장치로 표시 됩니다. 이 정의를 수동으로 변경할 수 있습니다. <br /> - **스캐너로 알려짐**:이 장치를 스캐너 라고 하 고 사용자에 게 경고 하지 않아도 되는 경우이 옵션을 사용 하도록 설정 합니다. <br /> - **프로그래밍 장치**:이 장치를 프로그래밍 장치 라고 하 고 사용자에 게 경고할 필요가 없는 경우이 옵션을 사용 하도록 설정 합니다. |
| 사용자 지정 그룹 | 이 장치가 참여 하는 장치 맵의 사용자 지정 그룹입니다. |
| 시스템 상태 | 장치의 보안 및 권한 부여 상태: <br /> - `Secured` 경고가 없는 경우 상태입니다. <br /> -장치에 대 한 경고가 표시 되 면 경고 수가 표시 됩니다. <br /> - `Unauthorized` 학습 기간 후 네트워크에 추가 된 장치에 대 한 상태가 표시 됩니다. 설정에서 장치를 수동으로 정의할 수 있습니다. `Authorized Device` <br /> -이 장치의 주소가 동적 주소로 정의 되는 경우 `DHCP` 가 상태에 추가 됩니다. |


| 네트워크 | 설명 |
|--|--|
| 인터페이스 | 장치 인터페이스입니다. RO 필드입니다. |
| 프로토콜 | 장치에서 사용 하는 프로토콜입니다. RO 필드입니다. |
| 펌웨어 | 후면판 정보를 사용할 수 있는 경우 펌웨어 정보는 표시 되지 않습니다. |
| 주소 | 장치 IP 주소입니다. |
| 직렬 | 장치 일련 번호입니다. |
| 모듈 주소 | 장치 모델 및 슬롯 번호 또는 ID입니다. |
| 모델 | 장치 모델 번호입니다. |
| 펌웨어 버전 | 펌웨어 버전 번호입니다. |

장치 정보를 보려면 다음을 수행 합니다.

1. 측면 메뉴에서 **장치** 를 선택 합니다.

2. 장치를 마우스 오른쪽 단추로 클릭 하 고 **속성 보기** 를 선택 합니다. 장치 속성 창 표시 됩니다.

3. 이 장치에 대 한 경고에 대 한 자세한 정보를 보려면이 창의 맨 아래에서 필요한 경고를 선택 합니다.

### <a name="view-device-types"></a>장치 유형 보기

장치 유형은 장치 검색 프로세스 중에 센서에 의해 자동으로 식별 됩니다. 형식을 수동으로 변경할 수 있습니다.

:::image type="content" source="media/how-to-work-with-maps/type-of-device.png" alt-text="장치 유형 보기":::

다음 표에서는 시스템의 모든 형식을 보여 줍니다.

| 범주 | 디바이스 유형 |
|--|--|
| ICS | 엔지니어링 스테이션 <br /> PLC <br />Historian <br />HMI <br />I <br />DC 컨트롤러 <br />RTU <br />산업용 패키징 시스템 <br />산업용 크기 조정 <br />산업 로봇 <br />슬롯 <br />미터 <br />가변 주파수 드라이브  <br />로봇 컨트롤러 <br />서보 드라이브 <br />공기압 장치 <br />Marquee |
| IT | 도메인 컨트롤러 <br />DB 서버 <br />워크스테이션 <br />서버 <br />터미널 스테이션 <br />스토리지 <br />스마트폰 <br />태블릿 <br />백업 서버 |
| IoT | IP 카메라 <br />프린터  <br />펀치 클록 <br />ATM <br />스마트 TV <br />게임 콘솔 <br />DVR <br />도어 제어판 <br />공기 <br />Thermostat <br />화재 경보 <br />스마트 라이트 <br />스마트 스위치 <br />화재 탐지기 <br />IP 전화 <br />경보 시스템 <br />경보 Siren <br />동작 감지기 <br />엘리베이터 <br />습도 센서 <br />바코드 스캐너 <br />무정전 전원 공급 장치 <br />사용자 카운터 시스템 <br />Intercom <br />턴 스타일 |
| 네트워크 | 무선 액세스 지점 <br />라우터 <br />스위치 <br />방화벽 <br />VPN Gateway <br />NTP 서버 <br />Wifi P사이인 Apple <br />실제 위치 <br />I/o 어댑터 <br /> 프로토콜 변환기 |

장치 정보를 보려면 다음을 수행 합니다.

1.  측면 메뉴에서 **장치** 를 선택 합니다.

2. 장치를 마우스 오른쪽 단추로 클릭 하 고 **속성 보기** 를 선택 합니다. 장치 속성 창 표시 됩니다.

3. 이 장치에 대 한 경고에 대 한 자세한 정보를 보려면이 창의 맨 아래에서 필요한 경고를 선택 합니다.

### <a name="backplane-properties"></a>후면판 속성

PLC에 여러 개의 모듈이 랙 및 슬롯으로 분리 되어 있으면 모듈 카드 간에 특성이 다를 수 있습니다. 예를 들어 IP 주소와 MAC 주소가 같으면 펌웨어가 다를 수 있습니다.

후면판 옵션을 사용 하면 여러 컨트롤러/카드 및 중첩 된 장치를 다양 한 정의를 가진 하나의 엔터티로 검토할 수 있습니다. 후면판 보기의 각 슬롯은 기본 장치 (그 뒤에 검색 된 장치)를 나타냅니다.

:::image type="content" source="media/how-to-work-with-maps/backplane-image-v2.png" alt-text="후면판 속성":::

:::image type="content" source="media/how-to-work-with-maps/backplane-details-v2.png" alt-text="후면판 장치 속성":::

후면판은 최대 30 개의 컨트롤러 카드와 최대 30 개의 랙 단위를 포함할 수 있습니다. 여러 수준에 포함 된 총 장치 수는 최대 200 장치 일 수 있습니다.

후면판 창이 검색 되 면 장치 속성 창에 후면판 창이 표시 됩니다.

각 슬롯은 기본 장치 수와 모듈 유형을 표시 하는 아이콘으로 표시 됩니다.

| 아이콘 | 모듈 유형 |
|--|--|
| :::image type="content" source="media/how-to-work-with-maps/power.png" alt-text="전원 공급 장치"::: | 전원 공급 장치 |
| :::image type="content" source="media/how-to-work-with-maps/analog.png" alt-text="아날로그 i/o"::: | 아날로그 i/o |
| :::image type="content" source="media/how-to-work-with-maps/comms.png" alt-text="통신 어댑터"::: | 통신 어댑터 |
| :::image type="content" source="media/how-to-work-with-maps/digital.png" alt-text="디지털 i/o"::: | 디지털 i/o |
| :::image type="content" source="media/how-to-work-with-maps/computer-processor.png" alt-text="CPU"::: | CPU |
| :::image type="content" source="media/how-to-work-with-maps/HMI-icon.png" alt-text="HMI"::: | HMI |
| :::image type="content" source="media/how-to-work-with-maps/average.png" alt-text="일반"::: | 일반 |

슬롯을 선택 하면 슬롯 세부 정보가 표시 됩니다.

:::image type="content" source="media/how-to-work-with-maps/slot-selection-v2.png" alt-text="슬롯 선택":::

슬롯 뒤에 있는 기본 장치를 보려면 **지도에서 보기** 를 선택 합니다. 슬롯은 모든 기본 모듈 및 연결 된 장치를 사용 하 여 장치 맵에 표시 됩니다.

:::image type="content" source="media/how-to-work-with-maps/map-appearance-v2.png" alt-text="지도에서 보기":::

## <a name="view-a-timeline-of-events-for-the-device"></a>장치에 대 한 이벤트의 타임 라인 보기

장치와 연결 된 이벤트의 타임 라인을 봅니다.

타임 라인을 보려면 다음을 수행 합니다.

1. 지도에서 장치를 마우스 오른쪽 단추로 클릭 합니다.

2. **이벤트 표시** 를 선택 합니다. 선택한 장치에 대해 검색 된 이벤트에 대 한 정보가 포함 된 이벤트 타임 라인 창이 열립니다.

자세한 내용은 [이벤트 타임 라인](#event-timeline) 을 참조 하십시오.

## <a name="analyze-programming-details-and-changes"></a>프로그래밍 세부 정보 및 변경 내용 분석

네트워크 장치에서 수행 되는 프로그래밍 이벤트를 표시 하 고 코드 변경 내용을 분석 하 여 법률을 향상 시킵니다. 이 정보를 사용 하 여 의심 스러운 프로그래밍 활동을 검색할 수 있습니다. 예를 들면 다음과 같습니다.

  - 인간 오류: 엔지니어가 잘못 된 장치를 프로그래밍 하 고 있습니다.

  - 손상 된 프로그래밍 자동화: 자동화 실패로 인해 프로그래밍이 잘못 수행 되었습니다.

  - 해킹 당한 시스템: 권한이 없는 사용자가 프로그래밍 장치에 로그인 했습니다.

프로그래밍 된 장치를 표시 하 고 다른 장치에서 수행 되는 다양 한 프로그래밍 변경 내용을 스크롤할 수 있습니다.

프로그래밍 장치에서 추가, 변경, 제거 또는 변경 되지 않은 코드를 봅니다. 파일 형식, 날짜 또는 시간을 기준으로 프로그래밍 변경 내용을 검색 합니다.

### <a name="when-to-review-programming-activity"></a>프로그래밍 활동을 검토 하는 경우 

프로그래밍 작업을 검토 해야 할 수도 있습니다.

  - 권한 없는 프로그래밍에 대 한 경고를 본 후

  - 컨트롤러에 대 한 계획 된 업데이트 후

  - 프로세스나 컴퓨터가 올바르게 작동 하지 않는 경우 (마지막 업데이트를 수행한 사용자 및 시기를 확인 하려면)

:::image type="content" source="media/how-to-work-with-maps/differences.png" alt-text="프로그래밍 변경 로그":::

추가로 사용할 수 있는 옵션은 다음과 같습니다.

  - 관심 있는 이벤트를 별으로 표시 합니다.

  - 현재 코드를 사용 하 여 * .txt 파일을 다운로드 합니다.

### <a name="about-authorized-vs-unauthorized-programming-events"></a>권한 있는 vs 권한 없는 프로그래밍 이벤트 정보 

권한 없는 프로그래밍 이벤트는 프로그래밍 장치로 학습 되거나 수동으로 정의 되지 않은 장치에 의해 수행 됩니다. 권한 있는 프로그래밍 이벤트는 프로그래밍 장치로 확인 되거나 수동으로 정의 된 장치에 의해 수행 됩니다.

프로그래밍 분석 창에는 권한 있는 프로그래밍 이벤트와 권한 없는 프로그래밍 이벤트가 모두 표시 됩니다.

### <a name="accessing-programming-details-and-changes"></a>프로그래밍 세부 정보 및 변경 내용 액세스

에서 프로그래밍 분석 창에 액세스 합니다.

- [이벤트 타임 라인](#event-timeline)

- [권한 없는 프로그래밍 경고](#unauthorized-programming-alerts)

### <a name="event-timeline"></a>이벤트 타임 라인

이벤트 타임 라인을 사용 하 여 프로그래밍 변경이 검색 된 이벤트의 타임 라인을 표시 합니다.

:::image type="content" source="media/how-to-work-with-maps/timeline.png" alt-text="이벤트 타임 라인의 뷰입니다.":::

### <a name="unauthorized-programming-alerts"></a>권한 없는 프로그래밍 경고

권한 없는 프로그래밍 장치에서 프로그래밍 작업을 수행 하면 경고가 트리거됩니다.

:::image type="content" source="media/how-to-work-with-maps/unauthorized.png" alt-text="권한 없는 프로그래밍 경고":::

> [!NOTE]
> 장치 속성 창 및 장치 인벤토리에서 기본 프로그래밍 정보를 볼 수도 있습니다. 자세한 내용은 [장치 프로그래밍 정보: 추가 위치](#device-programming-information-additional-locations) 를 참조 하세요.

### <a name="working-in-the-programming-timeline-window"></a>프로그래밍 타임 라인 창에서 작업

이 섹션에서는 프로그래밍 파일을 보고 버전을 비교 하는 방법을 설명 합니다. 프로그래밍 된 장치로 전송 되는 특정 파일을 검색 합니다. 다음을 기준으로 파일 검색:

  - Date

  - 파일 형식

:::image type="content" source="media/how-to-work-with-maps/timeline-view.png" alt-text="프로그래밍 타임 라인 창":::

|프로그래밍 타임 라인 형식 | 설명 |
|--|--|
| 프로그래밍 장치 | 호스트 이름 및 파일을 포함 하 여 프로그래밍 된 장치에 대 한 세부 정보를 제공 합니다. |
| 최근 이벤트 | 센서에서 검색 된 최신 이벤트 50를 표시 합니다. <br />이벤트를 강조 표시 하려면 해당 이벤트 위로 마우스를 이동 하 고 별을 클릭 합니다. :::image type="icon" source="media/how-to-work-with-maps/star.png" border="false"::: <br /> 최근 50 이벤트를 볼 수 있습니다. |
| 파일 | 프로그램에서 선택한 날짜 및 파일 크기에 대해 검색 된 파일을 표시 합니다. <br /> 기본적으로 장치당 표시할 수 있는 파일의 최대 수는 300입니다. <br /> 기본적으로 각 파일의 최대 파일 크기는 15mb입니다. |
| 파일 상태 :::image type="icon" source="media/how-to-work-with-maps/status-v2.png" border="false"::: | 파일 레이블은 다음을 포함 하 여 장치에 있는 파일의 상태를 표시 합니다. <br /> **추가** 됨: 선택한 날짜 또는 시간의 끝점에 파일이 추가 되었습니다. <br /> **업데이트** 됨: 파일이 선택한 날짜 또는 시간에 업데이트 되었습니다. <br /> **삭제** 됨:이 파일이 제거 되었습니다. <br /> **레이블 없음**: 파일이 변경 되지 않았습니다.   |
| 프로그래밍 장치 | 프로그래밍을 변경한 장치입니다. 여러 장치에서 프로그래밍 된 단일 장치에 대 한 프로그래밍 변경을 수행 했을 수 있습니다. 호스트 이름, 날짜 또는 변경 시간 및 로그인 한 사용자가 표시 됩니다. |
| :::image type="icon" source="media/how-to-work-with-maps/current.png" border="false"::: | 프로그램 장치에 설치 된 현재 파일을 표시 합니다. |
| :::image type="icon" source="media/how-to-work-with-maps/download-text.png" border="false"::: | 표시 된 코드의 텍스트 파일을 다운로드 합니다. |
| :::image type="icon" source="media/how-to-work-with-maps/compare.png" border="false"::: | 선택한 날짜에서 검색 된 파일과 현재 파일을 비교 합니다. |

### <a name="choose-a-file-to-review"></a>검토할 파일 선택

이 섹션에서는 검토할 파일을 선택 하는 방법을 설명 합니다.

검토할 파일을 선택 하려면 다음을 수행 합니다.

1. **최근 이벤트** 창에서 이벤트를 선택 합니다.

2. 파일 창에서 파일 형식을 선택 합니다. 파일이 현재 창에 표시 됩니다.

:::image type="content" source="media/how-to-work-with-maps/choose-file.png" alt-text="사용할 파일을 선택 합니다.":::

### <a name="compare-files"></a>파일 비교

이 섹션에서는 프로그래밍 파일을 비교 하는 방법을 설명 합니다.

비교 대상:

1. 최근 이벤트 창에서 이벤트를 선택 합니다.

2. 파일 창에서 파일을 선택 합니다. 파일이 현재 창에 표시 됩니다. 이 파일을 다른 파일과 비교할 수 있습니다.

3. 비교 표시기를 선택 합니다.

   :::image type="content" source="media/how-to-work-with-maps/compare.png" alt-text="비교 표시기":::

   선택한 파일이 프로그래밍 된 장치에서 검색 된 모든 날짜가 창에 표시 됩니다. 파일이 프로그래밍 장치에서 여러 프로그래밍 장치에 의해 업데이트 되었을 수 있습니다.

   검색 된 차이점 수가 창의 오른쪽 위 모퉁이에 표시 됩니다. 차이점을 보려면 아래로 스크롤해야 할 수도 있습니다.

   :::image type="content" source="media/how-to-work-with-maps/scroll.png" alt-text="선택 영역까지 아래로 스크롤합니다.":::

   이 숫자는 변경 된 텍스트의 인접 한 줄로 계산 됩니다. 예를 들어 연속 하는 8 개의 코드 줄이 변경 (삭제, 업데이트 또는 추가) 된 경우이는 한 가지 차이로 계산 됩니다.

   :::image type="content" source="media/how-to-work-with-maps/program-timeline.png" alt-text="타임 라인 보기입니다.":::

4. 날짜를 선택 합니다. 선택한 날짜에서 검색 된 파일이 창에 표시 됩니다.

5. 최근 이벤트/파일 창에서 선택한 파일은 항상 오른쪽에 표시 됩니다.

### <a name="device-programming-information-additional-locations"></a>장치 프로그래밍 정보: 추가 위치

프로그래밍 타임 라인의 세부 정보를 검토 하는 것 외에도 장치 속성 창 및 장치 인벤토리에서 프로그래밍 정보에 액세스할 수 있습니다.

| 디바이스 유형 | 설명 |
|--|--|
| 디바이스 속성 | 장치 속성 창은 device\. 검색 된 마지막 프로그래밍 이벤트에 대 한 정보를 제공 합니다. :::image type="content" source="media/how-to-work-with-maps/information-from-device-v2.png" alt-text="장치의 속성"::: |
| 장치 인벤토리 | 장치 인벤토리는 장치가 프로그래밍 장치 인지 여부를 나타냅니다. :::image type="content" source="media/how-to-work-with-maps/inventory-v2.png" alt-text="장치 인벤토리"::: |

## <a name="manage-device-information-from-the-map"></a>맵에서 장치 정보 관리

센서는 네트워크에서 직접 장치를 업데이트 하거나 영향을 주지 않습니다. 여기에서 변경한 내용은에서 장치를 분석 하는 방법에만 영향을 줍니다.

### <a name="delete-devices"></a>디바이스 삭제

학습 된 정보가 관련이 없는 경우 장치를 삭제할 수 있습니다. 예를 들면 다음과 같습니다.

  - 엔지니어링 워크스테이션의 파트너 계약자는 구성 업데이트를 수행 하는 데 연결 합니다. 작업이 완료 된 후에는 장치를 더 이상 모니터링 하지 않아야 합니다.

  - 네트워크의 변경으로 인해 일부 장치는 더 이상 연결 되지 않습니다.

장치를 삭제 하지 않으면 센서가 계속 모니터링 하 게 됩니다. 60 일 후에 삭제를 제안 하는 알림이 표시 됩니다.

다른 장치에서 액세스 하려고 하는 경우 장치가 응답 하지 않음을 나타내는 경고가 표시 될 수 있습니다. 이 경우 네트워크가 잘못 구성 되었을 수 있습니다.

장치는 장치 맵, 장치 인벤토리 및 데이터 마이닝 보고서에서 제거 됩니다. 기타 정보 (예: 위젯에 저장 된 정보)는 유지 됩니다.

장치를 삭제 하려면 10 분 이상 장치를 활성화 해야 합니다.

장치 맵에서 장치를 삭제 하려면 다음을 수행 합니다.

1. 측면 메뉴에서 **장치** 를 선택 합니다.

2. 장치를 마우스 오른쪽 단추로 클릭 하 고 **삭제** 를 선택 합니다.

### <a name="merge-devices"></a>장치 병합

특정 상황에서는 장치를 병합 해야 할 수 있습니다. 센서가 하나의 고유한 장치인 별도의 네트워크 엔터티를 발견 한 경우에 필요할 수 있습니다. 예를 들면 다음과 같습니다.

  - 4 개의 네트워크 카드가 있는 PLC

  - WIFI 및 실제 카드를 사용 하는 랩톱

병합할 때 두 장치의 장치 속성을 하나로 결합 하도록 센서에 지시 합니다. 이렇게 하면 장치 속성 창 및 센서 보고서가 새 장치 속성 세부 정보로 업데이트 됩니다.

예를 들어 IP 주소를 사용 하 여 두 장치를 병합 하는 경우 두 IP 주소는 장치 속성 창 별도의 인터페이스로 표시 됩니다. 권한 있는 장치만 병합할 수 있습니다.

:::image type="content" source="media/how-to-work-with-maps/device-properties-v2.png" alt-text="장치 속성 창":::

이벤트 타임 라인은 merge 이벤트를 표시 합니다.

:::image type="content" source="media/how-to-work-with-maps/events-time.png" alt-text="병합 된 이벤트가 있는 이벤트 타임 라인입니다.":::

장치 병합을 실행 취소할 수 없습니다. 두 장치를 실수로 병합 한 경우 장치를 삭제 하 고 센서가 두 장치를 다시 검색할 때까지 기다립니다.

장치를 병합 하려면:

1. 두 개의 장치를 선택 하 고 그 중 하나를 마우스 오른쪽 단추로 클릭 합니다.

2. **병합** 을 선택 하 여 장치를 병합 합니다. 병합을 완료 하는 데 최대 2 분이 걸릴 수 있습니다.

3. 병합 장치 특성 설정 대화 상자에서 장치 이름을 선택 합니다.

   :::image type="content" source="media/how-to-work-with-maps/name-the-device-v2.png" alt-text="특성 대화 상자":::

4. **저장** 을 선택합니다.

### <a name="authorize-and-unauthorize-devices"></a>장치 권한 부여 및 unauthorize

학습 기간 동안 네트워크에서 검색 된 모든 장치는 권한 있는 장치로 식별 됩니다. **권한** 있는 레이블은 장치 맵의 이러한 장치에 표시 되지 않습니다.

학습 기간 후에 장치가 검색 되 면 권한이 없는 장치로 표시 됩니다. 지도에 권한이 없는 장치를 표시 하는 것 외에도 장치 인벤토리에 표시 될 수 있습니다.

:::image type="content" source="media/how-to-work-with-maps/inventory-icon.png" alt-text="장치 인벤토리":::

**새 장치 및 권한 없음**

학습 기간이 표시 된 후 새 장치가 및 레이블로 표시 됩니다 `New` `Unauthorized` .

지도에서 장치를 이동 하거나 장치 속성을 수동으로 변경 하는 경우 `New` 레이블이 장치 아이콘에서 제거 됩니다.

#### <a name="unauthorized-devices---attack-vectors-and-risk-assessment-reports"></a>권한 없는 장치-공격 벡터 및 위험 평가 보고서

권한 없는 장치는 위험 평가 보고서 및 공격 벡터 보고서에 포함 되어 계산 됩니다.

- **공격 벡터 보고서:** 허가 되지 않음으로 표시 된 장치는 네트워크에 위협이 될 수 있는 의심 스러운 rogue 장치로 공격 벡터에서 확인 됩니다.

   :::image type="content" source="media/how-to-work-with-maps/attack-vector-reports.png" alt-text="공격 벡터 보고서 보기가":::

- **위험 평가 보고서:** 인증 되지 않은 것으로 표시 된 장치는 다음과 같습니다.

  - 위험 평가 보고서에서 식별 됨

장치를 수동으로 인증 하거나 unauthorize:

1. 지도에서 장치를 마우스 오른쪽 단추로 클릭 하 고 **Unauthorize** 를 선택 합니다.

### <a name="mark-devices-as-important"></a>장치를 중요 한 것으로 표시

중요 한 네트워크 장치를 중요 한 것으로 표시할 수 있습니다 (예: 업무상 중요 한 서버). 이러한 장치는 맵에 별로 표시 됩니다. 별은 지도의 확대/축소 수준에 따라 달라 집니다.

:::image type="icon" source="media/how-to-work-with-maps/star-one.png" border="false"::: :::image type="icon" source="media/how-to-work-with-maps/star-two.png" border="false"::: :::image type="icon" source="media/how-to-work-with-maps/star-3.png" border="false":::

### <a name="important-devices---attack-vectors-and-risk-assessment-reports"></a>중요 한 장치-공격 벡터 및 위험 평가 보고서

중요 한 장치는 위험 평가 보고서 및 공격 벡터 보고서를 생성할 때 계산 됩니다.

  - 공격 벡터 보고 중요 한 것으로 표시 된 장치는 공격 대상으로 공격 벡터에서 확인 됩니다. 

  - 위험 평가 보고서: 위험 평가 보고서에서 보안 점수를 제공할 때 중요로 표시 된 장치가 계산 됩니다.

## <a name="generate-activity-reports-from-the-map"></a>맵에서 활동 보고서 생성

1, 6, 12 또는 24 시간 동안 선택한 장치에 대 한 활동 보고서를 생성 합니다. 다음과 같은 정보가 제공됩니다.

  - 범주: 트래픽 시나리오를 기반으로 하는 기본 검색 정보입니다.

  - 원본 및 대상 장치

  - 데이터: 추가 정보 제거.

  - 마지막으로 표시 된 시간 및 날짜입니다.

Microsoft Excel 또는 Word 파일로 보고서를 저장할 수 있습니다.

:::image type="content" source="media/how-to-work-with-maps/historical-information.png" alt-text="최근 활동":::

장치에 대 한 활동 보고서를 생성 하려면:

1. 지도에서 장치를 마우스 오른쪽 단추로 클릭 합니다.

2. 활동 보고서를 선택 합니다.

   :::image type="content" source="media/how-to-work-with-maps/activity-report.png" alt-text="활동 보고서를 봅니다.":::

## <a name="generate-attack-vector-reports-from-the-map"></a>맵에서 공격 벡터 보고서 생성

공격 벡터 보고서를 시뮬레이트하여 선택한 지도의 장치가 공격에 취약 한 공격 대상 인지를 알아보세요.

공격 벡터 보고서는 악용 가능한 장치의 취약점 체인에 대 한 그래픽 표현을 제공 합니다. 이러한 취약점으로 인해 키 네트워크 장치에 대 한 공격자 액세스 권한이 제공 될 수 있습니다. 공격 벡터 시뮬레이터는 공격 벡터를 실시간으로 계산 하 고 특정 대상에 따라 모든 공격 벡터를 분석 합니다.

공격 벡터 보고서에서 장치를 보려면 다음을 수행 합니다.

1. 지도에서 장치를 마우스 오른쪽 단추로 클릭 합니다.

2. **공격 벡터 시뮬레이트** 를 선택 합니다. 공격 대상으로 선택한 장치와 함께 공격 벡터 대화 상자가 열립니다.

   :::image type="content" source="media/how-to-work-with-maps/simulation.png" alt-text="공격 벡터 시뮬레이션 추가":::

3. 대화 상자에 나머지 매개 변수를 추가 하 고 **시뮬레이션 추가** 를 선택 합니다.

## <a name="export-device-information-from-the-map"></a>맵에서 장치 정보 내보내기

맵에서 다음 장치 정보를 내보냅니다.

  - 장치 세부 정보 (Microsoft Excel)

  - 장치 요약 (Microsoft Excel)

  - 그룹이 있는 word 파일 (Microsoft Word)

내보내기:

1. 맵에서 내보내기 아이콘을 선택 합니다.

1. 내보내기 옵션을 선택 합니다.

## <a name="see-also"></a>참조

[장치 인벤토리에서 센서 검색 조사](how-to-investigate-sensor-detections-in-a-device-inventory.md)
