---
title: "VM을 AWS에서 Azure로 마이그레이션| Microsoft Docs"
description: "이 문서에서는 Azure Site Recovery를 사용하여 AWS(Amazon Web Services)를 실행하는 가상 컴퓨터를 Azure로 마이그레이션하는 방법을 설명합니다."
services: site-recovery
documentationcenter: 
author: bsiva
manager: jwhit
editor: 
ms.assetid: ddb412fd-32a8-4afa-9e39-738b11b91118
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: backup-recovery
ms.date: 02/12/2017
ms.author: bsiva
translationtype: Human Translation
ms.sourcegitcommit: 5031f64ffcd34b6287a3ecd87dd027c2bc7c716f
ms.openlocfilehash: 9d0d0ba4ca5966b39ce62ea8296d48e5930c9782


---
# <a name="migrate-virtual-machines-in-amazon-web-services-aws-to-azure-with-azure-site-recovery"></a>Azure Site Recovery를 사용하여 AWS(Amazon Web Services)의 가상 컴퓨터를 Azure로 마이그레이션

이 문서에서는 [Azure Site Recovery](site-recovery-overview.md) 서비스를 사용하여 AWS Windows 인스턴스를 Azure Virtual Machines로 마이그레이션하는 방법을 설명합니다.

마이그레이션은 AWS에서 Azure로 발생하는 효율적인 장애 조치입니다. 컴퓨터를 장애 복구할 수 없고 진행 중인 복제도 없습니다. 이 문서에서는 Azure Portal에서 마이그레이션하기 위한 단계를 설명하고 [Azure에 물리적 컴퓨터를 복제](site-recovery-vmware-to-azure.md)하는 지침을 기준으로 합니다.

이 문서의 하단 또는 [Azure 복구 서비스 포럼](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)

## <a name="supported-operating-systems"></a>지원되는 운영 체제

Site Recovery는 다음 운영 체제 중 하나를 실행하는 EC2 인스턴스를 마이그레이션하는 데 사용될 수 있습니다.

- Windows(64비트만 해당)
    - Windows Server 2008 R2 SP1+(Citrix PV 드라이버 또는 AWS PV 드라이버만 해당. **RedHat PV 드라이버를 실행하는 인스턴스가 지원되지 않습니다**)Windows Server 2012 Windows Server 2012 R2
- Linux(64비트만 해당)
    - Red Hat Enterprise Linux 6.7(HVM 가상화된 인스턴스만 해당)

## <a name="prerequisites"></a>필수 조건

이 배포에 대해 필요한 사항은 다음과 같습니다.

* **구성 서버**: 구성 서버 역할로 배포된 Windows Server 2012 R2를 실행 중인 온-프레미스 VM입니다. 기본적으로 다른 Site Recovery 구성 요소(프로세스 서버와 마스터 대상 서버)는 구성 서버를 배포할 때 설치됩니다. [자세히 알아보기](site-recovery-components.md#replicate-vmware-vmsphysical-servers-to-azure)
* **EC2 인스턴스**: 마이그레이션하려는 EC2 가상 컴퓨터 인스턴스입니다.

## <a name="deployment-steps"></a>배포 단계

1. 자격 증명 모음 만들기
2. 구성 서버 배포
3. 배포한 후에 구성 서버가 마이그레이션할 VM과 통신할 수 있는지 유효성을 검사합니다.
4. 복제 설정을 지정합니다.
5. 모바일 서비스를 설치합니다. 보호하려는 각 VM은 모바일 서비스가 설치되어야 합니다. 이 서비스는 프로세스 서버에 데이터를 보냅니다. 모바일 서비스를 수동으로 설치 또는 푸시하고 VM에 대한 보호를 사용하는 경우 프로세스 서버에서 자동으로 설치합니다. EC2 인스턴스에서 마이그레이션할 방화벽 규칙을 구성하여 이 서비스의 강제 설치를 허용해야 합니다. EC2 인스턴스에 대한 보안 그룹에는 다음과 같은 규칙이 있어야 합니다.

    ![방화벽 규칙](./media/site-recovery-migrate-aws-to-azure/migrate-firewall.png)
6. 복제를 활성화합니다. 마이그레이션할 VM에 대해 복제를 사용하도록 설정합니다. EC2 콘솔에서 얻을 수 있는 개인 IP 주소를 사용하여 EC2 인스턴스를 검색할 수 있습니다.
7. 계획되지 않은 장애 조치(failover)를 실행합니다. 초기 복제가 완료된 후 각 VM에 대해 AWS에서 Azure로 계획되지 않은 장애 조치를 실행할 수 있습니다. 선택적으로 복구 계획을 만들고 장애 조치를 실행하여 AWS에서 Azure에 여러 가상 컴퓨터를 마이그레이션할 수 있습니다. [자세히 알아봅니다](site-recovery-create-recovery-plans.md) .

[배포 단계](site-recovery-vmware-to-azure.md)에 대한 자세한 지침 및 [계획되지 않은 장애 조치](site-recovery-failover.md#run-an-unplanned-failover)를 실행하는 지침을 가져옵니다.



<!--HONumber=Feb17_HO2-->


