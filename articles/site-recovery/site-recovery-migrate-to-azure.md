---
title: "Site Recovery를 사용하여 Azure에 마이그레이션 | Microsoft Docs"
description: "이 문서에서는 Azure Site Recovery를 사용하여 Azure에 VM 및 물리적 서버를 마이그레이션하는 개요를 제공합니다."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: c413efcd-d750-4b22-b34b-15bcaa03934a
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/04/2017
ms.author: raynew
translationtype: Human Translation
ms.sourcegitcommit: 67b4861ac564565b2a36932ae15141a1e1f56035
ms.openlocfilehash: 2fb457166b4ebe61c11173aa75bdcd2fbce3f03d
ms.lasthandoff: 02/23/2017


---
# <a name="migrate-to-azure-with-site-recovery"></a>Site Recovery를 사용하여 Azure에 마이그레이션

가상 컴퓨터와 물리적 서버를 마이그레이션하기 위해 Azure Site Recovery 서비스를 사용하는 방법에 대한 개요는 이 문서를 참고하세요.

Site Recovery는 온-프레미스 물리적 서버와 가상 컴퓨터를 클라우드(Azure) 또는 보조 데이터 센터에 복제하는 작업을 오케스트레이션하여 BCDR(비즈니스 연속성 및 재해 복구) 전략에 기여하는 Azure 서비스입니다. 기본 위치에서 중단이 발생하면 보조 위치로 장애 조치하여 앱과 워크로드를 가용 상태로 유지합니다. 기본 위치가 정상 작업 상태로 돌아오면 다시 기본 위치로 돌아갑니다. [사이트 복구란?](site-recovery-overview.md) Site Recovery를 사용하여 기존 온-프레미스 워크로드를 Azure로 마이그레이션하여 클라우드 과정을 신속하게 처리하고 Azure에서 제공하는 다양한 기능을 사용할 수도 있습니다.

이 문서에서는 [Azure Portal](https://portal.azure.com)의 배포에 대해 설명합니다. [Azure 클래식 포털](https://manage.windowsazure.com/)은 기존 Site Recovery 자격 증명을 유지하는 데 사용할 수 있지만 새 자격 증명 모음을 만들 수는 없습니다.

문서의 하단에 의견을 남겨 주세요. 기술 관련 문의 사항은 [Azure 복구 서비스 포럼](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)에 문의하세요.


## <a name="what-do-we-mean-by-migration"></a>마이그레이션 기준은 어떤 의미인가요?

온-프레미스 VM 및 물리적 서버를 복제하기 위해 Azure 또는 보조 사이트에 Site Recovery를 배포할 수 있습니다. 컴퓨터를 복제하고 작동 중단이 발생한 경우 기본 사이트에서 장애 조치를 수행하며 복구되면 기본 사이트에 다시 장애 복구를 수행합니다. 이 외에도 Site Recovery를 사용하여 VM 및 물리적 서버를 Azure로 마이그레이션할 수 있으므로 사용자가 Azure VM으로 액세스할 수 있습니다. 마이그레이션을 수행하면 기본 사이트에서 Azure로의 복제 및 장애 조치와 완전한 마이그레이션 제스처가 수반됩니다.

## <a name="what-can-site-recovery-migrate"></a>Site Recovery로 무엇을 마이그레이션할 수 있나요?

다음을 수행할 수 있습니다.

- 온-프레미스 Hyper-V VM, VMware VM 및 물리적 서버에서 실행 중인 워크로드를 마이그레이션하여 Azure VM에서 실행할 수 있습니다. 이 시나리오에서는 전체 복제 및 장애 복구도 수행할 수 있습니다.
- Azure 지역 간에 [Azure IaaS VM](site-recovery-migrate-azure-to-azure.md)을 마이그레이션합니다. 이 시나리오에서는 현재 마이그레이션만을 지원하며 장애 복구는 지원되지 않습니다.
- [AWS Windows 인스턴스](site-recovery-migrate-aws-to-azure.md)를 Azure IaaS VM으로 마이그레이션합니다. 이 시나리오에서는 현재 마이그레이션만을 지원하며 장애 복구는 지원되지 않습니다.

## <a name="migrate-on-premises-vms-and-physical-servers"></a>온-프레미스 VM 및 물리적 서버를 마이그레이션합니다.

온-프레미스 Hyper-V VM, VMware VM 및 물리적 서버를 마이그레이션하려면 정기 복제에 사용되는 것과 거의 동일한 단계를 수행합니다. Recovery Services 자격 증명 모음을 설정하고 마이그레이션할 대상에 따라 필수 관리 서버를 구성하며 자격 증명 모음에 추가하고 복제 설정을 지정합니다. 마이그레이션하려는 컴퓨터에 복제를 사용하도록 설정하고 빠른 테스트 장애 조치를 실행하여 모두 예상대로 작동하는지 확인합니다.

복제 환경이 제대로 작동하는지 확인한 후에 시나리오의 [지원 기능](site-recovery-failover.md)에 따라 계획되거나 계획되지 않은 장애 조치를 사용합니다. 마이그레이션의 경우 장애 조치를 커밋할 필요가 없습니다. 대신 마이그레이션할 각 컴퓨터에 대해 **마이그레이션 완료** 옵션을 선택합니다. **마이그레이션 완료** 작업은 마이그레이션 프로세스를 마치고 가상 컴퓨터에 대한 복제를 제거하며 컴퓨터에 대한 Site Recovery 청구를 중지합니다.

![completemigration](./media/site-recovery-hyper-v-site-to-azure/migrate.png)

## <a name="migrate-between-azure-regions"></a>Azure 지역 간 마이그레이션

Site Recovery를 사용하여 지역 간에 Azure VM을 마이그레이션할 수 있습니다. 이 시나리오에서는 마이그레이션만 지원됩니다. 즉 Azure VM을 복제하고 다른 지역으로 장애 조치를 수행할 수 있지만 다시 장애 복구를 수행할 수는 없습니다. Recovery Services 자격 증명 모음을 설정하는 이 시나리오에서 온-프레미스 구성 서버를 배포하여 복제를 관리하고 자격 증명 모음에 추가하며 복제 설정을 지정합니다. 마이그레이션하려는 컴퓨터에 복제를 사용하도록 설정하고 빠른 테스트 장애 조치를 실행합니다. 그런 다음 **마이그레이션 완료** 옵션을 사용하여 계획되지 않은 장애 조치를 실행합니다.

## <a name="migrate-aws-to-azure"></a>Azure에 AWS 마이그레이션

Azure VM에 AWS 인스턴스 마이그레이션할 수 있습니다. 이 시나리오에서는 마이그레이션만 지원됩니다. 즉, AWS 인스턴스를 복제하고 Azure로 장애 조치를 수행할 수 있지만 다시 장애 복구를 수행할 수는 없습니다. AWS 인스턴스는 마이그레이션하기 위해 물리적 서버와 동일한 방법으로 처리됩니다. Recovery Services 자격 증명 모음을 설정하고 온-프레미스 구성 서버를 배포하여 복제를 관리하고 자격 증명 모음에 추가하며 복제 설정을 지정합니다. 마이그레이션하려는 컴퓨터에 복제를 사용하도록 설정하고 빠른 테스트 장애 조치를 실행합니다. 그런 다음 **마이그레이션 완료** 옵션을 사용하여 계획되지 않은 장애 조치를 실행합니다.




## <a name="next-steps"></a>다음 단계

- [Azure에 VMware VM 마이그레이션](site-recovery-vmware-to-azure.md)
- [Azure에 VMM 클라우드의 Hyper-V VM 마이그레이션](site-recovery-vmm-to-azure.md)
- [Azure에 Hyper-V VM(VMM 없음) 마이그레이션](site-recovery-hyper-v-site-to-azure.md)
- [Azure 지역 간에 Azure VM 마이그레이션](site-recovery-migrate-azure-to-azure.md)
- [Azure에 AWS 인스턴스 마이그레이션](site-recovery-migrate-aws-to-azure.md)

