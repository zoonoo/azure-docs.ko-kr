---
title: Time Series Insights를 사용하여 Azure IoT 플러그 앤 플레이 디바이스 원격 분석 데이터를 저장 및 분석 | Microsoft Docs
description: Time Series Insights 환경을 설정하고 IoT Hub를 연결하여 IoT 플러그 앤 플레이 디바이스에서 원격 분석 데이터를 살펴보고 분석합니다.
author: lyrana
ms.author: lyhughes
ms.date: 10/14/2020
ms.topic: tutorial
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: aa99b9059fe8e3cd5b0dfe6f7e62bd02012fd144
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/06/2020
ms.locfileid: "93422266"
---
# <a name="tutorial-create-and-connect-to-time-series-insights-gen2-to-store-visualize-and-analyze-iot-plug-and-play-device-telemetry"></a>자습서: Time Series Insights Gen2를 만들고 연결하여 IoT 플러그 앤 플레이 디바이스 원격 분석 데이터를 저장, 시각화 및 분석합니다.

이 자습서에서는 IoT 플러그 앤 플레이 솔루션과 통합할 [Azure Time Series Insights Gen2](https://docs.microsoft.com/azure/time-series-insights/overview-what-is-tsi)(TSI) 환경을 만들고 올바르게 구성하는 방법을 알아봅니다. TSI를 사용하면 IoT(사물 인터넷) 규모로 시계열 데이터를 수집, 처리, 저장, 쿼리 및 시각화할 수 있습니다.

먼저 TSI 환경을 프로비저닝하고 IoT Hub를 스트리밍 이벤트 원본으로 연결합니다. 그런 다음, 모델 동기화를 통해 온도 컨트롤러 및 자동 온도 조절기 디바이스에 사용한 [DTDL(디지털 트윈 정의 언어)](https://github.com/Azure/opendigitaltwins-dtdl) 샘플 모델 파일을 기반으로 TSI 환경의 시계열 모델을 작성합니다.

## <a name="prerequisites"></a>필수 구성 요소

[!INCLUDE [iot-pnp-prerequisites](../../includes/iot-pnp-prerequisites.md)]

Azure CLI를 로컬에 설치해야 하는 요구 사항을 피하려면, Azure Cloud Shell을 사용하여 클라우드 서비스를 설정하면 됩니다.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

### <a name="time-series-id-selection"></a>시계열 ID 선택

TSI 환경을 프로비저닝하는 동안 시계열 ID를 선택해야 합니다. 적절한 시계열 ID를 선택하는 것은 중요합니다. 속성은 변경이 불가능하며 설정한 후에는 변경할 수 없기 때문입니다. 시계열 ID 선택은 데이터베이스에 대한 파티션 키를 선택하는 것과 같습니다. 일반적으로 TS ID는 자산 모델의 리프 노드여야 합니다. 다시 말해, 원격 분석 데이터를 내보내는 가장 세분화된 자산 또는 센서의 ID 속성을 선택하는 것이 일반적입니다.

IoT 플러그 앤 플레이 사용자가 TS ID를 선택하는 데 적절한 질문은 디바이스 모델에 [구성 요소](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md#component)가 있는지 여부입니다. 

![TS ID 선택](./media/tutorial-configure-tsi/ts-id-selection-pnp.png)

* 빠른 시작을 수행하고 IoT Hub 디바이스가 [자동 온도 조절기](https://raw.githubusercontent.com/Azure/opendigitaltwins-dtdl/master/DTDL/v2/samples/Thermostat.json)를 나타내는 경우 `iot-hub-connection-device-id`를 TS ID로 사용합니다.

* 다중 구성 요소 [TemperatureController](https://raw.githubusercontent.com/Azure/opendigitaltwins-dtdl/master/DTDL/v2/samples/TemperatureController.json)에 대한 자습서 중 하나를 수행한 경우 아래 섹션에서 `iot-hub-connection-device-id, dt-subject`로 작성된 복합 키를 사용합니다.

## <a name="provision-your-azure-time-series-insights-gen2-environment"></a>Azure Time Series Insights Gen2 환경 프로비저닝

아래 명령은 다음을 수행합니다.

* 기록 데이터에 대한 장기 보존 및 분석을 위해 설계된 환경의 [콜드 저장소](https://docs.microsoft.com/azure/time-series-insights/concepts-storage#cold-store)용 Azure 스토리지 계정을 만듭니다.
  * `mytsicoldstore`를 계정의 고유한 이름으로 바꿉니다.
* 보존 기간이 7일인 웜 스토리지 및 무한 보존을 위한 콜드 저장소를 포함하는 Azure Time Series Insights Gen2 환경을 만듭니다 
  * `my-tsi-env`를 TSI 환경의 고유한 이름으로 바꿉니다. 
  * `my-pnp-resourcegroup`을 설정 중에 사용한 리소스 그룹의 이름으로 바꿉니다.
  * 위의 선택 조건에 따라 `my-ts-id-property`를 TS ID 속성 값으로 바꿉니다.

```azurecli-interactive
storage=mytsicoldstore
rg=my-pnp-resourcegroup
az storage account create -g $rg -n $storage --https-only
key=$(az storage account keys list -g $rg -n $storage --query [0].value --output tsv)
az timeseriesinsights environment longterm create --name my-tsi-env --resource-group $rg --time-series-id-properties my-ts-id-property --sku-name L1 --sku-capacity 1 --data-retention 7 --storage-account-name $storage --storage-management-key $key --location eastus2
```

이제 이전에 만든 IoT Hub를 환경의 [이벤트 원본](https://docs.microsoft.com/azure/time-series-insights/concepts-streaming-ingestion-event-sources)으로 구성합니다. 이벤트 원본이 연결되면 TSI는 큐의 가장 빠른 이벤트부터 시작하여 허브에서 이벤트 인덱싱을 시작합니다.

먼저 TSI 환경을 위해 IoT Hub에 고유한 소비자 그룹을 만듭니다. `my-pnp-hub`를 이전에 사용한 IoT Hub의 이름으로 바꿉니다.

```azurecli-interactive
az iot hub consumer-group create --hub-name my-pnp-hub --name tsi-consumer-group 
```

IoT Hub를 연결합니다. `my-pnp-resourcegroup`, `my-pnp-hub`, `my-tsi-env`를 해당 값으로 바꿉니다.

```azurecli-interactive
rg=my-pnp-resourcegroup
iothub=my-pnp-hub
env=my-tsi-env
es_resource_id=$(az iot hub create -g $rg -n $iothub --query id --output tsv)
shared_access_key=$(az iot hub policy list -g $rg --hub-name $iothub --query "[?keyName=='service'].primaryKey" --output tsv)
az timeseriesinsights event-source iothub create -g $rg --environment-name $env -n iot-hub-event-source --consumer-group-name tsi-consumer-group  --key-name iothubowner --shared-access-key $shared_access_key --event-source-resource-id $es_resource_id
```
[Azure Portal](https://portal.azure.com)에서 리소스 그룹으로 이동하여 새로 만든 Time Series Insights 환경을 선택합니다. 인스턴스 개요에 표시된 Time Series Insights 탐색기 URL을 방문합니다.

![포털 개요 페이지](./media/tutorial-configure-tsi/view-environment.png)

탐색기에서 '모든 계층 구조' 아래에 자동 온도 조절기가 두 개 보입니다. 이제 디바이스 모델을 기반으로 시계열 모델을 큐레이팅합니다.

![탐색기 보기 1](./media/tutorial-configure-tsi/tsi-env-first-view.png)

## <a name="model-synchronization-between-digital-twins-definition-language-and-time-series-insights-gen2"></a>디지털 트윈 정의 언어와 Time Series Insights Gen2 간의 모델 동기화

다음으로 DTDL 디바이스 모델을 Azure TSI(Time Series Insights)의 자산 모델로 변환합니다. TSI의 시계열 모델은 TSI 내에서 데이터 맥락화를 위한 의미 체계 모델링 도구입니다. 시계열 모델에는 세 가지 핵심 구성 요소가 있습니다.

* [시계열 모델 인스턴스](https://docs.microsoft.com/azure/time-series-insights/concepts-model-overview#time-series-model-instances). 인스턴스는 시계열 자체의 가상 표현입니다. 인스턴스는 TS ID를 통해 고유하게 식별됩니다.
* [시계열 모델 계층 구조](https://docs.microsoft.com/azure/time-series-insights/concepts-model-overview#time-series-model-hierarchies). 계층 구조는 속성 이름 및 해당 관계를 지정하여 인스턴스를 구성합니다.
* [시계열 모델 형식](https://docs.microsoft.com/azure/time-series-insights/concepts-model-overview#time-series-model-types). 형식은 계산을 수행하기 위한 [변수](https://docs.microsoft.com/azure/time-series-insights/concepts-variables) 또는 수식을 정의하는 유용합니다. 형식은 특정 인스턴스와 연결됩니다.

> [!NOTE]
> 아래의 예는 다중 구성 요소 TemperatureController에 대한 예입니다.

### <a name="define-your-types"></a>형식 정의

모델을 미리 정의하지 않고도 Azure Time Series Insights Gen2에 데이터 수집을 시작할 수 있습니다. 원격 분석 데이터가 도착하면 TSI는 TS ID 속성 값을 기반으로 시계열 인스턴스를 자동 해결하려고 시도합니다. 모든 인스턴스에는 기본 형식이 할당됩니다. 모델을 나타내기 위해 새 형식을 수동으로 만들어야 합니다. 아래 이미지는 DTDL 모델과 TSM 형식을 동기화하는 간단한 방법을 보여줍니다.

![DTDL에서 TSM 형식으로](./media/tutorial-configure-tsi/DTDL-to-TSM-Type.png)

* DTMI(디지털 트윈 모델 식별자)가 형식 ID가 됩니다.
* 형식 이름은 모델의 이름 또는 표시 이름일 수 있습니다.
* 모델 설명은 형식의 설명이 됩니다.
* 숫자 스키마가 있는 각 원격 분석 구성 요소에 대해 형식 변수가 하나 이상 생성됩니다. 
  * 변수에는 숫자 데이터 형식만 사용할 수 있지만 값이 구문 분석 가능한 문자열(예: `"0"`)로 전송되면 `toDouble`과 같은 [변환](https://docs.microsoft.com/rest/api/time-series-insights/reference-time-series-expression-syntax#conversion-functions) 함수를 사용할 수 있습니다.
* 변수 이름은 원격 분석 이름이나 표시 이름일 수 있습니다.
* 변수 시계열 식(TSX)을 정의할 때는 연결되어 있는 원격 분석의 이름과 해당 데이터 유형을 참조합니다.

> [!NOTE]
> 이 예에서는 aggregate와 numeric이라는 두 변수만 보여주지만 각 형식은 최대 100개를 포함할 수 있습니다. 다른 변수는 동일한 원격 분석 값을 참조하여 필요에 따라 다른 계산을 수행할 수 있습니다. 필터, 집계 및 스칼라 함수의 전체 목록은 [Time Series Insights Gen2 시계열 식 구문](https://docs.microsoft.com/rest/api/time-series-insights/reference-time-series-expression-syntax) 설명서를 참조하십시오.

원하는 텍스트 편집기를 열고 아래 JSON을 로컬 드라이브에 저장합니다.

```JSON
{
  "put": [
    {
      "id": "dtmi:com:example:Thermostat;1",
      "name": "Thermostat",
      "description": "Reports current temperature and provides desired temperature control.",
      "variables": {
        "EventCount": {
          "kind": "aggregate",
          "aggregation": {
            "tsx": "count()"
          }
        },
        "Temperature": {
          "kind": "numeric",
          "value": {
            "tsx": "$event.temperature.Double"
          },
          "aggregation": {
            "tsx": "avg($value)"
          }
        }
      }
    }
  ]
}
```

Time Series Insights 탐색기에서 왼쪽의 모델 아이콘을 클릭하여 모델 탭으로 이동합니다. **형식** 을 클릭하고 **JSON 업로드** 를 클릭합니다.

![업로드](./media/tutorial-configure-tsi/upload-type.png)

**파일 선택** 을 선택하고 이전에 저장한 JSON을 선택한 다음, **업로드** 를 클릭합니다.

새로 정의된 자동 온도 조절기 형식이 보여야 합니다.

### <a name="optional---create-a-hierarchy"></a>선택 사항 - 계층 구조 만들기

필요에 따라 계층 구조를 만들어서 TemeraptureController라는 부모 아래에 두 개의 자동 온도 조절기 구성 요소를 구성할 수 있습니다.

계층 구조를 클릭하고 계층 구조 추가를 선택합니다.  `Device Fleet`을 이름으로 입력하고 `Device Name`이라는 수준을 하나 만든 다음, 저장을 클릭합니다.

![계층 구조 추가](./media/tutorial-configure-tsi/add-hierarchy.png)

### <a name="assign-your-instances-to-the-correct-type"></a>올바른 형식에 인스턴스 할당

다음으로 인스턴스의 형식을 변경하고 필요에 따라 계층 구조 내에 배치합니다.

인스턴스 탭을 선택하고 맨 오른쪽에 있는 편집 아이콘을 클릭합니다. 

![인스턴스 편집](./media/tutorial-configure-tsi/edit-instance.png)

형식 드롭다운을 클릭하고 `Thermostat`을 선택합니다. 

![인스턴스 형식 변경](./media/tutorial-configure-tsi/change-type.png)

계층 구조를 만들었으면 인스턴스 필드를 선택하고 `Device Fleet` 상자를 선택합니다. `Temperature Controller`를 부모 디바이스의 값으로 입력한 다음, 저장을 클릭합니다.

![계층 구조에 할당](./media/tutorial-configure-tsi/assign-to-hierarchy.png)

두 번째 자동 온도 조절기에 대해 위의 단계를 반복합니다.

### <a name="view-your-data"></a>데이터 보기

차트 창으로 돌아가서 Device Fleet 및 TemperatureController를 확장합니다. thermostat1을 클릭하고 `Temperature` 변수를 선택한 다음, 추가를 클릭하여 값을 차트로 표시합니다. thermostat2에 대해 동일한 작업을 수행합니다.

![thermostat2에 대한 인스턴스 형식 변경](./media/tutorial-configure-tsi/charting-values.png)

## <a name="next-steps"></a>다음 단계

* 간격 크기 조정 및 Y축 컨트롤을 비롯한 다양한 차트 옵션에 대해 자세히 알아보려면 [Azure Time Series Insights 탐색기](https://docs.microsoft.com/azure/time-series-insights/concepts-ux-panels) 설명서를 참조하세요.

* 환경의 시계열 모델에 대한 심층적인 개요는 [이](https://docs.microsoft.com/azure/time-series-insights/concepts-model-overview) 문서를 참조하세요.

* 쿼리 API 및 시계열 식 구문에 대해 자세히 알아보려면 [선택하세요](https://docs.microsoft.com/rest/api/time-series-insights/reference-query-apis).




