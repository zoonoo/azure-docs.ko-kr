---
title: 연속 통합 & 연속 배포-Azure IoT Edge
description: 연속 통합 및 지속적인 배포 설정 - Azure IoT Edge 및 Azure DevOps, Azure Pipelines
author: shizn
manager: philmea
ms.author: xshi
ms.date: 08/20/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: ac37e9bd10caea5c6e58fc797eac73ce6c714162
ms.sourcegitcommit: 856db17a4209927812bcbf30a66b14ee7c1ac777
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/29/2020
ms.locfileid: "82561036"
---
# <a name="continuous-integration-and-continuous-deployment-to-azure-iot-edge"></a>Azure IoT Edge 연속 통합 및 지속적인 배포

Azure Pipelines의 기본 제공 Azure IoT Edge 작업과 함께 Azure IoT Edge 애플리케이션을 사용하여 DevOps를 손쉽게 채택할 수 있습니다. 이 문서에서는 Azure Pipelines의 연속 통합 및 지속적인 배포 기능을 사용하여 애플리케이션을 빠르고 효율적으로 빌드하고 테스트한 다음, Azure IoT Edge에 배포하는 방법을 설명합니다.

![다이어그램 - 개발 및 프로덕션을 위한 CI 및 CD 분기](./media/how-to-ci-cd/cd.png)

이 문서에서는 Azure Pipelines의 기본 제공 Azure IoT Edge 작업을 사용하여 IoT Edge 솔루션을 위한 두 개의 파이프라인을 만드는 방법을 알아봅니다. Azure IoT Edge 작업에는 네 가지 동작을 사용할 수 있습니다.

* **Azure IoT Edge 빌드 모듈 이미지** 는 IoT Edge 솔루션 코드를 사용 하 고 컨테이너 이미지를 빌드합니다.
* **Azure IoT Edge Push module 이미지** 는 모듈 이미지를 지정한 컨테이너 레지스트리에 푸시합니다.
* **Azure IoT Edge-배포 매니페스트 생성** 은 배포 템플릿. json 파일 및 변수를 사용 하 여 최종 IoT Edge 배포 매니페스트 파일을 생성 합니다.
* **Azure IoT Edge-IoT Edge 장치에 배포** 하면 단일/다중 IoT Edge 장치에 대 한 IoT Edge 배포를 만들 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

* Azure Repos 리포지토리. 이 리포지토리가 없는 경우 [프로젝트에서 새 Git 리포지토리를 만들](https://docs.microsoft.com/azure/devops/repos/git/create-new-repo?view=vsts&tabs=new-nav) 수 있습니다.
* 리포지토리에 커밋되고 푸시된 IoT Edge 솔루션. 이 문서를 테스트하기 위한 새 샘플 솔루션을 만들려면 [Visual Studio Code에서 모듈 개발 및 디버그](how-to-vs-code-develop-module.md) 또는 [Visual Studio에서 C# 모듈 개발 및 디버그](how-to-visual-studio-develop-csharp-module.md)의 단계를 따릅니다.

   이 문서의 경우에는 Visual Studio Code 또는 Visual Studio에서 IoT Edge 템플릿으로 솔루션 폴더만 만들면 됩니다. 계속하기 전에 이 코드를 빌드, 푸시, 배포 또는 디버그할 필요가 없습니다. 해당 프로세스는 Azure Pipelines에서 설정합니다.

   새 솔루션을 만드는 경우에는 먼저 로컬로 리포지토리를 복제합니다. 그런 다음, 솔루션을 만들 때 리포지토리 폴더에서 직접 만들도록 선택할 수 있습니다. 여기에서 새 파일을 쉽게 커밋하고 푸시할 수 있습니다.

* 모듈 이미지를 푸시할 수 있는 컨테이너 레지스트리. [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) 또는 타사 레지스트리를 사용할 수 있습니다.
* 개별 테스트 및 프로덕션 배포 스테이지를 테스트하기 위한 IoT Edge 디바이스가 있는 활성 [IoT 허브](../iot-hub/iot-hub-create-through-portal.md). 빠른 시작 문서에 따라 [Linux](quickstart-linux.md) 또는 [Windows](quickstart.md)에서 IoT Edge 디바이스를 만들 수 있습니다.

Azure Repos를 사용 하는 방법에 대 한 자세한 내용은 [Visual Studio를 사용 하 여 코드 공유 및 Azure Repos](https://docs.microsoft.com/azure/devops/repos/git/share-your-code-in-git-vs?view=vsts) 를 참조 하세요.

## <a name="configure-continuous-integration"></a>연속 통합 구성

이 섹션에서는 새 빌드 파이프라인을 만듭니다. 샘플 IoT Edge 솔루션에 변경 사항을 체크 인하면 자동으로 실행되도록 파이프라인을 구성하고 빌드 로그를 게시합니다.

>[!NOTE]
>이 문서에서는 Azure DevOps 비주얼 디자이너를 사용합니다. 이 섹션의 단계를 수행하기 전에 새 YAML 파이프라인 생성 환경의 미리 보기 기능을 끕니다.
>
>1. Azure DevOps에서 프로필 아이콘을 선택한 다음, **미리 보기 기능**을 선택합니다.
>2. **새 YAML 파이프라인 생성 환경**을 끕니다.
>
>자세한 내용은 [Create a build pipeline](https://docs.microsoft.com/azure/devops/pipelines/create-first-pipeline)(빌드 파이프라인 만들기)을 참조하세요.

1. Azure DevOps 조직 (**https:\//dev.azure.com/{your 조직**)에 로그인 하 고 IoT Edge 솔루션 리포지토리가 포함 된 프로젝트를 엽니다.

   이 문서의 경우 **IoTEdgeRepo**라는 리포지토리를 만들었습니다. 해당 리포지토리에는 **filtermodule** 모듈의 코드가 있는 **IoTEdgeSolution**이 포함됩니다.

   ![DevOps 프로젝트 열기](./media/how-to-ci-cd/init-project.png)

2. 프로젝트에서 Azure Pipelines로 이동합니다. **빌드** 탭을 열고 **새 파이프라인**을 선택합니다. 또는 빌드 파이프라인이 이미 있는 경우 **새로 만들기** 단추를 선택합니다. 그런 다음, **새 빌드 파이프라인**을 선택합니다.

    ![새 빌드 파이프라인 만들기](./media/how-to-ci-cd/add-new-build.png)

3. 프롬프트에 따라 파이프라인을 만듭니다.

   1. 새 빌드 파이프라인의 원본 정보를 제공합니다. **Azure Repos Git**을 원본으로 선택한 다음, IoT Edge 솔루션 코드가 있는 프로젝트, 리포지토리 및 분기를 선택합니다. 그런 다음 **계속**을 선택 합니다.

      ![파이프라인 원본 선택](./media/how-to-ci-cd/pipeline-source.png)

   2. 템플릿 대신 **빈 작업**을 선택합니다.

      ![빈 프로세스로 시작](./media/how-to-ci-cd/start-with-empty.png)

4. 파이프라인이 만들어지면 파이프라인 편집기로 이동합니다. 파이프라인 설명에서 대상 플랫폼에 따라 올바른 에이전트 풀을 선택합니다.

   * Linux 컨테이너용 플랫폼 amd64에서 모듈을 빌드하려는 경우 **호스트된 Ubuntu 1604**를 선택합니다.

   * Windows 1809 컨테이너용 플랫폼 amd64에서 모듈을 빌드하려는 경우 [Windows에서 자체 호스팅 에이전트를 설치](https://docs.microsoft.com/azure/devops/pipelines/agents/v2-windows?view=vsts)해야 합니다.

   * Platform arm32v7 또는 arm64 for Linux 컨테이너에서 모듈을 빌드하려면 [linux에서 자체 호스트 된 에이전트를 설정](https://blogs.msdn.microsoft.com/iotdev/2018/11/13/setup-azure-iot-edge-ci-cd-pipeline-with-arm-agent/)해야 합니다.

     ![빌드 에이전트 풀 구성](./media/how-to-ci-cd/configure-env.png)

5. 파이프라인은 **에이전트 작업 1**이라는 작업으로 미리 구성됩니다. 더하기**+** 기호 ()를 선택 하 여 작업에 세 개의 작업을 추가 합니다. **Azure IoT Edge** 두 번, 파일을 한 번 **복사** 하 고 **빌드 아티팩트** 를 한 번 게시 합니다. 각 작업 이름을 가리키면 **추가** 단추가 표시됩니다.

   ![Azure IoT Edge 작업 추가](./media/how-to-ci-cd/add-iot-edge-task.png)

   4 개의 작업이 모두 추가 되 면 에이전트 작업은 다음 예제와 같습니다.

   ![빌드 파이프라인에 있는 세 개의 작업](./media/how-to-ci-cd/add-tasks.png)

6. 첫 번째 **Azure IoT Edge** 작업을 선택하여 편집합니다. 이 작업은 지정 된 대상 플랫폼을 사용 하 여 솔루션의 모든 모듈을 빌드합니다.

   * **표시 이름**: 기본 **Azure IoT Edge 빌드 모듈 이미지**를 적용 합니다.
   * **작업**: 기본 **빌드 모듈 이미지**를 적용 합니다.
   * **. template. json 파일**: 줄임표 (**...**)를 선택 하 고 IoT Edge 솔루션을 포함 하는 리포지토리의 **배포 템플릿인 json** 파일로 이동 합니다.
   * **기본 플랫폼**: 대상 IoT Edge 장치에 따라 모듈에 적절 한 플랫폼을 선택 합니다.
   * **출력 변수**: 출력 변수는 배포. json 파일이 생성 될 파일 경로를 구성 하는 데 사용할 수 있는 참조 이름을 포함 합니다. 참조 이름을 **edge**와 같이 기억하기 쉬운 이름을 설정합니다.


   이러한 구성은 `module.json` 파일에 정의 된 이미지 리포지토리 및 태그를 사용 하 여 모듈 이미지의 이름을 지정 하 고 태그를 지정 합니다. 또한 **빌드 모듈 이미지** 를 사용 하면 변수를 `module.json` 파일에서 정의한 정확한 값으로 바꿀 수 있습니다. Visual Studio 또는 Visual Studio Code에서 `.env` 파일의 실제 값을 지정 합니다. Azure Pipelines **파이프라인 변수** 탭에서 값을 설정 합니다. **변수** 탭을 선택 하 고 이름 및 값을 다음과 같이 구성 합니다.

    * **ACR_ADDRESS**: Azure Container Registry 주소입니다. 

    프로젝트에 다른 변수가 있는 경우이 탭에서 이름 및 값을 지정할 수 있습니다. **빌드 모듈 이미지** 는 `${VARIABLE}` 형식의 변수만 인식 합니다. `**/module.json` 파일에서이 형식을 사용 하는지 확인 합니다.
    
7. 두 번째 **Azure IoT Edge** 작업을 선택하여 편집합니다. 이 작업은 모든 모듈 이미지를 선택된 컨테이너 레지스트리로 푸시합니다.

   * **표시 이름**: 작업 필드가 변경 될 때 표시 이름이 자동으로 업데이트 됩니다.
   * **작업**: 드롭다운 목록을 사용 하 여 **모듈 푸시 이미지**를 선택 합니다.
   * **컨테이너 레지스트리 유형**: 모듈 이미지를 저장 하는 데 사용 하는 컨테이너 레지스트리 유형을 선택 합니다. 선택한 레지스트리 유형에 따라 양식이 변경됩니다. **Azure Container Registry**를 선택하는 경우에는 드롭다운 목록을 사용하여 Azure 구독 및 컨테이너 레지스트리 이름을 선택합니다. **일반 컨테이너 레지스트리**를 선택하는 경우에는 **새로 만들기**를 선택하여 레지스트리 서비스 연결을 만듭니다.
   * **. template. json 파일**: 줄임표 (**...**)를 선택 하 고 IoT Edge 솔루션을 포함 하는 리포지토리의 **배포 템플릿인 json** 파일로 이동 합니다.
   * **기본 플랫폼**: 빌드된 모듈 이미지와 동일한 플랫폼을 선택 합니다.

   모듈 이미지를 호스트하는 컨테이너 레지스트리가 여러 개 있는 경우 이 작업을 복제하고 다른 컨테이너 레지스트리를 선택한 다음, 고급 설정의 **모듈 무시**를 사용하여 이 특정 레지스트리에 해당되지 않는 이미지를 무시해야 합니다.

8. **파일 복사** 작업을 선택 하 여 편집 합니다. 이 작업을 사용 하 여 아티팩트 준비 디렉터리에 파일을 복사 합니다.

   * **표시 이름**: 저장 폴더에 파일을 복사 합니다.
   * **내용**:이 섹션에는 및 `deployment.template.json` `**/module.json`의 두 줄을 입력 합니다. 이러한 두 가지 유형의 파일은 IoT Edge 배포 매니페스트를 생성 하는 입력입니다. 아티팩트 준비 폴더에 복사 하 고 릴리스 파이프라인에 대해 게시 해야 합니다.
   * **대상 폴더**: 변수 `$(Build.ArtifactStagingDirectory)`를 저장 합니다. 설명에 대 한 자세한 내용은 [빌드 변수](https://docs.microsoft.com/azure/devops/pipelines/build/variables?view=azure-devops&tabs=yaml#build-variables) 를 참조 하세요.

9. **빌드 아티팩트 게시** 작업을 선택하여 편집합니다. 경로를 릴리스 파이프라인에 게시할 수 있도록 작업에 아티팩트 준비 디렉터리 경로를 제공 합니다.

   * **표시 이름**: 게시 아티팩트: drop.
   * **게시할 경로**: 변수 `$(Build.ArtifactStagingDirectory)`를 배치 합니다. 설명에 대 한 자세한 내용은 [빌드 변수](https://docs.microsoft.com/azure/devops/pipelines/build/variables?view=azure-devops&tabs=yaml#build-variables) 를 참조 하세요.
   * **아티팩트 이름**: drop.
   * **아티팩트 게시 위치**: Azure Pipelines.

10. **트리거** 탭을 열고 **연속 통합 사용** 상자를 선택합니다. 코드를 포함하는 분기가 포함되어 있는지 확인합니다.

    ![연속 통합 트리거 켜기](./media/how-to-ci-cd/configure-trigger.png)

11. **저장** 단추로 새 빌드 파이프라인을 저장합니다.

이제 이 파이프라인은 리포지토리에 새 코드를 푸시하면 자동으로 실행되도록 구성됩니다. 파이프라인 아티팩트를 게시하는 마지막 작업은 릴리스 파이프라인을 트리거합니다. 계속해서 다음 섹션에서 릴리스 파이프라인을 빌드합니다.

## <a name="configure-continuous-deployment"></a>지속적 배포 구성

이 섹션에서는 빌드 파이프라인이 아티팩트를 드롭하면 자동으로 실행되도록 구성된 릴리스 파이프라인을 만들고 Azure Pipelines에 배포 로그를 표시합니다.

새 파이프라인을 만들고 새 단계를 추가 합니다.

1. **릴리스** 탭에서 **+ 새 파이프라인**을 선택합니다. 또는 이미 릴리스 파이프라인이 있는 경우 **+ 새로 만들기** 단추를 선택하고 **+ 새 릴리스 파이프라인**을 선택합니다.  

    ![릴리스 파이프라인 추가](./media/how-to-ci-cd/add-release-pipeline.png)

2. 템플릿을 선택하라는 메시지가 표시되면 **빈 작업**으로 시작하도록 선택합니다.

    ![빈 작업으로 시작](./media/how-to-ci-cd/start-with-empty-job.png)

3. 새 릴리스 파이프라인은 **스테이지 1**이라는 하나의 스테이지로 초기화됩니다. 1 단계를 **dev** 로 바꾸고 테스트 환경으로 처리 합니다. 일반적으로 연속 배포 파이프라인에는 **개발**, **스테이징** 및 **프로덕션**을 포함 한 여러 단계가 있습니다. DevOps 방법에 따라 더 많은 작업을 만들 수 있습니다. 이름이 바뀐 후 [스테이지 세부 정보] 창을 닫습니다.

4. 빌드 파이프라인에서 게시한 빌드 아티팩트에 릴리스를 연결합니다. 아티팩트 영역에서 **추가**를 클릭합니다.

   ![아티팩트 추가](./media/how-to-ci-cd/add-artifacts.png)  

5. **아티팩트 페이지 추가**에서 원본 형식 **빌드**를 선택합니다. 그런 다음, 만든 프로젝트 및 빌드 파이프라인을 선택합니다. 그런 다음 **추가**를 선택 합니다.

   ![빌드 아티팩트 추가](./media/how-to-ci-cd/add-an-artifact.png)

6. 아티팩트 트리거를 열고 토글을 선택하여 지속적인 배포 트리거를 사용하도록 설정합니다. 이제 새 빌드를 사용할 수 있을 때마다 새 릴리스가 생성됩니다.

   ![지속적인 배포 트리거 구성](./media/how-to-ci-cd/add-a-trigger.png)

7. **Dev** 단계는 하나의 작업 및 0 개의 작업으로 미리 구성 됩니다. 파이프라인 메뉴에서 **작업** 을 선택한 다음 **개발** 단계를 선택 합니다.  작업(job) 및 작업(task) 수를 선택하여 이 스테이지에서 작업(task)을 구성합니다.

    ![개발 작업 구성](./media/how-to-ci-cd/view-stage-tasks.png)

8. **개발** 단계에서 기본 **에이전트 작업이**표시 됩니다. 에이전트 작업(job)의 세부 정보를 구성할 수 있지만, 배포 작업(task)은 플랫폼을 구분하지 않으므로 **에이전트 풀**(또는 직접 관리하는 다른 에이전트)에서 **Hosted VS2017** 또는 **Hosted Ubuntu 1604**를 사용할 수 있습니다.

9. 더하기 기호 (**+**)를 선택 하 여 두 개의 작업을 추가 합니다. **Azure IoT Edge** 를 검색 하 고 두 번 추가 합니다.

    ![개발에 대 한 작업 추가](./media/how-to-ci-cd/add-task-qa.png)

10. 첫 번째 **Azure IoT Edge** 작업을 선택 하 고 다음 값으로 구성 합니다.

    * **표시 이름**: 작업 필드가 변경 될 때 표시 이름이 자동으로 업데이트 됩니다.
    * **작업**: 드롭다운 목록을 사용 하 여 **배포 매니페스트 생성**을 선택 합니다. 작업(action) 값을 변경하면 일치하도록 작업(task) 표시 이름도 업데이트됩니다.
    * **. template. json 파일**: 경로 `$(System.DefaultWorkingDirectory)/Drop/drop/deployment.template.json`를 저장 합니다. 경로는 빌드 파이프라인에서 게시 됩니다.
    * **기본 플랫폼**: 모듈 이미지를 빌드할 때 동일한 값을 선택 합니다.
    * **출력 경로**: 경로 `$(System.DefaultWorkingDirectory)/Drop/drop/configs/deployment.json`를 저장 합니다. 이 경로는 최종 IoT Edge 배포 매니페스트 파일입니다.

    이러한 구성은 `deployment.template.json` 파일의 모듈 이미지 url을 대체 하는 데 도움이 됩니다. 또한 **생성 된 배포 매니페스트** 를 사용 하면 변수를 `deployment.template.json` 파일에서 정의한 정확한 값으로 바꿀 수 있습니다. VS/VS Code에서 `.env` 파일의 실제 값을 지정 합니다. Azure Pipelines의 릴리스 파이프라인 변수 탭에서 값을 설정 합니다. [변수] 탭으로 이동 하 고 이름 및 값을 다음과 같이 구성 합니다.

    * **ACR_ADDRESS**: Azure Container Registry 주소입니다.
    * **ACR_PASSWORD**: Azure Container Registry 암호입니다.
    * **ACR_USER**: Azure Container Registry 사용자 이름입니다.

    프로젝트에 다른 변수가 있는 경우이 탭에서 이름 및 값을 지정할 수 있습니다. **배포 매니페스트 생성** 은 변수를 `${VARIABLE}` 버전 으로만 인식할 수 있으며, `*.template.json` 파일에서이를 사용 하 고 있는지 확인 합니다.

    ![릴리스 파이프라인에 대 한 변수 구성](./media/how-to-ci-cd/configure-variables.png)

11. 두 번째 **Azure IoT Edge** 작업을 선택 하 고 다음 값으로 구성 합니다.

    * **표시 이름**: 작업 필드가 변경 될 때 표시 이름이 자동으로 업데이트 됩니다.
    * **작업**: 드롭다운 목록을 사용 하 여 **IoT Edge 장치에 배포**를 선택 합니다. 작업(action) 값을 변경하면 일치하도록 작업(task) 표시 이름도 업데이트됩니다.
    * **Azure 구독**: IoT Hub 포함 된 구독을 선택 합니다.
    * **IoT Hub 이름**: IoT Hub를 선택 합니다.
    * **단일/다중 장치 선택**: 릴리스 파이프라인을 하나 이상의 장치에 배포할지 아니면 여러 장치에 배포할지를 선택 합니다.
      * 단일 디바이스에 배포하는 경우 **IoT Edge 디바이스 ID**를 입력합니다.
      * 여러 디바이스에 배포하는 경우 디바이스 **대상 조건**을 지정합니다. 대상 조건은 IoT Hub의 IoT Edge 장치 집합과 일치 하는 필터입니다. 디바이스 태그를 조건으로 사용하려는 경우 IoT Hub 디바이스 쌍으로 해당 디바이스 태그를 업데이트해야 합니다. 고급 설정에서 **IoT Edge 배포 ID** 및 **IoT Edge 배포 우선 순위**를 업데이트합니다. 여러 디바이스용 배포를 만드는 방법에 대한 자세한 내용은 [IoT Edge 자동 배포 이해](module-deployment-monitoring.md)를 참조하세요.
    * 고급 설정을 확장 하 고, **IoT Edge 배포 ID**를 선택 `$(System.TeamProject)-$(Release.EnvironmentName)`하 고, 변수를 입력 합니다. 그러면 프로젝트와 릴리스 이름이 IoT Edge 배포 ID에 매핑됩니다.

12. **저장**을 선택하여 새 릴리스 파이프라인의 변경 사항을 저장합니다. 메뉴에서 **파이프라인**을 선택하여 파이프라인 보기로 돌아갑니다.

## <a name="verify-iot-edge-cicd-with-the-build-and-release-pipelines"></a>빌드 및 릴리스 파이프라인을 사용하여 IoT Edge CI/CD 확인

빌드 작업을 트리거하려면 소스 코드 리포지토리에 커밋을 푸시하거나 수동으로 트리거할 수 있습니다. 이 섹션에서는 CI/CD 파이프라인을 수동으로 트리거하여 작동하는지 테스트합니다. 그런 다음, 배포에 성공했는지 확인합니다.

1. 이 문서의 시작 부분에서 만든 빌드 파이프라인으로 이동합니다.

2. 다음 스크린샷과 같이 **큐** 단추를 선택하여 빌드 파이프라인에서 빌드 작업을 트리거할 수 있습니다.

    ![수동 트리거](./media/how-to-ci-cd/manual-trigger.png)

3. 빌드 작업을 선택하여 해당 진행 상태를 확인합니다. 빌드 파이프라인이 성공적으로 완료 되 면 **개발** 단계로 릴리스를 트리거합니다.

    ![빌드 로그](./media/how-to-ci-cd/build-logs.png)

4. 성공적인 **dev** 릴리스는 대상 IoT Edge 장치에 IoT Edge 배포를 만듭니다.

    ![Dev로 릴리스](./media/how-to-ci-cd/pending-approval.png)

5. **개발** 단계를 클릭 하 여 릴리스 로그를 확인 합니다.

    ![릴리스 로그](./media/how-to-ci-cd/release-logs.png)

## <a name="next-steps"></a>다음 단계

* [IoT Edge에 대 한 Azure Devops 프로젝트](how-to-devops-project.md) 의 devops 모범 사례 샘플 IoT Edge
* [단일 디바이스 또는 대규모 IoT Edge 배포에 대한 이해](module-deployment-monitoring.md)를 통해 IoT Edge 배포 이해
* [대규모 IoT Edge 모듈 배포 및 모니터링](how-to-deploy-at-scale.md)에서 배포를 생성, 업데이트 또는 삭제하는 단계를 연습합니다.
