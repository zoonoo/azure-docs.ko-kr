---
author: rothja
ms.service: azure-resource-manager
ms.topic: include
ms.date: 11/05/2020
ms.author: rohink
ms.openlocfilehash: 7011b92485c56187021c9043ba84bc85e448a98f
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/06/2020
ms.locfileid: "94329520"
---
**공용 DNS 영역**

| 리소스 | 제한 |
| --- | --- |
| 구독당 공용 DNS 영역 |250 <sup>1</sup> |
| 공용 DNS 영역당 레코드 집합 |10,000 <sup>1</sup> |
| 공용 DNS 영역에 있는 레코드 집합당 레코드 |20 |
| 단일 Azure 리소스의 별칭 레코드 수 |20|

<sup>1</sup> 이러한 제한을 늘려야 하는 경우 Azure 지원에 문의하세요.

**프라이빗 DNS 영역**

| 리소스 | 제한 |
| --- | --- |
| 구독당 프라이빗 DNS 영역 |1000|
| 프라이빗 DNS 영역당 레코드 집합 |25000|
| 프라이빗 DNS 영역의 레코드 집합당 레코드 |20|
| 프라이빗 DNS 영역당 Virtual Network 링크 |1000|
| 자동 등록을 사용하는 프라이빗 DNS 영역당 Virtual Network 링크 |100|
| 자동 등록을 사용하도록 설정한 상태에서 가상 네트워크를 연결할 수 있는 프라이빗 DNS 영역 수 |1|
| 가상 네트워크를 연결할 수 있는 프라이빗 DNS 영역 수 |1000|
| 가상 머신이 Azure DNS 확인자로 보낼 수 있는 초당 DNS 쿼리 수 |1000 <sup>1</sup> |
| 가상 머신당 최대 대기 중(응답 보류 중) DNS 쿼리 수 |200 <sup>1</sup> |

<sup>1</sup>이러한 제한은 가상 네트워크 수준이 아닌 모든 개별 가상 머신에 적용됩니다. 이러한 제한을 초과하는 DNS 쿼리는 삭제됩니다.
