---
title: Azure IoT Central에서 새 Azure IoT Edge 디바이스 유형 정의 | Microsoft Docs
description: 이 자습서에서는 개발자로서 Azure IoT Central 애플리케이션에서 새 Azure IoT Edge 디바이스를 만드는 방법을 보여줍니다. 유형에 대한 원격 분석, 상태, 속성 및 명령을 정의합니다.
author: rangv
ms.author: rangv
ms.date: 10/22/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: c205b4dd4871ed53e32dce72f12cc2dcfb3baf41
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/09/2019
ms.locfileid: "73892029"
---
# <a name="tutorial-define-a-new-azure-iot-edge-device-type-in-your-azure-iot-central-application-preview-features"></a>자습서: Azure IoT Central 애플리케이션에서 새 Azure IoT Edge 디바이스 유형 정의(미리 보기 기능)

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

이 자습서에서는 개발자로서 디바이스 템플릿을 사용하여 Azure IoT Central 애플리케이션에서 새로운 유형의 Azure IoT Edge 디바이스를 정의하는 방법을 보여줍니다. 

Azure IoT Edge에 대한 개요는 [이 문서를 참조하세요](overview-iot-central.md). 

Azure IoT Edge는 다음과 같은 세 가지 구성 요소로 구성됩니다.
* **IoT Edge 모듈**은 Azure 서비스, 타사 서비스 또는 개발자 고유의 코드를 실행하는 컨테이너입니다. 모듈은 IoT Edge 디바이스에 배포되어 해당 디바이스에서 로컬로 실행됩니다.
* **IoT Edge 런타임**은 각 IoT Edge 디바이스에서 실행되며 각 디바이스에 배포된 모듈을 관리합니다.
* **클라우드 기반 인터페이스**를 사용하여 IoT Edge 디바이스를 원격으로 모니터링 및 관리할 수 있습니다. IoT Central은 클라우드 인터페이스가 될 것입니다.

**Azure IoT Edge** 디바이스는 다운스트림 디바이스를 Azure IoT Edge 디바이스에 연결하는 게이트웨이 디바이스일 수 있습니다. 다운스트림 디바이스 연결 패턴은 이 자습서에서 설명합니다.

**디바이스 템플릿**은 디바이스 및 IoT Edge 모듈의 기능을 정의합니다. 기능에는 모듈에서 보내는 원격 분석 데이터, 디바이스 속성 및 디바이스에서 응답하는 명령이 포함됩니다.

이 자습서에서는 **환경 센서** 디바이스 템플릿을 만듭니다. 환경 센서 디바이스는 다음과 같습니다.

* 원격 분석(예: 온도)을 보냅니다.
* 클라우드에서 업데이트될 때 쓰기 가능한 속성(예: 원격 분석 송신 간격)에 응답합니다.
* 명령(예: 온도 재설정)에 응답합니다.

또한 이 자습서에서는 **환경 게이트웨이** 디바이스 템플릿을 만듭니다. 환경 게이트웨이 디바이스:

* 원격 분석(예: 온도)을 보냅니다.
* 클라우드에서 업데이트될 때 쓰기 가능한 속성(예: 원격 분석 송신 간격)에 응답합니다.
* 명령(예: 온도 재설정)에 응답합니다.
* 다른 디바이스 기능 모델과의 관계를 허용합니다.


이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 새 Azure IoT Edge 디바이스 템플릿 만들기
> * 배포 매니페스트 업로드
> * 각 모듈의 원격 분석, 속성 및 명령을 비롯한 기능 만들기
> * 디바이스 원격 분석에 대한 시각화 정의
> * 다운스트림 디바이스 템플릿에 관계 추가
> * 디바이스 템플릿을 게시합니다.

## <a name="prerequisites"></a>필수 조건

이 자습서를 완료하려면 Azure IoT Central 애플리케이션이 필요합니다. 이 빠른 시작에 따라 [Azure IoT Central 애플리케이션을 만듭니다](quick-deploy-iot-central.md).


## <a name="downstream-device-relationships-with-gateway--modules"></a>게이트웨이 및 모듈과의 다운스트림 디바이스 관계

다운스트림 디바이스는 $edgeHub 모듈을 통해 Azure IoT Edge 게이트웨이 디바이스에 연결할 수 있습니다. 이 시나리오의 Azure IoT Edge 디바이스는 투명 게이트웨이가 됩니다.

![Central 애플리케이션 페이지](./media/tutorial-define-edge-device-type/gateway-transparent.png)

다운스트림 디바이스는 사용자 지정 모듈을 통해 Azure IoT Edge 게이트웨이 디바이스에 연결할 수 있습니다. 아래 시나리오의 다운스트림 디바이스는 Modbus 사용자 지정 모듈을 통해 연결하며, 다운스트림 디바이스는 $edgeHub 모듈을 통해 Azure IoT Edge 게이트웨이 디바이스에 연결할 수 있습니다.

![Central 애플리케이션 페이지](./media/tutorial-define-edge-device-type/gateway-module.png)

다운스트림 디바이스는 사용자 지정 모듈을 통해 Azure IoT Edge 게이트웨이 디바이스에 연결할 수 있습니다. 아래 시나리오의 다운스트림 디바이스는 Modbus 사용자 지정 모듈을 통해 연결합니다. 

![Central 애플리케이션 페이지](./media/tutorial-define-edge-device-type/gateway-module-transparent.png)

다운스트림 디바이스는 여러 사용자 지정 모듈을 통해 Azure IoT Edge 게이트웨이 디바이스에 연결할 수 있습니다. 아래 시나리오의 다운스트림 디바이스는 Modbus 사용자 지정 모듈을 통해 연결하며, BLE 사용자 지정 모듈 및 다운스트림 디바이스는 $edgeHub 모듈을 통해 Azure IoT Edge 게이트웨이 디바이스에 연결할 수 있습니다. 

![Central 애플리케이션 페이지](./media/tutorial-define-edge-device-type/gateway-module2-transparent.png)


## <a name="create-a-template"></a>템플릿 만들기

개발자는 애플리케이션에서 Azure IoT Edge 디바이스 템플릿을 만들고 편집할 수 있습니다. 디바이스 템플릿이 게시되면 디바이스 템플릿을 구현하는 실제 디바이스를 연결할 수 있습니다.

### <a name="select-device-template-type"></a>디바이스 템플릿 유형 선택 

새 디바이스 템플릿을 애플리케이션에 추가하려면 **디바이스 템플릿** 페이지로 이동합니다. 이렇게 하려면 왼쪽 창에서 **디바이스 템플릿** 탭을 선택합니다.

![Central 애플리케이션 페이지](./media/tutorial-define-edge-device-type/edgedevicetemplate.png)

**+ 새로 만들기**를 클릭하여 새 디바이스 템플릿 만들기를 시작합니다.

![디바이스 템플릿 - 새로 만들기](./media/tutorial-define-edge-device-type/edgedevicetemplatenew.png)

디바이스 템플릿 유형 선택 페이지가 표시됩니다. **Azure IoT Edge** 타일을 선택하고, 아래쪽에서 **다음: 사용자 지정** 단추를 클릭합니다.

![디바이스 템플릿 선택 - Azure IoT Edge](./media/tutorial-define-edge-device-type/selectiotedge.png)

### <a name="customize-device-template"></a>디바이스 템플릿 사용자 지정

Azure IoT Edge를 사용하면 비즈니스 논리를 모듈 형태로 배포하고 관리할 수 있습니다. **Azure IoT Edge 모듈**은 IoT Edge가 관리하는 컴퓨팅의 최소 단위이며 Azure 서비스(예: Azure Stream Analytics) 또는 고유한 솔루션별 코드를 포함할 수 있습니다. 모듈을 개발, 배포 및 유지 관리하는 방법은 [IoT Edge 모듈](../../iot-edge/iot-edge-modules.md)을 참조하세요.

상위 수준에서 배포 매니페스트는 원하는 속성으로 구성된 모듈 쌍의 목록입니다. 배포 매니페스트는 설치할 모듈과 구성 방법을 IoT Edge 디바이스(또는 디바이스 그룹)에 알려 줍니다. 배포 매니페스트에는 각 모듈 쌍의 원하는 속성이 포함되어 있습니다. IoT Edge 디바이스는 각 모듈에 대해 보고된 속성을 다시 보고합니다.

Visual Studio Code를 사용하여 배포 매니페스트 만들기 [배포 매니페스트](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge)를 만드는 방법에 대한 설명서를 따르세요.

다음은 이 자습서에서 사용할 한 개의 모듈이 있는 기본 배포 매니페스트의 예제입니다. 아래 JSON을 복사하여 .json 파일로 저장합니다. 

   ```JSON
   {
     "modulesContent": {
       "$edgeAgent": {
         "properties.desired": {
           "schemaVersion": "1.0",
           "runtime": {
             "type": "docker",
             "settings": {
               "minDockerVersion": "v1.25",
               "loggingOptions": "",
               "registryCredentials": {}
             }
           },
           "systemModules": {
             "edgeAgent": {
               "type": "docker",
               "settings": {
                 "image": "mcr.microsoft.com/azureiotedge-agent:1.0",
                 "createOptions": "{}"
               }
             },
             "edgeHub": {
               "type": "docker",
               "status": "running",
               "restartPolicy": "always",
               "settings": {
                 "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
                 "createOptions": "{}"
               }
             }
           },
           "modules": {
             "SimulatedTemperatureSensor": {
               "version": "1.0",
               "type": "docker",
               "status": "running",
               "restartPolicy": "always",
               "settings": {
                 "image": "mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0",
                 "createOptions": "{}"
               }
             }
           }
         }
       },
       "$edgeHub": {
         "properties.desired": {
           "schemaVersion": "1.0",
           "routes": {
               "route": "FROM /* INTO $upstream"
           },
           "storeAndForwardConfiguration": {
             "timeToLiveSecs": 7200
           }
         }
       },
       "SimulatedTemperatureSensor": {
         "properties.desired": {
              "SendData": true,
              "SendInterval": 10
         }
       }
     }
   }
   ```

**Azure IoT Edge 배포 매니페스트 업로드**

**찾아보기** 단추 클릭 

![디바이스 템플릿 - Azure IoT Edge](./media/tutorial-define-edge-device-type/edgedevicetemplateuploadmanifest.png)

Azure IoT Edge 게이트웨이 디바이스 템플릿을 만들 계획인 경우 **다운스트림 디바이스를 사용하는 게이트웨이 디바이스** 확인란을 선택해야 합니다.

![디바이스 템플릿 - Azure IoT Edge](./media/tutorial-define-edge-device-type/gateway-upload.png)

파일 선택 대화 상자가 표시됩니다. 배포 매니페스트 파일을 선택하고 **열기** 단추를 클릭합니다.

스키마에 대해 배포 매니페스트 파일의 유효성이 검사됩니다. 유효성 검사에 성공하면 **검토** 단추를 클릭합니다.

![디바이스 템플릿 - Azure IoT Edge](./media/tutorial-define-edge-device-type/deploymentmanifestvalidate.png)

아래는 IoT Central의 배포 매니페스트 수명 주기 흐름입니다.

![디바이스 템플릿 - Azure IoT Edge](./media/tutorial-define-edge-device-type/dmflow.png)

검토 페이지에는 배포 매니페스트의 세부 정보가 표시됩니다. 배포 매니페스트의 모듈 목록이 검토 페이지에 표시됩니다. 이 자습서에서는 SimulatedTemperatureSensor 모듈이 나열됩니다. **만들기** 단추를 클릭합니다.

![디바이스 템플릿 - Azure IoT Edge](./media/tutorial-define-edge-device-type/edgedevicetemplatereview.png)

게이트웨이 디바이스를 선택한 경우 이 검토 페이지가 표시됩니다.

![디바이스 템플릿 - Azure IoT Edge](./media/tutorial-define-edge-device-type/gateway-review.png)


IoT Central에서 디바이스 템플릿이 만들어지는 새 디바이스 템플릿 만들기 회전자가 표시됩니다.

모듈 기능 모델을 사용하여 디바이스 템플릿이 만들어집니다. 이 자습서에서는 SimulatedTemperatureSensor 모듈 기능 모델을 만듭니다. 

디바이스 템플릿의 제목을 환경 센서 디바이스 템플릿으로 변경합니다.

![디바이스 템플릿 - Azure IoT Edge](./media/tutorial-define-edge-device-type/edgedevicetemplatelanding.png)

Azure IoT Edge 디바이스 플러그 앤 플레이 모델링은 다음과 같이 수행됩니다.
* 모든 Azure IoT Edge 디바이스 템플릿은 **디바이스 기능 모델**을 갖게 됩니다.
* 배포 매니페스트에 나열된 모든 사용자 지정 모듈에 대해 **모듈 기능 모델**이 생성됩니다.
* 각 모듈 기능 모델과 디바이스 기능 모델 간에 **관계**가 설정됩니다.
* 모듈 기능 모델은 **모듈 인터페이스**를 구현합니다.
* 각 모듈 인터페이스에는 다음이 포함됩니다.
   - 원격 분석
   - properties
   - 명령

![디바이스 템플릿 - Azure IoT Edge](./media/tutorial-define-edge-device-type/edgemodelling.png)

**모듈 기능 모델에 기능 추가**

다음은 SimulatedTemperatureSensor 모듈의 샘플 출력입니다.
```json
{

    "machine": {

        "temperature": 75.0,
        "pressure": 40.2
    },
    "ambient": {
        "temperature": 23.0,
        "humidity": 30.0
    },
    "timeCreated": ""
}
```

위의 JSON을 반영할 SimulatedTemperatureSensor 모듈에 기능을 추가합니다. 

* SimulatedTemperatureSensor 모듈 기능 모델의 인터페이스 **관리**를 클릭합니다. **기능 추가**를 클릭합니다. 

    ![디바이스 템플릿 - Azure IoT Edge](./media/tutorial-define-edge-device-type/edgetemplateaddcapability.png)
  
* 머신이 복합 형식이므로 머신을 개체 형식으로 추가합니다.
  
    ![디바이스 템플릿 - Azure IoT Edge](./media/tutorial-define-edge-device-type/edgetemplatemachineobject.png)

    **정의**를 클릭합니다. 팝업 모달에서 개체 이름을 머신으로 변경하고 속성 온도 및 압력을 만든 다음, **적용**을 클릭합니다.
  
    ![디바이스 템플릿 - Azure IoT Edge](./media/tutorial-define-edge-device-type/edgetemplatemachineattributes.png)
  
* 앰비언트가 복합 형식이므로 앰비언트를 개체 형식으로 추가합니다.

    **정의**를 클릭합니다. 팝업 모달에서 개체 이름을 앰비언트로 변경하고 속성 온도 및 습도를 만든 다음, **적용**을 클릭합니다.
  
    ![디바이스 템플릿 - Azure IoT Edge](./media/tutorial-define-edge-device-type/edgetemplateambientattributes.png)

  
* timeCreated를 DateTime 형식으로 추가하고 **저장**을 클릭합니다.
  
    ![디바이스 템플릿 - Azure IoT Edge](./media/tutorial-define-edge-device-type/edgetemplateallattributes.png)


### <a name="add-relationships"></a>관계 추가

Azure IoT Edge 디바이스를 게이트웨이 디바이스로 사용하기로 선택한 경우 게이트웨이 디바이스에 연결할 디바이스의 디바이스 기능 모델에 다운스트림 관계를 추가할 수 있습니다.
  
  ![디바이스 템플릿 - Azure IoT Edge](./media/tutorial-define-edge-device-type/gateway-add-relationship.png)

관계는 디바이스 또는 모듈에서 추가할 수 있습니다.
  
  ![디바이스 템플릿 - Azure IoT Edge](./media/tutorial-define-edge-device-type/gateway-relationship-types.png)


다운스트림 디바이스 기능 모델을 선택할 수도 있고, 별표를 선택할 수 있습니다. 
  
  ![디바이스 템플릿 - Azure IoT Edge](./media/tutorial-define-edge-device-type/gateway-downstream-rel.png)

  이 자습서에서는 모든 다운스트림 관계가 허용되는 것을 의미하는 별표를 선택하겠습니다. 페이지 맨 아래에 있는 **저장**

  ![디바이스 템플릿 - Azure IoT Edge](./media/tutorial-define-edge-device-type/gateway-add-relationship-asterix.png)


### <a name="add-cloud-properties"></a>클라우드 속성 추가

디바이스 템플릿에는 클라우드 속성이 포함될 수 있습니다. 클라우드 속성은 IoT Central 애플리케이션에만 있으며 디바이스에서 보내거나 받지 않습니다.

1. **클라우드 속성**, **+ 클라우드 속성 추가**를 차례로 선택합니다. 다음 표의 정보를 사용하여 클라우드 속성을 디바이스 템플릿에 추가합니다.

    | 표시 이름      | 의미 체계 유형 | 스키마 |
    | ----------------- | ------------- | ------ |
    | 마지막 서비스 날짜 | 없음          | Date   |
    | 고객 이름     | 없음          | 문자열 |

2. **저장**을 선택하여 변경 내용을 저장합니다.

  
    ![클라우드 속성 - Azure IoT Edge](./media/tutorial-define-edge-device-type/edgetemplatecloudproperties.png)

### <a name="add-customizations"></a>사용자 지정 추가

인터페이스를 수정하거나 디바이스 기능 모델의 버전을 관리할 필요가 없는 기능에 IoT Central 관련 기능을 추가해야 하는 경우 사용자 지정을 사용합니다. 기능 모델이 초안 또는 게시 상태이면 필드를 사용자 지정할 수 있습니다. 인터페이스 호환성이 손상되지 않는 필드만 사용자 지정할 수 있습니다. 예를 들어 다음을 수행할 수 있습니다.

- 기능의 표시 이름과 단위를 사용자 지정합니다.
- 값이 차트에 표시될 때 사용할 기본 색을 추가합니다.
- 속성의 초기 값, 최솟값 및 최댓값을 지정합니다.

기능 이름 또는 기능 유형은 사용자 지정할 수 없습니다. 페이지 맨 아래에 있는 **저장**
  
![사용자 지정 - Azure IoT Edge](./media/tutorial-define-edge-device-type/edgetemplatecustomize.png)


### <a name="create-views"></a>뷰 만들기

작성기처럼 환경 센서 디바이스와 관련된 정보를 운영자에게 표시하도록 애플리케이션을 사용자 지정할 수 있습니다. 사용자 지정을 사용하면 운영자가 애플리케이션에 연결된 환경 센서 디바이스를 관리할 수 있습니다. 운영자가 디바이스와 상호 작용하는 데 사용할 다음 두 가지 유형의 보기를 만들 수 있습니다.

* 디바이스 및 클라우드 속성을 보고 편집하는 양식
* 디바이스를 시각화하는 대시보드

### <a name="configure-a-view-to-visualize-devices"></a>디바이스를 시각화하도록 보기 구성

디바이스 대시보드를 사용하면 운영자가 차트와 메트릭을 사용하여 디바이스를 시각화할 수 있습니다. 작성기로서 디바이스 대시보드에 표시되는 정보를 정의할 수 있습니다. 디바이스에 대해 여러 개의 대시보드를 정의할 수 있습니다. 환경 센서 원격 분석을 시각화하는 대시보드를 만들려면 **보기**, **디바이스를 시각화하는 중**을 차례로 선택합니다.

  
![보기 - Azure IoT Edge](./media/tutorial-define-edge-device-type/visualizingthedevice.png)


앰비언트 원격 분석 및 머신 원격 분석은 복합 개체이므로 차트를 만들려면 다음을 수행합니다.

앰비언트 원격 분석을 끌어서 꺾은선형 차트를 선택합니다. 
  
![보기 - Azure IoT Edge](./media/tutorial-define-edge-device-type/sensorambientchart.png)

[구성] 아이콘을 클릭하고 온도 및 습도를 선택하여 데이터를 시각화하고, **구성 업데이트** 단추를 클릭합니다. 
  
![보기 - Azure IoT Edge](./media/tutorial-define-edge-device-type/sensorambienttelemetrychart.png)

**저장**을 선택하여 보기를 저장합니다.

다른 속성 또는 원격 분석 값을 표시하는 타일을 추가할 수 있습니다. 정적 텍스트, 링크 및 이미지도 추가할 수 있습니다. 대시보드에서 타일을 이동하거나 크기를 조정하려면 마우스 포인터를 타일 위로 이동하여 해당 타일을 새 위치로 끌거나 크기를 조정합니다.
  
![보기 - Azure IoT Edge](./media/tutorial-define-edge-device-type/viewsdashboard.png)

### <a name="add-a-device-form"></a>디바이스 양식 추가

디바이스 양식을 사용하면 운영자가 쓰기 가능한 디바이스 속성 및 클라우드 속성을 편집할 수 있습니다. 작성기처럼 여러 양식을 정의하고, 각 양식에 표시할 디바이스 및 클라우드 속성을 선택할 수 있습니다. 또한 읽기 전용 디바이스 속성을 양식에 표시할 수 있습니다.

환경 센서 속성을 보고 편집할 양식을 만들려면 다음을 수행합니다.

**환경 센서** 템플릿에서 **보기**로 이동합니다. **디바이스 및 클라우드 데이터 편집** 타일을 선택하여 새 보기를 추가합니다.
  
![보기 - Azure IoT Edge](./media/tutorial-define-edge-device-type/editingdeviceandclouddata.png)

**환경 센서 속성**이라는 양식 이름을 입력합니다.

**고객 이름** 및 **마지막 서비스 날짜** 클라우드 속성을 양식의 기존 섹션으로 끕니다.
  
![보기 - Azure IoT Edge](./media/tutorial-define-edge-device-type/views-properties.png)

**저장**을 선택하여 보기를 저장합니다.

### <a name="generate-default-views"></a>기본 보기 생성

기본 보기 생성 기능은 Azure IoT Edge 템플릿에서 지원되지 않습니다. 

## <a name="publish-device-template"></a>디바이스 템플릿 게시

시뮬레이션된 환경 센서를 만들거나 실제 환경 센서를 연결하려면 먼저 디바이스 템플릿을 게시해야 합니다.

디바이스 템플릿을 게시하려면 다음을 수행합니다.

1. **디바이스 템플릿** 페이지에서 디바이스 템플릿으로 이동합니다.

2. **게시**를 선택합니다.
  
    ![보기 - 게시](./media/tutorial-define-edge-device-type/edgetemplatepublish.png)

1. **디바이스 템플릿 게시** 대화 상자에서 **게시**를 선택합니다.
  
    ![보기 - 게시](./media/tutorial-define-edge-device-type/edgepublishtemplate.png)

디바이스 템플릿이 게시되면 해당 템플릿이 **디바이스** 페이지 및 운영자에게 표시됩니다. 게시된 디바이스 템플릿에서 새 버전을 만들어야 디바이스 기능 모델을 편집할 수 있습니다. 그러나 클라우드 속성, 사용자 지정 및 보기는 버전 관리를 수행하지 않고도 게시된 디바이스 템플릿에서 업데이트할 수 있습니다. 변경되면 **게시**를 선택하여 해당 변경 내용을 운영자에게 푸시합니다.
  
![보기 - 게시](./media/tutorial-define-edge-device-type/publishedtemplate.png)

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 방법에 대해 알아보았습니다.

* 리프 디바이스 템플릿으로 새 에지 만들기
* 업로드된 배포 매니페스트에서 모듈 생성
* 복합 형식 원격 분석 및 속성 추가
* 클라우드 속성을 만듭니다.
* 사용자 지정을 만듭니다.
* 디바이스 원격 분석에 대한 시각화를 정의합니다.
* 에지 디바이스 템플릿 게시

Azure IoT Central 애플리케이션에서 디바이스 템플릿을 만들었으므로 제안되는 다음 단계는 다음과 같습니다.

> [!div class="nextstepaction"]
> [디바이스 연결](./tutorial-connect-pnp-device.md)
