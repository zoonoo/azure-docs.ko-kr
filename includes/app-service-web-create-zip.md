---
author: cephalin
ms.service: app-service-web
ms.topic: include
ms.date: 11/03/2016
ms.author: cephalin
ms.openlocfilehash: d804cb310a8638713cabf76c2f4192a0e4d0f43d
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/08/2019
ms.locfileid: "65411868"
---
## <a name="create-a-project-zip-file"></a>프로젝트 ZIP 파일 만들기

여전히 샘플 프로젝트의 루트 디렉터리에 있는지 확인합니다. 프로젝트의 모든 것에 대한 ZIP 아카이브를 만듭니다. 다음 명령은 터미널의 기본 도구를 사용합니다.

```
# Bash
zip -r myAppFiles.zip .

# PowerShell
Compress-Archive -Path * -DestinationPath myAppFiles.zip
``` 

나중에 이 ZIP 파일을 Azure에 업로드하고 이를 App Service에 배포합니다.