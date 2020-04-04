---
title: 암호화된 장치에서 LVM 및 RAID 구성 - Azure 디스크 암호화
description: 이 문서에서는 Linux VM용 암호화된 장치에서 LVM 및 RAID를 구성하는 방법에 대한 지침을 제공합니다.
author: jofrance
ms.service: security
ms.topic: article
ms.author: jofrance
ms.date: 03/17/2020
ms.custom: seodec18
ms.openlocfilehash: 4e342ff44af38b8e79dc8695c1270b1f5c68e0a8
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80657438"
---
# <a name="configure-lvm-and-raid-on-encrypted-devices"></a>암호화된 장치에서 LVM 및 RAID 구성

이 문서는 암호화된 장치에서 LVM(논리 볼륨 관리) 및 RAID를 수행하는 방법에 대한 단계별 프로세스입니다. 이 프로세스는 다음 환경에 적용됩니다.

- Linux 배포
    - RHEL 7.6 이상
    - 우분투 18.04+
    - 수세 12+
- Azure 디스크 암호화 단일 패스 확장
- Azure 디스크 암호화 이중 패스 확장


## <a name="scenarios"></a>시나리오

이 문서의 절차는 다음 시나리오를 지원합니다.  

- 암호화된 장치 위에 LVM 구성(LVM-온-토굴)
- 암호화된 장치 위에 RAID 구성(RAID-온-토굴)

기본 장치 또는 장치가 암호화된 후 암호화된 계층 위에 LVM 또는 RAID 구조를 만들 수 있습니다. 

물리적 볼륨(PV)은 암호화된 계층 위에 작성됩니다. 물리적 볼륨은 볼륨 그룹을 만드는 데 사용됩니다. 볼륨을 만들고 /etc/fstab에 필요한 항목을 추가합니다. 

![LVM 구조의 레이어 다이어그램](./media/disk-encryption/lvm-raid-on-crypt/000-lvm-raid-crypt-diagram.png)

비슷한 방식으로 RAID 장치는 디스크의 암호화된 계층 위에 만들어집니다. 파일 시스템은 RAID 장치 위에 만들어지고 일반 장치로 /etc/fstab에 추가됩니다.

## <a name="considerations"></a>고려 사항

LVM-on-crypt를 사용하는 것이 좋습니다. RAID는 특정 응용 프로그램 또는 환경 제한으로 인해 LVM을 사용할 수 없는 옵션입니다.

**암호화FormatAll** 옵션을 사용합니다. 이 옵션에 대한 자세한 내용은 [Linux VM의 데이터 디스크에 대한 암호화FormatAll 사용 기능을](https://docs.microsoft.com/azure/virtual-machines/linux/disk-encryption-linux#use-encryptformatall-feature-for-data-disks-on-linux-vms)참조하십시오.

OS를 암호화할 때도 이 방법을 사용할 수 있지만 여기에서는 데이터 드라이브를 암호화하기만 하면 됩니다.

이 프로시저는 Linux VM 및 빠른 시작에서 [Azure 디스크 암호화 시나리오의](https://docs.microsoft.com/azure/virtual-machines/linux/disk-encryption-linux) 필수 구성 조건을 이미 검토했다고 가정합니다. [Quickstart: Create and encrypt a Linux VM with the Azure CLI](https://docs.microsoft.com/azure/virtual-machines/linux/disk-encryption-cli-quickstart)

Azure 디스크 암호화 이중 패스 버전은 사용 중단 경로에 있으며 새 암호화에 더 이상 사용되지 않아야 합니다.

## <a name="general-steps"></a>일반적인 단계

"토굴 중" 구성을 사용하는 경우 다음 절차에 설명된 프로세스를 사용합니다.

>[!NOTE] 
>문서 전체에서 변수를 사용하고 있습니다. 그에 따라 값을 바꿉습니다.

### <a name="deploy-a-vm"></a>VM 배포 
다음 명령은 선택 사항이지만 새로 배포된 VM(가상 시스템)에 적용하는 것이 좋습니다.

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
### <a name="attach-disks-to-the-vm"></a>디스크를 VM에 연결
VM에 연결할 `$N` 새 디스크 수에 대해 다음 명령을 반복합니다.

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

### <a name="verify-that-the-disks-are-attached-to-the-vm"></a>디스크가 VM에 연결되어 있는지 확인합니다.
PowerShell:
```powershell
$VM = Get-AzVM -ResourceGroupName ${RGNAME} -Name ${VMNAME}
$VM.StorageProfile.DataDisks | Select-Object Lun,Name,DiskSizeGB
```
![PowerShell의 연결된 디스크 목록](./media/disk-encryption/lvm-raid-on-crypt/001-lvm-raid-check-disks-powershell.png)

Azure CLI:

```bash
az vm show -g ${RGNAME} -n ${VMNAME} --query storageProfile.dataDisks -o table
```
![Azure CLI의 연결된 디스크 목록](./media/disk-encryption/lvm-raid-on-crypt/002-lvm-raid-check-disks-cli.png)

포털:

![포털에서 연결된 디스크 목록](./media/disk-encryption/lvm-raid-on-crypt/003-lvm-raid-check-disks-portal.png)

OS:

```bash
lsblk 
```
![OS의 연결된 디스크 목록](./media/disk-encryption/lvm-raid-on-crypt/004-lvm-raid-check-disks-os.png)

### <a name="configure-the-disks-to-be-encrypted"></a>암호화할 디스크 구성
이 구성은 운영 체제 수준에서 수행됩니다. 해당 디스크는 Azure 디스크 암호화를 통해 기존 암호화에 대해 구성됩니다.

- 파일 시스템은 디스크 위에 만들어집니다.
- 파일 시스템을 마운트하기 위해 임시 마운트 지점이 생성됩니다.
- 파일 시스템은 부팅 시 장착할 /etc/fstab에서 구성됩니다.

새 디스크에 할당된 장치 문자를 확인합니다. 이 예제에서는 4개의 데이터 디스크를 사용하고 있습니다.

```bash
lsblk 
```
![OS에 연결된 데이터 디스크](./media/disk-encryption/lvm-raid-on-crypt/004-lvm-raid-check-disks-os.png)

### <a name="create-a-file-system-on-top-of-each-disk"></a>각 디스크 위에 파일 시스템 만들기
이 명령은 "for" 주기의 "in" 부분에 정의된 각 디스크에 ext4 파일 시스템의 생성을 다시 한 번 거론합니다.

```bash
for disk in c d e f; do echo mkfs.ext4 -F /dev/sd${disk}; done |bash
```
![ext4 파일 시스템 생성](./media/disk-encryption/lvm-raid-on-crypt/005-lvm-raid-create-temp-fs.png)

최근에 만든 파일 시스템의 보편적으로 고유한 식별자(UUID)를 찾고, 임시 폴더를 만들고, /etc/fstab에 해당 항목을 추가하고, 모든 파일 시스템을 탑재합니다.

이 명령은 "for" 주기의 "in" 부분에 정의된 각 디스크에서도 계속 됩니다.

```bash
for disk in c d e f; do diskuuid="$(blkid -s UUID -o value /dev/sd${disk})"; \
mkdir /tempdata${disk}; \
echo "UUID=${diskuuid} /tempdata${disk} ext4 defaults,nofail 0 0" >> /etc/fstab; \
mount -a; \
done
``` 

### <a name="verify-that-the-disks-are-mounted-properly"></a>디스크가 제대로 장착되었는지 확인
```bash
lsblk
```
![탑재된 임시 파일 시스템 목록](./media/disk-encryption/lvm-raid-on-crypt/006-lvm-raid-verify-temp-fs.png)

또한 디스크가 구성되어 있는지 확인합니다.

```bash
cat /etc/fstab
```
![fstab을 통한 구성 정보](./media/disk-encryption/lvm-raid-on-crypt/007-lvm-raid-verify-temp-fstab.png)

### <a name="encrypt-the-data-disks"></a>데이터 디스크 암호화
키 암호화 키(KEK)를 사용하는 PowerShell:

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

KEK를 사용하는 Azure CLI:

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

모든 디스크가 암호화된 경우에만 다음 단계로 계속합니다.

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

확장자는 파일 시스템을 /var/lib/azure_disk_encryption_config/azure_crypt_mount(이전 암호화) 또는 /etc/crypttab(새 암호화)에 추가합니다.

>[!NOTE] 
>이러한 파일은 수정하지 마십시오.

이 파일은 LVM 또는 RAID가 나중에 사용할 수 있도록 부팅 프로세스 중에 이러한 디스크를 활성화하는 데 주의를 기울입니다. 

이 파일의 마운트 포인트에 대해 걱정하지 마십시오. Azure Disk 암호화는 암호화된 장치 위에 물리적 볼륨 또는 RAID 장치를 만든 후 일반 파일 시스템으로 마운트된 디스크를 얻는 기능을 잃게 됩니다. (이렇게 하면 준비 과정에서 사용한 파일 시스템 형식이 제거됩니다.)

### <a name="remove-the-temporary-folders-and-temporary-fstab-entries"></a>임시 폴더 및 임시 fstab 항목 제거
LVM의 일부로 사용될 디스크의 파일 시스템 마운트를 해제합니다.

```bash
for disk in c d e f; do unmount /tempdata${disk}; done
```
그리고 /etc/fstab 항목을 제거합니다.

```bash
vi /etc/fstab
```
### <a name="verify-that-the-disks-are-not-mounted-and-that-the-entries-on-etcfstab-were-removed"></a>디스크가 탑재되지 않았는지, /etc/fstab의 항목이 제거되었는지 확인합니다.

```bash
lsblk
```
![임시 파일 시스템 마운트 해제 확인](./media/disk-encryption/lvm-raid-on-crypt/012-lvm-raid-verify-disks-not-mounted.png)

디스크가 구성되어 있는지 확인합니다.
```bash
cat /etc/fstab
```
![임시 fstab 항목이 제거된 확인](./media/disk-encryption/lvm-raid-on-crypt/013-lvm-raid-verify-fstab-temp-removed.png)

## <a name="steps-for-lvm-on-crypt"></a>LVM 온 토굴을 위한 단계
기본 디스크가 암호화되었으므로 LVM 구조를 만들 수 있습니다.

장치 이름을 사용하는 대신 각 디스크에 대해 /dev/mapper 경로를 사용하여 디스크 자체가 아닌 디스크 상단의 토굴 계층에서 실제 볼륨을 만듭니다.

### <a name="configure-lvm-on-top-of-the-encrypted-layers"></a>암호화된 레이어 위에 LVM 구성
#### <a name="create-the-physical-volumes"></a>실제 볼륨 만들기
파일 시스템 서명을 지울 수 있는지 묻는 경고가 표시됩니다. **y**를 입력하여 계속하거나 그림과 같이 **에코 "y"를** 사용합니다.

```bash
echo "y" | pvcreate /dev/mapper/c49ff535-1df9-45ad-9dad-f0846509f052
echo "y" | pvcreate /dev/mapper/6712ad6f-65ce-487b-aa52-462f381611a1
echo "y" | pvcreate /dev/mapper/ea607dfd-c396-48d6-bc54-603cf741bc2a
echo "y" | pvcreate /dev/mapper/4159c60a-a546-455b-985f-92865d51158c
```
![실제 볼륨이 생성된 지 확인](./media/disk-encryption/lvm-raid-on-crypt/014-lvm-raid-pvcreate.png)

>[!NOTE] 
>여기서 /dev/매퍼/장치 이름은 **lsblk의**출력에 따라 실제 값에 대해 대체되어야 합니다.

#### <a name="verify-the-information-for-physical-volumes"></a>실제 볼륨에 대한 정보 확인
```bash
pvs
```

![실제 볼륨에 대한 정보](./media/disk-encryption/lvm-raid-on-crypt/015-lvm-raid-pvs.png)

#### <a name="create-the-volume-group"></a>볼륨 그룹 만들기
이미 초기화된 동일한 장치를 사용하여 볼륨 그룹을 만듭니다.

```bash
vgcreate vgdata /dev/mapper/
```

### <a name="check-the-information-for-the-volume-group"></a>볼륨 그룹에 대한 정보 확인

```bash
vgdisplay -v vgdata
```
```bash
pvs
```
![볼륨 그룹에 대한 정보](./media/disk-encryption/lvm-raid-on-crypt/016-lvm-raid-pvs-on-vg.png)

#### <a name="create-logical-volumes"></a>논리적 볼륨 만들기

```bash
lvcreate -L 10G -n lvdata1 vgdata
lvcreate -L 7G -n lvdata2 vgdata
``` 

#### <a name="check-the-created-logical-volumes"></a>생성된 논리 볼륨 확인

```bash
lvdisplay
lvdisplay vgdata/lvdata1
lvdisplay vgdata/lvdata2
```
![논리 볼륨에 대한 정보](./media/disk-encryption/lvm-raid-on-crypt/017-lvm-raid-lvs.png)

#### <a name="create-file-systems-on-top-of-the-structures-for-logical-volumes"></a>논리 볼륨에 대한 구조 위에 파일 시스템 만들기

```bash
echo "yes" | mkfs.ext4 /dev/vgdata/lvdata1
echo "yes" | mkfs.ext4 /dev/vgdata/lvdata2
```

#### <a name="create-the-mount-points-for-the-new-file-systems"></a>새 파일 시스템에 대한 마운트 포인트 작성

```bash
mkdir /data0
mkdir /data1
```

#### <a name="add-the-new-file-systems-to-etcfstab-and-mount-them"></a>/etc/fstab에 새 파일 시스템을 추가하고 마운트합니다.

```bash
echo "/dev/mapper/vgdata-lvdata1 /data0 ext4 defaults,nofail 0 0" >>/etc/fstab
echo "/dev/mapper/vgdata-lvdata2 /data1 ext4 defaults,nofail 0 0" >>/etc/fstab
mount -a
```

#### <a name="verify-that-the-new-file-systems-are-mounted"></a>새 파일 시스템이 탑재되었는지 확인

```bash
lsblk -fs
df -h
```
![탑재된 파일 시스템에 대한 정보](./media/disk-encryption/lvm-raid-on-crypt/018-lvm-raid-lsblk-after-lvm.png)

**lsblk의**이 변형에 대해 종속성을 역순으로 표시하는 장치를 나열합니다. 이 옵션은 원래 /dev/sd[disk] 장치 이름 대신 논리 볼륨별로 그룹화된 장치를 식별하는 데 도움이 됩니다.

Azure 디스크 암호화를 통해 암호화된 장치 위에 생성된 LVM 볼륨의 탑재 지점 옵션에 **nofail** 옵션이 추가되었는지 확인하는 것이 중요합니다. 부팅 프로세스(또는 유지 관리 모드)에서 OS가 막히는 것을 방지합니다.

**nofail** 옵션을 사용하지 않는 경우:

- OS는 Azure 디스크 암호화가 시작되고 데이터 디스크가 잠금 해제되고 탑재되는 단계에 들어가지 않습니다. 
- 암호화된 디스크는 부팅 프로세스가 끝나면 잠금이 해제됩니다. LVM 볼륨 및 파일 시스템은 Azure 디스크 암호화가 잠금을 해제할 때까지 자동으로 탑재됩니다. 

VM 재부팅을 테스트하고 부팅 시간 이후에 파일 시스템이 자동으로 탑재되고 있는지 확인할 수 있습니다. 이 프로세스는 파일 시스템의 수와 크기에 따라 몇 분 정도 걸릴 수 있습니다.

#### <a name="reboot-the-vm-and-verify-after-reboot"></a>VM을 재부팅하고 재부팅 한 후 확인

```bash
shutdown -r now
```
```bash
lsblk
df -h
```
## <a name="steps-for-raid-on-crypt"></a>RAID-온-토굴을 위한 단계
기본 디스크가 암호화되었으므로 RAID 구조를 계속 만들 수 있습니다. 이 프로세스는 LVM용 프로세스와 동일하지만 장치 이름을 사용하는 대신 각 디스크에 대해 /dev/매퍼 경로를 사용합니다.

#### <a name="configure-raid-on-top-of-the-encrypted-layer-of-the-disks"></a>디스크의 암호화된 계층 위에 RAID 구성
```bash
mdadm --create /dev/md10 \
--level 0 \
--raid-devices=4 \
/dev/mapper/c49ff535-1df9-45ad-9dad-f0846509f052 \
/dev/mapper/6712ad6f-65ce-487b-aa52-462f381611a1 \
/dev/mapper/ea607dfd-c396-48d6-bc54-603cf741bc2a \
/dev/mapper/4159c60a-a546-455b-985f-92865d51158c
```
![mdadm 명령을 통해 구성된 RAID에 대한 정보](./media/disk-encryption/lvm-raid-on-crypt/019-lvm-raid-md-creation.png)

>[!NOTE] 
>여기에 /dev/매퍼/장치 이름은 **lsblk의**출력에 따라 실제 값으로 바꿔야 합니다.

### <a name="checkmonitor-raid-creation"></a>RAID 생성 확인/모니터
```bash
watch -n1 cat /proc/mdstat
mdadm --examine /dev/mapper/[]
mdadm --detail /dev/md10
```
![RAID 현황](./media/disk-encryption/lvm-raid-on-crypt/020-lvm-raid-md-details.png)

### <a name="create-a-file-system-on-top-of-the-new-raid-device"></a>새 RAID 장치 위에 파일 시스템 만들기
```bash
mkfs.ext4 /dev/md10
```

파일 시스템에 대한 새 마운트 지점을 만들고/etc/fstab에 새 파일 시스템을 추가하고 마운트합니다.

```bash
for device in md10; do diskuuid="$(blkid -s UUID -o value /dev/${device})"; \
mkdir /raiddata; \
echo "UUID=${diskuuid} /raiddata ext4 defaults,nofail 0 0" >> /etc/fstab; \
mount -a; \
done
```

새 파일 시스템이 탑재되었는지 확인합니다.

```bash
lsblk -fs
df -h
```
![탑재된 파일 시스템에 대한 정보](./media/disk-encryption/lvm-raid-on-crypt/021-lvm-raid-lsblk-md-details.png)

Azure 디스크 암호화를 통해 암호화된 장치 위에 생성된 RAID 볼륨의 탑재 지점 옵션에 **nofail** 옵션이 추가되었는지 확인하는 것이 중요합니다. 부팅 프로세스(또는 유지 관리 모드)에서 OS가 막히는 것을 방지합니다.

**nofail** 옵션을 사용하지 않는 경우:

- OS는 Azure 디스크 암호화가 시작되고 데이터 디스크가 잠금 해제되고 탑재되는 단계에 들어가지 않습니다.
- 암호화된 디스크는 부팅 프로세스가 끝나면 잠금이 해제됩니다. AZURE 디스크 암호화가 잠금을 해제할 때까지 RAID 볼륨 및 파일 시스템이 자동으로 탑재됩니다.

VM 재부팅을 테스트하고 부팅 시간 이후에 파일 시스템이 자동으로 탑재되고 있는지 확인할 수 있습니다. 이 프로세스는 파일 시스템의 수와 크기에 따라 몇 분 정도 걸릴 수 있습니다.

```bash
shutdown -r now
```

그리고 로그인 할 수 있을 때 :

```bash
lsblk
df -h
```
## <a name="next-steps"></a>다음 단계

- [Azure Disk Encryption 문제 해결](disk-encryption-troubleshooting.md)

