---
title: 지역 간에 Azure API Management를 마이그레이션하는 방법
description: 한 지역에서 다른 지역으로 API Management 인스턴스를 마이그레이션하는 방법에 대해 알아봅니다.
services: api-management
documentationcenter: ''
author: miaojiang
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/26/2019
ms.author: apimpm
ms.openlocfilehash: 0eed2328aca78402c5f4691bb9b3d07d4f36472e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "86250229"
---
# <a name="how-to-migrate-azure-api-management-across-regions"></a>지역 간에 Azure API Management를 마이그레이션하는 방법
한 Azure 지역에서 다른 지역으로 API Management 인스턴스를 마이그레이션하려면 [백업 및 복원](api-management-howto-disaster-recovery-backup-restore.md) 기능을 사용할 수 있습니다. 원본 및 대상 지역에서 동일한 API Management 가격 책정 계층을 선택해야 합니다. 

> [!NOTE]
> 서로 다른 클라우드 유형 간에 마이그레이션하는 동안에는 백업 및 복원이 작동하지 않습니다. 이를 위해서는 리소스를 [템플릿으로](../azure-resource-manager/management/manage-resource-groups-portal.md#export-resource-groups-to-templates)내보내야 합니다. 그런 다음 대상 Azure 지역에 대해 내보낸 템플릿을 조정하고 리소스를 다시 만듭니다. 

## <a name="option-1-use-a-different-api-management-instance-name"></a>옵션 1: 다른 API Management 인스턴스 이름 사용

1. 대상 영역에서 원본 API Management 인스턴스와 동일한 가격 책정 계층을 사용하여 새 API Management 인스턴스를 만듭니다. 새 인스턴스의 이름은 달라야 합니다. 
1. 기존 API Management 인스턴스를 스토리지 계정에 백업합니다.
1. 2단계에서 만든 백업을 1단계의 새 지역에서 만든 새 API Management 인스턴스로 복원합니다.
1. 원본 지역 API Management 인스턴스를 가리키는 사용자 지정 도메인이 있는 경우 새 API Management 인스턴스를 가리키도록 사용자 지정 도메인 CNAME을 업데이트합니다. 


## <a name="option-2-use-the-same-api-management-instance-name"></a>옵션 2: 동일한 API Management 인스턴스 이름 사용

> [!NOTE]
> 이 옵션을 선택하면 마이그레이션하는 동안 가동 중지 시간이 발생합니다.

1. 원본 지역의 API Management 인스턴스를 스토리지 계정에 백업합니다.
1. 원본 지역에서 API Management를 삭제합니다. 
1. 원본 지역의 이름과 동일한 이름을 사용하여 대상 지역에 새 API Management 인스턴스를 만듭니다.
1. 1단계에서 만든 백업을 대상 지역의 새 API Management 인스턴스로 복원합니다.  


## <a name="next-steps"></a><a name="next-steps"> </a>다음 단계
* 백업 및 복원 기능에 대한 자세한 내용은 [재해 복구를 구현하는 방법](api-management-howto-disaster-recovery-backup-restore.md)을 참조하세요.
* Azure 리소스 마이그레이션에 대한 자세한 내용은 [Azure 지역 간 마이그레이션 지침](https://github.com/Azure/Azure-Migration-Guidance)을 참조하세요.
* [클라우드 비용을 최적화하고 비용을 절약합니다](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
