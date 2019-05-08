---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 05/07/2019
ms.openlocfilehash: 4d2cfb8a39defec9d0d429bc80bfa6abf4f62164
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65031785"
---
### <a name="running-multiple-containers-on-the-same-host"></a>동일한 호스트에서 실행 중인 여러 컨테이너

노출 된 포트를 사용 하 여 여러 컨테이너를 실행 하려는 경우에 다른 노출 된 포트를 사용 하 여 각 컨테이너를 실행 해야 합니다. 예를 들어 포트 5001의 두 번째 컨테이너의 포트 5000에 첫 번째 컨테이너를 실행 합니다.

이 컨테이너와 함께 호스트에서 실행 되는 다른 Cognitive 서비스 컨테이너를 할 수 있습니다 또는 실행 중인 동일한 Cognitive 서비스 컨테이너의 여러 컨테이너를 할 수 있습니다. 


