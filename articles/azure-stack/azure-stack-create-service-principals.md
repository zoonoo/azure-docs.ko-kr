---
title: Azure Stack에 대 한 서비스 주체를 관리 합니다. | Microsoft Docs
description: 리소스에 대 한 액세스를 관리 하려면 Azure Resource Manager에서 역할 기반 액세스 제어를 사용 하 여 사용할 수 있는 새 서비스 주체를 관리 하는 방법에 설명 합니다.
services: azure-resource-manager
documentationcenter: na
author: sethmanheim
manager: femila
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/18/2018
ms.author: sethm
ms.openlocfilehash: 50ece9edbc4bee1dea2cc61f2cdd851b278aa7b0
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/21/2018
ms.locfileid: "53720444"
---
# <a name="provide-applications-access-to-azure-stack"></a>Azure Stack에 대한 액세스를 응용 프로그램에 제공합니다.

*적용 대상: Azure Stack 통합 시스템 및 Azure Stack 개발 키트*

응용 프로그램에서 배포 하거나 Azure Stack에서 Azure Resource Manager를 통해 리소스 구성에 대 한 액세스를 필요한 경우 응용 프로그램에 대 한 자격 증명 하는 서비스 주체를 만듭니다. 그런 다음 해당 서비스 주체에만 필요한 권한을 위임할 수 있습니다.  

예를 들어, Azure Resource Manager를 사용 하 여 Azure 리소스를 인벤토리 하는 구성 관리 도구를 할 수 있습니다. 이 시나리오에서는 서비스 주체 만들기, 해당 서비스 주체의 읽기 권한자 역할을 부여 및 구성 관리 도구 읽기 전용 액세스를 제한할 수 있습니다. 

서비스 주체는 때문에 사용자 고유의 자격 증명으로 앱을 실행 하는 것이 좋습니다.

 - 서비스 계정 사용 권한과 다른 된 주체에 권한을 할당할 수 있습니다. 일반적으로 이러한 권한은 정확히 앱 실행에 필요한 것으로 제한됩니다.
 - 책임이 변경되면 앱의 자격 증명을 변경할 필요가 없습니다.
 - 무인 스크립트를 실행할 때 인증서를 사용하여 인증을 자동화할 수 있습니다.  

## <a name="getting-started"></a>시작

Azure Stack을 배포 방법에 따라 서비스 주체 만들기에서 시작 합니다. 이 문서에서는 서비스 주체를 만들기를 설명 합니다.

- [Azure AD(Azure Active Directory)](#create-service-principal-for-azure-ad). Azure AD는 다중 테 넌 트, 클라우드 기반 디렉터리 및 id 관리 서비스는입니다. 연결 된 Azure Stack을 사용 하 여 Azure AD를 사용할 수 있습니다.
- [Active Directory Federation Services (AD FS)](#create-service-principal-for-ad-fs)합니다. AD FS는 간편 하 고 안전한 id 페더레이션 및 웹에서 single sign-on (SSO) 기능을 제공합니다. 연결 및 연결이 끊긴에 해당 하는 Azure Stack의 인스턴스를 사용 하 여 AD FS를 사용할 수 있습니다.

AD FS와 Azure Active Directory에 공통 단계 집합이에 사용 되는 서비스 주체를 만든 후 [권한을 위임할](#assign-role-to-service-principal) 역할을 합니다.

## <a name="manage-service-principal-for-azure-ad"></a>Azure AD에 대 한 서비스 주체 관리

사용자 id 관리 서비스와 Azure Active Directory (Azure AD)를 사용 하 여 Azure Stack을 배포한 경우 Azure에 대해 수행 하는 것 처럼 서비스 주체를 만들 수 있습니다. 이 섹션에서는 포털을 통해 단계를 수행 하는 방법을 보여 줍니다. 있는지 확인 합니다 [필요한 Azure AD 권한](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions) 시작 하기 전에 합니다.

### <a name="create-service-principal"></a>서비스 주체 만들기

이 섹션에서는 응용 프로그램을 나타내는 Azure AD에서 응용 프로그램 (서비스 주체)를 만듭니다.

1. [Azure Portal](https://portal.azure.com)을 통해 Azure 계정에 로그인합니다.
2. 선택 **Azure Active Directory** > **앱 등록** > **새 응용 프로그램 등록**
3. 애플리케이션에 대한 이름 및 URL을 제공합니다. 만들려는 애플리케이션 유형으로 **웹앱/API** 또는 **네이티브**를 선택합니다. 값을 설정한 후 **만들기**를 선택합니다.

응용 프로그램에 대 한 서비스 주체를 만들었습니다.

### <a name="get-credentials"></a>자격 증명 가져오기

응용 프로그램 및 웹 앱에 대 한 ID를 사용 하 프로그래밍 방식으로 로그인 할 때 / API, 인증 키입니다. 이러한 값을 가져오려면 다음 단계를 사용합니다.

1. Active Directory의 **앱 등록**에서 애플리케이션을 선택합니다.

2. **응용 프로그램 ID**를 복사하고 응용 프로그램 코드에 저장합니다. [샘플 응용 프로그램](#sample-applications) 섹션의 응용 프로그램은 이 값을 클라이언트 ID로 참조합니다.

     ![클라이언트 ID](./media/azure-stack-create-service-principal/image12.png)
3. 웹 앱에 대 한 인증 키를 생성할 / API를 선택 **설정을** > **키**합니다. 

4. 키에 대한 설명 및 키의 기간을 제공합니다. 완료되면 **저장**을 선택합니다.

키를 저장하면 키 값이 표시됩니다. 나중에 키를 검색할 수 없습니다 때문에 메모장 또는 다른 몇 가지 임시 위치에이 값을 복사 합니다. 응용 프로그램으로 로그인 하려면 응용 프로그램 ID를 사용 하 여 키 값을 제공 합니다. 응용 프로그램 검색할 수 있는 위치에서 키 값을 저장 합니다.

![공유 키](./media/azure-stack-create-service-principal/image15.png)

완료 되 면 있습니다 [응용 프로그램 역할 할당](#assign-role-to-service-principal)합니다.

## <a name="manage-service-principal-for-ad-fs"></a>AD FS에 대 한 서비스 주체를 관리 합니다.

사용자 id 관리 서비스와 Active Directory Federation Services (AD FS)를 사용 하 여 Azure Stack을 배포한 경우 서비스 주체 만들기, 액세스에 대 한 역할 할당 및 해당 id로 로그인 하려면 PowerShell을 사용 합니다.

AD FS를 사용 하 여 서비스 주체 만들기에 두 가지 방법 중 하나를 사용할 수 있습니다. 다음을 수행할 수 있습니다.
 - [인증서를 사용 하 여 서비스 주체 만들기](azure-stack-create-service-principals.md#create-a-service-principal-using-a-certificate)
 - [클라이언트 암호를 사용 하 여 서비스 주체 만들기](azure-stack-create-service-principals.md#create-a-service-principal-using-a-client-secret)

서비스 주체를 AD FS 관리를 위한 작업입니다.

| type | 조치 |
| --- | --- |
| AD FS 인증서 | [만들기](azure-stack-create-service-principals.md#create-a-service-principal-using-a-certificate) |
| AD FS 인증서 | [업데이트](azure-stack-create-service-principals.md#update-certificate-for-service-principal-for-AD-FS) |
| AD FS 인증서 | [제거](azure-stack-create-service-principals.md#remove-a-service-principal-for-AD-FS) |
| AD FS 클라이언트 암호 | [만들기](azure-stack-create-service-principals.md#create-a-service-principal-using-a-client-secret) |
| AD FS 클라이언트 암호 | [업데이트](azure-stack-create-service-principals.md#create-a-service-principal-using-a-client-secret) |
| AD FS 클라이언트 암호 | [제거](azure-stack-create-service-principals.md##remove-a-service-principal-for-AD-FS) |

### <a name="create-a-service-principal-using-a-certificate"></a>인증서를 사용 하 여 서비스 주체 만들기

Id에 대 한 AD FS를 사용 하는 동안 서비스 주체를 만들 때 인증서를 사용할 수 있습니다.

#### <a name="certificate"></a>인증서

인증서가 필요 합니다.

**인증서 요구 사항**

 - 암호화 서비스 공급자 (CSP)에 레거시 키 공급자 여야 합니다.
 - 인증서 형식 공개 및 개인 키가 필요한 처럼 PFX 파일 이어야 합니다. Windows 서버에는 공개 키 파일 (SSL 인증서 파일)를 포함 하는.pfx 파일과 연결 된 개인 키 파일을 사용 합니다.
 - 프로덕션 환경에 대 한 인증서는 내부 인증 기관 또는 공용 인증 기관에서 발급 되어야 합니다. 공용 인증 기관에서 사용 하는 경우 기관 Microsoft 신뢰할 수 있는 루트 인증 기관 프로그램의 일부로 기본 운영 체제 이미지에 포함 해야 합니다. 전체 목록을 찾을 수 있습니다 [Microsoft 신뢰할 수 있는 루트 인증서 프로그램: 참가자](https://gallery.technet.microsoft.com/Trusted-Root-Certificate-123665ca)합니다.
 - Azure Stack 인프라에는 인증서에 게시 하는 인증 기관의 인증서 해지 목록 (CRL) 위치에 네트워크 액세스를 권한이 있어야 합니다. 이 CRL은 HTTP 끝점 이어야 합니다.

#### <a name="parameters"></a>매개 변수

다음 정보를 반드시 automation 매개 변수에 대 한 입력으로:

|매개 변수|설명|예|
|---------|---------|---------|
|이름|SPN 계정의 이름|MyAPP|
|ClientCertificates|인증서 개체의 배열|X509 인증서|
|ClientRedirectUris<br>(선택 사항)|응용 프로그램 리디렉션 URI|-|

#### <a name="use-powershell-to-create-a-service-principal"></a>PowerShell을 사용 하 여 서비스 주체를 만들려면

1. 관리자 권한 Windows PowerShell 세션을 열고 다음 cmdlet을 실행 합니다.

   ```PowerShell  
    # Credential for accessing the ERCS PrivilegedEndpoint, typically domain\cloudadmin
    $creds = Get-Credential

    # Creating a PSSession to the ERCS PrivilegedEndpoint
    $session = New-PSSession -ComputerName <ERCS IP> -ConfigurationName PrivilegedEndpoint -Credential $creds

    # If you have a managed certificate use the Get-Item command to retrieve your certificate from your certificate location.
    # If you don't want to use a managed certificate, you can produce a self signed cert for testing purposes: 
    # $cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=<yourappname>" -KeySpec KeyExchange
    $cert = Get-Item "<yourcertificatelocation>"
    
    $ServicePrincipal = Invoke-Command -Session $session -ScriptBlock { New-GraphApplication -Name '<yourappname>' -ClientCertificates $using:cert}
    $AzureStackInfo = Invoke-Command -Session $session -ScriptBlock { get-azurestackstampinformation }
    $session|remove-pssession

    # For Azure Stack development kit, this value is set to https://management.local.azurestack.external. This is read from the AzureStackStampInformation output of the ERCS VM.
    $ArmEndpoint = $AzureStackInfo.TenantExternalEndpoints.TenantResourceManager

    # For Azure Stack development kit, this value is set to https://graph.local.azurestack.external/. This is read from the AzureStackStampInformation output of the ERCS VM.
    $GraphAudience = "https://graph." + $AzureStackInfo.ExternalDomainFQDN + "/"

    # TenantID for the stamp. This is read from the AzureStackStampInformation output of the ERCS VM.
    $TenantID = $AzureStackInfo.AADTenantID

    # Register an AzureRM environment that targets your Azure Stack instance
    Add-AzureRMEnvironment `
    -Name "AzureStackUser" `
    -ArmEndpoint $ArmEndpoint

    # Set the GraphEndpointResourceId value
    Set-AzureRmEnvironment `
    -Name "AzureStackUser" `
    -GraphAudience $GraphAudience `
    -EnableAdfsAuthentication:$true

    Add-AzureRmAccount -EnvironmentName "azurestackuser" `
    -ServicePrincipal `
    -CertificateThumbprint $ServicePrincipal.Thumbprint `
    -ApplicationId $ServicePrincipal.ClientId `
    -TenantId $TenantID

    # Output the SPN details
    $ServicePrincipal

   ```
   > [!Note]  
   > 유효성 검사를 사용 하 여 자체 서명 된 인증서를 만들 수 있습니다 용도로 아래 예제:

   ```PowerShell  
   $cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=<yourappname>" -KeySpec KeyExchange
   ```


2. 자동화에는 다음이 완료 되 면 SPN을 사용 하려면 필요한 세부 정보를 표시 합니다. 나중에 사용할 출력을 저장 하는 것이 좋습니다.

   예: 

   ```shell
   ApplicationIdentifier : S-1-5-21-1512385356-3796245103-1243299919-1356
   ClientId              : 3c87e710-9f91-420b-b009-31fa9e430145
   Thumbprint            : 30202C11BE6864437B64CE36C8D988442082A0F1
   ApplicationName       : Azurestack-MyApp-c30febe7-1311-4fd8-9077-3d869db28342
   PSComputerName        : azs-ercs01
   RunspaceId            : a78c76bb-8cae-4db4-a45a-c1420613e01b
   ```

### <a name="update-certificate-for-service-principal-for-ad-fs"></a>AD FS에 대 한 서비스 주체에 대 한 인증서를 업데이트 합니다.

AD FS 사용 하 여 Azure Stack을 배포한 경우에 서비스 주체에 대 한 암호를 업데이트 하려면 PowerShell을 사용할 수 있습니다.

스크립트는 권한 있는 끝점에서 ERCS 가상 머신에서 실행 됩니다.

#### <a name="parameters"></a>매개 변수

다음 정보를 반드시 automation 매개 변수에 대 한 입력으로:

|매개 변수|설명|예|
|---------|---------|---------|
|이름|SPN 계정의 이름|MyAPP|
|ApplicationIdentifier|고유 식별자|S-1-5-21-1634563105-1224503876-2692824315-2119|
|ClientCertificate|인증서 개체의 배열|X509 인증서|

#### <a name="example-of-updating-service-principal-for-ad-fs"></a>AD FS에 대 한 서비스 주체를 업데이트 하는 예제

이 예제에서는 자체 서명 된 인증서를 만듭니다. 프로덕션 배포에서 cmdlet을 실행 하는 경우 사용 하 여 [Get-item](https://docs.microsoft.com/powershell/module/Microsoft.PowerShell.Management/Get-Item) 사용 하려는 인증서에 대 한 인증서 개체를 검색 합니다.

1. 관리자 권한 Windows PowerShell 세션을 열고 다음 cmdlet을 실행 합니다.

     ```powershell
          # Creating a PSSession to the ERCS PrivilegedEndpoint
          $session = New-PSSession -ComputerName <ERCS IP> -ConfigurationName PrivilegedEndpoint -Credential $creds

          # This produces a self signed cert for testing purposes. It is preferred to use a managed certificate for this.
          $Newcert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=<yourappname>" -KeySpec KeyExchange

          $RemoveServicePrincipal = Invoke-Command -Session $session -ScriptBlock {Set-GraphApplication -ApplicationIdentifier  S-1-5-21-1634563105-1224503876-2692824315-2120 -ClientCertificates $Newcert}

          $session|remove-pssession
     ```

2. 자동화에는 다음이 완료 되 면 SPN 인증에 필요한 업데이트 된 지문 값을 표시 합니다.

     ```Shell  
          ClientId              : 
          Thumbprint            : AF22EE716909041055A01FE6C6F5C5CDE78948E9
          ApplicationName       : Azurestack-ThomasAPP-3e5dc4d2-d286-481c-89ba-57aa290a4818
          ClientSecret          : 
          RunspaceId            : a580f894-8f9b-40ee-aa10-77d4d142b4e5
     ```

### <a name="create-a-service-principal-using-a-client-secret"></a>클라이언트 암호를 사용 하 여 서비스 주체 만들기

Id에 대 한 AD FS를 사용 하는 동안 서비스 주체를 만들 때 인증서를 사용할 수 있습니다. Cmdlet을 실행 하려면 권한 있는 끝점을 사용 합니다.

이러한 스크립트는 권한 있는 끝점에서 ERCS 가상 머신에서 실행 됩니다. 권한 있는 끝점에 대 한 자세한 내용은 참조 하세요. [권한 있는 끝점을 사용 하 여 Azure Stack에서](https://docs.microsoft.com/azure/azure-stack/azure-stack-privileged-endpoint)합니다.

#### <a name="parameters"></a>매개 변수

다음 정보를 반드시 automation 매개 변수에 대 한 입력으로:

| 매개 변수 | 설명 | 예 |
|----------------------|--------------------------|---------|
| 이름 | SPN 계정의 이름 | MyAPP |
| GenerateClientSecret | 비밀 만들기 |  |

#### <a name="use-the-ercs-privilegedendpoint-to-create-the-service-principal"></a>ERCS PrivilegedEndpoint를 사용 하 여 서비스 주체 만들기

1. 관리자 권한 Windows PowerShell 세션을 열고 다음 cmdlet을 실행 합니다.

     ```PowerShell  
      # Credential for accessing the ERCS PrivilegedEndpoint, typically domain\cloudadmin
     $creds = Get-Credential

     # Creating a PSSession to the ERCS PrivilegedEndpoint
     $session = New-PSSession -ComputerName <ERCS IP> -ConfigurationName PrivilegedEndpoint -Credential $creds

     # Creating a SPN with a secre
     $ServicePrincipal = Invoke-Command -Session $session -ScriptBlock { New-GraphApplication -Name '<yourappname>' -GenerateClientSecret}
     $AzureStackInfo = Invoke-Command -Session $session -ScriptBlock { get-azurestackstampinformation }
     $session|remove-pssession

     # Output the SPN details
     $ServicePrincipal
     ```

2. Cmdlet 실행 후 셸에서 SPN을 사용 하려면 필요한 세부 정보를 표시 합니다. 클라이언트 비밀을 저장 했는지 확인 합니다.

     ```PowerShell  
     ApplicationIdentifier : S-1-5-21-1634563105-1224503876-2692824315-2623
     ClientId              : 8e0ffd12-26c8-4178-a74b-f26bd28db601
     Thumbprint            : 
     ApplicationName       : Azurestack-YourApp-6967581b-497e-4f5a-87b5-0c8d01a9f146
     ClientSecret          : 6RUZLRoBw3EebMDgaWGiowCkoko5_j_ujIPjA8dS
     PSComputerName        : 192.168.200.224
     RunspaceId            : 286daaa1-c9a6-4176-a1a8-03f543f90998
     ```

#### <a name="update-client-secret-for-a-service-principal-for-ad-fs"></a>AD FS에 대 한 서비스 주체에 대 한 클라이언트 비밀을 업데이트 합니다.

새 클라이언트 암호는 PowerShell cmdlet에서 자동으로 생성 합니다.

스크립트는 권한 있는 끝점에서 ERCS 가상 머신에서 실행 됩니다.

##### <a name="parameters"></a>매개 변수

다음 정보를 반드시 automation 매개 변수에 대 한 입력으로:

| 매개 변수 | 설명 | 예 |
|-----------------------|-----------------------------------------------------------------------------------------------------------|------------------------------------------------|
| ApplicationIdentifier | 고유 식별자입니다. | S-1-5-21-1634563105-1224503876-2692824315-2119 |
| ChangeClientSecret | 이전 암호가 여전히 유효한 2880 분 롤오버 기간을 사용 하 여 클라이언트 암호를 변경 합니다. |  |
| ResetClientSecret | 클라이언트 암호를 즉시 변경 |  |

##### <a name="example-of-updating-a-client-secret-for-ad-fs"></a>AD FS에 대 한 클라이언트 암호를 업데이트 하는 예제

이 예제에서는 사용 합니다 **resetclientsecret** 는 즉시 클라이언트 암호를 변경 하는 매개 변수입니다.

1. 관리자 권한 Windows PowerShell 세션을 열고 다음 cmdlet을 실행 합니다.

     ```PowerShell  
          # Creating a PSSession to the ERCS PrivilegedEndpoint
          $session = New-PSSession -ComputerName <ERCS IP> -ConfigurationName PrivilegedEndpoint -Credential $creds

          # This produces a self signed cert for testing purposes. It is preferred to use a managed certificate for this.
          $Newcert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=<yourappname>" -KeySpec KeyExchange

          $UpdateServicePrincipal = Invoke-Command -Session $session -ScriptBlock {Set-GraphApplication -ApplicationIdentifier  S-1-5-21-1634563105-1224503876-2692824315-2120 -ResetClientSecret}

          $session|remove-pssession
     ```

2. 자동화에는 다음이 완료 되 면 새로 생성 된 SPN 인증에 필요한 암호 표시 됩니다. 새 클라이언트 암호를 저장 해야 합니다.

     ```PowerShell  
          ApplicationIdentifier : S-1-5-21-1634563105-1224503876-2692824315-2120
          ClientId              :  
          Thumbprint            : 
          ApplicationName       : Azurestack-Yourapp-6967581b-497e-4f5a-87b5-0c8d01a9f146
          ClientSecret          : MKUNzeL6PwmlhWdHB59c25WDDZlJ1A6IWzwgv_Kn
          RunspaceId            : 6ed9f903-f1be-44e3-9fef-e7e0e3f48564
     ```

### <a name="remove-a-service-principal-for-ad-fs"></a>AD FS에 대 한 서비스 주체를 제거 합니다.

AD FS 사용 하 여 Azure Stack을 배포한 경우에 서비스 주체를 삭제 하려면 PowerShell을 사용할 수 있습니다.

스크립트는 권한 있는 끝점에서 ERCS 가상 머신에서 실행 됩니다.

#### <a name="parameters"></a>매개 변수

다음 정보를 반드시 automation 매개 변수에 대 한 입력으로:

|매개 변수|설명|예|
|---------|---------|---------|
| 매개 변수 | 설명 | 예 |
| ApplicationIdentifier | 고유 식별자 | S-1-5-21-1634563105-1224503876-2692824315-2119 |

> [!Note]  
> 모든 기존 서비스 주체 및 해당 응용 프로그램 식별자의 목록을 보려면 get graphapplication 명령을 사용할 수 있습니다.

#### <a name="example-of-removing-the-service-principal-for-ad-fs"></a>AD FS에 대 한 서비스 주체를 제거 하는 예제

```powershell  
     Credential for accessing the ERCS PrivilegedEndpoint, typically domain\cloudadmin
     $creds = Get-Credential

     # Creating a PSSession to the ERCS PrivilegedEndpoint
     $session = New-PSSession -ComputerName <ERCS IP> -ConfigurationName PrivilegedEndpoint -Credential $creds

     $UpdateServicePrincipal = Invoke-Command -Session $session -ScriptBlock { Remove-GraphApplication -ApplicationIdentifier S-1-5-21-1634563105-1224503876-2692824315-2119}

     $session|remove-pssession
```

## <a name="assign-a-role"></a>역할 할당

구독의 리소스에 액세스하려면 역할에 애플리케이션을 할당해야 합니다. 애플리케이션에 적합한 사용 권한을 나타내는 역할을 결정합니다. 사용 가능한 역할에 대해 알아보려면 [RBAC: 기본 제공 역할](../role-based-access-control/built-in-roles.md)을 참조하세요.

구독, 리소스 그룹 또는 리소스 수준에서 범위를 설정할 수 있습니다. 권한은 하위 수준의 범위로 상속됩니다. 예를 들어 애플리케이션에 리소스 그룹에 대한 읽기 권한자 역할을 추가하면 애플리케이션이 리소스 그룹과 그 안에 포함된 모든 리소스를 읽을 수 있습니다.

1. Azure Stack 포털에서 응용 프로그램을 할당 하려는 범위 수준으로 이동 합니다. 예를 들어 구독 범위에서 역할을 할당하려면 **구독**을 선택합니다. 대신 리소스 그룹 또는 리소스를 선택할 수 있습니다.

2. 애플리케이션을 할당할 특정 구독(리소스 그룹 또는 리소스)을 선택합니다.

     ![할당을 위한 구독 선택](./media/azure-stack-create-service-principal/image16.png)

3. **Access Control(IAM)** 을 선택합니다.

     ![액세스 선택](./media/azure-stack-create-service-principal/image17.png)

4. **역할 할당 추가**를 선택합니다.

5. 애플리케이션에 할당할 역할을 선택합니다.

6. 애플리케이션을 검색하고 선택합니다.

7. **확인**을 선택하여 역할 할당을 완료합니다. 목록에서 해당 범위에 대한 역할에 할당된 사용자 목록에 애플리케이션이 표시될 것입니다.

서비스 주체를 생성 하 고 역할을 할당 하면, 했으므로를 사용 하 여이 응용 프로그램 내에서 Azure Stack 리소스에 액세스를 시작할 수 있습니다.  

## <a name="next-steps"></a>다음 단계

[AD FS 사용자 추가](azure-stack-add-users-adfs.md)  
[사용자 권한 관리](azure-stack-manage-permissions.md)  
[Azure Active Directory 설명서](https://docs.microsoft.com/azure/active-directory)  
[ADFS(Active Directory Federation Services)](https://docs.microsoft.com/windows-server/identity/active-directory-federation-services)
