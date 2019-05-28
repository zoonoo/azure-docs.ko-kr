---
title: 지속적으로 통합 하 고 Azure Stream Analytics CI/CD npm 패키지를 사용 하 여 개발
description: 이 문서에서는 Azure Stream Analytics CI/CD npm 패키지를 사용 하 여 연속 통합 및 배포 프로세스를 설정 하는 방법을 설명 합니다.
services: stream-analytics
author: su-jie
ms.author: sujie
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/15/2019
ms.openlocfilehash: fa5a57afa379c6bbe027be80f400fc176800d289
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66158499"
---
# <a name="continuously-integrate-and-develop-with-stream-analytics-cicd-npm-package"></a>지속적으로 통합 하 고 Stream Analytics CI/CD npm 패키지를 사용 하 여 개발
이 문서에서는 Azure Stream Analytics CI/CD npm 패키지를 사용 하 여 연속 통합 및 배포 프로세스를 설정 하는 방법을 설명 합니다.

## <a name="build-the-vs-code-project"></a>VS Code 프로젝트 빌드

지속적인 통합 및 배포를 사용 하 여 Azure Stream Analytics 작업에 대해 설정할 수 있습니다.는 **asa-streamanalytics-cicd** npm 패키지 있습니다. Azure Resource Manager 템플릿을 생성 하는 도구를 제공 하는 npm 패키지 [Stream Analytics Visual Studio Code 프로젝트](quick-create-vs-code.md)합니다. Visual Studio Code를 설치 하지 않고 Windows, macOS 및 Linux에서 사용할 수 있습니다.

했으면 [패키지를 다운로드 한](https://www.npmjs.com/package/azure-streamanalytics-cicd), 다음 명령을 사용 하 여 Azure Resource Manager 템플릿 출력 합니다. **scriptPath** 인수는 절대 경로 **asaql** 프로젝트 파일에에서. Asaproj.json 및 JobConfig.json 파일 스크립트 파일을 사용 하 여 동일한 폴더에 있는지 확인 합니다. 경우는 **outputPath** 지정 하지 않으면 서식 파일에 배치 됩니다 합니다 **배포** 프로젝트의 폴더 **bin** 폴더입니다.

```powershell
azure-streamanalytics-cicd build -scriptPath <scriptFullPath> -outputPath <outputPath>
```
(MacOS)에 대 한 예제
```powershell
azure-streamanalytics-cicd build -scriptPath "/Users/roger/projects/samplejob/script.asaql" 
```

Stream Analytics Visual Studio 코드 프로젝트를 성공적으로 빌드되면 다음 두 Azure Resource Manager 템플릿 파일에서 생성 된 **bin / [Debug/Retail] /deploy** 폴더: 

*  Resource Manager 템플릿 파일

       [ProjectName].JobTemplate.json 

*  Resource Manager 매개 변수 파일

       [ProjectName].JobTemplate.parameters.json   

Parameters.json 파일의 기본 매개 변수는 Visual Studio 코드 프로젝트의 설정에서. 다른 환경에 배포하려면 해당 매개 변수를 적절하게 바꾸면 됩니다.

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

* [빠른 시작: Visual Studio Code (미리 보기)에서 클라우드 Azure Stream Analytics 작업 만들기](quick-create-vs-code.md)
* [Visual Studio Code (미리 보기)를 사용 하 여 로컬 Stream Analytics 쿼리 테스트](vscode-local-run.md)
* [Visual Studio Code (미리 보기)를 사용 하 여 Azure Stream Analytics 살펴보기](vscode-explore-jobs.md)
