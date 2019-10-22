---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/26/2019
ms.author: glenga
ms.openlocfilehash: e10de8093bf152b75cc6f262a142ff07c3d5b0d3
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72329555"
---
이미 Azure에서 필요한 Storage 계정과 함께 함수 앱을 만들었습니다. 이 계정의 연결 문자열은 Azure의 앱 설정에 안전하게 저장됩니다. 이 문서에서는 같은 계정의 Storage 큐에 메시지를 작성합니다. 함수를 로컬로 실행할 때 Storage 계정에 연결하려면 앱 설정을 local.settings.json 파일에 다운로드해야 합니다. 

프로젝트의 루트에서 다음 Azure Functions Core Tools 명령을 실행하여 설정을 local.settings.json에 다운로드하고, `<APP_NAME>`을 이전 문서의 함수 앱 이름으로 바꿉니다.

```bash
func azure functionapp fetch-app-settings <APP_NAME>
```

Azure 계정에 로그인해야 할 수도 있습니다.

> [!IMPORTANT]  
> 이 명령은 기존 설정을 Azure의 함수 앱 값으로 덮어씁니다.  
>
> local.settings.json 파일은 비밀을 포함하고 있으므로 절대 게시되지 않으며, 소스 제어에서 제외되어야 합니다.  
> 

Storage 계정 연결 문자열인 `AzureWebJobsStorage` 값이 필요합니다. 이 연결을 사용하여 출력 바인딩이 예상대로 작동하는지 확인합니다.
