---
title: 포함 파일
description: 포함 파일
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 01/14/2020
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 6b5aa4f409b8c2f5a9125ab01e8587f4ac9c4ee5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75945184"
---
## <a name="create-a-project-zip-file"></a>프로젝트 ZIP 파일 만들기

>[!NOTE]
> 파일을 ZIP 파일로 다운로드한 경우 먼저 파일을 추출합니다. 예를 들어 GitHub에서 ZIP 파일을 다운로드한 경우 해당 파일을 그대로 배포할 수 없습니다. GitHub에서는 App Service에서 작동하지 않는 추가적인 중첩된 디렉터리를 추가합니다. 
>

로컬 터미널 창에서 앱 프로젝트의 루트 디렉터리로 이동합니다. 

이 디렉터리는 _index.html_, _index.php_ 및 _app.js_와 같은 웹앱에 대한 입력 파일을 포함해야 합니다. 또한 _project.json_, _composer.json_, _package.json_, _bower.json_ 및 _requirements.txt_와 같은 패키지 관리 파일을 포함할 수 있습니다.

App Service에서 배포 자동화를 실행하지 않으려면 모든 빌드 작업(예: `npm`, `pip`, 및) `bower` `gulp` `composer`및 앱을 실행하는 데 필요한 모든 파일이 있는지 확인합니다. 이 단계는 [패키지를 직접 실행하려면](../articles/app-service/deploy-run-package.md)필수입니다.

프로젝트의 모든 것에 대한 ZIP 아카이브를 만듭니다. 다음 명령은 터미널의 기본 도구를 사용합니다.

```
# Bash
zip -r <file-name>.zip .

# PowerShell
Compress-Archive -Path * -DestinationPath <file-name>.zip
``` 

