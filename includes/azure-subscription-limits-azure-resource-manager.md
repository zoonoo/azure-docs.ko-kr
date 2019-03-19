---
title: 포함 파일
description: 포함 파일
services: billing
author: rothja
ms.service: billing
ms.topic: include
ms.date: 10/19/2018
ms.author: jroth
ms.custom: include file
ms.openlocfilehash: a1645a8471f75f1d56f3e61c0adfc3fadee14560
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/07/2019
ms.locfileid: "57554016"
---
| 리소스 | 기본 제한 | 최대 제한 |
| --- | --- | --- |
|  [구독](../articles/billing-buy-sign-up-azure-subscription.md) |25,000<sup>1</sup> 지역당 합니다. |지역당 25,000 합니다. |
| [구독](../articles/billing-buy-sign-up-azure-subscription.md)당 VM 총 코어 수 |20<sup>1</sup> 지역당 합니다. | 지원에 문의 |
| VM 시리즈, Dv2, F 등 당 코어 당 [구독](../articles/billing-buy-sign-up-azure-subscription.md) |20<sup>1</sup> 지역당 합니다. | 지원에 문의 |
| [공동](../articles/billing-add-change-azure-subscription-administrator.md) 구독 당 |무제한. |무제한. |
| 구독당 하위 지역별 [저장소 계정](../articles/storage/common/storage-quickstart-create-account.md) |200 |200<sup>2</sup> |
| [리소스 그룹](../articles/azure-resource-manager/resource-group-overview.md) 구독 당 |980 |980 |
| [가용성 집합](../articles/virtual-machines/windows/manage-availability.md#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy) 구독 당 |지역당 2,000 개입니다. |지역당 2,000 개입니다. |
| Azure Resource Manager API 요청 크기 |4,194,304 바이트 수입니다. |4,194,304 바이트 수입니다. |
| 구독당 태그 수<sup>3</sup> |무제한. |무제한. |
| 구독당 고유한 태그 계산<sup>3</sup> | 10000 | 10000 |
| [클라우드 서비스](../articles/cloud-services/cloud-services-choose-me.md)  |해당 없음<sup>4</sup> |해당 없음<sup>4</sup> |
| [선호도 그룹](../articles/virtual-network/virtual-networks-migrate-to-regional-vnet.md)  |해당 없음<sup>4</sup> |해당 없음<sup>4</sup> |
| [구독 수준 배포](../articles/azure-resource-manager/deploy-to-subscription.md) 위치 당 | 800 | 800 |

<sup>1</sup>기본 제한은 무료 평가판에서 종 량 제 등의 제품 범주 유형에 따라 다릅니다 시리즈, Dv2, F 및 7. 예:

<sup>2</sup>모두 표준 및 프리미엄 저장소 계정이 포함 됩니다. 저장소 계정이 200 개 이상 필요한 경우 요청을 통해 [Azure 지원](https://azure.microsoft.com/support/faq/)합니다. Azure Storage 비즈니스 사례를 검토 하 고 storage 계정을 250 개까지 승인할 수 있습니다.

<sup>3</sup>구독당 무제한의 태그를 적용할 수 있습니다. 리소스당 또는 리소스 그룹당 태그 수는 15개로 제한됩니다. 리소스 관리자를 반환 합니다는 [고유한 태그 이름 및 값의 목록](/rest/api/resources/tags) 태그 수는 10,000 개입니다 하는 경우에 또는 구독에서. 여전히 찾을 수 있습니다 리소스 태그로 숫자가 10,000을 초과 하는 경우.  

<sup>4</sup>이러한 기능은 더 이상 Azure 리소스 그룹 및 리소스 관리자를 사용 하 여 필요 합니다.

> [!NOTE]
> 가상 컴퓨터 코어에 지역 총 한도가 있습니다. Dv2 및 6. 같은 지역 당 크기 계열에 대 한 제한 또한 있습니다. 이러한 제한은 개별적으로 적용 됩니다. 미국 동부 총 VM 코어 한도가 30이고, A 시리즈 코어 한도 30이며, D 시리즈 코어 한도는 30인 구독을 예로 들 수 있습니다. 이 구독을 총 코어가 30 개를 넘지 A1 Vm 30 또는 D1 Vm 30, 또는 둘의 조합에 배포할 수 있습니다. A1 Vm 10 및 D1 Vm 20 조합 예로 있습니다.  
> <!-- -->
> 
> 

