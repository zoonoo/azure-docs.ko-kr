---
title: Active Directory 백업 및 복원
description: Active Directory 도메인 컨트롤러를 백업 하 고 복원 하는 방법에 대해 알아봅니다.
ms.topic: conceptual
ms.date: 07/08/2020
ms.openlocfilehash: afbc538b84c20f9baaa664c7d47140365c747cb0
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/07/2020
ms.locfileid: "96754765"
---
# <a name="back-up-and-restore-active-directory-domain-controllers"></a>Active Directory 도메인 컨트롤러 백업 및 복원

Active Directory를 백업 하 고 손상, 손상 또는 재해가 발생 하는 경우의 성공적인 복원은 Active Directory 유지 관리의 중요 한 부분입니다.

이 문서에서는 Azure 가상 머신 또는 온-프레미스 서버 든 상관 없이 Azure Backup를 사용 하 여 Active Directory 도메인 컨트롤러를 백업 및 복원 하는 적절 한 절차를 설명 합니다. 전체 도메인 컨트롤러를 백업 시 상태로 복원 해야 하는 시나리오에 대해 설명 합니다. 적절 한 복원 시나리오를 확인 하려면 [이 문서](https://docs.microsoft.com/windows-server/identity/ad-ds/manage/ad-forest-recovery-determine-how-to-recover)를 참조 하세요.  

>[!NOTE]
> 이 문서에서는 [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis)에서 항목을 복원 하는 방법에 대해서는 설명 하지 않습니다. Azure Active Directory 사용자 복원에 대 한 자세한 내용은 [이 문서](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-restore)를 참조 하세요.

## <a name="best-practices"></a>모범 사례

- 도메인 컨트롤러를 하나 이상 백업 했는지 확인 합니다. 둘 이상의 도메인 컨트롤러를 백업 하는 경우 [FSMO (신축 단일 마스터 작업) 역할](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/planning-operations-master-role-placement) 을 포함 하는 모든 역할을 백업 해야 합니다.

- Active Directory 자주 백업 합니다. 삭제 표식 수명 보다 오래 된 개체는 "삭제 표시" 되 고 더 이상 유효한 것으로 간주 되지 않으므로 백업은 항상 삭제 표시 수명 (기본값 60 일) 보다 작아야 합니다.

- 도메인 컨트롤러를 복원 하는 방법에 대 한 지침이 포함 된 명확한 재해 복구 계획을 수립 합니다. Active Directory 포리스트 복원을 준비 하려면 [Active Directory 포리스트 복구 가이드](https://docs.microsoft.com/windows-server/identity/ad-ds/manage/ad-forest-recovery-guide)를 참조 하세요.

- 도메인 컨트롤러를 복원 하 고 도메인에 남아 있는 도메인 컨트롤러를 유지 해야 하는 경우 백업에서 복원 하는 대신 새 서버를 만들 수 있습니다. 새 서버에 **Active Directory Domain Services** 서버 역할을 추가 하 여 기존 도메인의 도메인 컨트롤러로 만듭니다. 그런 다음 Active Directory 데이터가 새 서버에 복제 됩니다. Active Directory에서 이전 도메인 컨트롤러를 제거 하려면 [이 문서의](https://docs.microsoft.com/windows-server/identity/ad-ds/deploy/ad-ds-metadata-cleanup) 단계에 따라 메타 데이터 정리를 수행 합니다.

>[!NOTE]
>Azure Backup에는 Active Directory에 대 한 항목 수준 복원이 포함 되지 않습니다. 삭제 된 개체를 복원 하려는 경우 도메인 컨트롤러에 액세스할 수 있는 경우 [Active Directory 휴지통](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/adac/introduction-to-active-directory-administrative-center-enhancements--level-100-#ad_recycle_bin_mgmt)을 사용 합니다. 해당 메서드를 사용할 수 없는 경우 도메인 컨트롤러 백업을 사용 하 여 [여기](https://support.microsoft.com/help/840001/how-to-restore-deleted-user-accounts-and-their-group-memberships-in-ac)에 설명 된 대로 **ntdsutil.exe** 도구를 사용 하 여 삭제 된 개체를 복원할 수 있습니다.
>
>SYSVOL의 정식 복원을 수행 하는 방법에 대 한 자세한 내용은 [이 문서](https://docs.microsoft.com/windows-server/identity/ad-ds/manage/ad-forest-recovery-authoritative-recovery-sysvol)를 참조 하세요.

## <a name="backing-up-azure-vm-domain-controllers"></a>Azure VM 도메인 컨트롤러 백업

도메인 컨트롤러가 Azure VM 인 경우 [AZURE Vm 백업을](backup-azure-vms-introduction.md)사용 하 여 서버를 백업할 수 있습니다.

가상화 된 [도메인 컨트롤러에 대 한 운영 고려 사항](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/virtualized-domain-controllers-hyper-v#operational-considerations-for-virtualized-domain-controllers) 에 대해 읽어 Azure VM 도메인 컨트롤러의 성공적인 백업 (및 향후 복원)을 확인 합니다.

## <a name="backing-up-on-premises-domain-controllers"></a>온-프레미스 도메인 컨트롤러 백업

온-프레미스 도메인 컨트롤러를 백업 하려면 서버의 시스템 상태 데이터를 백업 해야 합니다.

- MARS를 사용 하는 경우 [다음 지침](backup-azure-system-state.md)을 따르세요.
- MABS (Azure Backup Server)를 사용 하는 경우 [다음 지침](backup-mabs-system-state-and-bmr.md)을 따르세요.

>[!NOTE]
> 온-프레미스 도메인 컨트롤러 (시스템 상태 또는 Vm에서)를 Azure 클라우드로 복원 하는 것은 지원 되지 않습니다. 온-프레미스 Active Directory 환경에서 Azure로 장애 조치 (failover) 옵션을 사용 하려는 경우 [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-active-directory)를 사용 하는 것이 좋습니다.

## <a name="restoring-active-directory"></a>Active Directory 복원

Active Directory 데이터는 두 가지 모드, 즉 **신뢰할** 수 있는 데이터 나 **비정식** 으로 복원할 수 있습니다. 신뢰할 수 있는 복원에서 복원 된 Active Directory 데이터는 포리스트의 다른 도메인 컨트롤러에 있는 데이터를 재정의 합니다.

그러나이 시나리오에서는 기존 도메인의 도메인 컨트롤러를 다시 작성 하 여 **신뢰할** 수 없는 복원을 수행 해야 합니다.

복원 중에 서버는 DSRM (디렉터리 서비스 복원 모드)에서 시작 됩니다. 디렉터리 서비스 복원 모드에 대 한 관리자 암호를 제공 해야 합니다.

>[!NOTE]
>DSRM 암호가 기억나지 않는 경우 [이러한 지침](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/cc754363(v=ws.11))을 사용 하 여 다시 설정할 수 있습니다.

### <a name="restoring-azure-vm-domain-controllers"></a>Azure VM 도메인 컨트롤러 복원

Azure VM 도메인 컨트롤러를 복원 하려면 [도메인 컨트롤러 Vm 복원](backup-azure-arm-restore-vms.md#restore-domain-controller-vms)을 참조 하세요.

단일 도메인 컨트롤러 VM 또는 여러 도메인 컨트롤러 vm을 단일 도메인에 복원 하는 경우 다른 VM과 같이 복원 합니다. Active Directory 복원 모드(DSRM)도 사용할 수 있으므로 모든 Directory Services 복원 모드를 실행할 수 있습니다.

여러 도메인 구성에서 단일 도메인 컨트롤러 VM을 복원 해야 하는 경우에는 [PowerShell을 사용 하 여](backup-azure-vms-automation.md#restore-the-disks)디스크를 복원 하 고 vm을 만듭니다.

도메인에서 마지막으로 남은 도메인 컨트롤러를 복원 하거나 한 포리스트에서 여러 도메인을 복원 하는 경우에는 [포리스트 복구](https://docs.microsoft.com/windows-server/identity/ad-ds/manage/ad-forest-recovery-single-domain-in-multidomain-recovery)를 권장 합니다.

>[!NOTE]
> Windows 2012부터 가상화 된 도메인 컨트롤러는 [가상화 기반 보호](https://docs.microsoft.com/windows-server/identity/ad-ds/introduction-to-active-directory-domain-services-ad-ds-virtualization-level-100#virtualization-based-safeguards)기능을 사용 합니다. 이러한 보호 기능을 통해 Active directory는 복원 된 VM이 도메인 컨트롤러 인지를 이해 하 고 Active Directory 데이터를 복원 하는 데 필요한 단계를 수행 합니다.

### <a name="restoring-on-premises-domain-controllers"></a>온-프레미스 도메인 컨트롤러 복원

온-프레미스 도메인 컨트롤러를 복원 하려면 [도메인 컨트롤러에서 시스템 상태 복구에 대 한 특별 고려 사항](backup-azure-restore-system-state.md#special-considerations-for-system-state-recovery-on-a-domain-controller)에 대 한 지침을 사용 하 여 Windows Server에 시스템 상태 복원에 대 한 지침을 따르세요.

## <a name="next-steps"></a>다음 단계

- [Azure Backup Server의 지원 매트릭스](backup-support-matrix.md)
