---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 09aa5affefc576606984ea7116b3d9bda4ba83d5
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67704170"
---
### <a name="run-multiple-containers-on-the-same-host"></a>동일한 호스트에서 여러 컨테이너 실행

노출된 포트로 여러 컨테이너를 실행하려는 경우, 각 컨테이너를 다른 노출된 포트로 실행해야 합니다. 예를 들어 첫 번째 컨테이너는 포트 5000에서 실행하고 두 번째 컨테이너는 포트 5001에서 실행합니다.

이 컨테이너와 다른 Azure Cognitive Services 컨테이너를 HOST에서 함께 실행할 수 있습니다. 또한 동일한 Cognitive Services 컨테이너의 여러 컨테이너를 실행할 수 있습니다.
