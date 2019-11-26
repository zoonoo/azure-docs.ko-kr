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
ms.openlocfilehash: 3f028431fcd4b338d2e610ce1828a02b753c4d32
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/25/2019
ms.locfileid: "74483710"
---
# <a name="use-remote-tools-to-troubleshoot-azure-vm-issues"></a>원격 도구를 사용하여 Azure VM 문제 해결

When you troubleshoot issues on an Azure virtual machine (VM), you can connect to the VM by using the remote tools that are discussed in this article instead of using the Remote Desktop Protocol (RDP).

## <a name="serial-console"></a>직렬 콘솔

Use a [serial console for Azure Virtual Machines](serial-console-windows.md) to run commands on the remote Azure VM.

## <a name="remote-cmd"></a>원격 CMD

[PsExec](https://docs.microsoft.com/sysinternals/downloads/psexec)을 다운로드합니다. 다음 명령을 실행하여 VM에 연결합니다.

```cmd
psexec \\<computer>-u user -s cmd
```

>[!NOTE]
>* The command must be run on a computer that's in the same virtual network.
>* DIP or HostName can be used to replace \<computer>.
>* -s 매개 변수는 시스템 계정(관리자 권한)을 사용하여 명령이 호출되도록 만듭니다.
>* PsExec는 TCP 포트 135 및 445를 사용합니다. As a result, the two ports have to be open on the firewall.

## <a name="run-command"></a>명령 실행

For more information about how to use the run command feature to run scripts on the VM, see [Run PowerShell scripts in your Windows VM with run command](../windows/run-command.md).

## <a name="custom-script-extension"></a>사용자 지정 스크립트 확장

사용자 지정 스크립트 확장 기능을 사용하여 대상 VM에서 사용자 지정 스크립트를 실행할 수 있습니다. 이 기능을 사용하려면 다음 조건이 충족되어야 합니다.

* VM에 연결되어 있습니다.
* Azure Virtual Machine Agent is installed and is working as expected on the VM.
* The extension wasn't previously installed on the VM.
 
  The extension injects the script only the first time that it's used. If you use this feature later, the extension recognizes that it was already used and doesn't upload the new script.

Upload your script to a storage account, and generate its own container. 그런 다음, VM에 연결된 컴퓨터의 Azure PowerShell에서 다음 스크립트를 실행합니다.

### <a name="for-classic-deployment-model-vms"></a>For classic deployment model VMs

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

### <a name="for-azure-resource-manager-vms"></a>For Azure Resource Manager VMs

 

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
>For Azure Resource Manager VMs, you must open port 5986 on the network security group (NSG). 자세한 내용은 보안 그룹을 참조하세요. 
>
>RDFE VM의 경우 프라이빗 포트(5986) 및 공용 포트가 있는 엔드포인트가 필요합니다. Then, you also have to open that public-facing port on the NSG.

### <a name="set-up-the-client-computer"></a>클라이언트 컴퓨터 설정

PowerShell을 사용하여 VM에 원격으로 연결하려면 먼저 연결을 허용하도록 클라이언트 컴퓨터를 설정해야 합니다. 이렇게 하려면 다음 명령을 적절하게 실행하여 PowerShell 신뢰할 수 있는 호스트 목록에 VM을 추가합니다.

To add one VM to the trusted hosts list:

```powershell
Set-Item wsman:\localhost\Client\TrustedHosts -value <ComputerName>
```

To add multiple VMs to the trusted hosts list:

```powershell
Set-Item wsman:\localhost\Client\TrustedHosts -value <ComputerName1>,<ComputerName2>
```

신뢰할 수 있는 호스트 목록에 모든 컴퓨터를 추가하려면:

```powershell
Set-Item wsman:\localhost\Client\TrustedHosts -value *
```

### <a name="enable-remoteps-on-the-vm"></a>VM에서 RemotePS 사용

For VMs created using the classic deployment model, use the Custom Script Extension to run the following script:

```powershell
Enable-PSRemoting -Force
New-NetFirewallRule -Name "Allow WinRM HTTPS" -DisplayName "WinRM HTTPS" -Enabled True -Profile Any -Action Allow -Direction Inbound -LocalPort 5986 -Protocol TCP
$thumbprint = (New-SelfSignedCertificate -DnsName $env:COMPUTERNAME -CertStoreLocation Cert:\LocalMachine\My).Thumbprint
$command = "winrm create winrm/config/Listener?Address=*+Transport=HTTPS @{Hostname=""$env:computername""; CertificateThumbprint=""$thumbprint""}"
cmd.exe /C $command
```

For Azure Resource Manager VMs, use run commands from the portal to run the EnableRemotePS script:

![명령 실행](./media/remote-tools-troubleshoot-azure-vm-issues/run-command.png)

### <a name="connect-to-the-vm"></a>VM에 연결

Run the following command based on the client computer location:

* Outside the virtual network or deployment

  * For a VM created using the classic deployment model, run the following command:

    ```powershell
    $Skip = New-PSSessionOption -SkipCACheck -SkipCNCheck
    Enter-PSSession -ComputerName  "<<CLOUDSERVICENAME.cloudapp.net>>" -port "<<PUBLIC PORT NUMBER>>" -Credential (Get-Credential) -useSSL -SessionOption $Skip
    ```

  * For an Azure Resource Manager VM, first add a DNS name to the public IP address. 자세한 단계는 [Azure Portal에서 Windows VM에 대한 정규화된 도메인 이름 만들기](../windows/portal-create-fqdn.md)를 참조하세요. 다음 명령을 실행합니다.

    ```powershell
    $Skip = New-PSSessionOption -SkipCACheck -SkipCNCheck
    Enter-PSSession -ComputerName "<<DNSname.DataCenter.cloudapp.azure.com>>" -port "5986" -Credential (Get-Credential) -useSSL -SessionOption $Skip
    ```

* Inside the virtual network or deployment, run the following command:
  
  ```powershell
  $Skip = New-PSSessionOption -SkipCACheck -SkipCNCheck
  Enter-PSSession -ComputerName  "<<HOSTNAME>>" -port 5986 -Credential (Get-Credential) -useSSL -SessionOption $Skip
  ```

>[!NOTE] 
>SkipCaCheck 플래그를 설정하면 세션을 시작할 때 VM에 인증서를 가져오는 요구 사항이 무시됩니다.

You can also use the Invoke-Command cmdlet to run a script on the VM remotely.

```powershell
Invoke-Command -ComputerName "<<COMPUTERNAME>" -ScriptBlock {"<<SCRIPT BLOCK>>"}
```

## <a name="remote-registry"></a>원격 레지스트리

>[!NOTE]
>이 옵션을 사용하려면 TCP 포트 135 또는 445를 열어야 합니다.
>
>For Azure Resource Manager VMs, you have to open port 5986 on the NSG. 자세한 내용은 보안 그룹을 참조하세요. 
>
>RDFE VM의 경우 프라이빗 포트 5986 및 공용 포트가 있는 엔드포인트가 필요합니다. You also have to open that public-facing port on the NSG.

1. From another VM on the same virtual network, open the registry editor (regedit.exe).

2. **파일** > **네트워크 레지스트리 연결**을 선택합니다.

   ![Registry editor](./media/remote-tools-troubleshoot-azure-vm-issues/remote-registry.png) 

3. Locate the target VM by **host name** or **dynamic IP** (preferable) by entering it in the **Enter the object name to select** box.

   ![Enter the object name to select box](./media/remote-tools-troubleshoot-azure-vm-issues/input-computer-name.png) 
 
4. 대상 VM의 자격 증명을 입력합니다.

5. 레지스트리를 필요한 대로 변경합니다.

## <a name="remote-services-console"></a>Remote services console

>[!NOTE]
>이 옵션을 사용하려면 TCP 포트 135 또는 445를 열어야 합니다.
>
>For Azure Resource Manager VMs, you have to open port 5986 on the NSG. 자세한 내용은 보안 그룹을 참조하세요. 
>
>RDFE VM의 경우 프라이빗 포트 5986 및 공용 포트가 있는 엔드포인트가 필요합니다. You also have to open that public-facing port on the NSG.

1. From another VM on the same virtual network, open an instance of **Services.msc**.

2. **서비스(로컬)** 을 마우스 오른쪽 단추로 클릭합니다.

3. **다른 컴퓨터에 연결**을 선택합니다.

   ![원격 서비스](./media/remote-tools-troubleshoot-azure-vm-issues/remote-services.png)

4. Enter the dynamic IP of the target VM.

   ![Input dynamic IP](./media/remote-tools-troubleshoot-azure-vm-issues/input-ip-address.png)

5. 서비스를 필요한 대로 변경합니다.

## <a name="next-steps"></a>다음 단계

- For more information about the Enter-PSSession cmdlet, see [Enter-PSSession](https://technet.microsoft.com/library/hh849707.aspx).
- For more information about the Custom Script Extension for Windows using the classic deployment model, see [Custom Script Extension for Windows](../extensions/custom-script-classic.md).
- PsExec는 [PSTools 도구 모음](https://download.sysinternals.com/files/PSTools.zip)의 일부입니다.
- For more information about the PSTools Suite, see [PSTools](https://docs.microsoft.com/sysinternals/downloads/pstools).


