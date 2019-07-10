---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 05/07/2019
ms.openlocfilehash: 327654ca06a3997855d904414fa4258491ee6c88
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67182478"
---
### <a name="run-multiple-containers-on-the-same-host"></a>동일한 호스트에서 여러 컨테이너 실행

노출된 포트로 여러 컨테이너를 실행하려는 경우, 각 컨테이너를 다른 노출된 포트로 실행해야 합니다. 예를 들어 포트 5000에 첫 번째 컨테이너, 포트 5001에 두 번째 컨테이너를 실행 합니다.

이 컨테이너와 다른 Azure Cognitive Services 컨테이너를 HOST에서 함께 실행할 수 있습니다. 또한 동일한 Cognitive Services 컨테이너의 여러 컨테이너를 실행할 수 있습니다.
