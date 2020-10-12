---
title: Azure DevOps 스타터를 사용 하는 CI/CD 파이프라인-Azure IoT Edge | Microsoft Docs
description: Azure DevOps Starter를 사용하면 Azure를 쉽게 시작할 수 있습니다. 몇 단계로 원하는 Azure IoT Edge 앱을 시작할 수 있습니다.
author: shizn
ms.author: kgremban
ms.date: 08/25/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: f0f0452854ef7456d1bdf7caf0674fab5bd14557
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91297133"
---
# <a name="create-a-cicd-pipeline-for-iot-edge-with-azure-devops-starter"></a>Azure DevOps 스타터를 사용 하 여 IoT Edge에 대 한 CI/CD 파이프라인 만들기

DevOps 프로젝트를 사용하여 IoT Edge 애플리케이션에 대한 지속적인 통합(CI) 및 지속적인 업데이트(CD)를 구성합니다. DevOps Starter는 Azure Pipelines에서 빌드 및 릴리스 파이프라인의 초기 구성을 간소화합니다.

활성 Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free)을 만드세요.

## <a name="sign-in-to-the-azure-portal"></a>Azure Portal에 로그인

DevOps Starter는 Azure DevOps에 CI/CD 파이프라인을 만듭니다. 새 Azure DevOps 조직을 만들거나 기존 조직을 사용할 수 있습니다. 또한 DevOps Starter는 선택한 Azure 구독에서 Azure 리소스를 만듭니다.

1. [Microsoft Azure 포털](https://portal.azure.com)에 로그인합니다.

1. 왼쪽 창에서 **리소스 만들기**를 선택 하 고 **devops 스타터**를 검색 합니다.  

1. **만들기**를 선택합니다.

## <a name="create-a-new-application-pipeline"></a>새 애플리케이션 파이프라인 만들기

1. Azure IoT Edge 모듈은[C#](tutorial-csharp-module.md), [Node.js](tutorial-node-module.md), [Python](tutorial-python-module.md), [C](tutorial-c-module.md) 및 [Java](tutorial-java-module.md)로 작성할 수 있습니다. **.NET**, **Node.js**, **Python**, **C** 또는 **Java** 중에 원하는 언어를 선택하여 새 애플리케이션을 시작합니다. **다음**을 선택하여 계속합니다.

   ![언어를 선택하여 새 애플리케이션을 만듭니다.](./media/how-to-devops-starter/select-language.png)

2. 애플리케이션 프레임워크로 **단순 IoT**를 선택하고, **다음**을 선택합니다.

   ![Simple IoT 프레임 워크 선택](media/how-to-devops-starter/select-iot.png)

3. 애플리케이션을 배포하는 Azure 서비스로 **IoT Edge**를 선택하고, **다음**을 선택합니다.

   ![IoT Edge 서비스 선택](media/how-to-devops-starter/select-iot-edge.png)

4. 새 무료 Azure DevOps 조직을 만들거나 기존 조직을 선택합니다.

   1. 프로젝트 이름을 입력합니다.

   2. Azure DevOps 조직을 선택합니다. 기존 조직이 없는 경우 **추가 설정**을 선택하여 새로 만듭니다.

   3. Azure 구독을 선택합니다.

   4. 프로젝트 이름으로 생성된 IoT Hub를 사용하거나 직접 이름을 입력합니다.

   5. 기본 위치를 그대로 적용하거나 본인에게 가까운 위치를 선택합니다.

   6. **추가 설정을** 선택 하 여 Devops 스타터에서 사용자를 대신 하 여 만드는 Azure 리소스를 구성 합니다.

   7. **완료**를 선택하여 프로젝트 만들기를 마칩니다.

   ![이름 및 프로젝트 만들기](media/how-to-devops-starter/create-project.png)

잠시 후에 DevOps Starter 대시보드가 Azure Portal에 표시됩니다. 진행률을 보려면 프로젝트 이름을 선택합니다. 페이지를 새로 고쳐야 할 수도 있습니다. 샘플 IoT Edge 애플리케이션이 Azure DevOps 조직의 리포지토리에서 설정되고, 빌드가 실행되고, 애플리케이션이 IoT Edge 디바이스에 배포됩니다. 이 대시보드에서는 코드 리포지토리, CI/CD 파이프라인 및 Azure의 애플리케이션에 가시성을 제공합니다.

   ![Azure Portal에서 프로젝트 보기](./media/how-to-devops-starter/portal.png)

## <a name="commit-code-changes-and-execute-cicd"></a>코드 변경 내용 커밋 및 CI/CD 실행

DevOps 스타터는 Azure Repos에서 프로젝트에 대 한 Git 리포지토리를 만들었습니다. 이 섹션에서는 리포지토리를 살펴보고 애플리케이션의 코드를 변경합니다.

1. 프로젝트에 대해 생성된 리포지토리로 이동하려면 프로젝트 대시보드 메뉴에서 **리포지토리**를 선택합니다. 이 링크는 새 프로젝트에 대 한 브라우저 탭 및 Azure DevOps 리포지토리를 엽니다.

   ![Azure Repos에서 생성된 리포지토리 보기](./media/how-to-devops-starter/view-repositories.png)

> [!NOTE]
> 다음은 웹 브라우저를 사용하여 코드를 변경하는 단계입니다. 리포지토리를 로컬로 복제하려면 창의 오른쪽 위에서 **복제**를 선택합니다. 제공된 URL을 사용하여 Visual Studio Code 또는 원하는 개발 도구에 Git 리포지토리를 복제합니다.

2. 리포지토리에는 사용자가 생성 프로세스에서 선택한 애플리케이션 언어를 기반으로 하는 **FilterModule**이라는 모듈의 코드가 이미 포함되어 있습니다. **modules/FilterModule/module.json** 파일을 엽니다.

   ![Azure Repos에서 module.json 파일 열기](./media/how-to-devops-starter/open-module-json.png)

3. 이 파일은 **version** 매개 변수에서 [Azure DevOps 빌드 변수](https://docs.microsoft.com/azure/devops/pipelines/build/variables?view=vsts#build-variables)를 사용합니다. 이 구성을 사용하면 새 빌드가 실행될 때마다 모듈의 새 버전이 생성됩니다.

## <a name="examine-the-cicd-pipeline"></a>CD 파이프라인 검토

이전 섹션에서 Azure DevOps 스타터는 IoT Edge 응용 프로그램에 대 한 전체 CI/CD 파이프라인을 자동으로 구성 했습니다. 이제 필요에 따라 파이프라인을 살펴보고 사용자 지정합니다. 다음 단계에 따라 Azure DevOps 빌드 및 릴리스 파이프라인을 숙지합니다.

1. DevOps 프로젝트에서 빌드 파이프라인을 보려면 프로젝트 대시보드 메뉴에서 **빌드 파이프라인**을 선택합니다. 이 링크는 브라우저 탭 및 새 프로젝트에 대한 Azure DevOps 빌드 파이프라인을 엽니다.

   ![Azure Pipelines에서 빌드 파이프라인 보기](./media/how-to-devops-starter/view-build-pipelines.png)

2. 자동으로 생성 된 빌드 파이프라인을 열고 오른쪽 위에 있는 **편집** 을 선택 합니다.

    ![빌드 파이프라인 편집](media/how-to-devops-starter/click-edit-button.png)

3. 열리는 패널에서 빌드 파이프라인이 실행될 때 발생하는 작업을 검사할 수 있습니다. 빌드 파이프라인은 Git 리포지토리에서 원본 가져오기, IoT Edge 모듈 이미지 빌드, 컨테이너 레지스트리에 IoT Edge 푸시, 배포에서 사용되는 출력 게시 등의 다양한 작업을 수행합니다. Azure DevOps의 Azure IoT Edge 작업에 대한 자세한 내용은 [지속적인 통합을 위한 Azure Pipelines 구성](how-to-continuous-integration-continuous-deployment-classic.md#create-a-build-pipeline-for-continuous-integration)을 참조하세요.

4. 파이프라인 세부 정보를 열려면 빌드 파이프라인의 맨 위에서 **파이프라인** 헤더를 선택합니다. 빌드 파이프라인의 이름을 좀 더 구체적인 이름으로 변경합니다.

   ![파이프라인 세부 정보 편집](./media/how-to-devops-starter/edit-build-pipeline.png)

5. **저장 및 큐**를 선택한 다음, **저장**을 선택합니다. 주석에서 선택 사항입니다.

6. 빌드 파이프라인 메뉴에서 **트리거**를 선택합니다. DevOps Starter는 CI 트리거를 자동으로 생성했으며 리포지토리에 대한 모든 커밋이 새 빌드를 시작합니다.  필요에 따라 CI 프로세스에서 분기를 포함할지를 선택할 수 있습니다.

7. **보존**을 선택합니다. 링크를 따라 보존 정책이 있는 프로젝트 설정으로 이동 합니다. 시나리오에 따라 특정 수의 빌드를 유지하거나 제거하는 정책을 지정할 수 있습니다.

8. **기록**을 선택합니다. [기록] 창에는 최근 빌드 변경 내용의 감사 내역이 포함되어 있습니다. Azure Pipelines는 빌드 파이프라인에 대한 모든 변경 내용을 계속 추적하고 버전을 비교할 수 있습니다.

9. 빌드 파이프라인을 모두 살펴본 후에는 해당 릴리스 파이프라인으로 이동합니다. **파이프라인** 아래에서 **릴리스**를 선택한 다음, **편집**을 선택하여 파이프라인 세부 정보를 봅니다.

    ![릴리스 파이프라인 보기](media/how-to-devops-starter/release-pipeline.png)

10. **아티팩트** 아래에서 **드롭**을 선택합니다. 이 아티팩트가 감시하는 소스는 이전 단계에서 검사한 빌드 파이프라인의 출력입니다.

11. **드롭** 아이콘 옆에서 번개처럼 생긴 **지속적인 배포 트리거**를 선택합니다. 이 릴리스 파이프라인은 새 빌드 아티팩트를 사용할 수 있을 때마다 배포를 실행하는 트리거를 사용하도록 설정했습니다. 필요에 따라 트리거를 비활성화할 수 있으므로 배포는 수동 실행이 필수적입니다.  

12. 릴리스 파이프라인에 대한 메뉴에서 **작업**을 선택한 다음, 드롭다운 목록에서 **개발** 단계를 선택합니다. DevOps Projects에서는 IoT Hub를 만들고, 해당 허브에서 IoT Edge 디바이스를 만들고, 빌드 파이프라인의 샘플 모듈을 배포하고, IoT Edge 디바이스로 실행될 가상 머신을 프로비전하는 릴리스 단계를 만들었습니다. CD에 대한 Azure IoT Edge 작업의 자세한 내용은 [지속적인 배포를 위한 Azure Pipelines 구성](how-to-continuous-integration-continuous-deployment-classic.md#create-a-release-pipeline-for-continuous-deployment)을 참조하세요.

    ![지속적인 배포 작업 보기](media/how-to-devops-starter/choose-release.png)

13. 오른쪽에서 **릴리스 보기**를 선택합니다. 이 보기에는 릴리스의 기록이 표시됩니다.

14. 자세한 정보를 보려면 릴리스 이름을 선택합니다.

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않을 경우 만든 Azure App Service 및 기타 관련 리소스를 삭제할 수 있습니다. DevOps Starter 대시보드의 **삭제** 기능을 사용합니다.

## <a name="next-steps"></a>다음 단계

* [Azure IoT Edge에 대한 지속적인 통합 및 지속적인 배포](how-to-continuous-integration-continuous-deployment.md)에서 Azure DevOps에 대한 Azure IoT Edge 작업에 대해 배우기
* [단일 디바이스 또는 대규모 IoT Edge 배포에 대한 이해](module-deployment-monitoring.md)를 통해 IoT Edge 배포 이해
* [대규모 IoT Edge 모듈 배포 및 모니터링](how-to-deploy-at-scale.md)에서 배포를 생성, 업데이트 또는 삭제하는 단계를 연습합니다.
