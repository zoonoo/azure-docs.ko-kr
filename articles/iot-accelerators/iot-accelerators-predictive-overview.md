---
title: 예측 유지 관리 솔루션 가속기 개요 - Azure | Microsoft Docs
description: Azure 예측 유지 관리 솔루션 가속기에 대한 설명
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 11/14/2017
ms.author: dobett
ms.openlocfilehash: 064c94c420b1ca9e45be37024e9b2b73135e1242
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/25/2018
ms.locfileid: "36938352"
---
# <a name="predictive-maintenance-solution-accelerator-overview"></a>예측 유지 관리 솔루션 가속기 개요

*예측 유지 관리* [솔루션 가속기][lnk_preconfigured_solutions]는 [Microsoft Azure IoT 솔루션 가속기][lnk_iot_suite] 솔루션 가속기 중 하나입니다. 이 솔루션은 실시간 장치 원격 분석 컬렉션과 [Azure Machine Learning][lnk-machine-learning]을 사용하여 생성되는 예측 모델을 통합합니다.

사용자는 Azure IoT 솔루션 가속기를 통해 신속하고 간편하게 연결되고 자산을 모니터링하며 실시간으로 원격 분석 데이터를 대시보드 및 시각적 개체로 분석할 수 있습니다. 예측 유지 관리 솔루션 가속기에서 대시보드와 시각화는 효율을 증진하고 매출원을 향상시킬 수 있는 새로운 인텔리전스를 제공합니다.

## <a name="the-scenario"></a>시나리오

Fabrikam은 경쟁력 있는 가격으로 우수한 고객 경험을 제공하는 데 중점을 두고 있는 지역 항공사입니다. 항공기 지연의 이유 중 하나는 정비 문제이며 항공 엔진 정비는 특히 어려운 부분입니다. Fabrikam은 비행 중 엔진 고장은 어떻게 해서든 막아야 하기 때문에 엔진을 정기적으로 조사하고 계획에 따라 유지 관리 일정을 예약합니다. 하지만 항공기 엔진이 항상 동일하게 마모되는 것은 아닙니다. 엔진에 대해 필요 이상의 정비를 수행하는 경우도 있습니다. 무엇보다, 정비가 수행될 때까지 항공기를 이륙할 수 없는 문제가 발생합니다. 적당한 기술자나 예비 부품이 없는 곳에 항공기가 있는 경우에는 특히 비용이 많이 드는 문제를 유발할 수 있습니다.

Fabrikam 항공기의 엔진에는 비행 중에 엔진 상태를 모니터링하는 센서가 달려 있습니다. Fabrikam은 비행 중 수집된 센서 데이터를 수집하는 예측 유지 관리 솔루션 가속기를 사용합니다. Fabrikam의 데이터 과학자들은 엔진 작동 및 고장 데이터를 다년간 축적한 후에 항공기 엔진의 잔여 수명(Remaining Useful Life, RUL)을 예측하는 방식을 모델링했습니다. 이 모델은 4개 엔진 센서의 데이터와 우발적인 고장으로 이어지는 엔진 마모 사이의 상관 관계를 사용합니다. Fabrikam은 안전을 보장하기 위하여 정기적인 정밀 검사를 계속 수행하는 한편, 이 모델을 통해 비행이 끝날 때마다 각 엔진에 대한 RUL을 계산할 수 있게 되었습니다. 모델은 비행 중 엔진에서 수집한 원격 분석 데이터를 사용합니다. Fabrikam은 이제 향후 고장 시점을 예측하고 정비 및 수리를 사전에 계획할 수 있습니다.

> [!NOTE]
> 솔루션 모델은 실제 엔진 마모 데이터를 사용합니다.

정비가 필요한 시점을 예측함으로써, Fabrikam은 비용을 줄이도록 운영을 최적화할 수 있습니다.

유지 관리 코디네이터는 다음 작업을 수행하도록 스케줄러와 함께 작동합니다.

- 특정 위치에서 중지하는 항공기와 일치하도록 유지 관리를 계획합니다.
- 일정 중단 없이 항공기가 서비스되지 않을 충분한 시간을 보장합니다.
- 대기 시간 없이 항공기를 충분히 정비할 수 있도록 기술자의 일정을 예약합니다.

재고 관리자는 정비 계획을 수신하기 때문에 주문 공정 및 예비 부품 재고를 최적화할 수 있습니다.

이러한 활동을 통하여 Fabrikam은 승객과 승무원의 안전을 보장하면서 항공기 지상 체류 시간을 최소화하고 운영비를 줄일 수 있습니다.

[Azure IoT 솔루션 가속기][lnk_iot_suite]가 고객에게 필요한 기능을 제공하는 방식을 이해하려면 예측 정비의 잠재력을 깨달을 필요가 있습니다. 이 내용은 [infographic][lnk_infographic]을 참조하세요.

## <a name="how-the-predictive-maintenance-solution-accelerator-is-built"></a>예측 유지 관리 솔루션 가속기가 구축되는 방식

솔루션은 템플릿으로 사용할 수 있는 기존의 Azure Machine Learning을 사용하여 IoT 솔루션 가속기 서비스를 통해 수집되는 장치 원격 분석에서 작동하는 이러한 기능을 보여줍니다. Microsoft는 공개적으로 사용 가능한 데이터<sup>\[1\]</sup>을 기반으로 하는 항공기 엔진의 [회귀 모델][lnk_regression_model] 및 해당 모델을 사용하는 방법에 대한 단계별 지침을 구축했습니다.

Azure IoT 예측 유지 관리 솔루션 가속기는 이 템플릿에서 만든 회귀 모델을 사용합니다. 모델은 Azure 구독에 배포되고 자동으로 생성된 API를 통해 노출됩니다. 이 솔루션은 4개(총 100개 중)의 엔진을 나타내는 테스트 데이터의 하위 집합과 4개(총 21개 중)의 센서 데이터 스트림을 포함합니다. 이 데이터는 학습된 모델을 통해 정확한 결과를 제공하는 데 충분합니다.

*\[1\] A. Saxena and K. Goebel(2008). "Turbofan 엔진 성능 저하 시뮬레이션 데이터 집합", NASA Ames Prognostics Data Repository(https://c3.nasa.gov/dashlink/resources/139/), NASA Ames Research Center, Moffett Field, CA*)

## <a name="get-started-with-predictive-maintenance"></a>예측 유지 관리 시작

이 자습서에서는 예측 유지 관리 솔루션 가속기를 프로비전하는 방법을 보여 줍니다. 또한 예측 유지 관리 솔루션 가속기의 기본 기능을 안내합니다. 솔루션 가속기와 함께 배포한 솔루션 대시보드를 통해 이러한 기능 다수에 액세스할 수 있습니다.

이 자습서를 완료하려면 활성 Azure 구독이 필요합니다.

> [!NOTE]
> 계정이 없는 경우 몇 분 만에 무료 평가판 계정을 만들 수 있습니다. 자세한 내용은 [Azure 무료 평가판][lnk_free_trial]을 참조하세요.

1. Azure 계정 자격 증명을 사용하여 [azureiotsuite.com][lnk-azureiotsuite]에 로그온한 다음 **+** 를 클릭하여 솔루션을 만듭니다.
1. **예측 유지 관리** 타일 **선택**을 클릭합니다.
1. 예측 유지 관리 솔루션 가속기에 대한 **솔루션 이름**을 입력합니다.
1. 솔루션을 프로비전하는 데 사용할 **지역** 및 **구독**을 선택합니다.
1. **솔루션 만들기** 를 클릭하여 프로비전 프로세스를 시작합니다. 일반적으로 이 프로세스는 실행하는 데 몇 분 정도 걸립니다.

### <a name="wait-for-the-provisioning-process-to-complete"></a>프로비전 프로세스가 완료될 때까지 대기

1. **프로비전** 상태인 솔루션 타일을 클릭합니다.
1. Azure 서비스가 Azure 구독에 배포될 때 **프로비전 상태** 입니다.
1. 프로비전이 완료되면 **준비**상태로 바뀝니다.
1. 타일을 클릭하여 오른쪽 창에 솔루션의 세부 정보를 표시합니다. 이 창에서 솔루션 대시보드를 실행하고 Machine Learning 작업 영역에 액세스할 수 있습니다.

> [!NOTE]
> 솔루션 가속기를 배포하는 데 문제가 발생하면 [azureiotsuite.com 사이트에 대한 사용 권한][lnk-permissions] 및 [FAQ][lnk-faq]를 검토하세요. 문제가 지속되면 [포털][lnk-portal]에서 서비스 티켓을 만듭니다.

목록에는 없지만 솔루션에 대해 참조하고 싶은 세부 정보가 있나요? [사용자 의견](https://feedback.azure.com/forums/321918-azure-iot)을 통해 기능을 제안해 주세요.

## <a name="view-the-solution"></a>솔루션 보기

이 섹션에서는 솔루션 UI를 안내합니다.

### <a name="predictive-maintenance-dashboard"></a>대시보드에서 예측 유지 관리

웹 응용 프로그램의 이 페이지는 PowerBI JavaScript 제어를 사용하여([PowerBI 시각 효과 리포지토리][lnk-powerbi]를 참조) 다음을 시각화합니다.

* Blob Storage의 Stream Analytics 작업에서 출력 데이터.
* 항공기 엔진 당 RUL 및 주기 수.

### <a name="observing-the-behavior-of-the-cloud-solution"></a>클라우드 솔루션의 동작 관찰

Azure 포털에서 선택한 솔루션 이름을 가진 리소스 그룹으로 이동하여 프로비전된 리소스를 볼 수 있습니다.

![][img-resource-group]

솔루션 가속기를 프로비전할 때 Machine Learning 작업 영역에 대한 링크가 포함된 전자 메일을 수신합니다. 또한 프로비전된 솔루션에 대한 [azureiotsuite.com][lnk-azureiotsuite] 페이지에서 Machine Learning 작업 영역으로 이동할 수 있습니다. 솔루션이 **준비** 상태일 때 이 페이지에 타일이 제공됩니다.

![][img-machine-learning]

솔루션 포털에서 항공기 당 2개의 엔진, 엔진 당 4개의 센서가 있는 2대의 항공기를 나타내는 네 가지의 시뮬레이션된 장치를 사용하여 샘플이 프로비전된 것을 볼 수 있습니다. 솔루션 포털로 먼저 이동할 때 시뮬레이션이 중지됩니다.

![][img-simulation-stopped]

**시뮬레이션 시작**을 클릭하여 시뮬레이션을 시작합니다. 센서 기록, RUL, 주기 및 RUL 기록으로 대시보드가 채워집니다.

![][img-simulation-running]

RUL이 160(데모 목적으로 선택한 임의의 임계값) 미만인 경우, 솔루션 포털은 RUL 표시 옆에 경고 기호를 표시합니다. 또한 솔루션 포털은 항공기 엔진을 노란색으로 강조 표시합니다. RUL 값이 전반적으로 일반 하향 추세를 갖지만 아래 위로 요동치는 경향이 있습니다. 이 동작은 다양한 주기 길이 및 모델 정확도로 인해 발생합니다.

![][img-simulation-warning]

전체 시뮬레이션이 148 주기를 완료하려면 약 35분이 걸립니다. 160 RUL 임계값은 처음 약 5분이 지나서 도달하고 두 엔진은 약 8분이 지나서 임계값에 도달합니다.

시뮬레이션은 148주기 동안 전체 데이터 집합을 실행하고 최종 RUL 및 주기 값에 도달합니다.

언제든 시뮬레이션을 중지할 수 있지만 **시뮬레이션 시작** 을 클릭하면 데이터 집합의 처음부터 시뮬레이션을 재생합니다.

## <a name="next-steps"></a>다음 단계

Azure IoT가 예측 정비 시나리오를 가능하게 하는 방식에 대해 자세히 알아보려면, [사물 인터넷에서 값 캡처][lnk_capture_value]를 참조하세요.

예측 유지 관리 솔루션 가속기를 [연습][lnk-predictive-walkthrough]합니다.

IoT 솔루션 가속기의 몇 가지 다른 기능을 탐색할 수도 있습니다.

* [IoT 솔루션 가속기에 대한 질문과 대답][lnk-faq]
* [처음부터 IoT 보안을 고려][lnk-security-groundup]

[img-resource-group]: media/iot-accelerators-predictive-overview/resource-group.png
[img-simulation-stopped]: media/iot-accelerators-predictive-overview/simulation-stopped.png
[img-simulation-running]: media/iot-accelerators-predictive-overview/simulation-running.png
[img-simulation-warning]: media/iot-accelerators-predictive-overview/simulation-warning.png
[img-machine-learning]: media/iot-accelerators-predictive-overview/machine-learning.png

[lnk-powerbi]: https://www.github.com/Microsoft/PowerBI-visuals
[lnk-predictive-walkthrough]:iot-accelerators-predictive-walkthrough.md
[lnk_preconfigured_solutions]:iot-accelerators-what-are-solution-accelerators.md
[lnk_iot_suite]:iot-accelerators-options.md
[lnk_infographic]: https://azure.microsoft.com/en-us/solutions/architecture/predictive-maintenance/
[lnk_regression_model]: http://gallery.cortanaanalytics.com/Collection/Predictive-Maintenance-Template-3

[lnk_capture_value]: http://download.microsoft.com/download/0/7/D/07D394CE-185D-4B96-AC3C-9B61179F7080/Capture_value_from_the_Internet%20of%20Things_with_Predictive_Maintenance.PDF
[lnk-faq]: iot-accelerators-faq.md
[lnk-security-groundup]:/azure/iot-fundamentals/iot-security-ground-up
[lnk-azureiotsuite]: https://www.azureiotsolutions.com/
[lnk_free_trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-azureiotsuite]: https://www.azureiotsolutions.com
[lnk-permissions]: iot-accelerators-permissions.md
[lnk-portal]: http://portal.azure.com/
[lnk-machine-learning]: https://azure.microsoft.com/services/machine-learning/