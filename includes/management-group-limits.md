---
title: 파일 포함
description: 포함 파일
author: tfitzmac
ms.service: governance
ms.topic: include
ms.date: 03/26/2020
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 9b2bf8763d175bca028be1fcceda921e87eb5b23
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "80335116"
---
| 리소스 | 제한 |
| --- | --- |
| 디렉터리 당 관리 그룹 | 10000 |
| 관리 그룹당 구독 | 무제한. |
| 관리 그룹 계층 구조 수준 | 루트 수준 + 6 수준<sup>1</sup> |
| 관리 그룹당 직접 부모 관리 그룹 | 하나 |
| 위치 당 [관리 그룹 수준 배포](../articles/azure-resource-manager/templates/deploy-to-management-group.md) | 800<sup>2</sup> |

<sup>1</sup> 6 수준에는 구독 수준이 포함 되지 않습니다.

<sup>2</sup> 800 배포 제한에 도달 하면 더 이상 필요 하지 않은 기록에서 배포를 삭제 합니다. 관리 그룹 수준 배포를 삭제 하려면 [AzManagementGroupDeployment](/powershell/module/az.resources/Remove-AzManagementGroupDeployment) 또는 [az deployment mg delete](/cli/azure/deployment/mg?view=azure-cli-latest#az-deployment-mg-delete)를 사용 합니다.
