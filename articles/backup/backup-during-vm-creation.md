---
title: Azure VM을 만드는 동안 백업 사용
description: Azure 가상 머신 백업 생성 프로세스 동안 사용 하도록 설정 하는 방법.
services: backup, virtual-machines
author: rayne-wiselman
manager: carmonm
tags: azure-resource-manager, virtual-machine-backup
ms.service: backup
ms.topic: conceptual
ms.date: 01/08/2018
ms.author: trinadhk
ms.openlocfilehash: fd2beaa39f03d4f2342c94bf1cd8b8aea7440e62
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/12/2019
ms.locfileid: "57780446"
---
# <a name="enable-backup-when-you-create-an-azure-virtual-machine"></a>Azure 가상 머신을 만들 때 백업을 사용 하도록 설정

Azure virtual machines (Vm)를 백업 하기 위해 Azure Backup 서비스를 사용 합니다. 백업 정책에 지정 된 일정에 따라 Vm 백업 및 백업에서 복구 지점이 생성 됩니다. 복구 지점은 Recovery Services 자격 증명 모음에 저장 됩니다.

이 문서에서는 Azure portal에서 가상 머신 (VM)를 만드는 동안 백업을 사용 하도록 설정 하는 방법을 자세히 설명 합니다.  

## <a name="sign-in-to-azure"></a>Azure에 로그인

계정에 로그인 이미 하지 않은 경우에 로그인 합니다 [Azure portal](https://portal.azure.com)합니다.
 
## <a name="create-a-vm-with-backup-configured"></a>백업이 구성 된 VM 만들기 

1. Azure portal의 왼쪽 위 모퉁이에서 선택 **새로 만들기**합니다.

1. 선택 **계산**, 한 다음 VM의 이미지를 선택 합니다.

1. VM에 대 한 정보를 입력 합니다. 사용자 이름 및 암호는 VM에 로그인 됩니다. 이 과정을 완료 하는 경우 선택 **확인**합니다. 

1. VM의 크기를 선택합니다.  

1. 아래 **설정을** > **백업**선택 **Enabled** 하 여 백업 구성 설정을 엽니다.

   - 선택의 기본 설정인 **확인** 에 **설정** 페이지. 다음 이동 합니다 **요약** 페이지 VM을 만듭니다. 6-8 단계를 건너뜁니다.
   - 백업 구성 값을 변경 하려면 다음 단계를 수행 합니다.  

1. 만들거나 VM의 백업을 보유 하는 Recovery Services 자격 증명 모음을 선택 합니다. Recovery Services 자격 증명 모음을 만드는 경우 자격 증명 모음에 대 한 리소스 그룹을 선택할 수 있습니다.  

    ![VM 만들기 페이지의 백업 구성 설정](./media/backup-during-vm-creation/create-vm-backup-config.png) 

    > [!NOTE] 
    > Recovery Services 자격 증명 모음에 대 한 리소스 그룹을 VM에 대 한 리소스 그룹과 다를 수 있습니다.  

1. 기본적으로 VM을 보호 하는 데에 대 한 백업 정책을 선택 됩니다. 백업 정책은 백업 스냅숏을 작성 하는 빈도 지정 하 고 해당 백업 복사본을 유지할 기간입니다. 

   - 기본 정책을 그대로 사용하거나 다른 백업 정책을 만들거나 선택할 수 있습니다. 
   - 백업 정책을 편집 하려면 선택 **Backup 정책** 값을 변경 합니다.  

1. 값을 설정 하면 백업 구성이 완료 되 면, 선택 **확인** 에 **설정** 페이지입니다.  

1. 에 **요약** 유효성 검사를 통과 한 후 페이지 선택 **만들기** 구성된 된 백업 설정을 사용 하는 VM을 만듭니다. 

## <a name="start-a-backup-after-creating-the-vm"></a>VM을 만든 후 백업 시작 

VM에 대 한 백업 정책을 구성한 경우에 초기 백업을 만드는 데는 것이 좋습니다. 

VM 만들기 템플릿이 완료 되 면 후 이동할 **Operations** 왼쪽된 메뉴에서 선택 **백업** 가상 머신에 대 한 백업 세부 정보를 보려면. 이 페이지를 사용할 수 있습니다.

- 요청 시 백업을 트리거하십시오.
- 전체 VM 또는 모든 디스크를 복원 합니다.
- VM 백업에서 파일을 복원 합니다.
- VM에 연결 된 백업 정책을 변경 합니다.  

## <a name="use-a-resource-manager-template-to-deploy-a-protected-vm"></a>Resource Manager 템플릿을 사용 하 여 보호 된 VM 배포

이전 단계는 가상 머신을 만들고 Recovery Services 자격 증명 모음에서 보호에 Azure portal을 사용 하는 방법에 설명 합니다. 하나 이상의 가상 머신을 배포 하 고 Recovery Services 자격 증명 모음에서 보호할 신속 하 게, 참조 템플릿을 [Windows VM을 배포 하 고 백업을 사용 하도록 설정](https://azure.microsoft.com/resources/templates/101-recovery-services-create-vm-and-configure-backup/)합니다.

## <a name="frequently-asked-questions"></a>질문과 대답 

### <a name="which-vm-images-support-backup-configuration-during-vm-creation"></a>어떤 VM 이미지가 VM 생성 도중 백업 구성을 지원 하나요?

Microsoft에서 게시 한 다음 core 이미지는 VM 만드는 동안 백업을 사용 하도록 설정 하는 것에 대 한 지원 됩니다. 다른 Vm에 대 한 VM을 만든 후 백업 작업을 사용할 수 있습니다. 자세한 내용은 참조 하세요 [VM을 만든 후 백업 사용](quick-backup-vm-portal.md)합니다.

- **Windows** -Windows Server 2016 Datacenter, Windows Server 2016 Datacenter Core, Windows Server 2012 Datacenter, Windows Server 2012 R2 Datacenter, Windows Server 2008 R2 SP1 
- **Ubuntu** -Ubuntu Server 17.10, Ubuntu Server 17.04, Ubuntu Server 16.04 LTS (), Ubuntu Server 14.04 (LTS) 
- **Red Hat** - RHEL 6.7, 6.8, 6.9, 7.2, 7.3, 7.4 
- **SUSE** - SUSE Linux Enterprise Server 11 SP4, 12 SP2, 12 SP3 
- **Debian** - Debian 8, Debian 9 
- **CentOS** - CentOS 6.9, CentOS 7.3 
- **Oracle Linux** - Oracle Linux 6.7, 6.8, 6.9, 7.2, 7.3 
 
### <a name="is-the-backup-cost-included-in-the-vm-cost"></a>VM 비용에 포함 된 백업 비용은 인가요? 

아니요. 백업 비용은 VM의 비용과 별개입니다. 백업 가격 책정에 대 한 자세한 내용은 참조 하세요. [Azure Backup 가격 책정](https://azure.microsoft.com/pricing/details/backup/)합니다.
 
### <a name="which-permissions-are-required-to-enable-backup-on-a-vm"></a>VM에서 백업을 사용하도록 설정하는 데 필요한 권한은 무엇인가요? 

VM 참가자 인 경우 VM에서 백업을 사용할 수 있습니다. 사용자 지정 역할을 사용 하는 경우 VM에서 백업을 사용 하도록 설정 하려면 다음 권한이 필요 합니다. 

- Microsoft.RecoveryServices/Vaults/write 
- Microsoft.RecoveryServices/Vaults/read 
- Microsoft.RecoveryServices/locations/* 
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/*/read 
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read 
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write 
- Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write 
- Microsoft.RecoveryServices/Vaults/backupPolicies/read 
- Microsoft.RecoveryServices/Vaults/backupPolicies/write 
 
Recovery Services 자격 증명 모음 및 VM에 다른 리소스 그룹이 있는 경우에 Recovery Services 자격 증명 모음에 대 한 리소스 그룹에 대 한 쓰기 권한이 있는지 확인 합니다.  

## <a name="next-steps"></a>다음 단계 

이제 VM을 보호 했습니다 보면 관리 하 고 Vm을 복원 하는 방법을 알아보려면 다음 문서:

- [가상 머신 관리 및 모니터링](backup-azure-manage-vms.md) 
- [가상 머신 복원](backup-azure-arm-restore-vms.md) 
