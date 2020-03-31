---
title: 리눅스에 대 한 암호화 상태를 확인 하는 방법
description: 이 문서에서는 플랫폼 및 OS 수준에서 암호화 상태를 확인하는 방법에 대한 지침을 제공합니다.
author: kailashmsft
ms.service: security
ms.topic: article
ms.author: kaib
ms.date: 03/11/2020
ms.custom: seodec18
ms.openlocfilehash: 0aaa32c46d915eafffcfac9d95cfdd3a24d4086d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80123416"
---
# <a name="how-to-verify-encryption-status-for-linux"></a>리눅스에 대 한 암호화 상태를 확인 하는 방법 

**이 시나리오는 ADE 이중 패스 및 단일 패스 확장에 적용됩니다.**  
이 문서 범위는 다른 방법을 사용하여 가상 시스템의 암호화 상태를 확인하는 것입니다.

### <a name="environment"></a>Environment

- Linux 배포

### <a name="procedure"></a>절차

가상 시스템은 이중 패스 또는 단일 패스를 사용하여 암호화되었습니다.

암호화 상태는 다른 방법을 사용하여 암호화 하는 동안 또는 후에 유효성을 검사할 수 있습니다.

>[!NOTE] 
>문서 전체에서 변수를 사용하고 그에 따라 값을 바꿉습니다.

### <a name="verification"></a>확인

확인은 포털, PowerShell, AZ CLI 및 VM OS 측에서 수행할 수 있습니다. 

이 확인은 특정 VM에 연결된 디스크를 확인하여 수행할 수 있습니다. 

또는 각 개별 디스크의 암호화 설정을 쿼리하여 디스크가 연결되어 있는지 또는 연결 해제되었는지 여부를 쿼리합니다.

다른 유효성 검사 방법 아래:

## <a name="using-the-portal"></a>포털 사용

Azure 포털의 확장 섹션을 확인하여 암호화 상태의 유효성을 검사합니다.

**확장** 섹션 안에 ADE 확장이 나열되어 있습니다. 

그것을 클릭하고 **상태 메시지를**살펴, 그것은 현재 암호화 상태를 나타냅니다 :

![포털 확인 번호 1](./media/disk-encryption/verify-encryption-linux/portal-check-001.png)

확장 목록에서 해당 ADE 확장 버전이 표시됩니다. 버전 0.x는 ADE 듀얼 패스에 해당하며 버전 1.x는 ADE 단일 패스에 해당합니다.

확장을 클릭한 다음 *자세한 상태 보기에서*자세한 세부 정보를 얻을 수 있습니다.

json 형식으로 암호화 프로세스의 자세한 상태를 확인할 수 있습니다.

![포털 체크 번호 2](./media/disk-encryption/verify-encryption-linux/portal-check-002.png)

![포털 확인 번호 3](./media/disk-encryption/verify-encryption-linux/portal-check-003.png)

암호화 상태를 확인하는 또 다른 방법은 **디스크** 섹션을 살펴보는 것입니다.

![포털 체크 번호 4](./media/disk-encryption/verify-encryption-linux/portal-check-004.png)

>[!NOTE] 
> 이 상태는 디스크에 암호화 설정이 스탬프가 찍혀 있지만 실제로 OS 수준에서 암호화되지 는 않음을 의미합니다. 의도적으로 디스크는 먼저 스탬프가 찍혀 나중에 암호화됩니다. 암호화 프로세스가 실패하면 디스크가 도각되지만 암호화되지 않을 수 있습니다. 디스크가 실제로 암호화되었는지 확인하려면 OS 수준에서 각 디스크의 암호화를 다시 확인할 수 있습니다.

## <a name="using-powershell"></a>PowerShell 사용

다음 PowerShell 명령을 사용하여 암호화된 VM의 **일반** 암호화 상태를 확인할 수 있습니다.

```azurepowershell
   $VMNAME="VMNAME"
   $RGNAME="RGNAME"
   Get-AzVmDiskEncryptionStatus -ResourceGroupName  ${RGNAME} -VMName ${VMNAME}
```
![파워쉘 1 확인](./media/disk-encryption/verify-encryption-linux/verify-status-ps-01.png)

다음 PowerShell 명령을 사용하여 각 개별 디스크에서 암호화 설정을 캡처할 수 있습니다.

### <a name="single-pass"></a>싱글 패스
단일 패스의 경우 암호화 설정이 각 디스크(OS 및 Data)에 스탬프가 찍혀 있는 경우 다음과 같이 단일 패스로 OS 디스크 암호화 설정을 캡처할 수 있습니다.

``` powershell
$RGNAME = "RGNAME"
$VMNAME = "VMNAME"

$VM = Get-AzVM -Name ${VMNAME} -ResourceGroupName ${RGNAME}  
 $Sourcedisk = Get-AzDisk -ResourceGroupName ${RGNAME} -DiskName $VM.StorageProfile.OsDisk.Name
 Write-Host "============================================================================================================================================================="
 Write-Host "Encryption Settings:"
 Write-Host "============================================================================================================================================================="
 Write-Host "Enabled:" $Sourcedisk.EncryptionSettingsCollection.Enabled
 Write-Host "Version:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettingsVersion
 Write-Host "Source Vault:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.DiskEncryptionKey.SourceVault.Id
 Write-Host "Secret URL:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.DiskEncryptionKey.SecretUrl
 Write-Host "Key URL:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.KeyEncryptionKey.KeyUrl
 Write-Host "============================================================================================================================================================="
```
![OS 싱글 패스 01 확인](./media/disk-encryption/verify-encryption-linux/verify-os-single-ps-001.png)

디스크에 암호화 설정스탬프가 찍혀 있지 않으면 아래와 같이 출력이 비어 있습니다.

![OS 암호화 설정 2](./media/disk-encryption/verify-encryption-linux/os-encryption-settings-2.png)

데이터 디스크 암호화 설정 캡처:

```azurepowershell
$RGNAME = "RGNAME"
$VMNAME = "VMNAME"

$VM = Get-AzVM -Name ${VMNAME} -ResourceGroupName ${RGNAME}
 clear
 foreach ($i in $VM.StorageProfile.DataDisks|ForEach-Object{$_.Name})
 {
 Write-Host "============================================================================================================================================================="
 Write-Host "Encryption Settings:"
 Write-Host "============================================================================================================================================================="
 Write-Host "Checking Disk:" $i
 $Disk=(Get-AzDisk -ResourceGroupName ${RGNAME} -DiskName $i)
 Write-Host "Encryption Enable: " $Sourcedisk.EncryptionSettingsCollection.Enabled
 Write-Host "Encryption KeyEncryptionKey: " $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.KeyEncryptionKey.KeyUrl;
 Write-Host "Encryption DiskEncryptionKey: " $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.DiskEncryptionKey.SecretUrl;
 Write-Host "============================================================================================================================================================="
 }
```
![데이터 단일 ps 001 확인](./media/disk-encryption/verify-encryption-linux/verify-data-single-ps-001.png)

### <a name="dual-pass"></a>듀얼 패스
듀얼 패스에서 암호화 설정은 각 개별 디스크가 아닌 VM 모델에 스탬프가 찍혀 있습니다.

암호화 설정이 듀얼 패스에 스탬프가 찍혀 있는지 확인하려면 다음 명령을 사용할 수 있습니다.

```azurepowershell
$RGNAME = "RGNAME"
$VMNAME = "VMNAME"

$vm = Get-AzVm -ResourceGroupName ${RGNAME} -Name ${VMNAME};
$Sourcedisk = Get-AzDisk -ResourceGroupName ${RGNAME} -DiskName $VM.StorageProfile.OsDisk.Name
clear
Write-Host "============================================================================================================================================================="
Write-Host "Encryption Settings:"
Write-Host "============================================================================================================================================================="
Write-Host "Enabled:" $Sourcedisk.EncryptionSettingsCollection.Enabled
Write-Host "Version:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettingsVersion
Write-Host "Source Vault:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.DiskEncryptionKey.SourceVault.Id
Write-Host "Secret URL:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.DiskEncryptionKey.SecretUrl
Write-Host "Key URL:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.KeyEncryptionKey.KeyUrl
Write-Host "============================================================================================================================================================="
```
![듀얼 패스 파워쉘 1 확인](./media/disk-encryption/verify-encryption-linux/verify-dual-ps-001.png)

### <a name="unattached-disks"></a>첨부되지 않은 디스크

VM에 연결되지 않은 디스크의 암호화 설정을 확인합니다.

### <a name="managed-disks"></a>관리 디스크
```powershell
$Sourcedisk = Get-AzDisk -ResourceGroupName ${RGNAME} -DiskName ${TARGETDISKNAME}
Write-Host "============================================================================================================================================================="
Write-Host "Encryption Settings:"
Write-Host "============================================================================================================================================================="
Write-Host "Enabled:" $Sourcedisk.EncryptionSettingsCollection.Enabled
Write-Host "Version:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettingsVersion
Write-Host "Source Vault:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.DiskEncryptionKey.SourceVault.Id
Write-Host "Secret URL:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.DiskEncryptionKey.SecretUrl
Write-Host "Key URL:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.KeyEncryptionKey.KeyUrl
Write-Host "============================================================================================================================================================="
```
## <a name="using-az-cli"></a>AZ CLI 사용

다음 AZ CLI 명령을 사용하여 암호화된 VM의 **일반** 암호화 상태를 확인할 수 있습니다.

```bash
VMNAME="VMNAME"
RGNAME="RGNAME"
az vm encryption show --name ${VMNAME} --resource-group ${RGNAME} --query "substatus"
```
![CLI를 사용하여 일반 확인 ](./media/disk-encryption/verify-encryption-linux/verify-gen-cli.png)

### <a name="single-pass"></a>싱글 패스
다음과 같은 AZ CLI 명령을 사용하여 각 개별 디스크의 암호화 설정의 유효성을 검사할 수 있습니다.

```bash
az vm encryption show -g ${RGNAME} -n ${VMNAME} --query "disks[*].[name, statuses[*].displayStatus]"  -o table
```

![데이터 암호화 설정](./media/disk-encryption/verify-encryption-linux/data-encryption-settings-2.png)

>[!IMPORTANT]
> 디스크에 암호화 설정이 스탬프가 찍혀 있지 않은 경우 "디스크가 암호화되지 않음"으로 표시됩니다.

자세한 상태 및 암호화 설정:

OS 디스크:

```bash
RGNAME="RGNAME"
VMNAME="VNAME"

disk=`az vm show -g ${RGNAME} -n ${VMNAME} --query storageProfile.osDisk.name -o tsv`
for disk in $disk; do \
echo "============================================================================================================================================================="
echo -ne "Disk Name: "; az disk show -g ${RGNAME} -n ${disk} --query name -o tsv; \
echo -ne "Encryption Enabled: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.enabled -o tsv; \
echo -ne "Version: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query encryptionSettingsCollection.encryptionSettingsVersion -o tsv; \
echo -ne "Disk Encryption Key: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.encryptionSettings[].diskEncryptionKey.secretUrl -o tsv; \
echo -ne "key Encryption Key: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.encryptionSettings[].keyEncryptionKey.keyUrl -o tsv; \
echo "============================================================================================================================================================="
done
```

![오스미미클리](./media/disk-encryption/verify-encryption-linux/os-single-cli.png)

데이터 디스크:

```bash
RGNAME="RGNAME"
VMNAME="VMNAME"
az vm encryption show --name ${VMNAME} --resource-group ${RGNAME} --query "substatus"

for disk in `az vm show -g ${RGNAME} -n ${VMNAME} --query storageProfile.dataDisks[].name -o tsv`; do \
echo "============================================================================================================================================================="; \
echo -ne "Disk Name: "; az disk show -g ${RGNAME} -n ${disk} --query name -o tsv; \
echo -ne "Encryption Enabled: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.enabled -o tsv; \
echo -ne "Version: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query encryptionSettingsCollection.encryptionSettingsVersion -o tsv; \
echo -ne "Disk Encryption Key: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.encryptionSettings[].diskEncryptionKey.secretUrl -o tsv; \
echo -ne "key Encryption Key: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.encryptionSettings[].keyEncryptionKey.keyUrl -o tsv; \
echo "============================================================================================================================================================="
done
```

![데이터 단일 CLI ](./media/disk-encryption/verify-encryption-linux/data-single-cli.png)

### <a name="dual-pass"></a>듀얼 패스

``` bash
az vm encryption show --name ${VMNAME} --resource-group ${RGNAME} -o table
```

![CLI를 ](./media/disk-encryption/verify-encryption-linux/verify-gen-dual-cli.png) 사용하여 일반 이중 확인 OS 디스크의 VM 모델 저장소 프로필에서 암호화 설정을 확인할 수도 있습니다.

```bash
disk=`az vm show -g ${RGNAME} -n ${VMNAME} --query storageProfile.osDisk.name -o tsv`
for disk in $disk; do \
echo "============================================================================================================================================================="; \
echo -ne "Disk Name: "; az disk show -g ${RGNAME} -n ${disk} --query name -o tsv; \
echo -ne "Encryption Enabled: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.enabled -o tsv; \
echo -ne "Version: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query encryptionSettingsCollection.encryptionSettingsVersion -o tsv; \
echo -ne "Disk Encryption Key: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.encryptionSettings[].diskEncryptionKey.secretUrl -o tsv; \
echo -ne "key Encryption Key: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.encryptionSettings[].keyEncryptionKey.keyUrl -o tsv; \
echo "============================================================================================================================================================="
done
```

![CLI를 사용하여 vm 프로파일 이중 확인 ](./media/disk-encryption/verify-encryption-linux/verify-vm-profile-dual-cli.png)

### <a name="unattached-disks"></a>첨부되지 않은 디스크

VM에 연결되지 않은 디스크의 암호화 설정을 확인합니다.

### <a name="managed-disks"></a>관리 디스크

```bash
RGNAME="RGNAME"
TARGETDISKNAME="DISKNAME"
echo "============================================================================================================================================================="
echo -ne "Disk Name: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query name -o tsv; \
echo -ne "Encryption Enabled: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query encryptionSettingsCollection.enabled -o tsv; \
echo -ne "Version: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query encryptionSettingsCollection.encryptionSettingsVersion -o tsv; \
echo -ne "Disk Encryption Key: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query encryptionSettingsCollection.encryptionSettings[].diskEncryptionKey.secretUrl -o tsv; \
echo -ne "key Encryption Key: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query encryptionSettingsCollection.encryptionSettings[].keyEncryptionKey.keyUrl -o tsv; \
echo "============================================================================================================================================================="
```
### <a name="unmanaged-disks"></a>관리되지 않는 디스크

비관리 디스크는 Azure Storage 계정에 페이지 Blob으로 저장된 VHD 파일입니다.

특정 디스크의 세부 정보를 얻으려면 다음을 제공해야 합니다.

디스크를 포함하는 저장소 계정의 ID입니다.
특정 저장소 계정에 대한 연결 문자열입니다.
디스크를 저장하는 컨테이너의 이름입니다.
디스크 이름입니다.

이 명령은 모든 저장소 계정에 대한 모든 ID를 나열합니다.

```bash
az storage account list --query [].[id] -o tsv
```
저장소 계정 아이디는 다음 형식으로 나열됩니다.

/구독/구독\<id>/resourceGroups/리소스\<그룹 이름>/공급자/Microsoft.저장소/저장소 계정\<이름>

적절한 ID를 선택하고 변수에 저장합니다.
```bash
id="/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Storage/storageAccounts/<storage account name>"
```
연결 문자열입니다.

이 명령은 하나의 특정 저장소 계정에 대한 연결 문자열을 가져옵니다 및 변수에 저장 :

```bash
ConnectionString=$(az storage account show-connection-string --ids $id --query connectionString -o tsv)
```

컨테이너 이름

다음 명령은 저장소 계정 아래에 있는 모든 컨테이너를 나열합니다.
```bash
az storage container list --connection-string $ConnectionString --query [].[name] -o tsv
```
디스크에 사용되는 컨테이너의 이름은 일반적으로 "vhds"입니다.

컨테이너 이름을 변수에 저장 
```bash
ContainerName="name of the container"
```

디스크 이름입니다.

이 명령을 사용하여 특정 컨테이너의 모든 Blob을 나열합니다.
```bash 
az storage blob list -c ${ContainerName} --connection-string $ConnectionString --query [].[name] -o tsv
```
쿼리할 디스크를 선택하고 해당 이름을 변수에 저장합니다.
```bash
DiskName="diskname.vhd"
```
디스크 암호화 설정 쿼리
```bash
az storage blob show -c ${ContainerName} --connection-string ${ConnectionString} -n ${DiskName} --query metadata.DiskEncryptionSettings
```

## <a name="from-the-os"></a>OS에서
데이터 디스크 파티션이 암호화되어 있고 OS 디스크가 암호화되지 않은지 확인합니다.

파티션/디스크가 암호화되면 **암호화되지** 않은 경우 암호 해독 유형으로 **표시됩니다.**

``` bash
lsblk
```

![오스 토굴 층 ](./media/disk-encryption/verify-encryption-linux/verify-os-crypt-layer.png)

다음 "lsblk" 변형을 사용하여 자세한 내용을 확인할 수 있습니다. 

확장에 의해 탑재된 **암호 해독** 유형 레이어가 표시됩니다.

다음 예제에서는 **"암호화\_LUKS FSTYPE"을**갖는 논리 볼륨 및 일반 디스크를 보여 주십습니다.

```bash
lsblk -o NAME,TYPE,FSTYPE,LABEL,SIZE,RO,MOUNTPOINT
```
![오스 토굴 층 2](./media/disk-encryption/verify-encryption-linux/verify-os-crypt-layer-2.png)

추가 단계로 데이터 디스크에 키가 로드되었는지 여부를 확인할 수도 있습니다.

``` bash
cryptsetup luksDump /dev/VGNAME/LVNAME
```

``` bash
cryptsetup luksDump /dev/sdd1
```

그리고 어떤 DM 장치가 토굴로 나열되어 있는지

```bash
dmsetup ls --target crypt
```

## <a name="next-steps"></a>다음 단계

- [Azure Disk Encryption 문제 해결](disk-encryption-troubleshooting.md)
