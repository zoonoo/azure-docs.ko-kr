---
title: 토양 수분 히트맵 생성
description: Azure FarmBeats에서 토양 수분 히트맵을 생성하는 방법에 대해 설명합니다.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: a2115e9c1601c86cce8857c10baf12b91cc2b997
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75482570"
---
# <a name="generate-soil-moisture-heatmap"></a>토양 수분 히트맵 생성

토양 수분은 토양 입자 사이의 공간에 보관되는 물입니다.토양 수분 히트맵은 농장 내에서 모든 깊이와 고해상도에서 수분 데이터를 이해하는 데 도움이 됩니다. 정확하고 사용 가능한 토양 수분 히트맵을 생성하려면 동일한 공급자로부터 센서를 균일하게 배치해야 합니다. 다른 공급자는 보정의 차이와 함께 토양 수분을 측정하는 방식에 차이가있을 것이다. 히트맵은 해당 깊이에 배치된 센서를 사용하여 특정 깊이에 대해 생성됩니다.

이 문서에서는 Azure FarmBeats 가속기를 사용하여 팜에 대한 토양 수분 히트맵을 생성하는 프로세스에 대해 설명합니다. 이 아티클에서는 다음 방법을 설명합니다.

- [팜 만들기](#create-a-farm)
- [농장에 센서 할당](#get-soil-moisture-sensor-data-from-partner)
- [토양 수분 히트맵 생성](#generate-soil-moisture-heatmap)

## <a name="before-you-begin"></a>시작하기 전에

다음을 확인합니다.  

- Azure 구독
- Azure FarmBeats의 실행 중인 인스턴스입니다.
- 농장에는 최소 3개의 토양 수분 센서가 있습니다.

## <a name="create-a-farm"></a>팜 만들기

농장은 토양 수분 히트맵을 만들려는 지리적 영역입니다. 팜 API 또는 [FarmsBeats 가속기 UI에서](manage-farms-in-azure-farmbeats.md#create-farms) [팜을](https://aka.ms/FarmBeatsDatahubSwagger) 만들 수 있습니다.

## <a name="deploy-sensors"></a>센서 배포

농장에 토양 수분 센서를 물리적으로 배치해야 합니다. [데이비스 인스트루먼트와](https://www.davisinstruments.com/product/enviromonitor-gateway/) [테라리틱](https://teralytic.com/)- 당신은 우리의 승인 된 파트너중 하나에서 토양 수분 센서를 구입할 수 있습니다. 팜에서 실제 설정을 수행하려면 센서 공급자와 협력해야 합니다.

## <a name="get-soil-moisture-sensor-data-from-partner"></a>파트너로부터 토양 수분 센서 데이터 얻기

센서가 스트리밍을 시작하면 파트너 데이터 대시보드로 데이터가 Azure FarmBeats로 데이터를 사용하도록 설정됩니다. 이 작업은 파트너 응용 프로그램에서 수행할 수 있습니다.

예를 들어 Davis 센서를 구입한 경우 날씨 링크 계정에 로그인하고 Azure FarmBeats로 데이터 스트리밍을 활성화하는 데 필요한 자격 증명을 제공합니다. 필요한 자격 증명을 얻으려면 센서 [데이터 받기의](get-sensor-data-from-sensor-partner.md#get-sensor-data-from-sensor-partners)지침을 따르십시오.

자격 증명을 입력하고 파트너 응용 프로그램에서 **제출을** 선택하면 Azure FarmBeats로 데이터가 흐를 수 있습니다.

### <a name="assign-soil-moisture-sensors-to-the-farm"></a>농장에 토양 수분 센서 지정

센서 계정을 Azure FarmBeats에 연결한 후에는 관심 있는 팜에 토양 수분 센서를 할당해야 합니다.

1.  홈 페이지에서 메뉴에서 **팜을** 선택하면 **팜** 목록 페이지가 표시됩니다.
2.  **MyFarm** > **장치 추가 를**선택합니다.
3.  **장치 추가** 창이 표시됩니다. 농장의 토양 수분 센서에 연결된 장치를 선택합니다.

    ![Farm Beats 프로젝트](./media/get-sensor-data-from-sensor-partner/add-devices-1.png)

4. **장치 추가를**선택합니다.     

## <a name="generate-soil-moisture-heatmap"></a>토양 수분 히트맵 생성

이 단계는 농장에 대한 토양 수분 히트맵을 생성하는 작업 또는 장기 실행 작업을 만드는 것입니다.

1.  홈 페이지에서 왼쪽 탐색 메뉴에서 **팜으로** 이동하여 팜 페이지를 봅니다.
2.  **내 팜을**선택합니다.
3.  팜 **세부 정보** 페이지에서 **정밀도 맵 생성을**선택합니다.
4.  드롭다운 메뉴에서 토양 **수분을**선택합니다.
5.  토양 **수분** 창에서 **이번 주를**선택합니다.
6.  토양 **수분** 센서 **측정**선택에서 맵에 사용할 측정값을 입력합니다.
    센서 측정값을 찾으려면 **센서에서**토양 수분 센서를 선택합니다. **센서 속성에서** **이름 측정** 값을 사용합니다.

    ![Farm Beats 프로젝트](./media/get-sensor-data-from-sensor-partner/soil-moisture-1.png)


7.  **맵 생성을**선택합니다.
    작업 세부 정보가 있는 확인 메시지가 표시됩니다. 자세한 내용은 작업의 작업 상태를 참조하십시오.

    >[!NOTE]
    > 작업을 완료하는 데 약 3~4시간이 걸립니다.

### <a name="download-the-soil-moisture-heatmap"></a>토양 수분 히트맵 다운로드

다음 단계를 사용합니다.

1. **작업** 페이지에서 마지막 프로시저에서 만든 **작업의 작업 상태를** 확인합니다.
2. 작업 상태가 **성공됨으로**표시되면 메뉴에서 **지도를** 선택합니다.
3. 토양 moisture_MyFarm_YYYY MM-DD> <형식으로 생성 된 날까지지도를 검색합니다.
4. **이름** 열에서 맵을 선택하면 선택한 맵의 미리 보기와 함께 팝업 창이 표시됩니다.
5. **다운로드**를 선택합니다. 맵이 다운로드되어 컴퓨터의 로컬 폴더에 저장됩니다.

    ![Farm Beats 프로젝트](./media/get-sensor-data-from-sensor-partner/download-soil-moisture-map-1.png)

## <a name="next-steps"></a>다음 단계

토양 수분 히트맵을 성공적으로 생성했으므로 [센서 배치를 생성하고](generate-maps-in-azure-farmbeats.md#sensor-placement-map) [기록 원격 분석 데이터를 수집하는](ingest-historical-telemetry-data-in-azure-farmbeats.md)방법을 알아봅니다. 
