---
title: 자습서 - Azure IoT Edge 장치에 ASA 작업 배포 | Microsoft Docs
description: Edge 장치에 Azure Stream Analytics를 모듈로 배포합니다.
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 06/25/2018
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: ad2895a457a20632823260f2429ac95fad82089c
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/27/2018
ms.locfileid: "37060198"
---
# <a name="tutorial-deploy-azure-stream-analytics-as-an-iot-edge-module---preview"></a>자습서: Azure Stream Analytics를 IoT Edge 모듈로 배포 - 미리 보기

많은 IoT 솔루션에서는 분석 서비스를 사용하여 IoT 장치에서 클라우드에 도착하는 대로 데이터에 대한 인사이트를 얻습니다. Azure IoT Edge를 사용하면 [Azure Stream Analytics][azure-stream] 논리를 가져와서 장치 자체로 이동할 수 있습니다. 에지 장치에서 원격 분석 스트림을 처리함으로써 업로드되는 데이터의 양을 줄이고 실행 가능한 인사이트에 대응하는 데 걸리는 시간을 단축할 수 있습니다.

Azure IoT Edge와 Azure Stream Analytics가 통합되어 Azure Portal에서 Azure Stream Analytics 작업을 만든 다음, 추가 코드 없이 IoT Edge 모듈로 배포할 수 있습니다.  

ASA(Azure Stream Analytics)는 클라우드 및 IoT Edge 장치 둘 다에서 데이터 분석을 위한 풍부하게 구조화된 쿼리 구문을 제공합니다. IoT Edge의 Azure Stream Analytics에 대한 자세한 내용은 [Azure Stream Analytics 설명서](../stream-analytics/stream-analytics-edge.md)를 참조하세요.

이 자습서의 Stream Analytics 모듈은 순환하는 30초 시간 범위에 걸친 평균 온도를 계산합니다. 이 평균 온도가 70에 도달하면 모듈은 장치에서 조치를 취하도록 경고를 보냅니다. 이 경우, 그 동작은 시뮬레이션된 온도 센서를 다시 설정하는 것입니다. 프로덕션 환경에서 이 기능을 사용하여 온도가 위험 수준에 도달하면 컴퓨터를 종료하거나 예방 조치를 취할 수 있습니다. 

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 에지에서 데이터를 처리하는 Azure Stream Analytics 작업을 만듭니다.
> * 새 Azure Stream Analytics 작업을 다른 IoT Edge 모듈에 연결합니다.
> * Azure Portal에서 Azure Stream Analytics 작업을 IoT Edge 장치에 배포합니다.

>[!NOTE]
>IoT Edge용 Azure Stream Analytics 모듈은 [공개 미리 보기](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)로 있습니다.

## <a name="prerequisites"></a>필수 조건

* IoT 허브
* [Windows][lnk-quickstart-win] 또는 [Linux][lnk-quickstart-lin]용 빠른 시작에서 만들고 구성한 IoT Edge 장치 

## <a name="create-an-azure-stream-analytics-job"></a>Azure Stream Analytics 작업 만들기

이 섹션에서는 IoT Hub에서 데이터를 가져오고, 장치에서 전송된 원격 분석 데이터를 쿼리하고, 결과를 Azure Blob Storage 컨테이너로 전달하기 위한 Azure Stream Analytics 작업을 만듭니다. 자세한 내용은 [Stream Analytics 설명서][azure-stream]의 “개요” 섹션을 참조하세요. 

### <a name="create-a-storage-account"></a>저장소 계정 만들기

Azure Stream Analytics 작업에서 작업 출력에 대한 엔드포인트 역할을 수행하려면 Azure Storage 계정이 필요합니다. 이 섹션의 예제에서는 BLOB Storage 유형을 사용합니다. 자세한 내용은 [Azure Storage 설명서][azure-storage]의 “Blob” 섹션을 참조하세요.

1. Azure Portal에서 **리소스 만들기**로 이동한 후 검색 상자에 **저장소 계정**을 입력하고 **저장소 계정 - Blob, 파일, 테이블, 큐**를 선택합니다.

2. **저장소 계정 만들기** 창에 저장소 계정의 이름을 입력하고, IoT Hub가 저장된 위치와 동일한 위치를 선택하고, IoT Hub와 동일한 리소스 그룹을 선택한 다음, **만들기**를 선택합니다. 나중에 사용할 수 있게 이름을 적어둡니다.

    ![저장소 계정 만들기][1]


### <a name="create-a-stream-analytics-job"></a>Stream Analytics 작업 만들기

1. Azure Portal에서 **리소스 만들기** > **사물 인터넷**으로 이동한 후 **Stream Analytics 작업**을 선택합니다.

2. **새 Stream Analytics 작업** 창에서 다음 단계를 수행합니다.

   1. **작업 이름** 상자에 작업 이름을 입력합니다.
   
   2. IoT 허브와 동일한 **리소스 그룹**과 **위치**를 사용합니다. 

      > [!NOTE]
      > 현재 IoT Edge의 Azure Stream Analytics 작업은 미국 서부 2 지역에서 지원되지 않습니다. 

   3. **호스팅 환경** 아래에서 **에지**를 선택합니다.
    
3. **만들기**를 선택합니다.

4. 만든 작업의 **작업 토폴로지** 아래에서 **입력**을 엽니다.

   ![Azure Stream Analytics 입력](./media/tutorial-deploy-stream-analytics/asa_input.png)

5. **스트림 입력 추가**를 선택하고 **Edge Hub**를 선택합니다.

6. **새 입력** 창에 입력 별칭으로 **온도**를 입력합니다. 

7. **저장**을 선택합니다.

8. **작업 토폴로지**에서 **출력**을 엽니다.

   ![Azure Stream Analytics 출력](./media/tutorial-deploy-stream-analytics/asa_output.png)

9. **추가**를 선택하고 **Edge Hub**를 선택합니다.

10. **새 출력** 창에서 출력 별칭으로 **경고**를 입력합니다. 

11. **저장**을 선택합니다.

12. **작업 토폴로지** 아래에서 **쿼리**를 선택한 다음, 기본 텍스트를 30초 시간 범위에서 평균 컴퓨터 온도가 70도에 도달하면 경고를 만드는 다음 쿼리로 바꿉니다.

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

13. **저장**을 선택합니다.

14. **구성** 아래에서 **IoT Edge 설정**을 선택합니다.

15. 드롭다운 메뉴에서 **저장소 계정**을 선택합니다.

16. **컨테이너** 필드에 대해 **새로 만들기**를 선택하고 저장소 컨테이너에 대한 이름을 제공합니다. 

17. **저장**을 선택합니다. 


## <a name="deploy-the-job"></a>작업 배포

이제 IoT Edge 장치에 Azure Stream Analytics 작업을 배포할 준비가 되었습니다.

1. Azure Portal의 IoT 허브에서 **IoT Edge**로 이동한 다음, IoT Edge 장치의 세부 정보 페이지를 엽니다.

2. **모듈 설정**을 선택합니다.  

   이전에 이 장치에 tempSensor 모듈을 배포한 경우 자동으로 입력될 수 있습니다. 그렇지 않은 경우 다음 단계에 따라 모듈을 추가합니다.

   1. **추가**를 클릭하고 **IoT Edge 모듈**을 선택합니다.
   2. 이름에 대해 **tempsensor**를 입력합니다.
   3. 이미지 URI에 대해 **mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0**을 입력합니다. 
   4. 다른 설정은 변경하지 말고 그대로 둡니다.
   5. **저장**을 선택합니다.

3. 다음 단계에 따라 Azure Stream Analytics Edge 작업을 추가합니다.

   1. **추가**를 클릭하고 **Azure Stream Analytics 모듈**을 선택합니다.
   2. 구독 및 사용자가 만든 Azure Stream Analytics Edge 작업을 선택합니다. 
   3. **저장**을 선택합니다.

4. **다음**을 선택합니다.

5. **경로**의 기본값을 다음 코드로 바꿉니다. _{moduleName}_ 을 Azure Stream Analytics 모듈의 이름으로 업데이트합니다. 모듈의 이름은 만든 작업과 동일한 이름이어야 합니다. 

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

6. **다음**을 선택합니다.

7. **배포 검토** 단계에서 **제출**을 선택합니다.

8. 장치 세부 정보 페이지로 돌아가서 **새로 고침**을 선택합니다.  

    IoT Edge 에이전트 모듈 및 IoT Edge Hub와 함께 실행되는 새로운 Stream Analytics 모듈이 표시됩니다.

    ![모듈 출력][7]

## <a name="view-data"></a>데이터 보기

이제 IoT Edge 장치로 가서 Azure Stream Analytics 모듈과 tempSensor 모듈 간의 상호 작용을 확인할 수 있습니다.

1. Docker에서 모든 모듈이 실행되는지 확인합니다.

   ```cmd/sh
   iotedge list  
   ```
<!--
   ![Docker output][8]
-->
2. 모든 시스템 로그 및 메트릭 데이터를 봅니다. Stream Analytics 모듈 이름을 사용합니다.

   ```cmd/sh
   iotedge logs -f {moduleName}  
   ```

30초 동안 70도에 도달할 때까지 컴퓨터의 온도가 점차 상승하는 것을 볼 수 있어야 합니다. 그러면 Stream Analytics 모듈이 재설정을 트리거하고 컴퓨터 온도가 21도로 떨어집니다. 

   ![Docker 로그][9]

## <a name="clean-up-resources"></a>리소스 정리 

<!--[!INCLUDE [iot-edge-quickstarts-clean-up-resources](../../includes/iot-edge-quickstarts-clean-up-resources.md)] -->

권장되는 다음 문서로 계속 진행하는 경우 지금까지 만든 리소스와 구성을 그대로 유지하고 다시 사용할 수 있습니다.

그렇지 않으면 요금이 부과되지 않도록 이 문서에서 만든 로컬 구성과 Azure 리소스를 삭제할 수 있습니다. 

> [!IMPORTANT]
> Azure 리소스와 리소스 그룹을 삭제하면 되돌릴 수 없습니다. 일단 삭제되면 리소스 그룹 및 해당 그룹에 포함된 모든 리소스가 영구적으로 삭제됩니다. 잘못된 리소스 그룹 또는 리소스를 자동으로 삭제하지 않도록 해야 합니다. 보관할 리소스가 포함된 기존 리소스 그룹 내에 IoT Hub를 만든 경우 리소스 그룹을 삭제하지 말고 IoT Hub 리소스만 삭제하면 됩니다.
>

IoT Hub만 삭제하려면 허브 이름과 리소스 그룹 이름을 사용하여 다음 명령을 실행합니다.

```azurecli-interactive
az iot hub delete --name MyIoTHub --resource-group TestResources
```


이름으로 전체 리소스 그룹을 삭제하려면 다음을 수행합니다.

1. [Azure 포털](https://portal.azure.com) 에 로그인하고 **리소스 그룹**을 클릭합니다.

2. **이름을 기준으로 필터링...** 텍스트 상자에 IoT Hub가 들어 있는 리소스 그룹의 이름을 입력합니다. 

3. 결과 목록의 리소스 그룹 오른쪽에서 **...** 를 클릭한 다음, **리소스 그룹 삭제**를 클릭합니다.

<!--
   ![Delete](./media/iot-edge-quickstarts-clean-up-resources/iot-edge-delete-resource-group.png)
-->
4. 리소스 그룹을 삭제할지 확인하는 메시지가 표시됩니다. 리소스 그룹의 이름을 다시 입력하여 확인한 다음, **삭제**를 클릭합니다. 잠시 후, 리소스 그룹 및 해당 그룹에 포함된 모든 리소스가 삭제됩니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 IoT Edge 장치의 데이터를 분석하도록 Azure Stream Analytics 작업을 구성했습니다. 그런 다음, 이 Azure Stream Analytics 모듈을 IoT Edge 장치에 로드하여 온도 상승을 로컬에서 처리하고, 이에 대응하며, 집계된 데이터 스트림을 클라우드로 보냈습니다. Azure IoT Edge에서 추가 업무용 솔루션을 만드는 방법을 확인하려는 경우 다른 자습서를 계속 진행할 수 있습니다.

> [!div class="nextstepaction"] 
> [Azure Machine Learning 모델을 모듈로 배포][lnk-ml-tutorial]

<!-- Images. -->
[1]: ./media/tutorial-deploy-stream-analytics/storage.png
[4]: ./media/tutorial-deploy-stream-analytics/add_device.png
[5]: ./media/tutorial-deploy-stream-analytics/asa_job.png
[6]: ./media/tutorial-deploy-stream-analytics/set_module.png
[7]: ./media/tutorial-deploy-stream-analytics/module_output2.png
[8]: ./media/tutorial-deploy-stream-analytics/docker_output.png
[9]: ./media/tutorial-deploy-stream-analytics/docker_log.png
[10]: ./media/tutorial-deploy-stream-analytics/storage_settings.png
[11]: ./media/tutorial-deploy-stream-analytics/temp_module.png


<!-- Links -->
[lnk-what-is-iot-edge]: what-is-iot-edge.md
[lnk-module-dev]: module-development.md
[iot-hub-get-started-create-hub]: ../../includes/iot-hub-get-started-create-hub.md
[azure-iot]: https://docs.microsoft.com/azure/iot-hub/
[azure-storage]: https://docs.microsoft.com/azure/storage/
[azure-stream]: https://docs.microsoft.com/azure/stream-analytics/
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-quickstart-win]: quickstart.md
[lnk-quickstart-lin]: quickstart-linux.md
[lnk-module-tutorial]: tutorial-csharp-module.md
[lnk-ml-tutorial]: tutorial-deploy-machine-learning.md

