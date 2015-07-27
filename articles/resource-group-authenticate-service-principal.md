<properties
   pageTitle="Azure 리소스 관리자를 사용하여 서비스 사용자 인증"
   description="역할 기반 액세스 제어를 통해 서비스 사용자에게 액세스 권한을 부여하고 서비스 사용자를 인증하는 방법을 설명합니다. PowerShell 및 Azure CLI를 사용하여 이러한 작업을 수행하는 방법을 보여 줍니다."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="multiple"
   ms.workload="na"
   ms.date="07/15/2015"
   ms.author="tomfitz"/>

# Azure 리소스 관리자를 사용하여 서비스 사용자 인증

이 항목에서는 서비스 사용자(예: 자동화된 프로세스, 응용 프로그램 또는 서비스)에게 구독 내의 다른 리소스에 액세스하도록 허용하는 방법을 보여 줍니다. Azure 리소스 관리자에서 역할 기반 액세스 제어를 사용하여 서비스 사용자에게 허용된 작업을 수락하고 서비스 사용자를 인증할 수 있습니다. 이 항목에서는 PowerShell 및 Azure CLI를 사용하여 서비스 사용자에게 역할을 할당하고 서비스 사용자를 인증하는 방법을 보여 줍니다.


## 개념
1. AAD(Azure Active Directory) - 클라우드에 대한 ID 및 액세스 관리 서비스입니다. 자세한 내용은 [Azure Active Directory란](active-directory/active-directory-whatis.md)을 참조하세요.
2. 서비스 사용자 - 다른 리소스에 액세스해야 하는 디렉터리의 응용 프로그램 인스턴스입니다.
3. AD 응용 프로그램 - AAD에 응용 프로그램을 식별하는 디렉터리 레코드입니다. 자세한 내용은 [Azure AD의 인증 기본 사항](https://msdn.microsoft.com/library/azure/874839d9-6de6-43aa-9a5c-613b0c93247e#BKMK_Auth)을 참조하세요.

## PowerShell을 사용하여 서비스 사용자에게 액세스 권한 부여 및 인증

Azure PowerShell을 설치하지 않은 경우 [Azure PowerShell 설치 및 구성 방법](./powershell-install-configure.md)을 참조하세요.

먼저 서비스 사용자를 만듭니다. 이렇게 하려면 디렉터리에서 응용 프로그램 만들기를 사용해야 합니다. 이 섹션에서는 디렉터리에서 새 응용 프로그램을 만드는 방법을 안내합니다.

1. **New-AzureADApplication** 명령을 실행하여 새 AAD 응용 프로그램을 만듭니다. 응용 프로그램의 표시 이름, 응용 프로그램을 설명하는 페이지에 대한 링크(이 링크는 확인되지 않음), 응용 프로그램을 식별하는 URI, 응용 프로그램 ID에 대한 암호를 제공합니다.

        PS C:> $azureAdApplication = New-AzureADApplication -DisplayName "<Your Application Display Name>" -HomePage "<https://YourApplicationHomePage>" -IdentifierUris "<https://YouApplicationUri>" -Password "<Your_Password>"

     Azure AD 응용 프로그램이 반환됩니다. 서비스 사용자 만들기, 역할 할당 및 JWT 토큰 획득을 위해서는 **ApplicationId** 속성이 필요합니다. 출력을 저장하거나 출력을 변수로 캡처합니다.

        Type                    : Application
        ApplicationId           : a41acfda-d588-47c9-8166-d659a335a865
        ApplicationObjectId     : a26aaa48-bd52-4a7f-b29f-1bebf74c91e3
        AvailableToOtherTenants : False
        AppPermissions          : {{
                            "claimValue": "user_impersonation",
                            "description": "Allow the application to access My
                              Application on behalf of the signed-in user.",
                            "directAccessGrantTypes": [],
                            "displayName": "Access <<Your Application Display Name>>",
                            "impersonationAccessGrantTypes": [
                              {
                                "impersonated": "User",
                                "impersonator": "Application"
                              }
                            ],
                            "isDisabled": false,
                            "origin": "Application",
                            "permissionId":
                            "b866ef28-9abb-4698-8c8f-eb4328533831",
                            "resourceScopeType": "Personal",
                            "userConsentDescription": "Allow the application
                             to access <<Your Application Display Name>> on your behalf.",
                            "userConsentDisplayName": "Access <<Your Application Display Name>>",
                            "lang": null
                          }}


2. 응용 프로그램에 대한 서비스 사용자를 만듭니다.

        PS C:> New-AzureADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId

     이제 디렉터리에서 서비스 사용자를 만들었지만, 아직은 서비스에 할당된 권한 또는 범위가 없습니다. 서비스 사용자에게 일부 범위에서 작업을 수행할 수 있는 권한을 명시적으로 부여해야 합니다.

3. 서비스 사용자에게 구독에 대한 권한을 부여합니다. 이 샘플에서는 서비스 사용자에게 구독에서 모든 리소스를 읽을 수 있는 권한을 부여합니다. **ServicePrincipalName** 매개 변수의 경우 응용 프로그램을 만들 때 사용한 **ApplicationId** 또는 **IdentifierUris**를 제공합니다. 역할 기반 액세스 제어에 대한 자세한 내용은 [리소스에 대한 액세스 관리 및 감사](azure-portal/resource-group-rbac.md)를 참조하세요.

        PS C:> New-AzureRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $azureAdApplication.ApplicationId

4. 역할 할당을 만든 구독을 검색합니다. 이 구독은 나중에 서비스 사용자의 역할이 있는 테넌트의 **TenantId**를 가져오는 데 사용됩니다.

        PS C:> $subscription = Get-AzureSubscription | where { $_.IsCurrent }

     현재 선택된 구독이 아닌 다른 구독에서 역할 할당을 만든 경우 **SubscriptoinId** 또는 **SubscriptionName** 매개 변수를 지정하여 다른 구독을 검색할 수 있습니다.

5. **Get-Credential** 명령을 실행하여 자격 증명을 포함하는 새 **PSCredential** 개체를 만듭니다.

        PS C:> $creds = Get-Credential

     자격 증명을 입력하라는 메시지가 표시됩니다.

     ![][1]

     사용자 이름의 경우 응용 프로그램을 만들 때 사용한 **ApplicationId** 또는 **IdentifierUris**를 사용합니다. 암호의 경우 계정을 만들 때 지정한 암호를 사용합니다.

6. **Add-AzureAccount** cmdlet에 입력한 자격 증명을 사용하여 서비스 사용자를 로그인합니다.

        PS C:> Add-AzureAccount -Credential $creds -ServicePrincipal -Tenant $subscription.TenantId

     이제 사용자는 작성한 AAD 응용 프로그램에 대한 서비스 사용자로 인증됩니다.


## Azure CLI을 사용하여 서비스 사용자에게 액세스 권한 부여 및 인증

Mac, Linux 및 Windows용 Azure CLI를 설치하지 않은 경우 [Azure CLI 설치 및 구성](xplat-cli-install.md)을 참조하세요.

1. **Azure AD 앱 만들기** 명령을 실행하여 새 AAD 응용 프로그램을 만듭니다. 응용 프로그램의 표시 이름, 응용 프로그램을 설명하는 페이지에 대한 링크(이 링크는 확인되지 않음), 응용 프로그램을 식별하는 URI, 응용 프로그램 ID에 대한 암호를 제공합니다.

        azure ad app create --name "<Your Application Display Name>" --home-page "<https://YourApplicationHomePage>" --identifier-uris "<https://YouApplicationUri>" --password <Your_Password>
        
    Azure AD 응용 프로그램이 반환됩니다. 서비스 사용자 만들기, 역할 할당 및 JWT 토큰 획득을 위해서는 ApplicationId 속성이 필요합니다.

        info:    Executing command ad app create
        + Creating application exampleapp                                                
        data:    Application Id:          b57dd71d-036c-4840-865e-23b71d8098ec
        data:    Application Object Id:   d5c519e2-6149-447e-b323-88d2c4ea27de
        data:    Application Permissions:  
        data:                             claimValue:  user_impersonation
        data:                             description:  Allow the application to access exampleapp on behalf of the signed-in user.
        ...
        info:    ad app create command OK

2. 응용 프로그램에 대한 서비스 사용자를 만듭니다. 이전 단계에서 반환된 응용 프로그램 id를 제공합니다.

        azure ad sp create b57dd71d-036c-4840-865e-23b71d8098ec
        
    새 서비스 주체가 반환됩니다. 사용 권한을 부여할 때 개체 ID가 필요합니다.
    
        info:    Executing command ad sp create
        + Creating service principal for application b57dd71d-036c-4840-865e-23b71d8098ec
        data:    Object Id:               47193a0a-63e4-46bd-9bee-6a9f6f9c03cb
        data:    Display Name:            exampleapp
        ...
        info:    ad sp create command OK

    이제 디렉터리에서 서비스 사용자를 만들었지만, 아직은 서비스에 할당된 권한 또는 범위가 없습니다. 서비스 사용자에게 일부 범위에서 작업을 수행할 수 있는 권한을 명시적으로 부여해야 합니다.

3. 서비스 사용자에게 구독에 대한 권한을 부여합니다. 이 샘플에서는 서비스 사용자에게 구독에서 모든 리소스를 읽을 수 있는 권한을 부여합니다. **ServicePrincipalName** 매개 변수의 경우 응용 프로그램을 만들 때 사용한 **ApplicationId** 또는 **IdentifierUris**를 제공합니다. 역할 기반 액세스 제어에 대한 자세한 내용은 [리소스에 대한 액세스 관리 및 감사](azure-portal/resource-group-rbac.md)를 참조하세요.

        azure role assignment create --objectId 47193a0a-63e4-46bd-9bee-6a9f6f9c03cb -o Reader -c /subscriptions/{subscriptionId}/

4. 계정을 나열하고 출력에서 **TenantId**를 조사하여 서비스 사용자의 역할 할당이 있는 테넌트의 **TenantId** 결정합니다.

        azure account list

5. 서비스 사용자를 ID로 사용하여 로그인합니다. 사용자 이름의 경우 응용 프로그램을 만들 때 사용한 **ApplicationId**를 사용합니다. 암호의 경우 계정을 만들 때 지정한 암호를 사용합니다.

        azure login -u "<ApplicationId>" -p "<password>" --service-principal --tenant "<TenantId>"

    이제 사용자는 작성한 AAD 응용 프로그램에 대한 서비스 사용자로 인증됩니다.

## 다음 단계
시작하기

- [Azure 리소스 관리자 개요](./resource-group-overview.md)  
- [Azure 리소스 관리자로 Azure PowerShell 사용](./powershell-azure-resource-manager.md)
- [Azure 리소스 관리에서 Mac, Linux 및 Windows용 Azure CLI 사용](virtual-machines/xplat-cli-azure-resource-manager.md)  
- [Azure 포털을 사용하여 Azure 리소스 관리](azure-portal/resource-group-portal.md)

  
응용 프로그램 만들기 및 배포
  
- [Azure 리소스 관리자 템플릿 작성](./resource-group-authoring-templates.md)  
- [Azure 리소스 관리자 템플릿을 사용하여 응용 프로그램 배포](azure-portal/resource-group-template-deploy.md)  
- [Azure에서 리소스 그룹 배포 문제 해결](virtual-machines/resource-group-deploy-debug.md)  
- [Azure 리소스 관리자 템플릿 함수](./resource-group-template-functions.md)  
- [고급 템플릿 작업](./resource-group-advanced-template.md)  
- [.NET 라이브러리 및 템플릿을 사용하여 Azure 리소스 배포](virtual-machines/arm-template-deployment.md)
  
리소스 구성
  
- [태그를 사용하여 Azure 리소스 구성](./resource-group-using-tags.md)  
  
액세스 관리 및 감사
  
- [리소스에 대한 액세스 관리 및 감사](azure-portal/resource-group-rbac.md)  
- [Azure 포털을 사용하여 새 Azure 서비스 사용자 만들기](./resource-group-create-service-principal-portal.md)  
  


<!-- Images. -->
[1]: ./media/resource-group-authenticate-service-principal/arm-get-credential.png

<!---HONumber=July15_HO3-->