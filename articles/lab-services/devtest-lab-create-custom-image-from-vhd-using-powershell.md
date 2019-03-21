---
title: PowerShell을 사용하여 VHD 파일에서 Azure DevTest Labs 사용자 지정 이미지 만들기 | Microsoft Docs
description: PowerShell을 사용하여 VHD 파일에서 Azure DevTest Labs에 사용자 지정 이미지 만들기 자동화
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/05/2018
ms.author: spelluru
ms.openlocfilehash: e594ace368799f85eea2e7291ead6febea0ea4b7
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/07/2019
ms.locfileid: "57543885"
---
# <a name="create-a-custom-image-from-a-vhd-file-using-powershell"></a>PowerShell을 사용하여 VHD 파일에서 사용자 지정 이미지 만들기

[!INCLUDE [devtest-lab-create-custom-image-from-vhd-selector](../../includes/devtest-lab-create-custom-image-from-vhd-selector.md)]

[!INCLUDE [devtest-lab-custom-image-definition](../../includes/devtest-lab-custom-image-definition.md)]

[!INCLUDE [devtest-lab-upload-vhd-options](../../includes/devtest-lab-upload-vhd-options.md)]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="step-by-step-instructions"></a>단계별 지침

다음 단계는 PowerShell을 사용하여 VHD 파일에서 사용자 지정 이미지를 만드는 과정을 안내합니다.

1. PowerShell 프롬프트에서 로그 다음 호출을 사용 하 여 Azure 계정에는 **Connect AzAccount** cmdlet.  
    
    ```PowerShell
    Connect-AzAccount
    ```

1.  호출 하 여 Azure 구독을 선택 합니다 **선택 AzSubscription** cmdlet. **$subscriptionId** 변수에 대한 다음 자리 표시자를 유효한 Azure 구독 ID로 바꿉니다. 

    ```PowerShell
    $subscriptionId = '<Specify your subscription ID here>'
    Select-AzSubscription -SubscriptionId $subscriptionId
    ```

1.  호출 하 여 랩 개체를 가져올는 **Get AzResource** cmdlet. **$labRg** 및 **$labName** 변수에 대한 다음 자리 표시자를 환경에 적합한 값으로 바꿉니다. 

    ```PowerShell
    $labRg = '<Specify your lab resource group name here>'
    $labName = '<Specify your lab name here>'
    $lab = Get-AzResource -ResourceId ('/subscriptions/' + $subscriptionId + '/resourceGroups/' + $labRg + '/providers/Microsoft.DevTestLab/labs/' + $labName)
    ```
 
1.  랩 객체에서 랩 저장소 계정 및 랩 저장소 계정 키 값을 가져옵니다. 

    ```PowerShell
    $labStorageAccount = Get-AzResource -ResourceId $lab.Properties.defaultStorageAccount 
    $labStorageAccountKey = (Get-AzStorageAccountKey -ResourceGroupName $labStorageAccount.ResourceGroupName -Name $labStorageAccount.ResourceName)[0].Value
    ```

1.  **$vhdUri** 변수에 대한 다음 자리 표시자를 업로드한 VHD 파일에 대한 URI로 바꿉니다. Azure Portal의 저장소 계정의 Blob 블레이드에서 VHD 파일의 URl를 가져올 수 있습니다.

    ```PowerShell
    $vhdUri = '<Specify the VHD URI here>'
    ```

1.  사용 하 여 사용자 지정 이미지 만들기를 **새로 만들기-AzResourceGroupDeployment** cmdlet. **$customImageName** 및 **$customImageDescription** 변수에 대한 다음 자리 표시자를 환경에 의미 있는 이름으로 바꿉니다.

    ```PowerShell
    $customImageName = '<Specify the custom image name>'
    $customImageDescription = '<Specify the custom image description>'

    $parameters = @{existingLabName="$($lab.Name)"; existingVhdUri=$vhdUri; imageOsType='windows'; isVhdSysPrepped=$false; imageName=$customImageName; imageDescription=$customImageDescription}

    New-AzResourceGroupDeployment -ResourceGroupName $lab.ResourceGroupName -Name CreateCustomImage -TemplateUri 'https://raw.githubusercontent.com/Azure/azure-devtestlab/master/Samples/201-dtl-create-customimage-from-vhd/azuredeploy.json' -TemplateParameterObject $parameters
    ```

## <a name="powershell-script-to-create-a-custom-image-from-a-vhd-file"></a>VHD 파일에서 사용자 지정 이미지를 만들 PowerShell 스크립트

다음 PowerShell 스크립트를 사용하여 VHD 파일에서 사용자 지정 이미지를 만들 수 있습니다. 자리 표시자(각괄호)를 필요한 적합한 값으로 바꿉니다. 

```PowerShell
# Log in to your Azure account.  
Connect-AzAccount

# Select the desired Azure subscription. 
$subscriptionId = '<Specify your subscription ID here>'
Select-AzSubscription -SubscriptionId $subscriptionId

# Get the lab object.
$labRg = '<Specify your lab resource group name here>'
$labName = '<Specify your lab name here>'
$lab = Get-AzResource -ResourceId ('/subscriptions/' + $subscriptionId + '/resourceGroups/' + $labRg + '/providers/Microsoft.DevTestLab/labs/' + $labName)

# Get the lab storage account and lab storage account key values.
$labStorageAccount = Get-AzResource -ResourceId $lab.Properties.defaultStorageAccount 
$labStorageAccountKey = (Get-AzStorageAccountKey -ResourceGroupName $labStorageAccount.ResourceGroupName -Name $labStorageAccount.ResourceName)[0].Value

# Set the URI of the VHD file.  
$vhdUri = '<Specify the VHD URI here>'

# Set the custom image name and description values.
$customImageName = '<Specify the custom image name>'
$customImageDescription = '<Specify the custom image description>'

# Set up the parameters object.
$parameters = @{existingLabName="$($lab.Name)"; existingVhdUri=$vhdUri; imageOsType='windows'; isVhdSysPrepped=$false; imageName=$customImageName; imageDescription=$customImageDescription}

# Create the custom image. 
New-AzResourceGroupDeployment -ResourceGroupName $lab.ResourceGroupName -Name CreateCustomImage -TemplateUri 'https://raw.githubusercontent.com/Azure/azure-devtestlab/master/Samples/201-dtl-create-customimage-from-vhd/azuredeploy.json' -TemplateParameterObject $parameters
```

## <a name="related-blog-posts"></a>관련 블로그 게시물

- [사용자 지정 이미지 또는 수식?](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)
- [Azure DevTest Labs 간의 사용자 지정 이미지 복사](https://www.visualstudiogeeks.com/blog/DevOps/How-To-Move-CustomImages-VHD-Between-AzureDevTestLabs#copying-custom-images-between-azure-devtest-labs)

## <a name="next-steps"></a>다음 단계

- [랩에 VM 추가](devtest-lab-add-vm.md)
