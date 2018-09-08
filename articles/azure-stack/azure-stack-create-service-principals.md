---
title: Azure Stack에 대 한 서비스 주체 만들기 | Microsoft Docs
description: 리소스에 대 한 액세스를 관리 하려면 Azure Resource Manager에서 역할 기반 액세스 제어를 사용 하 여 사용할 수 있는 새 서비스 주체를 만드는 방법을 설명 합니다.
services: azure-resource-manager
documentationcenter: na
author: sethmanheim
manager: femila
ms.assetid: 7068617b-ac5e-47b3-a1de-a18c918297b6
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/06/2018
ms.author: sethm
ms.openlocfilehash: 65fa9593b35af45ee9b8568bac5e4886909314e1
ms.sourcegitcommit: af60bd400e18fd4cf4965f90094e2411a22e1e77
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/07/2018
ms.locfileid: "44092547"
---
# <a name="provide-applications-access-to-azure-stack"></a>Azure Stack에 대한 액세스를 응용 프로그램에 제공합니다.

*적용 대상: Azure Stack 통합 시스템 및 Azure Stack 개발 키트*

응용 프로그램에서 배포 하거나 Azure Stack에서 Azure Resource Manager를 통해 리소스 구성에 대 한 액세스를 필요한 경우 응용 프로그램에 대 한 자격 증명 하는 서비스 주체를 만듭니다. 그런 다음 해당 서비스 주체에만 필요한 권한을 위임할 수 있습니다.  

예를 들어, Azure Resource Manager를 사용 하 여 Azure 리소스를 인벤토리 하는 구성 관리 도구를 할 수 있습니다. 이 시나리오에서는 서비스 주체 만들기, 해당 서비스 주체의 읽기 권한자 역할을 부여 및 구성 관리 도구 읽기 전용 액세스를 제한할 수 있습니다. 

서비스 주체는 때문에 사용자 고유의 자격 증명으로 앱을 실행 하는 것이 좋습니다.

* 서비스 계정 사용 권한과 다른 된 주체에 권한을 할당할 수 있습니다. 일반적으로 이러한 권한은 정확히 앱 실행에 필요한 것으로 제한됩니다.
* 책임이 변경되면 앱의 자격 증명을 변경할 필요가 없습니다.
* 무인 스크립트를 실행할 때 인증서를 사용하여 인증을 자동화할 수 있습니다.  

## <a name="getting-started"></a>시작

Azure Stack을 배포 방법에 따라 서비스 주체 만들기에서 시작 합니다. 이 문서에서는 둘 다에 대 한 서비스 주체를 만들면 [Azure Active Directory (Azure AD)](#create-service-principal-for-azure-ad) 하 고 [Active Directory 페더레이션 Services(AD FS)](#create-service-principal-for-ad-fs)합니다. AD FS와 Azure Active Directory에 공통 단계 집합이에 사용 되는 서비스 주체를 만든 후 [권한을 위임할](#assign-role-to-service-principal) 역할을 합니다.     

## <a name="create-service-principal-for-azure-ad"></a>Azure AD에 대 한 서비스 주체 만들기

Azure AD id 저장소로 사용 하 여 Azure Stack을 배포한 경우 Azure에 대해 수행 하는 것 처럼 서비스 주체를 만들 수 있습니다. 이 섹션에서는 포털을 통해 단계를 수행 하는 방법을 보여 줍니다. 있는지 확인 합니다 [필요한 Azure AD 권한](../azure-resource-manager/resource-group-create-service-principal-portal.md#required-permissions) 시작 하기 전에 합니다.

### <a name="create-service-principal"></a>서비스 주체 만들기
이 섹션에서는 응용 프로그램을 나타내는 Azure AD에서 응용 프로그램 (서비스 주체)를 만듭니다.

1. 통해 Azure 계정에 로그인 합니다 [Azure portal](https://portal.azure.com)합니다.
2. 선택 **Azure Active Directory** > **앱 등록** > **새 응용 프로그램 등록**   
3. 응용 프로그램에 대한 이름 및 URL을 제공합니다. 만들려는 응용 프로그램 유형으로 **웹앱/API** 또는 **네이티브**를 선택합니다. 값을 설정한 후 **만들기**를 선택합니다.

응용 프로그램에 대 한 서비스 주체를 만들었습니다.

### <a name="get-credentials"></a>자격 증명 가져오기
응용 프로그램 및 웹 앱에 대 한 ID를 사용 하 프로그래밍 방식으로 로그인 할 때 / API, 인증 키입니다. 이러한 값을 가져오려면 다음 단계를 사용합니다.

1. Active Directory의 **앱 등록**에서 응용 프로그램을 선택합니다.

2. **응용 프로그램 ID**를 복사하고 응용 프로그램 코드에 저장합니다. [샘플 응용 프로그램](#sample-applications) 섹션의 응용 프로그램은 이 값을 클라이언트 ID로 참조합니다.

     ![클라이언트 ID](./media/azure-stack-create-service-principal/image12.png)
3. 웹 앱에 대 한 인증 키를 생성할 / API를 선택 **설정을** > **키**합니다. 

4. 키에 대한 설명 및 키의 기간을 제공합니다. 완료되면 **저장**을 선택합니다.

키를 저장하면 키 값이 표시됩니다. 나중에 키를 검색할 수 없습니다 때문에 메모장 또는 다른 몇 가지 임시 위치에이 값을 복사 합니다. 응용 프로그램으로 로그인 하려면 응용 프로그램 ID를 사용 하 여 키 값을 제공 합니다. 응용 프로그램 검색할 수 있는 위치에서 키 값을 저장 합니다.

![공유 키](./media/azure-stack-create-service-principal/image15.png)

완료 되 면 진행 [응용 프로그램 역할 할당](#assign-role-to-service-principal)합니다.

## <a name="create-service-principal-for-ad-fs"></a>AD FS에 대 한 서비스 주체 만들기
AD FS 사용 하 여 Azure Stack을 배포한 경우에 서비스 주체 만들기, 액세스에 대 한 역할 할당 및 해당 id를 사용 하 여 PowerShell에서 로그인 하려면 PowerShell을 사용할 수 있습니다.

스크립트는 권한 있는 끝점에서 ERCS 가상 머신에서 실행 됩니다.

Requirements:
- 인증서가 필요 합니다.

#### <a name="parameters"></a>매개 변수

다음 정보를 반드시 automation 매개 변수에 대 한 입력으로:


|매개 변수|설명|예|
|---------|---------|---------|
|이름|SPN 계정의 이름|MyAPP|
|ClientCertificates|인증서 개체의 배열|X509 인증서|
|ClientRedirectUris<br>(선택 사항)|응용 프로그램 리디렉션 URI|-|

#### <a name="example"></a>예

1. 관리자 권한 Windows PowerShell 세션을 열고 다음 명령을 실행 합니다.

   > [!NOTE]
   > 이 예제에서는 자체 서명 된 인증서를 만듭니다. 이러한 명령은 프로덕션 배포에서를 실행 하면 사용 [Get-certificate](/powershell/module/pkiclient/get-certificate) 사용 하려는 인증서에 대 한 인증서 개체를 검색 합니다.

   ```PowerShell  
    # Credential for accessing the ERCS PrivilegedEndpoint, typically domain\cloudadmin
    $creds = Get-Credential

    # Creating a PSSession to the ERCS PrivilegedEndpoint
    $session = New-PSSession -ComputerName <ERCS IP> -ConfigurationName PrivilegedEndpoint -Credential $creds

    # This produces a self signed cert for testing purposes. It is prefered to use a managed certificate for this.
    $cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=<yourappname>" -KeySpec KeyExchange

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

2. 자동화에는 다음이 완료 되 면 SPN을 사용 하려면 필요한 세부 정보를 표시 합니다. 

   예: 

   ```shell
   ApplicationIdentifier : S-1-5-21-1512385356-3796245103-1243299919-1356
   ClientId              : 3c87e710-9f91-420b-b009-31fa9e430145
   Thumbprint            : 30202C11BE6864437B64CE36C8D988442082A0F1
   ApplicationName       : Azurestack-MyApp-c30febe7-1311-4fd8-9077-3d869db28342
   PSComputerName        : azs-ercs01
   RunspaceId            : a78c76bb-8cae-4db4-a45a-c1420613e01b
   ```

### <a name="assign-a-role"></a>역할 할당
서비스 주체를 만든 후 해야 [역할에 할당할](#assign-role-to-service-principal)합니다.

### <a name="sign-in-through-powershell"></a>PowerShell을 통해 로그인
역할을 할당 한 후 다음 명령을 사용 하 여 서비스 주체를 사용 하 여 Azure Stack에 로그인 할 수 있습니다.

```powershell
Add-AzureRmAccount -EnvironmentName "<AzureStackEnvironmentName>" `
 -ServicePrincipal `
 -CertificateThumbprint $servicePrincipal.Thumbprint `
 -ApplicationId $servicePrincipal.ClientId ` 
 -TenantId $directoryTenantId
```

## <a name="assign-role-to-service-principal"></a>서비스 주체에 역할 할당
구독의 리소스에 액세스하려면 역할에 응용 프로그램을 할당해야 합니다. 응용 프로그램에 적합한 사용 권한을 나타내는 역할을 결정합니다. 사용 가능한 역할에 대해 알아보려면 [RBAC: 기본 제공 역할](../role-based-access-control/built-in-roles.md)을 참조하세요.

구독, 리소스 그룹 또는 리소스 수준에서 범위를 설정할 수 있습니다. 권한은 하위 수준의 범위로 상속됩니다. 예를 들어 응용 프로그램에 리소스 그룹에 대한 읽기 권한자 역할을 추가하면 응용 프로그램이 리소스 그룹과 그 안에 포함된 모든 리소스를 읽을 수 있습니다.

1. Azure Stack 포털에서 응용 프로그램을 할당 하려는 범위 수준으로 이동 합니다. 예를 들어 구독 범위에서 역할을 할당하려면 **구독**을 선택합니다. 대신 리소스 그룹 또는 리소스를 선택할 수 있습니다.

2. 응용 프로그램을 할당할 특정 구독(리소스 그룹 또는 리소스)을 선택합니다.

     ![할당을 위한 구독 선택](./media/azure-stack-create-service-principal/image16.png)

3. **Access Control(IAM)** 을 선택합니다.

     ![액세스 선택](./media/azure-stack-create-service-principal/image17.png)

4. **추가**를 선택합니다.

5. 응용 프로그램에 할당할 역할을 선택합니다.

6. 응용 프로그램을 검색하고 선택합니다.

7. **확인**을 선택하여 역할 할당을 완료합니다. 목록에서 해당 범위에 대한 역할에 할당된 사용자 목록에 응용 프로그램이 표시될 것입니다.

서비스 주체를 생성 하 고 역할을 할당 하면, 했으므로를 사용 하 여이 응용 프로그램 내에서 Azure Stack 리소스에 액세스를 시작할 수 있습니다.  

## <a name="next-steps"></a>다음 단계

[ADFS에 대 한 사용자 추가](azure-stack-add-users-adfs.md)
[사용자 권한 관리](azure-stack-manage-permissions.md)
