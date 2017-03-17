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
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: c01805f797151f8970e1dcd2fdc58a8634fb0083
ms.lasthandoff: 03/15/2017


---
# <a name="migrate-virtual-machines-in-amazon-web-services-aws-to-azure-with-azure-site-recovery"></a>Azure Site Recovery를 사용하여 AWS(Amazon Web Services)의 가상 컴퓨터를 Azure로 마이그레이션

이 문서에서는 [Azure Site Recovery](site-recovery-overview.md) 서비스를 사용하여 AWS Windows 인스턴스를 Azure Virtual Machines로 마이그레이션하는 방법을 설명합니다.

마이그레이션은 AWS에서 Azure로 발생하는 효율적인 장애 조치입니다. 컴퓨터를 AWS로 장애 복구할 수 없고 진행 중인 복제도 없습니다. 이 문서에서는 Azure Portal에서 마이그레이션하기 위한 단계를 설명하고 [Azure에 물리적 컴퓨터를 복제](site-recovery-vmware-to-azure.md)하는 지침을 기준으로 합니다.

이 문서의 하단 또는 [Azure 복구 서비스 포럼](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)

## <a name="supported-operating-systems"></a>지원되는 운영 체제

Site Recovery는 다음 운영 체제 중 하나를 실행하는 EC2 인스턴스를 마이그레이션하는 데 사용될 수 있습니다.

- Windows(64비트만 해당)
    - Windows Server 2008 R2 SP1+(Citrix PV 드라이버 또는 AWS PV 드라이버만 해당. **RedHat PV 드라이버를 실행하는 인스턴스가 지원되지 않습니다**)Windows Server 2012 Windows Server 2012 R2
- Linux(64비트만 해당)
    - Red Hat Enterprise Linux 6.7(HVM 가상화된 인스턴스만 해당)

## <a name="prerequisites"></a>필수 조건

이 배포에 대해 필요한 사항은 다음과 같습니다.

* **구성 서버**: 구성 서버 역할로 배포된 Windows Server 2012 R2를 실행 중인 Amazon EC2 VM입니다. 기본적으로 다른 Azure Site Recovery 구성 요소(프로세스 서버와 마스터 대상 서버)는 구성 서버를 배포할 때 설치됩니다. 이 문서에서는 Azure Portal에서 마이그레이션하기 위한 단계를 설명하고 [자세한 정보](site-recovery-components.md#vmware-to-azure)의 지침을 기준으로 합니다.

* **EC2 인스턴스**: 마이그레이션하려는 Amazon EC2 가상 컴퓨터 인스턴스입니다.

## <a name="deployment-steps"></a>배포 단계

1. 복구 서비스 자격 증명 모음 만들기

2. EC2 인스턴스에 대한 보안 그룹에는 다음과 같이 구성된 규칙이 있어야 합니다. ![규칙](./media/site-recovery-migrate-aws-to-azure/migration_pic1.png)

3. EC2 인스턴스와 동일한 Amazon 가상 사설 클라우드에서 ASR 구성 서버를 배포합니다. 구성 서버 배포 요구 사항의 Azure 필수 구성 요소에 대한 VMware / 물리적 참조 ![DeployCS](./media/site-recovery-migrate-aws-to-azure/migration_pic2.png)

4.    구성 서버가 AWS에 배포되고 Recovery Services 자격 증명 모음으로 등록되면 아래와 같이 Site Recovery 인프라 아래에서 구성 서버 및 프로세스 서버가 표시됩니다. ![CSinVault](./media/site-recovery-migrate-aws-to-azure/migration_pic3.png)
  >[!NOTE]
  >구성 서버 및 프로세스 서버가 표시되는 데 최대 15분까지 걸릴 수 있습니다.
  >

5. 배포한 후에 구성 서버가 마이그레이션할 VM과 통신할 수 있는지 유효성을 검사합니다.

6. [복제 설정 지정](site-recovery-setup-replication-settings-vmware.md)

7. 복제 사용: 마이그레이션할 VM에 대해 복제를 사용하도록 설정합니다. EC2 콘솔에서 얻을 수 있는 개인 IP 주소를 사용하여 EC2 인스턴스를 검색할 수 있습니다.
![SelectVM](./media/site-recovery-migrate-aws-to-azure/migration_pic4.png)
8. EC2 인스턴스가 보호되고 Azure에 복제가 완료되면 [테스트 장애 조치를 실행](site-recovery-test-failover-to-azure.md)하여 Azure에서 응용 프로그램의 성능을 검사합니다. ![TFI](./media/site-recovery-migrate-aws-to-azure/migration_pic5.png)

9. 각 VM에 대해 AWS에서 Azure로 장애 조치를 실행합니다. 선택적으로 복구 계획을 만들고 장애 조치를 실행하여 AWS에서 Azure에 여러 가상 컴퓨터를 마이그레이션할 수 있습니다. [자세히 알아봅니다](site-recovery-create-recovery-plans.md) .

10. 마이그레이션의 경우 장애 조치를 커밋할 필요가 없습니다. 대신 마이그레이션할 각 컴퓨터에 대해 마이그레이션 완료 옵션을 선택합니다. 마이그레이션 완료 작업은 마이그레이션 프로세스를 마치고 가상 컴퓨터에 대한 복제를 제거하며 컴퓨터에 대한 Site Recovery 청구를 중지합니다.![마이그레이션](./media/site-recovery-migrate-aws-to-azure/migration_pic6.png)

