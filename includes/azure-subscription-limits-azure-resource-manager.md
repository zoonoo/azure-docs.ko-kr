---
title: 파일 포함
description: 포함 파일
services: azure-resource-manager
author: tfitzmac
ms.service: cost-management-billing
ms.topic: include
ms.date: 03/26/2020
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 94fd7e692be31ba247e3342246d3940ed08ef9b7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80334695"
---
| 리소스 | 제한 |
| --- | --- |
| Azure Active Directory 테 넌 트 당 구독 | 무제한. |
| 구독 당 [공동 관리자](../articles/cost-management-billing/manage/add-change-subscription-administrator.md) |무제한. |
| 구독 당 [리소스 그룹](../articles/azure-resource-manager/management/overview.md) |980 |
| Azure Resource Manager API 요청 크기 |4194304 바이트 |
| 구독 당 태그<sup>1</sup> |50 |
| 구독 당 고유한 태그 계산<sup>1</sup> | 10000 |
| 위치별 [구독 수준 배포](../articles/azure-resource-manager/templates/deploy-to-subscription.md) | 800<sup>2</sup> |

<sup>1</sup> 구독에는 최대 50 개의 태그를 직접 적용할 수 있습니다. 그러나 구독에는 구독 내의 리소스 그룹 및 리소스에 적용 되는 태그를 무제한으로 포함할 수 있습니다. 리소스 또는 리소스 그룹당 태그 수는 50 개로 제한 됩니다. 리소스 관리자은 태그 수가 1만 이하인 경우에만 구독의 [고유 태그 이름 및 값 목록을](/rest/api/resources/tags) 반환 합니다. 숫자가 1만를 초과 하는 경우에도 태그를 기준으로 리소스를 찾을 수 있습니다.  

<sup>2</sup> 800 배포 제한에 도달 하면 더 이상 필요 하지 않은 기록에서 배포를 삭제 합니다. 구독 수준 배포를 삭제 하려면 [AzDeployment](/powershell/module/az.resources/Remove-AzDeployment) 또는 [az deployment sub delete](/cli/azure/deployment/sub?view=azure-cli-latest#az-deployment-sub-delete)를 사용 합니다.
