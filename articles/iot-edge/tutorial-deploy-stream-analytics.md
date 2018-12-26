---
title: 자습서 - 디바이스에 Azure Stream Analytics 작업 배포 - Azure IoT Edge | Microsoft Docs
description: 이 자습서에서는 Iot Edge 디바이스에 Azure Stream Analytics를 모듈로 배포합니다.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 09/21/2018
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc, seodec18
ms.openlocfilehash: 0096a7a57cb4a404f5c8e36d8b69eac2c20c1fab
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/10/2018
ms.locfileid: "53139814"
---
# <a name="tutorial-deploy-azure-stream-analytics-as-an-iot-edge-module"></a>자습서: Azure Stream Analytics를 IoT Edge 모듈로 배포

많은 IoT 솔루션에서는 분석 서비스를 사용하여 IoT 디바이스에서 클라우드에 도착하는 대로 데이터에 대한 인사이트를 얻습니다. Azure IoT Edge를 사용하면 [Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/) 논리를 가져와서 디바이스 자체로 이동할 수 있습니다. 에지 장치에서 원격 분석 스트림을 처리함으로써 업로드되는 데이터의 양을 줄이고 실행 가능한 인사이트에 대응하는 데 걸리는 시간을 단축할 수 있습니다.

Azure IoT Edge와 Azure Stream Analytics가 통합되어 Azure Portal에서 Azure Stream Analytics 작업을 만든 다음, 추가 코드 없이 IoT Edge 모듈로 배포할 수 있습니다.  

ASA(Azure Stream Analytics)는 클라우드 및 IoT Edge 디바이스 둘 다에서 데이터 분석을 위한 풍부하게 구조화된 쿼리 구문을 제공합니다. IoT Edge의 Azure Stream Analytics에 대한 자세한 내용은 [Azure Stream Analytics 설명서](../stream-analytics/stream-analytics-edge.md)를 참조하세요.

이 자습서의 Stream Analytics 모듈은 순환하는 30초 시간 범위에 걸친 평균 온도를 계산합니다. 이 평균 온도가 70에 도달하면 모듈은 디바이스에서 조치를 취하도록 경고를 보냅니다. 이 경우, 그 동작은 시뮬레이션된 온도 센서를 다시 설정하는 것입니다. 프로덕션 환경에서 이 기능을 사용하여 온도가 위험 수준에 도달하면 컴퓨터를 종료하거나 예방 조치를 취할 수 있습니다. 

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 에지에서 데이터를 처리하는 Azure Stream Analytics 작업을 만듭니다.
> * 새 Azure Stream Analytics 작업을 다른 IoT Edge 모듈에 연결합니다.
> * Azure Portal에서 Azure Stream Analytics 작업을 IoT Edge 디바이스에 배포합니다.

<center>
![다이어그램 - ASA 작업 아키텍처, 스테이징 및 배포 자습서](./media/tutorial-deploy-stream-analytics/asa-architecture.png)
</center>


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>필수 조건

Azure IoT Edge 장치:

* [Linux](quickstart-linux.md) 또는 [Windows 장치](quickstart.md)의 빠른 시작에 설명된 단계에 따라 개발 머신 또는 가상 머신을 Edge 장치로 사용할 수 있습니다.

클라우드 리소스:

* Azure의 무료 또는 표준 계층 [IoT Hub](../iot-hub/iot-hub-create-through-portal.md). 


## <a name="create-an-azure-stream-analytics-job"></a>Azure Stream Analytics 작업 만들기

이 섹션에서는 IoT Hub에서 데이터를 가져오고, 디바이스에서 전송된 원격 분석 데이터를 쿼리하고, 결과를 Azure Blob Storage 컨테이너로 전달하기 위한 Azure Stream Analytics 작업을 만듭니다. 

### <a name="create-a-storage-account"></a>저장소 계정 만들기

IoT Edge 디바이스에서 실행되는 Azure Stream Analytics 작업을 만들 때 디바이스에서 호출할 수 있는 방식으로 작업을 저장해야 합니다. 기존 Azure 저장소 계정을 사용해도 되고, 지금 새로 만들어도 됩니다. 

1. Azure Portal에서 **리소스 만들기** > **Storage** > **Storage 계정 - BLOB, 파일, 테이블, 큐**로 이동합니다. 

1. 다음 값을 입력하여 저장소 계정을 만듭니다.

   | 필드 | 값 |
   | ----- | ----- |
   | 이름 | 저장소 계정의 고유한 이름을 입력합니다. | 
   | 위치 | 가까운 위치를 선택합니다. |
   | 구독 | IoT Hub와 동일한 구독을 선택합니다. |
   | 리소스 그룹 | IoT Edge 빠른 시작 및 자습서에서 만드는 모든 테스트 리소스에 동일한 리소스 그룹을 사용하는 것이 좋습니다. 예를 들어 **IoTEdgeResources**를 사용합니다. |

1. 다른 필드는 기본값을 유지하고 **만들기**를 선택합니다. 

### <a name="create-a-new-job"></a>새 작업 만들기

1. Azure Portal에서 **리소스 만들기** > **사물 인터넷** > **Stream Analytics 작업**으로 이동합니다.

1. 다음 값을 입력하여 작업을 만듭니다.

   | 필드 | 값 |
   | ----- | ----- |
   | 작업 이름 | 작업의 이름을 입력합니다. 예: **IoTEdgeJob** | 
   | 구독 | IoT Hub와 동일한 구독을 선택합니다. |
   | 리소스 그룹 | IoT Edge 빠른 시작 및 자습서에서 만드는 모든 테스트 리소스에 동일한 리소스 그룹을 사용하는 것이 좋습니다. 예를 들어 **IoTEdgeResources**를 사용합니다. |
   | 위치 | 가까운 위치를 선택합니다. | 
   | 호스팅 환경 | **Edge**를 선택합니다. |
 
1. **만들기**를 선택합니다.

### <a name="configure-your-job"></a>작업 구성

Azure Portal에서 Stream Analytics 작업을 만든 후에는 통과하는 데이터에 대해 실행되는 입력, 출력 및 쿼리를 사용하여 작업을 구성할 수 있습니다. 

이 섹션에서는 입력, 출력, 쿼리의 세 가지 요소를 사용하여 IoT Edge 디바이스에서 온도 데이터를 수신하는 작업을 만듭니다. 이 작업은 순환하는 30초 동안의 데이터를 분석합니다. 이 시간의 평균 온도가 70도를 초과하면 IoT Edge 디바이스로 경고가 전송됩니다. 그 다음 섹션에서는 작업을 배포할 때 데이터가 들어오고 나오는 위치를 정확하게 지정할 것입니다.  

1. Azure Portal에서 Stream Analytics 작업으로 이동합니다. 

1. **작업 토폴로지**에서 **입력**, **스트림 입력 추가**를 차례로 선택합니다.

   ![Azure Stream Analytics 입력 추가](./media/tutorial-deploy-stream-analytics/asa_input.png)

1. 드롭다운 목록에서 **Edge Hub**를 선택합니다.

1. **새 입력** 창에 입력 별칭으로 **온도**를 입력합니다. 

1. 다른 필드는 기본값을 유지하고 **저장**을 선택합니다.

1. **작업 토폴로지**에서 **출력**을 열고 **추가**를 선택합니다.

   ![Azure Stream Analytics 출력 추가](./media/tutorial-deploy-stream-analytics/asa_output.png)

1. 드롭다운 목록에서 **Edge Hub**를 선택합니다.

1. **새 출력** 창에서 출력 별칭으로 **경고**를 입력합니다. 

1. 다른 필드는 기본값을 유지하고 **저장**을 선택합니다.

1. **작업 토폴로지**에서 **쿼리**를 선택합니다.

1. 기본 텍스트를 다음 쿼리로 바꿉니다. 30초 시간의 평균 머신 온도가 70도에 도달하면 SQL 코드는 출력 경고에 다시 설정 명령을 보냅니다. 다시 설정 명령은 센서에 수행 가능한 작업으로 미리 프로그래밍되어 있습니다. 

    ```sql
    SELECT  
        'reset' AS command 
    INTO 
       alert 
    FROM 
       temperature TIMESTAMP BY timeCreated 
    GROUP BY TumblingWindow(second,30) 
    HAVING Avg(machine.temperature) > 70
    ```

1. **저장**을 선택합니다.

### <a name="configure-iot-edge-settings"></a>IoT Edge 설정 구성

IoT Edge 디바이스에 배포할 Stream Analytics 작업을 준비하려면 작업을 저장소 계정의 컨테이너에 연결해야 합니다. 작업을 배포하려고 이동하면 작업 정의가 저장소 컨테이너로 내보내집니다. 

1. **구성**에서 **저장소 계정 설정**을 선택합니다.

1. **저장소 계정 추가**를 선택합니다. 

1. 드롭다운 메뉴에서 **저장소 계정**을 선택합니다.

1. **컨테이너** 필드에 대해 **새로 만들기**를 선택하고 저장소 컨테이너에 대한 이름을 제공합니다. 

1. **저장**을 선택합니다. 

## <a name="deploy-the-job"></a>작업 배포

이제 IoT Edge 디바이스에 Azure Stream Analytics 작업을 배포할 준비가 되었습니다. 

이 섹션에서는 Azure Portal의 **모듈 설정** 마법사를 사용하여 *배포 매니페스트*를 만듭니다. 배포 매니페스트는 디바이스에 배포될 모든 모듈, 모듈 이미지를 저장하는 컨테이너 레지스트리, 모듈을 관리하는 방법, 모듈이 서로 통신하는 방법을 설명하는 JSON 파일입니다. IoT Edge 디바이스는 IoT Hub에서 배포 매니페스트를 수신한 다음, 그 안에 들어 있는 정보를 사용하여 할당된 모든 모듈을 배포하고 구성합니다. 

이 자습서에서는 두 개의 모듈을 배포합니다. 첫 번째는 온도 및 습도 센서를 시뮬레이션하는 **tempSensor** 모듈입니다. 두 번째는 Stream Analytics 작업입니다. 센서 모듈은 작업 쿼리에서 분석할 데이터 스트림을 제공합니다. 

1. Azure Portal의 IoT 허브에서 **IoT Edge**로 이동한 다음, IoT Edge 디바이스의 세부 정보 페이지를 엽니다.

1. **모듈 설정**을 선택합니다.  

1. 이전에 이 디바이스에 tempSensor 모듈을 배포한 경우 자동으로 입력될 수 있습니다. 자동으로 입력되지 않으면 다음 단계에 따라 모듈을 추가합니다.

   1. **추가**를 클릭하고 **IoT Edge 모듈**을 선택합니다.
   1. 이름으로 **tempSensor**를 입력합니다.
   1. 이미지 URI에 대해 **mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0**을 입력합니다. 
   1. 다른 설정은 변경하지 않고 **저장**을 선택합니다.

1. 다음 단계에 따라 Azure Stream Analytics Edge 작업을 추가합니다.

   1. **추가**를 클릭하고 **Azure Stream Analytics 모듈**을 선택합니다.
   1. 구독 및 사용자가 만든 Azure Stream Analytics Edge 작업을 선택합니다. 
   1. **저장**을 선택합니다.

1. 앞에서 만든 저장소 컨테이너에 Stream Analytics 작업이 게시되면 모듈 이름을 클릭하여 Stream Analytics 모듈이 어떻게 구성되어 있는지 살펴봅니다. 

   이미지 URI는 표준 Azure Stream Analytics 이미지를 가리킵니다. IoT Edge 디바이스에 배포되는 모든 작업에 사용되는 이미지와 동일한 이미지입니다. 

   모듈 쌍은 **ASAJobInfo**라고 하는 desired 속성을 사용하여 구성됩니다. 이 속성의 값은 저장소 컨테이너의 작업 정의를 가리킵니다. 이 속성은 특정 작업 정보를 사용하여 Stream Analytics 이미지를 구성하는 방법을 지정합니다. 

1. 모듈 페이지를 닫습니다.

1. 그 다음 단계에서 필요하므로 Stream Analytics 모듈 이름을 적어 두고 **다음**을 선택하여 계속 진행합니다.

1. **경로**의 기본값을 다음 코드로 바꿉니다. _{moduleName}_ 의 세 인스턴스를 Azure Stream Analytics 모듈의 이름으로 업데이트합니다. 

    ```json
    {
        "routes": {
            "telemetryToCloud": "FROM /messages/modules/tempSensor/* INTO $upstream",
            "alertsToCloud": "FROM /messages/modules/{moduleName}/* INTO $upstream",
            "alertsToReset": "FROM /messages/modules/{moduleName}/* INTO BrokeredEndpoint(\"/modules/tempSensor/inputs/control\")",
            "telemetryToAsa": "FROM /messages/modules/tempSensor/* INTO BrokeredEndpoint(\"/modules/{moduleName}/inputs/temperature\")"
        }
    }
    ```

   여기에 선언하는 경로는 IoT Edge 디바이스를 통과하는 데이터 흐름을 정의합니다. tempSensor의 원격 분석 데이터는 IoT Hub 및 Stream Analytics 작업에서 구성한 **온도** 입력으로 전송됩니다. **경고** 출력 메시지는 IoT Hub 및 tempSensor 모듈로 전송되어 다시 설정 명령을 트리거합니다. 

1. **다음**을 선택합니다.

1. **배포 검토** 단계에서 **제출**을 선택합니다.

1. 디바이스 세부 정보 페이지로 돌아가서 **새로 고침**을 선택합니다.  

    IoT Edge 에이전트 모듈 및 IoT Edge Hub와 함께 실행되는 새로운 Stream Analytics 모듈이 표시됩니다.

    ![디바이스에서 보고하는 tempSensor 및 ASA 모듈](./media/tutorial-deploy-stream-analytics/module_output2.png)

## <a name="view-data"></a>데이터 보기

이제 IoT Edge 디바이스로 가서 Azure Stream Analytics 모듈과 tempSensor 모듈 간의 상호 작용을 확인할 수 있습니다.

1. Docker에서 모든 모듈이 실행되는지 확인합니다.

   ```cmd/sh
   iotedge list  
   ```
<!--
   ![Docker output](./media/tutorial-deploy-stream-analytics/docker_output.png)
-->
1. 모든 시스템 로그 및 메트릭 데이터를 봅니다. Stream Analytics 모듈 이름을 사용합니다.

   ```cmd/sh
   iotedge logs -f {moduleName}  
   ```

30초 동안 70도에 도달할 때까지 컴퓨터의 온도가 점차 상승하는 것을 볼 수 있어야 합니다. 그러면 Stream Analytics 모듈이 재설정을 트리거하고 컴퓨터 온도가 21도로 떨어집니다. 

   ![모듈 로그에 명령 출력 다시 설정](./media/tutorial-deploy-stream-analytics/docker_log.png)

## <a name="clean-up-resources"></a>리소스 정리 

권장되는 다음 문서를 계속 진행하려는 경우 만든 리소스와 구성을 그대로 유지하고 다시 사용할 수 있습니다. 테스트 장치와 동일한 IoT Edge 장치를 계속 사용해도 됩니다. 

그렇지 않은 경우 요금 청구를 방지하도록 이 문서에서 만든 로컬 구성 및 Azure 리소스를 삭제할 수 있습니다. 
 
[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

[!INCLUDE [iot-edge-clean-up-local-resources](../../includes/iot-edge-clean-up-local-resources.md)]


## <a name="next-steps"></a>다음 단계

이 자습서에서는 IoT Edge 디바이스의 데이터를 분석하도록 Azure Stream Analytics 작업을 구성했습니다. 그런 다음, 이 Azure Stream Analytics 모듈을 IoT Edge 디바이스에 로드하여 온도 상승을 로컬에서 처리하고, 이에 대응하며, 집계된 데이터 스트림을 클라우드로 보냈습니다. Azure IoT Edge에서 추가 업무용 솔루션을 만드는 방법을 확인하려는 경우 다른 자습서를 계속 진행할 수 있습니다.

> [!div class="nextstepaction"] 
> [Azure Machine Learning 모델을 모듈로 배포](tutorial-deploy-machine-learning.md)
