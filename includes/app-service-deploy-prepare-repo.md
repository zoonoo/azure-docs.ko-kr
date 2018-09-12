---
title: 포함 파일
description: 포함 파일
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 06/05/2018
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: c212bda3b59037f99139e02ee6adc63b0084cbe5
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/10/2018
ms.locfileid: "44305617"
---
## <a name="prepare-your-repository"></a>리포지토리 준비

App Service Kudu 빌드 서버의 자동 빌드 기능을 사용하려면 리포지토리 루트에 프로젝트의 올바른 파일에 있는지 확인합니다.

| 런타임 | 루트 디렉터리 파일 |
|-|-|
| ASP.NET(Windows만 해당) | _*.sln_, _*.csproj_ 또는 _default.aspx_ |
| ASP.NET Core | _*.sln_ 또는 _*.csproj_ |
| PHP | _index.php_ |
| Ruby(Linux만 해당) | _Gemfile_ |
| Node.js | _server.js_, _app.js_ 또는 _package.json_(시작 스크립트 포함) |
| Python(Windows만 해당) | _\*.py_, _requirements.txt_ 또는 _runtime.txt_ |
| HTML | _default.htm_, _default.html_, _default.asp_, _index.htm_, _index.html_ 또는 _iisstart.htm_ |
| 웹 작업 | _App\_Data/jobs/continuous_(연속 WebJobs용) 또는 _App\_Data/jobs/triggered_(트리거된 WebJobs용)의 _\<job_name>/run.\<extension>_ 자세한 내용은 [Kudu WebJobs 설명서](https://github.com/projectkudu/kudu/wiki/WebJobs)를 참조하세요. |
| Functions | [Azure Functions에 대한 연속 배포](../articles/azure-functions/functions-continuous-deployment.md#continuous-deployment-requirements)를 참조하세요. |

배포를 사용자 지정하려면 리포지토리 루트에 _.deployment_ 파일을 포함합니다. 자세한 내용은 [배포 사용자 지정](https://github.com/projectkudu/kudu/wiki/Customizing-deployments) 및 [사용자 지정 배포 스크립트](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script)를 참조하세요.

> [!NOTE]
> Visual Studio에서 개발하는 경우 [Visual Studio에서 자동으로 리포지토리를 만들도록](/azure/devops/repos/git/creatingrepo?view=vsts&tabs=visual-studio) 합니다. 그러면 프로젝트는 Git을 사용하여 바로 배포할 수 있는 상태가 됩니다.
>
>

