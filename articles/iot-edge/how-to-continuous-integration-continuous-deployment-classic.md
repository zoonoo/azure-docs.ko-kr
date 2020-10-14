---
title: Azure IoT Edge 장치에 연속 통합 및 연속 배포 (클래식 편집기)-Azure IoT Edge
description: Azure DevOps를 사용 하 여 클래식 편집기 Azure IoT Edge를 사용 하 여 연속 통합 및 연속 배포를 설정 Azure Pipelines
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/26/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 1866f3360b90a96b5e3f215eb7669a1451262bd8
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92046012"
---
# <a name="continuous-integration-and-continuous-deployment-to-azure-iot-edge-devices-classic-editor"></a>Azure IoT Edge 장치에 연속 통합 및 연속 배포 (클래식 편집기)

Azure Pipelines의 기본 제공 Azure IoT Edge 작업과 함께 Azure IoT Edge 애플리케이션을 사용하여 DevOps를 손쉽게 채택할 수 있습니다. 이 문서에서는 클래식 편집기를 사용 하 여 Azure Pipelines의 지속적인 통합 및 지속적인 배포 기능을 사용 하 여 응용 프로그램을 빠르고 Azure IoT Edge 효율적으로 빌드, 테스트 및 배포 하는 방법을 보여 줍니다. 또는 [YAML를 사용할](how-to-continuous-integration-continuous-deployment.md)수 있습니다.

![다이어그램 - 개발 및 프로덕션을 위한 CI 및 CD 분기](./media/how-to-continuous-integration-continuous-deployment-classic/model.png)

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

## <a name="create-a-build-pipeline-for-continuous-integration"></a>연속 통합을 위한 빌드 파이프라인 만들기

이 섹션에서는 새 빌드 파이프라인을 만듭니다. 샘플 IoT Edge 솔루션에 대 한 변경 내용을 체크 인하고 빌드 로그를 게시할 때 파이프라인이 자동으로 실행 되도록 구성 합니다.

1. Azure DevOps 조직 ()에 로그인 하 `https://dev.azure.com/{your organization}` 고 IoT Edge 솔루션 리포지토리가 포함 된 프로젝트를 엽니다.

    ![DevOps 프로젝트 열기](./media/how-to-continuous-integration-continuous-deployment-classic/initial-project.png)

2. 프로젝트의 왼쪽 창 메뉴에서 **파이프라인**을 선택 합니다. 페이지의 중앙에서 **파이프라인 만들기** 를 선택 합니다. 또는 빌드 파이프라인이 이미 있는 경우 오른쪽 위에 있는 **새 파이프라인** 단추를 선택 합니다.

    ![새 빌드 파이프라인 만들기](./media/how-to-continuous-integration-continuous-deployment-classic/add-new-pipeline.png)

3. **코드 위치** 페이지의 아래쪽에서 **클래식 편집기 사용**을 선택 합니다. YAML을 사용 하 여 프로젝트의 빌드 파이프라인을 만들려면 [yaml 가이드](how-to-continuous-integration-continuous-deployment.md)를 참조 하세요.

    ![클래식 편집기 사용을 선택 합니다.](./media/how-to-continuous-integration-continuous-deployment-classic/create-without-yaml.png)

4. 프롬프트에 따라 파이프라인을 만듭니다.

   1. 새 빌드 파이프라인의 원본 정보를 제공합니다. **Azure Repos Git**을 원본으로 선택한 다음, IoT Edge 솔루션 코드가 있는 프로젝트, 리포지토리 및 분기를 선택합니다. 그런 다음 **계속**을 선택 합니다.

      ![파이프라인 원본 선택](./media/how-to-continuous-integration-continuous-deployment-classic/pipeline-source.png)

   2. 템플릿 대신 **빈 작업**을 선택합니다.

      ![빌드 파이프라인에 대 한 빈 작업으로 시작](./media/how-to-continuous-integration-continuous-deployment-classic/start-with-empty-build-job.png)

5. 파이프라인이 만들어지면 파이프라인 편집기로 이동합니다. 여기에서 파이프라인의 이름, 에이전트 풀 및 에이전트 사양을 변경할 수 있습니다.

   Microsoft에서 호스팅하는 풀 또는 관리 하는 자체 호스팅 풀을 선택할 수 있습니다.

   파이프라인 설명에서 대상 플랫폼에 따라 올바른 에이전트 사양을 선택 합니다.

   * Linux 컨테이너에 대 한 platform amd64에서 모듈을 빌드하려면 **ubuntu-16.04** 을 선택 합니다.

   * Windows 1809 컨테이너용 플랫폼 amd64에서 모듈을 빌드하려는 경우 [Windows에서 자체 호스팅 에이전트를 설치](/azure/devops/pipelines/agents/v2-windows?view=vsts)해야 합니다.

   * Platform arm32v7 또는 arm64 for Linux 컨테이너에서 모듈을 빌드하려면 [linux에서 자체 호스트 된 에이전트를 설정](https://devblogs.microsoft.com/iotdev/setup-azure-iot-edge-ci-cd-pipeline-with-arm-agent)해야 합니다.

    ![빌드 에이전트 사양 구성](./media/how-to-continuous-integration-continuous-deployment-classic/configure-env.png)

6. 파이프라인은 **에이전트 작업 1**이라는 작업으로 미리 구성됩니다. 더하기 기호 ()를 선택 **+** 하 여 작업에 4 개의 작업을 추가 합니다. **Azure IoT Edge** 두 번, 파일을 한 번 **복사** 하 고 **빌드 아티팩트** 를 한 번 게시 합니다. 각 작업을 검색 하 고 작업 이름 위로 마우스를 이동 하 여 **추가** 단추를 표시 합니다.

   ![Azure IoT Edge 작업 추가](./media/how-to-continuous-integration-continuous-deployment-classic/add-iot-edge-task.png)

   4 개의 작업이 모두 추가 되 면 에이전트 작업은 다음 예제와 같습니다.

   ![빌드 파이프라인의 네 가지 작업](./media/how-to-continuous-integration-continuous-deployment-classic/add-tasks.png)

7. 첫 번째 **Azure IoT Edge** 작업을 선택하여 편집합니다. 이 작업은 지정 된 대상 플랫폼을 사용 하 여 솔루션의 모든 모듈을 빌드합니다. 다음 값을 사용 하 여 작업을 편집 합니다.

    | 매개 변수 | 설명 |
    | --- | --- |
    | 표시 이름 | 표시 이름은 작업 필드가 변경 될 때 자동으로 업데이트 됩니다. |
    | 작업 | **모듈 이미지 빌드**를 선택 합니다. |
    | 파일에 .template.js | 줄임표(**...**)를 선택하고 IoT Edge 솔루션이 포함된 리포지토리의 **deployment.template.json** 파일로 이동합니다. |
    | 기본 플랫폼 | 대상 IoT Edge 장치에 따라 모듈의 적절 한 운영 체제를 선택 합니다. |
    | 출력 변수 | **Edge**와 같이 파일의 deployment.js에서 생성 되는 파일 경로와 연결할 참조 이름을 제공 합니다. |

   이러한 구성은 파일에 정의 된 이미지 리포지토리 및 태그를 사용 `module.json` 하 여 모듈 이미지의 이름을 지정 하 고 태그를 지정 합니다. 또한 **빌드 모듈 이미지** 를 사용 하면 변수를 파일에서 정의한 정확한 값으로 바꿀 수 있습니다 `module.json` . Visual Studio 또는 Visual Studio Code에서 파일의 실제 값을 지정 합니다 `.env` . Azure Pipelines **파이프라인 변수** 탭에서 값을 설정 합니다. 파이프라인 편집기 메뉴에서 **변수** 탭을 선택 하 고 이름 및 값을 다음과 같이 구성 합니다.

    * **ACR_ADDRESS**: Azure Container Registry **로그인 서버** 값입니다. Azure Portal에서 컨테이너 레지스트리의 개요 페이지에서 로그인 서버를 검색할 수 있습니다.

    프로젝트에 다른 변수가 있는 경우이 탭에서 이름 및 값을 지정할 수 있습니다. **빌드 모듈 이미지** 는 형식의 변수만 인식 `${VARIABLE}` 합니다. 파일에서이 형식을 사용 하는지 확인 `**/module.json` 합니다.

8. 두 번째 **Azure IoT Edge** 작업을 선택하여 편집합니다. 이 작업은 모든 모듈 이미지를 선택된 컨테이너 레지스트리로 푸시합니다.

    | 매개 변수 | 설명 |
    | --- | --- |
    | 표시 이름 | 표시 이름은 작업 필드가 변경 될 때 자동으로 업데이트 됩니다. |
    | 작업 | **모듈 푸시 이미지**를 선택 합니다. |
    | 컨테이너 레지스트리 형식 | 기본 형식인를 사용 `Azure Container Registry` 합니다. |
    | Azure 구독 | 구독을 선택합니다. |
    | Azure Container Registry | 모듈 이미지를 저장하는 데 사용할 컨테이너 레지스트리 유형을 선택합니다. 선택한 레지스트리 유형에 따라 양식이 변경됩니다. **Azure Container Registry**를 선택하는 경우에는 드롭다운 목록을 사용하여 Azure 구독 및 컨테이너 레지스트리 이름을 선택합니다. **일반 컨테이너 레지스트리**를 선택하는 경우에는 **새로 만들기**를 선택하여 레지스트리 서비스 연결을 만듭니다. |
    | 파일에 .template.js | 줄임표(**...**)를 선택하고 IoT Edge 솔루션이 포함된 리포지토리의 **deployment.template.json** 파일로 이동합니다. |
    | 기본 플랫폼 | 대상 IoT Edge 장치에 따라 모듈의 적절 한 운영 체제를 선택 합니다. |
    | 배포 매니페스트에 레지스트리 자격 증명 추가 | 배포 매니페스트에 docker 이미지를 푸시하는 레지스트리 자격 증명을 추가 하려면 true를 지정 합니다. |

   모듈 이미지를 호스트 하는 컨테이너 레지스트리가 여러 개 있는 경우이 작업을 복제 하 고, 다른 컨테이너 레지스트리를 선택 하 고, **고급** 설정에서 **바이패스 모듈** 을 사용 하 여 특정 레지스트리에 없는 이미지를 무시 해야 합니다.

9. **파일 복사** 작업을 선택 하 여 편집 합니다. 이 작업을 사용 하 여 아티팩트 준비 디렉터리에 파일을 복사 합니다.

    | 매개 변수 | 설명 |
    | --- | --- |
    | 표시 이름 | 기본 이름을 사용 하거나 사용자 지정 |
    | 원본 폴더 | 복사할 파일이 포함 된 폴더입니다. |
    | 콘텐츠 | 및의 두 줄 `deployment.template.json` 을 추가 `**/module.json` 합니다. 이러한 두 파일은 IoT Edge 배포 매니페스트를 생성 하기 위한 입력으로 사용 됩니다. |
    | 대상 폴더 | 변수를 지정 `$(Build.ArtifactStagingDirectory)` 합니다. 설명에 대 한 자세한 내용은 [빌드 변수](/azure/devops/pipelines/build/variables?tabs=yaml&view=azure-devops#build-variables) 를 참조 하세요. |

10. **빌드 아티팩트 게시** 작업을 선택하여 편집합니다. 경로를 릴리스 파이프라인에 게시할 수 있도록 작업에 아티팩트 준비 디렉터리 경로를 제공 합니다.

    | 매개 변수 | 설명 |
    | --- | --- |
    | 표시 이름 | 기본 이름을 사용 하거나 사용자 지정 합니다. |
    | 게시할 경로 | 변수를 지정 `$(Build.ArtifactStagingDirectory)` 합니다. 자세히 알아보려면 [변수 빌드](/azure/devops/pipelines/build/variables?tabs=yaml&view=azure-devops#build-variables) 를 참조 하세요. |
    | 아티팩트 이름 | 기본 이름인 **drop** 을 사용 합니다. |
    | 아티팩트 게시 위치 | 기본 위치를 사용 합니다. **Azure Pipelines** |

11. **트리거** 탭을 열고 **연속 통합 사용** 상자를 선택합니다. 코드를 포함하는 분기가 포함되어 있는지 확인합니다.

    ![연속 통합 트리거 켜기](./media/how-to-continuous-integration-continuous-deployment-classic/configure-trigger.png)

12. 저장 **& 큐** 드롭다운에서 **저장** 을 선택 합니다.

이제 이 파이프라인은 리포지토리에 새 코드를 푸시하면 자동으로 실행되도록 구성됩니다. 파이프라인 아티팩트를 게시하는 마지막 작업은 릴리스 파이프라인을 트리거합니다. 계속해서 다음 섹션에서 릴리스 파이프라인을 빌드합니다.

[!INCLUDE [iot-edge-create-release-pipeline-for-continuous-deployment](../../includes/iot-edge-create-release-pipeline-for-continuous-deployment.md)]

>[!NOTE]
>파이프라인에서 **계층화 된 배포** 를 사용 하려는 경우에는 Azure devops의 Azure IoT Edge 작업에서 계층화 된 배포를 아직 지원 하지 않습니다.
>
>그러나 [Azure DevOps에서 Azure CLI 작업](/azure/devops/pipelines/tasks/deploy/azure-cli) 을 사용 하 여 배포를 계층화 된 배포로 만들 수 있습니다. **인라인 스크립트** 값의 경우 [az iot edge deployment create 명령을](/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment)사용할 수 있습니다.
>
>   ```azurecli-interactive
>   az iot edge deployment create -d {deployment_name} -n {hub_name} --content modules_content.json --layered true
>   ```

[!INCLUDE [iot-edge-verify-iot-edge-continuous-integration-continuous-deployment](../../includes/iot-edge-verify-iot-edge-continuous-integration-continuous-deployment.md)]

## <a name="next-steps"></a>다음 단계

* [IoT Edge에 대 한 Azure Devops 스타터](how-to-devops-starter.md) 의 devops 모범 사례 샘플 IoT Edge
* [단일 디바이스 또는 대규모 IoT Edge 배포에 대한 이해](module-deployment-monitoring.md)를 통해 IoT Edge 배포 이해
* [대규모 IoT Edge 모듈 배포 및 모니터링](how-to-deploy-at-scale.md)에서 배포를 생성, 업데이트 또는 삭제하는 단계를 연습합니다.