---
title: "Azure IoT Edge를 사용하여 Azure Stream Analytics 배포 | Microsoft Docs"
description: "Azure Stream Analytics를 Edge 장치에 모듈로 배포"
services: iot-edge
keywords: 
author: msebolt
manager: timlt
ms.author: v-masebo
ms.date: 11/15/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 0d19d1142cf15221f84692f7e613edd6b46b4083
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/23/2017
---
# <a name="deploy-azure-stream-analytics-as-an-iot-edge-module---preview"></a>Azure Stream Analytics를 IoT Edge 모듈로 배포 - 미리 보기

IoT 장치는 많은 양의 데이터를 생성할 수 있습니다. 업로드되는 데이터 크기를 줄이거나 실행 가능한 정보를 얻는 데 소요되는 왕복 대기 시간을 없애기 위해 클라우드에 연결하기 전에 이 데이터를 분석하거나 처리해야 하는 경우도 있습니다.

ASA([Azure Stream Analytics][azure-stream])는 클라우드 및 IoT Edge 장치 둘 다에서 데이터 분석을 위한 풍부하게 구조화된 쿼리 구문을 제공합니다. IoT Edge의 ASA에 대한 자세한 내용은 [ASA 설명서](../stream-analytics/stream-analytics-edge.md)를 참조하세요.

이 자습서에서는 장치에서 직접 로컬 원격 분석 스트림을 처리하고, 경고를 생성하여 장치에 즉각적인 조치를 취할 수 있도록 Azure Stream Analytics 작업의 생성과 IoT Edge 장치에 대한 배포 과정을 안내합니다.  이 자습서에서는 2가지 모듈이 사용됩니다. 20도에서 120도 사이의 온도 데이터를 생성하고 5초마다 1씩 커지는 시뮬레이트된 온도 센서 모듈(tempSensor)과 100도보다 큰 온도를 필터링하는 ASA 모듈이 있습니다. 또한 ASA 모듈은 30초 평균이 100에 도달할 때 tempSensor를 다시 설정합니다.

다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * Edge에서 데이터를 처리하기 위한 ASA 작업 만들기
> * 새 ASA 작업을 다른 IoT Edge 모듈에 연결
> * IoT Edge 장치에 ASA 작업 배포

## <a name="prerequisites"></a>필수 조건

* IoT Hub 
* 빠른 시작 또는 [Windows][lnk-tutorial1-win] 및 [Linux][lnk-tutorial1-lin]의 시뮬레이트된 장치에 Azure IoT Edge 배포 문서에서 만들어 구성한 장치
* IoT Edge 장치의 Docker
    * [Windows에 Docker를 설치][lnk-docker-windows]하고, 지금 실행 중인지 확인합니다.
    * [Linux에 Docker][lnk-docker-linux]를 설치하고, 지금 실행 중인지 확인합니다.
* IoT Edge 장치의 Python 2.7.x
    * [Windows에 Python 2.7을 설치][lnk-python]합니다.
    * Ubuntu를 포함한 대부분의 Linux 배포에는 Python 2.7이 이미 설치되어 있습니다.  다음 명령을 사용하여 pip가 설치되었는지 확인합니다. `sudo apt-get install python-pip`

> [!NOTE]
> 이 자습서에서는 장치 연결 문자열 및 IoT Edge 장치 ID가 필요합니다.

IoT Edge는 빠른 배포를 위해 미리 빌드한 Azure 서비스 IoT Edge 모듈을 사용하며 ASA(Azure Stream Analytics)가 이러한 모듈 중 하나입니다. 해당 포털에서 ASA 작업을 만든 후 IoT Hub 포털로 와서 IoT Hub 모듈로 배포할 수 있습니다.  

Azure Stream Analytics에 대한 자세한 내용은 [Stream Analytics 설명서][azure-stream]의 **개요** 섹션을 참조하세요.

## <a name="create-an-asa-job"></a>ASA 작업 만들기

이 섹션에서는 IoT Hub에서 데이터를 가져오고, 장치에서 전송된 원격 분석 데이터를 쿼리하고, 결과를 Azure Storage Container(BLOB)로 전달하기 위한 Azure Stream Analytics 작업을 만듭니다. 자세한 내용은 [Stream Analytics 설명서][azure-stream]의 **개요** 섹션을 참조하세요. 

> [!NOTE]
> ASA 작업에서 출력으로 사용할 끝점을 제공하려면 Azure Storage 계정이 필요합니다. 다음 예제에서는 BLOB Storage 유형을 사용합니다.  자세한 내용은 [Azure Storage 설명서][azure-storage]의 **Blob** 섹션을 참조하세요.

1. Azure Portal에서 **리소스 만들기 -> 저장소**로 이동한 후 **모두 보기**를 클릭한 후 **저장소 계정 - blob, 파일, 테이블, 큐**를 클릭합니다.

2. 저장소 계정의 이름을 입력하고 IoT Hub가 저장된 같은 위치를 선택합니다. **만들기**를 클릭합니다. 나중에 사용할 수 있게 이름을 적어둡니다.

    ![새 저장소 계정][1]

3. Azure Portal에서 방금 만든 저장소 계정으로 이동합니다. **Blob Service**에서 **Blob 찾아보기**를 클릭합니다. 
4. ASA 모듈에서 데이터를 저장할 새 컨테이너를 만듭니다. 액세스 수준을 _컨테이너_로 설정합니다. **확인**을 클릭합니다.

    ![저장소 설정][10]

5. Azure Portal에서 **리소스 만들기** > **사물 인터넷**으로 이동한 후 **Stream Analytics 작업**을 선택합니다.

2. 이름을 입력하고 호스팅 환경으로 **Edge**를 선택한 후 나머지 기본값은 그대로 사용합니다.  **만들기**를 클릭합니다.

    >[!NOTE]
    >현재, IoT Edge의 ASA 작업은 미국 서부 2 지역에서 지원되지 않습니다. 다른 위치를 선택하세요.

    ![ASA 만들기][5]

2. 만든 작업으로 이동한 후 **작업 토폴로지** 아래에서 **입력**을 선택하고 **추가**를 클릭합니다.

3. 이름 `temperature`를 입력하고 원본 유형으로 **데이터 스트림**을 선택한 후 다른 매개 변수에 대해서는 기본값을 그대로 사용합니다. **만들기**를 클릭합니다.

    ![ASA 입력][2]

    > [!NOTE]
    > 추가 입력에는 IoT Edge 특정 끝점이 포함될 수 있습니다.

4. **작업 토폴로지**에서 **출력**을 선택하고 **추가**를 클릭합니다.

5. 이름 `alert`를 입력하고 기본값을 사용합니다. **만들기**를 클릭합니다.

    ![ASA 출력][3]

6. **작업 토폴로지**에서 **쿼리**를 선택하고 다음을 입력합니다.

    ```sql
    SELECT  
        'reset' AS command 
    INTO 
       alert 
    FROM 
       temperature TIMESTAMP BY timeCreated 
    GROUP BY TumblingWindow(second,30) 
    HAVING Avg(machine.temperature) > 100
    ```

## <a name="deploy-the-job"></a>작업 배포

이제 IoT Edge 장치에 ASA 작업을 배포할 준비가 되었습니다.

1. Azure Portal의 IoT Hub에서 **IoT Edge(미리 보기)**로 이동한 후 *{deviceId}*의 블레이드를 엽니다.

1. **모듈 설정**을 선택한 후 **Azure 서비스 IoT Edge 모듈 가져오기**를 선택합니다.

1. 구독 및 사용자가 만든 ASA Edge 작업을 선택합니다. 그런 후 저장소 계정을 선택합니다. **Save**를 클릭합니다.

    ![모듈 설정][6]

1. **IoT Edge 모듈 추가**를 클릭하여 온도 센서 모듈을 추가합니다. 이름으로 _tempSensor_를 입력하고 이미지 URL로 `microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview`를 입력합니다. 다른 설정은 변경하지 말고 **저장**을 클릭합니다.

    ![온도 모듈][11]

1. ASA 모듈의 이름을 복사합니다. **다음**을 클릭하여 경로를 구성합니다.

1. 다음을 **경로**에 복사합니다.  _{moduleName}_을 복사한 모듈 이름으로 바꿉니다.

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

1. **다음**을 누릅니다.

1. **템플릿 검토** 단계에서 **제출**을 클릭합니다.

1. 장치 세부 정보 페이지로 돌아가서 **새로 고침**을 클릭합니다.  **IoT Edge 에이전트** 모듈 및 **IoT Edge Hub**와 함께 실행되는 새로운 _{moduleName}_ 모듈이 표시됩니다.

    ![모듈 출력][7]

## <a name="view-data"></a>데이터 보기

이제 IoT Edge 장치로 가서 ASA 모듈과 tempSensor 모듈 간의 상호 작용을 확인할 수 있습니다.

1. 명령줄에서 IoT Edge 장치 연결 문자열로 런타임을 구성합니다.

    ```cmd/sh
    iotedgectl setup --connection-string "{device connection string}" --auto-cert-gen-force-no-passwords  
    ```

1. 다음 명령을 실행하여 런타임을 시작합니다.

    ```cmd/sh
    iotedgectl start  
    ```

1. 다음 명령을 실행하여 모듈이 실행되는지 확인합니다.

    ```cmd/sh
    docker ps  
    ```

    ![docker 출력][8]

1. 다음 명령을 실행하여 모든 시스템 로그 및 메트릭 데이터를 봅니다. 위의 모듈 이름을 사용합니다.

    ```cmd/sh
    docker logs -f {moduleName}  
    ```

    ![docker 로그][9]

1. Azure Portal의 저장소 계정에서 **Blob Service** 아래에 있는 **Blob 찾아보기**를 클릭하고 컨테이너를 선택한 후 새로 만든 JSON 파일을 선택합니다.

1. **다운로드**를 클릭하고 결과를 확인합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 IoT Edge 장치의 데이터를 분석하도록 Azure Storage 컨테이너 및 Stream Analytics 작업을 구성했습니다.  그런 다음 다운로드 목적으로 장치의 데이터를 스트림을 통해 BLOB으로 이동하기 위해 사용자 지정 ASA 모듈을 로드했습니다.  Azure IoT Edge에서 업무용 솔루션을 만드는 방법을 추가로 확인하려는 경우 다른 자습서를 계속 진행할 수 있습니다.

> [!div class="nextstepaction"] 
> [Azure Machine Learning 모델을 모듈로 배포][lnk-ml-tutorial]

<!-- Images. -->
[1]: ./media/tutorial-deploy-stream-analytics/storage.png
[2]: ./media/tutorial-deploy-stream-analytics/asa_input.png
[3]: ./media/tutorial-deploy-stream-analytics/asa_output.png
[4]: ./media/tutorial-deploy-stream-analytics/add_device.png
[5]: ./media/tutorial-deploy-stream-analytics/asa_job.png
[6]: ./media/tutorial-deploy-stream-analytics/set_module.png
[7]: ./media/tutorial-deploy-stream-analytics/module_output.png
[8]: ./media/tutorial-deploy-stream-analytics/docker_output.png
[9]: ./media/tutorial-deploy-stream-analytics/docker_log.png
[10]: ./media/tutorial-deploy-stream-analytics/storage_settings.png
[11]: ./media/tutorial-deploy-stream-analytics/temp_module.png


<!-- Links -->
[lnk-what-is-iot-edge]: what-is-iot-edge.md
[lnk-module-dev]: module-development.md
[iot-hub-get-started-create-hub]: ../../includes/iot-hub-get-started-create-hub.md
[azure-iot]: https://docs.microsoft.com/en-us/azure/iot-hub/
[azure-storage]: https://docs.microsoft.com/en-us/azure/storage/
[azure-stream]: https://docs.microsoft.com/en-us/azure/stream-analytics/
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md
[lnk-module-tutorial]: tutorial-csharp-module.md
[lnk-ml-tutorial]: tutorial-deploy-machine-learning.md

[lnk-docker-windows]: https://docs.docker.com/docker-for-windows/install/ 
[lnk-docker-linux]: https://docs.docker.com/engine/installation/linux/docker-ce/ubuntu/
[lnk-python]: https://www.python.org/downloads/