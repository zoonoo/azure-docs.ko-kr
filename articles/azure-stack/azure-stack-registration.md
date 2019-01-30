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
ms.date: 01/16/2019
ms.author: jeffgilb
ms.reviewer: brbartle
ms.lastreviewed: 01/16/2019
ms.openlocfilehash: d9ab89afba2b83f99bfbf432d033cd0546a25a9d
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55247394"
---
# <a name="register-azure-stack-with-azure"></a>Azure를 사용 하 여 Azure Stack 등록

Azure를 사용 하 여 Azure Stack 등록 하면 Azure에서 마켓플레이스 항목을 다운로드 하 고 Microsoft에 다시 보고 하는 전자 상거래 데이터를 설정 합니다. Azure Stack 등록 한 후에 사용량 Azure 상거래에 보고 되 고 등록에 사용 되는 구독에서 볼 수 있습니다.

이 문서에서 설명 Azure 사용 하 여 Azure Stack 통합 시스템을 등록 합니다. ASDK Azure를 사용 하 여 등록 하는 방법에 대 한 내용은 [Azure Stack 등록](https://docs.microsoft.com/azure/azure-stack/asdk/asdk-register) ASDK 설명서에서.

> [!IMPORTANT]  
> Marketplace에서 항목을 제공 하는 포함 하 여 전체 Azure Stack 기능을 지원 하려면 등록이 필요 합니다. 또한 Azure Stack-수-종 청구 모델을 사용 하는 경우 등록 하지 않은 경우 사용 조건에 위반에서 됩니다. Azure Stack 라이선스 모델에 대 한 자세한 내용은 참조 하십시오 합니다 [구입 방법 페이지](https://azure.microsoft.com/overview/azure-stack/how-to-buy/)합니다.

## <a name="prerequisites"></a>필수 조건

등록 하기 전에 다음 진행에서 해야 합니다.

 - 자격 증명 확인
 - PowerShell 언어 모드를 설정 합니다.
 - Azure Stack 용 PowerShell 설치
 - Azure Stack 도구 다운로드
 - 등록 시나리오를 확인 합니다.

### <a name="verify-your-credentials"></a>자격 증명 확인

Azure Stack에 Azure를 등록 하기 전에 다음이 필요 합니다.

- Azure 구독에 대 한 구독 ID입니다. EA, CSP 또는 CSP 공유 서비스 구독 등록에 대해 지원 됩니다. Csp를 결정 해야 합니다. 것인지 [APSS 또는 CSP 구독을 사용 하 여](azure-stack-add-manage-billing-as-a-csp.md#create-a-csp-or-apss-subscription)입니다.<br><br>ID를 가져오려면 Azure에 로그인, 클릭 **모든 서비스**합니다. 그런 다음 합니다 **일반** 범주를 선택한 **구독**를 사용 하려는 구독을 클릭 및 아래 **Essentials** id입니다. 구독을 찾을 수 있습니다

  > [!Note]  
  > 독일 클라우드 구독은 현재 지원 되지 않습니다.

- 사용자 이름 및 구독에 대 한 소유자가 계정에 대 한 암호입니다.

- 사용자 계정에 Azure 구독에 액세스할 수 있으며 해당 구독과 연결 된 디렉터리에 id 응용 프로그램 및 서비스 주체를 만들 수 있는 권한이 필요 합니다. 권장 Azure를 사용 하 여 Azure Stack 등록 하는 최소 권한 관리를 사용 하 여 [등록에 사용할 서비스 계정을 만드는](azure-stack-registration-role.md) 전역 관리자 자격 증명을 사용 하는 대신 합니다.

- Azure Stack 리소스 공급자 등록 (세부 정보에 대 한 다음 Azure Stack 리소스 공급자 등록 섹션 참조).

등록이 완료 되 면 Azure Active Directory 전역 관리자 권한이 필요 하지 않습니다. 그러나 일부 작업에는 전역 관리자 자격 증명을 필요할 수 있습니다. 예를 들어, 리소스 공급자 설치 관리자 스크립트 또는 권한 부여 요청을 새로운 기능. 일시적으로 계정의 전역 관리자 권한이 다시 시작 하거나의 소유자가 별도 전역 관리자 계정을 사용 합니다 *공급자 구독을 기본*입니다.

이러한 요구 사항을 충족 하는 Azure 구독이 없으면 있습니다 [여기서 무료 Azure 계정을 만들](https://azure.microsoft.com/free/?b=17.06)합니다. Azure Stack 등록 비용 없이 Azure 구독에서 발생 합니다.

### <a name="powershell-language-mode"></a>PowerShell 언어 모드

Azure Stack을 성공적으로 등록 하려면 PowerShell 언어 모드 설정 해야 합니다 **FullLanguageMode**합니다.  전체 관리자 권한 PowerShell 창을 열고 다음 PowerShell cmdlet을 실행 하려면 현재 언어 모드를 설정 되어 있는지 확인 합니다.

```PowerShell  
$ExecutionContext.SessionState.LanguageMode
```

출력 반환 **FullLanguageMode**합니다. 다른 언어 모드 반환, 다른 컴퓨터에서 실행 해야 하는 등록 되거나 언어 모드 설정 해야 **FullLanguageMode** 계속 하기 전에 합니다.

### <a name="install-powershell-for-azure-stack"></a>Azure Stack 용 PowerShell 설치

Azure Stack에 대 한 최신 PowerShell을 사용 하 여 Azure를 사용 하 여 등록 합니다.

최신 버전이 아니라 이미 설치 되어 있지 않으면 참조 [Azure Stack 용 PowerShell 설치](https://docs.microsoft.com/azure/azure-stack/azure-stack-powershell-install)합니다.

### <a name="download-the-azure-stack-tools"></a>Azure Stack 도구 다운로드

Azure Stack 기능을 지 원하는 PowerShell 모듈을 포함 하는 Azure Stack 도구 GitHub 리포지토리 등록 기능을 포함 합니다. 가져오고 사용 해야 등록 과정에서 **RegisterWithAzure.psm1** PowerShell 모듈을 Azure를 사용 하 여 Azure Stack 인스턴스를 등록 하려면 Azure Stack 도구 리포지토리에서 찾을 수 있습니다.

을 보장 하기 위해 최신 버전을 사용 하는 모든 기존 버전의 Azure Stack 도구를 삭제 해야 하 고 [GitHub에서 최신 버전 다운로드](azure-stack-powershell-download.md) Azure를 사용 하 여 등록 하기 전에 합니다.

### <a name="determine-your-registration-scenario"></a>등록 시나리오를 확인 합니다.

Azure Stack 배포 될 수 있습니다 *연결 된* 하거나 *끊어진*합니다.

 - **연결됨**  
 인터넷 및 Azure에 연결할 수 있도록 Azure Stack을 배포한 의미를 연결 합니다. Azure Active Directory (Azure AD) 또는 Active Directory Federation Services (AD FS)에 id 저장소에 대 한 중 하나 있는 합니다. 연결 된 배포를 사용 하 여 두 가지 청구 모델에서 선택할 수 있습니다.-수-종 또는 용량 기반 합니다.
    - [연결 된 Azure Stack을 사용 하 여 Azure 등록 합니다 **-수-종** 청구 모델](#register-connected-with-pay-as-you-go-billing)
    - [연결 된 Azure Stack을 사용 하 여 Azure 등록 합니다 **용량** 청구 모델](#register-connected-with-capacity-billing)

 - **연결 끊김**  
 연결 되지 않은 사용 하 여 Azure 배포 옵션에서 배포를 인터넷에 연결 하지 않고 Azure Stack을 사용 합니다. 그러나 연결이 끊긴된 배포는 AD FS id 저장소 및 용량 기반 청구 모델에 제한 됩니다.
    - [사용 하 여 연결이 끊긴 Azure Stack 등록 합니다 **용량** 청구 모델 ](#register-disconnected-with-capacity-billing)

### <a name="determine-a-unique-registration-name-to-use"></a>사용 하는 경우 고유한 등록 이름 확인 
Azure를 사용 하 여 Azure Stack을 등록할 때 등록 고유 이름을 제공 해야 합니다. Azure 등록을 사용 하 여 Azure Stack 구독을 연결 하는 쉬운 방법을 Azure Stack을 사용 하는 것 **클라우드 ID**합니다. 

> [!NOTE]
> 용량 기반 청구 모델을 사용 하 여 azure Stack 등록 하지 않는 한 해당 하는 연간 구독이 만료 된 후 다시 등록 하는 경우 고유 이름을 변경 해야 하면 [만료 된 등록을 삭제](azure-stack-registration.md#change-the-subscription-you-use) 하 고 다시 등록 Azure입니다.

Azure Stack 배포를 위한 클라우드 ID를 확인 하려면 권한 있는 끝점에 액세스할 수 있습니다, 다음 명령을 실행 하는 컴퓨터에서 관리자 권한으로 PowerShell을 열고 하 고 기록 합니다 **CloudID** 값: 

```powershell
Run: Enter-PSSession -ComputerName <privileged endpoint computer name> -ConfigurationName PrivilegedEndpoint
Run: get-azurestackstampinformation 
```

## <a name="register-connected-with-pay-as-you-go-billing"></a>종 량 제 청구를 사용 하 여 연결 된 등록

이러한 단계를 사용 하 여-수-종 청구 모델을 사용 하 여 Azure를 사용 하 여 Azure Stack 등록 합니다.

> [!Note]  
> 이러한 모든 단계는 권한 있는 끝점 (PEP)에 대 한 액세스 권한이 있는 컴퓨터에서 실행 되어야 합니다. PEP에 대 한 자세한 내용은 참조 하세요 [권한 있는 끝점을 사용 하 여 Azure Stack에서](azure-stack-privileged-endpoint.md)합니다.

연결 된 환경 인터넷 및 Azure에 액세스할 수 있습니다. 이러한 환경에서는 Azure를 사용 하 여 Azure Stack 리소스 공급자를 등록 하 고 다음 청구 모델을 구성 해야 합니다.

1. Azure를 사용 하 여 Azure Stack 리소스 공급자를 등록 하려면 관리자 권한으로 PowerShell ISE를 시작 하 고 사용 하 여 다음 PowerShell cmdlet을 사용 합니다 **EnvironmentName** 적절 한 Azure 구독 유형 (참조로 매개 변수 설정 매개 변수 아래)입니다.

2. Azure Stack 등록을 사용 하는 Azure 계정을 추가 합니다. 실행 계정을 추가 합니다 **Add-azurermaccount** cmdlet. Azure 계정 자격 증명을 입력 하 라는 메시지가 표시 됩니다 하 고 계정 구성에 따라 2 단계 인증을 사용 해야 합니다.

   ```PowerShell
      Add-AzureRmAccount -EnvironmentName "<environment name>"
   ```

   | 매개 변수 | 설명 |  
   |-----|-----|
   | EnvironmentName | Azure 클라우드 구독 환경 이름입니다. 지원 되는 환경 이름은 **AzureCloud**를 **AzureUSGovernment**, 중국 Azure 구독을 사용 하는 경우 또는 **AzureChinaCloud**합니다.  |

3. 여러 구독이 있는 경우 사용 하려는 것을 선택 하려면 다음 명령을 실행 합니다.  

   ```PowerShell  
      Get-AzureRmSubscription -SubscriptionID '<Your Azure Subscription GUID>' | Select-AzureRmSubscription
   ```

4. Azure 구독에서 Azure Stack 리소스 공급자를 등록 하려면 다음 명령을 실행 합니다.

   ```PowerShell  
   Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack
   ```

5. 관리자 권한으로 PowerShell ISE를 시작 하 고 이동 합니다 **등록** 폴더에는 **azurestack의 경우 도구-마스터** 디렉터리를 만들 때 있습니다 [AzureStack도구다운로드](#bkmk_tools). 가져오기의 **RegisterWithAzure.psm1** PowerShell을 사용 하 여 모듈:

   ```PowerShell  
   Import-Module .\RegisterWithAzure.psm1
   ```

6. 다음으로, 동일한 PowerShell 세션에서 올바른 Azure PowerShell 컨텍스트에 로그인를 확인 합니다. 이 이전에 Azure Stack 리소스 공급자를 등록 하는 데 사용 된 Azure 계정입니다. Powershell 실행:

   ```PowerShell  
      Add-AzureRmAccount -EnvironmentName "<environment name>"
   ```

   | 매개 변수 | 설명 |  
   |-----|-----|
   | EnvironmentName | Azure 클라우드 구독 환경 이름입니다. 지원 되는 환경 이름은 **AzureCloud**를 **AzureUSGovernment**, 중국 Azure 구독을 사용 하는 경우 또는 **AzureChinaCloud**합니다.  |

7. 동일한 PowerShell 세션에서 실행 합니다 **집합 AzsRegistration** cmdlet. PowerShell 실행:  

   ```PowerShell  
   $CloudAdminCred = Get-Credential -UserName <Privileged endpoint credentials> -Message "Enter the cloud domain credentials to access the privileged endpoint."
   $RegistrationName = "<unique-registration-name>"
   Set-AzsRegistration `
      -PrivilegedEndpointCredential $CloudAdminCred `
      -PrivilegedEndpoint <PrivilegedEndPoint computer name> `
      -BillingModel PayAsYouUse `
      -RegistrationName $RegistrationName
   ```
   집합 AzsRegistration cmdlet에 대 한 자세한 내용은 참조 하세요. [등록 참조](#registration-reference)합니다.

  프로세스는 10 ~ 15 분이 걸립니다. 메시지를 참조 하 여 명령이 완료 되 면 **"환경의 이제 등록 되 고 제공된 된 매개 변수를 사용 하 여 활성화 합니다."**

## <a name="register-connected-with-capacity-billing"></a>용량 요금 청구를 사용 하 여 연결 된 등록

이러한 단계를 사용 하 여-수-종 청구 모델을 사용 하 여 Azure를 사용 하 여 Azure Stack 등록 합니다.

> [!Note]  
> 이러한 모든 단계는 권한 있는 끝점 (PEP)에 대 한 액세스 권한이 있는 컴퓨터에서 실행 되어야 합니다. PEP에 대 한 자세한 내용은 참조 하세요 [권한 있는 끝점을 사용 하 여 Azure Stack에서](azure-stack-privileged-endpoint.md)합니다.

연결 된 환경 인터넷 및 Azure에 액세스할 수 있습니다. 이러한 환경에서는 Azure를 사용 하 여 Azure Stack 리소스 공급자를 등록 하 고 다음 청구 모델을 구성 해야 합니다.

1. Azure를 사용 하 여 Azure Stack 리소스 공급자를 등록 하려면 관리자 권한으로 PowerShell ISE를 시작 하 고 사용 하 여 다음 PowerShell cmdlet을 사용 합니다 **EnvironmentName** 적절 한 Azure 구독 유형 (참조로 매개 변수 설정 매개 변수 아래)입니다.

2. Azure Stack 등록을 사용 하는 Azure 계정을 추가 합니다. 실행 계정을 추가 합니다 **Add-azurermaccount** cmdlet. Azure 계정 자격 증명을 입력 하 라는 메시지가 표시 됩니다 하 고 계정 구성에 따라 2 단계 인증을 사용 해야 합니다.

   ```PowerShell  
      Add-AzureRmAccount -EnvironmentName "<environment name>"
   ```

   | 매개 변수 | 설명 |  
   |-----|-----|
   | EnvironmentName | Azure 클라우드 구독 환경 이름입니다. 지원 되는 환경 이름은 **AzureCloud**를 **AzureUSGovernment**, 중국 Azure 구독을 사용 하는 경우 또는 **AzureChinaCloud**합니다.  |

3. 여러 구독이 있는 경우 사용 하려는 것을 선택 하려면 다음 명령을 실행 합니다.  

   ```PowerShell  
      Get-AzureRmSubscription -SubscriptionID '<Your Azure Subscription GUID>' | Select-AzureRmSubscription
   ```

4. Azure 구독에서 Azure Stack 리소스 공급자를 등록 하려면 다음 명령을 실행 합니다.

   ```PowerShell  
   Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack
   ```

5. 관리자 권한으로 PowerShell ISE를 시작 하 고 이동 합니다 **등록** 폴더에는 **azurestack의 경우 도구-마스터** 디렉터리를 만들 때 있습니다 [AzureStack도구다운로드](#bkmk_tools). 가져오기의 **RegisterWithAzure.psm1** PowerShell을 사용 하 여 모듈:

  ```PowerShell  
  $CloudAdminCred = Get-Credential -UserName <Privileged endpoint credentials> -Message "Enter the cloud domain credentials to access the privileged endpoint."
  $RegistrationName = "<unique-registration-name>"
  Set-AzsRegistration `
      -PrivilegedEndpointCredential $CloudAdminCred `
      -PrivilegedEndpoint <PrivilegedEndPoint computer name> `
      -AgreementNumber <EA agreement number> `
      -BillingModel Capacity `
      -RegistrationName $RegistrationName
  ```
   > [!Note]  
   > 사용 현황에 대 한 UsageReportingEnabled 매개 변수를 사용 하 여 보고를 비활성화할 수 있습니다 합니다 **집합 AzsRegistration** cmdlet 매개 변수를 false로 설정 합니다. 
   
  집합 AzsRegistration cmdlet에 대 한 자세한 내용은 참조 하세요. [등록 참조](#registration-reference)합니다.

## <a name="register-disconnected-with-capacity-billing"></a>용량 요금 청구를 사용 하 여 연결이 끊긴 등록

Azure Stack 환경에서 등록 토큰 가져오기 및 다음 해당 토큰을 사용 하 여 Azure에 연결할 수 있고 컴퓨터 해야 연결이 끊어진된 환경에서 Azure Stack 등록 하는 (사용 하 여 인터넷 연결 되지 않은 경우), 경우 [PowerShell 설치 된 Azure Stack 용](#bkmk_powershell)합니다.  

### <a name="get-a-registration-token-from-the-azure-stack-environment"></a>Azure Stack 환경에서 등록 토큰 가져오기

1. 관리자 권한으로 PowerShell ISE를 시작 하 고 이동 합니다 **등록** 폴더에는 **azurestack의 경우 도구-마스터** 디렉터리를 만들 때 있습니다 [AzureStack도구다운로드](#bkmk_tools). 가져오기의 **RegisterWithAzure.psm1** 모듈:  

   ```PowerShell  
   Import-Module .\RegisterWithAzure.psm1
   ```

2. 등록 토큰을 가져오려면 다음 PowerShell cmdlet을 실행 합니다.  

   ```Powershell
   $FilePathForRegistrationToken = $env:SystemDrive\RegistrationToken.txt
   $RegistrationToken = Get-AzsRegistrationToken -PrivilegedEndpointCredential $YourCloudAdminCredential -UsageReportingEnabled:$False -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel Capacity -AgreementNumber '<EA agreement number>' -TokenOutputFilePath $FilePathForRegistrationToken
   ```
   Get-AzsRegistrationToken cmdlet에 대 한 자세한 내용은 참조 하세요. [등록 참조](#registration-reference)합니다.

   > [!Tip]  
   > 등록 토큰에 대 한 지정 된 파일에 저장 됩니다 *$FilePathForRegistrationToken*합니다. 파일 경로 또는 파일 이름을 임의로 변경할 수 있습니다.

3. Azure 사용에 대 한이 등록 토큰 저장 컴퓨터를 연결 합니다. $FilePathForRegistrationToken에서 텍스트 또는 파일을 복사할 수 있습니다.

### <a name="connect-to-azure-and-register"></a>등록 및 Azure에 연결

인터넷에 연결 되어 있는 컴퓨터에서 RegisterWithAzure.psm1 모듈을 가져올 올바른 Azure Powershell 컨텍스트를 로그인 하는 동일한 단계를 수행 합니다. 그런 다음 등록 AzsEnvironment를 호출 합니다. Azure를 사용 하 여 등록 하려면 등록 토큰을 지정 합니다. 동일한 Azure 구독 ID를 사용 하 여 Azure Stack의 둘 이상의 인스턴스를 등록 하는 경우 고유한 등록 이름을 지정 합니다. 다음 cmdlet을 실행합니다.

  ```PowerShell  
  $registrationToken = "<Your Registration Token>"
  $RegistrationName = "<unique-registration-name>"
  Register-AzsEnvironment -RegistrationToken $registrationToken  -RegistrationName $RegistrationName
  ```

필요에 따라 등록 토큰을 포함 하는 파일을 가리키도록 Get-content cmdlet을 사용할 수 있습니다.

  ```PowerShell  
  $registrationToken = Get-Content -Path '<Path>\<Registration Token File>'
  Register-AzsEnvironment -RegistrationToken $registrationToken -RegistrationName $RegistrationName
  ```

  > [!Note]  
  > 등록 리소스 이름 및 나중에 참조할 수에 대 한 등록 토큰을 저장 합니다.

### <a name="retrieve-an-activation-key-from-azure-registration-resource"></a>Azure 등록 리소스에서 활성화 키를 검색 합니다.

다음으로, AzsEnvironment 등록 중에 Azure에서 만든 등록 리소스에서 활성화 키를 검색 해야 합니다.

정품 인증 키를 가져오려면 다음 PowerShell cmdlet을 실행 합니다.  

  ```Powershell
  $RegistrationResourceName = "<unique-registration-name>"
  $KeyOutputFilePath = "$env:SystemDrive\ActivationKey.txt"
  $ActivationKey = Get-AzsActivationKey -RegistrationName $RegistrationResourceName -KeyOutputFilePath $KeyOutputFilePath
  ```

  > [!Tip]  
  > 정품 인증 키에 대 한 지정 된 파일에 저장 됩니다 *$KeyOutputFilePath*합니다. 파일 경로 또는 파일 이름을 임의로 변경할 수 있습니다.

### <a name="create-an-activation-resource-in-azure-stack"></a>Azure Stack에서 정품 인증 리소스 만들기

Get-AzsActivationKey에서 만든 정품 인증 키에서 파일 또는 텍스트를 사용 하 여 Azure Stack 환경에 반환 합니다. 다음 해당 정품 인증 키를 사용 하 여 Azure Stack에서 정품 인증 리소스를 만듭니다. 정품 인증 리소스를 만들려면 다음 PowerShell cmdlet을 실행 합니다.  

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

사용할 수는 **지역 관리** 타일 Azure Stack 등록에 성공 했음을 확인 합니다. 이 타일은 관리자 포털에서 기본 대시보드에서 사용할 수 있습니다. 상태는 등록 또는 등록 되지 않은 수 있습니다. 등록 하는 경우 또한 보여줍니다 Azure 구독 ID를 하는 데 등록 리소스 그룹 및 이름과 함께 Azure Stack에 등록.

1. 에 로그인 합니다 [Azure Stack 관리 포털](https://adminportal.local.azurestack.external)합니다.

2. 대시보드에서 선택 **하위 지역 관리**합니다.

3. **속성**을 선택합니다. 이 블레이드는 상태 및 사용자 환경의 세부 정보를 보여 줍니다. 상태 수 있습니다 **Registered** 또는 **등록 되지 않은**합니다.

    [ ![지역 관리 타일](media/azure-stack-registration/admin1sm.png "지역 관리 타일") ](media/azure-stack-registration/admin1.png#lightbox)

    등록 하는 경우 속성을 다음과 같습니다.
    
    - **구독 ID 등록**: Azure 구독 ID를 등록 하 고 Azure Stack에 연결
    - **리소스 그룹 등록**: Azure Stack 리소스를 포함 하는 연결된 된 구독에서 Azure 리소스 그룹입니다.

4. Azure Stack 앱 등록을 보려면 Azure portal을 사용 합니다. Azure Stack 등록에 사용한 구독에 연결 된 계정을 사용 하 여 Azure portal에 로그인 합니다. Azure Stack과 사용 하 여 연결 하는 테 넌 트로 전환 합니다.
5. 이동할 **Azure Active Directory > 앱 등록 > 모든 응용 프로그램 보기**합니다.

    ![앱 등록](media/azure-stack-registration/app-registrations.png)

    Azure Stack 앱 등록 붙습니다 **Azure Stack**합니다.

또는 Marketplace 관리 기능을 사용 하 여 등록에 성공 했는지 여부를 확인할 수 있습니다. Marketplace 관리 블레이드에서 marketplace 항목 목록에 표시 되 면 등록에 성공 했습니다. 그러나 연결이 끊어진 환경에서 있습니다 됩니다 Marketplace 관리에서 마켓플레이스 항목을 볼 수 있습니다. 그러나 등록을 확인 하려면 오프 라인 도구를 사용할 수 있습니다.

> [!NOTE]
> 등록이 완료 되 면 등록 하지 활성 경고가 더 이상 표시 됩니다. 연결이 끊긴된 시나리오에서는 Marketplace 관리 성공적으로 등록 한 경우에 등록 및 Azure Stack에 활성화 하 라는 메시지가 표시 됩니다.

## <a name="renew-or-change-registration"></a>갱신 또는 등록 변경

### <a name="renew-or-change-registration-in-connected-environments"></a>갱신 또는 연결 된 환경에서 등록 변경

업데이트 하거나 다음과 같은 경우에서 등록을 갱신 해야 합니다.

- 후 용량 기반 연간 구독을 갱신 합니다.
- 청구 모델을 변경 하면 됩니다.
- 용량 기반 청구에 대 한 변경 내용 (추가/제거 노드)를 확장할 수 있습니다.

#### <a name="change-the-subscription-you-use"></a>사용 하는 구독 변경

사용할 구독을 변경 하려는 경우, 먼저 실행 해야 합니다 **제거 AzsRegistration** cmdlet을 다음 올바른 Azure PowerShell 컨텍스트에 로그인을 확인 하 고 마지막으로 실행 **집합 AzsRegistration**  비롯 한 모든 변경 된 매개 변수를 사용 하 여 \<청구 모델\>:

  ```PowerShell  
  Remove-AzsRegistration -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint
  Set-AzureRmContext -SubscriptionId $NewSubscriptionId
  Set-AzsRegistration -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel <billing model> -RegistrationName $RegistrationName
  ```

#### <a name="change-the-billing-model-or-how-to-offer-features"></a>청구 모델 또는 기능을 제공 하는 방법 변경

청구 모델 또는 설치를 위한 기능을 제공 하는 방법을 변경 하려는 경우에 새 값을 설정 하는 등록 함수를 호출할 수 있습니다. 현재 등록을 먼저 제거할 필요가 없습니다.

  ```PowerShell  
  Set-AzsRegistration -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel <billing model> -RegistrationName $RegistrationName
  ```

### <a name="renew-or-change-registration-in-disconnected-environments"></a>갱신 또는 연결이 끊어진된 환경에서 등록 변경

업데이트 하거나 다음과 같은 경우에서 등록을 갱신 해야 합니다.

- 후 용량 기반 연간 구독을 갱신 합니다.
- 청구 모델을 변경 하면 됩니다.
- 용량 기반 청구에 대 한 변경 내용 (추가/제거 노드)를 확장할 수 있습니다.

#### <a name="remove-the-activation-resource-from-azure-stack"></a>Azure Stack에서 정품 인증 리소스를 제거 합니다.

먼저 Azure Stack 및 Azure에서 등록 리소스에서 정품 인증 리소스를 제거 해야 합니다.  

Azure Stack에서 정품 인증 리소스를 제거 하려면 Azure Stack 환경에서 다음 PowerShell cmdlet을 실행 합니다.  

  ```Powershell
  Remove-AzsActivationResource -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint
  ```

다음으로 Azure에 등록 리소스를 제거 하려면 Azure 연결 인지를 확인 컴퓨터를 올바른 Azure PowerShell 컨텍스트에 로그인 하 고 아래 설명 된 대로 적절 한 PowerShell cmdlet을 실행 합니다.

리소스를 만드는 데 등록 토큰을 사용할 수 있습니다.  

  ```Powershell
  $registrationToken = "<registration token>"
  Unregister-AzsEnvironment -RegistrationToken $registrationToken
  ```

또는 등록 이름을 사용할 수 있습니다.

  ```Powershell
  $registrationName = "AzureStack-<unique-registration-name>"
  Unregister-AzsEnvironment -RegistrationName $registrationName
  ```

### <a name="re-register-using-disconnected-steps"></a>연결이 끊긴된 단계를 사용 하 여 다시 등록

연결이 끊긴된 시나리오에서 완전히 등록이 취소 이제을 연결이 끊긴된 시나리오에 Azure Stack 환경을 등록 하기 위한 단계를 반복 해야 합니다.

### <a name="disable-or-enable-usage-reporting"></a>또는 사용 현황 보고 사용 안 함

용량 요금 청구 모델을 사용 하는 Azure Stack 환경에 대 한 사용 보고 설정 합니다 **UsageReportingEnabled** 중 하나를 사용 하 여 매개 변수를 **집합 AzsRegistration** 또는  **Get-AzsRegistrationToken** cmdlet. Azure Stack 기본적으로 사용 메트릭을 보고합니다. 연산자를 사용 하 여 용량 또는 연결이 끊어진된 환경 지원 사용 보고를 해제 해야 합니다.

#### <a name="with-a-connected-azure-stack"></a>연결 된 Azure Stack을 사용 하 여

   ```PowerShell  
   $CloudAdminCred = Get-Credential -UserName <Privileged endpoint credentials> -Message "Enter the cloud domain credentials to access the privileged endpoint."
   $RegistrationName = "<unique-registration-name>"
   Set-AzsRegistration `
      -PrivilegedEndpointCredential $CloudAdminCred `
      -PrivilegedEndpoint <PrivilegedEndPoint computer name> `
      -BillingModel Capacity
      -RegistrationName $RegistrationName
   ```

#### <a name="with-a-disconnected-azure-stack"></a>연결이 끊긴된 Azure Stack을 사용 하 여

1. 등록 토큰을 변경 하려면 다음 PowerShell cmdlet을 실행 합니다.  

   ```Powershell
   $FilePathForRegistrationToken = $env:SystemDrive\RegistrationToken.txt
   $RegistrationToken = Get-AzsRegistrationToken -PrivilegedEndpointCredential -UsageReportingEnabled:$False
   $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel Capacity -AgreementNumber '<EA agreement number>' -TokenOutputFilePath $FilePathForRegistrationToken
   ```

   > [!Tip]  
   > 등록 토큰에 대 한 지정 된 파일에 저장 됩니다 *$FilePathForRegistrationToken*합니다. 파일 경로 또는 파일 이름을 임의로 변경할 수 있습니다.

2. Azure 사용에 대 한이 등록 토큰 저장 컴퓨터를 연결 합니다. $FilePathForRegistrationToken에서 텍스트 또는 파일을 복사할 수 있습니다.

## <a name="move-a-registration-resource"></a>등록 리소스 이동
동일한 구독에서 리소스 그룹을 사용 하 여 등록 리소스를 이동할 **는** 모든 환경에 대 한 지원. 그러나 등록 리소스를 구독 간에 이동 하는 경우에 지원 Csp에 대 한 동일한 파트너 id와 같습니다. 두 구독 모두 해결 새 리소스 그룹에 리소스를 이동 하는 방법에 대 한 자세한 내용은 참조 하세요. [새 리소스 그룹 또는 구독으로 리소스 이동](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)합니다.

## <a name="registration-reference"></a>등록 참조

### <a name="set-azsregistration"></a>Set-AzsRegistration

Azure를 사용 하 여 Azure Stack 등록 및 사용 또는 marketplace 및 사용 보고 있는 항목의 제품을 사용 하지 않도록 설정 하려면 AzsRegistration 집합을 사용할 수 있습니다.

Cmdlet을 실행 하려면 다음을 수행 해야 합니다.
- 모든 형식의 전역 Azure 구독입니다.
- 하면 로그인 해야 합니다도 Azure PowerShell에 소유자 또는 참가자 해당 구독에 있는 계정으로 합니다.

```PowerShell
    Set-AzsRegistration [-PrivilegedEndpointCredential] <PSCredential> [-PrivilegedEndpoint] <String> [[-AzureContext]
    <PSObject>] [[-ResourceGroupName] <String>] [[-ResourceGroupLocation] <String>] [[-BillingModel] <String>]
    [-MarketplaceSyndicationEnabled] [-UsageReportingEnabled] [[-AgreementNumber] <String>] [[-RegistrationName]
    <String>] [<CommonParameters>]
   ```

| 매개 변수 | 형식 | 설명 |
|-------------------------------|--------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| PrivilegedEndpointCredential | PSCredential | 에 사용 된 자격 증명 [끝점에 권한 있는 액세스](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint)합니다. 형식의 사용자 이름이 **AzureStackDomain\CloudAdmin**합니다. |
| PrivilegedEndpoint | 문자열 | 미리 구성 된 원격 PowerShell 콘솔을 제공 하는 로그 수집 및 기타 post와 같은 기능을 사용 하 여 배포 작업입니다. 자세한 내용은 참조는 [권한 있는 끝점을 사용 하 여](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint) 문서. |
| AzureContext | PSObject |  |
| ResourceGroupName | 문자열 |  |
| ResourceGroupLocation | 문자열 |  |
| BillingModel | 문자열 | 구독을 사용 하는 청구 모델입니다. 이 매개 변수에 대해 허용 된 값은: 용량, PayAsYouUse, 및 개발 합니다. |
| MarketplaceSyndicationEnabled | True/False | Marketplace 관리 기능은 포털에서 사용할 수 있는지 여부를 결정 합니다. 인터넷 연결을 사용 하 여 등록 하는 경우 true로 설정 합니다. 연결이 끊어진 환경에 등록 하는 경우 false로 설정 합니다. 연결이 끊긴 등록 합니다 [오프 라인 배포 도구](azure-stack-download-azure-marketplace-item.md#disconnected-or-a-partially-connected-scenario) marketplace 항목 다운로드에 사용할 수 있습니다. |
| UsageReportingEnabled | True/False | Azure Stack 기본적으로 사용 메트릭을 보고합니다. 연산자를 사용 하 여 용량 또는 연결이 끊어진된 환경 지원 사용 보고를 해제 해야 합니다. 이 매개 변수에 대해 허용 된 값은: True, False |
| AgreementNumber | 문자열 |  |
| RegistrationName | 문자열 | Id입니다. 동일한 Azure 구독을 사용 하 여 Azure Stack의 둘 이상의 인스턴스에서 등록 스크립트를 실행 하는 경우 등록에 대 한 고유 이름 설정 매개 변수 중에서 기본값이 **AzureStackRegistration**합니다. 그러나 Azure Stack의 둘 이상의 인스턴스에서 같은 이름을 사용할 경우 스크립트가 실패 합니다. |

### <a name="get-azsregistrationtoken"></a>Get-AzsRegistrationToken

Get-AzsRegistrationToken 입력된 매개 변수에서 등록 토큰을 생성합니다.

```PowerShell  
    Get-AzsRegistrationToken [-PrivilegedEndpointCredential] <PSCredential> [-PrivilegedEndpoint] <String>
    [-BillingModel] <String> [[-TokenOutputFilePath] <String>] [-UsageReportingEnabled] [[-AgreementNumber] <String>]
    [<CommonParameters>]
```
| 매개 변수 | 형식 | 설명 |
|-------------------------------|--------------|-------------|
| PrivilegedEndpointCredential | PSCredential | 에 사용 된 자격 증명 [끝점에 권한 있는 액세스](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint)합니다. 형식의 사용자 이름이 **AzureStackDomain\CloudAdmin**합니다. |
| PrivilegedEndpoint | 문자열 |  미리 구성 된 원격 PowerShell 콘솔을 제공 하는 로그 수집 및 기타 post와 같은 기능을 사용 하 여 배포 작업입니다. 자세한 내용은 참조는 [권한 있는 끝점을 사용 하 여](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint) 문서. |
| AzureContext | PSObject |  |
| ResourceGroupName | 문자열 |  |
| ResourceGroupLocation | 문자열 |  |
| BillingModel | 문자열 | 구독을 사용 하는 청구 모델입니다. 이 매개 변수에 대해 허용 된 값은: 용량, PayAsYouUse, 및 개발 합니다. |
| MarketplaceSyndicationEnabled | True/False |  |
| UsageReportingEnabled | True/False | Azure Stack 기본적으로 사용 메트릭을 보고합니다. 연산자를 사용 하 여 용량 또는 연결이 끊어진된 환경 지원 사용 보고를 해제 해야 합니다. 이 매개 변수에 대해 허용 된 값은: True, False |
| AgreementNumber | 문자열 |  |


## <a name="next-steps"></a>다음 단계

[Azure에서 marketplace 항목 다운로드](azure-stack-download-azure-marketplace-item.md)
