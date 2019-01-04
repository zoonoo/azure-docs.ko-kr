---
title: Azure는 ASDK 등록 | Microsoft Docs
description: Azure Stack marketplace 배포 및 사용 보고를 사용 하도록 설정 하려면 Azure를 사용 하 여 등록 하는 방법에 설명 합니다.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: 00c4d750d0617d36ab476719ce31c8038065511c
ms.sourcegitcommit: 9f87a992c77bf8e3927486f8d7d1ca46aa13e849
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/28/2018
ms.locfileid: "53807213"
---
# <a name="azure-stack-registration"></a>Azure Stack 등록
Azure에서 마켓플레이스 항목을 다운로드 하 고 Microsoft에 다시 보고 하는 전자 상거래 데이터를 설정 하는 Azure를 사용 하 여 Azure Stack 개발 키트 ASDK () 설치를 등록할 수 있습니다. 마켓플레이스 배포를 포함 하 여 전체 Azure Stack 기능을 지원 하려면 등록이 필요 합니다. 마켓플레이스 배포 및 사용 보고와 같은 중요 한 Azure Stack 기능을 테스트할 수 있기 때문에 등록을 사용 하는 것이 좋습니다. Azure Stack 등록 한 후 Azure 상거래에 사용량이 보고 됩니다. 등록에 사용한 구독에서 볼 수 있습니다. 그러나 ASDK 사용자가 보고 하는 사용량에 대 한 요금이 청구 되지 않습니다.

ASDK 프로그램을 등록 하지 않은 경우 발생할 수 있습니다는 **활성화 필요** Azure Stack 개발 키트를 등록 해야 한다는 경고 합니다. 이는 정상적인 동작입니다.

## <a name="prerequisites"></a>필수 조건
이러한 지침을 ASDK Azure를 사용 하 여 등록을 사용 하기 전에 Azure Stack PowerShell 설치 되어 있고에 설명 된 대로 Azure Stack 도구를 다운로드를 확인 합니다 [사후 배포 구성](asdk-post-deploy.md) 문서.

또한 PowerShell 언어 모드를로 변경 해야 **FullLanguageMode** ASDK를 Azure에 등록 하는 데 사용 하는 컴퓨터입니다. 전체 관리자 권한 PowerShell 창을 열고 다음 PowerShell 명령을 실행 하려면 현재 언어 모드를 설정 되어 있는지 확인 합니다.

```PowerShell  
$ExecutionContext.SessionState.LanguageMode
```

출력 반환 **FullLanguageMode**합니다. 다른 언어 모드 반환, 다른 컴퓨터에서 실행 해야 등록 되거나 언어 모드 설정 해야 합니다 **FullLanguageMode** 계속 하기 전에 합니다.

## <a name="register-azure-stack-with-azure"></a>Azure를 사용 하 여 Azure Stack 등록
Azure는 ASDK 등록 하려면 다음이 단계를 따릅니다.

> [!NOTE]
> 이러한 모든 단계는 권한 있는 끝점에 대 한 액세스 권한이 있는 컴퓨터에서 실행 되어야 합니다. ASDK에 대 한 개발 키트 호스트 컴퓨터입니다.

1. 관리자 권한으로 PowerShell 콘솔을 엽니다.  

2. Azure를 사용 하 여 ASDK 설치를 등록 하려면 다음 PowerShell 명령을 실행 합니다. Azure 구독 및 로컬 ASDK 설치 모두에 로그인 해야 합니다. 아직 Azure 구독이 없는 할 수 있습니다 [여기서 무료 Azure 계정을 만들](https://azure.microsoft.com/free/?b=17.06)합니다. Azure Stack 등록 비용 없이 Azure 구독에서 발생 합니다.<br><br>실행 하는 경우 등록 스크립트를 Azure Stack의 둘 이상의 인스턴스에서 동일한 Azure 구독 ID를 사용 하 여를 실행 하는 경우 등록에 대 한 고유한 이름을 설정 합니다 **집합 AzsRegistration** cmdlet. 합니다 **RegistrationName** 매개 변수는 기본값인 **AzureStackRegistration**합니다. 그러나 Azure Stack의 둘 이상의 인스턴스에서 같은 이름을 사용할 경우 스크립트가 실패 합니다.

    ```PowerShell  
    # Add the Azure cloud subscription environment name. 
    # Supported environment names are AzureCloud, AzureChinaCloud or AzureUSGovernment depending which Azure subscription you are using.
    Add-AzureRmAccount -EnvironmentName "<environment name>"

    # Register the Azure Stack resource provider in your Azure subscription
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack

    # Import the registration module that was downloaded with the GitHub tools
    Import-Module C:\AzureStack-Tools-master\Registration\RegisterWithAzure.psm1

    # Register Azure Stack
    $AzureContext = Get-AzureRmContext
    $CloudAdminCred = Get-Credential -UserName AZURESTACK\CloudAdmin -Message "Enter the credentials to access the privileged endpoint."
    $RegistrationName = "<unique-registration-name>"
    Set-AzsRegistration `
    -PrivilegedEndpointCredential $CloudAdminCred `
    -PrivilegedEndpoint AzS-ERCS01 `
    -BillingModel Development `
    -RegistrationName $RegistrationName `
    -UsageReportingEnabled:$true
    ```
3. 스크립트가 완료 되 면이 메시지가 표시 됩니다. **이제 등록 된 환경과 제공된 된 매개 변수를 사용 하 여 활성화 합니다.**

    ![환경의 지금 등록](media/asdk-register/1.PNG)


## <a name="register-in-disconnected-environments"></a>연결이 끊어진된 환경에 등록
Azure Stack 환경에서 등록 하는 토큰을 가져오는 다음 해당 토큰을 사용 하 여 등록 하 고 활성화를 만들 Azure에 연결할 수 있는 컴퓨터를 해야으로 등록 하는 Azure Stack에서 연결이 끊어진된 환경 (인터넷 연결 되지 않은 경우)을 하는 경우 ASDK 환경에 대 한 리소스입니다.
 
 > [!IMPORTANT]
 > Azure Stack 용 PowerShell가 설치 되어 있고에 설명 된 대로 Azure Stack 도구를 다운로드 하기 전에 다음이 지침을 사용 하 여 Azure Stack 등록을 합니다 [사후 배포 구성](asdk-post-deploy.md) ASDK 호스트에 대 한 문서 컴퓨터 및 인터넷에 액세스할 등록 및 Azure에 연결 하는 데 사용 하 여 컴퓨터.

### <a name="get-a-registration-token-from-the-azure-stack-environment"></a>Azure Stack 환경에서 등록 토큰 가져오기
ASDK 호스트 컴퓨터에서 관리자 권한으로 PowerShell을 시작 하 고 이동할 합니다 **등록** 폴더에는 **azurestack의 경우 도구-마스터** Azure Stack 도구를 다운로드 하는 경우 만든 디렉터리입니다. 가져오려면 다음 PowerShell 명령을 사용 합니다 **RegisterWithAzure.psm1** 모듈을 사용 하 여는 **Get AzsRegistrationToken** cmdlet 등록 토큰을 가져오려면:  

   ```PowerShell  
   # Import the registration module that was downloaded with the GitHub tools
   Import-Module C:\AzureStack-Tools-master\Registration\RegisterWithAzure.psm1

   # Create registration token
   $CloudAdminCred = Get-Credential -UserName AZURESTACK\CloudAdmin -Message "Enter the credentials to access the privileged endpoint."
   # File path to save the token. This example saves the file as C:\RegistrationToken.txt.
   $FilePathForRegistrationToken = "$env:SystemDrive\RegistrationToken.txt"
   $RegistrationToken = Get-AzsRegistrationToken -PrivilegedEndpointCredential $CloudAdminCred `
   -UsageReportingEnabled:$false `
   -PrivilegedEndpoint AzS-ERCS01 `
   -BillingModel Development `
   -MarketplaceSyndicationEnabled:$false `
   -TokenOutputFilePath $FilePathForRegistrationToken
   ```

인터넷에 연결 된 컴퓨터의 사용에 대 한이 등록 토큰을 저장 합니다. $FilePathForRegistrationToken 매개 변수에서 생성 된 파일에서 텍스트 또는 파일을 복사할 수 있습니다.

### <a name="connect-to-azure-and-register"></a>등록 및 Azure에 연결
인터넷에 연결 된 컴퓨터 명령을 사용 하 여 다음 PowerShell 가져오기 합니다 **RegisterWithAzure.psm1** 모듈을 사용 하 여 합니다 **등록 AzsEnvironment** cmdlet을 사용 하 여 Azure 등록 방금 만든 등록 토큰 및 고유 등록 이름:  

  ```PowerShell  
  # Add the Azure cloud subscription environment name. 
  # Supported environment names are AzureCloud, AzureChinaCloud or AzureUSGovernment depending which Azure subscription you are using.
  Add-AzureRmAccount -EnvironmentName "<environment name>"

  # If you have multiple subscriptions, run the following command to select the one you want to use:
  # Get-AzureRmSubscription -SubscriptionID "<subscription ID>" | Select-AzureRmSubscription

  # Register the Azure Stack resource provider in your Azure subscription
  Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack

  # Import the registration module that was downloaded with the GitHub tools
  Import-Module C:\AzureStack-Tools-master\Registration\RegisterWithAzure.psm1

  # Register with Azure
  # This example uses the C:\RegistrationToken.txt file.
  $registrationToken = Get-Content -Path "$env:SystemDrive\RegistrationToken.txt"
  $RegistrationName = "<unique-registration-name>"
  Register-AzsEnvironment -RegistrationToken $registrationToken `
  -RegistrationName $RegistrationName
  ```

사용할 수 있습니다 합니다 **Get-content** cmdlet 등록 토큰을 포함 하는 파일을 가리키도록 합니다.

  ```PowerShell  
  # Add the Azure cloud subscription environment name. 
  # Supported environment names are AzureCloud, AzureChinaCloud or AzureUSGovernment depending which Azure subscription you are using.
  Add-AzureRmAccount -EnvironmentName "<environment name>"

  # If you have multiple subscriptions, run the following command to select the one you want to use:
  # Get-AzureRmSubscription -SubscriptionID "<subscription ID>" | Select-AzureRmSubscription

  # Register the Azure Stack resource provider in your Azure subscription
  Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack

  # Import the registration module that was downloaded with the GitHub tools
  Import-Module C:\AzureStack-Tools-master\Registration\RegisterWithAzure.psm1

  # Register with Azure 
  # This example uses the C:\RegistrationToken.txt file.
  $registrationToken = Get-Content -Path "$env:SystemDrive\RegistrationToken.txt"
  Register-AzsEnvironment -RegistrationToken $registrationToken `
  -RegistrationName $RegistrationName
  ```

유사한 메시지가 표시 등록이 완료 되 면 **Your Azure Stack 환경과 이제 Azure에 등록 됩니다.**

> [!IMPORTANT]
> PowerShell 창을 닫지 마세요. 

나중에 참조할 수에 대 한 등록 리소스 이름을 확인 하 고 등록 토큰을 저장 합니다.

### <a name="retrieve-an-activation-key-from-the-azure-registration-resource"></a>Azure 등록 리소스에서 활성화 키를 검색 합니다.

여전히 인터넷에 연결 된 컴퓨터를 사용 하 여 **와 같은 PowerShell 콘솔 창**, Azure를 사용 하 여 등록할 때 만든 등록 리소스에서 활성화 키를 검색 합니다.

정품 인증 키를 가져오려면 다음 PowerShell 명령을 실행, 이전 단계에서 Azure를 사용 하 여 등록할 때 제공한 동일한 경우 고유한 등록 이름 값을 사용 합니다.  

  ```Powershell
  $RegistrationResourceName = "<unique-registration-name>"
  # File path to save the activation key. This example saves the file as C:\ActivationKey.txt.
  $KeyOutputFilePath = "$env:SystemDrive\ActivationKey.txt"
  $ActivationKey = Get-AzsActivationKey -RegistrationName $RegistrationResourceName `
  -KeyOutputFilePath $KeyOutputFilePath
  ```
### <a name="create-an-activation-resource-in-azure-stack"></a>Azure Stack에서 정품 인증 리소스 만들기

파일을 사용 하 여 Azure Stack 환경에 반환 하거나 정품 인증 키의 텍스트에서 만든 **Get AzsActivationKey**합니다. 해당 정품 인증 키를 사용 하 여 Azure Stack에서 정품 인증 리소스를 만들려면 다음 PowerShell 명령을 실행 합니다.   

  ```Powershell
  # Import the registration module that was downloaded with the GitHub tools
  Import-Module C:\AzureStack-Tools-master\Registration\RegisterWithAzure.psm1
  
  $CloudAdminCred = Get-Credential -UserName AZURESTACK\CloudAdmin -Message "Enter the credentials to access the privileged endpoint."
  $ActivationKey = "<activation key>"
  New-AzsActivationResource -PrivilegedEndpointCredential $CloudAdminCred `
  -PrivilegedEndpoint AzS-ERCS01 `
  -ActivationKey $ActivationKey
  ```

사용할 수 있습니다 합니다 **Get-content** cmdlet 등록 토큰을 포함 하는 파일을 가리키도록 합니다.

  ```Powershell
  # Import the registration module that was downloaded with the GitHub tools
  Import-Module C:\AzureStack-Tools-master\Registration\RegisterWithAzure.psm1

  $CloudAdminCred = Get-Credential -UserName AZURESTACK\CloudAdmin -Message "Enter the credentials to access the privileged endpoint."
  # This example uses the C:\ActivationKey.txt file.
  $ActivationKey = Get-Content -Path "$env:SystemDrive\Activationkey.txt"
  New-AzsActivationResource -PrivilegedEndpointCredential $CloudAdminCred `
  -PrivilegedEndpoint AzS-ERCS01 `
  -ActivationKey $ActivationKey
  ```

유사한 메시지가 표시 활성화가 완료 되 면 **환경의 등록 및 정품 인증 프로세스를 완료 합니다.**

## <a name="verify-the-registration-was-successful"></a>등록이 성공 했는지 확인 합니다.

사용할 수는 **지역 관리** 타일 Azure Stack 등록에 성공 했음을 확인 합니다. 이 타일은 관리자 포털에서 기본 대시보드에서 사용할 수 있습니다.

1. 에 로그인 합니다 [Azure Stack 관리 포털](https://adminportal.local.azurestack.external)합니다.

2. 대시보드에서 선택 **하위 지역 관리**합니다.

    [ ![지역 관리 타일](media/asdk-register/admin1sm.png "지역 관리 타일") ](media/asdk-register/admin1.png#lightbox)

3. **속성**을 선택합니다. 이 블레이드는 상태 및 사용자 환경의 세부 정보를 보여 줍니다. 상태 수 있습니다 **Registered** 또는 **등록 되지 않은**합니다. 등록 하는 경우 또한 보여줍니다 Azure 구독 ID를 사용 하 여 Azure Stack에 등록 리소스 그룹 및 이름과 함께 등록를.

## <a name="move-a-registration-resource"></a>등록 리소스 이동
동일한 구독에서 리소스 그룹을 사용 하 여 등록 리소스를 이동할 **는** 지원 합니다. 새 리소스 그룹에 리소스를 이동 하는 방법에 대 한 자세한 내용은 참조 하세요. [새 리소스 그룹 또는 구독으로 리소스 이동](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)합니다.


## <a name="next-steps"></a>다음 단계

- [Azure Stack marketplace 항목 추가](../azure-stack-marketplace.md)
