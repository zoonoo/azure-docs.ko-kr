---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 03/25/2019
ms.openlocfilehash: 94e95864d8bac2d6dc0ff690a2a8f53bd2db5a40
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2019
ms.locfileid: "58522516"
---
## <a name="validate-container-is-running"></a>유효성 검사 컨테이너가 실행 되 고 

컨테이너의 유효성을 검사 하는 여러 가지 방법으로 실행 되 고 있습니다. 

|요청|목적|
|--|--|
|`http://localhost:5000/`|컨테이너는 홈 페이지를 제공합니다.|
|`http://localhost:5000/status`|GET을 사용 하 여 요청을 컨테이너의 유효성을 검사 하려면 없이 실행 되 고 끝점 쿼리를 유발 합니다. Kubernetes에 대해 사용할 수 있습니다 [선거 및 준비 프로브](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/)합니다.|
|`http://localhost:5000/swagger`|컨테이너는 `Try it now` 기능뿐만 아니라 엔드포인트에 대한 모든 설명서를 제공합니다. 이 기능을 사용하면 웹 기반 HTML 형식으로 설정을 입력할 수 있고 코드 작성 없이 쿼리를 만들 수 있습니다. 쿼리가 반환되면 예로 CURL 명령은 필요한 HTTP 헤더 및 본문 포맷을 데모하는 데 제공됩니다. |

![컨테이너의 홈페이지](./media/cognitive-services-containers-api-documentation/container-webpage.png)
