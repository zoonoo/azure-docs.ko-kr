<properties
   pageTitle="VM에 태그를 지정하는 방법 | Microsoft Azure"
   description="리소스 관리자 배포 모델을 사용하여 Azure에서 만든 Windows 가상 컴퓨터에 태그를 지정하는 방법을 알아봅니다."
   services="virtual-machines-windows"
   documentationCenter=""
   authors="mmccrory"
   manager="timlt"
   editor="tysonn"
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="04/06/2016"
   ms.author="iainfou;memccror"/>

# Azure에서 Windows 가상 컴퓨터에 태그를 지정하는 방법


이 문서에서는 리소스 관리자 배포 모델을 통해 Azure의 Windows 가상 컴퓨터에 태그를 지정하는 다양한 방법에 대해 설명합니다. 태그는 리소스 또는 리소스 그룹에 직접 배치할 수 있는 사용자 정의 키/값 쌍입니다. Azure는 현재 리소스 및 리소스 그룹당 최대 15개의 태그를 지원합니다. 태그를 만들 때 리소스에 배치하거나 기존 리소스에 추가할 수 있습니다. 태그는 리소스 관리자 배포 모델을 통해 만든 리소스에 대해서만 지원됩니다. Linux 가상 컴퓨터에 태그를 지정하려는 경우 [Azure에서 Linux 가상 컴퓨터에 태그를 지정하는 방법](virtual-machines-linux-tag.md)을 참조하세요.

[AZURE.INCLUDE [virtual-machines-common-tag](../../includes/virtual-machines-common-tag.md)]

## PowerShell을 사용한 태그 지정

PowerShell을 통해 태그를 만들고 추가 및 삭제하려면 먼저 [Azure Resource Manager를 사용하여 PowerShell 환경][]을 설정해야 합니다. 설정을 완료한 후 계산, 네트워크 및 저장소 리소스를 만들 때 또는 PowerShell을 통해 리소스를 만든 후에 태그를 배치할 수 있습니다. 이 문서에서는 가상 컴퓨터에 배치된 태그 보기/편집을 중점적으로 살펴봅니다.

먼저 `Get-AzureRmVM` cmdlet을 통해 가상 컴퓨터로 이동합니다.

        PS C:\> Get-AzureRmVM -ResourceGroupName "MyResourceGroup" -Name "MyTestVM"

가상 컴퓨터에 이미 태그가 포함되어 있는 경우 해당 리소스의 모든 태그가 표시됩니다.

        Tags : {
                "Application": "MyApp1",
                "Created By": "MyName",
                "Department": "MyDepartment",
                "Environment": "Production"
               }

PowerShell을 통해 태그를 추가하려는 경우 `Set-AzureRmResource` 명령을 사용할 수 있습니다. PowerShell을 통해 태그를 업데이트하는 경우 태그가 전체적으로 업데이트됩니다. 따라서 이미 태그가 있는 리소스에 하나의 태그를 추가하는 경우 리소스에 배치하려는 모든 태그를 포함해야 합니다. 다음은 PowerShell Cmdlet을 통해 리소스에 태그를 더 추가하는 방법의 예입니다.

첫 번째 cmdlet은 `Get-AzureRmResource` 및 `Tags` 함수를 사용하여 *MyTestVM*에 배치된 모든 태그를 *tags* 변수에 설정합니다.

        PS C:\> $tags = (Get-AzureRmResource -ResourceGroupName MyResourceGroup -Name MyTestVM).Tags

두 번째 명령은 지정된 변수에 대한 태그를 표시합니다.

        PS C:\> $tags

        Name		Value
        ----                           -----
        Value		MyDepartment
        Name		Department
        Value		MyApp1
        Name		Application
        Value		MyName
        Name		Created By
        Value		Production
        Name		Environment

세 번째 명령은 *tags* 변수에 태그를 더 추가합니다. **+=**을 사용하여 *tags* 목록에 새로운 키/값 쌍을 추가합니다.

        PS C:\> $tags +=@{Name="Location";Value="MyLocation"}

네 번째 명령은 *tags* 변수에 정의된 모든 태그를 지정된 리소스에 설정합니다. 이 경우 MyTestVM입니다.

        PS C:\> Set-AzureRmResource -ResourceGroupName MyResourceGroup -Name MyTestVM -ResourceType "Microsoft.Compute/VirtualMachines" -Tag $tags

다섯 번째 명령은 리소스의 모든 태그를 표시합니다. 여기서 볼 수 있듯이 *위치*가 이제 값이 *MyLocation*인 태그로 정의되었습니다.

        PS C:\> (Get-AzureRmResource -ResourceGroupName MyResourceGroup -Name MyTestVM).Tags

        Name		Value
        ----                           -----
        Value		MyDepartment
        Name		Department
        Value		MyApp1
        Name		Application
        Value		MyName
        Name		Created By
        Value		Production
        Name		Environment
        Value		MyLocation
        Name		Location

PowerShell을 통해 태그를 지정하는 방법에 대한 자세한 내용은 [Azure 리소스 Cmdlet][]를 참조하세요.

[AZURE.INCLUDE [virtual-machines-common-tag-usage](../../includes/virtual-machines-common-tag-usage.md)]

## 다음 단계

* Azure 리소스에 태그를 지정하는 방법에 대한 자세한 내용은 [Azure Resource Manager 개요][] 및 [태그를 사용하여 Azure 리소스 구성][]을 참조하세요.
* 태그로 Azure 리소스의 사용을 관리하는 방법은 [Azure 청구서 이해][] 및 [Microsoft Azure 리소스 소비에 대한 정보 얻기][]를 참조하세요.

[Azure Resource Manager를 사용하여 PowerShell 환경]: ../powershell-azure-resource-manager.md
[Azure 리소스 Cmdlet]: https://msdn.microsoft.com/library/azure/dn757692.aspx
[Azure Resource Manager 개요]: ../resource-group-overview.md
[태그를 사용하여 Azure 리소스 구성]: ../resource-group-using-tags.md
[Azure 청구서 이해]: ../billing-understand-your-bill.md
[Microsoft Azure 리소스 소비에 대한 정보 얻기]: ../billing-usage-rate-card-overview.md

<!---HONumber=AcomDC_0518_2016-->