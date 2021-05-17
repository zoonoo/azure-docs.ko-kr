---
title: Azure DevOps를 사용하여 Stream Analytics 작업용 CI/CD 파이프라인 만들기
description: 이 문서에서는 Azure DevOps에서 Azure Stream Analytics 작업용 CI/CD(지속적인 통합 및 배포) 파이프라인을 설정하는 방법에 대해 설명합니다.
services: stream-analytics
author: su-jie
ms.author: sujie
ms.service: stream-analytics
ms.topic: how-to
ms.date: 09/10/2020
ms.openlocfilehash: 82a2c3047f851c9fbc273cd13e730572c38b6bcd
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105640371"
---
# <a name="use-azure-devops-to-create-a-cicd-pipeline-for-a-stream-analytics-job"></a>Azure DevOps를 사용하여 Stream Analytics 작업용 CI/CD 파이프라인 만들기

이 문서에서는 Azure Stream Analytics CI/CD 도구를 사용하여 Azure DevOps [빌드](/azure/devops/pipelines/get-started/pipelines-get-started) 및 [릴리스](/azure/devops/pipelines/release/define-multistage-release-process) 파이프라인을 만드는 방법에 대해 알아봅니다.

## <a name="commit-your-stream-analytics-project"></a>Stream Analytics 프로젝트 커밋

시작하기 전에 전체 Stream Analytics 프로젝트를 원본 파일로 [Azure DevOps](/azure/devops/user-guide/source-control) 리포지토리에 커밋합니다. Azure Pipelines에서 이 [샘플 리포지토리](https://dev.azure.com/ASA-CICD-sample/azure-streamanalytics-cicd-demo) 및 [Stream Analytics 프로젝트 소스 코드](https://dev.azure.com/ASA-CICD-sample/_git/azure-streamanalytics-cicd-demo)를 참조할 수 있습니다.

이 문서의 단계에서는 Stream Analytics Visual Studio Code 프로젝트가 사용됩니다. Visual Studio 프로젝트를 사용하는 경우 [CI/CD 도구를 사용하여 Azure Stream Analytics 작업의 빌드, 테스트 및 배포 자동화](cicd-tools.md)의 단계를 따르세요.

## <a name="create-a-build-pipeline"></a>빌드 파이프라인 만들기

이 섹션에서는 빌드 파이프라인을 만드는 방법에 대해 알아봅니다. 

1. 웹 브라우저를 열고 Azure DevOps에서 프로젝트로 이동합니다.  

2. 왼쪽 탐색 메뉴의 **파이프라인** 에서 **빌드** 를 선택합니다. 그런 다음, **새 파이프라인** 을 선택합니다.

   :::image type="content" source="media/set-up-cicd-pipeline/new-pipeline.png" alt-text="새 Azure Pipeline 만들기":::

3. **클래식 편집기 사용** 을 선택해 YAML 없이 파이프라인을 만듭니다.

4. 원본 형식, 팀 프로젝트 및 리포지토리를 선택합니다. 그런 다음, **계속** 을 선택합니다.

   :::image type="content" source="media/set-up-cicd-pipeline/select-repo.png" alt-text="Azure Stream Analytics 프로젝트 선택":::

5. **템플릿 선택** 페이지에서 **빈 작업** 을 선택합니다.

## <a name="install-npm-package"></a>npm 패키지 설치

1. **작업** 페이지에서 **에이전트 작업 1** 옆에 있는 더하기 기호를 선택합니다. 작업 검색에서 *npm* 을 입력하고 **npm** 을 선택합니다.

   :::image type="content" source="media/set-up-cicd-pipeline/search-npm.png" alt-text="npm 작업 선택":::

2. 작업에 **표시 이름** 을 지정합니다. **명령** 옵션을 *사용자 지정* 으로 변경하고 **명령 및 인수** 에 다음 명령을 입력합니다. 나머지 기본 옵션은 그대로 둡니다.

   ```bash
   install -g azure-streamanalytics-cicd
   ```

   :::image type="content" source="media/set-up-cicd-pipeline/npm-config.png" alt-text="npm 작업에 대한 구성 입력":::

호스팅 Linux 에이전트를 사용해야 하는 경우 다음 단계를 사용합니다.
1.  **에이전트 사양** 선택
   
    :::image type="content" source="media/set-up-cicd-pipeline/select-linux-agent.png" alt-text="에이전트 사양 선택 스크린샷.":::

2.  **작업** 페이지에서 **에이전트 작업 1** 옆에 있는 더하기 기호를 선택합니다. 작업 검색에 *명령줄* 을 입력하고 **명령줄** 을 선택합니다.
   
    :::image type="content" source="media/set-up-cicd-pipeline/cmd-search.png" alt-text="명령줄 작업 검색의 스크린샷.":::

3.  작업에 **표시 이름** 을 지정합니다. **스크립트** 에 다음 명령을 입력합니다. 나머지 기본 옵션은 그대로 둡니다.

      ```bash
      sudo npm install -g azure-streamanalytics-cicd --unsafe-perm=true --allow-root
      ```
      :::image type="content" source="media/set-up-cicd-pipeline/cmd-scripts.png" alt-text="cmd 작업에 대한 스크립트 입력 스크린샷.":::

## <a name="add-a-build-task"></a>빌드 작업 추가

1. **변수** 페이지의 **파이프라인 변수** 에서 **+ 추가** 를 선택합니다. 다음 변수를 추가합니다. 기본 설정에 따라 다음 값을 설정합니다.

   |변수 이름|값|
   |-|-|
   |projectRootPath|[YourProjectName]|
   |outputPath|출력|
   |deployPath|배포:|

2. **작업** 페이지에서 **에이전트 작업 1** 옆에 있는 더하기 기호를 선택합니다. **명령줄** 을 검색합니다.

3. 작업에 **표시 이름** 을 지정하고 다음 스크립트를 입력합니다. 리포지토리 이름 및 프로젝트 이름으로 스크립트를 수정합니다.

   ```bash
   azure-streamanalytics-cicd build -project $(projectRootPath)/asaproj.json -outputpath $(projectRootPath)/$(outputPath)/$(deployPath)
   ```

   아래 이미지는 Stream Analytics Visual Studio Code 프로젝트를 예로 사용합니다.

   :::image type="content" source="media/set-up-cicd-pipeline/command-line-config-build.png" alt-text="명령줄 작업 Visual Studio Code에 대한 구성 입력":::

## <a name="add-a-test-task"></a>테스트 작업 추가

1. **변수** 페이지의 **파이프라인 변수** 에서 **+ 추가** 를 선택합니다. 다음 변수를 추가합니다. 출력 경로 및 리포지토리 이름으로 값을 수정합니다.

   |변수 이름|값|
   |-|-|
   |testPath|테스트|

   :::image type="content" source="media/set-up-cicd-pipeline/pipeline-variables-test.png" alt-text="파이프라인 변수 추가":::

2. **작업** 페이지에서 **에이전트 작업 1** 옆에 있는 더하기 기호를 선택합니다. **명령줄** 을 검색합니다.

3. 작업에 **표시 이름** 을 지정하고 다음 스크립트를 입력합니다. 프로젝트 파일 이름 및 테스트 구성 파일의 경로를 사용하여 스크립트를 수정합니다. 

   테스트 사례를 추가하고 구성하는 방법에 대한 자세한 내용은 [자동화된 테스트 지침](cicd-tools.md#automated-test)을 참조하세요.

   ```bash
   azure-streamanalytics-cicd test -project $(projectRootPath)/asaproj.json -outputpath $(projectRootPath)/$(outputPath)/$(testPath) -testConfigPath $(projectRootPath)/test/testConfig.json 
   ```

   :::image type="content" source="media/set-up-cicd-pipeline/command-line-config-test.png" alt-text="명령줄 작업에 대한 구성 입력":::

## <a name="add-a-copy-files-task"></a>파일 복사 작업 추가

파일 복사 작업을 추가하여 테스트 요약 파일 및 Azure Resource Manager 템플릿 파일을 아티팩트 폴더에 복사해야 합니다. 

1. **작업** 페이지에서 **에이전트 작업 1** 옆에 있는 **+** 를 선택합니다. **파일 복사** 를 검색합니다. 그런 다음, 다음 구성을 입력합니다. **콘텐츠** 에 `**`을(를) 할당하면 테스트 결과의 모든 파일이 복사됩니다.

   |매개 변수|입력|
   |-|-|
   |표시 이름|파일 복사 위치: $(build.artifactstagingdirectory)|
   |원본 폴더|`$(system.defaultworkingdirectory)/$(outputPath)/`|
   |콘텐츠| `**` |
   |대상 폴더| `$(build.artifactstagingdirectory)`|

2. **제어 옵션** 을 확장합니다. **작업 실행** 에서  **이전 작업에 실패했더라도 빌드가 취소되지 않은 경우** 를 선택합니다.

   :::image type="content" source="media/set-up-cicd-pipeline/copy-config.png" alt-text="복사 작업에 대한 구성 입력":::

## <a name="add-a-publish-build-artifacts-task"></a>빌드 아티팩트 게시 작업 추가

1. **작업** 페이지에서 **에이전트 작업 1** 옆에 있는 더하기 기호를 선택합니다. **빌드 아티팩트 게시** 를 검색하고 검은색 화살표 아이콘을 사용하여 옵션을 선택합니다.

2. **제어 옵션** 을 확장합니다. **작업 실행** 에서  **이전 작업에 실패했더라도 빌드가 취소되지 않은 경우** 를 선택합니다.

   :::image type="content" source="media/set-up-cicd-pipeline/publish-config.png" alt-text="게시 작업에 대한 구성 입력":::

## <a name="save-and-run"></a>저장 및 실행

npm 패키지, 명령줄, 파일 복사 및 빌드 아티팩트 게시 작업을 모두 추가했으면 **저장 및 큐** 를 선택합니다. 메시지가 표시되면 저장 주석을 입력하고 **저장 및 실행** 을 선택합니다. 파이프라인의 **요약** 페이지에서 테스트 결과를 다운로드할 수 있습니다.

## <a name="check-the-build-and-test-results"></a>빌드 및 테스트 결과 검사

**게시** 폴더에서 테스트 요약 파일 및 Azure Resource Manager 템플릿 파일을 찾을 수 있습니다.

   :::image type="content" source="media/set-up-cicd-pipeline/check-build-test-result.png" alt-text="빌드 및 테스트 결과 검사":::

   :::image type="content" source="media/set-up-cicd-pipeline/check-drop-folder.png" alt-text="아티팩트 검사":::

## <a name="release-with-azure-pipelines"></a>Azure Pipelines을 사용한 릴리스

이 섹션에서는 릴리스 파이프라인을 만드는 방법에 대해 알아봅니다. 

웹 브라우저를 열고 Azure Stream Analytics Visual Studio Code 프로젝트로 이동합니다.

1. 왼쪽 탐색 메뉴의 **파이프라인** 에서 **릴리스** 를 선택합니다. 그런 다음, **새 파이프라인** 을 선택합니다.

2. **빈 작업으로 시작** 을 선택합니다.

3. **아티팩트** 상자에서 **+ 아티팩트 추가** 를 선택합니다. **원본** 에서 만든 빌드 파이프라인을 선택하고 **추가** 를 선택합니다.

   :::image type="content" source="media/set-up-cicd-pipeline/build-artifact.png" alt-text="빌드 파이프라인 아티팩트 입력":::

4. **스테이지 1** 의 이름을 **테스트 환경에 작업 배포** 로 변경합니다.

5. 새 스테이지를 추가하고 이름을 **프로덕션 환경에 작업 배포** 로 지정합니다.

### <a name="add-deploy-tasks"></a>배포 작업 추가

1. 작업 드롭다운에서 **테스트 환경에 작업 배포** 를 선택합니다.

2. **에이전트 작업** 옆에 **+** 를 선택하고 **ARM 템플릿 배포** 를 검색합니다. 다음 매개 변수를 입력합니다.

   |매개 변수|값|
   |-|-|
   |표시 이름| *myASAProject 배포*|
   |Azure 구독| 구독을 선택합니다.|
   |작업| *리소스 그룹을 만들기 또는 업데이트*|
   |Resource group| Stream Analytics 작업을 포함할 테스트 리소스 그룹의 이름을 선택합니다.|
   |Location|테스트 리소스 그룹의 위치를 선택합니다.|
   |템플릿 위치| 연결된 아티팩트|
   |템플릿| $(System.DefaultWorkingDirectory)/_azure-streamanalytics-cicd-demo-CI-Deploy/drop/myASAProject.JobTemplate.json |
   |템플릿 매개 변수|$(System.DefaultWorkingDirectory)/_azure-streamanalytics-cicd-demo-CI-Deploy/drop/myASAProject.JobTemplate.parameters.json |
   |템플릿 매개 변수 재정의|-<arm_template_parameter> "사용자 값". **변수** 를 사용하여 매개 변수를 정의할 수 있습니다.|
   |배포 모드|증분|

3. 작업 드롭다운에서 **프로덕션 환경에 작업 배포** 를 선택합니다.

4. **에이전트 작업** 옆에 **+** 를 선택하고 *ARM 템플릿 배포* 를 검색합니다. 다음 매개 변수를 입력합니다.

   |매개 변수|값|
   |-|-|
   |표시 이름| *myASAProject 배포*|
   |Azure 구독| 구독을 선택합니다.|
   |작업| *리소스 그룹을 만들기 또는 업데이트*|
   |Resource group| Stream Analytics 작업을 포함할 프로덕션 리소스 그룹의 이름을 선택합니다.|
   |Location|프로덕션 리소스 그룹의 위치를 선택합니다.|
   |템플릿 위치| *연결된 아티팩트*|
   |템플릿| $(System.DefaultWorkingDirectory)/_azure-streamanalytics-cicd-demo-CI-Deploy/drop/myASAProject.JobTemplate.json |
   |템플릿 매개 변수|$(System.DefaultWorkingDirectory)/_azure-streamanalytics-cicd-demo-CI-Deploy/drop/myASAProject.JobTemplate.parameters.json |
   |템플릿 매개 변수 재정의|-<arm_template_parameter> "사용자 값"|
   |배포 모드|증분|

### <a name="create-a-release"></a>릴리스 만들기

릴리스를 만들려면 오른쪽 위 모서리에서 **릴리스 만들기** 를 선택합니다.

:::image type="content" source="media/set-up-cicd-pipeline/create-release.png" alt-text="Azure Pipelines을 사용해 릴리스 만들기":::

## <a name="next-steps"></a>다음 단계

* [Azure Stream Analytics에 대한 지속적인 통합 및 지속적인 배포](cicd-overview.md)
* [CI/CD 도구를 사용한 Azure Stream Analytics 작업의 빌드, 테스트 및 배포 자동화](cicd-tools.md)