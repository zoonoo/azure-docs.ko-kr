## 리소스 관리자 요청 인증 준비

Azure AD(Active Directory)에서 [Azure 리소스 관리자][lnk-authenticate-arm]를 사용하여 리소스에서 수행하는 모든 작업을 인증해야 합니다. 가장 쉽게 구성할 수 있는 방법은 PowerShell 또는 Azure CLI를 사용하는 것입니다.

[Azure PowerShell 1.0][lnk-powershell-install]을 설치하지 않은 경우 다음 PowerShell 명령을 사용하여 수행할 수 있습니다. 관리자 권한으로 PowerShell을 실행해야 합니다.

```
# Install the Azure Resource Manager modules from PowerShell Gallery
Install-Module AzureRM
Install-AzureRM

# Install the Azure Service Management module from PowerShell Gallery
Install-Module Azure
```

다음 단계는 PowerShell을 사용하여 AD 응용 프로그램에 대해 암호 인증을 설정하는 방법을 보여 줍니다. 표준 PowerShell 세션에서 이러한 명령을 실행할 수 있습니다.

1. 다음 명령을 사용하여 Azure 구독에 로그인합니다.

    ```
    Login-AzureRmAccount
    ```

2. **TenantId** 및 **SubscriptionId**를 적어 둡니다. 나중에 필요합니다.

3. 다음 명령을 사용하여 새 Azure Active Directory 응용 프로그램을 만듭니다. 자리 표시자는 바꿉니다.

    - **{표시 이름}:** **MySampleApp**과 같은 응용 프로그램의 표시 이름입니다.
    - **{홈페이지 URL}:** ****http://mysampleapp/home**과 같은 앱의 홈페이지의 URL입니다. 이 URL이 실제 응용 프로그램을 가리킬 필요는 없습니다.
- **{응용 프로그램 식별자}:** ****http://mysampleapp**과 같은 고유 식별자입니다. 이 URL이 실제 응용 프로그램을 가리킬 필요는 없습니다.
- **{암호}:** 앱에서 인증하기 위해 사용할 암호입니다.

    ```
    New-AzureRmADApplication -DisplayName {Display name} -HomePage {Home page URL} IdentifierUris {Application identifier} -Password {Password}
    ```
    
4. 만든 응용 프로그램의 **ApplicationId**를 적어 둡니다. 이 ID는 나중에 필요합니다.

5. 다음 명령을 사용하여 새 서비스 주체를 만듭니다. 이전 단계에서 **{MyApplicationId}**를 **ApplicationId**로 바꿉니다.

    ```
    New-AzureRmADServicePrincipal -ApplicationId {MyApplicationId}
    ```
    
6. 다음 명령을 사용하여 역할 할당을 설정합니다. **{MyApplicationId}**를 **ApplicationId**로 바꿉니다.

    ```
    New-AzureRmRoleAssignment -RoleDefinitionName Owner -ServicePrincipalName {MyApplicationId}
    ```
    
이제 사용자 지정 C# 응용 프로그램에서 인증할 수 있는 Azure AD 응용 프로그램을 만드는 작업을 완료했습니다. 이 자습서의 뒷부분에서 다음 값이 필요합니다.

- TenantId
- SubscriptionId
- ApplicationId
- 암호

[lnk-authenticate-arm]: https://msdn.microsoft.com/library/azure/dn790557.aspx
[lnk-powershell-install]: https://azure.microsoft.com/ko-KR/blog/azps-1-0-pre/

<!---HONumber=AcomDC_1203_2015-->