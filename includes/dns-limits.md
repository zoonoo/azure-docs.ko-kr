---
author: rothja
ms.service: cost-management-billing
ms.topic: include
ms.date: 2/14/2020
ms.author: rohink
ms.openlocfilehash: 3fbbb9d491eef202309c9e64f9a5a839a5d4516d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335065"
---
**공용 DNS 영역**

| 리소스 | 제한 |
| --- | --- |
| 구독당 공용 DNS 영역 |250 <sup>1</sup> |
| 공용 DNS 영역당 레코드 세트 |10,000 <sup>1</sup> |
| 공용 DNS 영역에서 설정된 레코드당 레코드수 |20 |
| 단일 Azure 리소스에 대한 별칭 레코드 수 |20|
| 구독당 개인 DNS 영역 |1000|
| 개인 DNS 영역당 레코드 세트 |25000|
| 개인 DNS 영역에 대해 설정된 레코드당 레코드 |20|
| 개인 DNS 영역당 가상 네트워크 링크 |1000|
| 자동 등록이 활성화된 개인 DNS 영역당 가상 네트워크 링크 |100|
| 가상 네트워크가 자동 등록을 사용하도록 설정하여 연결할 수 있는 개인 DNS 영역 수 |1|
| 가상 네트워크가 연결될 수 있는 개인 DNS 영역 수 |1000|
| 가상 시스템이 Azure DNS 확인자로 초당 보낼 수 있는 DNS 쿼리 수 |500 <sup>2</sup> |
| 가상 시스템당 대기 중인 최대 DNS 쿼리 수(대기 중인 응답) |200 <sup>2</sup> |

<sup>1개</sup> 이러한 제한을 늘려야 하는 경우 Azure 지원에 문의하십시오.

<sup>2개</sup> 이러한 제한은 가상 네트워크 수준이 아닌 모든 개별 가상 시스템에 적용됩니다. 이러한 제한을 초과하는 DNS 쿼리는 삭제됩니다.