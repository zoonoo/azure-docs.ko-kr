---
title: 포함 파일
description: 포함 파일
author: tfitzmac
ms.service: governance
ms.topic: include
ms.date: 03/26/2020
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 9b2bf8763d175bca028be1fcceda921e87eb5b23
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335116"
---
| 리소스 | 제한 |
| --- | --- |
| 디렉터리당 관리 그룹 | 10000 |
| 관리 그룹별 구독 | 무제한. |
| 관리 그룹 계층 구조 수준 | 루트 레벨 플러스 6 레벨<sup>1</sup> |
| 관리 그룹당 직접 상위 관리 그룹 | 1 |
| 위치별 [관리 그룹 수준 배포](../articles/azure-resource-manager/templates/deploy-to-management-group.md) | 800<sup>2</sup> |

<sup>1개</sup> 6개 수준에는 구독 수준이 포함되지 않습니다.

<sup>2개</sup> 배포 800개 한도에 도달하면 더 이상 필요하지 않은 기록에서 배포를 삭제합니다. 관리 그룹 수준 배포를 삭제하려면 [제거-AzManagementGroupDeploy](/powershell/module/az.resources/Remove-AzManagementGroupDeployment) 또는 [az 배포 mg 삭제를](/cli/azure/deployment/mg?view=azure-cli-latest#az-deployment-mg-delete)사용합니다.
