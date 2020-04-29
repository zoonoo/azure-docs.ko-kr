---
title: Linux에 대 한 암호화 상태를 확인 하는 방법
description: 이 문서에서는 플랫폼 및 OS 수준에서 암호화 상태를 확인 하는 방법에 대 한 지침을 제공 합니다.
author: kailashmsft
ms.service: security
ms.topic: article
ms.author: kaib
ms.date: 03/11/2020
ms.custom: seodec18
ms.openlocfilehash: 0aaa32c46d915eafffcfac9d95cfdd3a24d4086d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80123416"
---
# <a name="how-to-verify-encryption-status-for-linux"></a>Linux에 대 한 암호화 상태를 확인 하는 방법 

**이 시나리오는 ADE 이중 패스 및 단일 패스 확장에 적용 됩니다.**  
이 문서 범위는 다른 방법을 사용 하 여 가상 컴퓨터의 암호화 상태에 대 한 유효성을 검사 하는 것입니다.

### <a name="environment"></a>환경

- Linux 배포

### <a name="procedure"></a>절차

가상 머신은 이중 패스 또는 단일 단계를 사용 하 여 암호화 되었습니다.

암호화 상태는 다른 방법을 사용 하 여 암호화 상태에서 유효성을 검사할 수 있습니다.

>[!NOTE] 
>문서 전체에서 변수를 사용 하 고 있으며 그에 따라 값을 바꿉니다.

### <a name="verification"></a>확인

검사는 포털, PowerShell, AZ CLI 및 VM OS 쪽에서 수행할 수 있습니다. 

이 확인은 특정 VM에 연결 된 디스크를 확인 하 여 수행할 수 있습니다. 

또는 디스크가 연결 되어 있는지 또는 연결 되지 않은 경우 각 개별 디스크의 암호화 설정을 쿼리 합니다.

다음과 같은 다양 한 유효성 검사 방법을 수행 합니다.

## <a name="using-the-portal"></a>포털 사용

Azure Portal의 확장 섹션을 확인 하 여 암호화 상태를 확인 합니다.

**확장** 섹션 안에 ADE 확장이 나열 되어 있습니다. 

클릭 하 고 **상태 메시지**를 살펴보면 현재 암호화 상태를 표시 합니다.

![포털 확인 번호 1](./media/disk-encryption/verify-encryption-linux/portal-check-001.png)

확장 목록에 해당 ADE 확장 버전이 표시 됩니다. 버전 0. x는 ADE 이중 패스에 해당 하 고 버전 1.x는 ADE 단일 단계에 해당 합니다.

확장을 클릭 하 고 *자세한 상태 보기*를 클릭 하 여 자세한 정보를 얻을 수 있습니다.

Json 형식의 암호화 프로세스에 대 한 보다 자세한 상태를 볼 수 있습니다.

![포털 확인 번호 2](./media/disk-encryption/verify-encryption-linux/portal-check-002.png)

![포털 확인 번호 3](./media/disk-encryption/verify-encryption-linux/portal-check-003.png)

암호화 상태를 확인 하는 또 다른 방법은 **디스크** 섹션을 살펴보는 것입니다.

![포털 확인 번호 4](./media/disk-encryption/verify-encryption-linux/portal-check-004.png)

>[!NOTE] 
> 이 상태는 디스크에 기록 된 암호화 설정이 있지만 실제로 OS 수준에서 암호화 되지 않았음을 의미 합니다. 기본적으로 디스크는 먼저 스탬프 되 고 나중에 암호화 됩니다. 암호화 프로세스가 실패 하는 경우 디스크는 스탬프 처리 되지만 암호화 되지 않을 수 있습니다. 디스크가 실제로 암호화 되었는지 확인 하기 위해 OS 수준에서 각 디스크의 암호화를 다시 확인할 수 있습니다.

## <a name="using-powershell"></a>PowerShell 사용

다음 PowerShell 명령을 사용 하 여 암호화 된 VM의 **일반** 암호화 상태에 대 한 유효성을 검사할 수 있습니다.

```azurepowershell
   $VMNAME="VMNAME"
   $RGNAME="RGNAME"
   Get-AzVmDiskEncryptionStatus -ResourceGroupName  ${RGNAME} -VMName ${VMNAME}
```
![PowerShell 1 확인](./media/disk-encryption/verify-encryption-linux/verify-status-ps-01.png)

다음 PowerShell 명령을 사용 하 여 각 개별 디스크에서 암호화 설정을 캡처할 수 있습니다.

### <a name="single-pass"></a>단일 단계
단일 통과 인 경우 암호화 설정은 각 디스크 (OS 및 데이터)의 스탬프 이며 다음과 같이 단일 패스에서 OS 디스크 암호화 설정을 캡처할 수 있습니다.

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
![OS Single pass 01 확인](./media/disk-encryption/verify-encryption-linux/verify-os-single-ps-001.png)

디스크에 스탬프 된 암호화 설정이 없는 경우 출력은 아래와 같이 비어 있게 됩니다.

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

### <a name="dual-pass"></a>이중 패스
이중 Pass에서 암호화 설정은 개별 디스크가 아닌 VM 모델에 스탬프 됩니다.

이중 pass에서 암호화 설정이 스탬프가 있는지 확인 하려면 다음 명령을 사용할 수 있습니다.

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
![이중 pass PowerShell 1 확인](./media/disk-encryption/verify-encryption-linux/verify-dual-ps-001.png)

### <a name="unattached-disks"></a>연결 되지 않은 디스크

VM에 연결 되지 않은 디스크에 대 한 암호화 설정을 확인 합니다.

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

다음 AZ CLI 명령을 사용 하 여 암호화 된 VM의 **일반** 암호화 상태에 대 한 유효성을 검사할 수 있습니다.

```bash
VMNAME="VMNAME"
RGNAME="RGNAME"
az vm encryption show --name ${VMNAME} --resource-group ${RGNAME} --query "substatus"
```
![CLI를 사용 하 여 일반 확인 ](./media/disk-encryption/verify-encryption-linux/verify-gen-cli.png)

### <a name="single-pass"></a>단일 패스
다음 AZ CLI 명령을 사용 하 여 각 개별 디스크에서 암호화 설정의 유효성을 검사할 수 있습니다.

```bash
az vm encryption show -g ${RGNAME} -n ${VMNAME} --query "disks[*].[name, statuses[*].displayStatus]"  -o table
```

![데이터 암호화 설정](./media/disk-encryption/verify-encryption-linux/data-encryption-settings-2.png)

>[!IMPORTANT]
> 디스크에 스탬프 된 암호화 설정이 없는 경우 "디스크가 암호화 되지 않음"으로 표시 됩니다.

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

![OSSingleCLI](./media/disk-encryption/verify-encryption-linux/os-single-cli.png)

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

### <a name="dual-pass"></a>이중 패스

``` bash
az vm encryption show --name ${VMNAME} --resource-group ${RGNAME} -o table
```

![CLI ](./media/disk-encryption/verify-encryption-linux/verify-gen-dual-cli.png) 를 사용 하 여 일반 이중 확인 OS 디스크의 VM 모델 저장소 프로필에서 암호화 설정을 확인할 수도 있습니다.

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

![CLI를 사용 하 여 vm 프로필 이중 확인 ](./media/disk-encryption/verify-encryption-linux/verify-vm-profile-dual-cli.png)

### <a name="unattached-disks"></a>연결 되지 않은 디스크

VM에 연결 되지 않은 디스크에 대 한 암호화 설정을 확인 합니다.

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

특정 디스크에 대 한 세부 정보를 가져오려면 다음을 제공 해야 합니다.

디스크를 포함 하는 저장소 계정의 ID입니다.
해당 특정 저장소 계정에 대 한 연결 문자열입니다.
디스크를 저장 하는 컨테이너의 이름입니다.
디스크 이름입니다.

이 명령은 모든 저장소 계정에 대 한 모든 Id를 나열 합니다.

```bash
az storage account list --query [].[id] -o tsv
```
저장소 계정 Id는 다음과 같은 형식으로 나열 됩니다.

/subscriptions/\<구독 id>/sourceggs\</리소스 그룹 이름>\</providers/microsoft.storage/storageaccounts/Storage account name>

적절 한 ID를 선택 하 고 변수에 저장 합니다.
```bash
id="/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Storage/storageAccounts/<storage account name>"
```
연결 문자열입니다.

이 명령은 하나의 특정 저장소 계정에 대 한 연결 문자열을 가져와서 변수에 저장 합니다.

```bash
ConnectionString=$(az storage account show-connection-string --ids $id --query connectionString -o tsv)
```

컨테이너 이름

다음 명령은 저장소 계정에 있는 모든 컨테이너를 나열 합니다.
```bash
az storage container list --connection-string $ConnectionString --query [].[name] -o tsv
```
디스크에 사용 되는 컨테이너는 일반적으로 "vhd"로 지정 됩니다.

변수에 컨테이너 이름 저장 
```bash
ContainerName="name of the container"
```

디스크 이름입니다.

이 명령을 사용 하 여 특정 컨테이너의 모든 blob을 나열 합니다.
```bash 
az storage blob list -c ${ContainerName} --connection-string $ConnectionString --query [].[name] -o tsv
```
쿼리 하려는 디스크를 선택 하 고 변수에 해당 이름을 저장 합니다.
```bash
DiskName="diskname.vhd"
```
디스크 암호화 설정 쿼리
```bash
az storage blob show -c ${ContainerName} --connection-string ${ConnectionString} -n ${DiskName} --query metadata.DiskEncryptionSettings
```

## <a name="from-the-os"></a>OS에서
데이터 디스크 파티션이 암호화 되어 있는지 확인 합니다 (그리고 OS 디스크는 그렇지 않음).

파티션/디스크가 암호화 되는 경우이는 암호 형식 **으로 표시** 되 고 암호화 되지 않은 경우 **파트/디스크** 형식으로 표시 됩니다.

``` bash
lsblk
```

![Os 묘지 계층 ](./media/disk-encryption/verify-encryption-linux/verify-os-crypt-layer.png)

다음 "lsblk" variant를 사용 하 여 자세한 정보를 얻을 수 있습니다. 

확장에 의해 탑재 되 **는 문자 형식** 계층이 표시 됩니다.

다음 예제에서는 "**crypto\_luks FSTYPE**"가 있는 논리 볼륨 및 일반 디스크를 보여 줍니다.

```bash
lsblk -o NAME,TYPE,FSTYPE,LABEL,SIZE,RO,MOUNTPOINT
```
![Os 묘지 계층 2](./media/disk-encryption/verify-encryption-linux/verify-os-crypt-layer-2.png)

추가 단계로, 데이터 디스크에 로드 된 키가 있는지 확인할 수도 있습니다.

``` bash
cryptsetup luksDump /dev/VGNAME/LVNAME
```

``` bash
cryptsetup luksDump /dev/sdd1
```

그리고 서 수로 표시 되는 dm 장치

```bash
dmsetup ls --target crypt
```

## <a name="next-steps"></a>다음 단계

- [Azure Disk Encryption 문제 해결](disk-encryption-troubleshooting.md)
