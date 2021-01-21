---
title: 모든 엔터프라이즈 센서가 검색 하는 장치에 대해 알아보기
description: 온-프레미스 관리 콘솔에서 장치 인벤토리를 사용 하 여 연결 된 센서에서 장치 정보에 대 한 포괄적인 보기를 가져올 수 있습니다. 가져오기, 내보내기 및 필터링 도구를 사용 하 여이 정보를 관리 합니다.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/02/2020
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 9da5c8c89ee124e527584164b21b096ac815e5ca
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/21/2021
ms.locfileid: "98625467"
---
# <a name="investigate-all-enterprise-sensor-detections-in-the-device-inventory"></a>장치 인벤토리에서 모든 엔터프라이즈 센서 검색 조사

온-프레미스 관리 콘솔에서 *장치 인벤토리* 를 사용 하 여 연결 된 센서에서 장치 정보를 볼 수 있습니다. 이 기능은 모든 네트워크 정보에 대 한 포괄적인 보기를 제공 합니다. 가져오기, 내보내기 및 필터링 도구를 사용 하 여이 정보를 관리 합니다. 연결 된 센서 버전에 대 한 상태 정보도 표시 됩니다.

:::image type="content" source="media/how-to-work-with-asset-inventory-information/device-inventory-data-table.png" alt-text="장치 인벤토리 데이터 테이블의 스크린샷":::

다음 표에서는 장치 인벤토리의 테이블 열에 대해 설명 합니다.

| 매개 변수 | Description |
|--|--|
| **승인 되지 않은 경고** | 이 장치와 연결 된 처리 되지 않은 경고의 수입니다. |
| **비즈니스 단위** | 이 장치를 포함 하는 사업부입니다. |
| **지역** | 이 장치를 포함 하는 지역입니다. |
| **사이트** | 이 장치를 포함 하는 사이트입니다. |
| **영역** | 이 장치를 포함 하는 영역입니다. |
| **어플라이언스** | 이 장치를 보호 하는 IoT 용 Azure Defender 센서입니다. |
| **이름** | IoT 용 Defender에서이 장치의 이름을 검색 했습니다. |
| **Type** | 장치 유형 (예: PLC 또는 HMI)입니다. |
| **공급업체** | MAC 주소에 정의 된 장치 공급 업체의 이름입니다. |
| **운영 체제** | 장치의 OS입니다. |
| **펌웨어** | 장치의 펌웨어입니다. |
| **IP 주소** | 장치의 IP 주소입니다. |
| **VLAN** | 장치의 VLAN입니다. |
| **MAC 주소** | 디바이스의 Mac 주소입니다. |
| **프로토콜** | 장치에서 사용 하는 프로토콜입니다. |
| **승인 되지 않은 경고** | 이 장치와 연결 된 처리 되지 않은 경고의 수입니다. |
| **권한 부여 됨** | 장치의 권한 부여 상태:<br />- **True**: 장치에 권한이 부여 되었습니다.<br />- **False**: 장치에 권한이 부여 되지 않았습니다. |
| **스캐너 라고 합니다.** | 이 장치가 네트워크에서 검사와 같은 작업을 수행 하는지 여부입니다. |
| **프로그래밍 장치** | 프로그래밍 장치 인지 여부입니다.<br />- **True**: 장치에서 엔지니어링 스테이션과 관련 된 Plcs, RTUs 및 컨트롤러에 대 한 프로그래밍 활동을 수행 합니다.<br />- **False**: 장치가 프로그래밍 장치가 아닙니다. |
| **그룹** | 이 장치가 참여 하는 그룹입니다. |
| **마지막 작업** | 장치에서 수행 하는 마지막 작업입니다. |
| **Discovered** | 이 장치가 네트워크에서 처음으로 표시 되는 경우 |

## <a name="integrate-data-into-the-enterprise-device-inventory"></a>엔터프라이즈 장치 인벤토리에 데이터 통합

데이터 통합 기능을 사용 하면 다른 엔터프라이즈 리소스의 정보를 사용 하 여 장치 인벤토리의 데이터를 향상 시킬 수 있습니다. 이러한 원본에는 CMDBs, DNS, 방화벽 및 Web Api가 포함 됩니다.

이 정보를 사용 하 여 학습할 수 있습니다. 예를 들면 다음과 같습니다.

- 장치 구매 날짜 및 보증 종료 날짜

- 각 장치를 담당 하는 사용자

- 장치에 대해 열린 티켓

- 펌웨어가 업그레이드 된 마지막 날짜

- 인터넷에 대 한 액세스를 허용 하는 장치

- 활성 바이러스 백신 응용 프로그램을 실행 하는 장치

- 장치에 로그인 한 사용자

:::image type="content" source="media/how-to-work-with-asset-inventory-information/asset-inventory-screen-with-items-highlighted-v2.png" alt-text="장치 인벤토리 화면의 데이터 테이블":::

다음 중 하나를 수행 하 여 데이터를 통합할 수 있습니다.

- 수동으로 추가

- IoT 용 Defender에서 제공 하는 사용자 지정 스크립트 실행

:::image type="content" source="media/how-to-work-with-asset-inventory-information/enterprise-data-integrator-graph.png" alt-text="엔터프라이즈 데이터 통합자의 다이어그램입니다.":::

IoT 기술 지원을 위해 Defender를 사용 하 여 웹 API 쿼리를 수신 하는 시스템을 설정할 수 있습니다.

수동으로 데이터를 추가 하려면:

1. 측면 메뉴에서 **장치 인벤토리** 를 선택 하 고를 선택 :::image type="icon" source="media/how-to-work-with-asset-inventory-information/menu-icon.png" border="false"::: 합니다.

   :::image type="content" source="media/how-to-work-with-asset-inventory-information/asset-inventory-settings-v2.png" alt-text="장치의 인벤토리 설정을 편집 합니다.":::

2. **장치 인벤토리 설정** 대화 상자에서 **사용자 지정 열 추가** 를 선택 합니다.

   :::image type="content" source="media/how-to-work-with-asset-inventory-information/add-custom-column.png" alt-text="인벤토리에 사용자 지정 열을 추가 합니다.":::

3. **사용자 지정 열 추가** 대화 상자에서 새 열 이름 (최대 250 자)을 추가 하 고 **수동** 을 선택한 다음 **저장** 을 선택 합니다. **장치 인벤토리 설정** 대화 상자에 새 항목이 나타납니다.

4. **장치 인벤토리** 창의 오른쪽 위 모퉁이에서 :::image type="icon" source="media/how-to-work-with-asset-inventory-information/menu-icon-device-inventory.png" border="false"::: **모든 장치 인벤토리 내보내기** 를 선택 하 고 선택 합니다. CSV 파일이 생성 됩니다.

   :::image type="content" source="media/how-to-work-with-asset-inventory-information/sample-exported-csv-file.png" alt-text="내보낸 CSV 파일입니다.":::

5. 새 열에 수동으로 정보를 추가 하 고 파일을 저장 합니다.

6. **장치 인벤토리** 창의 오른쪽 위 모서리에서을 선택 하 :::image type="icon" source="media/how-to-work-with-asset-inventory-information/menu-icon-device-inventory.png" border="false"::: 고, **수동 입력 열 가져오기** 를 선택 하 고, CSV 파일로 이동 합니다. 새 데이터가 **장치 인벤토리** 테이블에 표시 됩니다.

다른 엔터프라이즈 엔터티의 데이터를 통합 하려면 다음을 수행 합니다.

1. **장치 인벤토리** 창의 오른쪽 위 모퉁이에서 :::image type="icon" source="media/how-to-work-with-asset-inventory-information/menu-icon-device-inventory.png" border="false"::: **모든 장치 인벤토리 내보내기** 를 선택 하 고 선택 합니다.

2. **장치 인벤토리 설정** 대화 상자에서 **사용자 지정 열 추가** 를 선택 합니다.

   :::image type="content" source="media/how-to-work-with-asset-inventory-information/add-custom-column.png" alt-text="인벤토리에 사용자 지정 열을 추가 합니다.":::

3. **사용자 지정 열 추가** 대화 상자에서 새 열 이름 (최대 250 자 u t f)을 추가한 다음 **자동** 을 선택 합니다. **스크립트 업로드** 및 **스크립트 테스트** 옵션이 나타납니다.

   :::image type="content" source="media/how-to-work-with-asset-inventory-information/add-custom-column-automatic.png" alt-text="사용자 지정 열을 자동으로 추가 합니다.":::

4. [Microsoft 지원](https://support.serviceshub.microsoft.com/supportforbusiness/create?sapId=82c88f35-1b8e-f274-ec11-c6efdd6dd099)에서 받은 스크립트를 업로드 하 고 테스트 합니다.

## <a name="retrieve-information-from-the-device-inventory"></a>장치 인벤토리에서 정보 검색

관리 센서에서 검색 한 광범위 한 장치 정보를 검색 하 고 해당 정보를 파트너 시스템과 통합할 수 있습니다. 예를 들어 센서, 영역, 사이트 ID, IP 주소, MAC 주소, 펌웨어, 프로토콜 및 공급 업체 정보를 검색할 수 있습니다. 다음을 기준으로 검색 한 필터 정보:

- 권한 있는 장치 및 권한이 없는 장치.

- 특정 사이트와 연결 된 장치입니다.

- 특정 영역과 연결 된 장치입니다.

- 특정 센서와 연결 된 장치입니다.

IoT API 용 Defender를 사용 하 여이 정보를 검색 하 고 통합 합니다. 자세한 내용은 [IoT 용 DEFENDER api 센서 및 관리 콘솔 api](references-work-with-defender-for-iot-apis.md)를 참조 하세요.

## <a name="filter-the-device-inventory"></a>장치 인벤토리 필터링

장치 인벤토리를 필터링 하 여 관심 있는 열을 표시할 수 있습니다. 예를 들어 PLC 장치 정보를 볼 수 있습니다.

:::image type="content" source="media/how-to-work-with-asset-inventory-information/asset-inventory-view-v2.png" alt-text="장치 인벤토리의 스크린샷.":::

창을 벗어나면 필터가 지워집니다.

동일한 필터를 여러 번 사용 하려면 필요한 필터 또는 필터 조합을 저장할 수 있습니다. 왼쪽 창을 열고 저장 한 필터를 볼 수 있습니다.

:::image type="content" source="media/how-to-work-with-asset-inventory-information/view-your-asset-inventories-v2.png" alt-text="장치 인벤토리 화면":::

장치 인벤토리를 필터링 하려면:

1. 필터링 할 열에서을 선택 :::image type="icon" source="media/how-to-work-with-asset-inventory-information/filter-a-column-icon.png" border="false"::: 합니다.

2. **필터** 대화 상자에서 필터 형식을 선택 합니다.

   - **Equals**: 열을 필터링 하는 데 사용할 정확한 값입니다. 예를 들어, 및에 따라 프로토콜 **열을 필터링** 하는 경우이 `value=ICMP` 열은 ICMP 프로토콜만 사용 하는 장치를 표시 합니다.

   - **Contains**: 열의 다른 값 사이에 포함 된 값입니다. 예를 **들어** 및에 따라 프로토콜 열을 필터링 하는 경우이 `value=ICMP` 열은 장치에서 사용 하는 프로토콜 목록의 일부로 ICMP 프로토콜을 사용 하는 장치를 표시 합니다.

3. 사전순에 따라 열 정보를 구성 하려면를 선택 :::image type="icon" source="media/how-to-work-with-asset-inventory-information/alphabetical-order-icon.png" border="false"::: 합니다. 및 화살표를 선택 하 여 순서를 정렬 합니다 :::image type="icon" source="media/how-to-work-with-asset-inventory-information/alphabetical-a-z-order-icon.png" border="false"::: :::image type="icon" source="media/how-to-work-with-asset-inventory-information/alphabetical-z-a-order-icon.png" border="false"::: .

4. 새 필터를 저장 하려면 필터를 정의 하 고 다른 **이름으로 저장** 을 선택 합니다.

5. 필터 정의를 변경 하려면 정의를 변경 하 고 **변경 내용 저장** 을 선택 합니다.

## <a name="view-device-information-per-zone"></a>영역별 장치 정보 보기

영역에서 장치에 대 한 다음 정보를 알아볼 수 있습니다.

### <a name="view-a-device-map"></a>장치 맵 보기

선택한 영역에 대 한 센서 장치 맵을 보려면:

- **사이트 관리** 창의 영역 이름을 포함 하는 표시줄에서 **영역 맵 보기** 를 선택 합니다.

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/default-region-to-default-business-unit-v2.png" alt-text="기본 사업부의 기본 영역입니다.":::

**장치 맵** 창이 표시 됩니다. 센서, 연결 된 장치 및 기타 정보를 포함 하 여 선택한 영역과 관련 된 모든 네트워크 요소를 표시 합니다.

:::image type="content" source="media/how-to-work-with-asset-inventory-information/zone-map-screenshot.png" alt-text="영역 지도의 스크린샷":::

지도에서 장치 및 장치 정보를 보는 데 사용할 수 있는 도구는 다음과 같습니다. 이러한 각 기능에 대 한 자세한 내용은 *IoT platform 사용자 가이드* 를 참조 하세요.

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

### <a name="view-alerts-associated-with-a-zone"></a>영역과 연결 된 경고 보기

특정 영역과 연결 된 경고를 보려면 다음을 수행 합니다.

- **영역** 창에서 경고 아이콘을 선택 합니다. 

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/business-unit-view-v2.png" alt-text="예제가 포함 된 기본 사업부 뷰입니다.":::

자세한 내용은 [개요: 경고 사용](how-to-work-with-alerts-on-premises-management-console.md)을 참조 하세요.

### <a name="view-the-device-inventory-of-a-zone"></a>영역에 대 한 장치 인벤토리 보기

특정 영역과 연결 된 장치 인벤토리를 보려면 다음을 수행 합니다.

- **영역** 창에서 **장치 인벤토리 보기** 를 선택 합니다.

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/default-business-unit.png" alt-text="장치 인벤토리 화면이 표시 됩니다.":::

자세한 내용은 [장치 인벤토리에서 모든 엔터프라이즈 센서 검색 조사](how-to-investigate-all-enterprise-sensor-detections-in-a-device-inventory.md)를 참조 하세요.

### <a name="view-additional-zone-information"></a>추가 영역 정보 보기

다음과 같은 추가 영역 정보를 사용할 수 있습니다.

- **영역 세부 정보**: 영역에 연결 된 장치, 경고 및 센서의 수를 확인 합니다.

- **센서 세부 정보**: 영역에 할당 된 각 센서의 이름, IP 주소 및 버전을 표시 합니다.

- **연결 상태**: 센서의 연결이 끊어지면 센서에서 연결 합니다. [온-프레미스 관리 콘솔에 센서 연결을](how-to-activate-and-set-up-your-on-premises-management-console.md#connect-sensors-to-the-on-premises-management-console)참조 하세요. 

- **업데이트 진행률**: 연결 된 센서를 업그레이드 하는 경우 업그레이드 상태가 표시 됩니다. 업그레이드 하는 동안 온-프레미스 관리 콘솔은 센서에서 장치 정보를 받지 않습니다.

## <a name="see-also"></a>참고 항목

[디바이스 인벤토리에서 센서 감지 조사](how-to-investigate-sensor-detections-in-a-device-inventory.md)
