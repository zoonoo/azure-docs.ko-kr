---
title: 자습서 - Azure IoT Central에서 새 Azure IoT Edge 디바이스 유형 정의
description: 이 자습서에서는 개발자로서 Azure IoT Central 애플리케이션에서 새 Azure IoT Edge 디바이스를 만드는 방법을 보여줍니다. 유형에 대한 원격 분석, 상태, 속성 및 명령을 정의합니다.
author: rangv
ms.author: rangv
ms.date: 10/22/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: 97bfd2b1e8b571f44c0b782459567f5677dd36a7
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74702801"
---
# <a name="tutorial-define-a-new-azure-iot-edge-device-type-in-your-azure-iot-central-application-preview-features"></a>자습서: Azure IoT Central 애플리케이션에서 새 Azure IoT Edge 디바이스 유형 정의(미리 보기 기능)

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

이 자습서에서는 개발자로서 디바이스 템플릿을 사용하여 Azure IoT Central 애플리케이션에서 새로운 유형의 Azure IoT Edge 디바이스를 정의하는 방법을 보여줍니다. 

개요는 [Azure IoT Central(미리 보기 기능)이란?](overview-iot-central.md)을 참조하세요. 

IoT Edge는 다음 세 가지 구성 요소로 구성됩니다.
* **IoT Edge 모듈**은 Azure 서비스, 파트너 서비스 또는 사용자 고유의 코드를 실행하는 컨테이너입니다. 모듈은 IoT Edge 디바이스에 배포되며, 해당 디바이스에서 로컬로 실행됩니다.
* **IoT Edge 런타임**은 각 IoT Edge 디바이스에서 실행되며, 각 디바이스에 배포된 모듈을 관리합니다.
* **클라우드 기반 인터페이스**를 사용하여 IoT Edge 디바이스를 원격으로 모니터링 및 관리할 수 있습니다. IoT Central은 클라우드 인터페이스입니다.

**Azure IoT Edge** 디바이스는 다운스트림 디바이스가 IoT Edge 디바이스에 연결되는 게이트웨이 디바이스일 수 있습니다. 이 자습서에서는 다운스트림 디바이스 연결 패턴에 대한 자세한 정보를 공유합니다.

**디바이스 템플릿**은 디바이스 및 IoT Edge 모듈의 기능을 정의합니다. 기능에는 모듈에서 보내는 원격 분석 데이터, 디바이스 속성 및 디바이스에서 응답하는 명령이 포함됩니다.

이 자습서에서는 환경 센서 디바이스 템플릿을 만듭니다. 환경 센서 디바이스는 다음과 같습니다.

* 원격 분석(예: 온도)을 보냅니다.
* 클라우드에서 업데이트될 때 쓰기 가능한 속성(예: 원격 분석 송신 간격)에 응답합니다.
* 명령(예: 온도 재설정)에 응답합니다.

또한 이 자습서에서는 환경 게이트웨이 디바이스 템플릿을 만듭니다. 환경 게이트웨이 디바이스:

* 원격 분석(예: 온도)을 보냅니다.
* 클라우드에서 업데이트될 때 쓰기 가능한 속성(예: 원격 분석 송신 간격)에 응답합니다.
* 명령(예: 온도 재설정)에 응답합니다.
* 다른 디바이스 기능 모델과의 관계를 허용합니다.


이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 새 Azure IoT Edge 디바이스 템플릿 만들기
> * 배포 매니페스트 업로드
> * 각 모듈의 원격 분석, 속성 및 명령을 포함한 기능 만들기
> * 디바이스 원격 분석에 대한 시각화 정의
> * 다운스트림 디바이스 템플릿에 관계 추가
> * 디바이스 템플릿을 게시합니다.

## <a name="prerequisites"></a>필수 조건

이 자습서를 완료하려면 [Azure IoT Central 애플리케이션을 만들어야 합니다](quick-deploy-iot-central.md).


## <a name="downstream-device-relationships-with-a-gateway-and-modules"></a>게이트웨이 및 모듈과의 다운스트림 디바이스 관계

다운스트림 디바이스는 `$edgeHub` 모듈을 통해 IoT Edge 게이트웨이 디바이스에 연결할 수 있습니다. 이 시나리오의 IoT Edge 디바이스는 투명한 게이트웨이가 됩니다.

![투명한 게이트웨이의 다이어그램](./media/tutorial-define-edge-device-type/gateway-transparent.png)

다운스트림 디바이스는 사용자 지정 모듈을 통해 IoT Edge 게이트웨이 디바이스에 연결할 수도 있습니다. 다음 시나리오에서 다운스트림 디바이스는 Modbus 사용자 지정 모듈을 통해 연결합니다.

![사용자 지정 모듈 연결의 다이어그램](./media/tutorial-define-edge-device-type/gateway-module.png)

다음 다이어그램에서는 두 가지 유형의 모듈(사용자 지정 및 `$edgeHub`)을 통한 IoT Edge 게이트웨이 디바이스에 대한 연결을 보여 줍니다.  

![두 연결 모듈을 통한 연결의 다이어그램](./media/tutorial-define-edge-device-type/gateway-module-transparent.png)

마지막으로, 다운스트림 디바이스는 여러 사용자 지정 모듈을 통해 IoT Edge 게이트웨이 디바이스에 연결할 수 있습니다. 다음 다이어그램에서는 Modbus 사용자 지정 모듈, BLE 사용자 지정 모듈 및 `$edgeHub` 모듈을 통해 연결하는 다운스트림 디바이스를 보여 줍니다. 

![여러 사용자 지정 모듈을 통한 연결의 다이어그램](./media/tutorial-define-edge-device-type/gateway-module2-transparent.png)


## <a name="create-a-template"></a>템플릿 만들기

작성자는 애플리케이션에서 IoT Edge 디바이스 템플릿을 만들고 편집할 수 있습니다. 디바이스 템플릿이 게시되면 디바이스 템플릿을 구현하는 실제 디바이스를 연결할 수 있습니다.

### <a name="select-device-template-type"></a>디바이스 템플릿 유형 선택 

새 디바이스 템플릿을 애플리케이션에 추가하려면 왼쪽 창에서 **디바이스 템플릿**을 선택합니다.

![디바이스 템플릿이 강조 표시된 미리 보기 애플리케이션의 스크린샷](./media/tutorial-define-edge-device-type/edgedevicetemplate.png)

**+ 새로 만들기**를 선택하여 새 디바이스 템플릿 만들기를 시작합니다.

![새로 만들기가 강조 표시된 디바이스 템플릿 페이지의 스크린샷](./media/tutorial-define-edge-device-type/edgedevicetemplatenew.png)

**템플릿 유형 선택** 페이지에서 **Azure IoT Edge**를 선택하고, **다음: 사용자 지정**을 선택합니다.

![템플릿 유형 선택 페이지의 스크린샷](./media/tutorial-define-edge-device-type/selectiotedge.png)

### <a name="customize-device-template"></a>디바이스 템플릿 사용자 지정

IoT Edge에서는 비즈니스 논리를 모듈 형태로 배포하고 관리할 수 있습니다. IoT Edge 모듈은 IoT Edge에서 관리하는 가장 작은 계산 단위이며, Azure 서비스(예: Azure Stream Analytics) 또는 고유한 솔루션 관련 코드를 포함할 수 있습니다. 모듈을 개발, 배포 및 유지 관리하는 방법을 이해하려면 [IoT Edge 모듈](../../iot-edge/iot-edge-modules.md)을 참조하세요.

상위 수준에서 배포 매니페스트는 원하는 속성으로 구성된 모듈 쌍의 목록입니다. 배포 매니페스트는 설치할 모듈과 이를 구성하는 방법을 IoT Edge 디바이스(또는 디바이스 그룹)에 알려줍니다. 배포 매니페스트에는 각 모듈 쌍의 원하는 속성이 포함되어 있습니다. IoT Edge 디바이스는 각 모듈에 대해 보고된 속성을 다시 보고합니다.

Visual Studio Code를 사용하여 배포 매니페스트 만들기 자세한 내용은 [Visual Studio Code용 Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge)를 참조하세요.

다음은 이 자습서에서 사용되는 예제인 하나의 모듈이 포함된 기본 배포 매니페스트입니다. 다음 JSON을 복사하여 .json 파일로 저장합니다. 

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

#### <a name="upload-an-iot-edge-deployment-manifest"></a>IoT Edge 배포 매니페스트 업로드

**디바이스 사용자 지정** 페이지의 **Azure IoT Edge 배포 매니페스트**에서 **찾아보기**를 선택합니다. 

![찾아보기가 강조 표시된 디바이스 사용자 지정 페이지의 스크린샷](./media/tutorial-define-edge-device-type/edgedevicetemplateuploadmanifest.png)

IoT Edge 게이트웨이 디바이스 템플릿을 만들려면 **다운스트림 디바이스가 있는 게이트웨이 디바이스**를 선택해야 합니다.

![다운스트림 디바이스가 있는 게이트웨이 디바이스가 강조 표시된 디바이스 사용자 지정 페이지의 스크린샷](./media/tutorial-define-edge-device-type/gateway-upload.png)

파일 선택 대화 상자에서 배포 매니페스트 파일, **열기**를 차례로 선택합니다.

IoT Edge에서 스키마에 대해 배포 매니페스트 파일의 유효성을 검사합니다. 유효성 검사가 성공하면 **검토**를 선택합니다.

![배포 매니페스트 및 검토가 강조 표시된 디바이스 사용자 지정 페이지의 스크린샷](./media/tutorial-define-edge-device-type/deploymentmanifestvalidate.png)

다음 순서도는 IoT Central의 배포 매니페스트 수명 주기를 보여 줍니다.

![배포 매니페스트 수명 주기 순서도](./media/tutorial-define-edge-device-type/dmflow.png)

다음으로, 배포 매니페스트에 대한 세부 정보가 포함된 검토 페이지가 표시됩니다. 이 페이지에는 배포 매니페스트의 모듈 목록이 표시됩니다. 여기서는 `SimulatedTemperatureSensor` 모듈이 나열되어 있습니다. **만들기**를 선택합니다.

![모듈 및 만들기가 강조 표시된 검토 페이지의 스크린샷](./media/tutorial-define-edge-device-type/edgedevicetemplatereview.png)

게이트웨이 디바이스를 선택한 경우 다음 검토 페이지가 표시됩니다.

![Azure IoT Edge 게이트웨이가 강조 표시된 검토 페이지의 스크린샷](./media/tutorial-define-edge-device-type/gateway-review.png)


모듈 기능 모델을 사용하여 디바이스 템플릿을 만듭니다. 여기서는 `SimulatedTemperatureSensor` 모듈 기능 모델을 사용하여 디바이스 템플릿을 만듭니다. 

디바이스 템플릿의 제목을 **환경 센서 디바이스 템플릿**으로 변경합니다.

![업데이트된 제목이 강조 표시된 디바이스 템플릿의 스크린샷](./media/tutorial-define-edge-device-type/edgedevicetemplatelanding.png)

IoT Edge 디바이스에서 다음과 같이 IoT 플러그 앤 플레이를 모델링합니다.
* 모든 IoT Edge 디바이스 템플릿에는 디바이스 기능 모델이 있습니다.
* 배포 매니페스트에 나열된 모든 사용자 지정 모듈에 대한 모듈 기능 모델이 생성됩니다.
* 각 모듈 기능 모델과 디바이스 기능 모델 간의 관계가 설정됩니다.
* 모듈 기능 모델에서 모듈 인터페이스를 구현합니다.
* 각 모듈 인터페이스에는 원격 분석, 속성 및 명령이 포함됩니다.

![IoT Edge 모델링 다이어그램](./media/tutorial-define-edge-device-type/edgemodelling.png)

#### <a name="add-capabilities-to-a-module-capability-model"></a>모듈 기능 모델에 기능 추가

`SimulatedTemperatureSensor` 모듈의 샘플 출력은 다음과 같습니다.
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

`SimulatedTemperatureSensor` 모듈에 이전 출력을 반영하는 기능을 추가할 수 있습니다. 

1. `SimulatedTemperatureSensor` 모듈 기능 모델의 인터페이스를 관리하려면 **관리** > **기능 추가**를 차례로 선택합니다. 

    ![기능 추가가 강조 표시된 환경 센서 템플릿의 스크린샷](./media/tutorial-define-edge-device-type/edgetemplateaddcapability.png)
  
1. 개체 형식으로 machine을 추가합니다.
  
    ![스키마가 강조 표시된 환경 센서 템플릿 기능 페이지의 스크린샷](./media/tutorial-define-edge-device-type/edgetemplatemachineobject.png)

1. **정의**를 선택합니다. 표시되는 대화 상자에서 개체 이름을 **machine**으로 변경합니다. 온도 및 압력 속성을 만들고, **적용**을 선택합니다.
  
    ![다양한 옵션이 강조 표시된 특성 대화 상자의 스크린샷](./media/tutorial-define-edge-device-type/edgetemplatemachineattributes.png)
  
1. 개체 형식으로 **ambient**를 추가합니다.

1. **정의**를 선택합니다. 표시되는 대화 상자에서 개체 이름을 **ambient**로 변경합니다. 온도 및 습도 속성을 만들고, **적용**을 선택합니다.
  
    ![다양한 옵션이 강조 표시된 특성 대화 상자의 스크린샷](./media/tutorial-define-edge-device-type/edgetemplateambientattributes.png)

  
1. `DateTime` 형식으로 `timeCreated`를 추가하고, **저장**을 선택합니다.
  
    ![저장이 강조 표시된 환경 센서 템플릿의 스크린샷](./media/tutorial-define-edge-device-type/edgetemplateallattributes.png)


### <a name="add-relationships"></a>관계 추가

IoT Edge 디바이스를 게이트웨이 디바이스로 선택한 경우 게이트웨이 디바이스에 연결하려는 디바이스의 디바이스 기능 모델에 다운스트림 관계를 추가할 수 있습니다.
  
  ![관계 추가가 강조 표시된 환경 게이트웨이 템플릿의 스크린샷](./media/tutorial-define-edge-device-type/gateway-add-relationship.png)

관계는 디바이스 또는 모듈에서 추가할 수 있습니다.
  
  ![디바이스 및 모듈 수준 관계가 강조 표시된 환경 게이트웨이 템플릿의 스크린샷](./media/tutorial-define-edge-device-type/gateway-relationship-types.png)


다운스트림 디바이스 기능 모델을 선택하거나 별표 기호를 선택할 수 있습니다. 
  
  ![대상이 강조 표시된 환경 게이트웨이 템플릿의 스크린샷](./media/tutorial-define-edge-device-type/gateway-downstream-rel.png)

  여기서는 별표를 선택합니다. 이 옵션은 모든 다운스트림 관계를 허용합니다. 그런 다음 **저장**을 선택합니다.

  ![대상이 강조 표시된 환경 게이트웨이 템플릿의 스크린샷](./media/tutorial-define-edge-device-type/gateway-add-relationship-asterix.png)


### <a name="add-cloud-properties"></a>클라우드 속성 추가

디바이스 템플릿에는 클라우드 속성이 포함될 수 있습니다. 클라우드 속성은 IoT Central 애플리케이션에만 있으며, 디바이스에서 보내거나 받지 않습니다.

1. **클라우드 속성** >  **+ 클라우드 속성 추가**를 차례로 선택합니다. 다음 표의 정보를 사용하여 클라우드 속성을 디바이스 템플릿에 추가합니다.

    | 표시 이름      | 의미 체계 유형 | 스키마 |
    | ----------------- | ------------- | ------ |
    | 마지막 서비스 날짜 | 없음          | Date   |
    | 고객 이름     | 없음          | 문자열 |

2. **저장**을 선택합니다.

  
    ![저장이 강조 표시된 환경 센서 템플릿의 스크린샷](./media/tutorial-define-edge-device-type/edgetemplatecloudproperties.png)

### <a name="add-customizations"></a>사용자 지정 추가

사용자 지정을 사용하여 인터페이스를 수정하거나, IoT Central 관련 기능을 디바이스 기능 모델의 버전을 관리할 필요가 없는 기능에 추가합니다. 기능 모델이 초안 또는 게시 상태이면 필드를 사용자 지정할 수 있습니다. 인터페이스 호환성이 손상되지 않는 필드만 사용자 지정할 수 있습니다. 예를 들어 다음을 수행할 수 있습니다.

- 기능의 표시 이름과 단위를 사용자 지정합니다.
- 값이 차트에 표시될 때 사용할 기본 색을 추가합니다.
- 속성의 초기 값, 최솟값 및 최댓값을 지정합니다.

기능 이름 또는 기능 유형은 사용자 지정할 수 없습니다.

사용자 지정이 완료되면 **저장**을 선택합니다.
  
![환경 센서 템플릿 사용자 지정 페이지의 스크린샷](./media/tutorial-define-edge-device-type/edgetemplatecustomize.png)


### <a name="create-views"></a>뷰 만들기

작성기처럼 환경 센서 디바이스와 관련된 정보를 운영자에게 표시하도록 애플리케이션을 사용자 지정할 수 있습니다. 사용자 지정을 사용하면 운영자가 애플리케이션에 연결된 환경 센서 디바이스를 관리할 수 있습니다. 운영자가 디바이스와 상호 작용하는 데 사용할 다음 두 가지 유형의 보기를 만들 수 있습니다.

* 디바이스 및 클라우드 속성을 보고 편집하는 양식
* 디바이스를 시각화하는 대시보드

### <a name="configure-a-view-to-visualize-devices"></a>디바이스를 시각화하도록 보기 구성

디바이스 대시보드를 사용하면 운영자가 차트와 메트릭을 사용하여 디바이스를 시각화할 수 있습니다. 작성자는 디바이스 대시보드에 표시되는 정보를 정의할 수 있습니다. 디바이스에 대해 여러 개의 대시보드를 정의할 수 있습니다. 환경 센서 원격 분석을 시각화하는 대시보드를 만들려면 **보기** > **디바이스를 시각화하는 중**을 차례로 선택합니다.

  
![디바이스를 시각화하는 중이 강조 표시된 환경 센서 템플릿 보기 페이지의 스크린샷](./media/tutorial-define-edge-device-type/visualizingthedevice.png)


앰비언트 원격 분석 및 머신 원격 분석은 복합 개체입니다. 차트를 만들려면 다음을 수행합니다.

1. **앰비언트 원격 분석**을 끌어서 **꺾은선형 차트**를 선택합니다. 
  
   ![앰비언트 원격 분석과 꺾은선형 차트가 강조 표시된 환경 센서 템플릿의 스크린샷](./media/tutorial-define-edge-device-type/sensorambientchart.png)

1. 구성 아이콘을 선택합니다. **온도** 및 **습도**를 선택하여 데이터를 시각화하고, **구성 업데이트** 단추를 클릭합니다. 
  
   ![다양한 옵션이 강조 표시된 환경 센서 템플릿의 스크린샷](./media/tutorial-define-edge-device-type/sensorambienttelemetrychart.png)

1. **저장**을 선택합니다.

다른 속성 또는 원격 분석 값을 표시하는 타일을 추가할 수 있습니다. 정적 텍스트, 링크 및 이미지도 추가할 수 있습니다. 대시보드에서 타일을 이동하거나 크기를 조정하려면 마우스 포인터를 타일 위로 이동하고, 해당 타일을 새 위치로 끌거나 크기를 조정합니다.
  
![환경 센서 템플릿 대시보드 보기의 스크린샷](./media/tutorial-define-edge-device-type/viewsdashboard.png)

### <a name="add-a-device-form"></a>디바이스 양식 추가

디바이스 양식을 사용하면 운영자가 쓰기 가능한 디바이스 속성 및 클라우드 속성을 편집할 수 있습니다. 작성기처럼 여러 양식을 정의하고, 각 양식에 표시할 디바이스 및 클라우드 속성을 선택할 수 있습니다. 또한 읽기 전용 디바이스 속성을 양식에 표시할 수 있습니다.

환경 센서 속성을 보고 편집할 양식을 만들려면 다음을 수행합니다.

1. **환경 센서 템플릿**에서 **보기**로 이동합니다. **디바이스 및 클라우드 데이터 편집** 타일을 선택하여 새 보기를 추가합니다.
  
   ![디바이스 및 클라우드 데이터 편집이 강조 표시된 환경 센서 템플릿 보기 페이지의 스크린샷](./media/tutorial-define-edge-device-type/editingdeviceandclouddata.png)

1. **환경 센서 속성**이라는 양식 이름을 입력합니다.

1. **고객 이름** 및 **마지막 서비스 날짜** 클라우드 속성을 양식의 기존 섹션으로 끕니다.
  
   ![다양한 옵션이 강조 표시된 환경 센서 템플릿 보기 페이지의 스크린샷](./media/tutorial-define-edge-device-type/views-properties.png)

1. **저장**을 선택합니다.

## <a name="publish-a-device-template"></a>디바이스 템플릿 게시

시뮬레이션된 환경 센서를 만들거나 실제 환경 센서를 연결하려면 먼저 디바이스 템플릿을 게시해야 합니다.

디바이스 템플릿을 게시하려면 다음을 수행합니다.

1. **디바이스 템플릿** 페이지에서 디바이스 템플릿으로 이동합니다.

2. **게시**를 선택합니다.
  
    ![게시가 강조 표시된 환경 센서 템플릿의 스크린샷](./media/tutorial-define-edge-device-type/edgetemplatepublish.png)

1. **디바이스 템플릿 게시** 대화 상자에서 **게시**를 선택합니다.
  
    ![게시가 강조 표시된 디바이스 템플릿 게시 대화 상자의 스크린샷](./media/tutorial-define-edge-device-type/edgepublishtemplate.png)

디바이스 템플릿이 게시되면 해당 템플릿이 **디바이스** 페이지 및 운영자에게 표시됩니다. 게시된 디바이스 템플릿에서 새 버전을 만들어야 디바이스 기능 모델을 편집할 수 있습니다. 그러나 클라우드 속성, 사용자 지정 및 보기는 게시된 디바이스 템플릿에서 업데이트할 수 있습니다. 이러한 업데이트로 인해 새 버전을 만들지는 않습니다. 변경되면 **게시**를 선택하여 해당 변경 내용을 운영자에게 푸시합니다.
  
![게시된 템플릿의 디바이스 템플릿 목록에 대한 스크린샷](./media/tutorial-define-edge-device-type/publishedtemplate.png)

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 방법에 대해 알아보았습니다.

* 새 에지를 리프 디바이스 템플릿으로 만듭니다.
* 업로드된 배포 매니페스트에서 모듈을 생성합니다.
* 복합 형식 원격 분석 및 속성을 추가합니다.
* 클라우드 속성을 만듭니다.
* 사용자 지정을 만듭니다.
* 디바이스 원격 분석에 대한 시각화를 정의합니다.
* 에지 디바이스 템플릿 게시

이제 Azure IoT Central 애플리케이션에서 디바이스 템플릿을 만들었으므로 다음을 수행할 수 있습니다.

> [!div class="nextstepaction"]
> [디바이스 연결](./tutorial-connect-pnp-device.md)
