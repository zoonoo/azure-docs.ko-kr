---
title: "Site Recovery를 사용하여 Azure에 온-프레미스 VM 및 물리적 서버 마이그레이션 | Microsoft Docs"
description: "이 문서에서는 Azure Site Recovery를 사용하여 온-프레미스 VM과 물리적 서버를 Azure로 마이그레이션하는 방법을 설명합니다."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: c413efcd-d750-4b22-b34b-15bcaa03934a
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/30/2017
ms.author: raynew
ms.openlocfilehash: 423a1727efb0e1fd54eb0f8d5971ace3f8efc6cb
ms.sourcegitcommit: 43c3d0d61c008195a0177ec56bf0795dc103b8fa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/01/2017
---
# <a name="migrate-to-azure-with-site-recovery"></a>Site Recovery를 사용하여 Azure에 마이그레이션

이 문서를 참조하여 [Azure Site Recovery](site-recovery-overview.md) 서비스를 통해 온-프레미스 VM(가상 컴퓨터) 및 물리적 서버를 Azure VM으로 마이그레이션하는 방법에 대해 알아봅니다.

## <a name="before-you-start"></a>시작하기 전에

Azure로 마이그레이션하는 데 필요한 단계에 대해 간략히 설명하는 다음 비디오를 시청합니다.
>[!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/ASRHowTo-Video2-Migrate-Virtual-Machines-to-Azure/player]


## <a name="what-do-we-mean-by-migration"></a>마이그레이션 기준은 어떤 의미인가요?

Site Recovery를 온-프레미스 복제 VM 및 물리적 서버에 배포하고 마이그레이션할 수 있습니다.

- 복제하는 경우 온-프레미스 컴퓨터를 정기적으로 Azure에 복제하도록 구성합니다. 그런 다음 중단이 발생하면 컴퓨터를 온-프레미스 사이트에서 Azure로 장애 조치하고 여기에서 액세스합니다. 온-프레미스 사이트를 다시 사용할 수 있게 되면 Azure에서 장애 복구합니다.
- 마이그레이션에 Site Recovery를 사용하는 경우 온-프레미스 컴퓨터를 Azure로 복제합니다. 그런 다음 온-프레미스 사이트에서 Azure로 장애 조치하고 마이그레이션 프로세스를 완료합니다. 장애 복구는 필요하지 않습니다.  

## <a name="what-can-site-recovery-migrate"></a>Site Recovery로 무엇을 마이그레이션할 수 있나요?

다음을 수행할 수 있습니다.

- **온-프레미스에서 마이그레이션**: 온 프레미스 Hyper-V VM, VMware VM 및 물리적 서버를 Azure로 마이그레이션합니다. 마이그레이션한 후에는 온-프레미스 컴퓨터에서 실행되는 워크로드가 Azure VM에서 실행됩니다. 
- **Azure 내에서 마이그레이션**: Azure 지역 간에 Azure VM을 마이그레이션합니다. 
- **AWS 마이그레이션**: AWS Windows 인스턴스를 Azure IaaS VM으로 마이그레이션합니다. 

## <a name="migrate-from-on-premises-to-azure"></a>온-프레미스에서 Azure로 마이그레이션

온-프레미스 VMware VM, Hyper-V VM 및 물리적 서버를 마이그레이션하려면 전체 복제와 거의 동일한 단계를 수행합니다. 


## <a name="migrate-between-azure-regions"></a>Azure 지역 간 마이그레이션

지역 간에 Azure VM을 마이그레이션하려면 전체 마이그레이션과 거의 동일한 단계를 수행합니다.

1. 마이그레이션하려는 컴퓨터에 대해 [복제를 사용하도록 설정](azure-to-azure-tutorial-enable-replication.md)합니다.
2. [빠른 테스트 장애 조치를 실행](azure-to-azure-tutorial-dr-drill.md)하여 모든 것이 제대로 작동하는지 확인합니다.
3. 그런 다음 **마이그레이션 완료** 옵션을 사용하여 [계획되지 않은 장애 조치를 실행](azure-to-azure-tutorial-failover-failback.md)합니다.
4. 마이그레이션이 완료되면 마이그레이션한 Azure 지역에서 보조 지역으로 [재해 복구하기 위한 복제를 설정](site-recovery-azure-to-azure-after-migration.md)할 수 있습니다.



## <a name="migrate-aws-to-azure"></a>Azure에 AWS 마이그레이션

Azure VM에 AWS 인스턴스 마이그레이션할 수 있습니다.
- 이 시나리오에서는 마이그레이션만 지원됩니다. 즉, AWS 인스턴스를 복제하고 Azure로 장애 조치를 수행할 수 있지만 다시 장애 복구를 수행할 수는 없습니다.
- AWS 인스턴스는 마이그레이션하기 위해 물리적 서버와 동일한 방법으로 처리됩니다. Recovery Services 자격 증명 모음을 설정하고 온-프레미스 구성 서버를 배포하여 복제를 관리하고 자격 증명 모음에 추가하며 복제 설정을 지정합니다.
- 마이그레이션하려는 컴퓨터에 복제를 사용하도록 설정하고 빠른 테스트 장애 조치를 실행합니다. 그런 다음 **마이그레이션 완료** 옵션을 사용하여 계획되지 않은 장애 조치를 실행합니다.






## <a name="next-steps"></a>다음 단계

- [Azure로 온-프레미스 컴퓨터 마이그레이션](tutorial-migrate-on-premises-to-azure.md)
- [Azure 지역 간 VM 마이그레이션](site-recovery-migrate-azure-to-azure.md)
- [Azure에 AWS 마이그레이션](tutorial-migrate-aws-to-azure.md)
