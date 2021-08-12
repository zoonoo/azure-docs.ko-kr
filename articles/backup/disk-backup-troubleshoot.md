---
title: Azure Disk Backup의 백업 오류 문제 해결
description: Azure Disk Backup의 백업 오류 문제 해결 방법 알아보기
ms.topic: conceptual
ms.date: 06/08/2021
ms.openlocfilehash: a749c87949d53781dc810148d01cc5d179d70f77
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/09/2021
ms.locfileid: "111754118"
---
# <a name="troubleshooting-backup-failures-in-azure-disk-backup"></a>Azure Disk Backup의 백업 오류 문제 해결

이 문서에서는 Azure Disk에서 발생한 백업 및 복원 문제에 대한 해결 정보를 제공합니다. [Azure Disk Backup](disk-backup-overview.md) 지역 가용성, 지원되는 시나리오 및 제한 사항에 대한 자세한 내용은 [지원 매트릭스](disk-backup-support-matrix.md)를 참조하세요.

## <a name="common-issues-faced-with-azure-disk-backup"></a>Azure Disk Backup에서 발생하는 일반적인 문제

### <a name="error-code-usererrorsnapshotrgsubscriptionmismatch"></a>오류 코드: UserErrorSnapshotRGSubscriptionMismatch

오류 메시지: 스냅샷 데이터 저장소에 대해 잘못된 구독이 선택됨

권장 작업: 디스크 및 디스크 스냅샷이 동일한 구독에 저장됩니다. 리소스 그룹을 선택하여 구독 내에 디스크 스냅샷을 저장할 수 있습니다. 원본 디스크와 동일한 구독을 선택합니다. 자세한 내용은 [지원 매트릭스](disk-backup-support-matrix.md)를 참조하세요.

### <a name="error-code-usererrorsnapshotrgnotfound"></a>오류 코드: UserErrorSnapshotRGNotFound

오류 메시지: 스냅샷 데이터 저장소 리소스 그룹이 없으므로 작업을 수행할 수 없습니다.

권장 작업: 리소스 그룹을 만들고 해당 그룹에 필요한 권한을 제공합니다. 자세한 내용은 [백업 구성](backup-managed-disks.md#configure-backup)을 참조하세요.

### <a name="error-code-usererrormanageddisknotfound"></a>오류 코드: UserErrorManagedDiskNotFound

오류 메시지: 관리 디스크가 더 이상 존재하지 않으므로 작업을 수행할 수 없습니다.

권장 작업: 원본 디스크가 삭제되었거나 다른 위치로 이동했을 수 있어 백업이 계속 실패합니다. 실수로 삭제된 경우 기존 복원 지점을 사용하여 디스크를 복원합니다. 디스크가 다른 위치로 이동된 경우 디스크에 대한 백업을 구성합니다.

### <a name="error-code-usererrornotenoughpermissionondisk"></a>오류 코드: UserErrorNotEnoughPermissionOnDisk

오류 메시지: 이 작업을 수행하려면 Azure Backup 서비스에 디스크에 대한 추가 권한이 필요합니다.

권장 작업: 백업 자격 증명 모음 관리 ID에 디스크에 대한 적절한 권한을 부여합니다. 백업 자격 증명 모음 관리 ID에 필요한 권한과 이를 제공하는 방법을 이해하려면 [설명서](backup-managed-disks.md)를 참조하세요.

### <a name="error-code-usererrornotenoughpermissiononsnapshotrg"></a>오류 코드: UserErrorNotEnoughPermissionOnSnapshotRG

오류 메시지: 이 작업을 수행하려면 Azure Backup 서비스에 스냅샷 데이터 저장소 리소스 그룹에 대한 추가 권한이 필요합니다.

권장 작업: 백업 자격 증명 모음 관리 ID에 스냅샷 데이터 저장소 리소스 그룹에 대한 적절한 권한을 부여합니다. 스냅샷 데이터 저장소 리소스 그룹은 디스크 스냅샷이 저장되는 위치입니다. 리소스 그룹이 무엇인지, 백업 자격 증명 모음 관리 ID에 필요한 권한과 이를 제공하는 방법을 이해하려면 [설명서](backup-managed-disks.md)를 참조하세요.

### <a name="error-code-usererrordiskbackupdiskormsipermissionsnotpresent"></a>오류 코드: UserErrorDiskBackupDiskOrMSIPermissionsNotPresent

오류 메시지: 잘못된 디스크이거나, Azure Backup 서비스에서 이 작업을 수행하려면 디스크에 대한 추가 권한이 필요합니다.

권장 작업: 원본 디스크가 삭제되었거나 다른 위치로 이동했을 수 있어 백업이 계속 실패합니다. 실수로 삭제된 경우 기존 복원 지점을 사용하여 디스크를 복원합니다. 디스크가 다른 위치로 이동된 경우 디스크에 대한 백업을 구성합니다. 디스크가 삭제 또는 이동되지 않은 경우 백업 자격 증명 모음 관리 ID에 디스크에 대한 적절한 권한을 부여합니다. 백업 자격 증명 모음 관리 ID에 필요한 권한과 이를 제공하는 방법을 이해하려면 [설명서](backup-managed-disks.md)를 참조하세요.

### <a name="error-code-usererrordiskbackupsnapshotrgormsipermissionsnotpresent"></a>오류 코드: UserErrorDiskBackupSnapshotRGOrMSIPermissionsNotPresent

오류 메시지: 스냅샷 데이터 저장소 리소스 그룹이 없으므로 작업을 수행할 수 없습니다. 또는 이 작업을 수행하려면 Azure Backup 서비스에 스냅샷 데이터 저장소 리소스 그룹에 대한 추가 권한이 필요합니다.

권장 작업: 리소스 그룹을 만들고 백업 자격 증명 모음 관리 ID에 스냅샷 데이터 저장소 리소스 그룹에 대한 적절한 권한을 부여합니다. 스냅샷 데이터 저장소 리소스 그룹은 디스크 스냅샷이 저장되는 위치입니다. 리소스 그룹이 무엇인지, 백업 자격 증명 모음 관리 ID에 필요한 권한과 이를 제공하는 방법을 이해하려면 [설명서](backup-managed-disks.md)를 참조하세요.

### <a name="error-code-usererrordiskbackupauthorizationfailed"></a>오류 코드: UserErrorDiskBackupAuthorizationFailed

오류 메시지: 백업 자격 증명 모음 관리 ID에 이 작업을 수행하기 위해 필요한 권한이 없습니다.

권장 작업: 백업 자격 증명 모음 관리 ID에 백업할 디스크 및 스냅샷이 저장되는 스냅샷 데이터 저장소 리소스 그룹에 대한 적절한 권한을 부여합니다. 백업 자격 증명 모음 관리 ID에 필요한 권한과 이를 제공하는 방법을 이해하려면 [설명서](backup-managed-disks.md)를 참조하세요.

### <a name="error-code-usererrorsnapshotrgormsipermissionsnotpresent"></a>오류 코드: UserErrorSnapshotRGOrMSIPermissionsNotPresent

오류 메시지: 스냅샷 데이터 저장소 리소스 그룹이 없으므로 작업을 수행할 수 없습니다. 또는 이 작업을 수행하려면 Azure Backup 서비스에 스냅샷 데이터 저장소 리소스 그룹에 대한 추가 권한이 필요합니다.

권장 작업: 리소스 그룹을 만들고 백업 자격 증명 모음 관리 ID에 스냅샷 데이터 저장소 리소스 그룹에 대한 적절한 권한을 부여합니다. 스냅샷 데이터 저장소 리소스 그룹은 스냅샷이 저장되는 위치입니다. 리소스 그룹이 무엇인지, 백업 자격 증명 모음 관리 ID에 필요한 권한과 이를 제공하는 방법을 이해하려면 [설명서](backup-managed-disks.md)를 참조하세요.

### <a name="error-code-usererroroperationalstoreparametersnotprovided"></a>오류 코드: UserErrorOperationalStoreParametersNotProvided

오류 메시지: 스냅샷 데이터 저장소 리소스 그룹 매개 변수가 제공되지 않아 작업을 수행할 수 없습니다.

권장 작업: 스냅샷 데이터 저장소 리소스 그룹 매개 변수를 제공합니다. 스냅샷 데이터 저장소 리소스 그룹은 디스크 스냅샷이 저장되는 위치입니다. 자세한 내용은 [설명서](backup-managed-disks.md)를 참조하세요.

### <a name="error-code-usererrorinvalidoperationalstoreresourcegroup"></a>오류 코드: UserErrorInvalidOperationalStoreResourceGroup

오류 메시지: 제공된 스냅샷 데이터 저장소 리소스 그룹이 잘못되었습니다.

권장 작업: 스냅샷 데이터 저장소에 대한 올바른 리소스 그룹을 제공합니다. 스냅샷 데이터 저장소 리소스 그룹은 디스크 스냅샷이 저장되는 위치입니다. 자세한 내용은 [설명서](backup-managed-disks.md)를 참조하세요.

### <a name="error-code-usererrordiskbackupdisktypenotsupported"></a>오류 코드: UserErrorDiskBackupDiskTypeNotSupported

오류 메시지: 지원되지 않는 디스크 유형

권장 작업: 지원되지 않는 시나리오와 제한 사항은 [지원 매트릭스](disk-backup-support-matrix.md)를 참조하세요.

### <a name="error-code-usererrorsamenamediskalreadyexists"></a>오류 코드: UserErrorSameNameDiskAlreadyExists

오류 메시지: 선택한 대상 리소스 그룹에 이름이 같은 디스크가 이미 있기 때문에 복원할 수 없습니다.

권장 작업: 복원을 위해 다른 디스크 이름을 제공합니다. 자세한 내용은 [Azure 관리 디스크 복원](restore-managed-disks.md)을 참조하세요.

### <a name="error-code-usererrorrestoretargetrgnotfound"></a>오류 코드: UserErrorRestoreTargetRGNotFound

오류 메시지: 대상 리소스 그룹이 없어서 작업에 실패했습니다.

권장 작업: 복원을 위해 올바른 리소스 그룹을 제공합니다. 자세한 내용은 [Azure 관리 디스크 복원](restore-managed-disks.md)을 참조하세요.

### <a name="error-code-usererrornotenoughpermissionontargetrg"></a>오류 코드: UserErrorNotEnoughPermissionOnTargetRG

오류 메시지: 이 작업을 수행하려면 Azure Backup 서비스에 대상 리소스 그룹에 대한 추가 권한이 필요합니다.

권장 작업: 백업 자격 증명 모음 관리 ID에 대상 리소스 그룹에 대한 적절한 권한을 부여합니다. 대상 리소스 그룹은 디스크를 복원하도록 선택된 위치입니다. 백업 자격 증명 모음 관리 ID에 필요한 권한과 이를 제공하는 방법을 이해하려면 [복원 설명서](restore-managed-disks.md)를 참조하세요.

### <a name="error-code-usererrorsubscriptiondiskquotalimitreached"></a>오류 코드: UserErrorSubscriptionDiskQuotaLimitReached

오류 메시지: 구독에서 디스크 할당량 최대 한도에 도달했기 때문에 작업에 실패했습니다.

권장 작업: [Azure 구독 및 서비스 제한과 할당량 설명서](../azure-resource-manager/management/azure-subscription-service-limits.md)를 참조하거나 Microsoft 지원에 추가 지침을 문의합니다.

### <a name="error-code-usererrordiskbackuprestorergormsipermissionsnotpresent"></a>오류 코드: UserErrorDiskBackupRestoreRGOrMSIPermissionsNotPresent

오류 메시지: 대상 리소스 그룹이 없어서 작업에 실패했습니다. 또는 이 작업을 수행하려면 Azure Backup 서비스에 대상 리소스 그룹에 대한 추가 권한이 필요합니다.

권장 작업: 복원을 위해 올바른 리소스 그룹을 제공하고 백업 자격 증명 모음 관리 ID에 대상 리소스 그룹에 대한 적절한 권한을 부여합니다. 대상 리소스 그룹은 디스크를 복원하도록 선택된 위치입니다. 백업 자격 증명 모음 관리 ID에 필요한 권한과 이를 제공하는 방법을 이해하려면 [복원 설명서](restore-managed-disks.md)를 참조하세요.

### <a name="error-code-usererrordeskeyvaultkeydisabled"></a>오류 코드: UserErrorDESKeyVaultKeyDisabled

오류 메시지: 디스크 암호화 집합에 사용되는 키 자격 증명 모음 키가 사용 상태가 아닙니다.

권장 작업: 디스크 암호화 집합에 사용되는 키 자격 증명 모음 키를 사용하도록 설정합니다. [지원 매트릭스](disk-backup-support-matrix.md)에서 제한 사항을 참조하세요.

### <a name="error-code-usererrormsipermissionsnotpresentondes"></a>오류 코드: UserErrorMSIPermissionsNotPresentOnDES

오류 메시지: Azure Backup 서비스에는 디스크에서 사용되는 디스크 암호화 집합에 액세스할 수 있는 권한이 필요합니다.

권장 작업: 백업 자격 증명 모음 관리 ID에 대한 읽기 관리자 액세스를 디스크 암호화 집합(DES)에 제공합니다.

### <a name="error-code-usererrordeskeyvaultkeynotavailable"></a>오류 코드: UserErrorDESKeyVaultKeyNotAvailable

오류 메시지: 디스크 암호화 집합에 사용되는 키 자격 증명 모음 키를 사용할 수 없습니다.

권장 작업: 디스크 암호화 집합에 사용되는 키 자격 증명 모음 키가 사용하지 않도록 설정되거나 삭제되지 않았는지 확인합니다.

### <a name="error-code-usererrordisksnapshotnotfound"></a>오류 코드: UserErrorDiskSnapshotNotFound

오류 메시지: 이 복원 지점의 디스크 스냅샷이 삭제되었습니다.

권장 작업: 스냅샷은 구독 내의 스냅샷 데이터 저장소 리소스 그룹에 저장됩니다. 선택한 복원 지점과 관련된 스냅샷이 삭제되었거나 이 리소스 그룹에서 이동되었을 수 있습니다. 다른 복구 지점을 사용하여 복원해 보세요. 또한 [복원 설명서](restore-managed-disks.md)에서 언급한 스냅샷 리소스 그룹 선택에 대한 권장 지침을 따르세요.

### <a name="error-code-usererrorsnapshotmetadatanotfound"></a>오류 코드: UserErrorSnapshotMetadataNotFound

오류 메시지: 이 복원 지점의 디스크 스냅샷(메타데이터)이 삭제되었습니다.

권장 작업: 복원할 다른 복구 지점을 사용해 보세요. 자세한 내용은 [복원 설명서](restore-managed-disks.md)를 참조하세요.

### <a name="error-code-backupagentpluginhostvalidateprotectionerror"></a>오류 코드: BackupAgentPluginHostValidateProtectionError

오류 메시지: 보호 구성이 실행 중인 백업 자격 증명 모음의 지역에서 디스크 백업을 사용할 수 없습니다.

권장 작업: 백업 자격 증명 모음이 지원되는 지역에 있어야 합니다. 지역 가용성은 [지원 매트릭스](disk-backup-support-matrix.md)를 참조하세요.

### <a name="error-code-usererrordppdatasourcealreadyhasbackupinstance"></a>오류 코드: UserErrorDppDatasourceAlreadyHasBackupInstance

오류 메시지: 백업을 구성하려는 디스크가 이미 보호되고 있습니다. 디스크가 백업 자격 증명 모음의 백업 인스턴스와 이미 연결되어 있습니다.

권장 작업: 이 디스크는 백업 자격 증명 모음의 백업 인스턴스와 이미 연결되어 있습니다. 이 디스크를 다시 보호하려면 현재 보호되는 백업 자격 증명 모음에서 백업 인스턴스를 삭제하고 다른 자격 증명 모음에서 디스크를 다시 보호합니다.

### <a name="error-code-usererrordppdatasourcealreadyprotected"></a>오류 코드: UserErrorDppDatasourceAlreadyProtected

오류 메시지: 백업을 구성하려는 디스크가 이미 보호되고 있습니다. 디스크가 백업 자격 증명 모음의 백업 인스턴스와 이미 연결되어 있습니다.

권장 작업: 이 디스크는 백업 자격 증명 모음의 백업 인스턴스와 이미 연결되어 있습니다. 이 디스크를 다시 보호하려면 현재 보호되는 백업 자격 증명 모음에서 백업 인스턴스를 삭제하고 다른 자격 증명 모음에서 디스크를 다시 보호합니다.

### <a name="error-code-usererrormaxconcurrentoperationlimitreached"></a>오류 코드: UserErrorMaxConcurrentOperationLimitReached

오류 메시지: 허용되는 최대 동시 백업 수에 도달하여 작업을 시작할 수 없습니다.

권장 작업: 이전에 실행 중인 백업이 완료될 때까지 기다립니다.

### <a name="error-code-usererrormissingsubscriptionregistration"></a>오류 코드: UserErrorMissingSubscriptionRegistration

오류 메시지: 구독이 'Microsoft.Compute' 네임스페이스를 사용하도록 등록되어 있지 않습니다.

권장 작업: 필요한 리소스 공급자가 구독에 등록되지 않았습니다. [솔루션 3](../azure-resource-manager/templates/error-register-resource-provider.md#solution-3---azure-portal)의 단계를 사용하여 리소스 공급자의 네임스페이스(_Microsoft.Compute_ 및 _Microsoft.Storage_)를 모두 등록합니다.

## <a name="next-steps"></a>다음 단계

[Azure Disk Backup 지원 매트릭스](disk-backup-support-matrix.md)