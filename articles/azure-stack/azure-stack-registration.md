---
title: Azure 스택에 대 한 azure 등록 통합 시스템 | Microsoft Docs
description: 다중 노드 배포 Azure 스택 Azure에 연결 된 Azure 등록 프로세스를 설명합니다.
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
ms.date: 03/26/2018
ms.author: jeffgilb
ms.reviewer: avishwan
ms.openlocfilehash: 1dc3d9a96b9b27927cc8cc66b5e80987fba4f8ea
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2018
---
# <a name="register-azure-stack-with-azure"></a>Azure 스택 Azure 등록
등록 [Azure 스택](azure-stack-poc.md) Azure 사용 하면 Azure에서 마켓플레이스 항목을 다운로드 하 고 상용 데이터를 Microsoft에 다시 보고를 설정 합니다. Azure 스택 등록 한 후에 사용 현황 Azure commerce에 보고 되 고 해당 등록에 사용 되는 구독에서 볼 수 있습니다. 

> [!IMPORTANT]
> 등록은 지불으로-있습니다-사용 요금 청구 모델을 선택 하는 경우에 필수입니다. 그렇지 않으면 있습니다 Azure 스택 배포의 라이선싱 조건 위반 하는 됩니다 사용 그렇지 않은 경우 보고 되지 것입니다.

## <a name="prerequisites"></a>필수 구성 요소
Azure 스택 Azure를 등록 하기 전에 다음이 필요 합니다.

- Azure 구독에 대 한 구독 ID입니다. ID를 가져오려면 Azure에 로그인을 클릭 **더 많은 서비스** > **구독**를 사용 하려는 구독을 클릭 하 고 아래에서 **Essentials** 찾을 수 있습니다는 구독 id입니다. 

  > [!NOTE]
  > 중국, 독일에 및 미국 정부 클라우드 구독 현재 지원 되지 않습니다. 

- 사용자 이름 및 구독에 대 한 소유자가 있는 계정에 대 한 암호 (MSA/2FA 계정이 지원 됩니다.)
- Azure 스택 리소스 공급자 등록 (자세한 내용은 아래의 Azure 스택 리소스 공급자 등록 섹션 참조).

이러한 요구 사항을 충족 하는 Azure 구독에 없으면 다음을 할 수 있습니다 [여기 무료 Azure 계정을 만들](https://azure.microsoft.com/free/?b=17.06)합니다. Azure 스택 등록 무료로 Azure 구독에서 발생 합니다.

### <a name="bkmk_powershell"></a>Azure 스택에 대 한 PowerShell을 설치 합니다.
Azure 스택에 대 한 최신 PowerShell을 사용 하 여 Azure에 등록 해야 합니다.

아직 설치 하지, [Azure 스택에 대 한 PowerShell을 설치](https://docs.microsoft.com/azure/azure-stack/azure-stack-powershell-install)합니다. 

### <a name="bkmk_tools"></a>Azure 스택 도구 다운로드
Azure 스택 도구 GitHub 리포지토리; Azure 스택 기능을 지 원하는 PowerShell 모듈이 포함 되어 있습니다. 등록 기능을 포함 합니다. 등록 하는 동안 가져오고 RegisterWithAzure.psm1 PowerShell 모듈을 사용 해야 합니다 중에 Azure를 사용한 Azure 스택 인스턴스를 등록 하려면 Azure 스택 도구 리포지토리에 발견 되었습니다. 

최신 버전을 사용 하려면 Azure 스택 도구의 기존 버전을 삭제 해야 하 고 [GitHub에서 최신 버전을 다운로드](azure-stack-powershell-download.md) Azure를 등록 하기 전에.

## <a name="register-azure-stack-in-connected-environments"></a>Azure 스택 연결 되는 환경에 등록
인터넷 및 Azure에 연결된 하는 환경 액세스할 수 있습니다. 이러한 환경에서는 Azure와 함께 Azure 스택 리소스 공급자를 등록 하 고 다음 청구 모델을 구성 해야 합니다.

> [!NOTE]
> 이러한 모든 단계는 권한 있는 끝점에 액세스할 수 있는 컴퓨터에서 실행 되어야 합니다. 

### <a name="register-the-azure-stack-resource-provider"></a>Azure 스택 리소스 공급자 등록
Azure 스택 리소스 공급자를 Azure에 등록 하려면 관리자 권한으로 Powershell ISE를 시작 하 고 다음 PowerShell 명령을 사용 합니다. 이러한 명령을 수행합니다.
- 사용 하 고 설정 하기 위해 Azure 구독의 소유자로 로그인 하 라는 메시지가 표시 된 **EnvironmentName** 매개 변수를 **azure 클라우드**합니다.
- Azure 리소스 공급자 등록 **Microsoft.AzureStack**합니다.

1. 사용 하면 Azure 스택 등록할 Azure 계정을 추가 합니다. 실행 계정을 추가 하는 **추가 AzureRmAccount** cmdlet. Azure 전역 관리자 계정 자격 증명을 입력 하는 메시지가 및 계정의 구성에 따라 2 단계 인증을 사용 하 여 할 수 있습니다.

   ```Powershell
      Add-AzureRmAccount -EnvironmentName AzureCloud
   ```

2. 여러 구독이 있는 경우 사용 하려는 목록을 선택 하려면 다음 명령을 실행 합니다.  

   ```powershell
      Get-AzureRmSubscription -SubscriptionID '<Your Azure Subscription GUID>' | Select-AzureRmSubscription
   ```

3. Azure 구독에서 Azure 스택 리소스 공급자를 등록 하려면 다음 명령을 실행 합니다.

   ```Powershell
   Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack
   ```

### <a name="register-azure-stack-with-azure-using-the-pay-as-you-use-billing-model"></a>Azure 스택은 사용량 기준 과금으로-있습니다-사용 가능한 요금 청구 모델을 사용 하 여 azure에 등록
Azure 스택은 사용량 기준 과금으로-있습니다-사용 가능한 요금 청구 모델을 사용 하 여 azure에 등록 하는 이러한 단계를 사용 합니다.

관리자 권한으로 PowerShell ISE를 시작 하 고 탐색 하 고 **등록** 폴더에는 **AzureStack 도구 마스터** 디렉터리를 만들 때 있습니다 [Azure스택도구다운로드](#bkmk_tools). 가져오기는 **RegisterWithAzure.psm1** PowerShell을 사용 하 여 모듈: 

PowerShell을 실행 하려면:

```powershell
Import-Module .\RegisterWithAzure.psm1
```
그런 다음, 동일한 PowerShell 세션에서 실행 된 **집합 AzsRegistration** cmdlet. 자격 증명에 대 한 메시지가 표시 되 면 Azure 구독의 소유자를 지정 합니다.  

PowerShell을 실행 하려면:

```powershell
$AzureContext = Get-AzureRmContext
$CloudAdminCred = Get-Credential -UserName <Privileged endpoint credentials>  -Message "Enter the credentials to access the privileged endpoint"
Set-AzsRegistration `
    -CloudAdminCredential $CloudAdminCred `
    -PrivilegedEndpoint <PrivilegedEndPoint computer name> `
    -BillingModel PayAsYouUse
```

|매개 변수|설명|
|-----|-----|
|CloudAdminCredential|권한 있는 끝점에 액세스 하는 데 자격 증명 정보 (사용자 이름 및 암호)를 포함 하는 PowerShell 개체입니다.|
|PrivilegedEndpoint|미리 구성 된 원격 PowerShell 콘솔을 제공 하는 로그 수집 및 기타 post와 같은 기능은 배포 작업입니다. 자세한 내용은를 참조는 [권한 있는 끝점을 사용 하 여](https://docs.microsoft.com/azure/azure-stack/azure-stack-privileged-endpoint#access-the-privileged-endpoint) 문서.|
|BillingModel|구독을 사용 하는 청구 모델입니다. 이 매개 변수 값은 허용: 용량, PayAsYouUse, 및 개발 합니다.|

### <a name="register-azure-stack-with-azure-using-the-capacity-billing-model"></a>Azure 스택 용량 청구 모델을 사용 하 여 azure에 등록
사용량 기준 과금-으로--사용 요금 청구 모델을 사용 하 여 등록에 사용 된 동일한 지침에 따라 하지만 용량 구매한 계약 번호를 추가 하 고, 변경 된 `BillingModel` 매개 변수를 **용량**합니다. 다른 모든 매개 변수가 변경 되지 않습니다.

PowerShell을 실행 하려면:
```powershell
$AzureContext = Get-AzureRmContext
$CloudAdminCred = Get-Credential -UserName <Privileged endpoint credentials>  -Message "Enter the credentials to access the privileged endpoint"
Set-AzsRegistration `
    -CloudAdminCredential $CloudAdminCred `
    -PrivilegedEndpoint <PrivilegedEndPoint computer name> `
    -AgreementNumber <EA agreement number> `
    -BillingModel Capacity
```

## <a name="register-azure-stack-in-disconnected-environments"></a>Azure 스택 연결이 끊어진된 환경에 등록 
*이 섹션의 정보는 Azure 스택 1712 업데이트 버전 (180106.1)로 시작을 적용 하 고 이전 버전에서는 지원 되지 않습니다.*

Azure 스택 환경에서 등록 하는 토큰을 가져올 다음 해당 토큰을 사용 하 여 Azure에 연결할 수 있으며 컴퓨터에서을 해야 연결이 끊어진된 환경에 Azure 스택 등록 하는 인터넷에 연결 되지) (있음, 경우 [PowerShell Azure 스택 설치에 대 한](https://docs.microsoft.com/azure/azure-stack/azure-stack-powershell-install)합니다.  

### <a name="get-a-registration-token-from-the-azure-stack-environment"></a>Azure 스택 환경에서 등록 하는 토큰 가져오기
  1. 등록 토큰을 가져오려면 다음 PowerShell 명령을 실행 합니다.  

     ```Powershell
        $FilePathForRegistrationToken = $env:SystemDrive\RegistrationToken.txt
        $RegistrationToken = Get-AzsRegistrationToken -CloudAdminCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel Capacity -AgreementNumber '<your agreement number>' -TokenOutputFilePath $FilePathForRegistrationToken
      ```
      > [!TIP]  
      > 등록 토큰에 대 한 지정 된 파일에 저장 된 *$env:SystemDrive\RegistrationToken.txt*합니다.

  2. Azure에서 사용 하기 위해이 등록 토큰이 저장 컴퓨터를 연결 합니다.


### <a name="connect-to-azure-and-register"></a>레지스터와 Azure에 연결
관리자 권한으로 PowerShell ISE를 시작 하 고 탐색 하 고 **등록** 폴더에는 **AzureStack 도구 마스터** 디렉터리를 만들 때 있습니다 [Azure스택도구다운로드](#bkmk_tools). 가져오기는 **RegisterWithAzure.psm1** 모듈: 

PowerShell을 실행 하려면:
```powershell
Import-Module .\RegisterWithAzure.psm1
```
다음으로, 동일한 PowerShell 세션에서 Azure에 등록할 등록 토큰을 지정:

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
> 등록 리소스 이름 또는 나중에 참조할 수에 대 한 등록 토큰을 저장 합니다.

## <a name="verify-azure-stack-registration"></a>Azure 스택 등록 확인
Azure 스택이 Azure에 성공적으로 등록 하 고 있는지 확인 하려면 다음이 단계를 사용 합니다.
1. Azure 스택에 로그인 [관리자 포털](https://docs.microsoft.com/azure/azure-stack/azure-stack-manage-portals#access-the-administrator-portal): https&#58;/ / adminportal. *&lt;지역 > 합니다. &lt;fqdn >*합니다.
2. 클릭 **더 많은 서비스** > **마켓플레이스 관리** > **Azure에서 추가**합니다.

(예: WordPress) Azure에서 사용할 수 있는 항목 목록이 표시 되 면 인증 과정에 성공 했습니다.

> [!NOTE]
> 등록이 완료 되 면 등록 하 고 있지에 대 한 활성 경고 더 이상 나타나지 않습니다.

## <a name="renew-or-change-registration"></a>등록을 변경 또는 갱신
업데이트 하거나 갱신 다음과 같은 경우에 등록 해야 합니다.
- 후 용량 기반 매년 구독을 갱신 합니다.
- 청구 모델을 변경 하면 됩니다.
- 용량 기반 요금 청구에 대 한 변경 (추가/제거 노드)를 확장할 수 있습니다.

### <a name="change-the-subscription-you-use"></a>사용 된 구독을 변경
사용 된 구독을 변경 하려는 경우, 먼저 실행 해야는 **제거 AzsRegistration** cmdlet을 다음 올바른 Azure PowerShell 컨텍스트를 로그인을 확인 하 고 마지막으로 실행 **집합 AzsRegistration**  함께 매개 변수를 변경 합니다.

```powershell
Remove-AzsRegistration -CloudAdminCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint
Set-AzureRmContext -SubscriptionId $NewSubscriptionId
Set-AzsRegistration -CloudAdminCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel PayAsYouUse
```

### <a name="change-the-billing-model-or-syndication-features"></a>청구 모델 또는 배포 기능 변경
청구 모델 또는 설치를 위한 배포 기능을 변경 하려는 경우 새 값을 설정 하는 등록 함수를 호출할 수 있습니다. 현재 등록을 먼저 제거할 필요가 없습니다. 

```powershell
Set-AzsRegistration -CloudAdminCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel PayAsYouUse
```
## <a name="next-steps"></a>다음 단계

[Azure에서 마켓플레이스 항목을 다운로드](azure-stack-download-azure-marketplace-item.md)