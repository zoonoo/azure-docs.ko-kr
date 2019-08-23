---
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 07/30/2019
ms.author: tomfitz
ms.openlocfilehash: f027f5b295d75c105e81360a10718239e5b08e4d
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/31/2019
ms.locfileid: "68698286"
---
| 리소스 | 기본 제한 | 최대 제한 |
| --- | --- | --- |
| [리소스 그룹](../articles/azure-resource-manager/resource-group-overview.md#resource-groups)당 리소스 유형별 리소스 |800 |일부 리소스 형식은 800 한계를 초과할 수 있습니다. 리소스 그룹당 [800 인스턴스로 제한 되지 않는 리소스를](../articles/azure-resource-manager/resources-without-rg-limit.md)참조 하세요. |
| 배포 기록의 리소스 그룹당 배포 |800<sup>1</sup> |800 |
| 배포당 리소스 |800 |800 |
| 고유 범위 당 관리 잠금 |20 |20 |
| 리소스 또는 리소스 그룹당 태그 수 |15 |15 |
| 태그 키 길이 |512 |512 |
| 태그 값 길이 |256 |256 |

<sup>1</sup> 리소스 그룹당 800 배포 제한에 도달 하면 더 이상 필요 하지 않은 기록에서 배포를 삭제 합니다. 배포 기록에서 항목을 삭제 해도 배포 된 리소스에는 영향을 주지 않습니다. Azure CLI에서 [az group deployment delete](/cli/azure/group/deployment)를 사용하거나 PowerShell에서 [Remove-AzResourceGroupDeployment](/powershell/module/az.resources/remove-azresourcegroupdeployment)를 사용하여 기록에서 항목을 삭제할 수 있습니다.  지속적인 통합 및 지속적인 업데이트 (CI/CD) 시나리오에서 배포 삭제를 자동화 하는 PowerShell 스크립트는 [remove-deployments](https://gist.github.com/bmoore-msft/ed33fb940dafb09380174b7fca57651f)를 참조 하세요.

#### <a name="template-limits"></a>템플릿 제한

| 값 | 기본 제한 | 최대 제한 |
| --- | --- | --- |
| 매개 변수 |256 |256 |
| 변수 |256 |256 |
| 리소스(인쇄 매수 포함) |800 |800 |
| 출력 |64 |64 |
| 템플릿 식 |24,576개 문자 |24,576개 문자 |
| 내보낸 템플릿의 리소스 |200 |200 | 
| 템플릿 크기 |4MB |4MB |
| 매개 변수 파일 크기 |64KB |64KB |

중첩된 템플릿을 사용하여 일부 템플릿 제한을 초과할 수 있습니다. 자세한 내용은 [Azure 리소스를 배포할 때 연결 된 템플릿 사용](../articles/azure-resource-manager/resource-group-linked-templates.md)을 참조 하세요. 매개 변수, 변수 또는 출력의 수를 줄이려면 개체에 여러 값을 결합할 수 있습니다. 자세한 내용은 [매개 변수로 개체 사용](../articles/azure-resource-manager/resource-manager-objects-as-parameters.md)을 참조하세요.
