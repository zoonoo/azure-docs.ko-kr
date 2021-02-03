---
title: 특정 센서에서 검색 된 장치에 대 한 통찰력 얻기
description: 장치 인벤토리는 센서가 검색 하는 광범위 한 장치 특성을 표시 합니다.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/06/2020
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: c15dc62cad796f5f74f2316d148e217052dc9678
ms.sourcegitcommit: b85ce02785edc13d7fb8eba29ea8027e614c52a2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/03/2021
ms.locfileid: "99508865"
---
# <a name="investigate-sensor-detections-in-a-device-inventory"></a>디바이스 인벤토리에서 센서 감지 조사

장치 인벤토리는 센서가 검색 하는 광범위 한 장치 특성을 표시 합니다. 옵션은 다음에서 사용할 수 있습니다.

 - 쉽게 정보를 필터링 합니다.

 - CSV 파일로 정보를 내보냅니다.

 - Windows 레지스트리 세부 정보를 가져옵니다.

 - 장치 맵에 표시할 그룹을 만듭니다.

## <a name="view-device-attributes-in-the-device-inventory"></a>장치 인벤토리에서 장치 특성 보기

다음 특성은 장치 인벤토리 테이블에 표시 됩니다.

| 매개 변수 | 설명 |
|--|--|
| Name | 센서가 검색 한 장치 이름 이거나 사용자가 입력 한 장치 이름입니다. |
| 유형 | 센서에 의해 결정 되거나 사용자가 입력 한 장치의 유형입니다. |
| Vendor | MAC 주소에 정의 된 장치 공급 업체의 이름입니다. |
| 운영 체제 | 검색 된 장치의 OS입니다. |
| 펌웨어 버전 | 검색 된 장치의 펌웨어입니다. |
| IP 주소 | 정의 된 장치의 IP 주소입니다. |
| VLAN | 장치의 VLAN입니다. 센서에서 Vlan을 검색 하도록 지시 하는 방법에 대 한 자세한 내용은 [vlan 이름 정의](how-to-manage-the-on-premises-management-console.md#define-vlan-names)를 참조 하세요. (방법-관리------------vlan-이름). |
| MAC 주소 | 디바이스의 Mac 주소입니다. |
| 프로토콜 | 장치에서 사용 하는 프로토콜입니다. |
| 승인 되지 않은 경고 | 이 장치와 연결 된 승인 되지 않은 경고의 수입니다. |
| 권한 부여 됨 | 사용자가 정의한 권한 부여 상태:<br />- **True**: 장치에 권한이 부여 되었습니다.<br />- **False**: 장치에 권한이 부여 되지 않았습니다. |
| 스캐너 라고 합니다. | 사용자가 네트워크 검색 장치로 정의 됩니다. |
| 프로그래밍 장치 | 사용자가 권한 있는 프로그래밍 장치로 정의 됩니다. <br />- **True**: 장치에서 엔지니어링 스테이션과 관련 된 Plcs, RTUs 및 컨트롤러에 대 한 프로그래밍 활동을 수행 합니다. <br />- **False**: 장치가 프로그래밍 장치가 아닙니다. |
| 그룹 | 이 장치가 참여 하는 그룹입니다. |
| 마지막 작업 | 장치에서 수행 하는 마지막 작업입니다. |
| Discovered | 이 장치가 네트워크에서 처음으로 표시 되는 경우 |

장치 인벤토리를 보려면 다음을 수행 합니다.

1. 왼쪽 창에서 **장치** 를 선택 합니다. **장치** 창이 오른쪽에 열립니다.

2. **장치** 창에서을 선택 :::image type="icon" source="media/how-to-work-with-asset-inventory-information/device-pane-icon.png" border="false"::: 합니다.

열을 숨기 거 나 표시 하려면 장치 인벤토리 테이블을 사용자 지정 합니다.

1. 장치 인벤토리의 오른쪽 상단 메뉴에서를 선택 :::image type="icon" source="media/how-to-work-with-asset-inventory-information/settings-icon.png" border="false"::: 합니다.

    :::image type="content" source="media/how-to-work-with-asset-inventory-information/device-inventory-settings-screens-v2.png" alt-text="장치 인벤토리 설정 화면.":::

2. **장치 인벤토리 설정** 창에서 장치 인벤토리 테이블에 표시 하려는 열을 선택 합니다.

3. 화살표를 사용 하 여 테이블의 열 위치를 변경 합니다.

4. **저장** 을 선택합니다. **장치 인벤토리 설정** 창이 닫히고 새 설정이 표에 표시 됩니다.

### <a name="create-temporary-device-inventory-filters"></a>임시 장치 인벤토리 필터 만들기

테이블에 표시 되는 정보를 정의 하는 필터를 설정할 수 있습니다. 예를 들어 PLC 장치의 정보만 보도록 결정할 수 있습니다.

:::image type="content" source="media/how-to-work-with-asset-inventory-information/devices-learning-v2.png" alt-text="장치 학습.":::

인벤토리를 떠날 때 필터는 저장 되지 않습니다.

### <a name="save-device-inventory-filters"></a>장치 인벤토리 필터 저장

필요한 필터 또는 필터 조합을 저장 하 고 장치 인벤토리에 다시 적용할 수 있습니다. 특정 장치 유형 또는 특정 유형 및 특정 프로토콜을 기반으로 하는 더 좁은 필터를 기반으로 더 광범위 한 필터를 만듭니다.

저장 하는 필터는 장치 맵 그룹으로도 저장 됩니다. 이 기능은 지도의 네트워크 장치를 볼 때 추가 세분성 수준을 제공 합니다.

필터를 만들려면:

1. 필터링 할 열에서을 선택 :::image type="icon" source="media/how-to-work-with-asset-inventory-information/filter-icon.png" border="false"::: 합니다.

2. **필터** 대화 상자에서 필터 형식을 선택 합니다.

   - **Equals**: 열을 필터링 하는 데 사용할 정확한 값입니다. 예를 들어, 및에 따라 프로토콜 **열을 필터링** 하는 경우이 `value=ICMP` 열은 ICMP 프로토콜만 사용 하는 장치를 표시 합니다.

   - **Contains**: 열의 다른 값 사이에 포함 된 값입니다. 예를 **들어** 및에 따라 프로토콜 열을 필터링 하는 경우이 `value=ICMP` 열은 장치에서 사용 하는 프로토콜 목록의 일부로 ICMP 프로토콜을 사용 하는 장치를 표시 합니다.

3. 사전순에 따라 열 정보를 구성 하려면를 선택 :::image type="icon" source="media/how-to-work-with-asset-inventory-information/alphabetical-order-icon.png" border="false"::: 합니다. 및 화살표를 선택 하 여 순서를 정렬 합니다 :::image type="icon" source="media/how-to-work-with-asset-inventory-information/alphabetical-a-z-order-icon.png" border="false"::: :::image type="icon" source="media/how-to-work-with-asset-inventory-information/alphabetical-z-a-order-icon.png" border="false"::: .

4. 새 필터를 저장 하려면 필터를 정의 하 고 다른 **이름으로 저장** 을 선택 합니다.

5. 필터 정의를 변경 하려면 정의를 변경 하 고 **변경 내용 저장** 을 선택 합니다.

필터를 보려면 다음을 수행 합니다.

- 왼쪽 창을 열고 저장 한 필터를 확인 합니다.

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/filters-from-left-pane-v2.png" alt-text="왼쪽 창에서 필터를 봅니다.":::

### <a name="view-filtered-information-as-a-map-group"></a>필터링 된 정보를 지도 그룹으로 보기

지도 보기로 전환 하면 필터링 된 장치가 강조 표시 되 고 필터링 됩니다. 저장 한 필터 그룹이 **장치 인벤토리 필터** 그룹 아래의 측면 메뉴에 표시 됩니다.

:::image type="content" source="media/how-to-work-with-asset-inventory-information/filters-in-the-map-view-v2.png" alt-text="지도 보기에 필터를 표시 합니다.":::

## <a name="learn-windows-registry-details"></a>Windows 레지스트리 정보 알아보기

장치를 학습 하는 것 외에도 Microsoft Windows 워크스테이션과 서버를 검색할 수 있습니다. 이러한 장치는 장치 인벤토리에도 표시 됩니다. 장치를 학습 한 후 다음과 같은 자세한 Windows 정보를 사용 하 여 장치 인벤토리를 보강할 수 있습니다.

- 설치 된 Windows 버전

- 설치 된 응용 프로그램

- 패치 수준 정보

- 포트 열기

- OS 버전에 대 한 보다 강력한 정보

이 정보를 검색 하는 데 사용할 수 있는 두 가지 옵션은 다음과 같습니다.

- 예약 된 WMI 검색을 사용 하는 활성 폴링. 

- 장치에서 스크립트를 배포 및 실행 하 여 로컬 조사 로컬 스크립트로 작업 하면 끝점에서 WMI 폴링을 실행 하는 위험을 무시 합니다. 폭포 및 단방향 요소를 사용 하 여 규제 된 네트워크에도 유용 합니다.

이 문서에서는 스크립트를 사용 하 여 Windows 끝점 레지스트리를 로컬에서 조사 하는 방법을 설명 합니다. 이 정보는 경고, 알림, 데이터 마이닝 보고서, 위험 평가 및 공격 벡터 보고서를 생성 하는 데 사용 됩니다.

:::image type="content" source="media/how-to-work-with-asset-inventory-information/data-mining-screen.png" alt-text="데이터 마이닝 스크린샷.":::

다음 Windows 운영 체제를 조사할 수 있습니다.

- Windows XP

- Windows 2000

- Windows NT

- Windows 7

- Windows 10

- Windows Server 2003/2008/2012/2016

### <a name="before-you-begin"></a>시작하기 전에

스크립트를 사용 하려면 다음 요구 사항을 충족 해야 합니다.

- 장치에서 스크립트를 실행 하려면 관리자 권한이 필요 합니다.

- 센서가 Windows 장치를 이미 학습 했어야 합니다. 즉, 장치가 이미 존재 하는 경우 스크립트에서 해당 정보를 검색 합니다.

- 센서는 Windows PC가 연결 된 네트워크를 모니터링 합니다.

### <a name="acquire-the-script"></a>스크립트 가져오기

스크립트를 수신 하려면 [고객 지원에 문의 하세요](mailto:support.microsoft.com).

### <a name="deploy-the-script"></a>스크립트 배포

스크립트를 한 번 배포 하거나 표준 자동화 된 배포 방법 및 도구를 사용 하 여 진행 중인 쿼리를 예약할 수 있습니다.

### <a name="about-the-script"></a>스크립트 정보

- 스크립트는 설치 된 프로그램이 아닌 유틸리티로 실행 됩니다. 스크립트를 실행 해도 끝점에는 영향을 주지 않습니다.

- 스크립트가 생성 하는 파일은 사용자가 삭제할 때까지 로컬 드라이브에 남아 있습니다.

- 스크립트가 생성 하는 파일은 서로 옆에 있습니다. 구분 하지 마십시오.

- 동일한 위치에서 스크립트를 다시 실행 하는 경우 이러한 파일을 덮어씁니다.

스크립트를 실행하려면  

1. 스크립트를 로컬 드라이브에 복사 하 고 압축을 풉니다. 다음 파일이 표시 됩니다.

    - start.bat

    - settings.js

    - 데이터. bin

    - run.bat

   :::image type="content" source="media/how-to-work-with-asset-inventory-information/files-in-file-explorer.png" alt-text="파일 탐색기에서 파일을 봅니다.":::

2. 파일을 실행 `run.bat` 합니다.

3. 레지스트리를 검색 한 후에는 레지스트리 정보와 함께 CX-스냅숏 파일이 표시 됩니다.

4. 파일 이름은 스냅숏의 시스템 이름 및 날짜와 시간을 나타냅니다. 예제 파일 이름은 `CX-snaphot_SystemName_Month_Year_Time` 입니다.

### <a name="import-device-details"></a>장치 세부 정보 가져오기

각 끝점에서 배운 정보를 센서로 가져와야 합니다.

쿼리에서 생성 된 파일을 센서에서 액세스할 수 있는 하나의 폴더에 배치할 수 있습니다. 표준, 자동화 된 메서드 및 도구를 사용 하 여 각 Windows 끝점에서 센서로 가져올 위치로 파일을 이동 합니다.

파일 이름을 업데이트 하지 않습니다.

가져오기:

1. **Windows 구성 가져오기** 대화 상자에서 **설정 가져오기** 를 선택 합니다.

   :::image type="content" source="media/how-to-work-with-asset-inventory-information/import-windows-configuration-v2.png" alt-text="Windows 구성을 가져옵니다.":::

2. **추가** 를 선택한 다음 모든 파일 (Ctrl + A)을 선택 합니다.

3. **닫기** 를 선택합니다. 장치 레지스트리 정보를 가져옵니다. 파일 중 하나를 업로드 하는 데 문제가 있는 경우 파일 업로드에 실패 한 것을 알 수 있습니다.

   :::image type="content" source="media/how-to-work-with-asset-inventory-information/add-new-file.png" alt-text="추가 된 파일을 업로드 했습니다.":::

## <a name="export-device-inventory-information"></a>장치 인벤토리 정보 내보내기

장치 인벤토리 정보를 Excel 파일로 내보낼 수 있습니다.

CSV 파일을 내보내려면:

- 장치 인벤토리의 오른쪽 상단 메뉴에서를 선택 :::image type="icon" source="media/how-to-work-with-asset-inventory-information/csv-excel-export-icon.png" border="false"::: 합니다. CSV 보고서가 생성 되어 다운로드 됩니다.

## <a name="see-also"></a>참고 항목

[디바이스 인벤토리에서 모든 엔터프라이즈 센서 감지 조사](how-to-investigate-all-enterprise-sensor-detections-in-a-device-inventory.md)

[사이트 맵 보기 작업](how-to-gain-insight-into-global-regional-and-local-threats.md#work-with-site-map-views)
