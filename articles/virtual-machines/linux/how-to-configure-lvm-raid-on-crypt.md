---
title: 암호화 된 장치에서 LVM 및 RAID 구성-Azure Disk Encryption
description: 이 문서에서는 Linux Vm 용 암호화 된 장치에서 LVM 및 RAID를 구성 하기 위한 지침을 제공 합니다.
author: jofrance
ms.service: security
ms.topic: how-to
ms.author: jofrance
ms.date: 03/17/2020
ms.custom: seodec18
ms.openlocfilehash: 6ccb74fe58742974798732605b4a017a06777bcc
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91328176"
---
# <a name="configure-lvm-and-raid-on-encrypted-devices"></a>암호화 된 장치에서 LVM 및 RAID 구성

이 문서는 암호화 된 장치에서 LVM (논리 볼륨 관리) 및 RAID를 수행 하는 방법에 대 한 단계별 프로세스입니다. 프로세스는 다음 환경에 적용 됩니다.

- Linux 배포
    - RHEL 7.6 이상
    - Ubuntu 18.04 +
    - SUSE 12 이상
- 단일 패스 확장 Azure Disk Encryption
- Azure Disk Encryption 이중 패스 확장


## <a name="scenarios"></a>시나리오

이 문서의 절차는 다음과 같은 시나리오를 지원 합니다.  

- 암호화 된 장치 위에 LVM 구성 (LVM-온-반자)
- 암호화 된 장치 위에 RAID 구성 (RAID-0)

기본 장치 또는 장치를 암호화 한 후에는 해당 암호화 된 계층 위에 LVM 또는 RAID 구조를 만들 수 있습니다. 

실제 볼륨 (PVs)은 암호화 된 계층의 맨 위에 생성 됩니다. 물리적 볼륨은 볼륨 그룹을 만드는 데 사용 됩니다. 볼륨을 만들고/etc/fstab에 필요한 항목을 추가 합니다. 

![LVM 구조의 계층 다이어그램](./media/disk-encryption/lvm-raid-on-crypt/000-lvm-raid-crypt-diagram.png)

이와 비슷한 방식으로 RAID 장치는 디스크의 암호화 된 계층 위에 생성 됩니다. 파일 시스템은 RAID 장치 위에 생성 되 고/etc/fstab에 일반 장치로 추가 됩니다.

## <a name="considerations"></a>고려 사항

LVM을 사용 하는 것이 좋습니다. RAID는 특정 응용 프로그램 또는 환경 제한으로 인해 LVM을 사용할 수 없는 경우에 사용할 수 있는 옵션입니다.

**EncryptFormatAll** 옵션을 사용 합니다. 이 옵션에 대 한 자세한 내용은 [Linux vm에서 데이터 디스크에 EncryptFormatAll 기능 사용](./disk-encryption-linux.md#use-encryptformatall-feature-for-data-disks-on-linux-vms)을 참조 하세요.

OS를 암호화 하는 경우에도이 방법을 사용할 수 있지만 여기에서는 데이터 드라이브만 암호화 합니다.

이 절차에서는 [Linux vm에서 Azure Disk Encryption 시나리오](./disk-encryption-linux.md) 의 필수 구성 요소를 검토 하 고, [빠른 시작: Azure CLI를 사용 하 여 Linux vm을 만들고 암호화](./disk-encryption-cli-quickstart.md)하는 것으로 가정 합니다.

Azure Disk Encryption 이중 패스 버전은 사용 중단 경로에 있으므로 새 암호화에서 더 이상 사용 되지 않습니다.

## <a name="general-steps"></a>일반적인 단계

"다중 사용자" 구성을 사용 하는 경우 다음 절차에 설명 된 프로세스를 사용 합니다.

>[!NOTE] 
>문서 전체에서 변수가 사용됩니다. 상황에 맞게 변수를 바꾸세요.

### <a name="deploy-a-vm"></a>VM 배포 
다음 명령은 선택 사항 이지만 새로 배포 된 VM (가상 컴퓨터)에 적용 하는 것이 좋습니다.

PowerShell:

```powershell
New-AzVm -ResourceGroupName ${RGNAME} `
-Name ${VMNAME} `
-Location ${LOCATION} `
-Size ${VMSIZE} `
-Image ${OSIMAGE} `
-Credential ${creds} `
-Verbose
```
Azure CLI:

```bash
az vm create \
-n ${VMNAME} \
-g ${RGNAME} \
--image ${OSIMAGE} \
--admin-username ${username} \
--admin-password ${password} \
-l ${LOCATION} \
--size ${VMSIZE} \
-o table
```
### <a name="attach-disks-to-the-vm"></a>VM에 디스크 연결
다음 명령을 반복 하 여 `$N` VM에 연결할 새 디스크 수를 선택 합니다.

PowerShell:

```powershell
$storageType = 'Standard_LRS'
$dataDiskName = ${VMNAME} + '_datadisk0'
$diskConfig = New-AzDiskConfig -SkuName $storageType -Location $LOCATION -CreateOption Empty -DiskSizeGB 5
$dataDisk1 = New-AzDisk -DiskName $dataDiskName -Disk $diskConfig -ResourceGroupName ${RGNAME}
$vm = Get-AzVM -Name ${VMNAME} -ResourceGroupName ${RGNAME} 
$vm = Add-AzVMDataDisk -VM $vm -Name $dataDiskName -CreateOption Attach -ManagedDiskId $dataDisk1.Id -Lun 0
Update-AzVM -VM ${VM} -ResourceGroupName ${RGNAME}
```

Azure CLI:

```bash
az vm disk attach \
-g ${RGNAME} \
--vm-name ${VMNAME} \
--name ${VMNAME}datadisk1 \
--size-gb 5 \
--new \
-o table
```

### <a name="verify-that-the-disks-are-attached-to-the-vm"></a>디스크가 VM에 연결 되어 있는지 확인 합니다.
PowerShell:
```powershell
$VM = Get-AzVM -ResourceGroupName ${RGNAME} -Name ${VMNAME}
$VM.StorageProfile.DataDisks | Select-Object Lun,Name,DiskSizeGB
```
![PowerShell에서 연결 된 디스크의 목록](./media/disk-encryption/lvm-raid-on-crypt/001-lvm-raid-check-disks-powershell.png)

Azure CLI:

```bash
az vm show -g ${RGNAME} -n ${VMNAME} --query storageProfile.dataDisks -o table
```
![Azure CLI 연결 된 디스크의 목록](./media/disk-encryption/lvm-raid-on-crypt/002-lvm-raid-check-disks-cli.png)

포털:

![포털에서 연결 된 디스크의 목록](./media/disk-encryption/lvm-raid-on-crypt/003-lvm-raid-check-disks-portal.png)

OS:

```bash
lsblk 
```
![OS에서 연결 된 디스크의 목록](./media/disk-encryption/lvm-raid-on-crypt/004-lvm-raid-check-disks-os.png)

### <a name="configure-the-disks-to-be-encrypted"></a>암호화 되도록 디스크 구성
이 구성은 운영 체제 수준에서 수행 됩니다. 해당 디스크는 Azure Disk Encryption를 통해 기존 암호화에 대해 구성 됩니다.

- 디스크를 기반으로 파일 시스템을 만듭니다.
- 파일 시스템을 탑재 하는 임시 탑재 지점이 만들어집니다.
- 파일 시스템은 부팅 시 탑재 되도록/etc/fstab에 구성 됩니다.

새 디스크에 할당 된 장치 문자를 확인 합니다. 이 예제에서는 데이터 디스크 4 개를 사용 합니다.

```bash
lsblk 
```
![OS에 연결 된 데이터 디스크](./media/disk-encryption/lvm-raid-on-crypt/004-lvm-raid-check-disks-os.png)

### <a name="create-a-file-system-on-top-of-each-disk"></a>각 디스크를 기반으로 파일 시스템 만들기
이 명령은 "for" 주기의 "의" 부분에 정의 된 각 디스크에서 ext4 파일 시스템 만들기를 반복 합니다.

```bash
for disk in c d e f; do echo mkfs.ext4 -F /dev/sd${disk}; done |bash
```
![Ext4 파일 시스템 만들기](./media/disk-encryption/lvm-raid-on-crypt/005-lvm-raid-create-temp-fs.png)

최근에 만든 파일 시스템의 UUID (범용 고유 식별자)를 찾고, 임시 폴더를 만들고,/etc/fstab에 해당 항목을 추가 하 고, 모든 파일 시스템을 탑재 합니다.

또한이 명령은 "for" 주기의 "의" 부분에 정의 된 각 디스크를 반복 합니다.

```bash
for disk in c d e f; do diskuuid="$(blkid -s UUID -o value /dev/sd${disk})"; \
mkdir /tempdata${disk}; \
echo "UUID=${diskuuid} /tempdata${disk} ext4 defaults,nofail 0 0" >> /etc/fstab; \
mount -a; \
done
``` 

### <a name="verify-that-the-disks-are-mounted-properly"></a>디스크가 제대로 탑재 되었는지 확인 합니다.
```bash
lsblk
```
![탑재 된 임시 파일 시스템 목록](./media/disk-encryption/lvm-raid-on-crypt/006-lvm-raid-verify-temp-fs.png)

또한 디스크가 구성 되어 있는지 확인 합니다.

```bash
cat /etc/fstab
```
![Fstab를 통한 구성 정보](./media/disk-encryption/lvm-raid-on-crypt/007-lvm-raid-verify-temp-fstab.png)

### <a name="encrypt-the-data-disks"></a>데이터 디스크 암호화
KEK (키 암호화 키)를 사용 하는 PowerShell:

```powershell
$sequenceVersion = [Guid]::NewGuid() 
Set-AzVMDiskEncryptionExtension -ResourceGroupName $RGNAME `
-VMName ${VMNAME} `
-DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl `
-DiskEncryptionKeyVaultId $KeyVaultResourceId `
-KeyEncryptionKeyUrl $keyEncryptionKeyUrl `
-KeyEncryptionKeyVaultId $KeyVaultResourceId `
-VolumeType 'DATA' `
-EncryptFormatAll `
-SequenceVersion $sequenceVersion `
-skipVmBackup;
```

KEK를 사용 하 여 Azure CLI:

```bash
az vm encryption enable \
--resource-group ${RGNAME} \
--name ${VMNAME} \
--disk-encryption-keyvault ${KEYVAULTNAME} \
--key-encryption-key ${KEYNAME} \
--key-encryption-keyvault ${KEYVAULTNAME} \
--volume-type "DATA" \
--encrypt-format-all \
-o table
```
### <a name="verify-the-encryption-status"></a>암호화 상태 확인

모든 디스크가 암호화 된 경우에만 다음 단계를 계속 합니다.

PowerShell:

```powershell
Get-AzVmDiskEncryptionStatus -ResourceGroupName ${RGNAME} -VMName ${VMNAME}
```
![PowerShell의 암호화 상태](./media/disk-encryption/lvm-raid-on-crypt/008-lvm-raid-verify-encryption-status-ps.png)

Azure CLI:

```bash
az vm encryption show -n ${VMNAME} -g ${RGNAME} -o table
```
![Azure CLI의 암호화 상태](./media/disk-encryption/lvm-raid-on-crypt/009-lvm-raid-verify-encryption-status-cli.png)

포털:

![포털의 암호화 상태](./media/disk-encryption/lvm-raid-on-crypt/010-lvm-raid-verify-encryption-status-portal.png)

OS 수준:

```bash
lsblk
```
![OS의 암호화 상태](./media/disk-encryption/lvm-raid-on-crypt/011-lvm-raid-verify-encryption-status-os.png)

확장 프로그램은 파일 시스템을/var/lib/azure_disk_encryption_config/azure_crypt_mount (이전 암호화)에 추가 하거나/etc/crypttab (새 암호화)에 추가 합니다.

>[!NOTE] 
>이러한 파일을 수정 하지 마십시오.

이 파일은 부팅 프로세스 중에 이러한 디스크를 정품 인증 하므로 LVM 또는 RAID에서 나중에 사용할 수 있습니다. 

이 파일의 탑재 지점은 걱정할 필요가 없습니다. Azure Disk Encryption는 암호화 된 장치 위에 물리적 볼륨 또는 RAID 장치를 만든 후 일반 파일 시스템으로 탑재 되는 디스크를 가져오는 기능을 잃게 됩니다. 이렇게 하면 준비 프로세스 중에 사용한 파일 시스템 형식이 제거 됩니다.

### <a name="remove-the-temporary-folders-and-temporary-fstab-entries"></a>임시 폴더 및 임시 fstab 항목 제거
LVM의 일부로 사용 될 디스크에서 파일 시스템을 분리 합니다.

```bash
for disk in c d e f; do unmount /tempdata${disk}; done
```
/Etc/fstab 항목을 제거 합니다.

```bash
vi /etc/fstab
```
### <a name="verify-that-the-disks-are-not-mounted-and-that-the-entries-on-etcfstab-were-removed"></a>디스크가 탑재 되지 않고/etc/fstab의 항목이 제거 되었는지 확인 합니다.

```bash
lsblk
```
![임시 파일 시스템이 분리 되었는지 확인](./media/disk-encryption/lvm-raid-on-crypt/012-lvm-raid-verify-disks-not-mounted.png)

그리고 디스크가 구성 되어 있는지 확인 합니다.
```bash
cat /etc/fstab
```
![임시 fstab 항목이 제거 되었는지 확인](./media/disk-encryption/lvm-raid-on-crypt/013-lvm-raid-verify-fstab-temp-removed.png)

## <a name="steps-for-lvm-on-crypt"></a>LVM에 대 한 단계
이제 기본 디스크가 암호화 되었으므로 LVM 구조를 만들 수 있습니다.

장치 이름을 사용 하는 대신 각 디스크에 대 한/sv/vv/dd 경로를 사용 하 여 디스크 자체가 아닌 디스크의 맨 위에 있는 묘지 계층에 실제 볼륨을 만듭니다.

### <a name="configure-lvm-on-top-of-the-encrypted-layers"></a>암호화 된 계층 위에 LVM 구성
#### <a name="create-the-physical-volumes"></a>실제 볼륨 만들기
파일 시스템 서명을 지울 수 있는지 확인 하는 경고 메시지가 표시 됩니다. **Y**를 입력 하 여 계속 하거나 다음과 같이 **echo "y"** 를 사용 합니다.

```bash
echo "y" | pvcreate /dev/mapper/c49ff535-1df9-45ad-9dad-f0846509f052
echo "y" | pvcreate /dev/mapper/6712ad6f-65ce-487b-aa52-462f381611a1
echo "y" | pvcreate /dev/mapper/ea607dfd-c396-48d6-bc54-603cf741bc2a
echo "y" | pvcreate /dev/mapper/4159c60a-a546-455b-985f-92865d51158c
```
![실제 볼륨이 생성 되었는지 확인](./media/disk-encryption/lvm-raid-on-crypt/014-lvm-raid-pvcreate.png)

>[!NOTE] 
>/Dev/mapper/device 이름은 **lsblk**의 출력을 기반으로 하는 실제 값을 대체 해야 합니다.

#### <a name="verify-the-information-for-physical-volumes"></a>실제 볼륨에 대 한 정보 확인
```bash
pvs
```

![실제 볼륨 정보](./media/disk-encryption/lvm-raid-on-crypt/015-lvm-raid-pvs.png)

#### <a name="create-the-volume-group"></a>볼륨 그룹 만들기
이미 초기화 된 동일한 장치를 사용 하 여 볼륨 그룹을 만듭니다.

```bash
vgcreate vgdata /dev/mapper/
```

### <a name="check-the-information-for-the-volume-group"></a>볼륨 그룹에 대 한 정보 확인

```bash
vgdisplay -v vgdata
```
```bash
pvs
```
![볼륨 그룹에 대 한 정보](./media/disk-encryption/lvm-raid-on-crypt/016-lvm-raid-pvs-on-vg.png)

#### <a name="create-logical-volumes"></a>논리 볼륨 만들기

```bash
lvcreate -L 10G -n lvdata1 vgdata
lvcreate -L 7G -n lvdata2 vgdata
``` 

#### <a name="check-the-created-logical-volumes"></a>만든 논리 볼륨 확인

```bash
lvdisplay
lvdisplay vgdata/lvdata1
lvdisplay vgdata/lvdata2
```
![논리 볼륨에 대 한 정보](./media/disk-encryption/lvm-raid-on-crypt/017-lvm-raid-lvs.png)

#### <a name="create-file-systems-on-top-of-the-structures-for-logical-volumes"></a>논리 볼륨에 대 한 구조 위에 파일 시스템 만들기

```bash
echo "yes" | mkfs.ext4 /dev/vgdata/lvdata1
echo "yes" | mkfs.ext4 /dev/vgdata/lvdata2
```

#### <a name="create-the-mount-points-for-the-new-file-systems"></a>새 파일 시스템에 대 한 탑재 위치 만들기

```bash
mkdir /data0
mkdir /data1
```

#### <a name="add-the-new-file-systems-to-etcfstab-and-mount-them"></a>/Etc/fstab에 새 파일 시스템을 추가 하 고 탑재 합니다.

```bash
echo "/dev/mapper/vgdata-lvdata1 /data0 ext4 defaults,nofail 0 0" >>/etc/fstab
echo "/dev/mapper/vgdata-lvdata2 /data1 ext4 defaults,nofail 0 0" >>/etc/fstab
mount -a
```

#### <a name="verify-that-the-new-file-systems-are-mounted"></a>새 파일 시스템이 탑재 되었는지 확인

```bash
lsblk -fs
df -h
```
![탑재 된 파일 시스템에 대 한 정보](./media/disk-encryption/lvm-raid-on-crypt/018-lvm-raid-lsblk-after-lvm.png)

이러한 **lsblk**변형에서 종속성을 역순으로 표시 하는 장치를 나열 하 고 있습니다. 이 옵션을 사용 하면 원래/dev/sd [disk] 장치 이름 대신 논리 볼륨 별로 그룹화 된 장치를 식별할 수 있습니다.

Azure Disk Encryption를 통해 암호화 된 장치 위에 생성 된 LVM 볼륨의 탑재 지점 옵션에 **nofail** 옵션이 추가 되었는지 확인 하는 것이 중요 합니다. 부팅 프로세스 중 또는 유지 관리 모드에서 OS가 중단 되지 않도록 합니다.

**Nofail** 옵션을 사용 하지 않는 경우:

- OS는 Azure Disk Encryption을 시작 하 고 데이터 디스크의 잠금이 해제 되 고 탑재 된 단계에 있지 않습니다. 
- 암호화 된 디스크는 부팅 프로세스가 끝날 때 잠금 해제 됩니다. LVM 볼륨 및 파일 시스템은 Azure Disk Encryption 잠금 해제 될 때까지 자동으로 탑재 됩니다. 

VM을 다시 부팅 하는 테스트를 수행 하 고 부팅 시간 후에도 파일 시스템이 자동으로 탑재 되는지 확인할 수 있습니다. 이 프로세스는 파일 시스템의 수와 크기에 따라 몇 분 정도 걸릴 수 있습니다.

#### <a name="reboot-the-vm-and-verify-after-reboot"></a>VM을 다시 부팅 하 고 다시 부팅 한 후 확인

```bash
shutdown -r now
```
```bash
lsblk
df -h
```
## <a name="steps-for-raid-on-crypt"></a>RAID 온-에 대 한 단계
이제 기본 디스크가 암호화 되었으므로 RAID 구조를 계속 만들 수 있습니다. 이 프로세스는 LVM에 대 한 프로세스와 동일 하지만, 장치 이름을 사용 하는 대신 각 디스크에 대 한/sv/sv/dd 경로를 사용 합니다.

#### <a name="configure-raid-on-top-of-the-encrypted-layer-of-the-disks"></a>디스크의 암호화 된 계층을 기반으로 RAID 구성
```bash
mdadm --create /dev/md10 \
--level 0 \
--raid-devices=4 \
/dev/mapper/c49ff535-1df9-45ad-9dad-f0846509f052 \
/dev/mapper/6712ad6f-65ce-487b-aa52-462f381611a1 \
/dev/mapper/ea607dfd-c396-48d6-bc54-603cf741bc2a \
/dev/mapper/4159c60a-a546-455b-985f-92865d51158c
```
![Mdadm 명령을 통해 구성 된 RAID 정보](./media/disk-encryption/lvm-raid-on-crypt/019-lvm-raid-md-creation.png)

>[!NOTE] 
>/Dev/mapper/device 이름은 **lsblk**의 출력을 기반으로 하는 실제 값으로 바꾸어야 합니다.

### <a name="checkmonitor-raid-creation"></a>RAID 만들기 확인/모니터링
```bash
watch -n1 cat /proc/mdstat
mdadm --examine /dev/mapper/[]
mdadm --detail /dev/md10
```
![RAID 상태](./media/disk-encryption/lvm-raid-on-crypt/020-lvm-raid-md-details.png)

### <a name="create-a-file-system-on-top-of-the-new-raid-device"></a>새 RAID 장치 위에 파일 시스템 만들기
```bash
mkfs.ext4 /dev/md10
```

파일 시스템에 대 한 새 탑재 지점을 만들고/etc/fstab에 새 파일 시스템을 추가 하 고 탑재 합니다.

```bash
for device in md10; do diskuuid="$(blkid -s UUID -o value /dev/${device})"; \
mkdir /raiddata; \
echo "UUID=${diskuuid} /raiddata ext4 defaults,nofail 0 0" >> /etc/fstab; \
mount -a; \
done
```

새 파일 시스템이 탑재 되어 있는지 확인 합니다.

```bash
lsblk -fs
df -h
```
![탑재 된 파일 시스템에 대 한 정보](./media/disk-encryption/lvm-raid-on-crypt/021-lvm-raid-lsblk-md-details.png)

Azure Disk Encryption를 통해 암호화 된 장치 위에 생성 된 RAID 볼륨의 탑재 지점 옵션에 **nofail** 옵션이 추가 되었는지 확인 하는 것이 중요 합니다. 부팅 프로세스 중 또는 유지 관리 모드에서 OS가 중단 되지 않도록 합니다.

**Nofail** 옵션을 사용 하지 않는 경우:

- OS는 Azure Disk Encryption을 시작 하 고 데이터 디스크의 잠금이 해제 되 고 탑재 된 단계에 있지 않습니다.
- 암호화 된 디스크는 부팅 프로세스가 끝날 때 잠금 해제 됩니다. RAID 볼륨 및 파일 시스템은 Azure Disk Encryption 잠금이 해제 될 때까지 자동으로 탑재 됩니다.

VM을 다시 부팅 하는 테스트를 수행 하 고 부팅 시간 후에도 파일 시스템이 자동으로 탑재 되는지 확인할 수 있습니다. 이 프로세스는 파일 시스템의 수와 크기에 따라 몇 분 정도 걸릴 수 있습니다.

```bash
shutdown -r now
```

그리고 로그인 할 수 있는 경우:

```bash
lsblk
df -h
```
## <a name="next-steps"></a>다음 단계

- [Azure Disk Encryption로 암호화 된 논리 볼륨 관리 장치 크기 조정](how-to-resize-encrypted-lvm.md)
- [Azure Disk Encryption 문제 해결](disk-encryption-troubleshooting.md)
