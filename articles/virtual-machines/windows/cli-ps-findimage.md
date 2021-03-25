---
title: PowerShell을 사용 하 여 marketplace 구매 계획 정보 찾기 및 사용
description: Azure PowerShell를 사용 하 여 Marketplace VM 이미지의 게시자, 제품, SKU 및 버전과 같은 이미지 Urn 및 구매 계획 매개 변수를 찾을 수 있습니다.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 03/17/2021
ms.author: cynthn
ms.custom: contperf-fy21q3
ms.openlocfilehash: 34fd6720b93a1462836b51856d73573a86809367
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/24/2021
ms.locfileid: "105022826"
---
# <a name="find-and-use-azure-marketplace-vm-images-with-azure-powershell"></a>Azure PowerShell를 사용 하 여 Azure Marketplace VM 이미지 찾기 및 사용

이 문서에서는 Azure PowerShell을 사용하여 Azure Marketplace에서 VM 이미지를 찾는 방법을 설명합니다. 그런 다음 VM을 만들 때 마켓플레이스 이미지 및 계획 정보를 지정할 수 있습니다.

[Azure Marketplace](https://azuremarketplace.microsoft.com/) 또는 [Azure CLI](../linux/cli-ps-findimage.md)를 사용 하 여 사용 가능한 이미지 및 제품을 찾아볼 수도 있습니다. 

## <a name="terminology"></a>용어

Azure의 Marketplace 이미지에는 다음과 같은 특성이 있습니다.

* **Publisher**: 이미지를 만든 조직입니다. 예: Canonical, MicrosoftWindowsServer
* **Offer**: 게시자가 만든 관련 이미지 그룹의 이름입니다. 예: UbuntuServer, WindowsServer
* **SKU**: 제공의 인스턴스(예: 배포의 주 릴리스)입니다. 예: 18.04-LTS, 2019-Datacenter
* **Version**: 이미지 SKU의 버전 번호입니다. 

이러한 값은 콜론으로 구분 된 값을 결합 하 여 개별적으로 또는 이미지 *URN* 으로 전달 될 수 있습니다 (:). 예: *Publisher*:*제품*:*Sku*:*버전*. 에서 URN의 버전 번호를로 바꿔 `latest` 최신 버전의 이미지를 사용할 수 있습니다. 

이미지 게시자가 추가 라이선스 및 구매 조건을 제공 하는 경우 이미지를 사용 하려면 먼저 해당 항목에 동의 해야 합니다.  자세한 내용은 [구매 계획 조건 동의](#accept-purchase-plan-terms)를 참조 하세요.

## <a name="list-images"></a>이미지 나열

PowerShell을 사용 하 여 이미지 목록의 범위를 좁힐 수 있습니다. 변수의 값을 요구 사항에 맞게 바꿉니다.

1. [AzVMImagePublisher](/powershell/module/az.compute/get-azvmimagepublisher)를 사용 하 여 이미지 게시자를 나열 합니다.
    
    ```powershell
    $locName="<location>"
    Get-AzVMImagePublisher -Location $locName | Select PublisherName
    ```
1. 지정 된 게시자에 대해 [AzVMImageOffer](/powershell/module/az.compute/get-azvmimageoffer)를 사용 하 여 해당 제품을 나열 합니다.
    
    ```powershell
    $pubName="<publisher>"
    Get-AzVMImageOffer -Location $locName -PublisherName $pubName | Select Offer
    ```
1. 지정 된 게시자 및 제품에 대해 [AzVMImageSku](/powershell/module/az.compute/get-azvmimagesku)를 사용 하 여 사용할 수 있는 sku를 나열 합니다.
    
    ```powershell
    $offerName="<offer>"
    Get-AzVMImageSku -Location $locName -PublisherName $pubName -Offer $offerName | Select Skus
    ```
1. SKU의 경우 [AzVMImage](/powershell/module/az.compute/get-azvmimage)를 사용 하 여 이미지 버전을 나열 합니다.

    ```powershell
    $skuName="<SKU>"
    Get-AzVMImage -Location $locName -PublisherName $pubName -Offer $offerName -Sku $skuName | Select Version
    ```
    `latest`특정 이전 버전이 아닌 최신 이미지를 사용 하려는 경우에도를 사용할 수 있습니다.


이제 선택한 게시자, 제품, SKU 및 버전을 URN으로(값을 :으로 구분하여) 결합할 수 있습니다. [New-AzVM](/powershell/module/az.compute/new-azvm) cmdlet으로 VM을 만들 때 `--image` 매개 변수와 함께 URN을 전달합니다. URN의 버전 번호를로 바꿔 `latest` 최신 버전의 이미지를 가져올 수도 있습니다.

Resource Manager 템플릿을 사용하여 VM을 배포하는 경우 `imageReference` 속성에 이미지 매개 변수를 개별적으로 설정합니다. [템플릿 참조](/azure/templates/microsoft.compute/virtualmachines)를 참조하세요.


## <a name="view-purchase-plan-properties"></a>구매 계획 속성 보기

Azure Marketplace의 일부 VM 이미지를 프로그래밍 방식으로 배포하려면 동의가 필요한 추가 라이선스 및 구매 약관이 있습니다. 구독 당 한 번 이미지의 사용 약관에 동의 해야 합니다.

이미지의 구매 계획 정보를 보려면 `Get-AzVMImage` cmdlet을 실행합니다. 출력의 `PurchasePlan` 속성이 `null`이 아닌 경우, 이미지에는 프로그램 방식으로 배포하기 전에 동의해야 하는 약관이 있습니다.  

예를 들어, *Windows Server 2016 Datacenter* 이미지는 `PurchasePlan` 정보가 `null`이기 때문에 추가 약관이 없습니다.

```powershell
$version = "2016.127.20170406"
Get-AzVMImage -Location $locName -PublisherName $pubName -Offer $offerName -Skus $skuName -Version $version
```

출력은 다음과 비슷합니다.

```output
Id               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/Providers/Microsoft.Compute/Locations/westus/Publishers/MicrosoftWindowsServer/ArtifactTypes/VMImage/Offers/WindowsServer/Skus/2016-Datacenter/Versions/2019.0.20190115
Location         : westus
PublisherName    : MicrosoftWindowsServer
Offer            : WindowsServer
Skus             : 2019-Datacenter
Version          : 2019.0.20190115
FilterExpression :
Name             : 2019.0.20190115
OSDiskImage      : {
                     "operatingSystem": "Windows"
                   }
PurchasePlan     : null
DataDiskImages   : []

```

아래 예제에서는  `PurchasePlan` `name` , `product` 및 속성을 포함 하는 Data Science Virtual Machine Windows 2016 이미지에 대 한 유사한 명령을 보여 `publisher` 줍니다. 일부 이미지에는 `promotion code` 속성도 있습니다. 이 이미지를 배포하려면 다음 섹션에서 약관에 동의하고 프로그래밍 방식 배포를 사용하도록 설정합니다.

```powershell
Get-AzVMImage -Location "westus" -PublisherName "microsoft-ads" -Offer "windows-data-science-vm" -Skus "windows2016" -Version "0.2.02"
```

출력은 다음과 비슷합니다.

```
Id               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/Providers/Microsoft.Compute/Locations/westus/Publishers/microsoft-ads/ArtifactTypes/VMImage/Offers/windows-data-science-vm/Skus/windows2016/Versions/19.01.14
Location         : westus
PublisherName    : microsoft-ads
Offer            : windows-data-science-vm
Skus             : windows2016
Version          : 19.01.14
FilterExpression :
Name             : 19.01.14
OSDiskImage      : {
                     "operatingSystem": "Windows"
                   }
PurchasePlan     : {
                     "publisher": "microsoft-ads",
                     "name": "windows2016",
                     "product": "windows-data-science-vm"
                   }
DataDiskImages   : []

```

사용 조건을 보려면 [Get-AzMarketplaceterms](/powershell/module/az.marketplaceordering/get-azmarketplaceterms) cmdlet을 사용하여 구매 계획 매개 변수를 전달합니다. 출력에는 Marketplace 이미지의 약관 대한 링크가 제공되며 이전에 약관에 동의했는지 여부가 표시됩니다. 매개 변수 값에 모두 소문자를 사용해야 합니다.

```powershell
Get-AzMarketplaceterms -Publisher "microsoft-ads" -Product "windows-data-science-vm" -Name "windows2016"
```

출력은 다음과 비슷합니다.

```output
Publisher         : microsoft-ads
Product           : windows-data-science-vm
Plan              : windows2016
LicenseTextLink   : https://storelegalterms.blob.core.windows.net/legalterms/3E5ED_legalterms_MICROSOFT%253a2DADS%253a24WINDOWS%253a2DDATA%253a2DSCIENCE%253a2DVM%253a24WINDOWS2016%253a24OC5SKMQOXSED66BBSNTF4XRCS4XLOHP7QMPV54DQU7JCBZWYFP35IDPOWTUKXUC7ZAG7W6ZMDD6NHWNKUIVSYBZUTZ245F44SU5AD7Q.txt
PrivacyPolicyLink : https://www.microsoft.com/EN-US/privacystatement/OnlineServices/Default.aspx
Signature         : 2UMWH6PHSAIM4U22HXPXW25AL2NHUJ7Y7GRV27EBL6SUIDURGMYG6IIDO3P47FFIBBDFHZHSQTR7PNK6VIIRYJRQ3WXSE6BTNUNENXA
Accepted          : False
Signdate          : 1/25/2019 7:43:00 PM
```

## <a name="accept-purchase-plan-terms"></a>구매 계획 조건 수락

[Set-AzMarketplaceterms](/powershell/module/az.marketplaceordering/set-azmarketplaceterms) cmdlet을 사용하여 약관에 동의하거나 거부합니다. 이미지의 구독마다 약관에 한 번만 동의하면 됩니다. 매개 변수 값에 모두 소문자를 사용해야 합니다. 

```powershell
$agreementTerms=Get-AzMarketplaceterms -Publisher "microsoft-ads" -Product "windows-data-science-vm" -Name "windows2016"

Set-AzMarketplaceTerms -Publisher "microsoft-ads" -Product "windows-data-science-vm" -Name "windows2016" -Terms $agreementTerms -Accept
```



```output
Publisher         : microsoft-ads
Product           : windows-data-science-vm
Plan              : windows2016
LicenseTextLink   : https://storelegalterms.blob.core.windows.net/legalterms/3E5ED_legalterms_MICROSOFT%253a2DADS%253a24WINDOWS%253a2DDATA%253a2DSCIENCE%253a2DV
                    M%253a24WINDOWS2016%253a24OC5SKMQOXSED66BBSNTF4XRCS4XLOHP7QMPV54DQU7JCBZWYFP35IDPOWTUKXUC7ZAG7W6ZMDD6NHWNKUIVSYBZUTZ245F44SU5AD7Q.txt
PrivacyPolicyLink : https://www.microsoft.com/EN-US/privacystatement/OnlineServices/Default.aspx
Signature         : XXXXXXK3MNJ5SROEG2BYDA2YGECU33GXTD3UFPLPC4BAVKAUL3PDYL3KBKBLG4ZCDJZVNSA7KJWTGMDSYDD6KRLV3LV274DLBXXXXXX
Accepted          : True
Signdate          : 2/23/2018 7:49:31 PM
```


## <a name="create-a-new-vm-from-a-marketplace-image"></a>Marketplace 이미지에서 새 VM 만들기

사용할 이미지에 대 한 정보가 이미 있는 경우 해당 정보를 [AzVMSourceImage](/powershell/module/az.compute/set-azvmsourceimage) cmdlet으로 전달 하 여 VM 구성에 이미지 정보를 추가할 수 있습니다. Marketplace에서 사용할 수 있는 이미지를 검색 하 고 나열 하려면 다음 섹션을 참조 하세요.

또한 일부 유료 이미지에서는 [AzVMPlan](/powershell/module/az.compute/set-azvmplan)를 사용 하 여 구매 계획 정보를 제공 해야 합니다. 

```powershell
...

$vmConfig = New-AzVMConfig -VMName "myVM" -VMSize Standard_D1

# Set the Marketplace image
$offerName = "windows-data-science-vm"
$skuName = "windows2016"
$version = "19.01.14"
$vmConfig = Set-AzVMSourceImage -VM $vmConfig -PublisherName $publisherName -Offer $offerName -Skus $skuName -Version $version

# Set the Marketplace plan information, if needed
$publisherName = "microsoft-ads"
$productName = "windows-data-science-vm"
$planName = "windows2016"
$vmConfig = Set-AzVMPlan -VM $vmConfig -Publisher $publisherName -Product $productName -Name $planName

...
```

그런 다음 다른 구성 개체와 함께 VM 구성을 cmdlet에 전달 `New-AzVM` 합니다. PowerShell에서 VM 구성을 사용 하는 방법에 대 한 자세한 예제는이 [스크립트](https://github.com/Azure/azure-docs-powershell-samples/blob/master/virtual-machine/create-vm-detailed/create-windows-vm-detailed.ps1)를 참조 하세요.

이미지의 약관에 동의 하는 것에 대 한 메시지가 표시 되 면 이전 섹션인 [구매 계획 조건 동의](#accept-purchase-plan-terms)를 참조 하세요.

## <a name="create-a-new-vm-from-a-vhd-with-purchase-plan-information"></a>구매 계획 정보를 사용 하 여 VHD에서 새 VM 만들기

Azure Marketplace 이미지를 사용 하 여 만든 기존 VHD가 있는 경우 해당 VHD에서 새 VM을 만들 때 구매 계획 정보를 제공 해야 할 수 있습니다.

원래 VM 또는 동일한 이미지에서 만든 다른 VM이 있는 경우 New-azvm를 사용 하 여 계획 이름, 게시자 및 제품 정보를 가져올 수 있습니다. 이 예제에서는 *Myvm* 리소스 그룹에서 *MYVM* 이라는 vm을 가져온 다음 구매 계획 정보를 표시 합니다.

```azurepowershell-interactive
$vm = Get-azvm `
   -ResourceGroupName myResourceGroup `
   -Name myVM
$vm.Plan
```

원본 VM을 삭제 하기 전에 계획 정보를 가져오지 못한 경우 [지원 요청](https://ms.portal.azure.com/#create/Microsoft.Support)을 받을 수 있습니다. VM 이름, 구독 ID 및 삭제 작업의 타임 스탬프가 필요 합니다.

VHD를 사용 하 여 VM을 만들려면이 문서 특수 한 [vhd에서 Vm 만들기](create-vm-specialized.md) 문서를 참조 하 고 [AzVMPlan](/powershell/module/az.compute/set-azvmplan) 를 사용 하 여 vm 구성에 계획 정보를 추가 하도록 줄을 추가 합니다.

```azurepowershell-interactive
$vmConfig = Set-AzVMPlan `
   -VM $vmConfig `
   -Publisher "publisherName" `
   -Product "productName" `
   -Name "planName"
```


## <a name="next-steps"></a>다음 단계

기본 이미지 정보를 사용하여 `New-AzVM` cmdlet으로 가상 머신을 빠르게 만들려면 [PowerShell을 사용하여 Windows 가상 머신 만들기](quick-create-powershell.md)를 참조하세요.

Azure Marketplace 이미지를 사용 하 여 공유 이미지 갤러리에서 사용자 지정 이미지를 만드는 방법에 대 한 자세한 내용은 [이미지를 만들 때 Azure Marketplace 구매 계획 정보 제공](../marketplace-images.md)을 참조 하세요.
