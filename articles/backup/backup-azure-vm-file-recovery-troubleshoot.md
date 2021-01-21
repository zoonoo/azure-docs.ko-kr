---
title: Azure VM 파일 복구 문제 해결
description: Azure VM 백업에서 파일 및 폴더를 복구할 때 발생 하는 문제를 해결 합니다.
ms.topic: troubleshooting
ms.date: 07/12/2020
ms.openlocfilehash: aec69b91ad1dae5864e5e8fba61c53e6d15887f4
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/21/2021
ms.locfileid: "98624509"
---
# <a name="troubleshoot-issues-in-file-recovery-of-an-azure-vm-backup"></a>Azure VM 백업의 파일 복구 문제 해결

이 문서에서는 Azure VM (가상 머신) 백업에서 파일 및 폴더를 복구 하는 문제를 해결 하는 데 도움이 되는 문제 해결 단계를 제공 합니다.

## <a name="common-error-messages"></a>일반적인 오류 메시지

이 섹션에서는 표시 될 수 있는 오류 메시지를 해결 하는 단계를 제공 합니다.

### <a name="exception-caught-while-connecting-to-target"></a>"대상에 연결 하는 동안 예외가 발생 했습니다."

**가능한 원인**: 스크립트가 복구 지점에 액세스할 수 없습니다.

**권장 작업**:이 문제를 해결 하려면 스크립트 실행에 나열 된 단계를 수행 [하지만 연결에 실패 했습니다](#the-script-runs-but-the-connection-to-the-iscsi-target-failed).

### <a name="the-target-has-already-been-logged-in-via-an-iscsi-session"></a>"ISCSI 세션을 통해 대상이 이미 로그인 되었습니다."

**가능한 원인**: 스크립트가 이미 동일한 컴퓨터에서 실행 되었으며 드라이브가 연결 되어 있습니다.

**권장 조치**: 복구 지점의 볼륨이 이미 연결 되어 있습니다. 원본 VM의 동일한 드라이브 문자로 탑재할 수 없습니다. 파일 탐색기에서 사용 가능한 볼륨을 찾아봅니다.

### <a name="this-script-is-invalid-because-the-disks-have-been-dismounted-via-portalexceeded-the-12-hr-limit-download-a-new-script-from-the-portal"></a>"이 스크립트는 포털을 통해 디스크가 분리 되었거나 12 시간 제한을 초과 했기 때문에 유효 하지 않습니다. 포털에서 새 스크립트를 다운로드 합니다. "

**가능한 원인**: 디스크가 포털에서 분리 되었거나 12 시간 제한을 초과 했습니다.

**권장 작업**: 스크립트를 다운로드 한 후 12 시간 후에는 스크립트를 실행할 수 없습니다. 포털로 이동한 다음 새 스크립트를 다운로드 하 여 파일 복구를 계속 합니다.

### <a name="iscsi_tcp-module-cant-be-loaded-or-iscsi_tcp_module-not-found"></a>iscsi_tcp 모듈을 로드할 수 없습니다 (또는) iscsi_tcp_module 찾을 수 없습니다.

**권장 작업**:이 문제를 해결 하려면 스크립트 다운로드에 대 한 단계를 수행 [하지만 실행에 실패](#the-script-downloads-successfully-but-fails-to-run)합니다.

## <a name="common-problems"></a>일반적인 문제

이 섹션에서는 파일 복구 스크립트를 다운로드 하 고 실행 하는 동안 발생할 수 있는 일반적인 문제를 해결 하는 단계를 제공 합니다.

### <a name="you-cant-download-the-script"></a>스크립트를 다운로드할 수 없습니다.

1. [스크립트를 다운로드 하는 데 필요한 권한이](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#select-recovery-point-who-can-generate-script)있는지 확인 합니다.
1. Azure 대상 Ip에 대 한 연결을 확인 합니다. 관리자 권한 명령 프롬프트에서 다음 명령 중 하나를 실행 합니다.

   `nslookup download.microsoft.com`

    또는

    `ping download.microsoft.com`

### <a name="the-script-downloads-successfully-but-fails-to-run"></a>스크립트가 성공적으로 다운로드 되지만 실행에 실패 함

SUSE Linux Enterprise Server 12 s p 4에서 항목 수준 복구 (ILR)에 대 한 Python 스크립트를 실행 하면 "iscsi_tcp 모듈을 로드할 수 없습니다." 또는 "iscsi_tcp_module를 찾을 수 없습니다" 라는 오류와 함께 실패 합니다.

**가능한 원인**: ILR 모듈은 **iscsi_tcp** 를 사용 하 여 백업 서비스에 대 한 tcp 연결을 설정 합니다. SLES 12 SP4 릴리스의 일부로 SUSE가 오픈 iscsi 패키지에서 **iscsi_tcp** 제거 되었으므로 ILR 작업이 실패 합니다.

**권장 조치**: SUSE 12 SP4 vm에서는 파일 복구 스크립트 실행이 지원 되지 않습니다. 이전 버전의 SUSE 12 SP4에 대해 복원 작업을 시도 합니다.

### <a name="the-script-runs-but-the-connection-to-the-iscsi-target-failed"></a>스크립트가 실행 되지만 iSCSI 대상에 대 한 연결이 실패 했습니다.

"대상에 연결 하는 동안 예외가 catch 되었습니다." 라는 오류 메시지가 표시 될 수 있습니다.

1. 스크립트가 실행 되는 컴퓨터가 [액세스 요구 사항을](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#step-4-access-requirements-to-successfully-run-the-script)충족 하는지 확인 합니다.
1. Azure 대상 Ip에 대 한 연결을 확인 합니다. 관리자 권한 명령 프롬프트에서 다음 명령 중 하나를 실행 합니다.

   `nslookup download.microsoft.com`

   또는

   `ping download.microsoft.com`
1. ISCSI 아웃 바운드 포트 3260에 대 한 액세스를 확인 합니다.
1. Azure 대상 Ip 또는 복구 서비스 Url에 대 한 방화벽 또는 NSG 차단 트래픽을 확인 합니다.
1. 바이러스 백신 소프트웨어가 스크립트 실행을 차단 하지 않는지 확인 합니다.

### <a name="youre-connected-to-the-recovery-point-but-the-disks-werent-attached"></a>복구 지점에 연결 되었지만 디스크가 연결 되어 있지 않습니다.

운영 체제에 대 한 단계를 수행 하 여이 문제를 해결 합니다.

#### <a name="windows-file-recovery-fails-on-server-with-storage-pools"></a>저장소 풀을 사용 하는 서버에서 Windows 파일 복구가 실패 함

Windows Server 2012 R2 및 Windows Server 2016에서 처음으로 스크립트를 실행 하면 (저장소 풀을 사용 하 여) 저장소 풀이 읽기 전용으로 VM에 연결 될 수 있습니다.

>[!Tip]
> [스크립트를 실행할 올바른 컴퓨터가](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#step-2-ensure-the-machine-meets-the-requirements-before-executing-the-script)있는지 확인 합니다.

이 문제를 해결 하려면 저장소 풀에 읽기/쓰기 액세스 권한을 수동으로 할당 하 고 가상 디스크를 연결 합니다.

1. **서버 관리자**  >  **파일 및 저장소 서비스**  >  **볼륨**  >  **저장소 풀** 로 이동 합니다.

   ![저장소 풀 옵션을 보여 주는 스크린샷](./media/backup-azure-restore-files-from-vm/windows-storage-1.png)

1. **저장소 풀** 창에서 사용 가능한 저장소 풀을 마우스 오른쪽 단추로 클릭 하 고 **Read-Write 액세스 설정** 을 선택 합니다.

   ![저장소 스풀의 오른쪽 클릭 옵션을 보여 주는 스크린샷](./media/backup-azure-restore-files-from-vm/windows-storage-read-write-2.png)

1. 저장소 풀에 읽기/쓰기 액세스 권한이 할당 된 후 **가상 디스크** 섹션을 마우스 오른쪽 단추로 클릭 하 고 **가상 디스크 연결** 을 선택 합니다.

   ![가상 디스크에 대 한 마우스 오른쪽 단추 클릭 옵션을 보여 주는 스크린샷](./media/backup-azure-restore-files-from-vm/server-manager-virtual-disk-3.png)

#### <a name="linux-file-recovery-fails-to-auto-mount-because-the-disk-doesnt-contain-volumes"></a>디스크가 볼륨을 포함 하지 않으므로 Linux 파일 복구를 자동 탑재 하지 못함

파일 복구를 수행 하는 동안 백업 서비스는 볼륨 및 자동 탑재를 검색 합니다. 그러나 백업 된 디스크에 raw 파티션이 있는 경우에는 해당 디스크가 자동으로 탑재 되지 않으며 복구를 위해 데이터 디스크를 볼 수 없습니다.

이 문제를 해결 하려면 [Azure 가상 컴퓨터 백업에서 파일 복구](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#lvmraid-arrays-for-linux-vms)로 이동 합니다.

#### <a name="linux-file-recovery-fails-because-the-os-couldnt-identify-the-file-system"></a>OS에서 파일 시스템을 식별할 수 없기 때문에 Linux 파일 복구에 실패 합니다.

파일 복구 스크립트를 실행 하면 데이터 디스크가 연결 되지 않습니다. "OS에서 파일 시스템을 식별할 수 없어서 다음 파티션을 탑재 하지 못했습니다." 오류가 표시 됩니다.

이 문제를 해결 하려면 볼륨이 타사 응용 프로그램을 사용 하 여 암호화 되었는지 확인 합니다. 암호화 된 경우에는 디스크 또는 VM이 포털에 암호화 된 상태로 표시 되지 않습니다.

1. 백업 된 VM에 로그인 하 고 다음 명령을 실행 합니다.

   `lsblk -f`

   ![블록 장치를 나열 하는 명령의 결과를 보여 주는 스크린샷](./media/backup-azure-restore-files-from-vm/disk-without-volume-5.png)

1. 파일 시스템 및 암호화를 확인 합니다. 볼륨이 암호화 된 경우에는 파일 복구가 지원 되지 않습니다. [AZURE VM 백업에 대 한 지원 매트릭스](https://docs.microsoft.com/azure/backup/backup-support-matrix-iaas#support-for-file-level-restore)를 자세히 알아보세요.

### <a name="disks-are-attached-but-the-volumes-arent-mounted"></a>디스크가 연결 되었지만 볼륨이 탑재 되어 있지 않습니다.

운영 체제에 대 한 단계를 수행 하 여이 문제를 해결 합니다.

#### <a name="windows"></a>Windows

Windows 용 파일 복구 스크립트를 실행 하면 "0 개의 복구 볼륨이 연결 되어 있습니다." 라는 메시지가 표시 됩니다. 그러나 디스크는 디스크 관리 콘솔에서 검색 됩니다.

**가능한 원인**: iSCSI를 통해 볼륨을 연결 하면 검색 된 일부 볼륨이 오프 라인 상태가 된 것입니다. ISCSI 채널은 VM과 서비스 간에 통신 하는 경우 이러한 볼륨을 검색 하 고 온라인 상태로 만들지만 탑재 되지 않습니다.

   ![연결 된 0 개의 복구 볼륨을 보여 주는 스크린샷](./media/backup-azure-restore-files-from-vm/disk-not-attached-6.png)

이 문제를 식별 하 고 해결 하려면 다음 단계를 수행 합니다.

>[!Tip]
>[스크립트를 실행할 올바른 컴퓨터가](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#step-2-ensure-the-machine-meets-the-requirements-before-executing-the-script)있는지 확인 합니다.

1. **Cmd** 창에서 **diskmgmt.msc** 를 실행 하 여 **디스크 관리** 를 엽니다.
1. 추가 디스크를 찾습니다. 다음 예제에서 **디스크 2** 는 추가 디스크입니다.

   ![추가 디스크를 사용 하는 디스크 관리 창의 스크린샷](./media/backup-azure-restore-files-from-vm/disk-management-7.png)

1. **새 볼륨** 을 마우스 오른쪽 단추로 클릭 하 고 **드라이브 문자 및 경로 변경** 을 선택 합니다.

   ![추가 디스크의 마우스 오른쪽 단추 클릭 옵션을 보여 주는 스크린샷](./media/backup-azure-restore-files-from-vm/disk-management-8.png)

1. **드라이브 문자 또는 경로 변경** 창에서 **다음 드라이브 문자 할당** 을 선택 하 고 사용 가능한 드라이브를 할당 한 다음 **확인** 을 선택 합니다.

   ![드라이브 문자 또는 경로 변경 창의 스크린샷](./media/backup-azure-restore-files-from-vm/disk-management-9.png)

1. 파일 탐색기를 열어 선택한 드라이브를 확인 하 고 파일을 탐색 합니다.

#### <a name="linux"></a>Linux

>[!Tip]
>[스크립트를 실행할 올바른 컴퓨터가](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#step-2-ensure-the-machine-meets-the-requirements-before-executing-the-script)있는지 확인 합니다.

보호 된 Linux VM에서 LVM 또는 RAID 배열을 사용 하는 경우 [Azure virtual machine 백업에서 파일 복구](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#lvmraid-arrays-for-linux-vms)의 단계를 따릅니다.

### <a name="you-cant-copy-the-files-from-mounted-volumes"></a>탑재 된 볼륨에서 파일을 복사할 수 없습니다.

"0x80070780: 시스템에서 파일에 액세스할 수 없습니다." 오류가 발생 하 여 복사가 실패할 수 있습니다. 

원본 서버에서 디스크 중복 제거를 사용 하도록 설정 했는지 확인 합니다. 그래도 복원 서버에서 드라이브에 중복 제거를 사용 하도록 설정 해야 합니다. 복원 서버의 드라이브가 중복 제거 되지 않도록 중복 제거를 구성 되지 않은 상태로 둘 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [Azure 가상 컴퓨터 백업에서 파일 및 폴더 복구](backup-azure-restore-files-from-vm.md)
