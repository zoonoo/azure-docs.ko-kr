---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 02/21/2019
ms.openlocfilehash: e80feac7dbf16652cc2e2a6176ed8b2c8c48e35b
ms.sourcegitcommit: e88188bc015525d5bead239ed562067d3fae9822
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/24/2019
ms.locfileid: "56753611"
---
### <a name="running-multiple-containers-on-the-same-host"></a>동일한 호스트에서 실행 중인 여러 컨테이너

노출 된 포트를 사용 하 여 여러 컨테이너를 실행 하려는 경우에 다른 포트를 사용 하 여 각 컨테이너를 실행 해야 합니다. 예를 들어 포트 5001의 두 번째 컨테이너의 포트 5000에 첫 번째 컨테이너를 실행 합니다.

대체는 `<container-registry>` 및 `<container-name>` 사용할 컨테이너의 값입니다. 이러한 동일한 컨테이너를 사용할 필요가 없습니다. 얼굴 컨테이너와 함께 호스트에서 실행 중인 LUIS 컨테이너 할 수 있습니다 또는 실행 하는 여러 얼굴 컨테이너 할 수 있습니다. 

포트 5000에서 첫 번째 컨테이너를 실행 합니다. 

```bash 
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
<container-registry>/microsoft/<container-name> \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}
```

포트 5001에서 두 번째 컨테이너를 실행 합니다.


```bash 
docker run --rm -it -p 5001:5000 --memory 4g --cpus 1 \
<container-registry>/microsoft/<container-name> \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}
```

각 후속 컨테이너는 다른 포트에 있어야 합니다. 
