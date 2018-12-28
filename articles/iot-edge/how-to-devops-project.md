---
title: Azure DevOps Projects - Azure IoT Edge를 사용한 CI/CD 파이프라인 | Microsoft Docs
description: Azure DevOps Projects를 사용하면 Azure를 쉽게 시작할 수 있습니다. 몇 단계로 원하는 Azure IoT Edge 앱을 시작할 수 있습니다.
author: shizn
manager: ''
ms.author: xshi
ms.date: 12/04/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: ebb7e515f9d9205f364d50b3d686c68a2988f86a
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/08/2018
ms.locfileid: "53074217"
---
# <a name="create-a-cicd-pipeline-for-iot-edge-with-azure-devops-projects-preview"></a>Azure DevOps Projects(미리 보기)를 사용하여 IoT Edge에 대한 CI/CD 파이프라인 만들기

DevOps 프로젝트를 사용하여 IoT Edge 애플리케이션에 대한 지속적인 통합(CI) 및 지속적인 업데이트(CD)를 구성합니다. DevOps Projects는 Azure Pipelines에서 빌드 및 릴리스 파이프라인의 초기 구성을 간소화합니다.

활성 Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free)을 만드세요.

## <a name="sign-in-to-the-azure-portal"></a>Azure Portal에 로그인

DevOps Projects는 Azure DevOps에 CI/CD 파이프라인을 만듭니다. 새 Azure DevOps 조직을 만들거나 기존 조직을 사용할 수 있습니다. 또한 DevOps Projects는 선택한 Azure 구독에서 Azure 리소스를 만듭니다.

1. [Microsoft Azure 포털](https://portal.azure.com)에 로그인합니다.

1. 왼쪽 창의 왼쪽 탐색 모음에서 **리소스 만들기** 아이콘을 선택한 다음, **DevOps Projects**를 검색합니다.  

1.  **만들기**를 선택합니다.

## <a name="select-a-sample-application-and-azure-service"></a>샘플 응용 프로그램 및 Azure 서비스 선택

1. Azure IoT Edge 모듈은[C#](tutorial-csharp-module.md), [Node.js](tutorial-node-module.md), [Python](tutorial-python-module.md), [C](tutorial-c-module.md) 및 [Java](tutorial-java-module.md)로 작성할 수 있습니다. 원하는 언어를 선택한 다음 새 애플리케이션을 시작합니다. 마찬가지로 **.NET**, **Node.js**, **Python**, **C** 또는 **Java**를 선택한 다음 **다음**을 클릭할 수 있습니다.

    ![언어를 선택하여 새 애플리케이션을 만듭니다.](./media/how-to-devops-project/select-language.png)

2. **Simple IoT (미리 보기)** 를 선택한 다음 **다음**을 클릭합니다.

    ![Simple IoT 프레임 워크 선택](media/how-to-devops-project/select-iot.png)

3. **IoT Edge** 선택한 후 **다음**을 클릭합니다.

    ![IoT Edge 서비스 선택](media/how-to-devops-project/select-iot-edge.png)

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Azure DevOps 및 Azure 구독 구성

1. 새 무료 Azure DevOps 조직을 만들거나 기존 조직을 선택합니다.

    a. 프로젝트의 이름을 선택합니다. 

    b. Azure 구독 및 위치를 선택하고 애플리케이션의 이름을 선택한 후 **완료**를 선택합니다.  

    ![애플리케이션 이름 지정 및 만들기](media/how-to-devops-project/select-devops.png)

1. 잠시 후에 DevOps Projects 대시보드가 Azure Portal에 표시됩니다. 샘플 IoT Edge 애플리케이션이 Azure DevOps 조직의 리포지토리에서 설정되고, 빌드가 실행되고, 애플리케이션이 IoT Edge 디바이스에 배포됩니다. 이 대시보드에서는 코드 리포지토리, CI/CD 파이프라인 및 Azure의 애플리케이션에 가시성을 제공합니다.

    ![DevOps 포털에서 애플리케이션 보기](./media/how-to-devops-project/devops-portal.png)


## <a name="commit-code-changes-and-execute-cicd"></a>코드 변경 내용 커밋 및 CI/CD 실행

DevOps Projects는 Azure Repos 또는 GitHub에 Git 리포지토리를 만들었습니다. 리포지토리를 보고 코드 변경을 애플리케이션에 적용하려면 다음 단계를 실해하세요.

1. DevOps Projects 대시보드 왼쪽에서 **마스터** 분기에 대한 링크를 선택합니다.  
이 링크는 새로 생성된 Git 리포지토리 보기를 엽니다.

1. 리포지토리 복제 URL을 보려면 브라우저의 오른쪽 위에서 **복제**를 선택합니다. VS Code 또는 원하는 다른 도구에 Git 리포지토리를 복제할 수 있습니다. 다음 몇 단계로 웹 브라우저를 사용하여 마스터 브랜치에 코드 변경을 실행하고 커밋합니다.

    ![Git 리포지토리 복제](media/how-to-devops-project/clone.png)

1. 브라우저의 왼쪽에 있는 **modules/FilterModule/module.json** 파일로 이동합니다.

1. **편집**을 선택한 다음 `"tag"` 아래에 있는 `"version"`을 변경합니다. 예를 들어 `"version": "${BUILD_BUILDID}"`으로 업데이트하여 Azure IoT Edge 모듈 이미지 태그의 일부로 [Azure DevOps 빌드 변수](https://docs.microsoft.com/azure/devops/pipelines/build/variables?view=vsts#build-variables)를 사용할 수 있습니다.

    ![빌드 변수를 수락하도록 버전 편집](media/how-to-devops-project/update-module-json.png)

1. **커밋**을 선택하고 변경 내용을 저장합니다.

1. 브라우저에서 Azure DevOps 프로젝트 대시보드로 이동합니다.  이제 빌드가 진행되고 있음을 확인해야 합니다. 변경한 내용은 자동으로 빌드되며 CI/CD 파이프라인을 통해 배포됩니다.

    ![진행 중인 상태 보기](media/how-to-devops-project/ci-cd-in-progress.png)

## <a name="examine-the-cicd-pipeline"></a>CD 파이프라인 검토

이전 단계에서 Azure DevOps Projects를 실행하면 IoT Edge 애플리케이션에 대한 전체 CI/CD 파이프라인이 자동으로 구성됩니다. 필요에 따라 파이프라인을 탐색하고 사용자 지정합니다. Azure DevOps 빌드 및 릴리스 파이프라인을 숙지하려면 다음을 수행합니다.

1. DevOps Projects 대시보드 맨 위에서 **빌드 파이프라인**을 선택합니다.  
이 링크는 브라우저 탭 및 새 프로젝트에 대한 Azure DevOps 빌드 파이프라인을 엽니다.

1. **편집**을 선택합니다.

    ![빌드 파이프라인 편집](media/how-to-devops-project/click-edit-button.png)

1. 이 창에서 빌드 파이프라인의 다양한 작업을 검사할 수 있습니다. 빌드는 Git 리포지토리에서 원본 가져오기, IoT Edge 모듈 빌드, IoT Edge 푸싱 및 배포에서 사용된 출력 게시 등 다양한 작업을 수행합니다. CI에 대한 Azure IoT Edge 작업에 대한 자세한 내용은 [지속적인 배포를 위한 Azure 파아프라인 구성](https://docs.microsoft.com/azure/iot-edge/how-to-ci-cd#configure-azure-pipelines-for-continuous-integration)을 참조할 수 있습니다.

    ![지속적인 통합 작업 보기](media/how-to-devops-project/ci.png)

1. 빌드 파이프라인의 맨 위에서 빌드 파이프라인 이름을 선택합니다.

1. 빌드 파이프라인의 이름을 좀 더 구체적인 것으로 변경하고 **저장 및 큐에 넣기**, **저장**을 차례로 선택합니다.

1. 빌드 파이프라인 이름에서 **기록**을 선택합니다.   
**기록** 창에 빌드에 대한 최근 변경 내용의 감사 내역이 표시됩니다.  Azure Pipelines는 빌드 파이프라인에 대한 모든 변경 내용을 계속 추적하고 버전을 비교할 수 있습니다.

1. **트리거**를 선택합니다. DevOps Projects는 CI 트리거를 자동으로 생성하며, 리포지토리에 대한 모든 커밋이 새 빌드를 시작합니다.  필요에 따라 CI 프로세스에서 분기를 포함할지를 선택할 수 있습니다.

1. **보존**을 선택합니다. 시나리오에 따라 특정 수의 빌드를 유지하거나 제거하는 정책을 지정할 수 있습니다.

1. **파이프라인** 아래에 있는 **릴리스**를 선택합니다. Azure DevOps Projects는 Azure IoT Edge에 대한 배포를 관리하는 릴리스 파이프라인을 만듭니다.

    ![릴리스 파이프라인 보기](media/how-to-devops-project/release-pipeline.png)

1. **편집**을 선택합니다. 릴리스 파이프라인에는 릴리스 프로세스를 정의하는 파이프라인이 포함됩니다.  

1. **아티팩트** 아래에서 **드롭**을 선택합니다. 이전 단계에서 검사한 빌드 파이프라인이 아티팩트에 사용된 출력을 생성합니다. 

1. **드롭** 아이콘 옆에서 **지속적인 배포 트리거**를 선택합니다.  
이 릴리스 파이프라인은 새 빌드 아티팩트를 사용할 수 있을 때마다 배포를 실행하는 CD 트리거를 사용하도록 설정했습니다. 필요에 따라 트리거를 비활성화할 수 있으므로 배포는 수동 실행이 필수적입니다.  

1. 왼쪽에서 **작업**을 선택합니다. 작업은 배포 프로세스가 수행하는 활동입니다. 이 예제에서는Azure IoT Edge에 대한 모듈 이미지를 배포하기 위해 작업을 만들었습니다. CD에 대한 Azure IoT Edge 작업에 관한 자세한 내용은 [지속적인 통합을 위한 Azure 파아프라인 구성](https://docs.microsoft.com/azure/iot-edge/how-to-ci-cd#configure-azure-pipelines-for-continuous-deployment)을 참조할 수 있습니다.

    ![지속적인 배포 작업 보기](media/how-to-devops-project/dev-release.png)

1. 오른쪽에서 **릴리스 보기**를 선택합니다. 이 보기에는 릴리스의 기록이 표시됩니다.

1. 한 릴리스 옆에 있는 줄임표(...)를 선택하고 **열기**를 선택합니다.  
릴리스 요약, 연결된 작업 항목 및 테스트 등 여러 메뉴를 탐색할 수 있습니다.

1. **커밋**을 선택합니다. 이 보기에는 특정 배포와 연결된 코드 커밋이 표시됩니다. 

1. **로그**를 선택합니다. 로그에는 배포 프로세스에 대한 유용한 정보가 포함됩니다. 배포 도중 및 이후 모두에서 로그를 볼 수 있습니다.


## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않을 경우 만든 Azure App Service 및 기타 관련 리소스를 삭제할 수 있습니다. DevOps Projects 대시보드에서 **삭제** 기능을 사용합니다.

## <a name="next-steps"></a>다음 단계
* [Azure IoT Edge에 대한 지속적인 통합 및 지속적인 배포](how-to-ci-cd.md)에서 Azure DevOps에 대한 Azure IoT Edge 작업에 대해 배우기
* [단일 디바이스 또는 대규모 IoT Edge 배포에 대한 이해](module-deployment-monitoring.md)를 통해 IoT Edge 배포 이해
* [대규모 IoT Edge 모듈 배포 및 모니터링](how-to-deploy-monitor.md)에서 배포를 생성, 업데이트 또는 삭제하는 단계를 연습합니다.
