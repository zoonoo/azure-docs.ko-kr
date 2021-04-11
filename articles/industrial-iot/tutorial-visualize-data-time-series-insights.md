---
title: Azure Time Series Insights에서 OPC UA 데이터 시각화
description: 이 자습서에서 Time Series Insights를 사용하여 데이터를 시각화하는 방법을 알아봅니다.
author: jehona-m
ms.author: jemorina
ms.service: industrial-iot
ms.topic: tutorial
ms.date: 3/22/2021
ms.openlocfilehash: 5bd218c0d94922b6137a964e3993f516216ca4b7
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104787353"
---
# <a name="tutorial-visualize-data-with-time-series-insights-tsi"></a>자습서: TSI(Time Series Insights)를 사용하여 데이터 시각화

OPC 게시자 모듈은 OPC UA 서버에 연결하고, 이러한 서버의 데이터를 IoT Hub에 게시합니다. 산업용 IoT 플랫폼의 원격 분석 프로세서는 이러한 이벤트를 처리하고 컨텍스트화된 샘플을 TSI 및 기타 소비자에게 전달합니다.  

이 방법 가이드에서는 이 Time Series Insights 환경을 사용하여 OPC UA 원격 분석을 시각화하고 분석하는 방법을 보여 줍니다.

이 자습서에서는 다음과 같은 작업을 수행하는 방법을 살펴봅니다.

> [!div class="checklist"]
> * 모든 자습서에는 완료 단계를 요약한 목록이 포함되어 있습니다.
> * 이러한 각 글머리 기호가 H2 키에 맞춰 정렬됩니다.
> * 자습서에서 이러한 녹색 확인란을 사용합니다.

## <a name="prerequisite"></a>필수 조건

* IIoT 플랫폼을 배포하여 Time Series Insights 환경을 자동으로 만듭니다.
* 데이터가 IoT Hub에 게시됩니다.

## <a name="time-series-insights-explorer"></a>Time Series Insights 탐색기

Time Series Insights 탐색기는 원격 분석을 시각화하는 데 사용할 수 있는 웹앱입니다. 애플리케이션의 URL을 검색하려면 배포 결과로 저장된 `.env` 파일을 엽니다.  `PCS_TSI_URL` 변수의 URL에 대한 브라우저를 엽니다.  

Time Series Insights 탐색기를 사용하기 전에 데이터를 시각화할 수 있는 사용자에게 TSI 데이터에 대한 액세스 권한을 부여해야 합니다. 새 배포에서는 기본적으로 데이터 액세스 정책이 설정되지 않으므로 아무도 데이터를 볼 수 없습니다. 데이터 액세스 정책은 Azure Portal의 IIoT 플랫폼 배포 리소스 그룹에 배포된 Time Series Insights 환경에서 다음과 같이 설정해야 합니다.

   ![Time Series Insights 탐색기 1](media/tutorial-iiot-visualize-data-tsi/tutorial-time-series-insights-data-access-1.png)

데이터 액세스 정책을 선택합니다.

   ![Time Series Insights 탐색기 2](media/tutorial-iiot-visualize-data-tsi/tutorial-time-series-insights-data-access-2.png)

필요한 사용자를 할당합니다.

   ![Time Series Insights 탐색기 3](media/tutorial-iiot-visualize-data-tsi/tutorial-time-series-insights-data-access-3.png)


TSI 탐색기에서 할당되지 않은 시계열 인스턴스를 확인하세요. TSI 인스턴스는 OPC 서버의 게시된 노드에서 시작된 특정 데이터 포인트에 대한 시간/값 계열에 해당합니다. TSI 인스턴스(각각 OPC UA 데이터 포인트)는 EndpointId, SubscriptionId 및 NodeId로 고유하게 식별됩니다. TSI 인스턴스 모델은 IIoT 플랫폼 원격 분석 프로세서의 이벤트 허브에서 수집된 원격 분석 데이터를 기반으로 하여 자동으로 검색되어 탐색기에 표시됩니다.

   ![Time Series Insights 탐색기 4](media/tutorial-iiot-visualize-data-tsi/tutorial-time-series-insights-step-0.png)

원격 분석 데이터는 마우스 오른쪽 단추로 TSI 인스턴스를 클릭하고 값을 선택하여 차트에서 시각화할 수 있습니다. 차트에서 사용할 시간 프레임은 오른쪽 위 모서리에서 조정할 수 있습니다. 여러 인스턴스의 값을 동시에 선택하여 시각화 할 수 있습니다.

자세한 내용은 [빠른 시작: Azure Time Series Insights 미리 보기 살펴보기](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-quickstart)를 참조하세요.

## <a name="define-and-apply-a-new-model"></a>새 모델 정의 및 적용

원격 분석 인스턴스는 이제 원시 형식이므로 적절하게 컨텍스트화되어야 합니다. 

TSI 모델에 대한 자세한 내용은 [Azure Time Series Insights 미리 보기의 시계열 모델](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-tsm)을 참조하세요.

1. 1단계 - 탐색기의 모델 탭에서 수집된 원격 분석 데이터에 대한 새 계층 구조를 정의합니다. 계층 구조는 사용자가 TSI 인스턴스를 통해 더 직관적인 탐색에 필요한 메타 정보를 삽입할 수 있도록 하는 논리적 트리 구조입니다. 사용자는 나중에 다양한 TSI 인스턴스에 대해 인스턴스화할 수 있는 계층 구조 템플릿을 만들거나 삭제하거나 수정할 수 있습니다.

   ![1단계](media/tutorial-iiot-visualize-data-tsi/tutorial-time-series-insights-step-1.png)

2. 2단계 - 값에 대한 새 형식을 정의합니다. 이 예에서는 숫자 데이터 형식만 처리합니다.

   ![2단계](media/tutorial-iiot-visualize-data-tsi/tutorial-time-series-insights-step-2.png)

3. 3단계 - 이전에 정의된 계층 구조로 범주화해야 하는 새 TSI 인스턴스를 선택합니다.

   ![3단계](media/tutorial-iiot-visualize-data-tsi/tutorial-time-series-insights-step-3.png)

4. 4단계 - 논리적 구조와 일치하도록 인스턴스 속성(이름, 설명, 데이터 값) 및 계층 구조 필드를 채웁니다. 

   ![4단계](media/tutorial-iiot-visualize-data-tsi/tutorial-time-series-insights-step-4.png)

5. 5단계 - 범주화되지 않은 모든 TSI 인스턴스에 대해 5단계를 반복합니다.

   ![5단계](media/tutorial-iiot-visualize-data-tsi/tutorial-time-series-insights-step-5.png)

6. 6단계 - TSI 탐색기의 기본 페이지로 돌아가서 범주화된 인스턴스 계층 구조를 살펴보고, 분석할 데이터 포인트에 대한 값을 선택합니다.

   ![6단계](media/tutorial-iiot-visualize-data-tsi/tutorial-time-series-insights-step-6.png)

## <a name="connect-time-series-insights-to-power-bi"></a>Time Series Insights를 Power BI에 연결

Time Series Insights 환경을 Power BI에 연결할 수도 있습니다.  자세한 내용은 [TSI를 Power BI에 연결하는 방법](https://docs.microsoft.com/azure/time-series-insights/how-to-connect-power-bi) 및 [ Power BI에서 TSI의 데이터 시각화](https://docs.microsoft.com/azure/time-series-insights/concepts-power-bi)를 참조하세요.


## <a name="next-steps"></a>다음 단계
이제 TSI에서 데이터를 시각화하는 방법을 알아보았으므로 산업용 IoT GitHub 리포지토리를 확인할 수 있습니다.

> [!div class="nextstepaction"]
> [IIoT 플랫폼 GitHub 리포지토리](https://github.com/Azure/iot-edge-opc-publisher)