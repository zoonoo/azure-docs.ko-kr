---
title: "Azure PowerShell(리소스 관리자)에서 SQL Server 가상 컴퓨터 만들기 | Microsoft Docs"
description: "SQL Server 가상 컴퓨터 갤러리 이미지를 사용하여 Azure VM을 만드는 단계 및 PowerShell 스크립트를 제공합니다."
services: virtual-machines-windows
documentationcenter: na
author: rothja
manager: jhubbard
editor: 
tags: azure-resource-manager
ms.assetid: 98d50dd8-48ad-444f-9031-5378d8270d7b
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/17/2017
ms.author: jroth
translationtype: Human Translation
ms.sourcegitcommit: 7402249aa87ffe985ae13f28a701e22af3afd450
ms.openlocfilehash: 7706fd453ef8be4ebe65b65da643ec38b7c18433


---
# <a name="provision-a-sql-server-virtual-machine-using-azure-powershell-resource-manager"></a>Azure PowerShell(리소스 관리자)을 사용하여 SQL Server 가상 컴퓨터 프로비전
> [!div class="op_single_selector"]
> * [포털](virtual-machines-windows-portal-sql-server-provision.md)
> * [PowerShell](virtual-machines-windows-ps-sql-create.md)
>
>

## <a name="overview"></a>개요
이 자습서에서는 Azure PowerShell cmdlet을 사용하여 **Azure Resource Manager** 배포 모델을 사용하는 단일 Azure 가상 컴퓨터를 만드는 방법을 보여 줍니다. 이 자습서에서는 SQL 갤러리의 이미지에서 단일 디스크 드라이브를 사용하여 단일 가상 컴퓨터를 만듭니다. 가상 컴퓨터에서 사용할 저장소, 네트워크 및 계산 리소스에 대한 새 공급자를 만듭니다. 이러한 리소스에 대한 기존 공급자가 있는 경우 대신 이러한 공급자를 사용할 수 있습니다.

이 항목의 클래식 버전이 필요한 경우 [Azure PowerShell 클래식을 사용하여 SQL Server 가상 컴퓨터 프로비전](../sqlclassic/virtual-machines-windows-classic-ps-sql-create.md)을 참조하세요.

## <a name="prerequisites"></a>필수 조건
이 자습서에는 다음이 필요합니다.

* 시작하기 전에 Azure 계정 및 구독. 없는 경우 지금 [무료 평가판](https://azure.microsoft.com/pricing/free-trial/)에 등록하세요.
* [Azure PowerShell](/powershell/azureps-cmdlets-docs), 최소 버전 1.4.0 이상(이 자습서는 1.5.0 버전을 사용하여 작성)
  * 버전을 검색하려면 **Get-Module Azure -ListAvailable**을 입력합니다.

## <a name="configure-your-subscription"></a>구독 구성
Windows PowerShell을 열고 다음 cmdlet을 실행하여 Azure 계정에 대한 액세스를 설정합니다. 자격 증명을 입력할 수 있는 로그인 화면이 나타납니다. Azure 포털에 로그인할 때 사용한 것과 동일한 메일과 암호를 사용합니다.

    Add-AzureRmAccount

로그인에 성공하면 로그인에 사용한 SubscriptionId를 포함하는 화면에 일부 정보가 표시됩니다. 이는 다른 구독으로 변경하지 않는 경우 이 자습서에 대한 리소스가 만들어지는 구독입니다. 여러 SubscriptionId가 있는 경우 다음 cmdlet을 실행하여 모든 SubscriptionId 목록을 반환합니다.

    Get-AzureRmSubscription

다른 SubscriptionID로 변경하려면 원하는 SubscriptionId를 사용하여 다음 cmdlet을 실행합니다.

    Select-AzureRmSubscription -SubscriptionId xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx

## <a name="define-image-variables"></a>이미지 변수 정의
이 자습서에서 완료된 스크립트를 간단히 이해하고 사용하기 위해 여러 가지 변수를 정의하여 시작합니다. 적절하다고 판단되면 매개 변수 값을 변경하지만, 제공된 값을 수정할 때 이름 길이 및 특수 문자와 관련된 명명 제한 사항에 주의하세요.

### <a name="location-and-resource-group"></a>위치 및 리소스 그룹
두 변수를 사용하여 가상 컴퓨터에 대한 다른 리소스를 만들 리소스 그룹 및 데이터 영역을 정의합니다.

원하는 대로 수정하고 다음 cmdlet을 실행하여 이러한 변수를 초기화합니다.

    $Location = "SouthCentralUS"
    $ResourceGroupName = "sqlvm1"

### <a name="storage-properties"></a>저장소 속성
다음 변수를 사용하여 가상 컴퓨터에서 사용할 저장소 계정 및 저장소 유형을 정의합니다.

원하는 대로 수정하고 다음 cmdlet을 실행하여 이러한 변수를 초기화합니다. 이 예제에서는 프로덕션 워크로드에 권장되는 [프리미엄 저장소](../../../storage/storage-premium-storage.md)를 사용합니다. 이 참고 자료 및 기타 권장 사항에 대한 세부 정보는 [Azure 가상 컴퓨터의 SQL Server에 대한 성능 모범 사례](virtual-machines-windows-sql-performance.md)를 참조하세요.

    $StorageName = $ResourceGroupName + "storage"
    $StorageSku = "Premium_LRS"

### <a name="network-properties"></a>네트워크 속성
다음 변수를 사용하여 가상 컴퓨터의 네트워크에서 사용할 네트워크 인터페이스, TCP/IP 할당 방법, 가상 네트워크 이름, 가상 서브넷 이름, 가상 네트워크용 IP 주소 범위, 서브넷용 IP 주소 범위 및 공용 도메인 이름 레이블을 정의합니다.

원하는 대로 수정하고 다음 cmdlet을 실행하여 이러한 변수를 초기화합니다.

    $InterfaceName = $ResourceGroupName + "ServerInterface"
    $TCPIPAllocationMethod = "Dynamic"
    $VNetName = $ResourceGroupName + "VNet"
    $SubnetName = "Default"
    $VNetAddressPrefix = "10.0.0.0/16"
    $VNetSubnetAddressPrefix = "10.0.0.0/24"
    $DomainName = "sqlvm1"

### <a name="virtual-machine-properties"></a>가상 컴퓨터 속성
다음 변수를 사용하여 가상 컴퓨터에 대한 가상 컴퓨터 이름, 컴퓨터 이름, 가상 컴퓨터 크기 및 운영 체제 디스크 이름을 정의합니다.

원하는 대로 수정하고 다음 cmdlet을 실행하여 이러한 변수를 초기화합니다.

    $VMName = $ResourceGroupName + "VM"
    $ComputerName = $ResourceGroupName + "Server"
    $VMSize = "Standard_DS13"
    $OSDiskName = $VMName + "OSDisk"

### <a name="image-properties"></a>이미지 속성
다음 변수를 사용하여 가상 컴퓨터에 사용할 이미지를 정의합니다. 이 예제에서는 SQL Server 2016 Enterprise 이미지를 사용합니다.

원하는 대로 수정하고 다음 cmdlet을 실행하여 이러한 변수를 초기화합니다.

    $PublisherName = "MicrosoftSQLServer"
    $OfferName = "SQL2016-WS2016"
    $Sku = "Enterprise"
    $Version = "latest"

Get-AzureRmVMImageOffer 명령을 사용하여 SQL Server 이미지 제품의 전체 목록을 가져올 수 있습니다.

    Get-AzureRmVMImageOffer -Location 'East US' -Publisher 'MicrosoftSQLServer'

Get-AzureRmVMImageSku 명령을 사용하여 제품에 사용 가능한 Sku를 확인할 수 있습니다. 다음 명령을 사용하여 **SQL2014SP1 WS2012R2** 제품에 사용 가능한 모든 Sku를 확인할 수 있습니다.

    Get-AzureRmVMImageSku -Location 'East US' -Publisher 'MicrosoftSQLServer' -Offer 'SQL2014SP1-WS2012R2' | Select Skus

## <a name="create-a-resource-group"></a>리소스 그룹 만들기
리소스 관리자 배포 모델을 사용하여 만드는 첫 번째 개체는 리소스 그룹입니다. [New-AzureRmResourceGroup](https://msdn.microsoft.com/library/mt759837.aspx) cmdlet을 사용하여 이전에 초기화한 변수로 정의된 리소스 그룹 이름 및 위치로 Azure 리소스 그룹 및 해당 리소스를 만듭니다.

다음 cmdlet을 실행하여 새 리소스 그룹을 만듭니다.

    New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Location

## <a name="create-a-storage-account"></a>저장소 계정 만들기
가상 컴퓨터에 운영 체제 디스크와 SQL Server 데이터 및 로그 파일에 대한 저장소 리소스가 필요합니다. 간단히 하기 위해 둘 다에 대한 단일 디스크를 만듭니다. SQL Server 데이터와 로그 파일을 전용 디스크에 배치하기 위해 [Add-Azure Disk](https://msdn.microsoft.com/library/azure/dn495252.aspx) cmdlet을 사용하여 나중에 추가 디스크를 연결할 수 있습니다. [New-AzureRmStorageAccount](https://msdn.microsoft.com/library/mt607148.aspx) cmdlet을 사용하여 이전에 초기화한 변수로 정의된 저장소 계정 이름, 저장소 SKU 이름 및 위치로 새 리소스 그룹에서 표준 저장소 계정을 만듭니다.

다음 cmdlet을 실행하여 새 저장소 계정을 만듭니다.

    $StorageAccount = New-AzureRmStorageAccount -ResourceGroupName $ResourceGroupName -Name $StorageName -SkuName $StorageSku -Kind "Storage" -Location $Location

## <a name="create-network-resources"></a>네트워크 리소스 만들기
가상 컴퓨터에 네트워크 연결을 위해 여러 네트워크 리소스가 필요합니다.

* 각 가상 컴퓨터에 가상 네트워크가 필요합니다.
* 가상 네트워크에는 하나 이상의 서브넷이 정의되어 있어야 합니다.
* 네트워크 인터페이스가 공용 또는 개인 IP 주소 중 하나로 정의되어야 합니다.

### <a name="create-a-virtual-network-subnet-configuration"></a>가상 네트워크 서브넷 구성 만들기
가상 네트워크에 대한 서브넷 구성을 만들어 시작합니다. 이 자습서의 경우 [New-AzureRmVirtualNetworkSubnetConfig](https://msdn.microsoft.com/library/mt619412.aspx) cmdlet을 사용하여 기본 서브넷을 만듭니다. 이전에 초기화한 변수를 사용하여 정의된 서브넷 이름 및 주소 접두사로 가상 네트워크 서브넷 구성을 만듭니다.

> [!NOTE]
> 이 cmdlet을 사용하여 가상 네트워크 서브넷 구성의 추가 속성을 정의할 수 있지만, 이 내용은 이 자습서에서 다루지 않습니다.
>
>

다음 cmdlet을 실행하여 가상 서브넷 구성을 만듭니다.

    $SubnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix $VNetSubnetAddressPrefix

### <a name="create-a-virtual-network"></a>가상 네트워크 만들기
다음으로 [New-AzureRmVirtualNetwork](https://msdn.microsoft.com/library/mt603657.aspx) cmdlet을 사용하여 가상 네트워크를 만듭니다. 이전 단계에서 정의된 서브넷 구성을 사용하고 이전에 초기화한 변수를 사용하여 정의된 이름, 위치 및 주소 접두사로 새 리소스 그룹에서 가상 네트워크를 만듭니다.

다음 cmdlet을 실행하여 가상 네트워크를 만듭니다.

    $VNet = New-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName -Location $Location -AddressPrefix $VNetAddressPrefix -Subnet $SubnetConfig

### <a name="create-the-public-ip-address"></a>공용 IP 주소 만들기
이제 가상 네트워크를 정의했으므로 가상 컴퓨터에 연결하려면 IP 주소를 구성해야 합니다. 이 자습서의 경우 인터넷 연결을 지원하도록 동적 IP 주소 지정을 사용하여 공용 IP 주소를 만듭니다. [New-AzureRmPublicIpAddress](https://msdn.microsoft.com/library/mt603620.aspx) cmdlet을 사용하여 이전에 초기화한 변수로 정의된 이름, 위치, 할당 방법 및 DNS 도메인 이름 레이블로 이전에 만든 리소스 그룹에서 공용 IP 주소를 만듭니다.

> [!NOTE]
> 이 cmdlet을 사용하여 공용 IP 주소의 추가 속성을 정의할 수 있지만, 이 내용은 이 초기 자습서에서 다루지 않습니다. 또한 고정 주소로 개인 주소 또는 주소도 만들 수 있지만, 이 내용도 이 자습서에서 다루지 않습니다.
>
>

다음 cmdlet을 실행하여 공용 IP 주소를 만듭니다.

    $PublicIp = New-AzureRmPublicIpAddress -Name $InterfaceName -ResourceGroupName $ResourceGroupName -Location $Location -AllocationMethod $TCPIPAllocationMethod -DomainNameLabel $DomainName

### <a name="create-the-network-interface"></a>네트워크 인터페이스 만들기
이제 가상 컴퓨터에서 사용할 네트워크 인터페이스를 만들 수 있습니다. [New-AzureRmNetworkInterface](https://msdn.microsoft.com/library/mt619370.aspx) cmdlet을 사용하여 이전에 정의된 이름, 위치, 서브넷 및 공용 IP 주소로 이전에 만든 리소스 그룹에서 네트워크 인터페이스를 만듭니다.

다음 cmdlet을 실행하여 네트워크 인터페이스를 만듭니다.

    $Interface = New-AzureRmNetworkInterface -Name $InterfaceName -ResourceGroupName $ResourceGroupName -Location $Location -SubnetId $VNet.Subnets[0].Id -PublicIpAddressId $PublicIp.Id

## <a name="configure-a-vm-object"></a>VM 개체 구성
이제 저장소 및 네트워크 리소스를 정의했으므로 가상 컴퓨터에 대한 계산 리소스를 정의할 수 있습니다. 이 자습서의 경우, 가상 컴퓨터 크기 및 다양한 운영 체제 속성을 지정하고, 이전에 만든 네트워크 인터페이스를 지정하고, Blob 저장소를 정의한 다음 운영 체제 디스크를 지정합니다.

### <a name="create-the-vm-object"></a>VM 개체 만들기
가상 컴퓨터 크기를 지정하여 시작합니다. 이 자습서의 경우 DS13을 지정합니다. [New-AzureRmVMConfig](https://msdn.microsoft.com/library/mt603727.aspx) cmdlet을 사용하여 이전에 초기화한 변수로 정의된 이름 및 크기로 구성 가능한 가상 컴퓨터 개체를 만듭니다.

다음 cmdlet을 실행하여 가상 컴퓨터 개체를 만듭니다.

    $VirtualMachine = New-AzureRmVMConfig -VMName $VMName -VMSize $VMSize

### <a name="create-a-credential-object-to-hold-the-name-and-password-for-the-local-administrator-credentials"></a>로컬 관리자 자격 증명에 대한 이름 및 암호를 저장하도록 자격 증명 개체 만들기
가상 컴퓨터에 대한 운영 체제 속성을 설정하려면 먼저 보안 문자열로 로컬 관리자 계정의 자격 증명을 제공해야 합니다. 이렇게 하려면 [Get-Credential](https://technet.microsoft.com/library/hh849815.aspx) cmdlet을 사용합니다.

다음 cmdlet을 실행하고 Windows PowerShell 자격 증명 요청 창에서 Windows 가상 컴퓨터의 로컬 관리자 계정에 사용할 이름 및 암호를 입력합니다.

    $Credential = Get-Credential -Message "Type the name and password of the local administrator account."

### <a name="set-the-operating-system-properties-for-the-virtual-machine"></a>가상 컴퓨터에 대한 운영 체제 속성 설정
이제 가상 컴퓨터의 운영 체제 속성을 설정할 수 있습니다. [Set-AzureRmVMOperatingSystem](https://msdn.microsoft.com/library/mt603843.aspx) cmdlet을 사용하여 운영 체제 유형을 Windows로 설정하고, [가상 컴퓨터 에이전트](../../virtual-machines-windows-classic-agents-and-extensions.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)를 설치하도록 요구하고, cmdlet이 자동 업데이트를 사용하도록 지정하고, 이전에 초기화한 변수로 가상 컴퓨터 이름, 컴퓨터 이름 및 자격 증명을 설정합니다.

다음 cmdlet을 실행하여 가상 컴퓨터에 대한 운영 체제 속성을 설정합니다.

    $VirtualMachine = Set-AzureRmVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName $ComputerName -Credential $Credential -ProvisionVMAgent -EnableAutoUpdate

### <a name="add-the-network-interface-to-the-virtual-machine"></a>가상 컴퓨터에 네트워크 인터페이스를 추가합니다.
다음으로 가상 컴퓨터에 이전에 만든 네트워크 인터페이스를 추가합니다. [Add-AzureRmVMNetworkInterface](https://msdn.microsoft.com/library/mt619351.aspx) cmdlet을 사용하여 이전에 정의한 네트워크 인터페이스 변수를 사용하는 네트워크 인터페이스를 추가합니다.

다음 cmdlet을 실행하여 가상 컴퓨터에 대한 네트워크 인터페이스를 설정합니다.

    $VirtualMachine = Add-AzureRmVMNetworkInterface -VM $VirtualMachine -Id $Interface.Id

### <a name="set-the-blob-storage-location-for-the-disk-to-be-used-by-the-virtual-machine"></a>가상 컴퓨터에서 사용할 디스크에 대한 Blob 저장소 위치를 설정합니다.
다음으로 이전에 정의한 변수를 사용하여 가상 컴퓨터에서 사용할 디스크에 대한 Blob 저장소 위치를 설정합니다.

다음 cmdlet을 실행하여 Blob 저장소 위치를 설정합니다.

    $OSDiskUri = $StorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $OSDiskName + ".vhd"

### <a name="set-the-operating-system-disk-properties-for-the-virtual-machine"></a>가상 컴퓨터에 대한 운영 체제 디스크 속성을 설정합니다.
다음으로 가상 컴퓨터에 대한 운영 체제 디스크 속성을 설정합니다. [Set-AzureRmVMOSDisk](https://msdn.microsoft.com/library/mt603746.aspx) cmdlet을 사용하여 가상 컴퓨터의 운영 체제를 이미지에서 구성하도록 지정하고 캐싱을 SQL Server가 같은 디스크에 설치되는 중이므로 읽기 전용으로 설정하며 이전에 정의한 변수로 정의된 가상 컴퓨터 이름 및 운영 체제 디스크를 정의합니다.

다음 cmdlet을 실행하여 가상 컴퓨터에 대한 운영 체제 디스크 속성을 설정합니다.

    $VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -Name $OSDiskName -VhdUri $OSDiskUri -Caching ReadOnly -CreateOption FromImage

### <a name="specify-the-platform-image-for-the-virtual-machine"></a>가상 컴퓨터에 대한 플랫폼 이미지 지정
마지막 구성 단계는 가상 컴퓨터에 대한 플랫폼 이미지를 지정하는 것입니다. 이 자습서의 경우 최신 SQL Server 2016 CTP 이미지를 사용합니다. [Set-AzureRmVMSourceImage](https://msdn.microsoft.com/library/mt619344.aspx) cmdlet을 사용하여 이전에 정의한 변수에서 정의된 대로 이 이미지를 사용합니다.

다음 cmdlet을 실행하여 가상 컴퓨터에 대한 플랫폼 이미지를 지정합니다.

    $VirtualMachine = Set-AzureRmVMSourceImage -VM $VirtualMachine -PublisherName $PublisherName -Offer $OfferName -Skus $Sku -Version $Version

## <a name="create-the-sql-vm"></a>SQL VM 만들기
이제 구성 단계를 완료했으므로 가상 컴퓨터를 만들 수 있습니다. [New-AzureRmVM](https://msdn.microsoft.com/library/mt603754.aspx) cmdlet을 사용하여 정의한 변수로 가상 컴퓨터를 만듭니다.

다음 cmdlet을 실행하여 가상 컴퓨터를 만듭니다.

    New-AzureRmVM -ResourceGroupName $ResourceGroupName -Location $Location -VM $VirtualMachine

가상 컴퓨터가 만들어집니다. 가상 컴퓨터의 디스크에 대해 지정된 저장소 계정은 프리미엄 저장소 계정이므로 표준 저장소 계정이 부팅 진단에 대해 만들어집니다.

이제 Azure 포털에서 이 컴퓨터를 보고 [해당 공용 IP 주소 및 정규화된 도메인 이름](virtual-machines-windows-portal-sql-server-provision.md)을 확인할 수 있습니다.

## <a name="example-script"></a>예제 스크립트
다음 스크립트에는 이 자습서에 대한 전체 PowerShell 스크립트가 포함됩니다. **Add-AzureRmAccount** 및 **Select-AzureRmSubscription** 명령을 사용하도록 이미 Azure 구독을 설정한 것으로 가정합니다.

    # Variables
    ## Global
    $Location = "SouthCentralUS"
    $ResourceGroupName = "sqlvm1"
    ## Storage
    $StorageName = $ResourceGroupName + "storage"
    $StorageSku = "Premium_LRS"

    ## Network
    $InterfaceName = $ResourceGroupName + "ServerInterface"
    $VNetName = $ResourceGroupName + "VNet"
    $SubnetName = "Default"
    $VNetAddressPrefix = "10.0.0.0/16"
    $VNetSubnetAddressPrefix = "10.0.0.0/24"
    $TCPIPAllocationMethod = "Dynamic"
    $DomainName = "sqlvm1"

    ##Compute
    $VMName = $ResourceGroupName + "VM"
    $ComputerName = $ResourceGroupName + "Server"
    $VMSize = "Standard_DS13"
    $OSDiskName = $VMName + "OSDisk"

    ##Image
    $PublisherName = "MicrosoftSQLServer"
    $OfferName = "SQL2016-WS2016"
    $Sku = "Enterprise"
    $Version = "latest"

    # Resource Group
    New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Location

    # Storage
    $StorageAccount = New-AzureRmStorageAccount -ResourceGroupName $ResourceGroupName -Name $StorageName -SkuName $StorageSku -Kind "Storage" -Location $Location

    # Network
    $SubnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix $VNetSubnetAddressPrefix
    $VNet = New-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName -Location $Location -AddressPrefix $VNetAddressPrefix -Subnet $SubnetConfig
    $PublicIp = New-AzureRmPublicIpAddress -Name $InterfaceName -ResourceGroupName $ResourceGroupName -Location $Location -AllocationMethod $TCPIPAllocationMethod -DomainNameLabel $DomainName
    $Interface = New-AzureRmNetworkInterface -Name $InterfaceName -ResourceGroupName $ResourceGroupName -Location $Location -SubnetId $VNet.Subnets[0].Id -PublicIpAddressId $PublicIp.Id

    # Compute
    $VirtualMachine = New-AzureRmVMConfig -VMName $VMName -VMSize $VMSize
    $Credential = Get-Credential -Message "Type the name and password of the local administrator account."
    $VirtualMachine = Set-AzureRmVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName $ComputerName -Credential $Credential -ProvisionVMAgent -EnableAutoUpdate #-TimeZone = $TimeZone
    $VirtualMachine = Add-AzureRmVMNetworkInterface -VM $VirtualMachine -Id $Interface.Id
    $OSDiskUri = $StorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $OSDiskName + ".vhd"
    $VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -Name $OSDiskName -VhdUri $OSDiskUri -Caching ReadOnly -CreateOption FromImage

    # Image
    $VirtualMachine = Set-AzureRmVMSourceImage -VM $VirtualMachine -PublisherName $PublisherName -Offer $OfferName -Skus $Sku -Version $Version

    ## Create the VM in Azure
    New-AzureRmVM -ResourceGroupName $ResourceGroupName -Location $Location -VM $VirtualMachine

## <a name="next-steps"></a>다음 단계
가상 컴퓨터가 만들어지면 RDP를 사용하여 가상 컴퓨터에 연결하고 연결을 설정할 수 있습니다. 자세한 내용은 [Azure(리소스 관리자)에서 SQL Server 가상 컴퓨터에 연결](virtual-machines-windows-sql-connect.md)을 참조하세요.




<!--HONumber=Jan17_HO2-->


