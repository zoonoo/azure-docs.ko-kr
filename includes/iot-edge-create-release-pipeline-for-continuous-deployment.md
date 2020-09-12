---
author: v-tcassi
ms.service: iot-edge
ms.topic: include
ms.date: 08/26/2020
ms.author: v-tcassi
ms.openlocfilehash: 706b2306fbe9f2a744d2874a8b55f78fa2fc8e4d
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/02/2020
ms.locfileid: "89302954"
---
## <a name="create-a-release-pipeline-for-continuous-deployment"></a>연속 배포에 대 한 릴리스 파이프라인 만들기

이 섹션에서는 빌드 파이프라인이 아티팩트를 드롭하면 자동으로 실행되도록 구성된 릴리스 파이프라인을 만들고 Azure Pipelines에 배포 로그를 표시합니다.

새 파이프라인을 만들고 새 단계를 추가 합니다.

1. **파이프라인**의 **릴리스** 탭에서 **+ 새 파이프라인**을 선택 합니다. 또는 이미 릴리스 파이프라인이 있는 경우 **+ 새로 만들기** 단추를 선택하고 **+ 새 릴리스 파이프라인**을 선택합니다.  

    ![+ 새 파이프라인 단추를 사용 하 여 릴리스 파이프라인 추가](./media/iot-edge-create-release-pipeline-for-continuous-deployment/add-release-pipeline.png)

2. 템플릿을 선택하라는 메시지가 표시되면 **빈 작업**으로 시작하도록 선택합니다.

    ![릴리스 파이프라인에 대 한 빈 작업으로 시작](./media/iot-edge-create-release-pipeline-for-continuous-deployment/start-with-empty-release-job.png)

3. 새 릴리스 파이프라인은 **스테이지 1**이라는 하나의 스테이지로 초기화됩니다. 1 단계를 **dev** 로 바꾸고 개발 환경에 대 한 연속 배포 파이프라인으로 처리 합니다. 일반적으로 연속 배포 파이프라인에는 **개발**, **스테이징**및 **prod**를 비롯 한 여러 단계가 있습니다. 다른 이름을 사용 하 고 DevOps 관행에 따라 더 많은 이름을 만들 수 있습니다. 이름이 바뀐 후 [스테이지 세부 정보] 창을 닫습니다.

   위쪽에서 "새 릴리스 파이프라인" 텍스트를 선택 하 여 릴리스 파이프라인의 이름을 바꿀 수도 있습니다.

4. 빌드 파이프라인에서 게시한 빌드 아티팩트에 릴리스를 연결합니다. 아티팩트 영역에서 **추가**를 클릭합니다.

   ![인터페이스의 아티팩트 영역에서 추가를 클릭 합니다.](./media/iot-edge-create-release-pipeline-for-continuous-deployment/add-artifacts.png)

5. **아티팩트 추가 페이지**에서 **원본 유형**으로 **빌드** 를 선택 합니다. 만든 프로젝트 및 빌드 파이프라인을 선택 합니다. 원하는 경우 **원본 별칭** 을 보다 알기 쉬운 항목으로 변경할 수 있습니다. 그런 다음 **추가**를 선택 합니다.

   ![아티팩트 추가 페이지에서 추가를 선택 하 여 아티팩트를 만듭니다.](./media/iot-edge-create-release-pipeline-for-continuous-deployment/add-artifact.png)

6. 아티팩트 트리거를 열고 토글을 선택하여 지속적인 배포 트리거를 사용하도록 설정합니다. 이제 새 빌드를 사용할 수 있을 때마다 새 릴리스가 생성됩니다.

   ![아티팩트 트리거를 열고 전환 하 여 연속 배포 트리거를 사용 하도록 설정 합니다.](./media/iot-edge-create-release-pipeline-for-continuous-deployment/add-trigger.png)

7. **Dev** 단계는 하나의 작업 및 0 개의 작업으로 미리 구성 됩니다. 파이프라인 메뉴에서 **작업** 을 선택한 다음 **개발** 단계를 선택 합니다. **에이전트 작업** 을 선택 하 고 **표시 이름을** **QA**로 변경 합니다. 에이전트 작업에 대 한 세부 정보를 구성할 수 있지만, 선택한 **에이전트 풀**의 모든 **에이전트 사양을** 사용할 수 있도록 배포 작업은 플랫폼을 구분 하지 않습니다.

   ![작업 탭 아래에서 개발 단계에 대 한 작업 보기](./media/iot-edge-create-release-pipeline-for-continuous-deployment/view-stage-tasks.png)

8. QA 작업에서 더하기 기호 ()를 선택 **+** 하 여 두 개의 작업을 추가 합니다. **Azure IoT Edge** 를 검색 하 고 두 번 추가 합니다.

9. 첫 번째 **Azure IoT Edge** 작업을 선택 하 고 다음 값으로 구성 합니다.

    | 매개 변수 | 설명 |
    | --- | --- |
    | 표시 이름 | 표시 이름은 작업 필드가 변경 될 때 자동으로 업데이트 됩니다. |
    | 작업 | `Generate deployment manifest`를 선택합니다. |
    | 파일에 .template.js | 경로를 지정 `$(System.DefaultWorkingDirectory)/Drop/drop/deployment.template.json` 합니다. 경로는 빌드 파이프라인에서 게시 됩니다. |
    | 기본 플랫폼 | 대상 IoT Edge 장치에 따라 모듈의 적절 한 운영 체제를 선택 합니다. |
    | 출력 경로| 경로를 입력 `$(System.DefaultWorkingDirectory)/Drop/drop/configs/deployment.json` 합니다. 이 경로는 최종 IoT Edge 배포 매니페스트 파일입니다. |

    이러한 구성은 파일의 모듈 이미지 Url을 대체 하는 데 도움이 됩니다 `deployment.template.json` . 또한 **생성 된 배포 매니페스트** 를 사용 하면 변수를 파일에서 정의한 정확한 값으로 바꿀 수 있습니다 `deployment.template.json` . VS/VS Code에서 파일의 실제 값을 지정 합니다 `.env` . Azure Pipelines의 **릴리스 파이프라인 변수** 탭에서 값을 설정 합니다. **변수** 탭으로 이동 하 고 이름 및 값을 다음과 같이 구성 합니다.

    * **ACR_ADDRESS**: Azure Container Registry **로그인 서버** 값입니다. Azure Portal에서 컨테이너 레지스트리의 개요 페이지에서 로그인 서버를 검색할 수 있습니다.
    * **ACR_PASSWORD**: Azure Container Registry 암호입니다.
    * **ACR_USER**: Azure Container Registry 사용자 이름입니다.

    프로젝트에 다른 변수가 있는 경우이 탭에서 이름 및 값을 지정할 수 있습니다. **배포 매니페스트 생성** 은 변수만을 인식할 수 있습니다 `${VARIABLE}` . 파일에서이 버전을 사용 하 고 있는지 확인 `*.template.json` 합니다.

    ![변수 탭에서 릴리스 파이프라인에 대 한 변수를 구성 합니다.](./media/iot-edge-create-release-pipeline-for-continuous-deployment/configure-variables.png)

10. 두 번째 **Azure IoT Edge** 작업을 선택 하 고 다음 값으로 구성 합니다.

    | 매개 변수 | 설명 |
    | --- | --- |
    | 표시 이름 | 표시 이름은 작업 필드가 변경 될 때 자동으로 업데이트 됩니다. |
    | 작업 | `Deploy to IoT Edge devices`를 선택합니다. |
    | 배포 파일 | 경로를 입력 `$(System.DefaultWorkingDirectory)/Drop/drop/configs/deployment.json` 합니다. 이 경로는 파일 IoT Edge 배포 매니페스트 파일입니다. |
    | Azure 구독 | IoT Hub를 포함하는 구독을 선택합니다.|
    | IoT Hub 이름 | IoT Hub를 선택합니다.|
    | 단일/다중 장치 선택 | 릴리스 파이프라인을 하나 이상의 장치에 배포할지 여부를 선택 합니다. 단일 디바이스에 배포하는 경우 **IoT Edge 디바이스 ID**를 입력합니다. 여러 디바이스에 배포하는 경우 디바이스 **대상 조건**을 지정합니다. 대상 조건은 IoT Hub의 IoT Edge 장치 집합과 일치 하는 필터입니다. 장치 태그를 조건으로 사용 하려면 장치 쌍 IoT Hub 사용 하 여 해당 장치 태그를 업데이트 해야 합니다. 고급 설정에서 **IoT Edge 배포 ID** 및 **IoT Edge 배포 우선 순위**를 업데이트합니다. 여러 디바이스용 배포를 만드는 방법에 대한 자세한 내용은 [IoT Edge 자동 배포 이해](../articles/iot-edge/module-deployment-monitoring.md)를 참조하세요. |
    | 장치 ID 또는 대상 조건 | 이전 선택 항목에 따라 장치 ID 또는 [대상 조건을](../articles/iot-edge/module-deployment-monitoring.md#target-condition) 지정 하 여 여러 장치에 배포 합니다. |
    | 고급 | IoT Edge 배포 ID에 대해를 지정 `$(System.TeamProject)-$(Release.EnvironmentName)` 합니다. 이 변수는 프로젝트와 릴리스 이름을 IoT Edge 배포 ID에 매핑합니다. |

    ![개발 단계에 대 한 Azure IoT Edge 작업 추가](./media/iot-edge-create-release-pipeline-for-continuous-deployment/add-quality-assurance-task.png)

11. **저장**을 선택하여 새 릴리스 파이프라인의 변경 사항을 저장합니다. 메뉴에서 **파이프라인** 탭을 선택 하 여 파이프라인 뷰로 돌아갑니다.
