---
title: 원격 도구를 사용하여 Azure VM 문제 해결 | Microsoft Docs
description: ''
services: virtual-machines-windows
documentationcenter: ''
author: Deland-Han
manager: dcscontentpm
editor: ''
tags: ''
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: azurecli
ms.date: 01/11/2018
ms.author: delhan
ms.openlocfilehash: b86b1a2d8a49554cc3df99e0a32a2c0ccaacb560
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77920011"
---
# <a name="use-remote-tools-to-troubleshoot-azure-vm-issues"></a>원격 도구를 사용하여 Azure VM 문제 해결

Azure 가상 시스템(VM)에서 문제를 해결하는 경우 RDP(원격 데스크톱 프로토콜)를 사용하는 대신 이 문서에서 설명하는 원격 도구를 사용하여 VM에 연결할 수 있습니다.

## <a name="serial-console"></a>직렬 콘솔

Azure [가상 컴퓨터에 대한 직렬 콘솔을](serial-console-windows.md) 사용하여 원격 Azure VM에서 명령을 실행합니다.

## <a name="remote-cmd"></a>원격 CMD

[PsExec](https://docs.microsoft.com/sysinternals/downloads/psexec)을 다운로드합니다. 다음 명령을 실행하여 VM에 연결합니다.

```cmd
psexec \\<computer>-u user -s cmd
```

>[!NOTE]
>* 명령은 동일한 가상 네트워크에 있는 컴퓨터에서 실행되어야 합니다.
>* DIP 또는 HostName을 사용하여 \<컴퓨터> 대체할 수 있습니다.
>* -s 매개 변수는 시스템 계정(관리자 권한)을 사용하여 명령이 호출되도록 만듭니다.
>* PsExec는 TCP 포트 135 및 445를 사용합니다. 따라서 두 포트는 방화벽에서 열어야 합니다.

## <a name="run-command"></a>명령 실행

실행 명령 기능을 사용하여 VM에서 스크립트를 실행하는 방법에 대한 자세한 내용은 [실행 명령을 사용하여 Windows VM의 PowerShell 스크립트 실행을](../windows/run-command.md)참조하십시오.

## <a name="custom-script-extension"></a>사용자 지정 스크립트 확장

사용자 지정 스크립트 확장 기능을 사용하여 대상 VM에서 사용자 지정 스크립트를 실행할 수 있습니다. 이 기능을 사용하려면 다음 조건이 충족되어야 합니다.

* VM에 연결되어 있습니다.
* Azure 가상 시스템 에이전트가 설치되고 VM에서 예상대로 작동합니다.
* 확장이 VM에 이전에 설치되지 않았습니다.
 
  확장은 스크립트가 처음 사용된 경우에만 스크립트를 삽입합니다. 나중에 이 기능을 사용하는 경우 확장 프로그램은 이미 사용중임을 인식하고 새 스크립트를 업로드하지 않습니다.

저장소 계정에 스크립트를 업로드하고 자체 컨테이너를 생성합니다. 그런 다음, VM에 연결된 컴퓨터의 Azure PowerShell에서 다음 스크립트를 실행합니다.

### <a name="for-classic-deployment-model-vms"></a>클래식 배포 모델 VM용

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]


```powershell
#Set up the basic variables.
$subscriptionID = "<<SUBSCRIPTION ID>>" 
$storageAccount = "<<STORAGE ACCOUNT>>" 
$localScript = "<<FULL PATH OF THE PS1 FILE TO EXECUTE ON THE VM>>" 
$blobName = "file.ps1" #Name you want for the blob in the storage.
$vmName = "<<VM NAME>>" 
$vmCloudService = "<<CLOUD SERVICE>>" #Resource group or cloud service where the VM is hosted. For example, for "demo305.cloudapp.net" the cloud service is going to be demo305.

#Set up the Azure PowerShell module, and ensure the access to the subscription.
Import-Module Azure
Add-AzureAccount  #Ensure login with the account associated with the subscription ID.
Get-AzureSubscription -SubscriptionId $subscriptionID | Select-AzureSubscription

#Set up the access to the storage account, and upload the script.
$storageKey = (Get-AzureStorageKey -StorageAccountName $storageAccount).Primary
$context = New-AzureStorageContext -StorageAccountName $storageAccount -StorageAccountKey $storageKey
$container = "cse" + (Get-Date -Format yyyyMMddhhmmss)<
New-AzureStorageContainer -Name $container -Permission Off -Context $context
Set-AzureStorageBlobContent -File $localScript -Container $container -Blob $blobName  -Context $context

#Push the script into the VM.
$vm = Get-AzureVM -ServiceName $vmCloudService -Name $vmName
Set-AzureVMCustomScriptExtension "CustomScriptExtension" -VM $vm -StorageAccountName $storageAccount -StorageAccountKey $storagekey -ContainerName $container -FileName $blobName -Run $blobName | Update-AzureVM
```

### <a name="for-azure-resource-manager-vms"></a>Azure 리소스 관리자 VM의 경우

 

```powershell
#Set up the basic variables.
$subscriptionID = "<<SUBSCRIPTION ID>>"
$storageAccount = "<<STORAGE ACCOUNT>>"
$storageRG = "<<RESOURCE GROUP OF THE STORAGE ACCOUNT>>" 
$localScript = "<<FULL PATH OF THE PS1 FILE TO EXECUTE ON THE VM>>" 
$blobName = "file.ps1" #Name you want for the blob in the storage.
$vmName = "<<VM NAME>>" 
$vmResourceGroup = "<<RESOURCE GROUP>>"
$vmLocation = "<<DATACENTER>>" 
 
#Set up the Azure PowerShell module, and ensure the access to the subscription.
Login-AzAccount #Ensure login with the account associated with the subscription ID.
Get-AzSubscription -SubscriptionId $subscriptionID | Select-AzSubscription

#Set up the access to the storage account, and upload the script.
$storageKey = (Get-AzStorageAccountKey -ResourceGroupName $storageRG -Name $storageAccount).Value[0]
$context = New-AzureStorageContext -StorageAccountName $storageAccount -StorageAccountKey $storageKey
$container = "cse" + (Get-Date -Format yyyyMMddhhmmss)
New-AzureStorageContainer -Name $container -Permission Off -Context $context
Set-AzureStorageBlobContent -File $localScript -Container $container -Blob $blobName  -Context $context

#Push the script into the VM.
Set-AzVMCustomScriptExtension -Name "CustomScriptExtension" -ResourceGroupName $vmResourceGroup -VMName $vmName -Location $vmLocation -StorageAccountName $storageAccount -StorageAccountKey $storagekey -ContainerName $container -FileName $blobName -Run $blobName
```

## <a name="remote-powershell"></a>원격 PowerShell

>[!NOTE]
>이 옵션을 사용할 수 있도록 TCP 포트 5986(HTTPS)을 열어야 합니다.
>
>Azure 리소스 관리자 VM의 경우 NSG(네트워크 보안 그룹)에서 포트 5986을 열어야 합니다. 자세한 내용은 보안 그룹을 참조하세요. 
>
>RDFE VM의 경우 프라이빗 포트(5986) 및 공용 포트가 있는 엔드포인트가 필요합니다. 그런 다음 NSG에서 공용 포트를 열어야 합니다.

### <a name="set-up-the-client-computer"></a>클라이언트 컴퓨터 설정

PowerShell을 사용하여 VM에 원격으로 연결하려면 먼저 연결을 허용하도록 클라이언트 컴퓨터를 설정해야 합니다. 이렇게 하려면 다음 명령을 적절하게 실행하여 PowerShell 신뢰할 수 있는 호스트 목록에 VM을 추가합니다.

신뢰할 수 있는 호스트 목록에 VM을 추가하려면 다음을 수행하십시오.

```powershell
Set-Item wsman:\localhost\Client\TrustedHosts -value <ComputerName>
```

신뢰할 수 있는 호스트 목록에 여러 VM을 추가하려면 다음을 수행하십시오.

```powershell
Set-Item wsman:\localhost\Client\TrustedHosts -value <ComputerName1>,<ComputerName2>
```

신뢰할 수 있는 호스트 목록에 모든 컴퓨터를 추가하려면:

```powershell
Set-Item wsman:\localhost\Client\TrustedHosts -value *
```

### <a name="enable-remoteps-on-the-vm"></a>VM에서 RemotePS 사용

클래식 배포 모델을 사용하여 만든 VM의 경우 사용자 지정 스크립트 확장을 사용하여 다음 스크립트를 실행합니다.

```powershell
Enable-PSRemoting -Force
New-NetFirewallRule -Name "Allow WinRM HTTPS" -DisplayName "WinRM HTTPS" -Enabled True -Profile Any -Action Allow -Direction Inbound -LocalPort 5986 -Protocol TCP
$thumbprint = (New-SelfSignedCertificate -DnsName $env:COMPUTERNAME -CertStoreLocation Cert:\LocalMachine\My).Thumbprint
$command = "winrm create winrm/config/Listener?Address=*+Transport=HTTPS @{Hostname=""$env:computername""; CertificateThumbprint=""$thumbprint""}"
cmd.exe /C $command
```

Azure 리소스 관리자 VM의 경우 포털에서 실행 명령을 사용하여 EnableRemotePS 스크립트를 실행합니다.

![명령 실행](./media/remote-tools-troubleshoot-azure-vm-issues/run-command.png)

### <a name="connect-to-the-vm"></a>VM에 연결

클라이언트 컴퓨터 위치에 따라 다음 명령을 실행합니다.

* 가상 네트워크 또는 배포 외부

  * 클래식 배포 모델을 사용하여 만든 VM의 경우 다음 명령을 실행합니다.

    ```powershell
    $Skip = New-PSSessionOption -SkipCACheck -SkipCNCheck
    Enter-PSSession -ComputerName  "<<CLOUDSERVICENAME.cloudapp.net>>" -port "<<PUBLIC PORT NUMBER>>" -Credential (Get-Credential) -useSSL -SessionOption $Skip
    ```

  * Azure 리소스 관리자 VM의 경우 먼저 공용 IP 주소에 DNS 이름을 추가합니다. 자세한 단계는 [Azure Portal에서 Windows VM에 대한 정규화된 도메인 이름 만들기](../windows/portal-create-fqdn.md)를 참조하세요. 다음 명령을 실행합니다.

    ```powershell
    $Skip = New-PSSessionOption -SkipCACheck -SkipCNCheck
    Enter-PSSession -ComputerName "<<DNSname.DataCenter.cloudapp.azure.com>>" -port "5986" -Credential (Get-Credential) -useSSL -SessionOption $Skip
    ```

* 가상 네트워크 또는 배포 내에서 다음 명령을 실행합니다.
  
  ```powershell
  $Skip = New-PSSessionOption -SkipCACheck -SkipCNCheck
  Enter-PSSession -ComputerName  "<<HOSTNAME>>" -port 5986 -Credential (Get-Credential) -useSSL -SessionOption $Skip
  ```

>[!NOTE] 
>SkipCaCheck 플래그를 설정하면 세션을 시작할 때 VM에 인증서를 가져오는 요구 사항이 무시됩니다.

호출 명령 cmdlet을 사용하여 VM에서 스크립트를 원격으로 실행할 수도 있습니다.

```powershell
Invoke-Command -ComputerName "<<COMPUTERNAME>" -ScriptBlock {"<<SCRIPT BLOCK>>"}
```

## <a name="remote-registry"></a>원격 레지스트리

>[!NOTE]
>이 옵션을 사용하려면 TCP 포트 135 또는 445를 열어야 합니다.
>
>Azure 리소스 관리자 VM의 경우 NSG에서 포트 5986을 열어야 합니다. 자세한 내용은 보안 그룹을 참조하세요. 
>
>RDFE VM의 경우 프라이빗 포트 5986 및 공용 포트가 있는 엔드포인트가 필요합니다. 또한 NSG에서 공용 포트를 열어야 합니다.

1. 동일한 가상 네트워크의 다른 VM에서 레지스트리 편집기(regedit.exe)를 엽니다.

2. **파일** > **연결 네트워크 레지스트리를**선택합니다.

   ![레지스트리 편집기](./media/remote-tools-troubleshoot-azure-vm-issues/remote-registry.png) 

3. 대상 VM을 **호스트 이름** 또는 동적 IP(바람직함)로 찾아 **개체 이름 입력에 입력하여 상자를 선택합니다.** **dynamic IP**

   ![개체 이름을 입력하여 상자를 선택합니다.](./media/remote-tools-troubleshoot-azure-vm-issues/input-computer-name.png) 
 
4. 대상 VM의 자격 증명을 입력합니다.

5. 레지스트리를 필요한 대로 변경합니다.

## <a name="remote-services-console"></a>원격 서비스 콘솔

>[!NOTE]
>이 옵션을 사용하려면 TCP 포트 135 또는 445를 열어야 합니다.
>
>Azure 리소스 관리자 VM의 경우 NSG에서 포트 5986을 열어야 합니다. 자세한 내용은 보안 그룹을 참조하세요. 
>
>RDFE VM의 경우 프라이빗 포트 5986 및 공용 포트가 있는 엔드포인트가 필요합니다. 또한 NSG에서 공용 포트를 열어야 합니다.

1. 동일한 가상 네트워크의 다른 VM에서 **Services.msc**의 인스턴스를 엽니다.

2. **서비스(로컬)** 을 마우스 오른쪽 단추로 클릭합니다.

3. **다른 컴퓨터에 연결**을 선택합니다.

   ![원격 서비스](./media/remote-tools-troubleshoot-azure-vm-issues/remote-services.png)

4. 대상 VM의 동적 IP를 입력합니다.

   ![입력 동적 IP](./media/remote-tools-troubleshoot-azure-vm-issues/input-ip-address.png)

5. 서비스를 필요한 대로 변경합니다.

## <a name="next-steps"></a>다음 단계

- Enter-PSSession cmdlet에 대한 자세한 내용은 [Enter-PSSession](https://technet.microsoft.com/library/hh849707.aspx)을 참조하십시오.
- 클래식 배포 모델을 사용하는 Windows용 사용자 지정 스크립트 확장에 대한 자세한 내용은 [Windows용 사용자 지정 스크립트 확장을](../extensions/custom-script-classic.md)참조하십시오.
- PsExec는 [PSTools 도구 모음](https://download.sysinternals.com/files/PSTools.zip)의 일부입니다.
- PSTools 제품군에 대한 자세한 내용은 [PSTools](https://docs.microsoft.com/sysinternals/downloads/pstools)를 참조하십시오.


