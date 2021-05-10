---
author: rothja
ms.service: cloud-services
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: 390460cf6a22c164ee41a9b6679c7b7f2979d8ec
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "85838856"
---
| 리소스 | 제한 |
| --- | --- |
| [배포당 웹 또는 작업자 역할](../articles/cloud-services/cloud-services-choose-me.md)<sup>1</sup> |25 |
| 배포당 [인스턴스 입력 엔드포인트](/previous-versions/azure/reference/gg557552(v=azure.100)#instanceinputendpoint) |25 |
| 배포당 [입력 엔드포인트](/previous-versions/azure/reference/gg557552(v=azure.100)#inputendpoint) |25 |
| 배포당 [내부 엔드포인트](/previous-versions/azure/reference/gg557552(v=azure.100)#internalendpoint) |25 |
| 배포당 [호스티드 서비스 인증서](../articles/cloud-services/cloud-services-certs-create.md#what-are-service-certificates) |199 |

<sup>1</sup>웹 또는 작업자 역할이 있는 각 Azure 클라우드 서비스에는 프로덕션용과 스테이징용으로 두 개의 배포가 있을 수 있습니다. 이 한도는 고유한 역할 수, 즉 구성을 참조합니다. 이 한도는 역할당 인스턴스 수, 즉 크기 조정을 참조하지 않습니다.

