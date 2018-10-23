---
title: Azure Digital Twins를 사용하여 공간 모니터링 | Microsoft Docs
description: 이 자습서의 단계에 따라 Azure Digital Twins를 사용하여 공간 리소스를 프로비전하고 작업 상태를 모니터링하는 방법을 알아봅니다.
services: digital-twins
author: dsk-2015
ms.service: digital-twins
ms.topic: tutorial
ms.date: 10/15/2018
ms.author: dkshir
ms.openlocfilehash: 1e5cb18b4e526cd0a0607f5bc93788fcf07430e1
ms.sourcegitcommit: 6361a3d20ac1b902d22119b640909c3a002185b3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/17/2018
ms.locfileid: "49364238"
---
# <a name="tutorial-provision-your-building-and-monitor-working-conditions-with-azure-digital-twins"></a>자습서: Azure Digital Twins를 사용하여 건물을 프로비전하고 작업 조건 모니터링

이 자습서에서는 Azure Digital Twins로 공간을 모니터링하여 원하는 온도 조건 및 쾌적도를 유지하는 방법을 알아봅니다. [샘플 건물을 구성](tutorial-facilities-setup.md)했으면, 이 자습서의 단계에 따라 건물을 프로비전하고 센서 데이터에 대해 사용자 지정 함수를 실행할 수 있습니다.

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 모니터링할 조건 정의
> * 사용자 정의 함수 만들기
> * 센서 데이터 시뮬레이션
> * 사용자 정의 함수 결과 가져오기

## <a name="prerequisites"></a>필수 조건

이 자습서에서는 이미 [Azure Digital Twins를 구성](tutorial-facilities-setup.md)한 것으로 가정합니다. 계속 진행하기 전에 다음 사항을 확인합니다.
- [Azure 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- 실행 중인 Digital Twins 인스턴스. 
- 작업 머신에 다운로드하여 추출한 [Digital Twins C# 샘플](https://github.com/Azure-Samples/digital-twins-samples-csharp). 
- 샘플을 빌드하고 실행하기 위해 개발 머신에 설치된 [.NET Core SDK 버전 2.1.403 이상](https://www.microsoft.com/net/download). 올바른 버전이 설치되어 있는지 확인하려면 `dotnet --version` 명령을 실행합니다. 
- 샘플 코드를 탐색할 [Visual Studio Code](https://code.visualstudio.com/). 

## <a name="define-conditions-to-monitor"></a>모니터링할 조건 정의
장치 또는 센서 데이터에서 모니터링할 특정 조건 집합, 즉, **선택기**를 정의할 수 있습니다. 그런 다음, *사용자 정의 함수*라고 하는 함수를 정의할 수 있습니다. 사용자 정의 함수는 선택기에서 지정한 조건이 발생하면 공간 및 장치에서 오는 데이터에 대해 사용자 지정 논리를 실행합니다. 자세한 내용은 [데이터 처리 및 사용자 정의 함수](concepts-user-defined-functions.md)를 참조하세요. 

**_occupancy-quickstart_** 샘플 프로젝트에서, **_src\actions\provisionSample.yaml_** 파일을 Visual Studio Code에서 엽니다. **matchers** 형식으로 시작하는 섹션을 봅니다. 이 형식에 속하는 각 항목은 지정된 **이름**을 사용하여 **dataTypeValue** 형식의 센서를 모니터링할 선택기를 만듭니다. **장치** 노드가 있고 몇 가지 **센서**를 포함하고 있는 *Focus Room A1*이라는 이름의 공간과 어떤 관계가 있는지 살펴봅니다. 이러한 센서 중 하나를 추적하는 선택기를 프로비전하려면 **dataTypeValue**가 해당 센서의 **dataType**과 일치해야 합니다. 

기존 선택기 아래에 다음 선택기를 추가한 다음, 키를 정렬하고 공백을 탭으로 바꾸지 않도록 주의합니다.

```yaml
      - name: Matcher Temperature
        dataTypeValue: Temperature
```

이렇게 하면 [첫 번째 자습서](tutorial-facilities-setup.md)에서 추가한 *SAMPLE_SENSOR_TEMPERATURE* 센서를 추적합니다. 또한 다음 줄은 *provisionSample.yaml*에 주석으로 처리되며, 각 줄 앞의 '#' 문자를 제거하여 간단하게 주석 처리를 제거할 수 있습니다. 

<a id="udf" />

## <a name="create-a-user-defined-function"></a>사용자 정의 함수 만들기
UDF(사용자 정의 함수)를 사용하여 센서 데이터의 처리 방식을 사용자 지정할 수 있습니다. 사용자 정의 함수는 선택기에서 설명하는 특정 조건이 충족될 때 Digital Twins 인스턴스 내에서 실행 가능한 사용자 지정 JavaScript 코드입니다. 모니터링하려는 각 센서에 대한 *선택기* 및 *사용자 정의 함수*를 만들 수 있습니다. 자세한 내용은 [데이터 처리 및 사용자 정의 함수](concepts-user-defined-functions.md)를 참조하세요. 

샘플 *provisionSample.yaml* 파일에서, **userdefinedfunctions** 형식으로 시작하는 섹션을 찾아봅니다. 이 섹션은 지정된 **이름**을 사용하여 **matcherNames** 아래의 선택기 목록에 따라 작동하는 사용자 정의 함수를 프로비전합니다. UDF에 대한 사용자 고유의 JavaScript 파일을 **스크립트**로 제공하는 방법을 살펴봅니다. **roleassignments** 섹션도 살펴봅니다. 이 섹션은 사용자 정의 함수에 *공간 관리자* 역할을 할당합니다. 이렇게 하면 사용자 정의 함수는 프로비전된 공간에서 오는 이벤트에 액세스할 수 있습니다. 

1. *provisionSample.yaml* 파일의 `matcherNames` 노드에 다음 줄을 추가하거나 주석 처리를 제거하여 온도 선택기를 포함하도록 UDF를 구성합니다.

    ```yaml
            - Matcher Temperature
    ```

1. 편집기에서 **_src\actions\userDefinedFunctions\availability.js_** 파일을 엽니다. 이 파일은 *provisionSample.yaml*의 **script** 요소에서 언급한 파일입니다. 이 파일의 사용자 정의 함수는 실내에서 동작이 감지되지 않고 이산화탄소 농도가 1000ppm 미만인 조건을 찾습니다. 다른 조건과 함께 온도를 모니터링하도록 JavaScript 파일을 수정합니다. 실내에서 동작이 감지되지 않고, 이산화탄소 농도가 1000ppm 미만이고, 온도가 화씨 78도 미만인 조건을 찾는 다음 코드 줄을 추가합니다.

   > [!NOTE]
   > 이 섹션에서는 사용자 정의 함수를 작성하는 한 가지 방법을 자세히 알아볼 수 있도록 *src\actions\userDefinedFunctions\availability.js* 파일을 수정합니다. 하지만 [src\actions\userDefinedFunctions\availabilityForTutorial.js](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/userDefinedFunctions/availabilityForTutorial.js) 파일을 직접 사용하도록 설정에서 선택할 수 있습니다. 이 파일에는 이 자습서에 필요한 모든 변경 내용이 들어 있습니다. 이 파일을 대신 사용하는 경우 [src\actions\provisionSample.yaml](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/provisionSample.yaml)의 **_스크립트_** 키에 올바른 파일 이름을 사용해야 합니다.

    1. 파일 맨 위에서, 온도에 대한 다음 줄을 주석 `// Add your sensor type here` 아래에 추가합니다.

        ```JavaScript
            var temperatureType = "Temperature";
            var temperatureThreshold = 78;
        ```
   
    1. 주석 `// Add your sensor variable here` 아래에서, `var motionSensor`를 정의하는 명령문 뒤에 다음 줄을 추가합니다.

        ```JavaScript
            var temperatureSensor = otherSensors.find(function(element) {
                return element.DataType === temperatureType;
            });
        ```
    
    1. 주석 `// Add your sensor latest value here` 아래에서, `var carbonDioxideValue`를 정의하는 명령문 뒤에 다음 줄을 추가합니다.

        ```JavaScript
            var temperatureValue = getFloatValue(temperatureSensor.Value().Value);
        ```
    
    1. 주석 `// Modify this line to monitor your sensor value` 아래에서 다음 코드 줄을 제거합니다. 

        ```JavaScript
            if(carbonDioxideValue === null || motionValue === null) {
                sendNotification(telemetry.SensorId, "Sensor", "Error: Carbon dioxide or motion are null, returning");
                return;
            }
        ```
       
       다음 줄로 바꿉니다.

        ```JavaScript
            if(carbonDioxideValue === null || motionValue === null || temperatureValue === null){
                sendNotification(telemetry.SensorId, "Sensor", "Error: Carbon dioxide, motion, or temperature are null, returning");
                return;
            }
        ```
    
    1. 주석 `// Modify these lines as per your sensor` 아래에서 다음 코드 줄을 제거합니다.

        ```JavaScript
            var availableFresh = "Room is available and air is fresh";
            var noAvailableOrFresh = "Room is not available or air quality is poor";
        ```

       다음 줄로 바꿉니다.

        ```JavaScript
            var alert = "Room with fresh air and comfortable temperature is available.";
            var noAlert = "Either room is occupied, or working conditions are not right.";
        ```
    
    1. 주석 `// Modify this code block for your sensor` 뒤에서 다음 *if-else* 코드 블록을 제거합니다.

        ```JavaScript
            // If carbonDioxide less than threshold and no presence in the room => log, notify and set parent space computed value
            if(carbonDioxideValue < carbonDioxideThreshold && !presence) {
                log(`${availableFresh}. Carbon Dioxide: ${carbonDioxideValue}. Presence: ${presence}.`);
                setSpaceValue(parentSpace.Id, spaceAvailFresh, availableFresh);

                // Set up custom notification for air quality
                parentSpace.Notify(JSON.stringify(availableFresh));
            }
            else {
                log(`${noAvailableOrFresh}. Carbon Dioxide: ${carbonDioxideValue}. Presence: ${presence}.`);
                setSpaceValue(parentSpace.Id, spaceAvailFresh, noAvailableOrFresh);

                // Set up custom notification for air quality
                parentSpace.Notify(JSON.stringify(noAvailableOrFresh));
            }
        ```
      
       다음 *if-else* 블록으로 바꿉니다.

        ```JavaScript
            // If sensor values are within range and room is available
            if(carbonDioxideValue < carbonDioxideThreshold && temperatureValue < temperatureThreshold && !presence) {
                log(`${alert}. Carbon Dioxide: ${carbonDioxideValue}. Temperature: ${temperatureValue}. Presence: ${presence}.`);

                // log, notify and set parent space computed value
                setSpaceValue(parentSpace.Id, spaceAvailFresh, alert);

                // Set up notification for this alert
                parentSpace.Notify(JSON.stringify(alert));
            }
            else {
                log(`${noAlert}. Carbon Dioxide: ${carbonDioxideValue}. Temperature: ${temperatureValue}. Presence: ${presence}.`);
    
                // log, notify and set parent space computed value
                setSpaceValue(parentSpace.Id, spaceAvailFresh, noAlert);
            }
        ```
        
        수정된 UDF는 방을 사용할 수 있게 되고 이산화탄소 및 온도가 허용 한도 내에 있는 조건을 찾습니다. 이 조건이 충족되면 UDF는 `parentSpace.Notify(JSON.stringigy(alert));` 문을 사용하여 알림을 생성합니다. UDF는 조건 충족 여부에 관계없이 해당 메시지를 사용하여 모니터링되는 공간의 값을 설정합니다.
    
    1. 파일을 저장합니다. 
    
1. 명령 창을 열고 **_occupancy-quickstart\src_** 폴더로 이동합니다. 다음 명령을 실행하여 공간 인텔리전스 그래프 및 사용자 정의 함수를 프로비전합니다. 

    ```cmd/sh
    dotnet run ProvisionSample
    ```

   > [!IMPORTANT]
   > Digital Twins 관리 API에 대한 무단 액세스를 방지하기 위해, **_occupancy-quickstart_** 응용 프로그램에서는 Azure 계정 자격 증명으로 로그인할 것을 요구합니다. 이 응용 프로그램은 짧은 기간 동안 자격 증명을 저장하므로 실행할 때 로그인이 필요 없는 경우도 있습니다. 이 프로그램을 처음으로 실행하면, 그리고 첫 실행 이후 저장된 자격 증명이 만료되면 [로그인] 페이지로 이동되고 해당 페이지에서 입력할 세션 관련 코드가 제공됩니다. 표시되는 메시지에 따라 Azure 계정으로 로그인하세요.


1. 계정이 인증되면 응용 프로그램이 *provisionSample.yaml* 파일에 구성된 대로 샘플 공간 그래프를 만들기 시작합니다. 프로비전이 완료될 때까지 몇 분 정도 기다립니다. 완료되면 명령 창의 메시지를 관찰하고, 공간 그래프가 어떻게 생성되는지 살펴봅니다. 루트 노드 또는 `Venue`에 IoT 허브를 만드는 방법을 살펴봅니다. 

1. 명령 창의 출력에서, `Devices` 섹션 아래의 `ConnectionString` 값을 클립보드에 복사합니다. 다음 섹션에서 장치 연결을 시뮬레이션하려면 이 값이 필요합니다.

    ![샘플 프로비전](./media/tutorial-facilities-udf/run-provision-sample.png)

> [!TIP]
> 프로비전 중에 "스레드 종료 또는 응용 프로그램 요청으로 인해 I/O 작업이 중단되었습니다"라는 오류 메시지를 받으면 명령을 다시 실행하세요. 네트워크 문제로 인해 HTTP 클라이언트 시간이 초과되면 이 오류가 발생할 수 있습니다.

<a id="simulate" />

## <a name="simulate-sensor-data"></a>센서 데이터 시뮬레이션
이 섹션에서는 샘플의 *device-connectivity* 프로젝트를 사용하여 동작, 온도 및 이산화탄소를 감지하는 센서 데이터를 시뮬레이션하겠습니다. 이 프로젝트는 센서에 대한 임의의 값을 생성하고, 장치 연결 문자열을 사용하여 IoT 허브로 보냅니다.

1. 별도의 명령 창에서 Digital Twin 샘플로 이동한 다음, **_device-connectivity_** 폴더로 이동합니다.

1. 다음 명령을 실행하여 프로젝트에 대한 종속성이 올바른지 확인합니다.

    ```cmd/sh
    dotnet restore
    ```

1. 편집기에서 *appSettings.json* 파일을 열고 다음 값을 편집합니다.
    1. *DeviceConnectionString*: 이전 섹션의 출력 창에 있는 `ConnectionString` 값을 할당합니다. 시뮬레이터가 IoT 허브에 올바르게 연결하도록, 따옴표 안의 이 문자열을 모두 복사해야 합니다.

    1. *센서* 배열의 *HardwareId*: Digital Twins 인스턴스에 프로비전된 센서의 이벤트를 시뮬레이션하는 것이므로, 이 파일의 하드웨어 ID와 센서 이름이 *provisionSample.yaml* 파일의 `sensors` 노드와 일치해야 합니다. 온도 센서에 대한 새 항목을 추가합니다. *appSettings.json*의 **센서** 노드는 다음과 같이 표시됩니다.

        ```JSON
        "Sensors": [{
          "DataType": "Motion",
          "HardwareId": "SAMPLE_SENSOR_MOTION"
        },{
          "DataType": "CarbonDioxide",
          "HardwareId": "SAMPLE_SENSOR_CARBONDIOXIDE"
        },{
          "DataType": "Temperature",
          "HardwareId": "SAMPLE_SENSOR_TEMPERATURE"
        }]
        ```

1. 다음 명령을 실행하여 온도, 동작 및 이산화탄소에 대한 장치 이벤트 시뮬레이션을 시작합니다.

    ```cmd/sh
    dotnet run
    ```

   > [!NOTE] 
   > 시뮬레이션 샘플은 Digital Twin 인스턴스와 직접 통신하지 않기 때문에 인증이 필요 없습니다.

## <a name="get-results-of-user-defined-function"></a>사용자 정의 함수 결과 가져오기
인스턴스가 장치 및 센서 데이터를 받을 때마다 사용자 정의 함수가 실행됩니다. 이 섹션에서는 Digital Twins 인스턴스를 쿼리하여 사용자 정의 함수의 결과를 가져옵니다. 언제 방을 사용할 수 있고, 공기가 깨끗하고, 온도가 적정 수준인지 거의 실시간으로 볼 수 있습니다. 

1. 샘플을 프로비전할 때 사용한 명령 창 또는 새 명령 창을 열고, 샘플의 **_occupancy-quickstart\src_** 폴더로 다시 이동합니다. 

1. 로그인하라는 메시지가 표시되면 다음 명령을 실행하여 로그인합니다.

    ```cmd/sh
    dotnet run GetAvailableAndFreshSpaces
    ```

출력 창에는 사용자 정의 함수가 실행되어 장치 시뮬레이션에서 이벤트를 가로채는 방법이 표시됩니다. 

   ![UDF 실행](./media/tutorial-facilities-udf/udf-running.png)

모니터링되는 조건의 충족 여부에 따라, 사용자 정의 함수는 [위 섹션](#udf)에서 살펴본 것처럼 관련 메시지를 사용하여 공간의 값을 설정하고, `GetAvailableAndFreshSpaces` 함수는 이 값을 콘솔에 출력합니다. 

## <a name="clean-up-resources"></a>리소스 정리

앞으로 Azure Digital Twins 탐색을 중지하려는 경우 이 자습서에서 만든 리소스를 자유롭게 삭제하면 됩니다.

1. [Azure Portal](http://portal.azure.com)의 왼쪽 메뉴에서 **모든 리소스**를 클릭한 다음, Digital Twins 리소스 그룹을 선택하여 **삭제**합니다.
2. 필요한 경우 작업 머신에서도 샘플 응용 프로그램을 삭제할 수 있습니다. 


## <a name="next-steps"></a>다음 단계

공간을 프로비전하고 사용자 지정 알림을 트리거하는 프레임워크를 만들었으므로 다음 자습서 중 아무 것이나 진행할 수 있습니다. 

> [!div class="nextstepaction"]
> [자습서: Logic Apps를 사용하여 Azure Digital Twins 공간에서 알림 수신](tutorial-facilities-events.md)

또는

> [!div class="nextstepaction"]
> [자습서: Time Series Insights를 사용하여 Azure Digital Twins 공간의 이벤트 시각화 및 분석](tutorial-facilities-analyze.md)
