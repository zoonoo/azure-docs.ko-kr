---
title: Azure IoT Edge 장치에 연속 통합 및 연속 배포-Azure IoT Edge
description: Azure DevOps를 사용 하 여 YAML-Azure IoT Edge를 사용 하 여 지속적인 통합 및 지속적인 배포를 설정 Azure Pipelines
author: shizn
manager: philmea
ms.author: kgremban
ms.date: 08/20/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 57031d4ccdfdba73b8b36c8dc943280a8280ffcc
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92048528"
---
# <a name="continuous-integration-and-continuous-deployment-to-azure-iot-edge-devices"></a>Azure IoT Edge 장치에 연속 통합 및 연속 배포

Azure Pipelines의 기본 제공 Azure IoT Edge 작업과 함께 Azure IoT Edge 애플리케이션을 사용하여 DevOps를 손쉽게 채택할 수 있습니다. 이 문서에서는 YAML을 사용 하 여 응용 프로그램을 빠르고 Azure IoT Edge 효율적으로 빌드, 테스트 및 배포 하는 Azure Pipelines의 지속적인 통합 및 지속적인 배포 기능을 사용 하는 방법을 보여 줍니다. 또는 [클래식 편집기를 사용할](how-to-continuous-integration-continuous-deployment-classic.md)수 있습니다.

![다이어그램 - 개발 및 프로덕션을 위한 CI 및 CD 분기](./media/how-to-continuous-integration-continuous-deployment/model.png)

이 문서에서는 Azure Pipelines에 대해 기본 제공 [Azure IoT Edge 작업](/azure/devops/pipelines/tasks/build/azure-iot-edge) 을 사용 하 여 IoT Edge 솔루션에 대 한 빌드 및 릴리스 파이프라인을 만드는 방법에 대해 알아봅니다. 파이프라인에 추가 된 각 Azure IoT Edge 작업은 다음 네 가지 작업 중 하나를 구현 합니다.

 | 작업 | 설명 |
 | --- | --- |
 | 빌드 모듈 이미지 | IoT Edge 솔루션 코드를 사용 하 고 컨테이너 이미지를 빌드합니다.|
 | 모듈 이미지 푸시 | 지정한 컨테이너 레지스트리에 모듈 이미지를 푸시합니다. |
 | 배포 매니페스트 생성 | 파일 및 변수에 대 한 deployment.template.js를 가져온 다음 최종 IoT Edge 배포 매니페스트 파일을 생성 합니다. |
 | IoT Edge 디바이스에 배포 | 하나 이상의 IoT Edge 장치에 대 한 IoT Edge 배포를 만듭니다. |

별도로 지정 하지 않는 한이 문서의 절차에서는 작업 매개 변수를 통해 사용할 수 있는 모든 기능을 탐색 하지 않습니다. 자세한 내용은

* [작업 버전](/azure/devops/pipelines/process/tasks?tabs=classic&view=azure-devops#task-versions)
* **고급** -해당 하는 경우 빌드되지 않으려는 모듈을 지정 합니다.
* [제어 옵션](/azure/devops/pipelines/process/tasks?tabs=classic&view=azure-devops#task-control-options)
* [환경 변수](/azure/devops/pipelines/process/variables?tabs=yaml%252cbatch&view=azure-devops#environment-variables)
* [출력 변수](/azure/devops/pipelines/process/variables?tabs=yaml%252cbatch&view=azure-devops#use-output-variables-from-tasks)

## <a name="prerequisites"></a>전제 조건

* Azure Repos 리포지토리. 이 리포지토리가 없는 경우 [프로젝트에서 새 Git 리포지토리를 만들](/azure/devops/repos/git/create-new-repo?tabs=new-nav&view=vsts) 수 있습니다. 이 문서의 경우 **IoTEdgeRepo**라는 리포지토리를 만들었습니다.
* 리포지토리에 커밋되고 푸시된 IoT Edge 솔루션. 이 문서를 테스트하기 위한 새 샘플 솔루션을 만들려면 [Visual Studio Code에서 모듈 개발 및 디버그](how-to-vs-code-develop-module.md) 또는 [Visual Studio에서 C# 모듈 개발 및 디버그](./how-to-visual-studio-develop-module.md)의 단계를 따릅니다. 이 문서에서는 **filtermodule**이라는 모듈에 대 한 코드를 포함 하는 **IoTEdgeSolution**라는 리포지토리에 솔루션을 만들었습니다.

   이 문서의 경우에는 Visual Studio Code 또는 Visual Studio에서 IoT Edge 템플릿으로 솔루션 폴더만 만들면 됩니다. 계속하기 전에 이 코드를 빌드, 푸시, 배포 또는 디버그할 필요가 없습니다. Azure Pipelines에서 이러한 프로세스를 설정 합니다.

   새 솔루션을 만드는 경우에는 먼저 로컬로 리포지토리를 복제합니다. 그런 다음, 솔루션을 만들 때 리포지토리 폴더에서 직접 만들도록 선택할 수 있습니다. 여기에서 새 파일을 쉽게 커밋하고 푸시할 수 있습니다.

* 모듈 이미지를 푸시할 수 있는 컨테이너 레지스트리. [Azure Container Registry](../container-registry/index.yml) 또는 타사 레지스트리를 사용할 수 있습니다.
* 별도의 테스트 및 프로덕션 배포 단계를 테스트 하기 위한 두 개 이상의 IoT Edge 장치를 포함 하는 활성 Azure [IoT hub](../iot-hub/iot-hub-create-through-portal.md) 입니다. 빠른 시작 문서에 따라 [Linux](quickstart-linux.md) 또는 [Windows](quickstart.md)에서 IoT Edge 디바이스를 만들 수 있습니다.

Azure Repos를 사용 하는 방법에 대 한 자세한 내용은 [Visual Studio를 사용 하 여 코드 공유 및 Azure Repos](/azure/devops/repos/git/share-your-code-in-git-vs?view=vsts) 를 참조 하세요.

## <a name="create-a-build-pipeline-for-continuous-integration"></a>연속 통합을 위한 빌드 파이프라인 만들기

이 섹션에서는 새 빌드 파이프라인을 만듭니다. 샘플 IoT Edge 솔루션에 대 한 변경 내용을 체크 인하고 빌드 로그를 게시할 때 파이프라인이 자동으로 실행 되도록 구성 합니다.

1. Azure DevOps 조직 ()에 로그인 하 `https://dev.azure.com/{your organization}` 고 IoT Edge 솔루션 리포지토리가 포함 된 프로젝트를 엽니다.

   ![DevOps 프로젝트 열기](./media/how-to-continuous-integration-continuous-deployment/initial-project.png)

2. 프로젝트의 왼쪽 창 메뉴에서 **파이프라인**을 선택 합니다. 페이지의 중앙에서 **파이프라인 만들기** 를 선택 합니다. 또는 빌드 파이프라인이 이미 있는 경우 오른쪽 위에 있는 **새 파이프라인** 단추를 선택 합니다.

    ![새 파이프라인 단추를 사용 하 여 새 빌드 파이프라인 만들기](./media/how-to-continuous-integration-continuous-deployment/add-new-pipeline.png)

3. **코드 위치** 페이지에서 **Git `YAML` Azure Repos **를 선택 합니다. 클래식 편집기를 사용 하 여 프로젝트의 빌드 파이프라인을 만들려면 [클래식 편집기 가이드](how-to-continuous-integration-continuous-deployment-classic.md)를 참조 하세요.

4. 파이프라인을 만들 리포지토리를 선택 합니다.

    ![빌드 파이프라인에 대 한 리포지토리 선택](./media/how-to-continuous-integration-continuous-deployment/select-repository.png)

5. **파이프라인 구성** 페이지에서 **스타터 파이프라인**을 선택 합니다. 이 파이프라인을 만드는 데 사용 하려는 기존 Azure Pipelines YAML 파일이 있는 경우 **기존 Azure Pipelines YAML 파일** 을 선택 하 고 리포지토리의 분기 및 경로를 파일에 제공할 수 있습니다.

    ![시작 파이프라인 또는 기존 Azure Pipelines YAML 파일을 선택 하 여 빌드 파이프라인 시작](./media/how-to-continuous-integration-continuous-deployment/configure-pipeline.png)

6. **파이프라인 YAML 검토** 페이지에서 기본 이름을 클릭 `azure-pipelines.yml` 하 여 파이프라인의 구성 파일 이름을 바꿀 수 있습니다.

   **길잡이 표시** 를 선택 하 여 **작업** 팔레트를 엽니다.

    ![작업 팔레트를 열려면 보조자 표시를 선택 합니다.](./media/how-to-continuous-integration-continuous-deployment/show-assistant.png)

7. 작업을 추가 하려면 작업에 대 한 지침을 추가 하려는 경우 또는 원하는 위치에 커서를 놓습니다. **Azure IoT Edge**를 검색 하 고 선택 합니다. 다음과 같이 작업의 매개 변수를 채웁니다. 그런 다음 **추가**를 선택 합니다.

   | 매개 변수 | 설명 |
   | --- | --- |
   | 작업 | **모듈 이미지 빌드**를 선택 합니다. |
   | 파일에 .template.js | IoT Edge 솔루션을 포함 하는 리포지토리의 파일 ** 에 대 한deployment.template.js** 경로를 제공 합니다. |
   | 기본 플랫폼 | 대상 IoT Edge 장치에 따라 모듈의 적절 한 운영 체제를 선택 합니다. |

    ![작업 색상표를 사용 하 여 파이프라인에 작업 추가](./media/how-to-continuous-integration-continuous-deployment/add-build-task.png)

   >[!TIP]
   > 각 작업이 추가 된 후 편집기에서 추가 된 줄을 자동으로 강조 표시 합니다. 실수로 덮어쓰는 일을 방지 하려면 추가 작업을 추가 하기 전에 줄을 선택 취소 하 고 다음 작업에 새 공간을 제공 합니다.

8. 다음 매개 변수를 사용 하 여 세 가지 작업을 더 추가 하려면이 프로세스를 반복 합니다.

   * 작업: **Azure IoT Edge**

       | 매개 변수 | 설명 |
       | --- | --- |
       | 작업 | **모듈 푸시 이미지**를 선택 합니다. |
       | 컨테이너 레지스트리 형식 | 기본 형식인 **Azure Container Registry**를 사용 합니다. |
       | Azure 구독 | 구독을 선택합니다. |
       | Azure Container Registry | 파이프라인에 사용 하려는 레지스트리를 선택 합니다. |
       | 파일에 .template.js | IoT Edge 솔루션을 포함 하는 리포지토리의 파일 ** 에 대 한deployment.template.js** 경로를 제공 합니다. |
       | 기본 플랫폼 | 대상 IoT Edge 장치에 따라 모듈의 적절 한 운영 체제를 선택 합니다. |

   * 작업: **파일 복사**

       | 매개 변수 | 설명 |
       | --- | --- |
       | 원본 폴더 | 복사할 원본 폴더입니다. Empty는 리포지토리의 루트입니다. 파일이 리포지토리에 없으면 변수를 사용 합니다. 예: `$(agent.builddirectory)`.
       | 콘텐츠 | 및의 두 줄 `deployment.template.json` 을 추가 `**/module.json` 합니다. |
       | 대상 폴더 | 변수를 지정 `$(Build.ArtifactStagingDirectory)` 합니다. 설명에 대 한 자세한 내용은 [빌드 변수](/azure/devops/pipelines/build/variables?tabs=yaml&view=azure-devops#build-variables) 를 참조 하세요. |

   * 작업: **빌드 아티팩트 게시**

       | 매개 변수 | 설명 |
       | --- | --- |
       | 게시할 경로 | 변수를 지정 `$(Build.ArtifactStagingDirectory)` 합니다. 설명에 대 한 자세한 내용은 [빌드 변수](/azure/devops/pipelines/build/variables?tabs=yaml&view=azure-devops#build-variables) 를 참조 하세요. |
       | 아티팩트 이름 | 기본 이름을 지정 합니다. `drop` |
       | 아티팩트 게시 위치 | 기본 위치를 사용 합니다. `Azure Pipelines` |

9. 오른쪽 위의 **저장 및 실행** 드롭다운에서 **저장** 을 선택 합니다.

10. 연속 통합에 대 한 트리거는 YAML 파이프라인에 대해 기본적으로 사용 하도록 설정 됩니다. 이러한 설정을 편집 하려면 파이프라인을 선택 하 고 오른쪽 위에 있는 **편집** 을 클릭 합니다. 오른쪽 위에 있는 **실행** 단추 옆의 **추가 작업** 을 선택 하 고 **트리거로**이동 합니다. **연속 통합** 은 파이프라인 이름 아래에서 사용 하도록 설정 된 것을 보여 줍니다. 트리거의 세부 정보를 보려면 **여기에서 YAML 연속 통합 트리거 재정의** 상자를 선택 합니다.

    ![파이프라인의 트리거 설정을 검토 하려면 추가 작업 아래의 트리거를 참조 하세요.](./media/how-to-continuous-integration-continuous-deployment/check-trigger-settings.png)

계속해서 다음 섹션에서 릴리스 파이프라인을 빌드합니다.

[!INCLUDE [iot-edge-create-release-pipeline-for-continuous-deployment](../../includes/iot-edge-create-release-pipeline-for-continuous-deployment.md)]

[!INCLUDE [iot-edge-verify-iot-edge-continuous-integration-continuous-deployment](../../includes/iot-edge-verify-iot-edge-continuous-integration-continuous-deployment.md)]

## <a name="next-steps"></a>다음 단계

* [IoT Edge에 대 한 Azure Devops 스타터](how-to-devops-starter.md) 의 devops 모범 사례 샘플 IoT Edge
* [단일 디바이스 또는 대규모 IoT Edge 배포에 대한 이해](module-deployment-monitoring.md)를 통해 IoT Edge 배포 이해
* [대규모 IoT Edge 모듈 배포 및 모니터링](how-to-deploy-at-scale.md)에서 배포를 생성, 업데이트 또는 삭제하는 단계를 연습합니다.