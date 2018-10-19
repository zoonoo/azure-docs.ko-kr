---
title: Azure IoT Central 응용 프로그램에서 장치 데이터 분석 | Microsoft Docs
description: Azure IoT Central 응용 프로그램에서 장치 데이터를 분석하는 방법을 설명합니다.
author: lmasieri
ms.author: lmasieri
ms.date: 09/18/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: bf86e769aff4a9b03d5df1b1aef702814c605fa4
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/19/2018
ms.locfileid: "46368089"
---
# <a name="how-to-use-analytics-to-analyze-your-device-data"></a>분석을 사용하여 장치 데이터를 분석하는 방법


*이 문서는 운영자, 빌더 및 관리자에게 적용됩니다.*


Microsoft Azure IoT Central은 장치에서 오는 대량의 데이터를 이해하는 풍부한 분석 기능을 제공합니다. 시작하려면 왼쪽 탐색 메뉴에서 **분석**으로 이동합니다. 

  ![IoT Central에서 분석으로 이동](media\howto-create-analytics\analytics-navigation.png)

## <a name="querying-your-data"></a>데이터 쿼리

**장치 집합**을 선택하고, **필터**(선택 사항)를 추가하고, 시작할 **기간**을 선택해야 합니다. 완료되면 *결과 표시*를 클릭하여 데이터 시각화를 시작합니다.


* **장치 집합:** [장치 집합](howto-use-device-sets.md)은 장치의 사용자 정의 장치 그룹입니다. 예를 들어 오클랜드의 모든 냉장고 또는 모든 rev 2.0 풍력 발전용 터빈이 있습니다.

<!---
to-do: confirm if 10 is the max number of filters
to-do: do we need to explain how fiters work?
--->

* **필터:** 검색에 필터를 선택적으로 추가하여 데이터를 효과적으로 활용할 수 있습니다. 한 번에 최대 10개의 필터를 추가할 수 있습니다. 예를 들어 오클랜드의 모든 냉장고 내에서 60도가 넘는 온도의 냉장고를 찾습니다. 
* **기간:** 기본적으로 지난 10분 동안의 데이터를 검색합니다. 이 값을 미리 정의된 시간 범위 중 하나로 변경하거나 사용자 지정 기간을 선택할 수 있습니다. 

 ![분석 쿼리](media\howto-create-analytics\analytics-query.png)

## <a name="visualizing-your-data"></a>데이터 시각화

데이터를 쿼리하면 시각화를 시작할 수 있습니다. 단위를 표시하거나 숨기고, 데이터 집계 방식을 변경하고, 다른 장치 속성을 사용하여 데이터를 추가로 분할할 수 있습니다.  

* **분할 기준:** 장치 속성에 따라 데이터를 분할하면 데이터를 자세히 드릴다운할 수 있습니다. 예를 들어 장치 ID 또는 위치별로 결과를 분할할 수 있습니다.
<!---
to-do: confirm if 10 is the max number of measurements
--->
* **단위:** 장치에서 한 번에 최대 10개의 서로 다른 원격 분석 항목을 표시하거나 숨기도록 선택할 수 있습니다. 단위는 온도, 습도 등입니다. 
* **집계:** 데이터는 기본적으로 평균으로 집계하지만, 데이터 집계를 요구 사항에 맞게 다른 항목으로 변경하도록 선택할 수 있습니다. 

   ![분석 시각화](media\howto-create-analytics\analytics-visualize.png) <br/><br/>
   ![분석 시각화 분할 기준](media\howto-create-analytics\analytics-splitby.png)

## <a name="interacting-with-your-data"></a>데이터와의 상호 작용

시각화 요구 사항에 맞게 쿼리 결과를 추가로 변경할 수 있는 다양한 방법이 있습니다. 그래프 보기와 그리드 보기 사이를 교대로 전환하고, 확대/축소하고, 데이터 집합을 새로 고치고, 선이 표시되는 방식을 변경할 수 있습니다.

* **그리드 표시:** 각 데이터 요소에 대한 특정 값을 볼 수 있도록 결과를 표 형식으로 사용할 수 있습니다. 이 보기는 내게 필요한 옵션 표준을 충족합니다. 
* **차트 표시:** 상향/하향 추세 및 이상 현상을 쉽게 파악할 수 있도록 결과를 선 형식으로 표시합니다. 

 ![분석에 대한 그리드 보기 표시](media\howto-create-analytics\analytics-showgrid.png)

확대/축소를 사용하면 데이터를 효과적으로 활용할 수 있습니다. 결과 집합 내에서 집중하려는 기간을 찾은 경우 커서를 사용하여 확대할 영역을 잡고, 사용 가능한 컨트롤을 사용하여 다음 작업 중 하나를 수행합니다.
* **확대:** 기간을 선택하면 확대/축소가 활성화되어 데이터를 확대할 수 있습니다.
* **축소:** 이 컨트롤을 사용하면 마지막 확대/축소에서 한 수준을 축소할 수 있습니다. 예를 들어 데이터를 세 번 확대한 경우 축소는 한 번에 한 단계씩 뒤로 이동합니다.
* **확대/축소 다시 설정:** 다양한 확대/축소 수준을 수행한 경우 확대/축소 다시 설정 컨트롤을 사용하여 원래 결과 집합으로 돌아갈 수 있습니다. 

 ![데이터 확대/축소 수행](media\howto-create-analytics\analytics-zoom.png)


선 스타일은 요구 사항에 맞게 변경할 수 있습니다. 선택할 수 있는 네 가지 옵션은 다음과 같습니다.
* **선형** 각 데이터 점 사이에 직선이 그려집니다. 
* **부드럽게:** 각 점 사이에 곡선이 그려집니다.
* **계단식:** 차트의 각 점 사이의 선에서 단계 차트를 만듭니다.
* **분산형:** 모든 점이 차트에 선으로 연결되지 않은 채 그려집니다. 

 ![분석에서 사용할 수 있는 다양한 선 유형](media\howto-create-analytics\analytics-linetypes.png)

마지막으로, 다음 세 가지 모드 중 하나를 선택하여 Y-축을 따라 데이터를 정렬할 수 있습니다.

* **누적형:** 모든 측정값의 그래프가 누적되고 그래프마다 고유의 Y축이 있습니다. 누적된 그래프는 측정값을 여러 개 선택했는데 각 측정값을 뚜렷하게 보고 싶은 경우에 유용합니다.
* **비 누적형:** 모든 측정값의 그래프가 한 Y축을 기준으로 표시되지만, Y축의 값은 강조 표시된 측정값에 따라 변합니다. 비 누적형 그래프는 여러 측정값을 오버레이하여 동일한 시간 범위의 측정값 패턴을 확인하려는 경우에 유용 합니다.
* **Y축 공유:** 모든 그래프가 동일한 Y축을 공유하고 축 값이 변하지 않습니다. Y축 공유 그래프는 단일 측정값을 살펴보고 데이터를 기준에 따라 조각화하려는 경우에 유용합니다.

 ![다양한 시각화 모드를 사용하여 y-축을 따라 데이터 정렬](media\howto-create-analytics\analytics-yaxis.png)

## <a name="next-steps"></a>다음 단계

Azure IoT Central 응용 프로그램에 대한 사용자 지정 분석을 만드는 방법을 알아보았으니, 다음과 같은 후속 단계를 진행하시기 바랍니다.

> [!div class="nextstepaction"]
> [Node.js 응용 프로그램 준비 및 연결](howto-connect-nodejs.md)