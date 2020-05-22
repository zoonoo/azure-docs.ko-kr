---
author: burkeholland
ms.service: static-web-apps
ms.topic: include
ms.date: 05/08/2020
ms.author: buhollan
ms.openlocfilehash: 879d0c2e8c4cd66ce04c0298d00f7d6a1987acf5
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83594402"
---
#### <a name="local-proxy"></a>로컬 프록시

`/api`에 대한 모든 요청을 `http://127.0.0.1:7071/api`에서 실행 중인 API 엔드포인트로 라우팅하는 Live Server Visual Studio Code 확장에 대한 프록시를 구성할 수 있습니다.

1. _.vscode/settings.json_ 파일을 엽니다.

1. 다음 설정을 추가하여 Live Server에 대한 프록시를 지정합니다.

   ```json
   "liveServer.settings.proxy": {
      "enable": true,
      "baseUri": "/api",
      "proxyUri": "http://127.0.0.1:7071/api"
   }
   ```

   이 구성은 사용자 설정 파일이 아닌 프로젝트 설정 파일에 저장하는 것이 가장 좋습니다.

   프로젝트 설정을 사용하면 Visual Studio Code에서 열린 다른 모든 프로젝트에 프록시가 적용되지 않습니다.
