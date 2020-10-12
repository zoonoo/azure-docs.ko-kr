---
author: dlepow
ms.service: container-instances
ms.topic: include
ms.date: 07/22/2020
ms.author: danlep
ms.openlocfilehash: 6878180ffedfaa53f25d2bdc6db72dcd7dd8b38b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87384833"
---
| 리소스 | 제한 |
| --- | :--- |
| 구독당 지역별 표준 SKU 컨테이너 그룹의 수 | 100<sup>1</sup> |
| 구독당 지역별 전용 SKU 컨테이너 그룹의 수 | 0<sup>1</sup> |
| 컨테이너 그룹당 컨테이너 개수 | 60 |
| 컨테이너 그룹당 볼륨 개수 | 20 |
| 구독당 지역별 표준 SKU 코어(CPU)의 수 | 10<sup>1,2</sup> | 
| 구독당 지역별 K80 GPU용 표준 코어(CPU)의 수 | 18<sup>1,2</sup> |
| 구독당 지역별 P100 또는 V100 GPU용 표준 코어(CPU)의 수 | 0<sup>1,2</sup> |
| IP당 포트 수 | 5 |
| 컨테이너 인스턴스 로그 크기 - 실행 중인 인스턴스 | 4MB |
| 컨테이너 인스턴스 로그 크기 - 중지된 인스턴스 | 16KB 또는 1000개 줄 |
| 시간당 컨테이너 생성 수 |300<sup>1</sup> |
| 5분당 컨테이너 생성 수 | 100<sup>1</sup> |
| 시간당 컨테이너 삭제 수 | 300<sup>1</sup> |
| 5분당 컨테이너 삭제 수 | 100<sup>1</sup> |


<sup>1</sup>제한을 늘리도록 요청하려면 [Azure 지원 요청][azure-support]을 만듭니다. [Azure 체험 계정](https://azure.microsoft.com/offers/ms-azr-0044p/) 및 [Azure for Students](https://azure.microsoft.com/offers/ms-azr-0170p/)를 포함한 체험 구독은 제한 또는 할당량을 늘릴 수 없습니다. [체험 구독](../articles/cost-management-billing/manage/upgrade-azure-subscription.md)을 사용하는 경우 종량제 구독으로 업그레이드할 수 있습니다.<br />
<sup>2</sup>기본적으로 [종량제](https://azure.microsoft.com/offers/ms-azr-0003p/) 구독으로 제한됩니다. 제한은 다른 범주 유형에 따라 달라질 수 있습니다.<br/>

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
