---
title: "Azure 스택 등록 | Microsoft Docs"
description: "Azure 스택 Azure 구독에 등록 합니다."
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 1/5/2018
ms.author: jeffgilb
ms.openlocfilehash: c2edafbf483692d5a11771268a1755c11b74521f
ms.sourcegitcommit: 1d423a8954731b0f318240f2fa0262934ff04bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/05/2018
---
# <a name="register-azure-stack-with-your-azure-subscription"></a>Azure 스택 Azure 구독에 등록

*적용 대상: Azure 스택 통합 시스템과 Azure 스택 개발 키트*

등록할 수 있는 [Azure 스택](azure-stack-poc.md) azure에서 Azure 마켓플레이스 항목을 다운로드 하 고 상용 데이터를 Microsoft에 다시 보고를 설정 하려면.

> [!NOTE]
>마켓플레이스 배포 및 사용 보고와 같은 중요 한 Azure 스택 기능을 테스트 하면 있기 때문에 등록을 사용 하는 것이 좋습니다. Azure 스택 등록 한 후 사용 Azure 상거래에 보고 됩니다. 등록에 사용 되는 구독에서 볼 수 있습니다. Azure 스택 개발 키트 사용자는 보고 사용에 대 한 요금이 청구 되지 않습니다.


## <a name="get-azure-subscription"></a>Azure 구독 가져오기

Azure 스택 Azure를 등록 하기 전에 다음이 필요 합니다.

- Azure 구독에 대 한 구독 ID입니다. ID를 가져오려면 Azure에 로그인을 클릭 **더 많은 서비스** > **구독**를 사용 하려는 구독을 클릭 하 고 아래에서 **Essentials** 찾을 수 있습니다는 **구독 ID**합니다. 중국, 독일에과 미국 정부 클라우드 구독 현재 지원 되지 않습니다.
- 사용자 이름 및 구독에 대 한 소유자가 있는 계정에 대 한 암호 (MSA/2FA 계정이 지원 됨).
- *Azure 스택 1712 업데이트 버전 (1.0.180103.2)로 시작 하는 필요 하지 않습니다:* Azure 구독에 대 한 Azure Active Directory 합니다. Azure 포털의 오른쪽 위 모서리에서 아바타 위로 이동 하 여 Azure에서이 디렉터리를 찾을 수 있습니다.

이러한 요구 사항을 충족 하는 Azure 구독에 없으면 다음을 할 수 있습니다 [여기 무료 Azure 계정을 만들](https://azure.microsoft.com/en-us/free/?b=17.06)합니다. Azure 스택 등록 무료로 Azure 구독에서 발생 합니다.

## <a name="register-azure-stack-with-azure"></a>Azure 스택 Azure 등록  
> [!NOTE]
> 이러한 모든 단계는 권한 있는 끝점에 액세스할 수 있는 컴퓨터에서 실행 되어야 합니다. Azure 스택 개발 키트에 대 한 호스트 컴퓨터 됩니다. 통합된 시스템을 사용 하는 경우 Azure 스택 운영자를 게 문의 하십시오.
>

1. 관리자 권한으로 PowerShell 콘솔을 열고 및 [Azure 스택에 대 한 PowerShell을 설치](azure-stack-powershell-install.md)합니다.  

2. 사용 하면 Azure 스택 등록할 Azure 계정을 추가 합니다. 실행 계정을 추가 하는 `Add-AzureRmAccount` 로 설정 EnvironmentName 매개 변수를 사용 하 여 cmdlet **azure 클라우드**합니다. Azure 계정 자격 증명을 입력 하는 메시지가 및 사용자 계정 구성에 따라 2 단계 인증을 사용 하 여 할 수 있습니다.

   ```Powershell
   Add-AzureRmAccount -EnvironmentName "AzureCloud"
   ```

3. 여러 구독이 있는 경우 사용 하려는 목록을 선택 하려면 다음 명령을 실행 합니다.  

   ```powershell
      Get-AzureRmSubscription -SubscriptionID '<Your Azure Subscription GUID>' | Select-AzureRmSubscription
   ```

4. Azure 구독에서 Azure 스택 리소스 공급자를 등록 하려면 다음 명령을 실행 합니다.

   ```Powershell
   Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack
   ```

5. 등록에 해당 하는 PowerShell 모듈의 기존 버전을 모두 삭제 하 고 [GitHub에서 최신 버전을 다운로드](azure-stack-powershell-download.md)합니다.  

6. 이전 단계에서 만든 "AzureStack 도구 마스터" 디렉터리에서 "등록" 폴더로 이동 하 고 ".\RegisterWithAzure.psm1" 모듈을 가져옵니다.  

   ```powershell
   Import-Module .\RegisterWithAzure.psm1
   ```

7. 동일한 PowerShell 세션에서 다음 스크립트를 실행: 자격 증명을 묻는 메시지가 나타나면 지정 `azurestack\cloudadmin` 사용자 및 암호 기능 중에 사용한 로컬 관리자에 대 한 배포 하는 것과 같습니다.  

   ```powershell
   $AzureContext = Get-AzureRmContext
   $CloudAdminCred = Get-Credential -UserName AZURESTACK\CloudAdmin -Message "Enter the cloud domain credentials to access the privileged endpoint"
   Set-AzsRegistration `
       -CloudAdminCredential $CloudAdminCred `
       -PrivilegedEndpoint AzS-ERCS01 `
       -BillingModel Development
   ```

   | 매개 변수 | 설명 |  
   |--------|-------------|
   | CloudAdminCredential | 하는 데 사용 되는 클라우드 도메인 자격 증명 [권한 있는 끝점에 액세스할](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint)합니다. 사용자 이름 형식으로 되어  **\<Azure 스택 도메인\>\cloudadmin**합니다. 로 설정 되어 사용자 이름을 개발 키트용 **azurestack\cloudadmin**합니다. 통합된 시스템을 사용 하는 경우이 값을 가져오기 귀하가 Azure 스택 운영자를 게 문의 하십시오.|  
   | PrivilegedEndpoint | 미리 구성 된 원격 PowerShell 콘솔을 제공 하는 로그 수집 및 기타 post와 같은 기능은 배포 작업입니다. 개발 키트에 대 한 권한 있는 끝점 "AzS ERCS01" 가상 컴퓨터에서 호스팅됩니다. 통합된 시스템을 사용 하는 경우이 값을 가져오기 귀하가 Azure 스택 운영자를 게 문의 하십시오. 자세한 내용은를 참조는 [권한 있는 끝점을 사용 하 여](azure-stack-privileged-endpoint.md) 문서.|  
   | BillingModel | 구독을 사용 하는 청구 모델입니다. 허용 되는 값이 매개 변수는- **용량**, **PayAsYouUse**, 및 **개발**합니다. 개발 키트에 대 한이 값은로 설정 **개발**합니다. 통합된 시스템을 사용 하는 경우이 값을 가져오기 귀하가 Azure 스택 운영자를 게 문의 하십시오. |

8. 스크립트가 완료 되는 메시지가 나타납니다. "활성 중 Azure 스택 (이 단계를 완료 하려면 최대 10 분까지 걸릴 수 있음)."




## <a name="verify-the-registration"></a>등록 확인  

1. 관리자 포털 (https://adminportal.local.azurestack.external)에 로그인 합니다.

2. 클릭 **더 많은 서비스** > **마켓플레이스 관리** > **Azure에서 추가**합니다.

3. (예: WordPress) Azure에서 사용할 수 있는 항목 목록이 표시 되 면 인증 과정에 성공 했습니다.

> [!NOTE]
> 등록이 완료 되 면 등록 하 고 있지에 대 한 활성 경고 더 이상 나타나지 않습니다.


## <a name="modify-the-registration"></a>등록 수정

### <a name="change-the-subscription-you-use"></a>사용 된 구독을 변경
사용 된 구독을 변경 하려는 경우 있습니다 먼저 제거 AzsRegistration 실행, 올바른 Azure PowerShell 컨텍스트를 로그인 다음을 실행 해야 집합 AzsRegistration 변경 된 모든 매개 변수를 사용 합니다.

  ```Powershell   
  Remove-AzsRegistration -CloudAdminCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint
  Set-AzureRmContext -SubscriptionId $NewSubscriptionId
  Set-AzsRegistration -CloudAdminCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel PayAsYouUse
  ```
### <a name="change-the-billing-model-or-syndication-features"></a>청구 모델 또는 배포 기능 변경
청구 모델 또는 설치를 위한 배포 기능을 변경 하려는 경우 새 값을 설정 하는 등록 함수를 호출할 수 있습니다. 현재 등록을 먼저 제거할 필요가 없습니다.  

  ```Powershell
     Set-AzsRegistration -CloudAdminCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel PayAsYouUse
  ```


## <a name="disconnected-registration"></a>연결이 끊긴된 등록
*이 섹션의 정보는 Azure 스택 1712 업데이트 버전 (1.0.180103.2)로 시작을 적용 하 고 이전 버전에서는 지원 되지 않습니다.*

연결이 끊어진된 환경에 Azure 스택을 등록 하는 경우 등록 하는 토큰 Azure 스택 환경에서 가져오고 다음 등록을 위해 Azure에 연결할 수 있는 컴퓨터에서 해당 토큰을 사용 해야 합니다.  

### <a name="get-a-registration-token-from-the-azure-stack-environment"></a>Azure 스택 환경에서 등록 하는 토큰 가져오기
  1. 등록 토큰을 가져오려면 다음을 실행 합니다.  

     ```Powershell
        $FilePathForRegistrationToken = $env:SystemDrive\RegistrationToken.txt
        $RegistrationToken = Get-AzsRegistrationToken -CloudAdminCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel Capacity -AgreementNumber '<your agreement number>' -TokenOutputFilePath $FilePathForRegistrationToken
      ```
      > [!TIP]  
      > 등록 토큰에 대 한 지정 된 파일에 저장 된 *$env:SystemDrive\RegistrationToken.txt*합니다.

  2. Azure에서 사용 하기 위해이 등록 토큰이 저장 컴퓨터를 연결 합니다.


### <a name="connect-to-azure-and-register"></a>레지스터와 Azure에 연결
Azure에 연결할 수 있는 컴퓨터에서이 절차에 대 한 단계를 실행 합니다.

  1. [GitHub에서 등록에 해당 하는 최신 PowerShell 모듈을 다운로드](azure-stack-powershell-download.md)합니다.  

  2. 이전 단계에서 만든 "AzureStack 도구 마스터" 디렉터리에서 "등록" 폴더로 이동 하 고 ".\RegisterWithAzure.psm1" 모듈을 가져옵니다.  

     ```powershell
     Import-Module .\RegisterWithAzure.psm1
     ```

  3. 복사 [RegisterWithAzure.psm1](https://go.microsoft.com/fwlink/?linkid=842959) 를 폴더와 같은 *C:\Temp*합니다.

  4. 관리자 권한으로 PowerShell ISE를 시작 하 고 RegisterWithAzure 모듈을 가져옵니다.  

  5. Azure 스택 환경을 등록 하는 데 사용할 올바른 Azure PowerShell 컨텍스트 로그인을 확인 합니다.  

     ```Powershell
        Set-AzureRmContext -SubscriptionId $YourAzureSubscriptionId   
     ```
  6. Azure를 등록 하려면 등록 토큰을 지정 합니다.

     ```Powershell  
       $registrationToken = "*Your Registration Token*"
       Register-AzsEnvironment -RegistrationToken $registrationToken  
     ```
    필요에 따라 등록 토큰을 포함 하는 파일을 가리키도록 Get-content cmdlet을 사용할 수 있습니다.

    ```Powershell  
       $registrationToken = Get-Content -Path 'C:\Temp\<Registration Token File>'
       Register-AzsEnvironment -RegistrationToken $registrationToken  
    ```
> [!NOTE]  
> 등록 리소스 이름 또는 나중에 참조할 수에 대 한 등록 토큰을 저장 합니다.

### <a name="remove-a-registered-resource"></a>등록 된 리소스를 제거 합니다.
등록 리소스를 제거 하려는 경우 다음 AzsEnvironment 등록 취소를 사용 하며 등록 리소스 이름 또는 레지스터 AzsEnvironment에 사용 등록 토큰을 전달 합니다.
- **등록 리소스 이름:**

  ```Powershell    
     UnRegister-AzsEnvironment -RegistrationName "*Name of the registration resource*"
  ```
- **등록 토큰:**    

  ```Powershell
     $registrationToken = "*Your copied registration token*"
     UnRegister-AzsEnvironment -RegistrationToken $registrationToken
   ```


## <a name="next-steps"></a>다음 단계
[Azure Stack에 연결](azure-stack-connect-azure-stack.md)
