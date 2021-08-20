---
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 05/21/2021
ms.author: danlep
ms.openlocfilehash: 6dfb53195491df729ea4b342aa44f209fe17f9c0
ms.sourcegitcommit: 7c44970b9caf9d26ab8174c75480f5b09ae7c3d7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/27/2021
ms.locfileid: "112983766"
---
> [!NOTE]
> 다음 예제에서는 Docker 허브에서 공용 컨테이너 이미지를 가져옵니다. 익명 끌어오기 요청을 수행하는 대신 먼저 Docker 허브 계정(`docker login`)을 사용하여 인증하는 것이 좋습니다. 공용 콘텐츠를 사용할 때 신뢰성을 향상시키려면 개인 Azure 컨테이너 레지스트리에서 이미지를 가져오고 관리하세요. [공용 이미지 사용에 대해 자세히 알아봅니다](../articles/container-registry/buffer-gate-public-content.md).