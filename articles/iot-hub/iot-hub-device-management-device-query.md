<properties
	pageTitle="IoT Hub 장치 관리 쌍 쿼리 | Microsoft Azure"
	description="쿼리를 사용하여 장치 쌍을 찾는 방법을 설명하는 Azure IoT Hub 장치 관리 자습서."
	services="iot-hub"
	documentationCenter=".net"
	authors="juanjperez"
	manager="timlt"
	editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="dotnet"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="04/29/2016"
 ms.author="juanpere"/>

# 자습서: C#으로 쿼리를 사용하여 쌍 장치를 찾는 방법(미리 보기)

[AZURE.INCLUDE [iot-hub-device-management-query-selector](../../includes/iot-hub-device-management-query-selector.md)]

Azure IoT 장치 관리를 통해 쿼리를 사용하여 물리적 장치의 서비스 표시인 장치 쌍을 찾을 수 있습니다. 장치 속성, 서비스 속성 또는 장치 쌍의 태그를 기반으로 쿼리할 수 있습니다. 태그와 속성을 사용하여 쿼리할 수 있습니다.

-   태그를 사용하여 장치 쌍을 쿼리하려면 문자열 배열을 전달합니다. 그러면 쿼리가 해당하는 모든 문자열로 태그가 지정된 장치 집합을 반환합니다.

-   서비스 속성 또는 장치 속성을 사용하여 장치 쌍을 쿼리하려면 JSON 쿼리 식을 사용합니다.

장치 쌍 및 쿼리에 대한 자세한 내용은 [Azure IoT Hub 장치 관리 개요][lnk-dm-overview]를 참조하세요.

## 장치 쿼리 샘플 실행

다음 샘플은 [Azure IoT Hub 장치 관리 시작][lnk-get-started] 자습서 기능을 확장합니다. 먼저 여러 가지 시뮬레이션된 장치를 실행하고 쿼리를 사용하여 특정 장치를 찾습니다.

### 필수 조건 

이 샘플을 실행하기 전에 [Azure IoT Hub 장치 관리 시작][lnk-get-started]의 단계를 완료했어야 합니다. 즉, 시뮬레이션된 장치가 실행 중이어야 합니다. 이전에 프로세스를 완료한 경우 시뮬레이션된 장치를 지금 다시 시작하세요.

### 샘플 시작

샘플을 시작하려면 **Query.exe** 프로세스를 실행해야 합니다. 그러면 몇 가지 쿼리가 실행됩니다. 아래 단계에 따라 샘플을 시작하세요.

1.  시뮬레이션된 장치가 실행될 때까지 최소 1 분 이상 기다리세요. 이렇게 하면 쌍의 장치 속성이 물리적 장치와 확실히 동기화됩니다. 동기화에 대한 자세한 내용은 [자습서: 장치 쌍을 사용하는 방법][lnk-twin-tutorial]을 참조하세요.

2.  **azure-iot-sdks** 리포지토리를 복제한 루트 폴더에서 **azure-iot-sdks\\csharp\\service\\samples\\bin** 폴더로 이동합니다.

3.  `Query.exe <IoT Hub Connection String>` 실행

태그, 서비스 속성 및 장치 속성을 사용하여 장치 개체에 대한 쿼리 결과를 보여 주는 명령줄 창의 출력이 표시됩니다.

## 쿼리 구조(JSON)

장치 속성 및 서비스 속성에 대한 쿼리는 쿼리 자체를 표현하는 JSON 문자열을 사용하여 실행됩니다. JSON 문자열은 4개 부분으로 구성됩니다. 다음은 각 부분 및 올바른 JSON 문자열로 직렬화할 수 있는 C# 개체에 대한 설명입니다.

- **Project**: 쿼리 결과 집합에 포함할 장치 개체의 필드를 지정하는 식(SQL의 SELECT와 같음):

  ```
	  var query = JsonConvert.SerializeObject(
		  new
		  {
			  project = new
			  {
				  all = false,
				  properties = new[]
				  {
					  new
					  {
					  name = "CustomerId",
					  type = "service"
					  },
					  new
					  {
					  name = "Weight",
					  type = "service"
					  }
				  }
			  }
		  }
	  );
	```

- **Filter**: 쿼리 결과 집합에 포함되는 장치 개체를 제한하는 식(SQL의 WHERE와 같음):

  ```
  var query = JsonConvert.SerializeObject(
      new
      {
        filter = new
        {
          property = new
          {
            name = "CustomerId",
            type = "service"
          },
          value = "123456",
          comparisonOperator = "eq",
          type = "comparison"
        }
      }
  );
  ```

- **Aggregate**: 쿼리 결과 집합을 그룹화하는 방법을 결정하는 식(SQL의 GROUPBY와 같음).

  ```
  var query = JsonConvert.SerializeObject(
      new
      {
      filter = new
        {
          property = new
          {
            name = "CustomerId",
            type = "service"
          },
          value = (string)null,
          comparisonOperator = "ne",
          type = "comparison"
        },
        aggregate = new
        {
          keys = new[]
          {
            new
            {
              name = "CustomerId",
              type = "service"
            }
          },
          properties = new[]
          {
            new
            {
              @operator = "avg",
              property = new
              {
                name = "Weight",
                type = "service"
              },
              columnName = "TotalWeight"
            }
          }
        }
      }
  );
  ```

- **Sort**: 쿼리 결과 집합을 정렬하는 데 사용해야 하는 속성을 정의하는 식(SQL의 ORDER BY와 같음). Sort가 null인 경우 **deviceID**가 기본적으로 사용됩니다.

  ```
  var query = JsonConvert.SerializeObject(
    new
    {
      sort = new[]
      {
        new
        {
          property = new
          {
            name = "QoS",
            type = "service"
          },
          order = "asc"
        }
      }
    }
  );
  ```

### 제한 사항

쿼리에 대한 공용 미리 보기 구현에는 몇 가지 제한이 있습니다.

-   쿼리 식에 대한 유효성을 검사하지 않습니다.

-   쿼리는 대/소문자를 구분합니다.

-   쿼리 식을 사용하여 서비스 또는 장치 속성별로 쿼리하는 경우 장치 100개만 반환됩니다. 페이징을 구현하는 방법의 예제는 [쿼리 라이브러리][lnk-query-samples]에서 사용할 수 있습니다.

JSON의 구문 및 사용 가능한 필드에 대한 자세한 내용을 [사용할 수 있습니다][lnk-query-expression-guide]. [쿼리 식 라이브러리][lnk-query-samples]에서 샘플 쿼리도 볼 수 있습니다.

### 장치 및 서비스 속성별로 쿼리

JSON 쿼리 식이 있으면 장치 쌍을 쿼리할 수 있습니다. **QueryDeviceJsonAsync**를 호출하고 집계 쿼리의 경우 **AggregateResult** 필드 및 모든 다른 쿼리의 경우 **Result** 필드를 확인합니다. **Result**에는 쿼리와 일치하는 장치 쌍을 표시하는 장치 개체의 목록이 포함됩니다. **AggregateResult**에는 각각 결과 행이 포함된 사전 배열이 포함됩니다.

이러한 쿼리는 **Query** 프로젝트의 **Program.cs**에 사용됩니다.

```
var foundDevices = (await registryManager.QueryDevicesJsonAsync(query)).Result;

var results = (await registryManager.QueryDevicesJsonAsync(query)).AggregateResult;
```

### 태그별로 쿼리

태그별로 쿼리를 통해 JSON 쿼리 식을 사용하지 않고 장치 개체를 찾을 수 있습니다. 둘 이상의 태그를 전달하는 경우 모든 태그가 있는 장치 개체만 반환됩니다. 두 번째 매개 변수는 반환해야 할 장치의 최대 수인 **maxCount**입니다. **maxCount**에 대한 최대값은 1000개입니다.

```
var foundDevices = await registryManager.QueryDevicesAsync(new[] { "bacon" }, 100);
```

### 장치 구현

쿼리는 Azure IoT Hub 장치 관리 클라이언트 라이브러리에서 사용하도록 설정됩니다. 장치 속성이 동기화되었다면([자습서: 장치 쌍을 사용하는 방법][lnk-twin-tutorial]에서 설명한 대로) 해당 속성에 대해 쿼리할 수 있습니다. 장치 속성은 물리적 장치가 IoT Hub에 연결하고 초기값을 제공한 후에만 사용할 수 있습니다.

## 다음 단계

Azure IoT Hub 장치 관리 기능에 대해 자세히 알아보려면 이 자습서를 차례로 실행해 볼 수 있습니다.

- [장치 쌍을 사용하는 방법][lnk-twin-tutorial]
- [장치 작업을 사용하여 장치 펌웨어를 업데이트하는 방법][lnk-jobs-tutorial]



<!-- images and links -->
[lnk-dm-overview]: iot-hub-device-management-overview.md
[lnk-get-started]: iot-hub-device-management-get-started.md
[lnk-twin-tutorial]: iot-hub-device-management-device-twin.md
[lnk-jobs-tutorial]: iot-hub-device-management-device-jobs.md
[lnk-query-spec]: https://github.com/Azure/azure-iot-sdks/blob/dmpreview/node/service/devdoc/query_expression_requirements.md
[lnk-query-samples]: https://github.com/Azure/azure-iot-sdks/blob/dmpreview/doc/get_started/dm_queries/query-samples.md
[lnk-query-expression-guide]: https://github.com/Azure/azure-iot-sdks/blob/dmpreview/node/service/devdoc/query_expression_requirements.md

<!---HONumber=AcomDC_0622_2016-->