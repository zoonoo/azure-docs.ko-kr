---
title: Azure Stack에 대 한 azure 등록 통합 시스템 | Microsoft Docs
description: 다중 노드 배포 Azure Stack에 연결 된 Azure 등록 프로세스를 설명합니다.
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
ms.date: 07/09/2018
ms.author: jeffgilb
ms.reviewer: brbartle
ms.openlocfilehash: 65525ffe33ddc100dd3066e7c2b52ef8a856fbc3
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/10/2018
ms.locfileid: "37934773"
---
# <a name="register-azure-stack-with-azure"></a>Azure를 사용 하 여 Azure Stack 등록

등록 [Azure Stack](azure-stack-poc.md) Azure를 사용 하 여 사용 하면 Azure에서 마켓플레이스 항목을 다운로드 하 고 Microsoft에 다시 보고 하는 전자 상거래 데이터를 설정 합니다. Azure Stack 등록 한 후에 사용량 Azure 상거래에 보고 되 고 등록에 사용 되는 구독에서 볼 수 있습니다.

> [!IMPORTANT]  
> 마켓플레이스 배포를 포함 하 여 전체 Azure Stack 기능을 지원 하려면 등록이 필요 합니다. 또한 Azure Stack-수-종 청구 모델을 사용 하는 경우 등록 하지 않은 경우 사용 조건에 위반에서 됩니다. Azure Stack 라이선스 모델에 대 한 자세한 내용은 참조 하십시오 합니다 [구입 방법 페이지](https://azure.microsoft.com/overview/azure-stack/how-to-buy/)합니다.

## <a name="prerequisites"></a>필수 조건

Azure Stack에 Azure를 등록 하기 전에 다음이 필요 합니다.

- Azure 구독에 대 한 구독 ID입니다. ID를 가져오려면 Azure에 로그인을 클릭 **더 많은 서비스** > **구독**를 사용 하려는 구독을 클릭 하 **Essentials** 찾을 수 있습니다는 구독 id입니다.

  > [!NOTE]
  > 독일 및 미국 정부 클라우드 구독은 현재 지원 되지 않습니다.

- 사용자 이름 및 구독에 대 한 소유자가 계정에 대 한 암호 (MSA/2FA 계정 지원 됨).
- Azure Stack 리소스 공급자 등록 (아래 세부 정보에 대 한 Azure Stack 리소스 공급자 등록 섹션 참조).

이러한 요구 사항을 충족 하는 Azure 구독이 없으면 있습니다 [여기서 무료 Azure 계정을 만들](https://azure.microsoft.com/free/?b=17.06)합니다. Azure Stack 등록 비용 없이 Azure 구독에서 발생 합니다.

### <a name="powershell-language-mode"></a>PowerShell 언어 모드

Azure Stack을 성공적으로 등록 하려면 PowerShell 언어 모드 설정 해야 합니다 **FullLanguageMode**합니다.  전체 관리자 권한 PowerShell 창을 열고 다음 PowerShell 명령을 실행 하려면 현재 언어 모드를 설정 되어 있는지 확인 합니다.

```powershell
$ExecutionContext.SessionState.LanguageMode
```

출력 반환 **FullLanguageMode**합니다. 다른 언어 모드 반환, 다른 컴퓨터에서 실행 되도록 등록 해야 되거나 언어 모드 설정 해야 합니다 **FullLanguageMode** 계속 하기 전에 합니다.

### <a name="bkmk_powershell"></a>Azure Stack 용 PowerShell 설치

Azure Stack에 대 한 최신 PowerShell을 사용 하 여 Azure를 사용 하 여 등록 해야 합니다.

설치 되어 있지 [Azure Stack 용 PowerShell 설치](https://docs.microsoft.com/azure/azure-stack/azure-stack-powershell-install)합니다.

### <a name="bkmk_tools"></a>Azure Stack 도구 다운로드

Azure Stack 기능을 지 원하는 PowerShell 모듈을 포함 하는 Azure Stack 도구 GitHub 리포지토리 등록 기능을 포함 합니다. 가져오고 RegisterWithAzure.psm1 PowerShell 모듈을 사용 해야 등록 과정에서 Azure를 사용 하 여 Azure Stack 인스턴스를 등록 하려면 Azure Stack 도구 리포지토리에서 찾을 수 있습니다.

을 보장 하기 위해 최신 버전을 사용 하는 모든 기존 버전의 Azure Stack 도구를 삭제 해야 하 고 [GitHub에서 최신 버전 다운로드](azure-stack-powershell-download.md) Azure를 사용 하 여 등록 하기 전에 합니다.

## <a name="register-azure-stack-in-connected-environments"></a>연결 된 환경에서 Azure Stack 등록

연결 된 환경 인터넷 및 Azure에 액세스할 수 있습니다. 이러한 환경에서는 Azure를 사용 하 여 Azure Stack 리소스 공급자를 등록 하 고 다음 청구 모델을 구성 해야 합니다.

> [!NOTE]
> 이러한 모든 단계는 권한 있는 끝점에 대 한 액세스 권한이 있는 컴퓨터에서 실행 되어야 합니다.

### <a name="register-the-azure-stack-resource-provider"></a>Azure Stack 리소스 공급자 등록

Azure를 사용 하 여 Azure Stack 리소스 공급자를 등록 하려면 관리자 권한으로 PowerShell ISE를 시작 하 고 사용 하 여 다음 PowerShell 명령을 사용 합니다 **EnvironmentName** 적절 한 Azure 구독 유형 (참조로 매개 변수 설정 매개 변수 아래)입니다.

1. Azure Stack 등록을 사용 하는 Azure 계정을 추가 합니다. 실행 계정을 추가 합니다 **Add-azurermaccount** cmdlet. Azure 전역 관리자 계정 자격 증명을 입력 하 라는 메시지가 표시 됩니다 하 고 계정 구성에 따라 2 단계 인증을 사용 해야 합니다.

   ```PowerShell
      Add-AzureRmAccount -EnvironmentName "<Either AzureCloud or AzureChinaCloud>"
   ```

   | 매개 변수 | 설명 |  
   |-----|-----|
   | EnvironmentName | Azure 클라우드 구독 환경 이름입니다. 지원 되는 환경 이름은 **AzureCloud** 중국 Azure 구독을 사용 하는 경우 **AzureChinaCloud**합니다.  |
   |  |  |

2. 여러 구독이 있는 경우 사용 하려는 것을 선택 하려면 다음 명령을 실행 합니다.  

   ```PowerShell
      Get-AzureRmSubscription -SubscriptionID '<Your Azure Subscription GUID>' | Select-AzureRmSubscription
   ```

3. Azure 구독에서 Azure Stack 리소스 공급자를 등록 하려면 다음 명령을 실행 합니다.

   ```PowerShell
   Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack
   ```

### <a name="register-azure-stack-with-azure-using-the-pay-as-you-use-billing-model"></a>수-종 청구 모델을 사용 하 여 Azure를 사용 하 여 Azure Stack 등록

이러한 단계를 사용 하 여-수-종 청구 모델을 사용 하 여 Azure를 사용 하 여 Azure Stack 등록 합니다.

1. 관리자 권한으로 PowerShell ISE를 시작 하 고 이동 합니다 **등록** 폴더에는 **azurestack의 경우 도구-마스터** 디렉터리를 만들 때 있습니다 [AzureStack도구다운로드](#bkmk_tools). 가져오기의 **RegisterWithAzure.psm1** PowerShell을 사용 하 여 모듈:

   ```powershell
   Import-Module .\RegisterWithAzure.psm1
   ```

2. 다음으로, 동일한 PowerShell 세션에서 올바른 Azure PowerShell 컨텍스트에 로그인를 확인 합니다. 이 위의 Azure Stack 리소스 공급자를 등록 하는 데 사용 된 azure 계정입니다. Powershell 실행:

   ```powershell
   Add-AzureRmAccount -Environment "<Either AzureCloud or AzureChinaCloud>"
   ```

3. 동일한 PowerShell 세션에서 실행 합니다 **집합 AzsRegistration** cmdlet. PowerShell 실행:  

   ```powershell
   $CloudAdminCred = Get-Credential -UserName <Privileged endpoint credentials> -Message "Enter the cloud domain credentials to access the privileged endpoint."
   Set-AzsRegistration `
      -PrivilegedEndpointCredential $CloudAdminCred `
      -PrivilegedEndpoint <PrivilegedEndPoint computer name> `
      -BillingModel PayAsYouUse
   ```

  |매개 변수|설명|
  |-----|-----|
  |PrivilegedEndpointCredential|에 사용 된 자격 증명 [끝점에 권한 있는 액세스](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint)합니다. 형식의 사용자 이름이 **AzureStackDomain\CloudAdmin**합니다.|
  |PrivilegedEndpoint|미리 구성 된 원격 PowerShell 콘솔을 제공 하는 로그 수집 및 기타 post와 같은 기능을 사용 하 여 배포 작업입니다. 자세한 내용은 참조는 [권한 있는 끝점을 사용 하 여](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint) 문서.|
  |BillingModel|구독을 사용 하는 청구 모델입니다. 허용 되는이 매개 변수 값은: 용량과 PayAsYouUse를 개발 합니다.|
  |  |  |

  프로세스는 10 ~ 15 분 걸립니다. 메시지가 표시는 명령에는 다음이 완료 되 면 **"환경의 이제 등록 되 고 제공된 된 매개 변수를 사용 하 여 활성화 합니다."**

### <a name="register-azure-stack-with-azure-using-the-capacity-billing-model"></a>용량 요금 청구 모델을 사용 하 여 Azure를 사용 하 여 Azure Stack 등록

수-종 청구 모델을 사용 하 여 등록에 사용 되는 동일한 지침을 따르고 용량 구매한 계약 번호를 추가 하지만 변경 된 **BillingModel** 매개 변수를 **용량**. 다른 모든 매개 변수가 변경 되지 않습니다.

PowerShell 실행:

```powershell
$CloudAdminCred = Get-Credential -UserName <Privileged endpoint credentials> -Message "Enter the cloud domain credentials to access the privileged endpoint."
Set-AzsRegistration `
    -PrivilegedEndpointCredential $CloudAdminCred `
    -PrivilegedEndpoint <PrivilegedEndPoint computer name> `
    -AgreementNumber <EA agreement number> `
    -BillingModel Capacity
```

## <a name="register-azure-stack-in-disconnected-environments"></a>연결이 끊어진된 환경에서 Azure Stack 등록
Azure Stack 환경에서 등록 토큰 가져오기 및 다음 해당 토큰을 사용 하 여 Azure에 연결할 수 있고 컴퓨터 해야 연결이 끊어진된 환경에서 Azure Stack 등록 하는 (사용 하 여 인터넷 연결 되지 않은 경우), 경우 [PowerShell 설치 된 Azure Stack 용](#bkmk_powershell)합니다.  

### <a name="get-a-registration-token-from-the-azure-stack-environment"></a>Azure Stack 환경에서 등록 토큰 가져오기

1. 관리자 권한으로 PowerShell ISE를 시작 하 고 이동 합니다 **등록** 폴더에는 **azurestack의 경우 도구-마스터** 디렉터리를 만들 때 있습니다 [AzureStack도구다운로드](#bkmk_tools). 가져오기의 **RegisterWithAzure.psm1** 모듈:  

   ```powershell
   Import-Module .\RegisterWithAzure.psm1
   ```

2. 등록 토큰을 가져오려면 다음 PowerShell 명령을 실행 합니다.  

   ```Powershell
   $FilePathForRegistrationToken = $env:SystemDrive\RegistrationToken.txt
   $RegistrationToken = Get-AzsRegistrationToken -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel Capacity -AgreementNumber '<EA agreement number>' -TokenOutputFilePath $FilePathForRegistrationToken
   ```

   > [!TIP]  
   > 등록 토큰에 대 한 지정 된 파일에 저장 됩니다 *$FilePathForRegistrationToken*합니다. 파일 경로 또는 파일 이름을 임의로 변경할 수 있습니다.

3. Azure 사용에 대 한이 등록 토큰 저장 컴퓨터를 연결 합니다. $FilePathForRegistrationToken에서 텍스트 또는 파일을 복사할 수 있습니다.

### <a name="connect-to-azure-and-register"></a>등록 및 Azure에 연결

인터넷에 연결 된 된 컴퓨터에서 올바른 Azure Powershell 컨텍스트에 RegisterWithAzure.psm1 모듈 및 로그인을 가져오려면 동일한 단계를 수행 합니다. 그런 다음 등록 AzsEnvironment를 호출 하 고 Azure를 사용 하 여 등록 하려면 등록 토큰을 지정:

  ```Powershell  
  $registrationToken = "<Your Registration Token>"
  Register-AzsEnvironment -RegistrationToken $registrationToken  
  ```

필요에 따라 등록 토큰을 포함 하는 파일을 가리키도록 Get-content cmdlet을 사용할 수 있습니다.

  ```Powershell  
  $registrationToken = Get-Content -Path '<Path>\<Registration Token File>'
  Register-AzsEnvironment -RegistrationToken $registrationToken  
  ```

  > [!NOTE]  
  > 등록 리소스 이름 및 나중에 참조할 수에 대 한 등록 토큰을 저장 합니다.

### <a name="retrieve-an-activation-key-from-azure-registration-resource"></a>Azure 등록 리소스에서 활성화 키를 검색 합니다.

다음으로, AzsEnvironment 등록 중에 Azure에서 만든 등록 리소스에서 활성화 키를 검색 해야 합니다.

정품 인증 키를 가져오려면 다음 PowerShell 명령을 실행 합니다.  

  ```Powershell
  $RegistrationResourceName = "AzureStack-<Cloud Id for the Environment to register>"
  $KeyOutputFilePath = "$env:SystemDrive\ActivationKey.txt"
  $ActivationKey = Get-AzsActivationKey -RegistrationName $RegistrationResourceName -KeyOutputFilePath $KeyOutputFilePath
  ```

  > [!TIP]  
  > 정품 인증 키에 대 한 지정 된 파일에 저장 됩니다 *$KeyOutputFilePath*합니다. 파일 경로 또는 파일 이름을 임의로 변경할 수 있습니다.

### <a name="create-an-activation-resource-in-azure-stack"></a>Azure Stack에서 정품 인증 리소스 만들기

Get-AzsActivationKey에서 만든 정품 인증 키에서 파일 또는 텍스트를 사용 하 여 Azure Stack 환경에 반환 합니다. 그런 다음 해당 정품 인증 키를 사용 하 여 Azure Stack에서 정품 인증 리소스를 만들어집니다. 다음 PowerShell 명령을 실행 하는 정품 인증 리소스를 만들려면:  

  ```Powershell
  $ActivationKey = "<activation key>"
  New-AzsActivationResource -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -ActivationKey $ActivationKey
  ```

필요에 따라 등록 토큰을 포함 하는 파일을 가리키도록 Get-content cmdlet을 사용할 수 있습니다.

  ```Powershell
  $ActivationKey = Get-Content -Path '<Path>\<Activation Key File>'
  New-AzsActivationResource -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -ActivationKey $ActivationKey
  ```

## <a name="verify-azure-stack-registration"></a>Azure Stack 등록 확인

이러한 단계를 사용 하 여 Azure를 사용 하 여 Azure Stack이 성공적으로 등록 되었는지 확인 합니다.

1. Azure Stack에 로그인 [관리자 포털](https://docs.microsoft.com/azure/azure-stack/azure-stack-manage-portals#access-the-administrator-portal): https&#58;/ / adminportal. *&lt;지역 >. &lt;fqdn >* 합니다.
2. 선택 **더 많은 서비스** > **Marketplace Management** > **Azure에서 추가**합니다.

(예: WordPress)는 Azure에서 사용할 수 있는 항목의 목록을 표시를 활성화가 했습니다. 그러나 연결이 끊어진된 환경에서 표시 되지 않습니다 Azure Stack marketplace에서 Azure marketplace 항목.

> [!NOTE]
> 등록이 완료 되 면 등록 하지 활성 경고가 더 이상 표시 됩니다.

## <a name="renew-or-change-registration"></a>갱신 또는 등록 변경

### <a name="renew-or-change-registration-in-connected-environments"></a>갱신 또는 연결 된 환경에서 등록 변경

업데이트 하거나 다음과 같은 경우에서 등록을 갱신 해야 합니다.

- 후 용량 기반 연간 구독을 갱신 합니다.
- 청구 모델을 변경 하면 됩니다.
- 용량 기반 청구에 대 한 변경 내용 (추가/제거 노드)를 확장할 수 있습니다.

#### <a name="change-the-subscription-you-use"></a>사용 하는 구독 변경

사용할 구독을 변경 하려는 경우, 먼저 실행 해야 합니다 **제거 AzsRegistration** cmdlet을 다음 올바른 Azure PowerShell 컨텍스트에 로그인을 확인 하 고 마지막으로 실행 **집합 AzsRegistration**  하나를 사용 하 여 매개 변수를 변경 합니다.

  ```powershell
  Remove-AzsRegistration -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint
  Set-AzureRmContext -SubscriptionId $NewSubscriptionId
  Set-AzsRegistration -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel PayAsYouUse
  ```

#### <a name="change-the-billing-model-or-syndication-features"></a>청구 모델 또는 배포 기능 변경

청구 모델 또는 설치를 위한 배포 기능을 변경 하려는 경우에 새 값을 설정 하는 등록 함수를 호출할 수 있습니다. 현재 등록을 먼저 제거할 필요가 없습니다.

  ```powershell
  Set-AzsRegistration -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel PayAsYouUse
  ```

### <a name="renew-or-change-registration-in-disconnected-environments"></a>갱신 또는 연결이 끊어진된 환경에서 등록 변경

업데이트 하거나 다음과 같은 경우에서 등록을 갱신 해야 합니다.

- 후 용량 기반 연간 구독을 갱신 합니다.
- 청구 모델을 변경 하면 됩니다.
- 용량 기반 청구에 대 한 변경 내용 (추가/제거 노드)를 확장할 수 있습니다.

#### <a name="remove-the-activation-resource-from-azure-stack"></a>Azure Stack에서 정품 인증 리소스를 제거 합니다.

먼저 Azure Stack 및 Azure에서 등록 리소스에서 정품 인증 리소스를 제거 해야 합니다.  

Azure Stack에서 정품 인증 리소스를 제거 하려면 Azure Stack 환경에서 다음 PowerShell 명령을 실행 합니다.  

  ```Powershell
  Remove-AzsActivationResource -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint
  ```

다음으로 Azure에 등록 리소스를 제거 하려면 Azure 연결 인지를 확인 컴퓨터를 올바른 Azure PowerShell 컨텍스트에 로그인 하 고 아래에 설명 된 대로 적절 한 PowerShell 명령을 실행 합니다.

리소스를 만드는 데 등록 토큰을 사용할 수 있습니다.  

  ```Powershell
  $registrationToken = "<registration token>"
  Unregister-AzsEnvironment -RegistrationToken $registrationToken
  ```

또는 등록 이름을 사용할 수 있습니다.

  ```Powershell
  $registrationName = "AzureStack-<Cloud Id of Azure Stack Environment>"
  Unregister-AzsEnvironment -RegistrationName $registrationName
  ```

### <a name="re-register-using-disconnected-steps"></a>연결이 끊긴된 단계를 사용 하 여 다시 등록

연결이 끊긴된 시나리오에서 완전히 등록이 취소 이제을 연결이 끊긴된 시나리오에 Azure Stack 환경을 등록 하기 위한 단계를 반복 해야 합니다.

## <a name="next-steps"></a>다음 단계

[Azure에서 marketplace 항목 다운로드](azure-stack-download-azure-marketplace-item.md)
