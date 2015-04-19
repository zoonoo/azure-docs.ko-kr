<properties
   pageTitle="manage-vms-azure-powershell"
   description="Azure PowerShell을 사용하여 VM 관리"
   services="virtual-machines"
   documentationCenter="windows"
   authors="singhkay"
   manager="timlt"
   editor=""/>

   <tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="02/20/2015"
   ms.author="kasing"/>

# Azure PowerShell을 사용하여 가상 컴퓨터 관리

여기서 설명하는 작업을 수행하기 전에 Azure PowerShell이 설치되어 있는지 확인해야 합니다. 이렇게 하려면 [Azure PowerShell을 설치 및 구성하는 방법](install-configure-powershell.md)을 참조하세요.

## 이미지 가져오기

VM을 만들려면 **사용할 이미지**를 결정해야 합니다. 다음 cmdlet을 사용하면 이미지 목록을 표시할 수 있습니다.

      Get-AzureVMImage

이 cmdlet은 Azure에서 사용 가능한 모든 이미지 목록을 반환합니다. 이 목록은 매우 길기 때문에 사용하려는 정확한 이미지를 찾기가 어려울 수 있습니다. 아래 예제에서는 다른 PowerShell cmdlet을 사용하여 **Windows Server 2012 R2 Datacenter**를 기준으로 관련 이미지만 포함하도록 반환되는 이미지 목록을 줄여 보겠습니다. 또한 반환되는 이미지 배열에 대해 [-1]을 지정하여 최신 이미지만 가져오도록 선택합니다.

    $img = (Get-AzureVMImage | Select -Property ImageName, Label | where {$_.Label -like '*Windows Server 2012 R2 Datacenter*'})[-1]

## VM 만들기

VM을 만들 때는 먼저 **New-AzureVMConfig** cmdlet을 실행합니다. 여기서는 VM의 **name**, **size** 및 VM에 사용할 **image**를 지정합니다. 이 cmdlet은 로컬 VM 개체 **$myVM**을 만듭니다. 이 가이드의 뒷부분에서 다른 Azure PowerShell cmdlet을 사용하여 이 개체를 수정할 것입니다.

      $myVM = New-AzureVMConfig -Name "testvm" -InstanceSize "Small" -ImageName $img.ImageName

다음으로는 VM의 **username** 및 **password**를 선택해야 합니다. 이 작업에는 **Add-AzureProvisioningConfig** cmdlet을 사용할 수 있습니다. 이 cmdlet을 통해 VM의 OS를 지정합니다. 이 시점까지는 로컬 **$myVM** 개체를 변경하게 됩니다.

    $user = "azureuser"
    $pass = "&Azure1^Pass@"
    $myVM = Add-AzureProvisioningConfig -Windows -AdminUsername $user -Password $pass

마지막으로 Azure에 VM을 업로드합니다. 이렇게 하려면 **New-AzureVM** cmdlet을 사용해야 합니다.

> [AZURE.NOTE] VM을 만들기 전에 클라우드 서비스를 구성해야 합니다. 두 가지 방법으로 이 작업을 수행할 수 있습니다.
* New-AzureService cmdlet을 사용하여 클라우드 서비스를 만듭니다. 이 방법을 선택하는 경우에는 아래의 New-AzureVM cmdlet에서 지정한 위치가 클라우드 서비스 위치와 일치하는지 확인해야 합니다. 이 두 위치가 일치하지 않으면 New-AzureVM cmdlet 실행이 실패합니다.
* New-AzureVM cmdlet에서 구성을 자동으로 수행하도록 합니다. 이 경우 서비스 이름이 고유해야 합니다. 그렇지 않으면 New-AzureVM cmdlet 실행이 실패합니다.

    New-AzureVM -ServiceName "mytestserv" -VMs $myVM -Location "West US"

**선택 사항**

**Add-AzureDataDisk**, **Add-AzureEndpoint** 등의 다른 cmdlet을 사용하여 VM의 추가 옵션을 구성할 수 있습니다.

## VM 확인
이제 Azure에서 VM을 만들었으므로 해당 VM이 정상적으로 작동하는지 확인할 수 있습니다. 작동을 확인하려면 아래에 나와 있는 것처럼 **Get-AzureVM** cmdlet을 사용할 수 있습니다.

    Get-AzureVM -ServiceName "mytestserv" -Name "testvm"


## 다음 단계
[RDP 또는 SSH를 사용하여 Azure 가상 컴퓨터에 연결](https://msdn.microsoft.com/library/azure/dn535788.aspx)<br>
[Azure 가상 컴퓨터 FAQ](https://msdn.microsoft.com/library/azure/dn683781.aspx)

<!--HONumber=47-->
