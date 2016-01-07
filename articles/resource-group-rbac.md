<properties
   pageTitle="리소스에 대한 액세스 관리"
   description="역할 기반 액세스 제어(RBAC)를 사용하여 Azure에 배포된 리소스의 사용자 권한을 관리합니다."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="AzurePortal"
   ms.workload="na"
   ms.date="11/28/2015"
   ms.author="tomfitz"/>

# 리소스에 대한 액세스 관리

Azure 리소스 관리자를 사용하여 해당 조직의 사용자가 리소스 관리 또는 액세스에 적합한 권한을 갖게 할 수 있습니다. 리소스 관리자는 사용자가 개별 리소스나 리소스 그룹에 쉽게 보안 정책을 적용할 수 있도록 역할 기반 액세스 제어(RBAC)를 활용합니다. 예를 들어 사용자에게 구독 중인 특정 가상 컴퓨터에 대한 액세스를 부여하거나, 다른 리소스를 제외한 구독 중인 모든 웹 사이트 관리 기능을 제공할 수 있습니다.

이 항목에서는 역할 및 사용 권한을 할당하기 위해 사용하는 명령을 중점적으로 설명합니다. 역할 기반 액세스 제어에 대한 개요는 [Microsoft Azure 포털에서 역할 기반 액세스 제어](active-directory/role-based-access-control-configure.md)를 참조하세요.

## 개념

이해를 위한 역할 기반 액세스 제어의 몇 가지 핵심 개념은 다음과 같습니다.

1. 보안 주체 - 엔터티 부여 권한(예: Azure Active Directory 사용자, 보안 그룹 또는 응용 프로그램)
2. 역할 - 허용된 작업 및 제외된 작업의 집합
3. 작업 - 리소스에 대해 수행된 작업(예: 읽기, 만들기) 
4. 범위 - 역할 적용 수준(예: 구독, 리소스 그룹 또는 리소스)
5. 역할 할당 - 역할에 보안 주체를 추가하고 범위를 설정하는 프로세스

Azure 플랫폼 간(xPlat) CLI 도구 또는 Azure PowerShell 모듈을 사용하여 지원되는 **작업** 목록을 가져올 수 있습니다.

Azure 플랫폼 간 CLI 도구의 경우 다음 명령을 사용하여 모든 리소스 공급자에 대한 **모든** 작업을 나열합니다.

    azure provider operations show --operationSearchString '*';

Azure 리소스 관리자 PowerShell 모듈(AzureRm)을 사용하는 경우 다음 명령을 사용하여 **모든** 리소스 공급자에 대한 **모든** 작업을 나열합니다.

    Get-AzureRmProviderOperation -OperationSearchString *;

## 역할 예
RBAC 개념을 이해하기 위해 몇 가지 일반적인 역할 정의에 대해 살펴보겠습니다.

| 역할 | 허용되는 작업 |
| ------- | ----------------- |
| 읽기 권한자 | **/read (모두 읽기) |
| 소유자 | * (모두 읽기/쓰기) |

다음 구독에서 이름이 **ExampleGroup**인 리소스 그룹에 **읽기 권한자** 역할을 할당하고 **사용자 A**와 **사용자 B**에게는 **소유자** 역할을 할당하려면 다음과 같이 지정하게 됩니다.

| 역할 | 주체 | 범위 |
| --------|-------------|---------- |
| 읽기 권한자 | 사용자 A | /subscriptions/{subscriptionId}/resourceGroups/examplegroup |
| 소유자 | 사용자 B | /subscriptions/{subscriptionId} |

## 예 시나리오

이 항목에서는 Azure PowerShell, Mac/Linux/Windows용 Azure CLI, REST API를 통해 다음과 같은 일반적인 시나리오를 수행하는 방법에 대해 살펴봅니다.

1. 구독에서 사용 가능한 역할 및 해당 역할에 허용되는 작업을 확인합니다.
2. 구독에서 그룹의 구성원에 읽기 권한자 권한을 부여합니다.
3. 응용 프로그램이 리소스 그룹 내 리소스를 관리할 수 있도록 응용 프로그램에 참가자 권한을 부여합니다.
4. 특정 웹 사이트에 대한 소유자 권한을 단일 사용자에게 부여합니다.
5. 리소스 그룹의 감사 로그 목록을 표시합니다.


## PowerShell을 사용한 액세스 관리 방법

[AZURE.INCLUDE [powershell-preview-inline-include](../includes/powershell-preview-inline-include.md)]


### 사용 가능한 역할 보기
구독에 사용 가능한 모든 역할을 보려면 **Get-AzureRmRoleDefinition** 명령을 실행합니다.

    PS C:\> Get-AzureRmRoleDefinition
    
    Name             : API Management Service Contributor
    Id               : /subscriptions/{subscription-id}/providers/Microsoft.Authorization/roleDefinitions/{guid}
    IsCustom         : False
    Description      : Lets you manage API Management services, but not access to them.
    Actions          : {Microsoft.ApiManagement/Services/*, Microsoft.Authorization/*/read,
                       Microsoft.Resources/subscriptions/resourceGroups/read,
                       Microsoft.Resources/subscriptions/resourceGroups/resources/read...}
    NotActions       : {}
    AssignableScopes : {/}

    Name             : Application Insights Component Contributor
    Id               : /subscriptions/{subscription-id}/providers/Microsoft.Authorization/roleDefinitions/{guid}
    IsCustom         : False
    Description      : Lets you manage Application Insights components, but not access to them.
    Actions          : {Microsoft.Insights/components/*, Microsoft.Insights/webtests/*, Microsoft.Authorization/*/read,
                       Microsoft.Resources/subscriptions/resourceGroups/read...}
    NotActions       : {}
    AssignableScopes : {/}
    ...

### 구독에 대해 그룹에 읽기 권한자 권한을 부여합니다.
1. **Get-AzureRmRoleDefinition** 명령을 실행할 때 역할 이름을 입력하여 **읽기 권한자** 역할 정의를 검토합니다. 할당하려는 작업이 허용되는 작업인지 확인합니다.

        PS C:\> Get-AzureRmRoleDefinition Reader
   
        Name             : Reader
        Id               : /subscriptions/{subscription-id}/providers/Microsoft.Authorization/roleDefinitions/{guid}
        IsCustom         : False
        Description      : Lets you view everything, but not make any changes.
        Actions          : {*/read}
        NotActions       : {}
        AssignableScopes : {/}

2. **Get-AzureRmADGroup** 명령을 실행하여 필요한 보안 그룹을 가져옵니다. 구독에서 그룹의 실제 이름을 제공합니다. ExampleAuditorGroup은 아래와 같습니다.

        PS C:\> $group = Get-AzureRmAdGroup -SearchString ExampleAuditorGroup

3. 감사자 보안 그룹에 대한 역할 할당을 만듭니다. 명령이 완료되면 새 역할 할당이 반환됩니다.

        PS C:\> New-AzureRmRoleAssignment -ObjectId $group.Id -Scope /subscriptions/{subscriptionId}/ -RoleDefinitionName Reader


###리소스 그룹에 대해 응용 프로그램에 참가자 권한을 부여합니다.
1. **Get-AzureRmRoleDefinition** 명령을 실행할 때 역할 이름을 입력하여 **참가자** 역할 정의를 검토합니다. 할당하려는 작업이 허용되는 작업인지 확인합니다.

        PS C:\> Get-AzureRmRoleDefinition Contributor

2. **Get-AzureRmADServicePrincipal** 명령을 실행하고 구독에서 응용 프로그램 이름을 제공하여 서비스 주체 개체 ID를 가져옵니다. ExampleApplication은 아래와 같습니다.

        PS C:\> $service = Get-AzureRmADServicePrincipal -SearchString ExampleApplicationName

3. **New-AzureRmRoleAssignment** 명령을 실행하여 서비스 주체에 대한 역할 할당을 만듭니다.

        PS C:\> New-AzureRmRoleAssignment -ObjectId $service.Id -ResourceGroupName ExampleGroupName -RoleDefinitionName Contributor

Azure Active Directory 응용 프로그램 및 서비스 주체 설정에 대한 자세한 설명은 [Azure 리소스 관리자를 사용하여 서비스 주체 인증](resource-group-authenticate-service-principal.md)을 참조하십시오.

###리소스에 대해 사용자에게 소유자 권한을 부여합니다.
1. **Get-AzureRmRoleDefinition** 명령을 실행할 때 역할 이름을 입력하여 **소유자** 역할 정의를 검토합니다. 할당하려는 작업이 허용되는 작업인지 확인합니다.

        PS C:\> Get-AzureRmRoleDefinition Owner

2. 사용자에 대한 역할 할당을 만듭니다.

        PS C:\> New-AzureRmRoleAssignment -UserPrincipalName "someone@example.com" -ResourceGroupName {groupName} -ResourceType "Microsoft.Web/sites" -ResourceName "mysite" -RoleDefinitionName Owner


###리소스 그룹의 감사 로그 목록을 표시합니다.
리소스 그룹에 대한 감사 로그를 가져오려면 **Get-AzureRmLog** 명령(또는 Azure PowerShell 1.0 Preview 이전 버전의 경우 **Get-AzureResourceGroupLog**)을 실행합니다.

      PS C:\> Get-AzureRmLog -ResourceGroup ExampleGroupName

## Mac, Linux 및 Windows용 Azure CLI를 사용하는 방법

Mac, Linux 및 Windows용 Azure CLI를 설치하지 않았거나 Azure CLI에 사용할 조직 계정을 구성하지 않은 경우 [Azure CLI 설치 및 구성](xplat-cli-install.md)을 참조하십시오.

1. 자격 증명으로 Azure 계정에 로그인합니다. 이 명령은 사용자의 로그인 결과를 반환합니다.

        azure login

        ...
        info:    login command OK

2. 여러 구독이 있는 경우 배포에 사용할 구독 ID를 제공합니다.

        azure account set <YourSubscriptionNameOrId>

3. Azure 리소스 관리자 모듈로 전환합니다. 새 모드에 대한 확인이 제공됩니다.

        azure config mode arm
        
        info:     New mode is arm

### 사용 가능한 역할 보기
구독에 대해 사용 가능한 모든 역할을 봅니다. 역할 정의 목록을 반환합니다.

    azure role list

### 구독에 대해 그룹에 읽기 권한자 권한을 부여합니다.
1. **읽기 권한자** 역할의 역할 정의를 가져옵니다. 할당하려는 작업이 허용되는 작업인지 확인합니다.

        azure role show Reader
        
        info:    Executing command role show
        + Getting role definitions
        data:    Name    Actions  NotActions
        data:    ------  -------  ----------
        data:    Reader  */read
        info:    role show command OK

2. 이름을 기준으로 그룹 검색을 통해 필요한 보안 그룹과 objectId를 가져옵니다. ExampleAuditorGroup은 다음 예제에 표시됩니다.

        azure ad group show --search ExampleAuditorGroup
        
        info:    Executing command ad group show
        + Getting group list
        data:    Display Name:      ExampleAuditorGroup
        data:    ObjectId:          1c272299-9729-462a-8d52-7efe5ece0c5c
        data:    Security Enabled:  true
        data:    Mail Enabled:
        data:
        info:    ad group show command OK

3. 보안 그룹에 대한 역할 할당을 만듭니다.

        azure role assignment create --objectId {group-object-id} -o Reader -c /subscriptions/{subscriptionId}/
        
        info:    Executing command role assignment create
        + Getting role definition id
        + Creating role assignment
        info:    role assignment create command OK


### 리소스 그룹에 대해 응용 프로그램에 참가자 권한을 부여합니다.
1. **참가자** 역할의 역할 정의를 가져옵니다. 할당하려는 작업이 허용되는 작업인지 확인합니다.

        azure role show Contributor

2. 응용 프로그램의 ObjectId를 가져옵니다. 검색할 응용 프로그램의 이름을 입력합니다.

        azure ad sp show --search ExampleApplicationName

2. 응용 프로그램에 대한 역할 할당을 만듭니다.

        azure role assignment create --ObjectId {service-principal-object-id} -o Contributor -c /subscriptions/{subscriptionId}/


###특정 웹 사이트에 대해 사용자에게 소유자 권한을 부여합니다.
1. **소유자** 역할의 역할 정의를 가져옵니다. 할당하려는 작업이 허용되는 작업인지 확인합니다.

        azure role show Owner

2. 사용자에 대한 역할 정의를 만듭니다.

        azure role assignment create --mail "someone@example.com" -o Owner -c /subscriptions/{subscriptionId}/resourceGroups/{groupName}/providers/Microsoft.Web/sites/{mySiteName}


###리소스 그룹의 감사 로그 목록을 표시합니다.
리소스 그룹에 대한 감사 로그를 가져오려면 **azure group log show** 명령을 실행하고 원하는 리소스 그룹 이름을 제공합니다.

         azure group log show ExampleGroupName


## REST API 사용 방법
Azure 리소스 관리자 REST API를 통한 역할 기반 액세스 제어를 관리하려면 요청을 보낼 때 명령 헤더와 매개 변수(인증 토큰 포함)를 설정해야 합니다. 정보는 [공통 매개 변수 및 헤더](https://msdn.microsoft.com/library/azure/dn906885.aspx)를 참조하십시오.
   
지원되는 api-versions를 확인하려면 다음을 실행합니다.

      GET https://management.azure.com/providers/Microsoft.Authorization?api-version=2015-01-01

이 항목에는 `2014-10-01-preview` 버전을 사용할 수 있습니다.

###역할 할당 만들기
사용 가능한 역할 정의를 가져옵니다.

    GET https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Authorization/roleDefinitions?api-version=2014-10-01-preview

역할 할당을 만듭니다.

    PUT https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Authorization/roleAssignments/{role-assignment-id}?api-version=2014-10-01-preview
    {
      "properties": {
          "roleDefinitionId": "/subscriptions/{subscription-id}/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
          "principalId": "{principal-object-id}",
          "scope": "/subscriptions/{subscription-id}"
       }
    }


###역할 할당 삭제

      Delete  https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Authorization/roleAssignments/{role-assignment-id}?api-version=2014-10-01-preview


## 다음 단계

- 역할 기반 액세스 제어에 대한 자세한 내용은 [Microsoft Azure 포털에서 역할 기반 액세스 제어](role-based-access-control-configure.md)를 참조하세요.
- 구독에서 응용 프로그램에 대한 액세스를 관리하는 데 필요한 서비스 주체 작업과 관련된 자세한 내용은 [Azure 리소스 관리자를 사용하여 서비스 주체 인증](resource-group-authenticate-service-principal.md) 및 [Azure 클래식 포털을 사용하여 새 Azure 서비스 사용자 만들기](../resource-group-create-service-principal-portal.md)를 참조하세요.
- 조직에서 작업을 감사하는 방법에 대한 자세한 내용은 [리소스 관리자로 작업 감사](resource-group-audit.md)를 참조하세요.
- 사용자 지정된 정책을 사용하여 구독을 통해 제한 사항 및 규칙을 적용할 수 있습니다. 자세한 내용은 [정책을 사용하여 리소스 및 컨트롤 액세스 관리](resource-manager-policy.md)를 참조하세요.

 

<!----HONumber=AcomDC_1203_2015-->