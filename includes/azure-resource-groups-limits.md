---
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 02/24/2020
ms.author: tomfitz
ms.openlocfilehash: c883383d3c870689bb95f808f6f60c5185c165c3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80334944"
---
| 리소스 | 제한 |
| --- | --- |
| [리소스](../articles/azure-resource-manager/management/overview.md#resource-groups) 그룹당 리소스 | 리소스는 리소스 그룹으로 제한 되지 않습니다. 대신 리소스 그룹의 리소스 형식에 의해 제한 됩니다. 다음 행을 참조 하세요. |
| 리소스 그룹당 리소스 유형별 리소스 |800-일부 리소스 형식은 800 제한을 초과할 수 있습니다. 리소스 그룹당 [800 인스턴스로 제한 되지 않는 리소스를](../articles/azure-resource-manager/management/resources-without-resource-group-limit.md)참조 하세요. |
| 배포 기록의 리소스 그룹당 배포 |800<sup>1</sup> |
| 배포당 리소스 |800 |
| 고유 범위 당 관리 잠금 |20 |
| 리소스 또는 리소스 그룹당 태그 수 |50 |
| 태그 키 길이 |512 |
| 태그 값 길이 |256 |

<sup>1</sup> 리소스 그룹당 800 배포 제한에 도달 하면 더 이상 필요 하지 않은 기록에서 배포를 삭제 합니다. 배포 기록에서 항목을 삭제 해도 배포 된 리소스에는 영향을 주지 않습니다. 자세한 내용은 [배포 수가 800를 초과 하는 경우 오류 해결](../articles/azure-resource-manager/templates/deployment-quota-exceeded.md)을 참조 하세요.

#### <a name="template-limits"></a>템플릿 제한

| 값 | 제한 |
| --- | --- |
| 매개 변수 |256 |
| 변수 |256 |
| 리소스(인쇄 매수 포함) |800 |
| outputs |64 |
| 템플릿 식 |24,576개 문자 |
| 내보낸 템플릿의 리소스 |200 |
| 템플릿 크기 |4MB |
| 매개 변수 파일 크기 |64KB |

중첩된 템플릿을 사용하여 일부 템플릿 제한을 초과할 수 있습니다. 자세한 내용은 [Azure 리소스를 배포할 때 연결 된 템플릿 사용](../articles/azure-resource-manager/templates/linked-templates.md)을 참조 하세요. 매개 변수, 변수 또는 출력의 수를 줄이려면 개체에 여러 값을 결합할 수 있습니다. 자세한 내용은 [매개 변수로 개체 사용](../articles/azure-resource-manager/resource-manager-objects-as-parameters.md)을 참조하세요.
