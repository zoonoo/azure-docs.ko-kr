---
title: Azure VM 파일 복구 문제 해결
description: Azure VM 백업에서 파일 및 폴더 복구와 관련 된 문제를 해결 합니다.
ms.reviewer: srinathv
ms.topic: troubleshooting
ms.date: 07/12/2020
ms.openlocfilehash: 96876415405cc5099b4d9ca209c36086b793d6e0
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/15/2020
ms.locfileid: "97513408"
---
# <a name="troubleshooting-issues-in-file-recovery-of-azure-vm-backup"></a>Azure VM 백업의 파일 복구 문제 해결

이 문서에서는 Azure VM 백업에서 파일 및 폴더 복구와 관련 된 문제를 Azure Backup 해결 하는 데 도움이 되는 문제 해결 단계를 제공 합니다. 

## <a name="exception-caught-while-connecting-to-target"></a>대상에 연결 하는 동안 예외가 catch 되었습니다.

가능한 원인: 스크립트가 복구 지점 권장 작업에 액세스할 수 없습니다. 컴퓨터가 모든 [액세스 요구 사항을](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#step-4-access-requirements-to-successfully-run-the-script)충족 하는지 확인 합니다.

## <a name="the-target-has-already-been-logged-in-via-an-iscsi-session"></a>대상이 iSCSI 세션을 통해 이미 로그인 되었습니다.

가능한 원인: 스크립트가 이미 동일한 컴퓨터에서 실행 되었으며 드라이브가 연결 되었습니다.
권장 조치: 복구 지점의 볼륨이 이미 연결 되어 있습니다. 원본 VM의 동일한 드라이브 문자로 탑재 되지 않을 수 있습니다. 파일 탐색기에서 사용 가능한 모든 볼륨을 검색 합니다.

## <a name="this-script-is-invalid-because-the-disks-have-been-dismounted-via-portalexceeded-the-12-hr-limit-download-a-new-script-from-the-portal"></a>디스크가 포털을 통해 분리되었고 12시간 제한을 초과했으므로 이 스크립트는 유효하지 않습니다. 포털에서 새 스크립트를 다운로드 합니다.

가능한 원인: 디스크가 포털에서 분리 되었거나 12 시간 제한을 초과 했습니다.
권장 작업: 스크립트가 다운로드 된 시간부터 12 시간 후에 유효 하지 않으므로 실행할 수 없습니다. 포털을 방문 하 여 새 스크립트를 다운로드 하 여 파일 복구를 진행 합니다.

## <a name="troubleshooting-common-issues"></a>일반적인 문제 해결

이 섹션에서는 파일 복구 스크립트를 다운로드 하 고 실행 하는 동안 발생 한 문제를 해결 하는 단계를 제공 합니다.

## <a name="cannot-download-the-script"></a>스크립트를 다운로드할 수 없습니다.

권장 작업:

1. [스크립트를 다운로드 하는 데 필요한 모든 권한이](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#select-recovery-point-who-can-generate-script)있는지 확인 합니다.
2. Azure 대상 IP에 연결 되어 있는지 확인 합니다.
연결을 확인 하려면 관리자 권한 명령 프롬프트에서 다음 명령을 실행 합니다. 
    - *nslookup download.microsoft.com* 또는
    - *ping download.microsoft.com*

## <a name="the-script-downloads-successfully-but-fails-to-run"></a>스크립트가 성공적으로 다운로드 되지만 실행에 실패 함

### <a name="for-sles-12-sp4-os-linux"></a>SLES 12 SP4 OS (Linux)의 경우

오류: iscsi_tcp_module 찾을 수 없음

가능한 원인: SUSE Linux OS 버전 12sp4에서 항목 수준 복구를 위해 Python 스크립트를 실행 하는 동안 오류가 발생 하면 실패 합니다. ***iscsi_tcp 모듈을 로드할 수 없습니다**. ILR 모듈은 ' iscsi_tcp '를 사용 하 여 backup 서비스에 대 한 tcp 연결을 설정 하 고, SUSE SP4 릴리스의 일부로 오픈 iscsi 패키지에서 iscsi_tcp 제거 했으므로 ILR 작업이 실패 합니다.

권장 작업: SUSE 12SP4 Vm에서 File recovery 스크립트 실행이 지원 되지 않습니다. SUSE 12SP4 이전 버전에서 복원 작업을 시도 하세요.

## <a name="the-script-runs-but-connection-to-iscsi-target-failed"></a>스크립트가 실행 되지만 iSCSI 대상에 대 한 연결이 실패 했습니다.

오류: 대상에 연결 하는 동안 예외가 발생 했습니다.

1. 스크립트가 실행 되는 컴퓨터가 모든 [액세스 요구 사항을](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#step-4-access-requirements-to-successfully-run-the-script)충족 하는지 확인 합니다.
2. Azure 대상 IP에 대 한 연결을 확인 합니다.
연결을 확인 하려면 관리자 권한 명령 프롬프트에서 다음 명령을 실행 합니다. 
    - _nslookup download.microsoft.com * 또는<br>
    - *ping download.microsoft.com*
3. ISCSI 아웃 바운드 포트 3260에 대 한 액세스 권한이 있는지 확인 합니다.
4. Azure 대상 IP 또는 복구 서비스 Url에 대 한 트래픽을 차단 하는 방화벽/n s g가 없는지 확인 합니다.
5. 바이러스 백신이 스크립트의 실행을 차단 하는지 확인 합니다.

## <a name="connected-to-recovery-point-but-disks-did-not-get-attached"></a>복구 지점에 연결 되었지만 디스크가 연결 되지 않았습니다.

[스크립트를 실행할 올바른 컴퓨터가](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#step-2-ensure-the-machine-meets-the-requirements-before-executing-the-script) 있는지 확인 합니다.

### <a name="on-windows-vm"></a>Windows VM에서

**VM의 저장소 풀이 읽기 전용 모드로 연결 되어** 있습니다. Windows 2012 R2 및 Windows 2016 (저장소 풀 포함)의 경우 스크립트를 처음 실행 하는 동안 VM에 연결 된 저장소 풀이 읽기 전용 상태로 전환 될 수 있습니다.

이 문제를 해결 하려면 처음으로 저장소 풀에 대 한 Read-Write 액세스를 수동으로 설정 하 고 가상 디스크를 연결 해야 합니다. 아래 단계를 수행 합니다.

1. Read-Write 액세스를 설정 합니다.
서버 관리자 > 파일 및 저장소 서비스 > 볼륨 > 저장소 풀로 이동 합니다.

   ![Windows 저장소](./media/backup-azure-restore-files-from-vm/windows-storage-1.png)

2. **저장소 풀** 창에서 사용 가능한 저장소 풀을 마우스 오른쪽 단추로 클릭 하 고 **Read-Write 액세스 옵션 설정** 을 선택 합니다.

   ![Windows 저장소 읽기/쓰기](./media/backup-azure-restore-files-from-vm/windows-storage-read-write-2.png)

3. 읽기/쓰기 액세스 권한으로 저장소 풀을 할당 한 후에는 가상 디스크를 연결 해야 합니다.
**서버 관리자 UI** 로 이동 하 여 가상 디스크 섹션에서 > 마우스 오른쪽 단추를 클릭 하 여 **가상 디스크 연결** 옵션을 선택 합니다.

   ![서버 관리자 가상 디스크](./media/backup-azure-restore-files-from-vm/server-manager-virtual-disk-3.png)

### <a name="on-linux-vm"></a>Linux VM에서

#### <a name="file-recovery-fails-to-auto-mount-because-disk-does-not-contain-volumes"></a>디스크에 볼륨이 포함 되어 있지 않으므로 파일 복구를 자동 탑재 하지 못함

파일 복구를 수행 하는 동안 백업 서비스는 볼륨 및 자동 탑재를 검색 합니다. 그러나 백업 된 디스크에 raw 파티션이 있으면 해당 디스크가 자동으로 탑재 되지 않으며 복구를 위해 데이터 디스크를 볼 수 없습니다.

이 문제를 해결 하려면이 [문서](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#lvmraid-arrays-for-linux-vms)에 설명 된 단계를 따르세요.
 
#### <a name="os-could-not-identify-the-filesystem-causing-linux-file-recovery-to-fail-while-mountings-disks"></a>OS에서 디스크를 mountings 하는 동안 Linux 파일 복구에 실패 하는 파일 시스템을 확인할 수 없습니다.

파일 복구 스크립트를 실행 하는 동안 다음 오류와 함께 데이터 디스크를 연결 하지 못했습니다. *OS가 파일 시스템을 식별할 수 없어 다음 파티션을 탑재 하지 못했습니다* .

- 이 문제를 해결 하려면 볼륨이 타사 응용 프로그램을 사용 하 여 암호화 되었는지 확인 합니다. 암호화 된 경우에는 디스크 또는 VM이 포털에 암호화 된 상태로 표시 되지 않습니다.
1. 백업 된 VM에 로그인 하 고 다음 명령을 실행 합니다. *lsblk-f*<br>

   ![볼륨이 없는 디스크](./media/backup-azure-restore-files-from-vm/disk-without-volume-5.png)

2. 파일 시스템 및 암호화를 확인 합니다.
3. 볼륨이 암호화 된 경우에는 파일 복구가 지원 되지 않습니다. [자세히 알아보기](https://docs.microsoft.com/azure/backup/backup-support-matrix-iaas#support-for-file-level-restore).

## <a name="disks-are-attached-but-unable-to-mount-volumes"></a>디스크가 연결 되어 있지만 볼륨을 탑재할 수 없습니다.

- [스크립트를 실행할 올바른 컴퓨터가](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#step-2-ensure-the-machine-meets-the-requirements-before-executing-the-script)있는지 확인 합니다.

### <a name="on-windows-vm"></a>Windows VM에서

Windows에 대 한 파일 복구 스크립트를 실행 하는 동안 ***0 개의 볼륨이 연결** 되어 있지만 디스크는 디스크 관리 콘솔에서 검색 됩니다. ISCSI를 통해 볼륨을 연결 하는 동안 검색 되는 일부 볼륨이 오프 라인 상태로 전환 됩니다. ISCSI 채널은 VM과 서비스 간에 통신 하는 경우 이러한 볼륨을 검색 하 고 온라인 상태로 만들지만 탑재 되지 않습니다.

   ![디스크가 연결 되지 않음](./media/backup-azure-restore-files-from-vm/disk-not-attached-6.png)

이 문제를 식별 하 고 해결 하려면 다음 단계를 수행 합니다.

1. Cmd 창에서 **diskmgmt.msc** 명령을 실행 하 여 _ *디스크 관리**로 이동 합니다.
2. 추가 디스크가 표시 되는지 확인 합니다. 아래 예제에서 디스크 2는 추가 디스크입니다.

   ![디스크 management0](./media/backup-azure-restore-files-from-vm/disk-management-7.png)

3. **볼륨** 을 마우스 오른쪽 단추로 클릭 하 고 **드라이브 문자 및 경로 변경** 을 선택 합니다.

   ![디스크 management1](./media/backup-azure-restore-files-from-vm/disk-management-8.png)

4. **드라이브 문자 또는 경로 추가** 창에서 **다음 드라이브 문자 할당** 을 선택 하 고 사용 가능한 드라이브를 할당 한 후 **확인** 을 클릭 합니다. 

   ![디스크 management2](./media/backup-azure-restore-files-from-vm/disk-management-9.png)

5. 파일 탐색기에서 드라이브를 볼 수 있습니다.

   ![디스크 management3](./media/backup-azure-restore-files-from-vm/disk-management-10.png)

6. 새 볼륨이 연결 됩니다.  

   ![디스크가 탑재 되지 않음](./media/backup-azure-restore-files-from-vm/disk-not-mounting-11.png)

7. 파일 탐색기에서 스크립트를 다시 실행 한 후 새 볼륨이 표시 됩니다.

   ![디스크가 mounting1 되지 않음](./media/backup-azure-restore-files-from-vm/disk-not-mounting-12.png)

   ![디스크가 mounting2 되지 않음](./media/backup-azure-restore-files-from-vm/disk-not-mounting-13.png)

#### <a name="on-linux-vms"></a>Linux Vm에서 

- [스크립트를 실행할 올바른 컴퓨터가](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#step-2-ensure-the-machine-meets-the-requirements-before-executing-the-script)있는지 확인 합니다.
- 보호 된 Linux VM에서 LVM 및/또는 RAID 배열을 사용 하는 경우이 [문서](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#lvmraid-arrays-for-linux-vms)에 나열 된 단계를 수행 합니다.

## <a name="cannot-copy-the-files-from-mounted-volumes"></a>탑재 된 볼륨에서 파일을 복사할 수 없습니다.

0x80070780 오류가 발생 하 여 복사가 실패할 수 있습니다. 시스템에서 파일에 액세스할 수 없습니다. 이 경우 원본 서버에서 디스크 중복 제거를 사용 하도록 설정 했는지 확인 합니다. 그런 다음 복원 서버에서 드라이브에 대 한 중복 제거를 사용 하도록 설정 해야 합니다. 복원 서버에서 드라이브를 중복 제거 하지 않도록 중복 제거 역할을 구성 되지 않은 상태로 둘 수 있습니다.
