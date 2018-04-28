---
title: Azure는 ASDK 등록 | Microsoft Docs
description: Azure 마켓플레이스 배포 및 사용 보고를 사용 하도록 설정 하려면 Azure 스택을 등록 하는 방법을 설명 합니다.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: 0ee18a7038ea9a4eebb76bc6648b94940d1db5c7
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/19/2018
---
# <a name="register-azure-stack-with-azure"></a>Azure 스택 Azure 등록
Azure에서 Azure 마켓플레이스 항목을 다운로드 하 고 상용 데이터를 Microsoft에 다시 보고를 설정 하 여 Azure 스택 개발 키트 (ASDK) 설치를 등록할 수 있습니다. 마켓플레이스 배포 및 사용 보고와 같은 중요 한 Azure 스택 기능을 테스트 하면 있기 때문에 등록을 사용 하는 것이 좋습니다. Azure 스택 등록 한 후 사용 Azure 상거래에 보고 됩니다. 등록에 사용 되는 구독에서 볼 수 있습니다. 그러나 ASDK 사용자가 보고 사용에 대 한 요금이 청구 되지 않습니다.


## <a name="register-azure-stack-with-azure"></a>Azure 스택 Azure 등록 
Azure는 ASDK를 등록 하려면 다음이 단계를 수행 합니다.

> [!NOTE]
> 이러한 모든 단계는 권한 있는 끝점에 액세스할 수 있는 컴퓨터에서 실행 되어야 합니다. ASDK에 대 한 개발 키트 호스트 컴퓨터입니다. 

Azure는 ASDK를 등록 하려면 다음이 단계를 사용 하기 전에 Azure 스택 PowerShell을 설치 하 고에 설명 된 대로 Azure 스택 도구를 다운로드 했는지 확인는 [배포 후 구성](asdk-post-deploy.md) 문서. 

1. 관리자 권한으로 PowerShell 콘솔을 엽니다.  

2. (로컬 ASDK 설치와 Azure 구독에 로그인 할 필요는) azure ASDK 설치를 등록 하려면 다음 PowerShell 명령을 실행 합니다.

    ```PowerShell
    # Add the Azure cloud subscription environment name. Supported environment names are AzureCloud or, if using a China Azure Subscription, AzureChinaCloud.
    Connect-AzureRmAccount -EnvironmentName "AzureCloud"

    # Register the Azure Stack resource provider in your Azure subscription
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack

    #Import the registration module that was downloaded with the GitHub tools
    Import-Module C:\AzureStack-Tools-master\Registration\RegisterWithAzure.psm1

    #Register Azure Stack
    $AzureContext = Get-AzureRmContext
    $CloudAdminCred = Get-Credential -UserName AZURESTACK\CloudAdmin -Message "Enter the credentials to access the privileged endpoint."
    Set-AzsRegistration `
        -PrivilegedEndpointCredential $CloudAdminCred `
        -PrivilegedEndpoint AzS-ERCS01 `
        -BillingModel Development

3. When the script completes, you should see this message: **Your environment is now registered and activated using the provided parameters.**

    ![](media/asdk-register/1.PNG) 

## Verify the registration was successful
Follow these steps to verify that the ASDK registration with Azure was successful.

1. Sign in to the [Azure Stack administration portal](https://adminportal.local.azurestack.external).

2. Click **Marketplace Management** > **Add from Azure**.

    ![](media/asdk-register/2.PNG) 

3. If you see a list of items available from Azure, your activation was successful.

    ![](media/asdk-register/3.PNG) 

## Next steps
[Add an Azure Stack marketplace item](asdk-marketplace-item.md)
