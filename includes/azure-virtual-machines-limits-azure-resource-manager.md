---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 02/10/2020
ms.author: cynthn
ms.openlocfilehash: 651027ffd63a376ff0b8595636ece4c8f39c86e8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "82615950"
---
| 리소스 | 제한 |
| --- | --- |
| [구독](../articles/billing-buy-sign-up-azure-subscription.md) |지역당 25,000<sup>1</sup> |
| [구독](../articles/billing-buy-sign-up-azure-subscription.md)당 VM 총 코어 수 |지역당 20<sup>1</sup> 한도를 늘리려면 고객 지원팀에 문의하세요. |
| [구독](../articles/billing-buy-sign-up-azure-subscription.md)당 Azure Spot VM 총 코어 수 |지역당 20<sup>1</sup> 한도를 늘리려면 고객 지원팀에 문의하세요. |
| 시리즈당 VM(Dv2 및 F 등), [구독](../articles/billing-buy-sign-up-azure-subscription.md)당 코어 |지역당 20<sup>1</sup> 한도를 늘리려면 고객 지원팀에 문의하세요. |
| 구독당 [가용성 집합](../articles/virtual-machines/windows/manage-availability.md#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy) |지역당 2,500 |
| 가용성 집합당 가상 머신 | 200 |
| 구독당 인증서 |무제한<sup>2</sup> |

<sup>1</sup>기본 한도는 무료 평가판 및 종량제와 같은 제안 범주 유형과 Dv2, F, G와 같은 시리즈에 따라 다릅니다. 예를 들어 기업 계약 구독의 기본값은 350입니다.

<sup>2</sup>Azure Resource Manager를 사용하면 인증서는 Azure Key Vault에 저장됩니다. 구독에 대한 인증서 수는 무제한입니다. 배포당 인증서 한도는 1MB이며 단일 VM 또는 가용성 집합으로 구성됩니다.

> [!NOTE]
> 가상 머신 코어에는 지역별 총 한도가 있습니다. 또한 Dv2 및 F와 같은 지역별 크기 시리즈에 대한 한도도 있습니다. 이러한 한도는 별도로 적용됩니다. 미국 동부 총 VM 코어 한도가 30이고, A 시리즈 코어 한도 30이며, D 시리즈 코어 한도는 30인 구독을 예로 들 수 있습니다. 이러한 구독은 30개의 A1 VM 또는 30개의 D1 VM 또는 총 코어가 30개를 초과하지 않는 두 가지의 조합을 배포할 수 있습니다. 조합의 예로는 10개의 A1 VM과 20개의 D1 VM이 있습니다.  
> <!-- -->
> 
