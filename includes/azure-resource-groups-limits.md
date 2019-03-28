---
author: rothja
ms.service: billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: 3dc09de6afaddeb06b0243eb46e888b673109545
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2019
ms.locfileid: "58505758"
---
| 리소스 | 기본 제한 | 최대 제한 |
| --- | --- | --- |
| 리소스 당 [리소스 그룹](../articles/azure-resource-manager/resource-group-overview.md#resource-groups), 리소스 유형별 |800 |리소스 유형마다 다름 |
| 배포 기록의 리소스 그룹당 배포 |800 |800 |
| 배포당 리소스 |800 |800 |
| 고유 범위당 관리 잠금 |20 |20 |
| 리소스 당 또는 리소스 그룹당 태그 수 |15 |15 |
| 태그 키 길이 |512 |512 |
| 태그 값 길이 |256 |256 |


#### <a name="template-limits"></a>템플릿 제한

| 값 | 기본 제한 | 최대 제한 |
| --- | --- | --- |
| 매개 변수 |256 |256 |
| variables |256 |256 |
| 복사본 개수를 포함 하는 리소스 |800 |800 |
| outputs |64 |64 |
| 템플릿 식 |24,576개 문자 |24,576개 문자 |
| 내보낸 템플릿의 리소스 |200 |200 | 
| 템플릿 크기 |1MB |1MB |
| 매개 변수 파일 크기 |64KB |64KB |

중첩된 템플릿을 사용하여 일부 템플릿 제한을 초과할 수 있습니다. 자세한 내용은 [Azure 리소스를 배포 하는 경우 연결 된 템플릿을 사용 하 여](../articles/azure-resource-manager/resource-group-linked-templates.md)입니다. 매개 변수, 변수 또는 출력의 수를 줄이려면 개체에 여러 값을 결합할 수 있습니다. 자세한 내용은 [매개 변수로 개체 사용](../articles/azure-resource-manager/resource-manager-objects-as-parameters.md)을 참조하세요.

리소스 그룹당 800개 배포 제한에 도달하면 기록에서 더 이상 필요하지 않은 배포를 삭제합니다. 사용 하 여 기록에서 항목을 삭제할 수 있습니다 [az 그룹 배포 삭제](/cli/azure/group/deployment) Azure CLI에 대 한 합니다. 사용할 수도 있습니다 [제거 AzResourceGroupDeployment](/powershell/module/az.resources/remove-azresourcegroupdeployment) powershell에서. 배포 기록에서 항목을 삭제 해도 배포 리소스 영향을 주지 않습니다. 