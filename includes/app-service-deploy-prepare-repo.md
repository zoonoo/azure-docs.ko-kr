---
title: 포함 파일
description: 포함 파일
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 06/12/2019
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: add0d392f39ab476c6d75f704d5b2e2e0faaa77c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "67836746"
---
## <a name="prepare-your-repository"></a>리포지토리 준비

Azure App Service Kudu 빌드 서버에서 자동 빌드를 얻으려면 리포지토리 루트에 프로젝트에 올바른 파일이 있는지 확인합니다.

| 런타임 | 루트 디렉터리 파일 |
|-|-|
| ASP.NET(Windows만 해당) | _*.sln_, _*.csproj_ 또는 _default.aspx_ |
| ASP.NET Core | _*.sln_ 또는 _*.csproj_ |
| PHP | _index.php_ |
| Ruby(Linux만 해당) | _Gemfile_ |
| Node.js | _server.js_, _app.js_ 또는 _package.json_(시작 스크립트 포함) |
| Python | .py, _요구 사항.txt,_ 또는 _런타임.txt_ _ \*_ |
| HTML | _default.htm_, _default.html_, _default.asp_, _index.htm_, _index.html_ 또는 _iisstart.htm_ |
| 웹 작업 | _\<job_name>/ 실행. 확장 \<>_ _앱\_데이터/작업/연속_ 웹 작업에 대한 연속 또는 트리거된 WebJobs에 대해 _트리거된\_앱 데이터/작업/트리거된 앱 데이터/작업 에서 트리거됩니다._ 자세한 내용은 [Kudu WebJobs 문서를](https://github.com/projectkudu/kudu/wiki/WebJobs)참조하십시오. |
| 함수 | [Azure Functions에 대한 연속 배포](../articles/azure-functions/functions-continuous-deployment.md#requirements-for-continuous-deployment)를 참조하세요. |

배포를 사용자 지정하려면 리포지토리 루트에 *.deployment* 파일을 포함합니다. 자세한 내용은 배포 및 사용자 [지정 배포 스크립트](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script) [를 참조하세요.](https://github.com/projectkudu/kudu/wiki/Customizing-deployments)

> [!NOTE]
> Visual Studio에서 개발하는 경우 [Visual Studio에서 자동으로 리포지토리를 만들도록](/azure/devops/repos/git/creatingrepo?view=vsts&tabs=visual-studio) 합니다. Git을 사용하여 프로젝트를 즉시 배포할 준비가 되었습니다.
>

