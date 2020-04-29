---
title: 파일 포함
description: 포함 파일
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 06/12/2019
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: add0d392f39ab476c6d75f704d5b2e2e0faaa77c
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/26/2020
ms.locfileid: "67836746"
---
## <a name="prepare-your-repository"></a>리포지토리 준비

Azure App Service Kudu 빌드 서버에서 자동 빌드를 가져오려면 리포지토리 루트의 프로젝트에 올바른 파일이 있는지 확인 합니다.

| 런타임 | 루트 디렉터리 파일 |
|-|-|
| ASP.NET(Windows만 해당) | _*.sln_, _*.csproj_ 또는 _default.aspx_ |
| ASP.NET Core | _* .sln_ 또는 _* .csproj_ |
| PHP | _index.php_ |
| Ruby(Linux만 해당) | _Gemfile_ |
| Node.js | 시작 스크립트를 사용 하는 _node.js_, _node.js_또는 _패키지인_ |
| Python | . py, _요구 사항 .txt_또는 _runtime .txt_ _ \*_ |
| HTML | _default.htm_, _default.html_, _default.asp_, _index.htm_, _index.html_ 또는 _iisstart.htm_ |
| 웹 작업 | _\<job_name>/run.>\<확장_ 은 _앱\_데이터/작업/연속_ WebJobs의 경우/연속으로, 트리거된 WebJobs의 경우에는 _앱\_데이터/작업/_ 실행 됩니다. 자세한 내용은 [Kudu WebJobs 설명서](https://github.com/projectkudu/kudu/wiki/WebJobs)를 참조 하세요. |
| Functions | [Azure Functions에 대한 연속 배포](../articles/azure-functions/functions-continuous-deployment.md#requirements-for-continuous-deployment)를 참조하세요. |

배포를 사용자 지정하려면 리포지토리 루트에 *.deployment* 파일을 포함합니다. 자세한 내용은 [배포 사용자](https://github.com/projectkudu/kudu/wiki/Customizing-deployments) 지정 및 [사용자 지정 배포 스크립트](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script)를 참조 하세요.

> [!NOTE]
> Visual Studio에서 개발하는 경우 [Visual Studio에서 자동으로 리포지토리를 만들도록](/azure/devops/repos/git/creatingrepo?view=vsts&tabs=visual-studio) 합니다. Git를 사용 하 여 프로젝트를 즉시 배포할 준비가 되었습니다.
>

