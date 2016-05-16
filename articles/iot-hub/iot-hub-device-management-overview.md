<properties
 pageTitle="장치 관리 개요 | Microsoft Azure"
 description="Azure IoT Hub 장치 관리 개요: 장치 쌍, 장치 쿼리, 장치 작업"
 services="iot-hub"
 documentationCenter=""
 authors="ellenfosborne"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="04/29/2016"
 ms.author="elfarber"/>

# Azure IoT Hub 장치 관리의 개요(미리 보기)

Azure IoT Hub 장치 관리를 사용하면 표준 기반 IoT 장치 관리를 통해 원격으로 장치를 관리, 구성, 업데이트할 수 있습니다.

Azure IoT의 장치 관리에는 세 가지 주요 개념이 있습니다.

1.  **장치 쌍:** IoT Hub 내 물리적 장치의 표현입니다.

2.  **장치 쿼리**: 장치 쌍을 찾아서 장치 쌍을 이해하는 집계를 생성할 수 있도록 합니다. 예를 들어, 펌웨어 버전이 1.0인 모든 장치 쌍을 찾을 수 있습니다.

3.  **장치 작업**: 펌웨어 업데이트, 재부팅, 공장 재설정 등과 같이, 하나 이상의 물리적 장치에서 수행하기 위한 작업입니다.

## 장치 쌍

장치 쌍은 Azure IoT 내 물리적 장치의 표현입니다. **Microsoft.Azure.Devices.Device** 개체는 장치 쌍을 나타내는 데 사용됩니다.

![][img-twin]

장치 쌍의 구성 요소는 다음과 같습니다.

1.  **장치 필드:** 장치 필드는 미리 정의된 속성으로, IoT Hub 메시징 및 장치 관리에 사용됩니다. IoT Hub가 물리적인 장치를 식별하고 연결하도록 도움을 줍니다. 장치 필드는 장치에 동기화되지 않으며, 장치 쌍에 단독으로 저장됩니다. 장치 필드는 장치 ID와 인증 정보를 포함합니다.

2.  **장치 속성:** 장치 속성은 물리적인 장치를 설명하는, 미리 정의된 속성의 사전입니다. 물리적인 장치는 각 장치 속성의 마스터이며 각 해당 값에 대한 권한이 있는 저장소입니다. 이러한 속성에 대한 궁극적으로 일관적인 표현이 클라우드의 장치 쌍에 저장됩니다. 일관성 및 신선도는 [Tutorial: how to use the device twin][lnk-tutorial-twin](자습서: 장치 쌍 사용 방법)에 설명되어 있는 동기화 설정에 따라 달라집니다. 장치 속성의 예에는 펌웨어 버전, 배터리 잔량, 제조 업체 이름이 포함됩니다.

3.  **서비스 속성:** 서비스 속성은 개발자가 서비스 속성 사전에 추가하는 **&lt;key,value&gt;** 쌍입니다. 이 속성은 장치 쌍에 대한 데이터 모델을 확장하여, 장치의 특징을 잘 구분할 수 있도록 합니다. 서비스 속성은 장치에 동기화되지 않고 클라우드의 장치 쌍에 단독으로 저장됩니다. **&lt;NextServiceDate, 11/12/2017&gt;**은 서비스 속성의 한 가지 예로, 다음 서비스 날짜를 기준으로 장치를 찾는 데 사용될 수 있습니다.

4.  **태그:** 태그는 서비스 속성의 하위 집합이며, 사전 속성이 아닌 임의의 문자열입니다. 장치 쌍에 주석을 달거나 장치를 그룹으로 구성하는 데 사용할 수 있습니다. 태그는 장치에 동기화되지 않으며 장치 쌍에 단독으로 저장됩니다. 예를 들어, 장치 쌍이 물리적인 트럭을 나타내는 경우, 트럭의 각 화물 유형에 대한 태그를 추가할 수 있습니다(예: **사과**, **오렌지**, **바나나**).

## 장치 쿼리

이전 섹션에서 장치 상의 다른 구성 요소에 대해 알아보았습니다. 이제, 장치 속성, 서비스 속성 또는 태그를 기반으로 IoT Hub 장치 레지스트리에서 장치 쌍을 찾는 방법을 알아보겠습니다. 쿼리를 사용하는 경우에 대한 예에는 업데이트 할 장치를 찾는 경우가 있습니다. 지정된 펌웨어 버전이 설치된 모든 장치를 쿼리하고, 그 결과를 특정 작업(다음 섹션에 설명되어 있는, IoT Hub에서 장치 작업이라고 하는)에 공급할 수 있습니다.

태그와 속성을 사용하여 쿼리할 수 있습니다.

-   태그를 사용하여 장치 쌍을 쿼리하려면, 사용자는 문자열 배열을 전달하고 쿼리는 해당되는 모든 문자열로 태그가 지정된 장치 집합을 반환합니다.

-   서비스 속성 또는 장치 속성을 사용하여 장치 쌍을 쿼리하려면, JSON 쿼리 식을 사용합니다. 아래 예는 키가 **FirmwareVersion**이고 값이 **1.0**인 장치 속성을 포함하는 모든 장치를 쿼리하는 방법을 보여줍니다. 속성의 **type**이 **device**인 것을 볼 수 있으며, 이것은 서비스 속성이 아닌 장치 속성을 기반으로 쿼리한다는 것을 나타냅니다.

  ```
  {                           
      "filter": {                  
        "property": {                
          "name": "FirmwareVersion",   
          "type": "device"             
        },                           
        "value": "1.0",              
        "comparisonOperator": "eq",  
        "type": "comparison"         
      },                           
      "project": null,             
      "aggregate": null,           
      "sort": null                 
  }
  ```

## 장치 작업

장치 관리의 다음 개념은 장치 작업으로, 이것은 여러 장치에서 다단계 오케스트레이션을 조정할 수 있도록 합니다.

현재 Azure IoT Hub 장치 관리를 통해 제공되는 장치 작업 유형은 여섯 가지입니다.(고객이 필요로 하는 작업이 있다면 추가할 예정입니다.)

- **펌웨어 업데이트**: 물리적인 장치의 펌웨어(또는 OS 이미지)를 업데이트합니다.
- **재부팅**: 물리적인 장치를 다시 부팅합니다.
- **공장 재설정**: 물리적인 장치의 펌웨어(또는 OS 이미지)를 장치에 저장되어 있는 공장에서 제공한 백업 이미지로 되돌립니다.
- **구성 업데이트**: 물리적인 장치에서 실행되는 IoT Hub 클라이언트 에이전트를 구성합니다.
- **장치 속성 읽기**: 물리적인 장치에서 장치 속성에 대한 최신 값을 가져옵니다.
- **장치 속성 쓰기:** 물리적인 장치의 장치 속성을 변경합니다.

각 작업의 사용법에 대한 자세한 내용은 [API documentation for C# and node.js][lnk-apidocs](C# 및 node.js에 대한 API 설명서)를 참조하세요.

작업은 여러 장치에서 작동할 수 있습니다. 작업을 시작하면, 각 장치에 대해 연결된 자식 작업이 생성됩니다. 자식 작업은 단일 장치에서 작동합니다. 각 자식 작업에는 해당 부모 작업에 대한 포인터가 있습니다. 부모 작업은 자식 작업에 대한 컨테이너일 뿐이며, 장치 유형을 구별하기 위한(예: Intel Edison 업데이트와 Raspberry Pi 업데이트) 어떠한 논리도 구현하지 않습니다. 다음 다이어그램은 부모 작업, 그 자식, 및 연결된 물리적 장치 사이의 관계를 보여줍니다.

![][img-jobs]

시작한 작업의 상태를 이해하기 위해 작업 내역을 쿼리할 수 있습니다. 예제 쿼리를 보려면 [쿼리 라이브러리][lnk-query-samples]를 참조하세요.

## 장치 구현

서비스 측 개념을 살펴봤으니, 관리 기능이 있는 장치를 만드는 방법을 알아보겠습니다. 장치에 대한 Azure IoT Hub 장치 관리 클라이언트 라이브러리는 물리적인 장치와 IoT Hub 사이의 통신을 구현하기 위해 필요한 아티팩트를 제공합니다.

Azure IoT Hub DM 클라이언트 라이브러리는 [LWM2M][lnk-lwm2m] 표준 및 CoAP 기반 요청/응답 프로토콜을 추상화합니다. 따라서, 라이브러리에는 *개체* 및 *리소스 정의*에 대한 장치 모델이 있습니다.

-   개체는 시스템 내에서 서로 관련된 기능 엔터티 집합을 설명합니다(예: 장치 및 펌웨어 업데이트).
-   리소스는 그러한 개체에 포함된 특성이나 작업을 설명합니다(예: 배터리 잔량 정보 및 재부팅 작업).

Azure IoT Hub DM 클라이언트 라이브러리를 사용하는 경우에는, 물리적인 장치의 각 리소스에 대해 읽기, 쓰기, 및 실행 작업에 대한 콜백을 구현해야 합니다. 라이브러리는 속성이 변경되면 IoT Hub 비동기 업데이트를 처리합니다.

아래 다이어그램은 IoT Hub 클라이언트 허브에 필요한 다양한 구성 요소를 보여줍니다.

![][img-client]

물리적인 장치에서의 구현에 대해 자세히 알아보려면 [Introducing the Azure IoT Hub device management client library for C][lnk-library-c](C용 Azure IoT Hub 장치 관리 클라이언트 라이브러리 소개)를 참조하세요.

## 다음 단계

Azure IoT Hub 장치 관리 기능에 대해 자세히 알아보려면, 자습서를 살펴보세요.

- [Azure IoT Hub 장치 관리 시작][lnk-get-started]

- [장치 쌍을 사용하는 방법][lnk-tutorial-twin]

- [쿼리를 사용하여 장치 쌍을 찾는 방법][lnk-tutorial-queries]

- [장치 작업을 사용하여 장치 펌웨어를 업데이트하는 방법][lnk-tutorial-jobs]

<!-- Images and links -->
[img-twin]: media/iot-hub-device-management-overview/image1.png
[img-jobs]: media/iot-hub-device-management-overview/image2.png
[img-client]: media/iot-hub-device-management-overview/image3.png

[lnk-lwm2m]: http://technical.openmobilealliance.org/Technical/technical-information/release-program/current-releases/oma-lightweightm2m-v1-0
[lnk-library-c]: iot-hub-device-management-library.md
[lnk-get-started]: iot-hub-device-management-get-started.md
[lnk-tutorial-twin]: iot-hub-device-management-device-twin.md
[lnk-tutorial-queries]: iot-hub-device-management-device-query.md
[lnk-tutorial-jobs]: iot-hub-device-management-device-jobs.md
[lnk-apidocs]: http://azure.github.io/azure-iot-sdks/
[lnk-query-samples]: https://github.com/Azure/azure-iot-sdks/blob/dmpreview/doc/get_started/dm_queries/query-samples.md

<!---HONumber=AcomDC_0504_2016-->