---
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 04/19/2019
ms.author: tomfitz
ms.openlocfilehash: 8bd16378e9c82a011309c12cf241b59d03405a77
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66238248"
---
| Resource | 기본 제한 | 최대 제한 |
| --- | --- | --- |
| 리소스 당 [리소스 그룹](../articles/azure-resource-manager/resource-group-overview.md#resource-groups), 리소스 유형별 |800 |리소스 유형마다 다름 |
| 배포 기록의 리소스 그룹당 배포 |800<sup>1</sup> |800 |
| 배포당 리소스 |800 |800 |
| 고유 범위당 관리 잠금 |20 |20 |
| 리소스 당 또는 리소스 그룹당 태그 수 |15 |15 |
| 태그 키 길이 |512 |512 |
| 태그 값 길이 |256 |256 |

<sup>1</sup>리소스 그룹당 800 배포의 제한에 도달 하면 더 이상 필요 없는 기록에서 배포를 삭제 합니다. 배포 기록에서 항목을 삭제 하면 배포 된 리소스 영향을 주지 않습니다. Azure CLI에서 [az group deployment delete](/cli/azure/group/deployment)를 사용하거나 PowerShell에서 [Remove-AzResourceGroupDeployment](/powershell/module/az.resources/remove-azresourcegroupdeployment)를 사용하여 기록에서 항목을 삭제할 수 있습니다.  PowerShell 스크립트를 자동화 하는 지속적인 통합 및 지속적인 업데이트 (CI/CD) 시나리오에서 배포를 삭제 대해서 [제거 deployments.ps1](https://gist.github.com/bmoore-msft/ed33fb940dafb09380174b7fca57651f)합니다.

#### <a name="template-limits"></a>템플릿 제한

| Value | 기본 제한 | 최대 제한 |
| --- | --- | --- |
| 매개 변수 |256 |256 |
| variables |256 |256 |
| 리소스(인쇄 매수 포함) |800 |800 |
| outputs |64 |64 |
| 템플릿 식 |24,576개 문자 |24,576개 문자 |
| 내보낸 템플릿의 리소스 |200 |200 | 
| 템플릿 크기 |1MB |1MB |
| 매개 변수 파일 크기 |64KB |64KB |

중첩된 템플릿을 사용하여 일부 템플릿 제한을 초과할 수 있습니다. 자세한 내용은 [Azure 리소스를 배포 하는 경우 연결 된 템플릿을 사용 하 여](../articles/azure-resource-manager/resource-group-linked-templates.md)입니다. 매개 변수, 변수 또는 출력의 수를 줄이려면 개체에 여러 값을 결합할 수 있습니다. 자세한 내용은 [매개 변수로 개체 사용](../articles/azure-resource-manager/resource-manager-objects-as-parameters.md)을 참조하세요.
