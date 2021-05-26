---
title: '자습서: Azure DevOps를 사용하여 Azure Static Web Apps 게시'
description: Azure DevOps를 사용하여 Azure Static Web Apps를 게시하는 방법을 알아봅니다.
services: static-web-apps
author: scubaninja
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 03/23/2021
ms.author: apedward
ms.openlocfilehash: 393b8857b3602d914143787cc9ea46074ff59c05
ms.sourcegitcommit: 0ce834cd348bb8b28a5f7f612c2807084cde8e8f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/12/2021
ms.locfileid: "109813905"
---
# <a name="tutorial-publish-azure-static-web-apps-with-azure-devops"></a>자습서: Azure DevOps를 사용하여 Azure Static Web Apps 게시

이 문서에서는 [Azure DevOps](https://dev.azure.com/)를 사용하여 [Azure Static Web Apps](./overview.md)에 배포하는 방법을 보여 줍니다.

이 자습서에서는 다음에 대해 알아봅니다.

- Azure Static Web Apps 사이트 설정
- Azure DevOps 파이프라인을 만들어 정적 웹앱 빌드 및 게시

## <a name="prerequisites"></a>필수 구성 요소

- **활성 Azure 계정:** 계정이 없는 경우 [체험 계정을 만들](https://azure.microsoft.com/free/) 수 있습니다.
- **Azure DevOps 프로젝트:** 프로젝트가 없으면 [프로젝트를 무료로 만들](https://azure.microsoft.com/pricing/details/devops/azure-devops-services/) 수 있습니다.
- **Azure DevOps 파이프라인:** 시작하는 데 도움이 필요한 경우 [첫 번째 파이프라인 만들기](/azure/devops/pipelines/create-first-pipeline?preserve-view=true&view=azure-devops)를 참조하세요.

## <a name="create-a-static-web-app-in-an-azure-devops-repository"></a>Azure DevOps 리포지토리에 정적 웹앱 만들기

  > [!NOTE]
  > 리포지토리에 기존 앱이 있는 경우 다음 섹션으로 건너뛸 수 있습니다.

1. Azure DevOps 리포지토리로 이동합니다.

1. **가져오기** 를 선택하여 샘플 애플리케이션 가져오기를 시작합니다.
  
    :::image type="content" source="media/publish-devops/devops-repo.png" alt-text="DevOps 리포지토리":::

1. **복제 URL** 에서 `https://github.com/staticwebdev/vanilla-api.git`을 입력합니다.

1. **가져오기** 를 선택합니다.

## <a name="create-a-static-web-app"></a>정적 웹앱 만들기

1. [Azure Portal](https://portal.azure.com)로 이동합니다.

1. **리소스 만들기** 를 선택합니다.

1. **Static Web Apps** 를 검색합니다.

1. **Static Web Apps** 를 선택합니다.

1. **만들기** 를 선택합니다.

1. _배포 세부 정보_ 아래에서 **기타** 를 선택했는지 확인합니다. 이렇게 하면 Azure DevOps 리포지토리에서 코드를 사용할 수 있습니다.

    :::image type="content" source="media/publish-devops/create-resource.png" alt-text="배포 세부 정보 - 기타":::

1. 배포가 성공적으로 완료되면 새 Static Web Apps 리소스로 이동합니다.

1. **배포 토큰 관리** 를 선택합니다.

1. **배포 토큰** 을 복사하고, 다른 화면에서 사용할 수 있도록 텍스트 편집기에 붙여넣습니다.

    > [!NOTE]
    > 다음 단계에서 더 많은 값을 복사하여 붙여넣을 수 있으므로 이 값은 현재 별도로 설정되어 있습니다.

    :::image type="content" source="media/publish-devops/deployment-token.png" alt-text="배포 토큰":::

## <a name="create-the-pipeline-task-in-azure-devops"></a>Azure DevOps에서 파이프라인 작업 만들기

1. 이전에 만든 Azure DevOps 리포지토리로 이동합니다.

1. **빌드 설정** 을 선택합니다.

    :::image type="content" source="media/publish-devops/azdo-build.png" alt-text="빌드 파이프라인":::

1. *파이프라인 구성* 화면에서 **시작 파이프라인** 을 선택합니다.

    :::image type="content" source="media/publish-devops/configure-pipeline.png" alt-text="파이프라인 구성":::

1. 다음 YAML을 복사하여 파이프라인에 붙여넣습니다.

    ```yaml
    trigger:
    - main

    pool:
      vmImage: ubuntu-latest

    steps:
      - checkout: self
        submodules: true
      - task: AzureStaticWebApp@0
        inputs:
          app_location: '/'
          api_location: 'api'
          output_location: ''
        env:
          azure_static_web_apps_api_token: $(deployment_token)
    ```

    > [!NOTE]
    > 샘플 앱을 사용하지 않는 경우 애플리케이션의 값과 일치하도록 `app_location`, `api_location` 및 `output_location`의 값을 변경해야 합니다.

    [!INCLUDE [static-web-apps-folder-structure](../../includes/static-web-apps-folder-structure.md)]

    `azure_static_web_apps_api_token` 값은 자체적으로 관리되며 수동으로 구성됩니다.

2. **변수** 를 선택합니다.

3. 새 변수를 만듭니다.

4. 변수 이름을 **deployment_token** 으로 지정합니다(워크플로의 이름과 일치).

5. 이전에 텍스트 편집기에 붙여넣은 배포 토큰을 복사합니다.

6. _값_ 상자에서 배포 토큰을 붙여넣습니다.

    :::image type="content" source="media/publish-devops/variable-token.png" alt-text="변수 토큰":::

7. **이 값을 비밀로 유지** 를 선택합니다.

8. **확인** 을 선택합니다.

9. **저장** 을 선택하여 파이프라인 YAML로 돌아갑니다.

10. **저장 및 실행** 을 선택하여 _저장 및 실행_ 대화 상자를 엽니다.

    :::image type="content" source="media/publish-devops/save-and-run.png" alt-text="파이프라인":::

11. **저장 및 실행** 을 선택하여 파이프라인을 실행합니다.

12. 배포가 성공적으로 완료되면 배포 구성에 대한 링크가 포함된 Azure Static Web Apps **개요** 로 이동합니다. 이제 _원본_ 링크에서 Azure DevOps 리포지토리의 분기 및 위치를 가리키는 방법을 확인합니다.

13. **URL** 을 선택하여 새로 배포된 웹 사이트를 표시합니다.

    :::image type="content" source="media/publish-devops/deployment-location.png" alt-text="배포 위치":::

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure Static Web Apps 구성](./configuration.md)