---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 2ae72045ae18d84eac2a6d619d94e3a9e49415ae
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/29/2018
ms.locfileid: "50227391"
---
## <a name="issue-custom-image-provisioning-errors"></a>문제: 사용자 지정 이미지; 프로비전 오류
프로비전 오류는 일반화된 VM 이미지를 특수화된 VM 이미지로 또는 그 반대로 업로드하거나 캡처하는 경우에 발생합니다. 전자의 경우는 프로비전 시간 초과 오류를, 후자는 프로비전 실패를 유발합니다. 오류 없이 사용자 지정 이미지를 배포하려면, 캡처 프로세스 중에 이미지 유형이 변경되지 않도록 해야 합니다.

다음 테이블은 일반화 및 특수화된 이미지의 가능한 조합, 접하게 되는 오류 유형, 오류를 해결하기 위해 수행할 작업이 나열되어 있습니다.

