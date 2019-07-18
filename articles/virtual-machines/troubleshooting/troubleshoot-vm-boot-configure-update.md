---
title: VM 시작이 다음 상태에서 중지되었습니다. "Windows가 준비 중입니다. Azure에서 컴퓨터를 끄지 마세요" | Microsoft Docs
description: 다음의 문제 해결을 위한 단계를 소개합니다. VM 시작이 다음 상태에서 중지되었습니다. "Windows가 준비 중입니다. 컴퓨터를 끄지 마세요."
services: virtual-machines-windows
documentationcenter: ''
author: Deland-Han
manager: willchen
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 09/18/2018
ms.author: delhan
ms.openlocfilehash: c3592529d20680c6920e569887effee4ffe38344
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "64683999"
---
# <a name="vm-startup-is-stuck-on-getting-windows-ready-dont-turn-off-your-computer-in-azure"></a>VM 시작이 다음 상태에서 중지되었습니다. "Windows가 준비 중입니다. Azure에서 컴퓨터를 끄지 마세요."

이 문서에서는 VM(가상 머신)이 “Windows가 준비 중입니다. 시작하는 동안 컴퓨터를 끄지 마세요” 상태에서 중지된 문제를 해결하도록 도와줍니다.

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="symptoms"></a>증상

**부트 진단**을 사용하여 VM의 스크린샷을 확인하는 경우 운영 체제가 완전히 시작되지 않은 것을 알 수 있습니다. VM은 다음과 같은 메시지를 표시합니다. "Windows가 준비 중입니다. 컴퓨터를 끄지 마세요."

![Windows Server 2012 R2에 대한 메시지 예제](./media/troubleshoot-vm-configure-update-boot/message1.png)

![메시지 예제](./media/troubleshoot-vm-configure-update-boot/message2.png)

## <a name="cause"></a>원인

일반적으로 이 문제는 구성이 변경된 후 서버가 마지막 다시 부팅을 수행하는 동안 발생합니다. 구성 변경은 Windows 업데이트로 인해 또는 서버 역할/기능 변경으로 인해 초기화될 수 있습니다. Windows 업데이트의 경우, 업데이트의 크기가 크면 운영 체제가 변경 내용을 다시 구성하는 데 더 많은 시간이 걸립니다.

## <a name="back-up-the-os-disk"></a>OS 디스크 백업

이 문제를 해결하려고 하기 전에 OS 디스크를 백업합니다.

### <a name="for-vms-with-an-encrypted-disk-you-must-unlock-the-disks-first"></a>암호화된 디스크를 사용하는 VM의 경우 먼저 디스크의 잠금을 해제해야 함

VM이 암호화된 VM인지 여부를 확인하려면 이러한 단계를 따릅니다.

1. Azure Portal에서 VM을 연 다음, 디스크를 찾습니다.

2. 암호화를 사용하도록 설정했는지 여부를 확인하려면 **암호화** 열을 살펴봅니다.

OS 디스크가 암호화되어 있으면 암호화된 디스크의 잠금을 해제합니다. 디스크의 잠금을 해제하려면 이러한 단계를 따릅니다.

1. 영향을 받는 VM과 동일한 리소스 그룹, 저장소 계정 및 위치에 있는 복구 VM을 만듭니다.

2. Azure Portal에서 영향을 받은 VM을 삭제하고 디스크를 유지합니다.

3. PowerShell을 관리자 권한으로 실행합니다.

4. 다음 cmdlet을 실행하여 비밀 이름을 가져옵니다.

    ```Powershell
    Login-AzAccount
 
    $vmName = “VirtualMachineName”
    $vault = “AzureKeyVaultName”
 
    # Get the Secret for the C drive from Azure Key Vault
    Get-AzureKeyVaultSecret -VaultName $vault | where {($_.Tags.MachineName -eq $vmName) -and ($_.Tags.VolumeLetter -eq “C:\”) -and ($_.ContentType -eq ‘BEK‘)}

    # OR Use the below command to get BEK keys for all the Volumes
    Get-AzureKeyVaultSecret -VaultName $vault | where {($_.Tags.MachineName -eq   $vmName) -and ($_.ContentType -eq ‘BEK’)}
    ```

5. 비밀 이름을 확인한 후 PowerShell에서 다음 명령을 실행합니다.

    ```Powershell
    $secretName = 'SecretName'
    $keyVaultSecret = Get-AzureKeyVaultSecret -VaultName $vault -Name $secretname
    $bekSecretBase64 = $keyVaultSecret.SecretValueText
    ```

6. Base64로 인코딩된 값을 바이트로 변환하고 출력을 파일에 씁니다. 

    > [!Note]
    > USB 잠금 해제 옵션을 사용하는 경우 BEK 파일 이름이 원래 BEK GUID와 일치해야 합니다. 이러한 단계를 따르기 전에 "BEK"라는 C 드라이브에 폴더를 만듭니다.
    
    ```Powershell
    New-Item -ItemType directory -Path C:\BEK
    $bekFileBytes = [Convert]::FromBase64String($bekSecretbase64)
    $path = “c:\BEK\$secretName.BEK”
    [System.IO.File]::WriteAllBytes($path,$bekFileBytes)
    ```

7. PC에서 BEK 파일이 만들어지면 해당 파일을 잠근 OS 디스크가 연결된 복구 VM으로 복사합니다. BEK 파일 위치를 사용하여 다음 명령을 실행합니다.

    ```Powershell
    manage-bde -status F:
    manage-bde -unlock F: -rk C:\BEKFILENAME.BEK
    ```
    **선택 사항**: 일부 시나리오에서는 이 명령을 사용하여 디스크의 암호를 해독해야 할 수도 있습니다.
   
    ```Powershell
    manage-bde -off F:
    ```

    > [!Note]
    > 이전 명령은 암호화될 디스크가 문자 F에 있다고 가정합니다.

8. 로그를 수집해야 할 경우 **DRIVE LETTER:\Windows\System32\winevt\Logs** 경로로 이동합니다.

9. 복구 컴퓨터에서 드라이브를 분리합니다.

### <a name="create-a-snapshot"></a>스냅샷 만들기

스냅샷을 만들려면 [디스크 스냅샷](../windows/snapshot-copy-managed-disk.md)의 단계를 따릅니다.

## <a name="collect-an-os-memory-dump"></a>OS 메모리 덤프 수집

구성 중에 VM이 중단되면 [OS 덤프 수집](troubleshoot-common-blue-screen-error.md#collect-memory-dump-file) 섹션에 있는 단계를 사용하여 OS 덤프를 수집합니다.

## <a name="contact-microsoft-support"></a>Microsoft 지원에 문의

덤프 파일을 수집한 후 [Microsoft 고객 지원팀](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)에 문의하여 근본 원인을 분석합니다.


## <a name="rebuild-the-vm-by-using-powershell"></a>PowerShell을 사용하여 VM 다시 빌드

메모리 덤프 파일을 수집한 후 이러한 단계에 따라 VM을 다시 빌드합니다.

**관리되지 않는 디스크**

```powershell
# To log in to Azure Resource Manager
Login-AzAccount

# To view all subscriptions for your account
Get-AzSubscription

# To select a default subscription for your current session
Get-AzSubscription –SubscriptionID “SubscriptionID” | Select-AzSubscription

$rgname = "RGname"
$loc = "Location"
$vmsize = "VmSize"
$vmname = "VmName"
$vm = New-AzVMConfig -VMName $vmname -VMSize $vmsize;

$nic = Get-AzNetworkInterface -Name ("NicName") -ResourceGroupName $rgname;
$nicId = $nic.Id;

$vm = Add-AzVMNetworkInterface -VM $vm -Id $nicId;

$osDiskName = "OSdiskName"
$osDiskVhdUri = "OSdiskURI"

$vm = Set-AzVMOSDisk -VM $vm -VhdUri $osDiskVhdUri -name $osDiskName -CreateOption attach -Windows

New-AzVM -ResourceGroupName $rgname -Location $loc -VM $vm -Verbose
```

**관리되는 디스크**

```powershell
# To log in to Azure Resource Manager
Login-AzAccount

# To view all subscriptions for your account
Get-AzSubscription

# To select a default subscription for your current session
Get-AzSubscription –SubscriptionID "SubscriptionID" | Select-AzSubscription

#Fill in all variables
$subid = "SubscriptionID"
$rgName = "ResourceGroupName";
$loc = "Location";
$vmSize = "VmSize";
$vmName = "VmName";
$nic1Name = "FirstNetworkInterfaceName";
#$nic2Name = "SecondNetworkInterfaceName";
$avName = "AvailabilitySetName";
$osDiskName = "OsDiskName";
$DataDiskName = "DataDiskName"

#This can be found by selecting the Managed Disks you wish you use in the Azure portal if the format below doesn't match
$osDiskResourceId = "/subscriptions/$subid/resourceGroups/$rgname/providers/Microsoft.Compute/disks/$osDiskName";
$dataDiskResourceId = "/subscriptions/$subid/resourceGroups/$rgname/providers/Microsoft.Compute/disks/$DataDiskName";

$vm = New-AzVMConfig -VMName $vmName -VMSize $vmSize;

#Uncomment to add Availability Set
#$avSet = Get-AzAvailabilitySet –Name $avName –ResourceGroupName $rgName;
#$vm = New-AzVMConfig -VMName $vmName -VMSize $vmSize -AvailabilitySetId $avSet.Id;

#Get NIC Resource Id and add
$nic1 = Get-AzNetworkInterface -Name $nic1Name -ResourceGroupName $rgName;
$vm = Add-AzVMNetworkInterface -VM $vm -Id $nic1.Id -Primary;

#Uncomment to add a secondary NIC
#$nic2 = Get-AzNetworkInterface -Name $nic2Name -ResourceGroupName $rgName;
#$vm = Add-AzVMNetworkInterface -VM $vm -Id $nic2.Id;

#Windows VM
$vm = Set-AzVMOSDisk -VM $vm -ManagedDiskId $osDiskResourceId -name $osDiskName -CreateOption Attach -Windows;

#Linux VM
#$vm = Set-AzVMOSDisk -VM $vm -ManagedDiskId $osDiskResourceId -name $osDiskName -CreateOption Attach -Linux;

#Uncomment to add additional Data Disk
#Add-AzVMDataDisk -VM $vm -ManagedDiskId $dataDiskResourceId -Name $dataDiskName -Caching None -DiskSizeInGB 1024 -Lun 0 -CreateOption Attach;

New-AzVM -ResourceGroupName $rgName -Location $loc -VM $vm;
```
