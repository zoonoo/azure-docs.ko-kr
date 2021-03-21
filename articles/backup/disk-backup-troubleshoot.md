---
title: Azure Disk Backup에서 백업 오류 문제 해결
description: Azure Disk Backup에서 백업 실패 문제를 해결 하는 방법 알아보기
ms.topic: conceptual
ms.date: 01/07/2021
ms.openlocfilehash: 855c6c5b19b10bdb699a25f89ebc29001b7941ac
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98737730"
---
# <a name="troubleshooting-backup-failures-in-azure-disk-backup-in-preview"></a>Azure 디스크 백업 (미리 보기)의 백업 오류 문제 해결

>[!IMPORTANT]
>Azure Disk Backup은 서비스 수준 계약 없이 미리 보기 상태 이며 프로덕션 워크 로드에는 권장 되지 않습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요. 지역 가용성은 [지원 매트릭스](disk-backup-support-matrix.md)를 참조 하세요.
>
>미리 보기에 등록 하려면 [이 양식을 작성](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR1vE8L51DIpDmziRt_893LVUNFlEWFJBN09PTDhEMjVHS05UWFkxUlUzUS4u) 하세요.

이 문서에서는 Azure Disk에 직면 한 백업 및 복원 문제에 대 한 문제 해결 정보를 제공 합니다. [Azure Disk backup](disk-backup-overview.md) 지역 가용성, 지원 되는 시나리오 및 제한 사항에 대 한 자세한 내용은 [지원 매트릭스](disk-backup-support-matrix.md)를 참조 하세요.

## <a name="common-issues-faced-with-azure-disk-backup"></a>Azure Disk Backup에 직면 하는 일반적인 문제

### <a name="error-code-usererrorsnapshotrgsubscriptionmismatch"></a>오류 코드: UserErrorSnapshotRGSubscriptionMismatch

오류 메시지: 스냅숏 데이터 저장소에 대해 잘못 된 구독을 선택 했습니다.

권장 작업: 디스크 및 디스크 스냅숏은 동일한 구독에 저장 됩니다. 모든 리소스 그룹을 선택 하 여 구독 내에 디스크 스냅숏을 저장할 수 있습니다. 원본 디스크와 동일한 구독을 선택 합니다. 자세한 내용은 [지원 매트릭스](disk-backup-support-matrix.md)를 참조 하세요.

### <a name="error-code-usererrorsnapshotrgnotfound"></a>오류 코드: UserErrorSnapshotRGNotFound

오류 메시지: 스냅숏 데이터 저장소 리소스 그룹이 없으므로 작업을 수행할 수 없습니다.

권장 작업: 리소스 그룹을 만들고 해당 그룹에 필요한 권한을 제공 합니다. 자세한 내용은 [백업 구성](backup-managed-disks.md#configure-backup)을 참조 하세요.

### <a name="error-code-usererrormanageddisknotfound"></a>오류 코드: UserErrorManagedDiskNotFound

오류 메시지: 관리 디스크가 더 이상 존재 하지 않으므로 작업을 수행할 수 없습니다.

권장 작업: 원본 디스크를 삭제 하거나 다른 위치로 이동할 수 있으므로 백업이 계속 실패 합니다. 실수로 삭제 한 경우 기존 복원 지점을 사용 하 여 디스크를 복원 합니다. 디스크가 다른 위치로 이동 하는 경우 디스크에 대 한 백업을 구성 합니다.

### <a name="error-code-usererrornotenoughpermissionondisk"></a>오류 코드: UserErrorNotEnoughPermissionOnDisk

오류 메시지:이 작업을 수행 하려면 Azure Backup 서비스에 디스크에 대 한 추가 권한이 필요 합니다.

권장 작업: 백업 자격 증명 모음 관리 id에 디스크에 대 한 적절 한 권한을 부여 합니다. 백업 자격 증명 모음 관리 id에 필요한 사용 권한 및이를 제공 하는 방법을 이해 하려면 [설명서](backup-managed-disks.md) 를 참조 하세요.

### <a name="error-code-usererrornotenoughpermissiononsnapshotrg"></a>오류 코드: UserErrorNotEnoughPermissionOnSnapshotRG

오류 메시지:이 작업을 수행 하려면 Azure Backup 서비스에 스냅숏 데이터 저장소 리소스 그룹에 대 한 추가 권한이 필요 합니다.

권장 작업: 백업 자격 증명 모음 관리 id에 스냅숏 데이터 저장소 리소스 그룹에 대 한 적절 한 권한을 부여 합니다. 스냅숏 데이터 저장소 리소스 그룹은 디스크 스냅숏이 저장 되는 위치입니다. 이 [문서](backup-managed-disks.md) 를 참조 하 여 리소스 그룹, 백업 자격 증명 모음 관리 id에 필요한 사용 권한 및이를 제공 하는 방법을 이해 하세요.

### <a name="error-code-usererrordiskbackupdiskormsipermissionsnotpresent"></a>오류 코드: UserErrorDiskBackupDiskOrMSIPermissionsNotPresent

오류 메시지:이 작업을 수행 하려면 잘못 된 디스크 또는 Azure Backup 서비스에 디스크에 대 한 추가 권한이 필요 합니다.

권장 작업: 원본 디스크를 삭제 하거나 다른 위치로 이동할 수 있으므로 백업이 계속 실패 합니다. 실수로 삭제 한 경우 기존 복원 지점을 사용 하 여 디스크를 복원 합니다. 디스크가 다른 위치로 이동 하는 경우 디스크에 대 한 백업을 구성 합니다. 디스크를 삭제 하거나 이동 하지 않은 경우 백업 자격 증명 모음 관리 id에 디스크에 대 한 적절 한 권한을 부여 합니다. 백업 자격 증명 모음에서 관리 되는 id 및이를 제공 하는 방법에 필요한 사용 권한은 [설명서](backup-managed-disks.md) 를 참조 하세요.

### <a name="error-code-usererrordiskbackupsnapshotrgormsipermissionsnotpresent"></a>오류 코드: UserErrorDiskBackupSnapshotRGOrMSIPermissionsNotPresent

오류 메시지: 스냅숏 데이터 저장소 리소스 그룹이 더 이상 존재 하지 않으므로 작업을 수행할 수 없습니다. 또는 Azure Backup 서비스에는이 작업을 수행 하기 위해 스냅숏 데이터 저장소 리소스 그룹에 대 한 추가 권한이 필요 합니다.

권장 작업: 리소스 그룹을 만들고 백업 자격 증명 모음 관리 id에 스냅숏 데이터 저장소 리소스 그룹에 대 한 적절 한 권한을 부여 합니다. 스냅숏 데이터 저장소 리소스 그룹은 디스크 스냅숏이 저장 되는 위치입니다. 리소스 그룹, 백업 자격 증명 모음에서 관리 하는 id에 필요한 권한 및 제공 하는 방법에 대 한 자세한 내용은 [설명서](backup-managed-disks.md) 를 참조 하세요.

### <a name="error-code-usererrordiskbackupauthorizationfailed"></a>오류 코드: UserErrorDiskBackupAuthorizationFailed

오류 메시지: 백업 자격 증명 모음 관리 id에이 작업을 수행 하는 데 필요한 권한이 없습니다.

권장 작업: 백업 자격 증명 모음 관리 id에 백업할 디스크 및 스냅숏이 저장 된 스냅숏 데이터 저장소 리소스 그룹에 대 한 적절 한 권한을 부여 합니다. 백업 자격 증명 모음에서 관리 되는 id 및이를 제공 하는 방법에 필요한 사용 권한은 [설명서](backup-managed-disks.md) 를 참조 하세요.

### <a name="error-code-usererrorsnapshotrgormsipermissionsnotpresent"></a>오류 코드: UserErrorSnapshotRGOrMSIPermissionsNotPresent

오류 메시지: 스냅숏 데이터 저장소 리소스 그룹이 더 이상 존재 하지 않으므로 작업을 수행할 수 없습니다. 또는이 작업을 수행 하려면 Azure Backup 서비스에 스냅숏 데이터 저장소 리소스 그룹에 대 한 추가 권한이 있어야 합니다.

권장 작업: 리소스 그룹을 만들고 백업 자격 증명 모음 관리 id에 스냅숏 데이터 저장소 리소스 그룹에 대 한 적절 한 권한을 부여 합니다. 스냅숏 데이터 저장소 리소스 그룹은 스냅숏이 저장 되는 위치입니다. 리소스 그룹, 백업 자격 증명 모음에서 관리 하는 id에 필요한 권한 및 제공 하는 방법에 대 한 자세한 내용은 [설명서](backup-managed-disks.md) 를 참조 하세요.

### <a name="error-code-usererroroperationalstoreparametersnotprovided"></a>오류 코드: UserErrorOperationalStoreParametersNotProvided

오류 메시지: 스냅숏 데이터 저장소 리소스 그룹 매개 변수가 제공 되지 않아 작업을 수행할 수 없습니다.

권장 작업: 스냅숏 데이터 저장소 리소스 그룹 매개 변수를 제공 합니다. 스냅숏 데이터 저장소 리소스 그룹은 디스크 스냅숏이 저장 되는 위치입니다. 자세한 내용은 [설명서](backup-managed-disks.md)를 참조 하세요.

### <a name="error-code-usererrorinvalidoperationalstoreresourcegroup"></a>오류 코드: UserErrorInvalidOperationalStoreResourceGroup

오류 메시지: 제공 된 스냅숏 데이터 저장소 리소스 그룹이 잘못 되었습니다.

권장 작업: 스냅숏 데이터 저장소에 대 한 올바른 리소스 그룹을 제공 합니다. 스냅숏 데이터 저장소 리소스 그룹은 디스크 스냅숏이 저장 되는 위치입니다. 자세한 내용은 [설명서](backup-managed-disks.md)를 참조 하세요.

### <a name="error-code-usererrordiskbackupdisktypenotsupported"></a>오류 코드: UserErrorDiskBackupDiskTypeNotSupported

오류 메시지: 지원 되지 않는 디스크 유형

권장 작업: 지원 되지 않는 시나리오 및 제한 사항에 대 한 [지원 매트릭스](disk-backup-support-matrix.md) 를 참조 하세요.

### <a name="error-code-usererrorsamenamediskalreadyexists"></a>오류 코드: UserErrorSameNameDiskAlreadyExists

오류 메시지: 선택한 대상 리소스 그룹에 동일한 이름의 디스크가 이미 있기 때문에 복원할 수 없습니다.

권장 작업: 복원에 다른 디스크 이름을 지정 합니다. 자세한 내용은 [Azure Managed Disks 복원](restore-managed-disks.md)을 참조 하세요.

### <a name="error-code-usererrorrestoretargetrgnotfound"></a>오류 코드: UserErrorRestoreTargetRGNotFound

오류 메시지: 대상 리소스 그룹이 없으므로 작업이 실패 했습니다.

권장 작업: 복원할 올바른 리소스 그룹을 제공 합니다. 자세한 내용은 [Azure Managed Disks 복원](restore-managed-disks.md)을 참조 하세요.

### <a name="error-code-usererrornotenoughpermissionontargetrg"></a>오류 코드: UserErrorNotEnoughPermissionOnTargetRG

오류 메시지:이 작업을 수행 하려면 Azure Backup 서비스에 대상 리소스 그룹에 대 한 추가 권한이 있어야 합니다.

권장 작업: 백업 자격 증명 모음 관리 id에 대상 리소스 그룹에 대 한 적절 한 사용 권한을 부여 합니다. 대상 리소스 그룹은 디스크를 복원할 위치를 선택 합니다. [복원 설명서](restore-managed-disks.md) 를 참조 하 여 Backup 자격 증명 모음에서 관리 되는 id에 필요한 사용 권한을 이해 하 고이를 제공 하는 방법을 알아보세요.

### <a name="error-code-usererrorsubscriptiondiskquotalimitreached"></a>오류 코드: UserErrorSubscriptionDiskQuotaLimitReached

오류 메시지: 구독에서 디스크 할당량 최대 제한에 도달 하 여 작업이 실패 했습니다.

권장 조치: 추가 지침은 [Azure 구독 및 서비스 제한 및 할당량 문서](../azure-resource-manager/management/azure-subscription-service-limits.md) 를 참조 하거나 Microsoft 지원 하세요.

### <a name="error-code-usererrordiskbackuprestorergormsipermissionsnotpresent"></a>오류 코드: UserErrorDiskBackupRestoreRGOrMSIPermissionsNotPresent

오류 메시지: 대상 리소스 그룹이 없으므로 작업이 실패 했습니다. 또는 Azure Backup 서비스에는이 작업을 수행 하기 위해 대상 리소스 그룹에 대 한 추가 권한이 필요 합니다.

권장 작업: 복원할 유효한 리소스 그룹을 제공 하 고, 대상 리소스 그룹에 대 한 적절 한 사용 권한을 백업 자격 증명 모음 관리 id에 부여 합니다. 대상 리소스 그룹은 디스크를 복원할 위치를 선택 합니다. [복원 설명서](restore-managed-disks.md) 를 참조 하 여 Backup 자격 증명 모음에서 관리 되는 id에 필요한 사용 권한을 이해 하 고이를 제공 하는 방법을 알아보세요.

### <a name="error-code-usererrordeskeyvaultkeydisabled"></a>오류 코드: UserErrorDESKeyVaultKeyDisabled

오류 메시지: 디스크 암호화 집합에 사용 되는 키 자격 증명 모음 키가 활성화 된 상태가 아닙니다.

권장 작업: 디스크 암호화 집합에 사용 되는 key vault 키를 사용 하도록 설정 합니다. [지원 매트릭스](disk-backup-support-matrix.md)의 제한 사항을 참조 하세요.

### <a name="error-code-usererrormsipermissionsnotpresentondes"></a>오류 코드: UserErrorMSIPermissionsNotPresentOnDES

오류 메시지: Azure Backup 서비스에는 디스크에 사용 되는 디스크 암호화 집합에 액세스할 수 있는 권한이 필요 합니다.

권장 작업: 디스크 암호화 집합 (DES)에 대 한 백업 자격 증명 모음 관리 id에 대 한 읽기 권한자 액세스를 제공 합니다.

### <a name="error-code-usererrordeskeyvaultkeynotavailable"></a>오류 코드: UserErrorDESKeyVaultKeyNotAvailable

오류 메시지: 디스크 암호화 집합에 사용 되는 key vault 키를 사용할 수 없습니다.

권장 작업: 디스크 암호화 집합에 사용 되는 key vault 키를 사용 하지 않도록 설정 하거나 삭제 해야 합니다.

### <a name="error-code-usererrordisksnapshotnotfound"></a>오류 코드: UserErrorDiskSnapshotNotFound

오류 메시지:이 복원 지점의 디스크 스냅숏이 삭제 되었습니다.

권장 작업: 스냅숏은 구독 내의 스냅숏 데이터 저장소 리소스 그룹에 저장 됩니다. 선택한 복원 지점과 관련 된 스냅숏이 삭제 되었거나이 리소스 그룹에서 이동 되었을 수 있습니다. 다른 복구 지점을 사용 하 여 복원 하는 것이 좋습니다. 또한 [복원 설명서](restore-managed-disks.md)에서 언급 한 스냅숏 리소스 그룹 선택에 대 한 권장 지침을 따르세요.

### <a name="error-code-usererrorsnapshotmetadatanotfound"></a>오류 코드: UserErrorSnapshotMetadataNotFound

오류 메시지:이 복원 지점의 디스크 스냅숏 메타 데이터가 삭제 되었습니다.

권장 조치: 복원할 다른 복구 지점을 사용 하는 것이 좋습니다. 자세한 내용은 [복원 설명서](restore-managed-disks.md)를 참조 하세요.

### <a name="error-code-backupagentpluginhostvalidateprotectionerror"></a>오류 코드: BackupAgentPluginHostValidateProtectionError

오류 메시지: 백업 자격 증명 모음에서 보호를 구성 하는 동안 아직 디스크 백업을 사용할 수 없습니다.

권장 작업: 백업 자격 증명 모음은 지원 되는 미리 보기 영역에 있어야 합니다. 지역 가용성은 [지원 매트릭스](disk-backup-support-matrix.md)를 참조 하세요.

### <a name="error-code-usererrordppdatasourcealreadyhasbackupinstance"></a>오류 코드: UserErrorDppDatasourceAlreadyHasBackupInstance

오류 메시지: 백업을 구성 하려는 디스크가 이미 보호 되 고 있습니다. 디스크가 백업 자격 증명 모음의 백업 인스턴스와 이미 연결 되어 있습니다.

권장 작업:이 디스크는 백업 자격 증명 모음의 백업 인스턴스와 이미 연결 되어 있습니다. 이 디스크를 다시 보호 하려면 현재 보호 된 백업 자격 증명 모음에서 백업 인스턴스를 삭제 하 고 다른 자격 증명 모음에서 디스크를 다시 보호 하십시오.

### <a name="error-code-usererrordppdatasourcealreadyprotected"></a>오류 코드: UserErrorDppDatasourceAlreadyProtected

오류 메시지: 백업을 구성 하려는 디스크가 이미 보호 되 고 있습니다. 디스크가 백업 자격 증명 모음의 백업 인스턴스와 이미 연결 되어 있습니다.

권장 작업:이 디스크는 백업 자격 증명 모음의 백업 인스턴스와 이미 연결 되어 있습니다. 이 디스크를 다시 보호 하려면 현재 보호 중인 백업 자격 증명 모음에서 백업 인스턴스를 삭제 하 고 다른 자격 증명 모음에서 디스크를 다시 보호 하십시오.

### <a name="error-code-usererrormaxconcurrentoperationlimitreached"></a>오류 코드: UserErrorMaxConcurrentOperationLimitReached

오류 메시지: 허용 되는 최대 동시 백업 수에 도달 하 여 작업을 시작할 수 없습니다.

권장 조치: 이전에 실행 중인 백업이 완료 될 때까지 기다립니다.

## <a name="next-steps"></a>다음 단계

- [Azure Disk Backup 지원 매트릭스](disk-backup-support-matrix.md)