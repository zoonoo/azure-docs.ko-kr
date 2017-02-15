---
title: "Site Recovery를 사용하여 Amazon Web Services의 가상 컴퓨터를 Azure로 마이그레이션 | Microsoft Docs"
description: "이 문서에서는 Azure Site Recovery를 사용하여 AWS(Amazon Web Services)를 실행하는 가상 컴퓨터를 Azure로 마이그레이션하는 방법을 설명합니다."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: ddb412fd-32a8-4afa-9e39-738b11b91118
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: backup-recovery
ms.date: 11/01/2016
ms.author: raynew
translationtype: Human Translation
ms.sourcegitcommit: 5614c39d914d5ae6fde2de9c0d9941e7b93fc10f
ms.openlocfilehash: c7238d677b315390bc5f53e54ab7dbded2871c5a


---
# <a name="migrate-virtual-machines-in-amazon-web-services-aws-to-azure-with-azure-site-recovery"></a>Azure Site Recovery를 사용하여 AWS(Amazon Web Services)의 가상 컴퓨터를 Azure로 마이그레이션
## <a name="overview"></a>개요
Azure Site Recovery에 오신 것을 환영합니다. 이 문서에서는 Site Recovery를 사용하여 AWS에서 실행 중인 EC2 인스턴스를 Azure로 마이그레이션합니다. 시작하기 전에 다음 사항에 주의하세요.

* Azure에는 리소스를 만들고 작업하기 위한 두 가지 배포 모델인 Azure Resource Manager 배포 모델과 클래식 배포 모델이 있습니다. 또한 Azure에는 두 가지 포털이 있는데, 하나는 클래식 배포 모델을 지원하는 Azure 클래식 포털이고 다른 하나는 두 가지 배포 모델을 모두 지원하는 Azure 포털입니다. Resource Manager 또는 클래식 중 어느 곳에서 Site Recovery를 구성하든 마이그레이션의 기본 단계는 동일합니다. 그러나 이 문서의 UI 지침과 스크린샷은 Azure 포털과 관련된 것입니다.
* **현재 AWS에서 Azure로만 마이그레이션할 수 있습니다. AWS에서 Azure로 장애 조치(failover)할 수 있으나 다시 장애 복구(failback)할 수 없습니다. 진행 중인 복제가 없습니다.**
* 이 문서의 마이그레이션 지침은 물리적 컴퓨터를 Azure에 복제하기 위한 지침을 기준으로 합니다. 여기에는 Azure 포털의 물리적 서버 복제 방법을 설명하는 [VMware VM 또는 물리적 서버를 Azure로 복제](site-recovery-vmware-to-azure.md)에 나오는 단계에 대한 링크가 포함됩니다.
* 클래식 포털에서 Site Recovery를 설정하는 경우 [이 문서](site-recovery-vmware-to-azure-classic.md)의 자세한 지침을 따르세요. **레거시 문서** 의 지침은 [더 이상 사용하지 마세요](site-recovery-vmware-to-azure-classic-legacy.md)의 자세한 지침을 따르세요.

이 문서의 하단 또는 [Azure 복구 서비스 포럼](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)

## <a name="supported-operating-systems"></a>지원되는 운영 체제
Site Recovery는 다음 운영 체제 중 하나를 실행하는 EC2 인스턴스를 마이그레이션하는 데 사용할 수 있습니다.

### <a name="windows64-bit-only"></a>Windows(64비트만 해당)
* Windows Server 2008 R2 SP1+(Citrix PV 드라이버 또는 AWS PV 드라이버만 해당, **RedHat PV 드라이버를 실행하는 인스턴스는 지원되지 않음**)
* Windows Server 2012
* Windows Server 2012 R2

### <a name="linux-64-bit-only"></a>Linux(64비트만 해당)
* Red Hat Enterprise Linux 6.7(HVM 가상화된 인스턴스만 해당)

## <a name="prerequisites"></a>필수 조건
이 배포에 대해 필요한 사항은 다음과 같습니다.

* **구성 서버**: 구성 서버 역할을 하는 Windows Server 2012 R2를 실행 중인 온-프레미스 VM입니다. 이 VM에 다른 Site Recovery 구성 요소(프로세스 서버 및 마스터 대상 서버 포함)도 설치합니다. 자세한 내용은 [시나리오 아키텍처](site-recovery-vmware-to-azure.md#scenario-architecture) 및 [구성 서버 필수 구성 요소](site-recovery-vmware-to-azure.md#configuration-server-or-additional-process-server-prerequisites)를 참조하세요.
* **EC2 VM 인스턴스**: 마이그레이션하려는 EC2 인스턴스입니다.

## <a name="deployment-steps"></a>배포 단계
이 섹션에서는 새 Azure 포털의 배포 단계를 설명합니다. 클래식 포털에서 Site Recovery에 대해 이러한 배포 단계가 필요한 경우 [이 문서](site-recovery-vmware-to-azure-classic.md)를 참조하세요.

1. [자격 증명 모음을 만듭니다](site-recovery-vmware-to-azure.md#create-a-recovery-services-vault).
2. [구성 서버를 배포합니다](site-recovery-vmware-to-azure.md#step-2-set-up-the-source-environment).
3. 배포한 후에 구성 서버가 마이그레이션할 VM과 통신할 수 있는지 유효성을 검사합니다.
4. [복제 설정을 지정합니다](site-recovery-vmware-to-azure.md#step-4-set-up-replication-settings). 복제 정책을 만들고 구성 서버에 할당합니다.
5. [모바일 서비스를 설치합니다](site-recovery-vmware-to-azure.md#step-6-replicate-applications). 보호하려는 각 VM은 모바일 서비스가 설치되어야 합니다. 이 서비스는 프로세스 서버에 데이터를 보냅니다. 모바일 서비스를 수동으로 설치 또는 푸시하고 VM에 대한 보호를 사용하는 경우 프로세스 서버에서 자동으로 설치합니다. EC2 인스턴스에서 마이그레이션할 방화벽 규칙을 구성하여 이 서비스의 강제 설치를 허용해야 합니다. EC2 인스턴스에 대한 보안 그룹에는 다음과 같은 규칙이 있어야 합니다.

    ![방화벽 규칙](./media/site-recovery-migrate-aws-to-azure/migrate-firewall.png)
6. [복제를 활성화합니다](site-recovery-vmware-to-azure.md#enable-replication). 마이그레이션할 VM에 대해 복제를 사용하도록 설정합니다. EC2 콘솔에서 얻을 수 있는 개인 IP 주소를 사용하여 EC2 인스턴스를 검색할 수 있습니다.
7. [ 계획되지 않은 장애 조치를 실행합니다](site-recovery-failover.md#run-an-unplanned-failover). 초기 복제가 완료된 후 각 VM에 대해 AWS에서 Azure로 계획되지 않은 장애 조치를 실행할 수 있습니다. 선택적으로 복구 계획을 만들고 장애 조치를 실행하여 AWS에서 Azure에 여러 가상 컴퓨터를 마이그레이션할 수 있습니다. [자세히 알아봅니다](site-recovery-create-recovery-plans.md) .

## <a name="next-steps"></a>다음 단계
 [Azure Site Recovery란?](site-recovery-overview.md)



<!--HONumber=Nov16_HO3-->


