---
title: Azure VM 파일 복구 문제 해결
description: Azure VM 백업에서 파일 및 폴더를 복구할 때 발생하는 문제를 해결합니다.
ms.topic: troubleshooting
ms.date: 07/12/2020
ms.openlocfilehash: 0ee856b10c6a5fbea6f6f76b2082949ab9c1e0db
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101700306"
---
# <a name="troubleshoot-issues-in-file-recovery-of-an-azure-vm-backup"></a>Azure VM 백업의 파일 복구 문제 해결

이 문서에서는 Azure VM(가상 머신) 백업에서 파일 및 폴더를 복구할 때 발생하는 문제를 해결하는 데 도움이 되는 문제 해결 단계를 제공합니다.

## <a name="common-error-messages"></a>일반적인 오류 메시지

이 섹션에서는 표시될 수 있는 오류 메시지를 해결하는 단계를 제공합니다.

### <a name="exception-caught-while-connecting-to-target"></a>"대상에 연결하는 동안 예외가 catch되었습니다."

**가능한 원인**: 스크립트가 복구 지점에 액세스할 수 없습니다.

**권장 조치**: 이 문제를 해결하려면 [스크립트가 실행되지만 연결에 실패함](#the-script-runs-but-the-connection-to-the-iscsi-target-failed)에 나열된 단계를 따르세요.

### <a name="the-target-has-already-been-logged-in-via-an-iscsi-session"></a>"iSCSI 세션을 통해 대상이 이미 로그인되었습니다."

**가능한 원인**: 동일한 머신에서 스크립트가 이미 실행되었고 드라이브가 연결되었습니다.

**권장 조치**: 복구 지점의 볼륨이 이미 연결되었습니다. 원래 VM과 동일한 드라이브 문자로 탑재되지 않을 수 있습니다. 파일 탐색기에서 사용 가능한 볼륨을 찾아보세요.

### <a name="this-script-is-invalid-because-the-disks-have-been-dismounted-via-portalexceeded-the-12-hr-limit-download-a-new-script-from-the-portal"></a>"디스크가 포털을 통해 분리되었고 12시간 제한을 초과했으므로 이 스크립트는 유효하지 않습니다. 포털에서 새 스크립트를 다운로드하세요."

**가능한 원인**: 디스크가 포털에서 분리되었거나 12시간 제한을 초과했습니다.

**권장 조치**: 스크립트를 다운로드한 후 12시간이 지나면 스크립트가 유효하지 않게 되고 실행할 수 없습니다. 포털로 이동한 후 새 스크립트를 다운로드하여 파일 복구를 계속합니다.

### <a name="iscsi_tcp-module-cant-be-loaded-or-iscsi_tcp_module-not-found"></a>iscsi_tcp 모듈을 로드할 수 없거나 iscsi_tcp_모듈을 찾을 수 없음

**권장 조치**: 이 문제를 해결하려면 [스크립트 다운로드에 성공했지만 실행에 실패함](#the-script-downloads-successfully-but-fails-to-run)에 나와 있는 단계를 따르세요.

## <a name="common-problems"></a>일반적인 문제

이 섹션에서는 파일 복구 스크립트를 다운로드하고 실행 하는 동안 발생할 수 있는 일반적인 문제를 해결하기 위한 단계를 제공합니다.

### <a name="you-cant-download-the-script"></a>스크립트를 다운로드할 수 없음

1. [스크립트를 다운로드하는 데 필요한 권한](./backup-azure-restore-files-from-vm.md#select-recovery-point-who-can-generate-script)이 있는지 확인합니다.
1. Azure 대상 IP에 대한 연결을 확인합니다. 이렇게 하려면 관리자 권한 명령 프롬프트에서 다음 명령 중 하나를 사용합니다.

   `nslookup download.microsoft.com`

    또는

    `ping download.microsoft.com`

### <a name="the-script-downloads-successfully-but-fails-to-run"></a>스크립트가 성공적으로 다운로드되었지만 실행에 실패함

SUSE Linux Enterprise Server 12 SP4에서 ILR(항목 수준 복구)를 위한 Python 스크립트를 실행하면 "iscsi_tcp 모듈을 로드할 수 없음" 또는 "iscsi_tcp_module을 찾을 수 없음" 오류와 함께 실패합니다.

**가능한 원인**: ILR 모듈은 **iscsi_tcp** 를 사용하여 백업 서비스에 대한 TCP 연결을 설정합니다. SLES 12 SP4 릴리스의 일부로 open-iscsi 패키지에서 **iscsi_tcp** 가 제거되었으므로 ILR 작업은 실패합니다.

**권장 조치**: SUSE 12 SP4 VM에서는 파일 복구 스크립트 실행이 지원되지 않습니다. 이전 버전의 SUSE 12 SP4에서 복원 작업을 시도해 보세요.

### <a name="the-script-runs-but-the-connection-to-the-iscsi-target-failed"></a>스크립트가 실행되었지만 iSCSI 대상에 대한 연결이 실패함

"대상에 연결하는 동안 예외가 catch되었습니다" 라는 오류 메시지가 표시될 수 있습니다.

1. 스크립트가 실행되는 머신이 [액세스 요구 사항](./backup-azure-restore-files-from-vm.md#step-4-access-requirements-to-successfully-run-the-script)을 충족하는지 확인하세요.
1. Azure 대상 IP에 대한 연결을 확인합니다. 이렇게 하려면 관리자 권한 명령 프롬프트에서 다음 명령 중 하나를 사용합니다.

   `nslookup download.microsoft.com`

   또는

   `ping download.microsoft.com`
1. iSCSI 아웃바운드 포트 3260에 대한 액세스를 확인합니다.
1. Azure 대상 IP 또는 복구 서비스 URL에 대한 트래픽을 차단하는 방화벽 또는 NSG를 확인합니다.
1. 바이러스 백신 소프트웨어가 스크립트 실행을 차단하지 않는지 확인합니다.

### <a name="youre-connected-to-the-recovery-point-but-the-disks-werent-attached"></a>복구 지점에 연결되었지만 디스크가 연결되지 않음

운영 체제에 해당하는 단계를 수행하여 이 문제를 해결합니다.

#### <a name="windows-file-recovery-fails-on-server-with-storage-pools"></a>스토리지 풀을 사용하는 서버에서 Windows 파일 복구가 실패함

스토리지 풀을 사용하는 Windows Server 2012 R2 및 Windows Server 2016에서 처음으로 스크립트를 실행하면 스토리지 풀이 읽기 전용으로 VM에 연결될 수 있습니다.

>[!Tip]
> [스크립트를 실행할 적절한 머신](./backup-azure-restore-files-from-vm.md#step-2-ensure-the-machine-meets-the-requirements-before-executing-the-script)이 있는지 확인합니다.

이 문제를 해결하려면 스토리지 풀에 읽기-쓰기 액세스 권한을 수동으로 할당하고 가상 디스크를 연결합니다.

1. **서버 관리자** > **파일 및 스토리지 서비스** > **볼륨** > **스토리지 풀** 을 선택합니다.

   ![스토리지 풀 옵션을 보여 주는 스크린샷](./media/backup-azure-restore-files-from-vm/windows-storage-1.png)

1. **스토리지 풀** 창에서 사용 가능한 스토리지 풀을 마우스 오른쪽 단추로 클릭하고 **읽기-쓰기 액세스 설정** 을 선택합니다.

   ![스토리지 풀의 마우스 오른쪽 단추 클릭 옵션을 보여 주는 스크린샷](./media/backup-azure-restore-files-from-vm/windows-storage-read-write-2.png)

1. 스토리지 풀에 읽기/쓰기 액세스 권한이 할당되면 **가상 디스크** 섹션을 마우스 오른쪽 단추로 클릭한 후 **가상 디스크 연결** 을 선택합니다.

   ![가상 디스크의 마우스 오른쪽 단추 클릭 옵션을 보여 주는 스크린샷](./media/backup-azure-restore-files-from-vm/server-manager-virtual-disk-3.png)

#### <a name="linux-file-recovery-fails-to-auto-mount-because-the-disk-doesnt-contain-volumes"></a>디스크에 볼륨이 포함되지 않아 Linux 파일 복구에서 자동으로 탑재하지 못함

백업 서비스는 파일 복구를 수행하는 동안 볼륨 및 자동 탑재를 검색합니다. 그러나 백업된 디스크에 원시 파티션이 있는 경우에는 해당 디스크가 자동으로 탑재되지 않으며 복구할 데이터 디스크가 표시되지 않습니다.

이 문제를 해결하려면 [Azure 가상 머신 백업에서 파일 복구](./backup-azure-restore-files-from-vm.md#lvmraid-arrays-for-linux-vms)로 이동합니다.

#### <a name="linux-file-recovery-fails-because-the-os-couldnt-identify-the-file-system"></a>OS에서 파일 시스템을 식별하지 못해 Linux 파일 복구에 실패함

파일 복구 스크립트를 실행하면 데이터 디스크가 연결되지 않습니다. "OS에서 식별하지 못해 다음 파티션을 탑재하지 못했습니다." 오류가 표시됩니다.

이 문제를 해결하려면 볼륨이 타사 애플리케이션을 사용하여 암호화되었는지 확인합니다. 암호화된 경우에는 디스크 또는 VM이 포털에 암호화된 상태로 표시되지 않습니다.

1. 백업된 VM에 로그인하고 다음 명령을 실행합니다.

   `lsblk -f`

   ![블록 디바이스를 나열하는 명령의 결과를 보여 주는 스크린샷](./media/backup-azure-restore-files-from-vm/disk-without-volume-5.png)

1. 파일 시스템 및 암호화를 확인합니다. 볼륨이 암호화된 경우에는 파일 복구가 지원되지 않습니다. [Azure VM 백업에 대한 지원 매트릭스](./backup-support-matrix-iaas.md#support-for-file-level-restore)에서 자세히 알아보세요.

### <a name="disks-are-attached-but-the-volumes-arent-mounted"></a>디스크가 연결되었지만 볼륨이 탑재되지 않음

운영 체제에 해당하는 단계를 수행하여 이 문제를 해결합니다.

#### <a name="windows"></a>Windows

Windows용 파일 복구 스크립트를 실행하면 "0개의 복구 볼륨이 연결되어 있습니다."라는 메시지가 표시됩니다. 그러나 디스크 관리 콘솔에서 디스크가 검색됩니다.

**가능한 원인**: iSCSI를 통해 볼륨을 연결한 경우 검색된 일부 볼륨이 오프라인 상태가 된 것입니다. ISCSI 채널이 VM과 서비스 간에 통신하는 경우 이러한 볼륨을 검색하고 온라인 상태로 만들지만 해당 볼륨이 탑재되지는 않습니다.

   ![연결된 복구 볼륨이 0개임을 보여 주는 스크린샷](./media/backup-azure-restore-files-from-vm/disk-not-attached-6.png)

이 문제를 파악하고 해결하려면 다음 단계를 수행합니다.

>[!Tip]
>[스크립트를 실행할 적절한 머신](./backup-azure-restore-files-from-vm.md#step-2-ensure-the-machine-meets-the-requirements-before-executing-the-script)이 있는지 확인합니다.

1. **cmd** 창에서 **diskmgmt** 를 실행하여 **디스크 관리** 를 엽니다.
1. 추가 디스크를 찾습니다. 다음 예제에서 **디스크 2** 는 추가 디스크입니다.

   ![추가 디스크를 사용하는 디스크 관리 창의 스크린샷](./media/backup-azure-restore-files-from-vm/disk-management-7.png)

1. **새 볼륨** 을 마우스 오른쪽 단추로 클릭한 후 **드라이브 문자 및 경로 변경** 을 선택합니다.

   ![추가 디스크의 마우스 오른쪽 단추 클릭 옵션을 보여 주는 스크린샷](./media/backup-azure-restore-files-from-vm/disk-management-8.png)

1. **드라이브 문자 또는 경로 변경** 창에서 **다음 드라이브 문자 할당** 을 선택하고 사용 가능한 드라이브를 할당한 다음, **확인** 을 선택합니다.

   ![드라이브 문자 또는 경로 변경 창의 스크린샷](./media/backup-azure-restore-files-from-vm/disk-management-9.png)

1. 파일 탐색기를 열어 선택한 드라이브를 확인하고 파일을 찾아봅니다.

#### <a name="linux"></a>Linux

>[!Tip]
>[스크립트를 실행할 적절한 머신](./backup-azure-restore-files-from-vm.md#step-2-ensure-the-machine-meets-the-requirements-before-executing-the-script)이 있는지 확인합니다.

보호된 Linux VM에서 LVM 또는 RAID 배열을 사용하는 경우 [Azure 가상 머신 백업에서 파일 복구](./backup-azure-restore-files-from-vm.md#lvmraid-arrays-for-linux-vms)의 단계를 따릅니다.

### <a name="you-cant-copy-the-files-from-mounted-volumes"></a>탑재된 볼륨에서 파일을 복사할 수 없음

"0x80070780: 시스템에서 파일에 액세스할 수 없습니다." 오류가 발생하여 복사가 실패할 수 있습니다. 

원본 서버에서 디스크 중복 제거를 사용하도록 설정했는지 확인합니다. 그렇다면 복원 서버도 드라이브에 중복 제거를 사용하도록 설정해야 합니다. 복원 서버의 드라이브가 중복 제거되지 않도록 중복 제거를 구성되지 않은 상태로 둘 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [Azure 가상 머신 백업에서 파일 및 폴더 복구](backup-azure-restore-files-from-vm.md)