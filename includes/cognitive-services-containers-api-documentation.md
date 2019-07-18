---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 00cc63f53388ab7bea05a0b55784247f63477684
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67704269"
---
## <a name="validate-that-a-container-is-running"></a>컨테이너가 실행 중인지 확인 

컨테이너가 실행되고 있는지 확인하는 방법은 여러 가지가 있습니다. 

|요청|용도|
|--|--|
|`http://localhost:5000/`|컨테이너는 홈페이지를 제공합니다.|
|`http://localhost:5000/status`|컨테이너가 실행 중인지 확인하기 위해 GET과 함께 요청되며, 엔드포인트 쿼리를 유발하지 않습니다. 이 요청은 Kubernetes [활동성 및 준비 상태 프로브](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/)에 사용될 수 있습니다.|
|`http://localhost:5000/swagger`|컨테이너는 `Try it now` 기능과 엔드포인트에 대한 모든 설명서를 제공합니다. 이 기능을 사용하면 웹 기반 HTML 양식으로 설정을 입력할 수 있고 코드 작성 없이 쿼리를 만들 수 있습니다. 쿼리가 반환되면 필요한 HTTP 헤더 및 본문 형식을 보여주기 위해 예제 CURL 명령이 제공됩니다. |

![컨테이너의 홈페이지](./media/cognitive-services-containers-api-documentation/container-webpage.png)
