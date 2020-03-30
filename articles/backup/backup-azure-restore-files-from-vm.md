---
title: Azure VM 백업에서 파일 및 폴더 복구
description: 이 문서에서는 Azure 가상 시스템 복구 지점에서 파일 및 폴더를 복구하는 방법을 알아봅니다.
ms.topic: conceptual
ms.date: 03/01/2019
ms.openlocfilehash: 0e3061ea8fc26adcf39fe415cd9a662de739543a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79273307"
---
# <a name="recover-files-from-azure-virtual-machine-backup"></a>Azure Virtual Machine 백업에서 파일 복구

Azure Backup에서는 복구 지점이라고도 하는 Azure VM 백업에서 [Azure VM(가상 머신) 및 디스크](./backup-azure-arm-restore-vms.md)를 복원하는 기능을 제공합니다. 이 문서에서는 Azure VM Backup에서 파일 및 폴더를 어떻게 복구할 수 있는지 설명합니다. 파일 및 폴더 복원은 Resource Manager 모델을 사용하여 배포된 Azure VM에서만 사용 가능하며 Recovery Services 자격 증명 모음에 대해 보호됩니다.

> [!NOTE]
> 이 기능은 Resource Manager 모델을 사용하여 배포된 Azure VM에서 사용 가능하며 Recovery Services 자격 증명 모음에 대해 보호됩니다.
> 암호화된 VM 백업으로부터 파일 복구는 지원되지 않습니다.
>

## <a name="mount-the-volume-and-copy-files"></a>볼륨 탑재 및 파일 복사

복구 지점에서 파일 또는 폴더를 복원하려면 가상 머신으로 이동하고 원하는 복구 지점을 선택합니다.

1. [Azure Portal](https://portal.Azure.com)에 로그인하고 왼쪽 창에서 **가상 머신**를 클릭합니다. 가상 머신 목록에서 가상 머신을 선택하여 해당 가상 머신의 대시보드를 엽니다.

2. 가상 머신의 메뉴에서 **Backup**을 클릭하여 Backup 대시보드를 엽니다.

    ![Recovery Services 자격 증명 모음 백업 항목 열기](./media/backup-azure-restore-files-from-vm/open-vault-for-vm.png)

3. Backup 대시보드 메뉴에서 **파일 복구**를 클릭합니다.

    ![파일 복구 단추](./media/backup-azure-restore-files-from-vm/vm-backup-menu-file-recovery-button.png)

    **파일 복구** 메뉴가 열립니다.

    ![파일 복구 메뉴](./media/backup-azure-restore-files-from-vm/file-recovery-blade.png)

4. **복구 지점 선택** 드롭다운 메뉴에서 원하는 파일이 있는 복구 지점을 선택합니다. 기본적으로 최신 복구 지점을 선택합니다.

5. 복구 지점에서 파일을 복사하는 데 사용되는 소프트웨어를 다운로드하려면 **실행 파일 다운로드(Windows** Azure VM의 경우) 또는 **스크립트 다운로드(Linux** Azure VM의 경우 파이썬 스크립트가 생성됩니다)를 클릭합니다.

    ![생성된 암호](./media/backup-azure-restore-files-from-vm/download-executable.png)

    Azure가 실행 파일 또는 스크립트를 로컬 컴퓨터로 다운로드합니다.

    ![실행 파일 또는 스크립트에 대한 다운로드 메시지](./media/backup-azure-restore-files-from-vm/run-the-script.png)

    실행 파일 또는 스크립트를 관리자로 실행하려면 다운로드한 파일을 컴퓨터에 저장하는 것이 좋습니다.

6. 실행 파일 또는 스크립트는 암호로 보호되어 암호가 필요합니다. **파일 복구** 메뉴에서 복사 버튼을 클릭하여 암호를 메모리에 로드합니다.

    ![생성된 암호](./media/backup-azure-restore-files-from-vm/generated-pswd.png)

7. 다운로드 위치(일반적으로 다운로드 폴더)에서 실행 파일 또는 스크립트를 마우스 오른쪽 단추로 클릭하고 관리자 자격 증명을 사용하여 실행합니다. 메시지가 표시되면 암호를 입력하거나 메모리에서 암호를 붙여 넣은 다음 **Enter**를 누릅니다. 올바른 암호를 입력하면 스크립트가 복구 지점에 연결됩니다.

    ![파일 복구 메뉴](./media/backup-azure-restore-files-from-vm/executable-output.png)

8. Linux 컴퓨터의 경우 파이썬 스크립트가 생성됩니다. 하나는 스크립트를 다운로드하고 관련 / 호환 리눅스 서버에 복사해야합니다. 을 사용하여 ```chmod +x <python file name>```실행하려면 권한을 수정해야 할 수 있습니다. 그런 다음 를 ```./<python file name>```통해 파이썬 파일을 실행합니다.

[스크립트가](#access-requirements) 성공적으로 실행되었는지 확인하려면 액세스 요구 사항 섹션을 참조하십시오.

### <a name="identifying-volumes"></a>볼륨 식별

#### <a name="for-windows"></a>Windows의 경우

실행 파일을 실행하면 운영 체제는 새 볼륨을 탑재하고 드라이브 문자를 할당합니다. Windows 탐색기 또는 파일 탐색기를 사용하여 해당 드라이브를 탐색할 수 있습니다. 볼륨에 할당된 드라이브 문자는 원래 가상 컴퓨터와 동일한 문자가 아닐 수 있습니다. 그러나 볼륨 이름은 유지됩니다. 예를 들어 원래 가상 컴퓨터의 볼륨이 "데이터 디스크(E:`\`)"인 경우 해당 볼륨을 로컬 컴퓨터에 "데이터`\`디스크('모든 문자': )"로 연결할 수 있습니다. 파일이나 폴더를 찾을 때까지 스크립트 출력에 언급된 모든 볼륨을 탐색합니다.  

   ![파일 복구 메뉴](./media/backup-azure-restore-files-from-vm/volumes-attached.png)

#### <a name="for-linux"></a>Linux의 경우

Linux에서 복구 지점의 볼륨은 스크립트가 실행되는 폴더에 탑재됩니다. 연결된 디스크, 볼륨 및 해당 탑재 경로는 적절하게 표시됩니다. 이러한 탑재 경로는 루트 수준 액세스 권한이 있는 사용자에게 표시됩니다. 스크립트 출력에서 언급한 볼륨을 통해 찾습니다.

  ![Linux 파일 복구 메뉴](./media/backup-azure-restore-files-from-vm/linux-mount-paths.png)

## <a name="closing-the-connection"></a>연결 닫기

파일을 식별하고 로컬 스토리지 위치에 복사한 후에는 추가 드라이브를 제거(또는 분리)합니다. 드라이브를 분리하려면 Azure Portal의 **파일 복구** 메뉴에서 **디스크 분리**를 클릭합니다.

![디스크 분리](./media/backup-azure-restore-files-from-vm/unmount-disks3.png)

디스크가 분리되면 메시지가 표시됩니다. 디스크를 제거할 수 있도록 연결을 새로 고치는 데 몇 분이 소요될 수 있습니다.

Linux에서 복구 지점에 대한 연결이 단절된 후 OS는 해당 탑재 경로를 자동으로 제거하지 않습니다. 마운트 경로는 "고아" 볼륨으로 존재하며 볼 수 있지만 파일에 액세스/쓸 때 오류를 throw합니다. 수동으로 제거할 수도 있습니다. 스크립트는 실행 시 모든 이전 복구 지점에서 존재하는 이러한 볼륨을 식별하고 승인 시 정리합니다.

## <a name="special-configurations"></a>특수 구성

### <a name="dynamic-disks"></a>동적 디스크

보호된 Azure VM에 다음과 같은 특성 중 하나 또는 달 다를 포함한 볼륨이 있는 경우 동일한 VM에서 실행 스크립트를 실행할 수 없습니다.

- 여러 디스크에 걸쳐 있는 볼륨(스팬 및 스트라이프 볼륨)
- 동적 디스크의 장애 조치 볼륨(미러 및 RAID-5 볼륨)

대신, 호환되는 운영 체제로 다른 컴퓨터에서 실행 가능한 스크립트를 실행합니다.

### <a name="windows-storage-spaces"></a>Windows 스토리지 공간

Windows 스토리지 공간은 스토리지를 가상화할 수 있는 Windows 기술입니다. Windows 스토리지 공간을 사용하면 업계 표준 디스크를 스토리지 풀로 그룹화할 수 있습니다. 그런 다음 해당 스토리지 풀의 사용 가능한 공간을 사용하여 스토리지 공간이라는 가상 디스크를 만들 수 있습니다.

보호된 Azure VM에서 Windows 스토리지 공간을 사용하는 경우 동일한 VM에서는 실행 가능한 스크립트를 실행할 수 없습니다. 대신, 호환되는 운영 체제로 다른 컴퓨터에서 실행 가능한 스크립트를 실행합니다.

### <a name="lvmraid-arrays"></a>LVM/RAID 어레이

Linux에서 LVM(논리 볼륨 관리자) 및/또는 소프트웨어 RAID 배열은 여러 디스크에 걸쳐 논리 볼륨을 관리하는 데 사용됩니다. 보호된 Linux VM에서 LVM 및/또는 RAID 배열을 사용하는 경우 동일한 VM에서 스크립트를 실행할 수 없습니다. 대신 호환되는 OS로 다른 컴퓨터에서 스크립트를 실행하고 보호된 VM의 파일 시스템을 지원합니다.

다음 스크립트 출력은 파티션 형식으로 LVM 및/또는 RAID 배열 디스크 및 볼륨을 표시합니다.

   ![Linux LVM 출력 메뉴](./media/backup-azure-restore-files-from-vm/linux-LVMOutput.png)

이 파티션을 온라인 상태로 만들려면 다음 섹션의 명령을 실행합니다.

#### <a name="for-lvm-partitions"></a>LVM 파티션의 경우

볼륨 그룹 이름을 물리적 볼륨 아래에 나열하려면 다음을 수행합니다.

```bash
#!/bin/bash
pvs <volume name as shown above in the script output>
```

볼륨 그룹에 모든 논리 볼륨, 이름 및 경로를 나열하려면 다음을 수행합니다.

```bash
#!/bin/bash
lvdisplay <volume-group-name from the pvs command's results>
```

논리 볼륨을 선택한 경로에 탑재하려면 다음을 수행합니다.

```bash
#!/bin/bash
mount <LV path> </mountpath>
```

#### <a name="for-raid-arrays"></a>RAID 어레이용

다음 명령에는 모든 공격대 디스크에 대한 세부 정보가 표시됩니다.

```bash
#!/bin/bash
mdadm –detail –scan
```

 관련 RAID 디스크는 `/dev/mdm/<RAID array name in the protected VM>`으로 표시됩니다.

RAID 디스크에 실제 볼륨이 있는 경우 마운트 명령을 사용합니다.

```bash
#!/bin/bash
mount [RAID Disk Path] [/mountpath]
```

RAID 디스크에 다른 LVM이 구성된 경우 LVM 파티션에 대한 이전 절차를 사용하지만 RAID 디스크 이름 대신 볼륨 이름을 사용합니다.

## <a name="system-requirements"></a>시스템 요구 사항

### <a name="for-windows-os"></a>Windows OS의 경우

다음 표에서는 서버와 컴퓨터 운영 체제 간의 호환성을 보여 줍니다. 파일을 복구할 경우 이전 또는 미래 운영 체제 버전으로 파일을 복원할 수 없습니다. 예를 들어 Windows Server 2016 VM의 파일을 Windows Server 2012 또는 Windows 8 컴퓨터로 복원할 수 없습니다. VM의 파일을 같은 서버 운영 체제 또는 호환되는 클라이언트 운영 체제로 복원할 수 있습니다.

|서버 OS | 호환되는 클라이언트 OS  |
| --------------- | ---- |
| Windows Server 2019    | 윈도우 10 |
| Windows Server 2016    | 윈도우 10 |
| Windows Server 2012 R2 | Windows 8.1 |
| Windows Server 2012    | Windows 8  |
| Windows Server 2008 R2 | Windows 7   |

### <a name="for-linux-os"></a>Linux OS

Linux에서 파일을 복원하는 데 사용하는 컴퓨터의 OS는 보호된 가상 머신의 파일 시스템을 지원해야 합니다. 스크립트를 실행할 컴퓨터를 선택할 때 컴퓨터에 호환되는 OS가 있는지 확인하고 다음 테이블에 나타난 버전 중 하나를 사용합니다.

|Linux OS | 버전  |
| --------------- | ---- |
| Ubuntu | 12.04 이상 |
| CentOS | 6.5 이상  |
| RHEL | 6.7 이상 |
| Debian | 7 이상 |
| Oracle Linux | 6.4 이상 |
| SLES | 12 이상 |
| openSUSE | 42.2 이상 |

> [!NOTE]
> SLES 12 SP4 OS가 있는 컴퓨터에서 파일 복구 스크립트를 실행하는 데 몇 가지 문제가 발견되었으며 SLES 팀과 함께 조사 중입니다.
> 현재 파일 복구 스크립트를 실행 중인 경우 SLES 12 SP2 및 SP3 OS 버전이 있는 컴퓨터에서 작업중입니다.
>

스크립트는 복구 지점에 안전하게 연결하고 실행하기 위해 Python 및 bash 구성 요소가 필요합니다.

|구성 요소 | 버전  |
| --------------- | ---- |
| bash | 4 이상 |
| python | 2.6.6 이상  |
| TLS | 1.2가 지원되어야 합니다.  |

## <a name="access-requirements"></a>액세스 요구 사항

제한된 액세스를 포함하는 컴퓨터에서 스크립트를 실행하는 경우 다음에 대한 액세스 권한이 있는지 확인합니다.

- `download.microsoft.com`
- Recovery Service URL(복구 서비스 자격 증명 모음이 있는 지역을 참조하는 지역 이름)
  - `https://pod01-rec2.geo-name.backup.windowsazure.com`(Azure 공용 지역의 경우)
  - `https://pod01-rec2.geo-name.backup.windowsazure.cn`(Azure China 21Vianet의 경우)
  - `https://pod01-rec2.geo-name.backup.windowsazure.us`(Azure 미국 정부의 경우)
  - `https://pod01-rec2.geo-name.backup.windowsazure.de`(Azure 독일의 경우)
- 아웃바운드 포트 53(DNS), 443, 3260

> [!NOTE]
>
> - 다운로드한 스크립트 파일 이름에는 URL에 입력할 **지리적 이름이** 있습니다. exampple의 경우: 다운로드한 \'스크립트 이름은\'\_\'vMname 이름\'_\'GUID로\' *ContosoVM_wcus_12345678* 시작됩니다ContosoVM_wcus_12345678
> - URL은 <https://pod01-rec2.wcus.backup.windowsazure.com>"
>

Linux의 경우 스크립트는 복구 지점에 연결하는 데 'open-iscsi' 및 'lshw' 구성 요소가 필요합니다. 스크립트가 실행되는 컴퓨터에 구성 요소가 없으면 스크립트에서 구성 요소를 설치할 수 있는 권한을 요청합니다. 동의하여 필요한 구성 요소를 설치 합니다.

에 대한 `download.microsoft.com` 액세스는 스크립트가 실행되는 컴퓨터와 복구 지점의 데이터 간에 보안 채널을 구축하는 데 사용되는 구성 요소를 다운로드하는 데 필요합니다.

백업된 VM과 동일한(또는 호환) 운영 체제가 있는 모든 컴퓨터에서 스크립트를 실행할 수 있습니다. 호환되는 운영 체제는 [호환되는 OS 표](backup-azure-restore-files-from-vm.md#system-requirements)를 참조하세요. 보호된 Azure 가상 머신에서 Windows 스토리지 공간(Windows Azure VM의 경우) 또는 LVM/RAID 배열(Linux VM의 경우)을 사용하는 경우 동일한 가상 머신에서 실행 파일 또는 스크립트를 실행할 수 없습니다. 대신, 호환되는 운영 체제로 다른 컴퓨터에서 실행 파일 또는 스크립트를 실행합니다.

## <a name="file-recovery-from-virtual-machine-backups-having-large-disks"></a>큰 디스크가 있는 가상 시스템 백업에서 파일 복구

이 섹션에서는 디스크가 16개 이상이고 각 디스크 크기가 32TB보다 큰 Azure Virtual 컴퓨터의 백업에서 파일 복구를 수행하는 방법을 설명합니다.

파일 복구 프로세스는 백업에서 모든 디스크를 연결하므로 많은 수의 디스크(>16) 또는 대형 디스크(각각 > 32TB)가 사용되는 경우 다음 작업 지점을 사용하는 것이 좋습니다.

- 파일 복구를 위해 별도의 복원 서버(Azure VM D2v3 VM)를 유지합니다. 파일 복구에만 사용할 수 있으며 필요하지 않은 경우 종료할 수 있습니다. 원래 컴퓨터에서 복원하는 것은 VM 자체에 큰 영향을 미치므로 권장되지 않습니다.
- 그런 다음 스크립트를 한 번 실행하여 파일 복구 작업이 성공했는지 확인합니다.
- 파일 복구 프로세스가 중단되면(디스크가 탑재되지 않았거나 탑재되었지만 볼륨이 나타나지 않는 경우) 다음 단계를 수행합니다.
  - 복원 서버가 Windows VM인 경우:
    - OS가 WS 2012 이상인지 확인합니다.
    - 레지스트리 키가 복원 서버에서 아래에 제안된 대로 설정되어 있는지 확인하고 서버를 다시 부팅해야 합니다. GUID 옆의 숫자는 0001-0005입니다. 다음 예제에서는 0004입니다. 매개 변수 섹션까지 레지스트리 키 경로를 탐색합니다.

    ![iscsi-reg-키-변경.png](media/backup-azure-restore-files-from-vm/iscsi-reg-key-changes.png)

```registry
- HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Disk\TimeOutValue – change this from 60 to 1200
- HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\Control\Class\{4d36e97b-e325-11ce-bfc1-08002be10318}\0003\Parameters\SrbTimeoutDelta – change this from 15 to 1200
- HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\Control\Class\{4d36e97b-e325-11ce-bfc1-08002be10318}\0003\Parameters\EnableNOPOut – change this from 0 to 1
- HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\Control\Class\{4d36e97b-e325-11ce-bfc1-08002be10318}\0003\Parameters\MaxRequestHoldTime - change this from 60 to 1200
```

- 복원 서버가 Linux VM인 경우:
  - 파일 /etc/iscsi/iscsid.conf에서 설정을 변경합니다.
    - node.conn[0].timeo.noop_out_timeout = 5 에서 node.conn[0].timeo.noop_out_timeout = 30
- 위에서 변경한 후 스크립트를 다시 실행합니다. 이러한 변경으로 파일 복구가 성공할 가능성이 높습니다.
- 사용자가 스크립트를 다운로드할 때마다 Azure Backup은 다운로드할 복구 지점을 준비하는 프로세스를 시작합니다. 큰 디스크의 경우 이 프로세스에는 상당한 시간이 소요됩니다. 요청이 연속적으로 터지는 경우 대상 준비는 다운로드 나선형으로 진행됩니다. 따라서 포털/Powershell/CLI에서 스크립트를 다운로드한 다음 20-30분 동안 기다린 다음 실행하는 것이 좋습니다. 이 때까지 대상은 스크립트에서 연결할 준비가 될 것으로 예상됩니다.
- 파일 복구 후 포털로 돌아가서 볼륨을 탑재할 수 없는 복구 지점의 **디스크 마운트 해제를** 클릭합니다. 기본적으로 이 단계는 기존 프로세스/세션을 정리하고 복구 가능성을 높입니다.

## <a name="troubleshooting"></a>문제 해결

가상 머신에서 파일을 복구하는 동안 문제가 생기는 경우 다음 표에서 추가 정보를 확인하세요.

| 오류 메시지/시나리오 | 가능한 원인 | 권장 작업 |
| ------------------------ | -------------- | ------------------ |
| Exe 출력: *대상에 연결하는 동안 적발된 예외* | 스크립트가 복구 지점에 액세스할 수 없습니다.    | 기기가 이전 액세스 [요구 사항을](#access-requirements)충족하는지 확인합니다. |  
| Exe 출력: *iSCSI 세션을 통해 대상이 이미 로그인되었습니다.* | 동일한 컴퓨터에서 스크립트가 이미 실행되었고 드라이브가 연결되었습니다. | 복구 지점의 볼륨이 이미 연결되었습니다. 원래 VM과 동일한 드라이브 문자로 탑재되지 않을 수 있습니다. 파일 탐색기에서 사용 가능한 모든 볼륨을 찾아봅습니다. |
| Exe 출력: *디스크가 포털을 통해 분리되었거나 12시간 제한을 초과했기 때문에 이 스크립트는 유효하지 않습니다. 포털에서 새 스크립트를 다운로드합니다.* |    포털에서 디스크가 분리되었거나 12시간 제한을 초과했습니다. | 이 특정 exe는 이제 유효하지 않으며 실행할 수 없습니다. 해당 복구 시점의 파일에 액세스하려면 포털을 방문하여 새 exe를 방문하십시오.|
| exe가 실행되는 컴퓨터에서: 분리 단추를 클릭한 후 새 볼륨이 분리되지 않습니다. | 컴퓨터의 iSCSI 이니시에이터가 대상에 대한 연결을 응답/새로 고치거나 캐시를 유지 관리하지 않습니다. |  **분리**를 클릭한 후에 잠시 대기합니다. 새 볼륨이 분리되지 않은 경우 모든 볼륨을 찾아봅합니다. 모든 볼륨을 탐색하면 시작자가 연결을 새로 고치게 되고 디스크를 사용할 수 없다는 오류 메시지가 볼륨이 분리됩니다.|
| Exe 출력: 스크립트가 성공적으로 실행되지만 "연결된 새 볼륨"이 스크립트 출력에 표시되지 않습니다. |    일시적인 오류입니다.    | 볼륨이 이미 첨부되어 있습니다. Explorer를 열어 탐색합니다. 매번 스크립트를 실행하는 데 동일한 컴퓨터를 사용하는 경우 컴퓨터를 다시 시작하는 것이 좋습니다. |
| Linux 특정: 원하는 볼륨을 볼 수 없습니다 | 스크립트가 실행되는 컴퓨터의 OS는 보호된 VM의 기본 파일 시스템을 인식하지 못할 수도 있습니다. | 복구 지점이 충돌 일관성인지 파일 일관성인지 확인합니다. 파일일관성이 있는 경우 OS에서 보호된 VM의 파일 시스템을 인식하는 다른 컴퓨터에서 스크립트를 실행합니다. |
| Windows 특정: 원하는 볼륨을 볼 수 없습니다. | 디스크가 연결되었지만 볼륨이 구성되지 않았을 수 있습니다. | 디스크 관리 화면에서 복구 지점과 관련된 추가 디스크를 식별합니다. 이러한 디스크 중 어느 것이 오프라인 상태인 경우 디스크를 마우스 오른쪽 단추로 클릭하여 온라인 상태로 가져오고 **Online**을 클릭합니다.|

## <a name="security"></a>보안

이 섹션에서는 Azure VM 백업에서 파일 복구 구현을 위해 수행된 다양한 보안 조치에 대해 설명합니다.

### <a name="feature-flow"></a>피처 흐름

이 기능은 전체 VM 또는 VM 디스크를 복원할 필요 없이 최소한의 단계로 VM 데이터에 액세스하도록 제작되었습니다. VM 데이터에 대한 액세스는 스크립트(아래 그림과 같이 실행시 복구 볼륨을 탑재)에 의해 제공되며 모든 보안 구현의 초석을 형성합니다.

  ![보안 기능 흐름](./media/backup-azure-restore-files-from-vm/vm-security-feature-flow.png)

### <a name="security-implementations"></a>보안 구현

#### <a name="select-recovery-point-who-can-generate-script"></a>복구 지점 선택(스크립트생성 할 수 있는 사용자)

스크립트는 VM 데이터에 대한 액세스를 제공하므로 처음에 데이터를 생성할 수 있는 사람을 규제하는 것이 중요합니다. Azure 포털에 로그인하고 스크립트를 생성할 [권한이 있는 RBAC가](backup-rbac-rs-vault.md#mapping-backup-built-in-roles-to-backup-management-actions) 있어야 합니다.

파일 복구는 VM 복원 및 디스크 복원에 필요한 것과 동일한 수준의 권한 부여가 필요합니다. 즉, 권한이 있는 사용자만 VM 데이터를 볼 수 있어 스크립트를 생성할 수 있다.

생성된 스크립트는 Azure 백업 서비스에 대한 공식 Microsoft 인증서로 서명됩니다. 스크립트를 변조하면 서명이 끊어졌으며 스크립트를 실행하려는 모든 시도가 OS에 의해 잠재적위험으로 강조 표시됩니다.

#### <a name="mount-recovery-volume-who-can-run-script"></a>복구 볼륨 마운트(스크립트를 실행할 수 있는 사람)

관리자만 스크립트를 실행할 수 있으며 상승 된 모드에서 실행해야 합니다. 스크립트는 미리 생성된 단계 집합만 실행하며 외부 소스의 입력을 허용하지 않습니다.

스크립트를 실행하려면 Azure 포털 또는 PowerShell/CLI에서 스크립트를 생성할 때 권한이 있는 사용자에게만 표시되는 암호가 필요합니다. 이는 스크립트를 다운로드하는 권한이 있는 사용자도 스크립트 실행을 담당하도록 하기 위한 것입니다.

#### <a name="browse-files-and-folders"></a>파일 및 폴더 찾아보기

파일 및 폴더를 찾아보기 위해 스크립트는 컴퓨터의 iSCSI 초기화기를 사용하고 iSCSI 대상으로 구성된 복구 지점에 연결합니다. 여기서는 구성 요소를 모방/스푸치하려는 시나리오를 상상할 수 있습니다.

각 구성 요소가 서로를 인증할 수 있도록 상호 CHAP 인증 메커니즘을 사용합니다. 즉, 가짜 이니시에이터가 iSCSI 대상에 연결하고 스크립트가 실행되는 컴퓨터에 가짜 대상이 연결되는 것은 매우 어렵습니다.

복구 서비스와 컴퓨터 간의 데이터 흐름은 TCP(TLS 1.2)를 통해 안전한 TLS 터널을[구축하여 보호됩니다(스크립트가](#system-requirements) 실행되는 컴퓨터에서 지원되어야 합니다).

부모/백업된 VM에 있는 모든 파일 액세스 제어 목록(ACL)도 마운트된 파일 시스템에 유지됩니다.

스크립트는 복구 지점에 대한 읽기 전용 액세스를 제공하며 12시간 동안만 유효합니다. 더 일찍 액세스를 제거하려면 Azure Portal/PowerShell/CLI에 로그인하고 특정 복구 지점에 대해 **디스크 마운트 해제를** 수행합니다. 스크립트는 즉시 무효화됩니다.

## <a name="next-steps"></a>다음 단계

- 파일을 복원하는 동안 문제가 발생하려면 [문제 해결](#troubleshooting) 섹션을 참조하십시오.
- [Powershell을 통해 파일을 복원하는](https://docs.microsoft.com/azure/backup/backup-azure-vms-automation#restore-files-from-an-azure-vm-backup) 방법 알아보기
- [Azure CLI를 통해 파일을 복원하는](https://docs.microsoft.com/azure/backup/tutorial-restore-files) 방법 알아보기
- VM을 복원한 후 [백업을 관리하는](https://docs.microsoft.com/azure/backup/backup-azure-manage-vms) 방법을 알아보십시오.
