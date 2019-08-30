---
title: Azure Stream Analytics CI/CD npm 패키지 사용
description: 이 문서에서는 Azure Stream Analytics CI/CD npm 패키지를 사용 하 여 지속적인 통합 및 배포 프로세스를 설정 하는 방법을 설명 합니다.
services: stream-analytics
author: su-jie
ms.author: sujie
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/15/2019
ms.openlocfilehash: df9afaaeeb7e41c111fe6bd053047095a9cb9349
ms.sourcegitcommit: ee61ec9b09c8c87e7dfc72ef47175d934e6019cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/30/2019
ms.locfileid: "70173338"
---
# <a name="use-the-stream-analytics-cicd-npm-package"></a>Stream Analytics CI/CD npm 패키지 사용
이 문서에서는 Azure Stream Analytics CI/CD npm 패키지를 사용 하 여 지속적인 통합 및 배포 프로세스를 설정 하는 방법을 설명 합니다.

## <a name="build-the-vs-code-project"></a>VS Code 프로젝트 빌드

**Cicd** npm 패키지를 사용 하 여 Azure Stream Analytics 작업에 대 한 연속 통합 및 배포를 사용 하도록 설정할 수 있습니다. Npm 패키지는 [Stream Analytics Visual Studio Code 프로젝트](quick-create-vs-code.md)의 Azure Resource Manager 템플릿을 생성 하는 도구를 제공 합니다. Visual Studio Code를 설치 하지 않고 Windows, macOS 및 Linux에서 사용할 수 있습니다.

[패키지를 다운로드](https://www.npmjs.com/package/azure-streamanalytics-cicd)한 후에는 다음 명령을 사용 하 여 Azure Resource Manager 템플릿을 출력 합니다. **ScriptPath** 인수는 프로젝트의 **script.asaql** 파일에 대 한 절대 경로입니다. Asaproj 및 JobConfig 파일이 스크립트 파일과 동일한 폴더에 있는지 확인 합니다. **OutputPath** 를 지정 하지 않으면 템플릿이 프로젝트의 **bin** 폴더 아래에 있는 **배포** 폴더에 배치 됩니다.

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
[Resource Manager 템플릿 파일 및 Azure PowerShell을 사용하여 배포](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy)하는 방법에 대해 자세히 알아보세요. [Resource Manager 템플릿에서 개체를 매개 변수로 사용](https://docs.microsoft.com/azure/architecture/building-blocks/extending-templates/objects-as-parameters)하는 방법에 대해 자세히 알아보세요.

Azure Data Lake Store Gen1용 관리 ID를 출력 싱크로 사용하려면 Azure에 배포하기 전에 PowerShell을 사용하여 서비스 주체에 액세스 권한을 제공해야 합니다. [Resource Manager 템플릿에서 관리 ID를 사용하여 ADLS Gen1을 배포](stream-analytics-managed-identities-adls.md#resource-manager-template-deployment)하는 방법을 자세히 알아봅니다.
## <a name="next-steps"></a>다음 단계

* [빠른 시작: Visual Studio Code에서 Azure Stream Analytics 클라우드 작업 만들기 (미리 보기)](quick-create-vs-code.md)
* [Visual Studio Code (미리 보기)를 사용 하 여 로컬로 쿼리 Stream Analytics 테스트](vscode-local-run.md)
* [Visual Studio Code를 사용 하 여 Azure Stream Analytics 탐색 (미리 보기)](vscode-explore-jobs.md)
