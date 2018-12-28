---
author: rothja
ms.service: billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: eb1fe7f83ed83efe078be0aadf26cc7db6f498e2
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/04/2018
ms.locfileid: "52886311"
---
| 리소스 | 기본 제한 | 최대 제한 |
| --- | --- | --- |
| [배포당 웹/작업자 역할](../articles/cloud-services/cloud-services-choose-me.md)<sup>1</sup> |25 |25 |
| [인스턴스 입력 엔드포인트](https://msdn.microsoft.com/library/gg557552.aspx#InstanceInputEndpoint) |25 |25 |
| [입력 엔드포인트](https://msdn.microsoft.com/library/gg557552.aspx#InputEndpoint) |25 |25 |
| [내부 엔드포인트](https://msdn.microsoft.com/library/gg557552.aspx#InternalEndpoint) |25 |25 |
| 배포당 [호스티드 서비스 인증서](../articles/cloud-services/cloud-services-certs-create.md#what-are-service-certificates) |199 |199 |

<sup>1</sup>웹/작업자 역할이 있는 각 클라우드 서비스에는 프로덕션용과 스테이징용으로 두 개의 배포가 있을 수 있습니다. 또한 이 제한은 역할당 인스턴스 수(확장)가 아닌 고유 역할 수(구성)를 지칭합니다.

