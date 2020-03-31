---
title: 여러 리전간에 Azure API 관리를 마이그레이션하는 방법
description: API 관리 인스턴스를 한 리전에서 다른 리전으로 마이그레이션하는 방법을 알아봅니다.
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
ms.openlocfilehash: 3294a7b2112e9527041ef343f4452aedb7a2a272
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "70073475"
---
# <a name="how-to-migrate-azure-api-management-across-regions"></a>여러 리전간에 Azure API 관리를 마이그레이션하는 방법
한 Azure 리전에서 다른 Azure 리전으로 API 관리 인스턴스를 마이그레이션하려면 [백업 및 복원](api-management-howto-disaster-recovery-backup-restore.md) 기능을 사용할 수 있습니다. 원본 및 대상 리전에서 동일한 API 관리 가격 책정 계층을 선택해야 합니다. 

> [!NOTE]
> 다른 클라우드 유형 간에 마이그레이션하는 동안에는 백업 및 복원이 작동하지 않습니다. 이를 위해 리소스를 [템플릿으로](https://docs.microsoft.com/azure/azure-resource-manager/manage-resource-groups-portal#export-resource-groups-to-templates)내보내야 합니다. 그런 다음 내보낸 템플릿을 대상 Azure 영역에 맞게 조정하고 리소스를 다시 만듭니다. 

## <a name="option-1-use-a-different-api-management-instance-name"></a>옵션 1: 다른 API 관리 인스턴스 이름 사용

1. 대상 리전에서 소스 API 관리 인스턴스와 동일한 가격 책정 계층을 가진 새 API 관리 인스턴스를 만듭니다. 새 인스턴스의 이름이 달라야 합니다. 
1. 저장소 계정에 기존 API 관리 인스턴스를 백업합니다.
1. 2단계에서 만든 백업을 1단계의 새 리전에서 만든 새 API 관리 인스턴스로 복원합니다.
1. 원본 지역 API 관리 인스턴스를 가리키는 사용자 지정 도메인이 있는 경우 새 API 관리 인스턴스를 가리키도록 사용자 지정 도메인 CNAME을 업데이트합니다. 


## <a name="option-2-use-the-same-api-management-instance-name"></a>옵션 2: 동일한 API 관리 인스턴스 이름 사용

> [!NOTE]
> 이 옵션을 사용하면 마이그레이션 중에 가동 중지 시간이 발생합니다.

1. 원본 지역의 API 관리 인스턴스를 저장소 계정에 백업합니다.
1. 원본 리전에서 API 관리를 삭제합니다. 
1. 원본 리전의 이름과 이름이 같은 대상 지역에 새 API 관리 인스턴스를 만듭니다.
1. 1단계에서 생성된 백업을 대상 지역의 새 API 관리 인스턴스로 복원합니다.  


## <a name="next-steps"></a><a name="next-steps"> </a>다음 단계
* 백업 및 복원 기능에 대한 자세한 내용은 [재해 복구를 구현하는 방법을](api-management-howto-disaster-recovery-backup-restore.md)참조하세요.
* Azure 리소스 마이그레이션에 대한 자세한 내용은 [Azure 지역 간 마이그레이션 지침을](https://github.com/Azure/Azure-Migration-Guidance)참조하십시오.