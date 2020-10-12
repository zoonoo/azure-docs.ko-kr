---
author: IEvangelist
ms.author: dapine
ms.date: 08/22/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 8b3d856d255968b4a6736db908ce3999cbd56193
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "86108937"
---
## <a name="validate-that-a-container-is-running"></a>컨테이너가 실행 중인지 확인 

컨테이너가 실행되고 있는지 확인하는 방법은 여러 가지가 있습니다. 해당 하는 컨테이너의 *외부 IP* 주소 및 노출 된 포트를 찾아 즐겨 찾는 웹 브라우저를 엽니다. 아래에서 다양 한 요청 Url을 사용 하 여 컨테이너가 실행 중인지 확인 합니다. 아래 나열 된 예제 요청 Url은 `http://localhost:5000` 이지만 특정 컨테이너는 다를 수 있습니다. 컨테이너의 *외부 IP* 주소 및 노출 된 포트를 사용 해야 한다는 점에 유의 하세요.

| 요청 URL | 용도 |
|--|--|
| `http://localhost:5000/` | 컨테이너는 홈페이지를 제공합니다. |
| `http://localhost:5000/ready` | GET을 사용 하 여 요청 하면 컨테이너에서 모델에 대 한 쿼리를 수락할 준비가 되었음을 확인할 수 있습니다.  이 요청은 Kubernetes [활동성 및 준비 상태 프로브](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/)에 사용될 수 있습니다. |
| `http://localhost:5000/status` | 또한 GET을 사용 하 여 요청 하면 컨테이너를 시작 하는 데 사용 된 api 키가 끝점 쿼리를 발생 시 키 지 않고 유효한 지 여부를 확인 합니다. 이 요청은 Kubernetes [활동성 및 준비 상태 프로브](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/)에 사용될 수 있습니다. |
| `http://localhost:5000/swagger` | 컨테이너는 엔드포인트에 대한 전체 설명서 세트와 **사용해 보기** 기능을 제공합니다. 이 기능을 사용하면 웹 기반 HTML 양식으로 설정을 입력할 수 있고 코드 작성 없이 쿼리를 만들 수 있습니다. 쿼리가 반환되면 필요한 HTTP 헤더 및 본문 형식을 보여주기 위해 예제 CURL 명령이 제공됩니다. |

![컨테이너의 홈페이지](./media/cognitive-services-containers-api-documentation/container-webpage.png)
