---
title: CI/CD npm 패키지를 사용 하 여 Azure Stream Analytics 작업 배포
description: 이 문서에서는 Azure Stream Analytics CI/CD npm 패키지를 사용 하 여 지속적인 통합 및 배포 프로세스를 설정 하는 방법을 설명 합니다.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/28/2020
ms.openlocfilehash: deb6c2439cc84f196b7f42fd9f49d3ebfd057cbb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "76962218"
---
# <a name="deploy-an-azure-stream-analytics-job-using-cicd-npm-package"></a>CI/CD npm 패키지를 사용 하 여 Azure Stream Analytics 작업 배포 

Azure Stream Analytics CI/CD npm 패키지를 사용 하 여 Stream Analytics 작업에 대 한 지속적인 통합 및 배포 프로세스를 설정할 수 있습니다. 이 문서에서는 CI/CD 시스템에서 npm 패키지를 사용 하는 방법 및 Azure Pipelines 배포에 대 한 구체적인 지침을 설명 합니다.

Powershell을 사용 하 여 배포 하는 방법에 대 한 자세한 내용은 [리소스 관리자 템플릿 파일을 사용 하 여 배포 및 Azure PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy)를 참조 하세요. [리소스 관리자 템플릿에서 개체를 매개 변수로 사용](https://docs.microsoft.com/azure/architecture/building-blocks/extending-templates/objects-as-parameters)하는 방법에 대해 자세히 알아볼 수도 있습니다.

## <a name="build-the-vs-code-project"></a>VS Code 프로젝트 빌드

**Cicd** npm 패키지를 사용 하 여 Azure Stream Analytics 작업에 대 한 연속 통합 및 배포를 사용 하도록 설정할 수 있습니다. Npm 패키지는 [Stream Analytics Visual Studio Code 프로젝트](quick-create-vs-code.md)의 Azure Resource Manager 템플릿을 생성 하는 도구를 제공 합니다. Visual Studio Code를 설치 하지 않고 Windows, macOS 및 Linux에서 사용할 수 있습니다.

패키지를 직접 [다운로드](https://www.npmjs.com/package/azure-streamanalytics-cicd) 하거나 `npm install -g azure-streamanalytics-cicd` 명령을 통해 [전역적](https://docs.npmjs.com/downloading-and-installing-packages-globally) 으로 설치할 수 있습니다. 이 방법은 **Azure Pipelines**에서 빌드 파이프라인의 PowerShell 또는 Azure CLI 스크립트 작업에 사용할 수 있는 권장 되는 방법입니다.

패키지를 설치한 후에는 다음 명령을 사용 하 여 Azure Resource Manager 템플릿을 출력 합니다. **ScriptPath** 인수는 프로젝트의 **script.asaql** 파일에 대 한 절대 경로입니다. Asaproj 및 JobConfig 파일이 스크립트 파일과 동일한 폴더에 있는지 확인 합니다. **OutputPath** 를 지정 하지 않으면 템플릿이 프로젝트의 **bin** 폴더 아래에 있는 **배포** 폴더에 배치 됩니다.

```powershell
azure-streamanalytics-cicd build -scriptPath <scriptFullPath> -outputPath <outputPath>
```
예 (macOS)
```powershell
azure-streamanalytics-cicd build -scriptPath "/Users/roger/projects/samplejob/script.asaql" 
```

Stream Analytics Visual Studio Code 프로젝트가 성공적으로 빌드되면 **bin/[Debug/Retail]/Sdeploy** 폴더 아래에 다음 두 개의 Azure Resource Manager 템플릿 파일이 생성 됩니다. 

*  Resource Manager 템플릿 파일

       [ProjectName].JobTemplate.json 

*  Resource Manager 매개 변수 파일

       [ProjectName].JobTemplate.parameters.json   

Visual Studio Code 프로젝트의 설정에서 매개 변수. json 파일의 기본 매개 변수를 사용할 수 있습니다. 다른 환경에 배포하려면 해당 매개 변수를 적절하게 바꾸면 됩니다.

> [!NOTE]
> 모든 자격 증명에 대해 기본값은 null로 설정됩니다. 클라우드에 배포하기 전에 값을 **설정해야 합니다**.

```json
"Input_EntryStream_sharedAccessPolicyKey": {
      "value": null
    },
```

## <a name="deploy-with-azure-pipelines"></a>Azure Pipelines를 사용하여 배포

이 섹션에서는 npm를 사용 하 여 파이프라인 [을 Azure Pipelines 만들고](https://docs.microsoft.com/azure/devops/pipelines/get-started-designer?view=vsts&tabs=new-nav) [릴리스](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts) 하는 방법에 대해 자세히 설명 합니다.

웹 브라우저를 열고 Azure Stream Analytics Visual Studio Code 프로젝트로 이동 합니다.

1. 왼쪽 탐색 메뉴의 **파이프라인** 에서 **빌드**를 선택 합니다. 그런 다음 **새 파이프라인** 을 선택 합니다.

   ![새 Azure 파이프라인 만들기](./media/setup-cicd-vs-code/new-pipeline.png)

2. **클래식 편집기를 사용** 하 여 yaml 없이 파이프라인 만들기를 선택 합니다.

3. 원본 유형, 팀 프로젝트 및 리포지토리를 선택 합니다. 그런 다음 **계속**을 선택합니다.

   ![Azure Stream Analytics 프로젝트 선택](./media/setup-cicd-vs-code/select-repo.png)

4. **템플릿 선택** 페이지에서 **빈 작업**을 선택 합니다.

### <a name="add-npm-task"></a>Npm 작업 추가

1. **작업** 페이지에서 **에이전트 작업 1**옆에 있는 더하기 기호를 선택 합니다. 작업 검색에 "npm"를 입력 하 고 **npm**를 선택 합니다.

   ![Npm 작업 선택](./media/setup-cicd-vs-code/search-npm.png)

2. 작업에 **표시 이름을**지정 합니다. **명령** 옵션을 *사용자 지정* 으로 변경 하 고 **명령 및 인수**에 다음 명령을 입력 합니다. 나머지 기본 옵션은 그대로 둡니다.

   ```cmd
   install -g azure-streamanalytics-cicd
   ```

   ![Npm 작업에 대 한 구성 입력](./media/setup-cicd-vs-code/npm-config.png)

### <a name="add-command-line-task"></a>명령줄 작업 추가

1. **작업** 페이지에서 **에이전트 작업 1**옆에 있는 더하기 기호를 선택 합니다. **명령줄**을 검색 합니다.

2. 작업에 **표시 이름을** 지정 하 고 다음 스크립트를 입력 합니다. 리포지토리 이름 및 프로젝트 이름으로 스크립트를 수정 합니다.

   ```cmd
   azure-streamanalytics-cicd build -scriptPath $(Build.SourcesDirectory)/myASAProject/myASAProj.asaql
   ```

   ![명령줄 작업에 대 한 구성 입력](./media/setup-cicd-vs-code/commandline-config.png)

### <a name="add-copy-files-task"></a>파일 복사 작업 추가

1. **작업** 페이지에서 **에이전트 작업 1**옆에 있는 더하기 기호를 선택 합니다. **파일 복사**를 검색 합니다. 다음 구성을 입력 합니다.

   |매개 변수|입력|
   |-|-|
   |표시 이름|파일을 다음으로 복사: $ (build.artifactstagingdirectory)|
   |원본 폴더|`$(system.defaultworkingdirectory)`| 
   |콘텐츠| `**\Deploy\**` |
   |대상 폴더| `$(build.artifactstagingdirectory)`|

   ![복사 작업에 대 한 구성 입력](./media/setup-cicd-vs-code/copy-config.png)

### <a name="add-publish-build-artifacts-task"></a>빌드 아티팩트 게시 작업 추가

1. **작업** 페이지에서 **에이전트 작업 1**옆에 있는 더하기 기호를 선택 합니다. **빌드 아티팩트 게시** 를 검색 하 고 검은색 화살표 아이콘을 사용 하 여 옵션을 선택 합니다. 

2. 기본 구성을 변경 하지 마십시오.

### <a name="save-and-run"></a>저장 및 실행

Npm, 명령줄, 파일 복사 및 빌드 아티팩트 게시 작업을 모두 추가 했으면 **큐 & 저장**을 선택 합니다. 메시지가 표시 되 면 저장 메모를 입력 하 고 **저장 및 실행**을 선택 합니다.

## <a name="release-with-azure-pipelines"></a>Azure Pipelines으로 릴리스

웹 브라우저를 열고 Azure Stream Analytics Visual Studio Code 프로젝트로 이동 합니다.

1. 왼쪽 탐색 메뉴의 **파이프라인** 에서 **릴리스**를 선택 합니다. 그런 다음 **새 파이프라인**을 선택 합니다.

2. **빈 작업으로 시작을**선택 합니다.

3. **아티팩트** 상자에서 **+ 아티팩트 추가**를 선택 합니다. **원본**에서 방금 만든 빌드 파이프라인을 선택 하 고 **추가**를 선택 합니다.

   ![빌드 파이프라인 아티팩트 입력](./media/setup-cicd-vs-code/build-artifact.png)

4. **1 단계의** 이름을 변경 하 여 **작업을 테스트 환경에 배포**합니다.

5. 새 단계를 추가 하 고 이름을 **배포 하는 작업을 프로덕션 환경에 배포**합니다.

### <a name="add-tasks"></a>작업 추가

1. 작업 드롭다운에서 **테스트 환경에 작업 배포**를 선택 합니다. 

2. **+** **에이전트 작업** 옆에 있는를 선택 하 고 *Azure 리소스 그룹 배포*를 검색 합니다. 다음 매개 변수를 입력합니다.

   |설정|값|
   |-|-|
   |표시 이름| *MyASAJob 배포*|
   |Azure 구독| 구독을 선택합니다.|
   |작업| *리소스 그룹을 만들기 또는 업데이트*|
   |Resource group| Stream Analytics 작업을 포함할 테스트 리소스 그룹의 이름을 선택 합니다.|
   |위치|테스트 리소스 그룹의 위치를 선택 합니다.|
   |템플릿 위치| *연결 된 아티팩트*|
   |템플릿| $ (Build.artifactstagingdirectory) \drop\myASAJob.JobTemplate.json |
   |템플릿 매개 변수|($ (Build.artifactstagingdirectory) \drop\myASAJob.JobTemplate.parameters.json|
   |템플릿 매개 변수 재정의|-Input_IoTHub1_iotHubNamespace $ (test_eventhubname)|
   |배포 모드|증분|

3. 작업 드롭다운에서 **프로덕션 환경에 작업 배포**를 선택 합니다.

4. **+** **에이전트 작업** 옆에 있는를 선택 하 고 *Azure 리소스 그룹 배포*를 검색 합니다. 다음 매개 변수를 입력합니다.

   |설정|값|
   |-|-|
   |표시 이름| *MyASAJob 배포*|
   |Azure 구독| 구독을 선택합니다.|
   |작업| *리소스 그룹을 만들기 또는 업데이트*|
   |Resource group| Stream Analytics 작업을 포함할 프로덕션 리소스 그룹의 이름을 선택 합니다.|
   |위치|프로덕션 리소스 그룹의 위치를 선택 합니다.|
   |템플릿 위치| *연결 된 아티팩트*|
   |템플릿| $ (Build.artifactstagingdirectory) \drop\myASAJob.JobTemplate.json |
   |템플릿 매개 변수|($ (Build.artifactstagingdirectory) \drop\myASAJob.JobTemplate.parameters.json|
   |템플릿 매개 변수 재정의|-Input_IoTHub1_iotHubNamespace $ (eventhubname)|
   |배포 모드|증분|

### <a name="create-release"></a>릴리스 만들기

릴리스를 만들려면 오른쪽 위 모서리에서 **릴리스 만들기** 를 선택 합니다.

![Azure Pipelines를 사용 하 여 릴리스 만들기](./media/setup-cicd-vs-code/create-release.png)

## <a name="additional-resources"></a>추가 리소스

Azure Data Lake Store Gen1용 관리 ID를 출력 싱크로 사용하려면 Azure에 배포하기 전에 PowerShell을 사용하여 서비스 주체에 액세스 권한을 제공해야 합니다. [Resource Manager 템플릿에서 관리 ID를 사용하여 ADLS Gen1을 배포](stream-analytics-managed-identities-adls.md#resource-manager-template-deployment)하는 방법을 자세히 알아봅니다.


## <a name="next-steps"></a>다음 단계

* [빠른 시작: Visual Studio Code (미리 보기)에서 Azure Stream Analytics 클라우드 작업 만들기](quick-create-vs-code.md)
* [Visual Studio Code (미리 보기)를 사용 하 여 로컬로 쿼리 Stream Analytics 테스트](visual-studio-code-local-run.md)
* [Visual Studio Code를 사용 하 여 Azure Stream Analytics 탐색 (미리 보기)](visual-studio-code-explore-jobs.md)
