---
title: 포함 파일
description: 포함 파일
services: billing
author: rothja
ms.service: billing
ms.topic: include
ms.date: 04/22/2019
ms.author: jroth
ms.custom: include file
ms.openlocfilehash: 712b70960e09a9c2b0e7a998bc0bddbc28c1e112
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64733745"
---
| 리소스 | 기본 제한 | 최대 제한 |
| --- | --- | --- |
|  [구독](../articles/billing-buy-sign-up-azure-subscription.md) |25,000<sup>1</sup> 지역당 합니다. |지역당 25,000 합니다. |
| [구독](../articles/billing-buy-sign-up-azure-subscription.md)당 VM 총 코어 수 |20<sup>1</sup> 지역당 합니다. | 지원에 문의 |
| VM 시리즈, Dv2, F 등 당 코어 당 [구독](../articles/billing-buy-sign-up-azure-subscription.md) |20<sup>1</sup> 지역당 합니다. | 지원에 문의 |
| [공동](../articles/billing-add-change-azure-subscription-administrator.md) 구독 당 |무제한. |무제한. |
| 구독당 하위 지역별 [저장소 계정](../articles/storage/common/storage-quickstart-create-account.md) |250 |250 |
| [리소스 그룹](../articles/azure-resource-manager/resource-group-overview.md) 구독 당 |980 |980 |
| [가용성 집합](../articles/virtual-machines/windows/manage-availability.md#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy) 구독 당 |지역당 2,000 개입니다. |지역당 2,000 개입니다. |
| Azure Resource Manager API 요청 크기 |4,194,304 바이트 수입니다. |4,194,304 바이트 수입니다. |
| 구독 당 태그 수<sup>2</sup> |무제한. |무제한. |
| 구독 당 고유한 태그 계산<sup>2</sup> | 10000 | 10000 |
| [클라우드 서비스](../articles/cloud-services/cloud-services-choose-me.md)  |해당 없음<sup>3</sup> |해당 없음<sup>3</sup> |
| [선호도 그룹](../articles/virtual-network/virtual-networks-migrate-to-regional-vnet.md)  |해당 없음<sup>3</sup> |해당 없음<sup>3</sup> |
| [구독 수준 배포](../articles/azure-resource-manager/deploy-to-subscription.md) 위치 당 | 800<sup>4</sup> | 800 |

<sup>1</sup>기본 제한은 무료 평가판에서 종 량 제 등의 제품 범주 유형에 따라 다릅니다 시리즈, Dv2, F 및 7. 예:

<sup>2</sup>개수에 제한 없이 구독 당 태그를 적용할 수 있습니다. 리소스당 또는 리소스 그룹당 태그 수는 15개로 제한됩니다. 리소스 관리자를 반환 합니다는 [고유한 태그 이름 및 값의 목록](/rest/api/resources/tags) 태그 수는 10,000 개입니다 하는 경우에 또는 구독에서. 여전히 찾을 수 있습니다 리소스 태그로 숫자가 10,000을 초과 하는 경우.  

<sup>3</sup>이러한 기능은 더 이상 Azure 리소스 그룹 및 리소스 관리자를 사용 하 여 필요 합니다.

<sup>4</sup>800 개 배포 제한에 도달 하면 더 이상 필요 없는 기록에서 배포를 삭제 합니다. 구독 수준 배포를 삭제 하려면 사용 하 여 [제거 AzDeployment](/powershell/module/az.resources/Remove-AzDeployment) 하거나 [az 배포 삭제](/cli/azure/deployment?view=azure-cli-latest#az-deployment-delete)합니다.

> [!NOTE]
> 가상 컴퓨터 코어에 지역 총 한도가 있습니다. Dv2 및 6. 같은 지역 당 크기 계열에 대 한 제한 또한 있습니다. 이러한 제한은 개별적으로 적용 됩니다. 미국 동부 총 VM 코어 한도가 30이고, A 시리즈 코어 한도 30이며, D 시리즈 코어 한도는 30인 구독을 예로 들 수 있습니다. 이 구독을 총 코어가 30 개를 넘지 A1 Vm 30 또는 D1 Vm 30, 또는 둘의 조합에 배포할 수 있습니다. A1 Vm 10 및 D1 Vm 20 조합 예로 있습니다.  
> <!-- -->
> 
> 

