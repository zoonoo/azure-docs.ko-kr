---
title: Active Directory 백업 및 복원
description: Active Directory 도메인 컨트롤러를 백업하고 복원하는 방법을 알아봅니다.
ms.topic: conceptual
ms.date: 07/08/2020
ms.openlocfilehash: 8bc6458895965d4c37667e0cff3051a4e4e8288e
ms.sourcegitcommit: 19dcad80aa7df4d288d40dc28cb0a5157b401ac4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107898210"
---
# <a name="back-up-and-restore-active-directory-domain-controllers"></a>Active Directory 도메인 컨트롤러 백업 및 복원

Active Directory를 백업하고 손상, 손상 또는 재해 발생 시 성공적인 복원을 보장하는 것은 Active Directory 유지 관리의 중요한 부분입니다.

이 문서에서는 Azure 가상 머신이든 온-프레미스 서버든 상관 없이 Azure Backup을 사용하여 Active Directory 도메인 컨트롤러를 백업 및 복원하는 적절한 절차를 설명합니다. 전체 도메인 컨트롤러를 백업 시 상태로 복원해야 하는 시나리오에 대해 설명합니다. 적절한 복원 시나리오를 확인하려면 [이 문서](/windows-server/identity/ad-ds/manage/ad-forest-recovery-determine-how-to-recover)를 참조하세요.  

>[!NOTE]
> 이 문서에서는 [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md)에서 항목을 복원하는 방법에 대해서는 설명하지 않습니다. Azure Active Directory 사용자 복원에 대한 자세한 내용은 [이 문서](../active-directory/fundamentals/active-directory-users-restore.md)를 참조하세요.

## <a name="best-practices"></a>모범 사례

- 도메인 컨트롤러를 하나 이상 백업했는지 확인합니다. 둘 이상의 도메인 컨트롤러를 백업하는 경우 [FSMO(신축 단일 마스터 작업) 역할](/windows-server/identity/ad-ds/plan/planning-operations-master-role-placement)을 포함하는 모든 컨트롤러가 백업되었는지 확인해야 합니다.

- Active Directory를 자주 백업합니다. TSL(삭제 표시 수명)보다 오래된 개체는 “삭제 표시”되고 더 이상 유효한 것으로 간주되지 않으므로 백업 수명은 항상 TSL보다 작아야 합니다.
  - Windows Server 2003 SP2 이상에서 빌드된 도메인의 기본 TSL은 180일입니다.
  - 다음 PowerShell 스크립트를 사용하여 구성된 TSL을 확인할 수 있습니다.

    ```powershell
    (Get-ADObject $('CN=Directory Service,CN=Windows NT,CN=Services,{0}' -f (Get-ADRootDSE).configurationNamingContext) -Properties tombstoneLifetime).tombstoneLifetime
    ```

- 도메인 컨트롤러를 복원하는 방법에 대한 지침이 포함된 명확한 재해 복구 계획을 수립합니다. Active Directory 포리스트 복원을 준비하려면 [Active Directory 포리스트 복구 가이드](/windows-server/identity/ad-ds/manage/ad-forest-recovery-guide)를 참조하세요.

- 도메인 컨트롤러를 복원하고 도메인에 남아 있는 도메인 컨트롤러를 유지해야 하는 경우 백업에서 복원하는 대신 새 서버를 만들 수 있습니다. 새 서버에 **Active Directory Domain Services** 서버 역할을 추가하여 기존 도메인의 도메인 컨트롤러로 만듭니다. 그런 다음 Active Directory 데이터가 새 서버에 복제됩니다. Active Directory에서 이전 도메인 컨트롤러를 제거하려면 [이 문서의](/windows-server/identity/ad-ds/deploy/ad-ds-metadata-cleanup) 단계에 따라 메타데이터 정리를 수행합니다.

>[!NOTE]
>Azure Backup에는 Active Directory에 대한 항목 수준 복원이 포함되지 않습니다. 삭제된 개체를 복원하려는 경우 도메인 컨트롤러에 액세스할 수 있는 경우 [Active Directory 휴지통](/windows-server/identity/ad-ds/get-started/adac/introduction-to-active-directory-administrative-center-enhancements--level-100-#ad_recycle_bin_mgmt)을 사용합니다. 해당 메서드를 사용할 수 없는 경우 도메인 컨트롤러 백업을 사용하여 [여기](https://support.microsoft.com/help/840001/how-to-restore-deleted-user-accounts-and-their-group-memberships-in-ac)에 설명된 대로 **ntdsutil.exe** 도구로 삭제된 개체를 복원할 수 있습니다.
>
>SYSVOL의 정식 복원을 수행하는 방법에 대한 자세한 내용은 [이 문서](/windows-server/identity/ad-ds/manage/ad-forest-recovery-authoritative-recovery-sysvol)를 참조하세요.

## <a name="backing-up-azure-vm-domain-controllers"></a>Azure VM 도메인 컨트롤러 백업

도메인 컨트롤러가 Azure VM인 경우 [Azure VM 백업](backup-azure-vms-introduction.md)을 사용하여 서버를 백업할 수 있습니다.

Azure VM 도메인 컨트롤러의 성공적인 백업(및 향후 복원)을 보장하려면 [가상화된 도메인 컨트롤러를 위한 운영 고려 사항](/windows-server/identity/ad-ds/get-started/virtual-dc/virtualized-domain-controllers-hyper-v#operational-considerations-for-virtualized-domain-controllers)을 읽어보세요.

## <a name="backing-up-on-premises-domain-controllers"></a>온-프레미스 도메인 컨트롤러 백업

온-프레미스 도메인 컨트롤러를 백업하려면 서버의 시스템 상태 데이터를 백업해야 합니다.

- MARS를 사용하는 경우 [다음 지침](backup-azure-system-state.md)을 따르세요.
- MABS(Azure Backup Server)를 사용하는 경우 [다음 지침](backup-mabs-system-state-and-bmr.md)을 따르세요.

>[!NOTE]
> 온-프레미스 도메인 컨트롤러(시스템 상태 또는 VM에서)를 Azure 클라우드로 복원하는 것은 지원되지 않습니다. 온-프레미스 Active Directory 환경에서 Azure로 장애 조치(failover) 옵션을 사용하려는 경우 [Azure Site Recovery](../site-recovery/site-recovery-active-directory.md)를 사용하는 것이 좋습니다.

## <a name="restoring-active-directory"></a>Active Directory 복원

Active Directory 데이터는 두 가지 모드, 즉 **정식** 또는 **비정식** 모드로 복원할 수 있습니다. 정식 복원에서 복원된 Active Directory 데이터는 포리스트의 다른 도메인 컨트롤러에 있는 데이터를 재정의합니다.

그러나 이 시나리오에서는 기존 도메인의 도메인 컨트롤러를 다시 작성하여 **비정식** 복원을 수행해야 합니다.

복원 중에 서버는 DSRM(디렉터리 서비스 복원 모드)에서 시작됩니다. 디렉터리 서비스 복원 모드를 위한 관리자 암호를 제공해야 합니다.

>[!NOTE]
>DSRM 암호가 기억나지 않는 경우 [이러한 지침](/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/cc754363(v=ws.11))을 사용하여 다시 설정할 수 있습니다.

### <a name="restoring-azure-vm-domain-controllers"></a>Azure VM 도메인 컨트롤러 복원

Azure VM 도메인 컨트롤러를 복원하려면 [도메인 컨트롤러 VM 복원](backup-azure-arm-restore-vms.md#restore-domain-controller-vms)을 참조하세요.

단일 도메인 컨트롤러 VM 또는 여러 도메인 컨트롤러 VM을 단일 도메인에 복원하는 경우 다른 VM과 같이 복원합니다. Active Directory 복원 모드(DSRM)도 사용할 수 있으므로 모든 Directory Services 복원 모드를 실행할 수 있습니다.

여러 도메인 구성에서 단일 도메인 컨트롤러 VM을 복원해야 하는 경우에는 [PowerShell을 사용하여](backup-azure-vms-automation.md#restore-the-disks) 디스크를 복원하고 VM을 만듭니다.

도메인에서 마지막으로 남은 도메인 컨트롤러를 복원하거나 한 포리스트에서 여러 도메인을 복원하는 경우에는 [포리스트 복구](/windows-server/identity/ad-ds/manage/ad-forest-recovery-single-domain-in-multidomain-recovery)를 권장합니다.

>[!NOTE]
> 가상화된 도메인 컨트롤러는 Windows 2012부터 [가상화 기반 보호](/windows-server/identity/ad-ds/introduction-to-active-directory-domain-services-ad-ds-virtualization-level-100#virtualization-based-safeguards) 기능을 사용합니다. 이러한 보호 기능을 통해 Active directory는 복원된 VM이 도메인 컨트롤러인지를 이해하고 Active Directory 데이터를 복원하는 데 필요한 단계를 수행합니다.

### <a name="restoring-on-premises-domain-controllers"></a>온-프레미스 도메인 컨트롤러 복원

온-프레미스 도메인 컨트롤러를 복원하려면 [도메인 컨트롤러에서 시스템 상태 복구를 위한 특별 고려 사항](backup-azure-restore-system-state.md#special-considerations-for-system-state-recovery-on-a-domain-controller)에 대한 지침을 사용하여 Windows Server로 시스템 상태를 복원하기 위한 지침을 따르세요.

## <a name="next-steps"></a>다음 단계

- [Azure Backup Server의 지원 매트릭스](backup-support-matrix.md)
