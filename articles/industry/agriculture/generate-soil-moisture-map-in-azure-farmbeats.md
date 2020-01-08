---
title: 토양 습기 열 지도 생성
description: Azure FarmBeats에서 토양 습기 열 지도을 생성 하는 방법을 설명 합니다.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: a2115e9c1601c86cce8857c10baf12b91cc2b997
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75482570"
---
# <a name="generate-soil-moisture-heatmap"></a>토양 습기 열 지도 생성

토양 습기는 토양 파티클 사이에 있는 공간에 유지 되는 물입니다. 토양 습기 열 지도는 모든 깊이에서 습기 데이터를 이해 하 고 팜 내에서 고해상도를 이해 하는 데 도움이 됩니다. 정확 하 고 사용 가능한 토양 습기 열 지도를 생성 하려면 동일한 공급자에서 센서를 균일 하 게 배포 해야 합니다. 토양 습기가 보정의 차이로 인해 측정 되는 방식에 따라 공급자는 차이가 있습니다. 열 지도는 해당 깊이에서 배포 된 센서를 사용 하 여 특정 깊이에 대해 생성 됩니다.

이 문서에서는 Azure FarmBeats Accelerator를 사용 하 여 팜에 대 한 토양 습기 열 지도를 생성 하는 프로세스를 설명 합니다. 이 아티클에서는 다음 방법을 설명합니다.

- [팜 만들기](#create-a-farm)
- [팜에 센서 할당](#get-soil-moisture-sensor-data-from-partner)
- [토양 습기 열 지도 생성](#generate-soil-moisture-heatmap)

## <a name="before-you-begin"></a>시작하기 전에

ID 열의 값이 ID 열의  

- Azure 구독
- Azure FarmBeats의 실행 중인 인스턴스입니다.
- 팜에 토양 습기 센서를 세 개 이상 사용할 수 있습니다.

## <a name="create-a-farm"></a>팜 만들기

팜은 토양 습기 열 지도를 만드는 데 필요한 지리적 영역입니다. 팜 [API](https://aka.ms/FarmBeatsDatahubSwagger) 또는 [FarmsBeats Accelerator UI](manage-farms-in-azure-farmbeats.md#create-farms) 를 사용 하 여 팜을 만들 수 있습니다.

## <a name="deploy-sensors"></a>센서 배포

팜에 토양 습기 센서를 물리적으로 배포 해야 합니다. 승인 된 파트너- [Davis 악기](https://www.davisinstruments.com/product/enviromonitor-gateway/) 및 [Teralytic](https://teralytic.com/)에서 토양 습기 센서를 구입할 수 있습니다. 팜에서 물리적 설정을 수행 하려면 센서 공급자와 조정 해야 합니다.

## <a name="get-soil-moisture-sensor-data-from-partner"></a>파트너의 토양 습기 센서 데이터 가져오기

센서는 데이터를 파트너 데이터 대시보드로 시작 하므로 데이터를 Azure FarmBeats로 사용할 수 있습니다. 파트너 응용 프로그램에서이 작업을 수행할 수 있습니다.

예를 들어 Davis 센서를 구매한 경우 날씨 링크 계정에 로그인 하 여 Azure FarmBeats로 데이터 스트리밍을 사용 하도록 설정 하는 데 필요한 자격 증명을 제공 합니다. 필요한 자격 증명을 가져오려면 [센서 데이터 가져오기](get-sensor-data-from-sensor-partner.md#get-sensor-data-from-sensor-partners)의 지침을 따르세요.

자격 증명을 입력 하 고 파트너 응용 프로그램에서 **제출** 을 선택 하면 데이터를 Azure FarmBeats로 이동할 수 있습니다.

### <a name="assign-soil-moisture-sensors-to-the-farm"></a>팜에 토양 습기 센서 할당

Azure FarmBeats에 센서 계정을 연결한 후에는 관심 있는 팜에 토양 습기 센서를 할당 해야 합니다.

1.  홈 페이지의 메뉴에서 **팜** 을 선택 합니다. **팜** 목록 페이지가 표시 됩니다.
2.  **Myfarm** > **장치 추가**를 선택 합니다.
3.  **장치 추가** 창이 표시 됩니다. 팜의 토양 습기 센서에 연결 된 장치를 선택 합니다.

    ![프로젝트 팜 비트](./media/get-sensor-data-from-sensor-partner/add-devices-1.png)

4. **장치 추가**를 선택 합니다.     

## <a name="generate-soil-moisture-heatmap"></a>토양 습기 열 지도 생성

이 단계에서는 팜에 대해 토양 습기 열 지도를 생성할 작업 또는 장기 실행 작업을 만듭니다.

1.  홈 페이지에서 왼쪽 탐색 메뉴의 **팜** 페이지로 이동 하 여 팜 페이지를 표시 합니다.
2.  **Myfarm**를 선택 합니다.
3.  **팜 세부 정보** 페이지에서 **전체 자릿수 맵 생성**을 선택 합니다.
4.  드롭다운 메뉴에서 **토양 습기**를 선택 합니다.
5.  **토양 습기** 창에서 **이번 주**를 선택 합니다.
6.  **Select 토양 습기** **센서 측정값**에서 지도에 사용할 측정값을 입력 합니다.
    센서 측정값을 찾으려면 **센서**에서 any 토양 습기 센서를 선택 합니다. **센서 속성**에서 **Measure Name** 값을 사용 합니다.

    ![프로젝트 팜 비트](./media/get-sensor-data-from-sensor-partner/soil-moisture-1.png)


7.  **맵 생성**을 선택 합니다.
    작업 정보를 포함 하는 확인 메시지가 표시 됩니다. 자세한 내용은 작업의 작업 상태를 참조 하세요.

    >[!NOTE]
    > 작업을 완료 하는 데 약 3 ~ 4 시간이 걸립니다.

### <a name="download-the-soil-moisture-heatmap"></a>토양 습기 열 지도 다운로드

다음 단계를 사용합니다.

1. **작업** 페이지에서 마지막 절차에서 만든 작업의 **작업 상태** 를 확인 합니다.
2. 작업 상태가 **성공**으로 표시 되 면 메뉴에서 **맵** 을 선택 합니다.
3. 토양 moisture_MyFarm_YYYY-MM-DD > < 형식으로 작성 된 날짜를 기준으로 맵을 검색 합니다.
4. **이름** 열에서 지도를 선택 하면 선택한 지도의 미리 보기가 표시 된 팝업 창이 표시 됩니다.
5. **다운로드**를 선택합니다. 맵이 다운로드 되어 컴퓨터의 로컬 폴더에 저장 됩니다.

    ![프로젝트 팜 비트](./media/get-sensor-data-from-sensor-partner/download-soil-moisture-map-1.png)

## <a name="next-steps"></a>다음 단계

토양 습기 열 지도를 성공적으로 생성 했으므로 [센서 배치를 생성](generate-maps-in-azure-farmbeats.md#sensor-placement-map) 하 고 [기록 된 원격 분석 데이터를 수집](ingest-historical-telemetry-data-in-azure-farmbeats.md)하는 방법을 알아보세요. 
