---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 02/10/2020
ms.author: cynthn
ms.openlocfilehash: 90dc5a067c05cadb3d5e102435b2e3d3de803e28
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335024"
---
| 리소스 | 제한 |
| --- | --- |
| [구독](../articles/billing-buy-sign-up-azure-subscription.md) |지역당 25,000<sup>개.</sup> |
| [구독](../articles/billing-buy-sign-up-azure-subscription.md)당 VM 총 코어 수 |지역당 20<sup>1개.</sup> 한도를 높이려면 지원팀에 문의하십시오. |
| [구독당](../articles/billing-buy-sign-up-azure-subscription.md) Azure 스팟 VM 총 코어 |지역당 20<sup>1개.</sup> 한도를 높이려면 지원팀에 문의하십시오. |
| Dv2 및 F와 같은 시리즈별 VM, [구독당](../articles/billing-buy-sign-up-azure-subscription.md) 코어 |지역당 20<sup>1개.</sup> 한도를 높이려면 지원팀에 문의하십시오. |
| 구독당 [가용성 집합](../articles/virtual-machines/windows/manage-availability.md#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy) |지역당 2,000개. |
| 가용성 집합당 가상 머신 | 200 |
| 구독당 인증서 |무제한<sup>2</sup> |

<sup>1개</sup> 기본 한도는 무료 평가판 및 종량제 와 같은 오퍼 카테고리 유형및 Dv2, F 및 G와 같은 시리즈별로 다릅니다. 예를 들어 엔터프라이즈 계약 구독의 기본값은 350입니다.

<sup>2개</sup> Azure 리소스 관리자를 사용하면 인증서가 Azure 키 자격 증명 모음에 저장됩니다. 인증서 수는 구독에 대해 무제한입니다. 단일 VM 또는 가용성 집합으로 구성된 배포당 인증서의 1MB 제한이 있습니다.

> [!NOTE]
> 가상 시스템 코어에는 지역별 총 제한이 있습니다. 또한 Dv2 및 F와 같은 지역별 시리즈당 제한도 있습니다. 이러한 제한은 별도로 적용됩니다. 미국 동부 총 VM 코어 한도가 30이고, A 시리즈 코어 한도 30이며, D 시리즈 코어 한도는 30인 구독을 예로 들 수 있습니다. 이 구독은 30개의 A1 VM 또는 30개의 D1 VM을 배포하거나 이 두 가지를 조합하여 총 30개의 코어를 초과하지 않도록 배포할 수 있습니다. 조합의 예로는 10개의 A1 VM과 20개의 D1 VM이 있습니다.  
> <!-- -->
> 
