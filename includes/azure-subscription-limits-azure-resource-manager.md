---
title: 포함 파일
description: 포함 파일
services: azure-resource-manager
author: tfitzmac
ms.service: cost-management-billing
ms.topic: include
ms.date: 03/15/2021
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: f02b7f0f80cfb875cc6207b542db90607b379b67
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107800051"
---
| 리소스 | 제한 |
| --- | --- |
| [Azure Active Directory 테넌트와 연결](../articles/active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md) 구독 | 제한 없음 |
| 구독당 [공동 관리자](../articles/cost-management-billing/manage/add-change-subscription-administrator.md) |제한 없음 |
| 구독당 [리소스 그룹](../articles/azure-resource-manager/management/overview.md) |980 |
| Azure Resource Manager API 요청 크기 |4,194,304바이트 |
| 구독당 태그 수<sup>1</sup> |50 |
| 구독당 고유한 태그 계산<sup>1</sup> | 80,000 |
| 위치당 [구독 수준 배포](../articles/azure-resource-manager/templates/deploy-to-subscription.md) | 800<sup>2</sup> |

<sup>1</sup>최대 50개의 태그를 구독에 직접 적용할 수 있습니다. 그러나 구독에는 구독 내의 리소스 그룹 및 리소스에 적용되는 태그를 무제한으로 포함할 수 있습니다. 리소스당 또는 리소스 그룹당 태그 수는 50개로 제한됩니다. Resource Manager는 태그 수가 80,000개 이하일 경우에만 구독에 [고유한 태그 이름 및 값의 목록](/rest/api/resources/tags)을 반환합니다. 개수가 80,000개를 초과하더라도 태그로 리소스를 찾을 수 있습니다.

<sup>2</sup>800개 배포 제한에 도달하면 기록에서 더 이상 필요하지 않은 배포를 삭제합니다. 구독 수준 배포를 삭제하려면 [Remove-AzDeployment](/powershell/module/az.resources/Remove-AzDeployment) 또는 [az deployment sub delete](/cli/azure/deployment/sub#az_deployment_sub_delete)를 사용합니다.
