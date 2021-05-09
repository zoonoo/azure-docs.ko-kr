---
author: IEvangelist
ms.author: dapine
ms.date: 08/22/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 8b3d856d255968b4a6736db908ce3999cbd56193
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "95996465"
---
## <a name="validate-that-a-container-is-running"></a>컨테이너가 실행 중인지 확인 

컨테이너가 실행되고 있는지 확인하는 방법은 여러 가지가 있습니다. 확인 대상인 컨테이너의 *외부 IP* 주소 및 노출된 포트를 찾고 즐겨 찾는 웹 브라우저를 엽니다. 아래의 다양한 요청 URL을 사용하여 컨테이너가 실행 중인지 확인합니다. 아래에 나열된 요청 URL 예는 `http://localhost:5000`이지만 특정 컨테이너는 다를 수 있습니다. 컨테이너의 *외부 IP* 주소 및 노출된 포트를 사용해야 한다는 점에 유의하세요.

| 요청 URL | 용도 |
|--|--|
| `http://localhost:5000/` | 컨테이너는 홈페이지를 제공합니다. |
| `http://localhost:5000/ready` | GET을 사용하여 요청하면 컨테이너가 모델에 대한 쿼리를 수락할 준비가 되었는지 확인할 수 있습니다.  이 요청은 Kubernetes [활동성 및 준비 상태 프로브](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/)에 사용될 수 있습니다. |
| `http://localhost:5000/status` | 또한 GET을 사용하여 요청하면 컨테이너를 시작하는 데 사용된 API 키가 엔드포인트 쿼리를 발생시키지 않고 유효한지 확인합니다. 이 요청은 Kubernetes [활동성 및 준비 상태 프로브](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/)에 사용될 수 있습니다. |
| `http://localhost:5000/swagger` | 컨테이너는 엔드포인트에 대한 전체 설명서 세트와 **사용해 보기** 기능을 제공합니다. 이 기능을 사용하면 웹 기반 HTML 양식으로 설정을 입력할 수 있고 코드 작성 없이 쿼리를 만들 수 있습니다. 쿼리가 반환되면 필요한 HTTP 헤더 및 본문 형식을 보여주기 위해 예제 CURL 명령이 제공됩니다. |

![컨테이너의 홈페이지](./media/cognitive-services-containers-api-documentation/container-webpage.png)
