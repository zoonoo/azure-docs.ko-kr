---
title: 토양 습기 열 지도 생성
description: Azure FarmBeats에서 토양 습기 열 지도를 생성하는 방법을 설명합니다.
author: RiyazPishori
ms.topic: article
ms.date: 11/04/2019
ms.author: riyazp
ms.openlocfilehash: 4b796af22604a2f09482bcb9a321159ebaafea11
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2021
ms.locfileid: "108756776"
---
# <a name="generate-soil-moisture-heatmap"></a>토양 습기 열 지도 생성

토양 습기는 토양 파티클 사이에 있는 공간에 유지되는 물입니다.토양 습기 열 지도는 팜 내 모든 깊이에서 고해상도로 습기 데이터를 이해하는 데 도움이 됩니다. 정확하고 사용 가능한 토양 습기 열 지도를 생성하려면 동일한 공급자의 센서를 균일하게 배포해야 합니다. 공급자마다 보정의 차이와 함께 토양 습기가 측정되는 방식에 차이가 있습니다. 열 지도는 특정 깊이에 배포된 센서를 사용하여 해당 깊이에 대해 생성됩니다.

이 문서에서는 Azure FarmBeats Accelerator를 사용하여 팜의 토양 습기 열 지도를 생성하는 프로세스를 설명합니다. 이 아티클에서는 다음 방법을 설명합니다.

- [팜 만들기](#create-a-farm)
- [팜에 센서 할당](#get-soil-moisture-sensor-data-from-partner)
- [토양 습기 열 지도 생성](#generate-soil-moisture-heatmap)

## <a name="before-you-begin"></a>시작하기 전에

다음을 확인합니다.  

- Azure 구독
- Azure FarmBeats의 실행 중인 인스턴스입니다.
- 팜에 토양 습기 센서를 세 개 이상 사용할 수 있습니다.

## <a name="create-a-farm"></a>팜 만들기

팜은 토양 습기 열 지도를 만드는 데 필요한 지리적 영역입니다. [Farms API](https://aka.ms/FarmBeatsDatahubSwagger) 또는 [FarmsBeats Accelerator UI](manage-farms-in-azure-farmbeats.md#create-farms)를 사용하여 팜을 만들 수 있습니다.

## <a name="deploy-sensors"></a>센서 배포

팜에 토양 습기 센서를 물리적으로 배포해야 합니다. 승인된 파트너인 [Davis Instruments](https://www.davisinstruments.com/product/enviromonitor-gateway/) 및 [Teralytic](https://teralytic.com/)에서 토양 습기 센서를 구입할 수 있습니다. 팜에서 물리적 설정을 수행하려면 센서 공급자와 조정해야 합니다.

## <a name="get-soil-moisture-sensor-data-from-partner"></a>파트너로부터 토양 습기 센서 데이터 가져오기

센서가 데이터를 파트너 데이터 대시보드로 스트리밍하기 시작하면 데이터가 Azure FarmBeats로 전송됩니다. 이는 파트너 애플리케이션에서 수행할 수 있습니다.

예를 들어, Davis 센서를 구입한 경우 날씨 링크 계정에 로그인하고 Azure FarmBeats로 데이터를 스트리밍하는 데 필요한 자격 증명을 제공합니다. 필요한 자격 증명을 얻으려면 [센서 데이터 가져오기](get-sensor-data-from-sensor-partner.md#get-sensor-data-from-sensor-partners)의 지침을 따르세요.

자격 증명을 입력하고 파트너 애플리케이션에서 **제출** 을 선택하면 데이터가 Azure FarmBeats로 흐를 수 있습니다.

### <a name="assign-soil-moisture-sensors-to-the-farm"></a>팜에 토양 습기 센서 할당

센서 계정을 Azure FarmBeats에 연결한 후에는 관심 있는 팜에 토양 습기 센서를 할당해야 합니다.

1.  홈페이지의 메뉴에서 **팜** 을 선택하면 **팜** 목록 페이지가 표시됩니다.
2.  **MyFarm** > **디바이스 추가** 를 선택합니다.
3.  **디바이스 추가** 창이 표시됩니다. 팜의 토양 습기 센서에 연결된 디바이스를 선택합니다.

    ![디바이스 추가 화면을 보여주는 스크린샷](./media/get-sensor-data-from-sensor-partner/add-devices-1.png)

4. **디바이스 추가** 를 선택합니다.     

## <a name="generate-soil-moisture-heatmap"></a>토양 습기 열 지도 생성

이 단계는 팜에 대한 토양 습기 토양 습기 열 지도를 생성하는 작업 또는 장기 실행 작업을 만드는 것입니다.

1.  홈페이지의 왼쪽 탐색 메뉴에서 **팜** 으로 이동하여 팜 페이지를 표시합니다.
2.  **MyFarm** 을 선택합니다.
3.  **팜 세부 정보** 페이지에서 **정밀도 지도 생성** 을 선택합니다.
4.  드롭다운 메뉴에서 **토양 습기** 를 선택합니다.
5.  **토양 습기** 창에서 **이번 주** 를 선택합니다.
6.  **토양 습기** **센서 측정값 선택** 에서 지도에 사용할 측정값을 입력합니다.
    센서 측정값을 찾으려면 **센서** 에서 토양 습기 센서를 선택합니다. **센서 속성** 에서 **측정값 이름** 값을 사용합니다.

    ![토양 습기 화면을 보여주는 스크린샷](./media/get-sensor-data-from-sensor-partner/soil-moisture-1.png)


7.  **지도 생성** 을 선택합니다.
    작업 세부 정보가 포함된 확인 메시지가 표시됩니다. 자세한 내용은 작업의 작업 상태를 참조하세요.

    >[!NOTE]
    > 작업을 완료하는 데 약 3~4시간이 걸립니다.

### <a name="download-the-soil-moisture-heatmap"></a>토양 습기 열 지도 다운로드

다음 단계를 사용합니다.

1. **작업** 페이지에서 마지막 절차에서 만든 작업의 **작업 상태** 를 확인합니다.
2. 작업 상태가 **성공** 으로 표시되면 메뉴에서 **지도** 를 선택합니다.
3. 날짜를 기준으로 <soil-moisture_MyFarm_YYYY-MM-DD> 형식으로 생성된 지도를 검색합니다.
4. **이름** 열에서 지도를 선택하면 선택한 지도의 미리 보기가 있는 팝업 창이 표시됩니다.
5. **다운로드** 를 선택합니다. 지도가 다운로드되어 컴퓨터의 로컬 폴더에 저장됩니다.

    ![Farm Beats 프로젝트](./media/get-sensor-data-from-sensor-partner/download-soil-moisture-map-1.png)

## <a name="next-steps"></a>다음 단계

토양 습기 열 지도를 성공적으로 생성했으므로 [센서 배치를 생성](generate-maps-in-azure-farmbeats.md#sensor-placement-map)하하고 [기록된 원격 분석 데이터를 수집](ingest-historical-telemetry-data-in-azure-farmbeats.md)하는 방법을 알아보세요. 
