---
title: Azure Site Recovery 서비스를 사용하여 Azure로 AWS VM 마이그레이션 | Microsoft Docs
description: 이 문서에서는 Azure Site Recovery를 사용하여 AWS(Amazon Web Services)에서 실행되는 Windows VM을 Azure로 마이그레이션하는 방법을 설명합니다.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 07/27/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: dd91e99b45405cca10b9ddc2982674e72ad6bf86
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87281296"
---
# <a name="migrate-amazon-web-services-aws-vms-to-azure"></a>AWS(Amazon Web Services)에서 Azure로 VM 마이그레이션

이 문서에서는 AWS(Amazon Web Services) 인스턴스를 Azure로 마이그레이션하는 옵션을 설명합니다.

## <a name="migrate-with-azure-migrate"></a>Azure Migrate로 마이그레이션

[Azure Migrate](../migrate/migrate-services-overview.md) 서비스를 사용하여 AWS 인스턴스를 Azure로 마이그레이션하는 것이 좋습니다. Azure Migrate는 Azure Migrate, 기타 Azure 서비스 및 타사 도구를 사용하여 온-프레미스 머신을 Azure로 평가하고 마이그레이션하기 위한 중앙 집중식 허브를 제공합니다.

Azure Migrate를 사용하여 AWS 인스턴스를 마이그레이션하는 [방법을 알아봅니다](../migrate/tutorial-migrate-aws-virtual-machines.md). 


## <a name="migrate-with-site-recovery"></a>Site Recovery로 마이그레이션

Site Recovery는 마이그레이션이 아닌 재해 복구에만 사용해야 합니다.

이미 Azure Site Recovery를 사용하고 있으며 AWS 마이그레이션에 계속 사용하려는 경우에는 [물리적 머신의 재해 복구](physical-azure-disaster-recovery.md)를 설정하는 데 사용하는 것과 동일한 단계를 수행합니다.


> [!NOTE]
> 재해 복구를 위해 장애 조치(failover)를 실행하는 경우 마지막 단계로 장애 조치(failover)를 커밋합니다. AWS 인스턴스를 마이그레이션하는 경우 **커밋** 옵션은 관련이 없습니다. 대신 **마이그레이션 완료** 옵션을 선택합니다. 

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> Azure Migrate에 대한 [일반적인 질문을 검토](../migrate/resources-faq.md)합니다.
