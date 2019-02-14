---
author: dominicbetts
ms.service: iot-hub
ms.topic: include
ms.date: 10/26/2018
ms.author: dobett
ms.openlocfilehash: e6ba2a9c27567ccbd3b744a9bfdc0603758c7068
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2019
ms.locfileid: "56213122"
---
## <a name="prepare-to-authenticate-azure-resource-manager-requests"></a>Azure Resource Manager 요청 인증 준비
Azure AD(Active Directory)에서 [Azure Resource Manager][lnk-authenticate-arm]를 사용하여 리소스에서 수행하는 모든 작업을 인증해야 합니다. 가장 쉽게 구성할 수 있는 방법은 PowerShell 또는 Azure CLI를 사용하는 것입니다.

계속하기 전에 [Azure PowerShell cmdlet][lnk-powershell-install]을 설치합니다.

다음 단계는 PowerShell을 사용하여 AD 애플리케이션에 대해 암호 인증을 설정하는 방법을 보여 줍니다. 표준 PowerShell 세션에서 이러한 명령을 실행할 수 있습니다.

1. 다음 명령을 사용하여 Azure 구독에 로그인합니다.

    ```powershell
    Connect-AzureRmAccount
    ```

1. Azure 구독이 여러 개 있는 경우 Azure에 로그인하면 자격 증명과 연결된 모든 Azure 구독에 액세스할 수 있습니다. 다음 명령을 사용하여 사용할 수 있는 Azure 구독을 나열합니다.

    ```powershell
    Get-AzureRMSubscription
    ```

    다음 명령을 사용하여 IoT Hub를 관리하는 명령을 실행하는 데 사용할 구독을 선택합니다. 이전 명령의 출력에서 구독 이름 또는 ID를 사용할 수 있습니다.

    ```powershell
    Select-AzureRMSubscription `
        -SubscriptionName "{your subscription name}"
    ```

2. **TenantId** 및 **SubscriptionId**를 적어둡니다. 나중에 필요합니다.
3. 다음 명령을 사용하여 새 Azure Active Directory 애플리케이션을 만듭니다. 자리 표시자는 바꿉니다.
   
   * **{표시 이름}:****MySampleApp**과 같은 애플리케이션의 표시 이름입니다.
   * **{홈페이지 URL}:** 앱의 홈페이지에 대한 URL입니다(예: **http://mysampleapp/home**). 이 URL이 실제 애플리케이션을 가리킬 필요는 없습니다.
   * **{애플리케이션 식별자}:** **http://mysampleapp**과 같은 고유 식별자입니다. 이 URL이 실제 애플리케이션을 가리킬 필요는 없습니다.
   * **{암호}:** 앱 인증에 사용하는 암호입니다.
     
     ```powershell
     $SecurePassword=ConvertTo-SecureString {password} –asplaintext –force
     New-AzureRmADApplication -DisplayName {Display name} -HomePage {Home page URL} -IdentifierUris {Application identifier} -Password $SecurePassword
     ```
4. 만든 애플리케이션의 **ApplicationId**를 적어 둡니다. 나중에 필요합니다.
5. 다음 명령을 사용하여 새 서비스 주체를 만듭니다. 이전 단계에서 **{MyApplicationId}** 를 **ApplicationId**로 바꿉니다.
   
    ```powershell
    New-AzureRmADServicePrincipal -ApplicationId {MyApplicationId}
    ```
6. 다음 명령을 사용하여 역할 할당을 설정합니다. **{MyApplicationId}** 를 **ApplicationId**로 바꿉니다.
   
    ```powershell
    New-AzureRmRoleAssignment -RoleDefinitionName Owner -ServicePrincipalName {MyApplicationId}
    ```

이제 사용자 지정 C# 애플리케이션에서 인증할 수 있는 Azure AD 애플리케이션을 만드는 작업을 완료했습니다. 이 자습서의 뒷부분에서 다음 값이 필요합니다.

* TenantId
* SubscriptionId
* ApplicationId
* 암호

[lnk-authenticate-arm]: https://msdn.microsoft.com/library/azure/dn790557.aspx
[lnk-powershell-install]: https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps
