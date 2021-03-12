---
title: IoT Hub에서 원격 분석 수집
titleSuffix: Azure Digital Twins
description: IoT Hub에서 장치 원격 분석 메시지를 수집 하는 방법을 참조 하세요.
author: alexkarcher-msft
ms.author: alkarche
ms.date: 9/15/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 3223a1c8e20d8b0caced5d940132c32fa0aba97c
ms.sourcegitcommit: 6776f0a27e2000fb1acb34a8dddc67af01ac14ac
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/11/2021
ms.locfileid: "103149093"
---
# <a name="ingest-iot-hub-telemetry-into-azure-digital-twins"></a>Azure Digital Twins에 IoT Hub 원격 분석 수집

Azure Digital Twins는 IoT 장치 및 기타 원본의 데이터를 기반으로 합니다. Azure Digital Twins에서 사용할 장치 데이터의 공통 소스는 [IoT Hub](../iot-hub/about-iot-hub.md)됩니다.

Azure Digital Twins로 데이터를 수집 하는 프로세스는 [Azure Functions](../azure-functions/functions-overview.md)를 사용 하 여 만든 함수와 같은 외부 계산 리소스를 설정 하는 것입니다. 함수는 데이터를 수신 하 고 [DigitalTwins api](/rest/api/digital-twins/dataplane/twins) 를 사용 하 여 그에 따라 [디지털](concepts-twins-graph.md) 쌍에서 속성을 설정 하거나 원격 분석 이벤트를 발생 시킵니다. 

이 방법 문서에서는 IoT Hub에서 원격 분석을 수집 하는 함수를 작성 하는 프로세스를 안내 합니다.

## <a name="prerequisites"></a>사전 요구 사항

이 예를 계속 하기 전에 다음 리소스를 필수 조건으로 설정 해야 합니다.
* **IoT hub**. 지침은 [이 IoT Hub 빠른](../iot-hub/quickstart-send-telemetry-cli.md)시작의 *IoT Hub 만들기* 섹션을 참조 하세요.
* 장치 원격 분석을 수신 하는 **Azure Digital Twins 인스턴스입니다** . 자세한 내용은 [*방법: Azure Digital Twins 인스턴스 및 인증 설정*](./how-to-set-up-instance-portal.md)을 참조 하세요.

### <a name="example-telemetry-scenario"></a>원격 분석 시나리오 예제

이 방법에서는 Azure의 기능을 사용 하 여 IoT Hub에서 Azure Digital Twins로 메시지를 보내는 방법을 간략하게 설명 합니다. 메시지를 보내는 데 사용할 수 있는 여러 가지 구성 및 일치 전략이 있지만이 문서의 예제에는 다음과 같은 부분이 포함 되어 있습니다.
* IoT Hub에서 알려진 장치 ID가 있는 자동 온도 조절기 장치
* 일치 하는 ID를 사용 하 여 장치를 나타내는 디지털 쌍

> [!NOTE]
> 이 예에서는 장치 ID와 해당 하는 디지털 쌍 ID 사이에 간단한 ID 일치를 사용 하지만, 장치에서 해당 쌍 (예: 매핑 테이블)으로 보다 정교한 매핑을 제공할 수 있습니다.

자동 온도 조절기 장치에서 온도 원격 분석 이벤트를 보낼 때마다 함수는 원격 분석을 처리 하 고 디지털 쌍의 *온도* 속성은 업데이트 해야 합니다. 이 시나리오는 아래 다이어그램에 설명 되어 있습니다.

:::image type="content" source="media/how-to-ingest-iot-hub-data/events.png" alt-text="순서도를 표시 하는 다이어그램입니다. 차트에서 IoT Hub 장치는 IoT Hub를 통해 온도 원격 분석을 azure의 함수로 전송 합니다. 그러면 azure에서 해당 쌍의 온도 속성을 업데이트 합니다." border="false":::

## <a name="add-a-model-and-twin"></a>모델 및 트윈 추가

이 섹션에서는 자동 온도 조절기 장치를 나타내며 IoT Hub 정보로 업데이트 되는 Azure Digital Twins에서 [디지털](concepts-twins-graph.md) 쌍을 설정 합니다.

자동 온도 조절기 쌍을 만들려면 먼저 자동 온도 조절기 모델을 인스턴스에 업로드 해야 합니다 .이 [모델](concepts-models.md) 은 자동 온도 조절기의 속성을 설명 하 고 나중에 쌍을 만드는 데 사용 됩니다. 

모델은 다음과 같습니다.
:::code language="json" source="~/digital-twins-docs-samples/models/Thermostat.json":::

**이 모델을 쌍 인스턴스에 업로드** 하려면 위의 모델을 인라인 JSON으로 업로드 하는 다음 Azure CLI 명령을 실행 합니다. CLI를 [로컬로 설치](/cli/azure/install-azure-cli.md)하는 경우 브라우저 또는 컴퓨터에서 [Azure Cloud Shell](/cloud-shell/overview.md) 에서 명령을 실행할 수 있습니다.

```azurecli-interactive
az dt model create --models '{  "@id": "dtmi:contosocom:DigitalTwins:Thermostat;1",  "@type": "Interface",  "@context": "dtmi:dtdl:context;2",  "contents": [    {      "@type": "Property",      "name": "Temperature",      "schema": "double"    }  ]}' -n {digital_twins_instance_name}
```

그런 다음 **이 모델을 사용 하 여 하나의** 쌍을 만들어야 합니다. 다음 명령을 사용 하 여 **thermostat67** 라는 자동 온도 조절기 쌍을 만들고 0.0를 초기 온도 값으로 설정 합니다.

```azurecli-interactive
az dt twin create --dtmi "dtmi:contosocom:DigitalTwins:Thermostat;1" --twin-id thermostat67 --properties '{"Temperature": 0.0,}' --dt-name {digital_twins_instance_name}
```

>[!NOTE]
> PowerShell 환경에서 Cloud Shell를 사용 하는 경우 해당 값을 올바르게 구문 분석 하려면 인라인 JSON 필드에서 인용 부호 문자를 이스케이프 해야 할 수 있습니다. 모델을 업로드 하 고이 수정으로 쌍을 만드는 명령은 다음과 같습니다.
>
> 모델 업로드:
> ```azurecli-interactive
> az dt model create --models '{  \"@id\": \"dtmi:contosocom:DigitalTwins:Thermostat;1\",  \"@type\": \"Interface\",  \"@context\": \"dtmi:dtdl:context;2\",  \"contents\": [    {      \"@type\": \"Property\",      \"name\": \"Temperature\",      \"schema\": \"double\"    }  ]}' -n {digital_twins_instance_name}
> ```
>
> 쌍 만들기:
> ```azurecli-interactive
> az dt twin create --dtmi "dtmi:contosocom:DigitalTwins:Thermostat;1" --twin-id thermostat67 --properties '{\"Temperature\": 0.0,}' --dt-name {digital_twins_instance_name}
> ```

쌍이 성공적으로 만들어지면 명령의 CLI 출력은 다음과 같습니다.
```json
{
  "$dtId": "thermostat67",
  "$etag": "W/\"0000000-9735-4f41-98d5-90d68e673e15\"",
  "$metadata": {
    "$model": "dtmi:contosocom:DigitalTwins:Thermostat;1",
    "Temperature": {
      "ackCode": 200,
      "ackDescription": "Auto-Sync",
      "ackVersion": 1,
      "desiredValue": 0.0,
      "desiredVersion": 1
    }
  },
  "Temperature": 0.0
}
```

## <a name="create-a-function"></a>함수 만들기

이 섹션에서는 azure 함수를 만들어 azure Digital 쌍에 액세스 하 고 수신 되는 IoT 원격 분석 이벤트에 따라 쌍를 업데이트 합니다. 아래 단계에 따라 함수를 만들고 게시 합니다.

#### <a name="step-1-create-a-function-app-project"></a>1 단계: 함수 앱 프로젝트 만들기

먼저 Visual Studio에서 새 함수 앱 프로젝트를 만듭니다. 이 작업을 수행 하는 방법에 대 한 지침은 *방법: 데이터 처리를 위한 함수 설정* 문서에서 [**Visual Studio에서 함수 앱 만들기**](how-to-create-azure-function.md#create-a-function-app-in-visual-studio) 섹션을 참조 하세요.

#### <a name="step-2-fill-in-function-code"></a>2 단계: 함수 코드 입력

프로젝트에 다음 패키지를 추가 합니다.
* [DigitalTwins](https://www.nuget.org/packages/Azure.DigitalTwins.Core/)
* [Azure. Id](https://www.nuget.org/packages/Azure.Identity/)
* [Microsoft.Azure.WebJobs.Extensions.EventGrid](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventGrid/)

새 프로젝트를 사용 하 여 Visual Studio에서 생성 한 *Function1.cs* sample 함수의 이름을 *IoTHubtoTwins.cs* 로 바꿉니다. 파일의 코드를 다음 코드로 바꿉니다.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/IoTHubToTwins.cs":::

함수 코드를 저장 합니다.

#### <a name="step-3-publish-the-function-app-to-azure"></a>3 단계: Azure에 함수 앱 게시

Azure의 함수 앱에 프로젝트를 게시 합니다.

이 작업을 수행 하는 방법에 대 한 지침은 *방법: 데이터 처리를 위한 함수 설정* 문서의 [**Azure에 함수 앱 게시**](how-to-create-azure-function.md#publish-the-function-app-to-azure) 섹션을 참조 하세요.

#### <a name="step-4-configure-the-function-app"></a>4 단계: 함수 앱 구성

다음으로, 함수에 대 한 **액세스 역할을 할당** 하 고 **응용 프로그램 설정을 구성** 하 여 Azure Digital twins 인스턴스에 액세스할 수 있도록 합니다. 이 작업을 수행 하는 방법에 대 한 지침은 *방법: 데이터 처리를 위한 함수 설정* 문서의 [**함수 앱에 대 한 보안 액세스 설정**](how-to-create-azure-function.md#set-up-security-access-for-the-function-app) 섹션을 참조 하세요.

## <a name="connect-your-function-to-iot-hub"></a>함수를 IoT Hub에 연결

이 섹션에서는 IoT hub 장치 데이터의 이벤트 대상으로 함수를 설정 합니다. 이렇게 하면 IoT Hub의 자동 온도 조절기 장치에 있는 데이터가 처리를 위해 Azure 함수에 전송 됩니다.

[Azure Portal](https://portal.azure.com/)에서 [*전제 조건*](#prerequisites) 섹션에서 만든 IoT Hub 인스턴스로 이동 합니다. **이벤트** 아래에서 함수에 대 한 구독을 만듭니다.

:::image type="content" source="media/how-to-ingest-iot-hub-data/add-event-subscription.png" alt-text="이벤트 구독을 추가 하는 것을 보여 주는 Azure Portal의 스크린샷":::

**이벤트 구독 만들기** 페이지에서 다음과 같이 필드를 채웁니다.
  1. **이름** 에서 이벤트 구독에 대해 원하는 이름을 선택 합니다.
  2. **이벤트 스키마** 에 대해 _Event Grid 스키마_ 를 선택 합니다.
  3. **시스템 항목 이름** 에서 원하는 이름을 선택 합니다.
  1. **이벤트 유형으로 필터링 하려면** _장치 원격 분석_ 확인란을 선택 하 고 다른 이벤트 유형을 선택 취소 합니다.
  1. **끝점 형식** 으로 _Azure 함수_ 를 선택 합니다.
  1. 끝점 **의 경우** 끝점 _선택_ 링크를 사용 하 여 끝점에 사용할 Azure 함수를 선택 합니다.
    
:::image type="content" source="media/how-to-ingest-iot-hub-data/create-event-subscription.png" alt-text="이벤트 구독 정보를 만드는 Azure Portal의 스크린샷":::

열리는 _Azure 함수 선택_ 페이지에서 아래 세부 정보를 확인 하거나 입력 합니다.
 1. **구독**: Azure 구독.
 2. **리소스 그룹**: 리소스 그룹입니다.
 3. **함수 앱**: 함수 앱 이름입니다.
 4. **슬롯**: _프로덕션_.
 5. **함수**: 드롭다운에서 이전 *IoTHubtoTwins* 함수를 선택 합니다.

_선택 확인_ 단추를 사용 하 여 세부 정보를 저장 합니다.            
      
:::image type="content" source="media/how-to-ingest-iot-hub-data/select-azure-function.png" alt-text="함수를 선택 하는 Azure Portal의 스크린샷":::

_만들기_ 단추를 선택 하 여 이벤트 구독을 만듭니다.

## <a name="send-simulated-iot-data"></a>시뮬레이션 된 IoT 데이터 보내기

새 수신 함수를 테스트 하려면 [*자습서: 종단 간 솔루션 연결*](./tutorial-end-to-end.md)에서 장치 시뮬레이터를 사용 합니다. 이 자습서는 c #으로 작성 된 샘플 프로젝트를 기반으로 합니다. 샘플 코드는 다음 위치에 있습니다. [Azure Digital Twins 종단 간 샘플](/samples/azure-samples/digital-twins-samples/digital-twins-samples). 해당 리포지토리에서 **DeviceSimulator** 프로젝트를 사용 하 게 됩니다.

종단 간 자습서에서 다음 단계를 완료 합니다.
1. [*IoT Hub에 시뮬레이션된 디바이스 등록*](./tutorial-end-to-end.md#register-the-simulated-device-with-iot-hub)
2. [*시뮬레이션 구성 및 실행*](./tutorial-end-to-end.md#configure-and-run-the-simulation)

## <a name="validate-your-results"></a>결과 유효성 검사

위의 장치 시뮬레이터를 실행 하는 동안 디지털 쌍의 온도 값이 변경 됩니다. Azure CLI에서 다음 명령을 실행 하 여 온도 값을 확인 합니다.

```azurecli-interactive
az dt twin query -q "select * from digitaltwins" -n {digital_twins_instance_name}
```

출력에는 다음과 같은 온도 값이 포함 되어야 합니다.

```json
{
  "result": [
    {
      "$dtId": "thermostat67",
      "$etag": "W/\"0000000-1e83-4f7f-b448-524371f64691\"",
      "$metadata": {
        "$model": "dtmi:contosocom:DigitalTwins:Thermostat;1",
        "Temperature": {
          "ackCode": 200,
          "ackDescription": "Auto-Sync",
          "ackVersion": 1,
          "desiredValue": 69.75806974934324,
          "desiredVersion": 1
        }
      },
      "Temperature": 69.75806974934324
    }
  ]
}
```

값 변경을 보려면 위의 쿼리 명령을 반복 해 서 실행 합니다.

## <a name="next-steps"></a>다음 단계

Azure Digital Twins를 사용 하 여 데이터 수신 및 송신에 대해 읽기:
* [*개념: 다른 서비스와 통합*](concepts-integration.md)
