---
author: dlepow
ms.service: container-instances
ms.topic: include
ms.date: 11/09/2018
ms.author: danlep
ms.openlocfilehash: 44bdaec78e1fad574f29a5945b07041b588aaff8
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2018
ms.locfileid: "51572435"
---
| 리소스 | 기본 제한 |
| --- | :--- |
| [구독](../articles/billing-buy-sign-up-azure-subscription.md)당 컨테이너 그룹 | 100<sup>1</sup> |
| 컨테이너 그룹당 컨테이너 개수 | 60 |
| 컨테이너 그룹당 볼륨 개수 | 20 |
| IP당 포트 수 | 5 |
| 시간당 컨테이너 생성 수 |300<sup>1</sup> |
| 5분당 컨테이너 생성 수 | 100<sup>1</sup> |
| 시간당 컨테이너 삭제 수 | 300<sup>1</sup> |
| 5분당 컨테이너 삭제 수 | 100<sup>1</sup> |
| 컨테이너 그룹당 다중 컨테이너 수 | Linux만 해당<sup>2</sup> |
| Azure 파일 볼륨 | Linux만 해당<sup>2</sup> |
| GitRepo 볼륨 | Linux만 해당<sup>2</sup> |
| 비밀 볼륨 | Linux만 해당<sup>2</sup> |

<sup>1</sup> [Azure 지원 요청][azure-support]을 만들어 제한 증가를 요청합니다.<br />
<sup>2</sup> Windows에서 이 기능이 지원될 예정입니다.

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
