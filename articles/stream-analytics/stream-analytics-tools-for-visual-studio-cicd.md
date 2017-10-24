---
title: "Stream Analytics Visual Studio 도구를 사용하여 연속 통합 및 배포 프로세스를 설정하는 방법 | Microsoft Docs"
description: "Stream Analytics Visual Studio 도구를 사용하여 연속 통합 및 배포 프로세스를 설정하기 위한 자습서"
keywords: visual studio, NuGet, DevOps, CI/CD
documentationcenter: 
services: stream-analytics
author: su-jie
manager: 
editor: 
ms.assetid: a473ea0a-3eaa-4e5b-aaa1-fec7e9069f20
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 9/27/2017
ms.author: sujie
ms.openlocfilehash: 947266dc94babab21556da26d8cc8c917cf81c12
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="use-stream-analytics-visual-studio-tools-to-set-up-the-continuous-integration-and-deployment-process"></a>Stream Analytics Visual Studio 도구를 사용하여 연속 통합 및 배포 프로세스 설정 
이 자습서에서는 Stream Analytics Visual Studio 도구를 사용하여 연속 통합 및 배포 프로세스를 설정하는 방법을 학습합니다.

[Visual Studio용 Stream Analytics 도구](https://docs.microsoft.com/en-us/azure/stream-analytics/stream-analytics-tools-for-visual-studio) 최신 버전(2.3.0000.0 이상)은 **MSBuild**에 대한 지원을 추가합니다. 

새로 릴리스된 NuGet 패키지 [Microsoft.Azure.Stream Analytics.CICD](https://www.nuget.org/packages/Microsoft.Azure.StreamAnalytics.CICD/)도 있습니다. Stream Analytics Visual Studio 프로젝트의 지속적 통합과 배포를 지원하는 MSBuild, 로컬 실행 및 배포 도구를 제공합니다. 
> [!NOTE] 
NuGet 패키지는 Visual Studio용 Stream Analytics Tools의 버전 2.3.0000.0 이상에서만 사용할 수 있습니다. 이전 Visual Studio 도구 버전에서 만든 프롲게트가 있는 경우 2.3.0000.0 이상 버전에서 열어 저장하기만 하면 됩니다. 그러면 새 기능을 사용할 수 있습니다. 

[Visual Studio용 Azure Stream Analytics Tools 사용 방법 알아보기](https://docs.microsoft.com/en-us/azure/stream-analytics/stream-analytics-tools-for-visual-studio)

## <a name="msbuild"></a>MSBuild
표준 Visual Studio MSBuild 환경처럼 프로젝트를 빌드하려면 프로젝트를 마우스 오른쪽 단추로 클릭한 다음 **빌드**를 선택하거나 명령줄에서 NuGet 패키지의 **MSBuild**를 사용합니다.
```
./build/msbuild /t:build [Your Project Full Path] /p:CompilerTaskAssemblyFile=Microsoft.WindowsAzure.StreamAnalytics.Common.CompileService.dll  /p:ASATargetsFilePath="[NuGet Package Local Path]\build\StreamAnalytics.targets"

```

Stream Analytics Visual Studio 프로젝트가 성공적으로 빌드되면 다음 두 Azure Resource Manager 템플릿 파일을 **bin/[Debug/Retail]/Deploy** 폴더 아래 생성합니다. 

Azure Resource Manager 템플릿 파일.
*       [ProjectName].JobTemplate.json 

Azure Resource Manager 매개 변수 파일.
*       [ProjectName].JobTemplate.parameters.json   

Parameters.json 파일의 기본 매개 변수는 Visual Studio 프로젝트의 설정에서 가져옵니다. 다른 환경에 배포하려는 경우 그에 따라 매개 변수를 바꾸기만 하면 됩니다. 
> [!NOTE] 
모든 자격 증명에 대해 기본값은 모두 null로 설정됩니다. 클라우드 배포에 앞서 **REQUIRED**로 설정됩니다.
```json
"Input_EntryStream_sharedAccessPolicyKey": {
      "value": null
    },
```
[Azure Resource Manager 템플릿 파일 및 Azure PowerShell을 사용한 배포 방법](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-template-deploy)과 [Azure Resource Manager 템플릿에서 개체를 매개 변수로 사용하는 방법](https://docs.microsoft.com/en-us/azure/architecture/building-blocks/extending-templates/objects-as-parameters)에 대해 자세히 알아봅니다.


## <a name="command-line-tool"></a>명령줄 도구 

### <a name="build-the-project"></a>프로젝트 빌드
NuGet 패키지에는 **SA.exe**라고 하는 명령줄 도구가 있습니다. 이 도구는 프로젝트 빌드, 임의 컴퓨터에서의 로컬 테스트를 지원하므로 연속 통합 및 연속 전달 프로세스에서 사용할 수 있습니다. 

배포 파일은 기본적으로 현재 디렉터리에 배치됩니다. -OutputPath 매개 변수에서 출력 경로를 지정할 수 있습니다.

```
./tools/SA.exe build -Project [Your Project Full Path] [-OutputPath <outputPath>] 
```

### <a name="test-the-script-locally"></a>스크립트를 로컬로 테스트

프로젝트가 Visual Studio에서 로컬 입력 파일을 지정한 경우 *localrun* 명령을 사용하여 자동 스크립트 테스트를 실행할 수 있습니다. 출력 결과는 현재 디렉터리 아래 배치됩니다.
 
```
localrun -Project [ProjectFullPath]
```

### <a name="generate-job-definition-file-to-use-with-stream-analytics-power-shell"></a>Stream Analytics Power Shell에 사용할 작업 정의 파일을 생성합니다.

*arm* 명령은 빌드를 통해 생성된 작업 템플릿 매개 변수 파일과 작업 템플릿을 입력으로 취합니다. 그런 다음 Stream Analytics PowerShell API에 사용할 수 있는 작업 정의 JSON 파일에 결합합니다.

```
arm -JobTemplate <templateFilePath> -JobParameterFile <jobParameterFilePath> [-OutputFile <asaArmFilePath>]
```

```
Example
./tools/SA.exe arm -JobTemplate "ProjectA.JobTemplate.json" -JobParameterFile "ProjectA.JobTemplate.parameters.json" -OutputFile "JobDefinition.json" 
```


