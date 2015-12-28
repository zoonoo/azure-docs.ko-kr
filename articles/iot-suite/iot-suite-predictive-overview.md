<properties
 pageTitle="예측 정비 사전 구성 솔루션 | Microsoft Azure"
 description="Azure IoT 예측 정비 사전 구성 솔루션에 대한 설명입니다."
 services=""
 documentationCenter=""
 authors="stevehob"
 manager="timlt"
 editor=""/>

<tags
 ms.service="na"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="12/01/2015"
 ms.author="stevehob"/>

# 예측 정비 사전 구성 솔루션 개요

*예측 정비* 사전 구성 솔루션은 [Microsoft Azure IoT Suite][lnk_iot_suite]의 일부로 릴리스되는 [사전 구성 솔루션][lnk_preconfigured_solutions] 중 하나입니다. 이 솔루션은 실시간 장치 원격 분석 컬렉션과 [Azure 기계 학습][lnk_machine_learning]을 사용하여 생성되는 예측 모델을 통합합니다.


엔터프라이즈는 Azure IoT Suite를 통해 신속하고 간편하게 연결하고, 자산을 모니터링하고, 실시간으로 데이터를 분석할 수 있습니다. 예측 정비 사전 구성 솔루션은 이러한 데이터와 함께 리치 대시보드와 시각화를 사용하여 효율을 증진하고 매출원을 향상시킬 수 있는 새로운 비즈니스 인텔리전스를 제공합니다.

## 시나리오

Fabrikam은 경쟁력 있는 가격으로 우수한 고객 경험을 제공하는 데 중점을 두고 있는 지역 항공사입니다. 항공기 지연의 이유 중 하나는 정비 문제이며 항공 엔진 정비는 특히 어려운 부분입니다. 비행 중 엔진 고장은 어떻게 해서든 막아야 하기 때문에 Fabrikam은 엔진을 정기적으로 조사하고 예정된 정비 프로그램을 충실하게 지키고 있습니다. 하지만 항공기 엔진이 항상 동일하게 마모되는 것은 아닙니다. 엔진에 대해 필요 이상의 정비를 수행하는 경우도 있습니다. 무엇보다, 정비가 수행될 때까지 항공기를 이륙할 수 없는 문제가 발생합니다. 이런 문제는 비용이 큰 지연을 유발하며, 적당한 기술자나 예비 부품이 없는 곳에 항공기가 있는 경우에는 특히 그렇습니다.

Fabrikam 항공기의 엔진에는 비행 중에 엔진 상태를 모니터링하는 센서가 달려 있습니다. Fabrikam은 Azure IoT Suite를 사용하여 비행 중에 수집된 센서 데이터를 수집합니다. Fabrikam의 데이터 과학자들은 엔진 작동 및 고장 데이터를 다년간 축적한 후에 항공기 엔진의 잔여 수명(Remaining Useful Life, RUL)을 예측하는 방식을 모델링했습니다. 이들이 확인한 것은 4개 엔진 센서의 데이터와 우발적인 고장으로 이어지는 엔진 마모 사이의 상관 관계입니다. Fabrikam은 안전을 보장하기 위하여 정기적인 정밀 검사를 계속 수행하는 한편, 이 모델을 통해 비행 중 엔진에서 수집한 원격 분석 데이터를 사용하여 비행이 끝날 때마다 각 엔진에 대한 RUL을 계산할 수 있게 되었습니다. Fabrikam은 이제 향후 고장 시점을 예측하고 정비 및 수리를 사전에 계획할 수 있습니다.

정비가 필요한 시점을 예측함으로써, Fabrikam은 비용을 줄이도록 운영을 최적화할 수 있습니다. 정비 코디네이터는 스케줄러를 통해 특정 위치에 정착하는 항공기와 동시간대에 정비 계획을 세우고, 일정에 차질을 주지 않으면서 항공기에 대해 충분한 정비 시간을 확보할 수 있도록 합니다. Fabrikam은 그에 맞게 기술자의 일정을 예약할 수 있고, 대기 시간 없이 항공기를 충분히 정비할 수 있도록 합니다. 재고 관리자는 정비 계획을 수신하기 때문에 주문 공정 및 예비 부품 재고를 최적화할 수 있습니다. 이 모든 것을 통하여 Fabrikam은 승객과 승무원의 안전을 보장하면서 항공기 지상 체류 시간을 최소화하고 운영비를 줄일 수 있습니다.

## 예측 정비 솔루션이 구축되는 방식

[Azure IoT Suite][lnk_iot_suite]가 고객에게 필요한 기능을 제공하는 방식을 이해하려면 예측 정비의 잠재력을 깨달을 필요가 있습니다. 이 내용은 [infographic][lnk_infographic]을 참조하세요.

IoT Suite 서비스를 통해 수집되는 장치 원격 분석 자료를 통해 작동하고 웹 응용프로그램으로 사용자에게 표면화되는 이러한 기능을 보여주기 위하여, 템플릿으로 제공되는 기존의 Microsoft Azure 기계 학습 모델이 솔루션에 활용됩니다. Microsoft는 [회귀 모델][lnk_regression_model]을 구축하고 전체 템플릿, 데이터<sup>[1]</sup>, 단계별 지침을 게시했습니다.

Azure IoT 예측 정비 사전 구성 솔루션은 이 템플릿으로부터 생성된 회귀 모델을 사용합니다. 템플릿은 사용자의 Azure 구독에 배포되어 있고 게시된 API를 통해 노출됩니다. 이 솔루션은 4개(총 100개 중)의 엔진을 나타내는 테스트 데이터의 하위 집합과, 학습된 모델을 통해 정확한 결과를 제공하는 4개(총 21개 중)의 센서 데이터 스트림을 포함합니다.

*[1] A. Saxena and K. Goebel(2008). "Turbofan Engine Degradation Simulation Data Set", NASA Ames Prognostics Data Repository (http://ti.arc.nasa.gov/tech/dash/pcoe/prognostic-data-repository/), NASA Ames Research Center, Moffett Field, CA*

## 다음 단계

Azure IoT가 예측 정비 시나리오를 가능하게 하는 방식에 대해 자세히 알아보려면, [Capture value from the Internet of Things][lnk_capture_value](영문)을 참조하세요.

예측 정비 사전 구성 솔루션을 [연습][lnk-predictive-walkthrough]합니다.

[lnk-predictive-walkthrough]: iot-suite-predictive-walkthrough.md
[lnk_preconfigured_solutions]: iot-suite-what-are-preconfigured-solutions.md
[lnk_iot_suite]: iot-suite-overview.md
[lnk_machine_learning]: https://azure.microsoft.com/services/machine-learning/
[lnk_infographic]: https://www.microsoft.com/ko-KR/server-cloud/predictivemaintenance/Index.html
[lnk_regression_model]: http://gallery.cortanaanalytics.com/Collection/Predictive-Maintenance-Template-3
[lnk_capture_value]: http://download.microsoft.com/download/0/7/D/07D394CE-185D-4B96-AC3C-9B61179F7080/Capture_value_from_the_Internet%20of%20Things_with_Predictive_Maintenance.PDF

<!---HONumber=AcomDC_1217_2015-->