---
title: 포함 파일
description: 포함 파일
services: azure-resource-manager
author: tfitzmac
ms.service: cost-management-billing
ms.topic: include
ms.date: 03/26/2020
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 94fd7e692be31ba247e3342246d3940ed08ef9b7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334695"
---
| 리소스 | 제한 |
| --- | --- |
| Azure Active 디렉터리 테넌트당 구독 | 무제한. |
| 구독당 [공동 관리자](../articles/cost-management-billing/manage/add-change-subscription-administrator.md) |무제한. |
| 구독당 [리소스 그룹](../articles/azure-resource-manager/management/overview.md) |980 |
| Azure 리소스 관리자 API 요청 크기 |4,194,304바이트. |
| 구독당 태그<sup>1</sup> |50 |
| 구독당 고유 태그 계산<sup>1</sup> | 10000 |
| 위치별 [구독 수준 배포](../articles/azure-resource-manager/templates/deploy-to-subscription.md) | 800<sup>2</sup> |

<sup>1개</sup> 구독에 최대 50개의 태그를 직접 적용할 수 있습니다. 그러나 구독에는 구독 내의 리소스 그룹 및 리소스에 적용되는 태그수가 무제한으로 포함될 수 있습니다. 리소스 또는 리소스 그룹당 태그 수는 50개로 제한됩니다. 리소스 관리자는 태그 수가 10,000개 미만인 경우에만 구독에서 [고유한 태그 이름 및 값 목록을](/rest/api/resources/tags) 반환합니다. 숫자가 10,000을 초과하면 태그별로 리소스를 찾을 수 있습니다.  

<sup>2개</sup> 배포 800개 한도에 도달하면 더 이상 필요하지 않은 기록에서 배포를 삭제합니다. 구독 수준 배포를 삭제하려면 [제거-AzDeploy](/powershell/module/az.resources/Remove-AzDeployment) 또는 [az 배포 하위 삭제를](/cli/azure/deployment/sub?view=azure-cli-latest#az-deployment-sub-delete)사용합니다.
