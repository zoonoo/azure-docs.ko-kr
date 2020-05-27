---
author: rothja
ms.service: cost-management-billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: 3295c7e677e4c9bf7bff27614fe5fa4fa3200570
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80334893"
---
| 리소스 | 제한 |
| --- | --- |
| [배포 당 웹 또는 작업자 역할](../articles/cloud-services/cloud-services-choose-me.md)<sup>1</sup> |25 |
| 배포 당 [인스턴스 입력 끝점](/previous-versions/azure/reference/gg557552(v=azure.100)#instanceinputendpoint) |25 |
| 배포 당 [입력 끝점](/previous-versions/azure/reference/gg557552(v=azure.100)#inputendpoint) |25 |
| 배포 당 [내부 끝점](/previous-versions/azure/reference/gg557552(v=azure.100)#internalendpoint) |25 |
| 배포당 [호스티드 서비스 인증서](../articles/cloud-services/cloud-services-certs-create.md#what-are-service-certificates) |199 |

<sup>1</sup> 웹 또는 작업자 역할을 사용 하는 각 Azure 클라우드 서비스에는 두 개의 배포 (프로덕션 및 스테이징 용)가 있을 수 있습니다. 이 제한은 구성의 고유한 역할 수를 나타냅니다. 이 제한은 역할 당 인스턴스 수, 즉 크기 조정을 참조 하지 않습니다.

