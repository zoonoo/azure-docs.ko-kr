<properties
   pageTitle="Azure에서 리소스 그룹 배포 문제 해결"
   description="Azure에서 리소스 배포와 관련한 일반적인 문제에 대해 설명하고 Azure 포털, Mac, Linux 및 Windows용 Azure CLI(Azure 명령줄 인터페이스), PowerShell 등을 사용하여 배포를 검사하고 문제를 감지하는 방법을 보여 줍니다."
   services="virtual-machines"
   documentationCenter=""
   authors="squillace"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="command-line-interface"
   ms.workload="infrastructure"
   ms.date="04/25/2015"
   ms.author="rasquill"/>

# Azure에서 리소스 그룹 배포 문제 해결

몇 가지 사항을 미리 확인하면 배포 오류를 훨씬 쉽게 방지할 수 있지만, 다양한 이유로 배포가 실패하는 경우가 있습니다. 이 문서에서는 간단한 실수를 방지하고, 템플릿 파일을 다운로드하고, 배포 로그를 검사하기 위한 도구 및 작업에 대해 설명합니다. 또한 배포 로그에서 오류를 검사할 때 고려해야 할 주요 영역에 대해 설명합니다.

## Azure를 조작하는 데 유용한 도구
AzureResourceManager 모듈에는 명령줄에서 Azure 리소스를 사용할 때 작업에 도움이 되는 도구를 수집하는 cmdlet이 포함되어 있습니다. Azure 리소스 그룹 템플릿은 JSON 문서이고 Azure 리소스 관리 API는 JSON을 수락하고 반환하므로, 리소스에 대한 정보를 탐색하거나 템플릿 및 템플릿 매개 변수 파일을 설계 또는 조작할 때 일반적으로 가장 먼저 사용하는 것은 JSON 구문 분석 도구입니다.

### Mac, Linux 및 Windows 도구
Mac, Linux 및 Windows용 Azure 명령줄 인터페이스를 사용하는 경우 표준 다운로드 도구(예: **[curl](http://curl.haxx.se/)**, **[wget](https://www.gnu.org/software/wget/)**, **[Resty](https://github.com/beders/Resty)**) 및 JSON 유틸리티(예: **[jq](http://stedolan.github.io/jq/download/)**, **[jsawk](https://github.com/micha/jsawk)** 및 JSON을 처리하는 언어 라이브러리)에 대해 잘 알고 있을 것입니다. 이러한 도구는 대부분 Windows용 포트(예: [wget](http://gnuwin32.sourceforge.net/packages/wget.htm))가 있습니다. 실제로 Windows에서 실행 중인 Linux 및 기타 오픈 소스 소프트웨어 도구를 다양한 방법으로 가져올 수 있습니다.

이 항목에는 원하는 정보를 보다 정확하고 효율적으로 가져오기 위해 **jq**와 함께 사용할 수 있는 일부 Azure CLI 명령이 포함되어 있습니다. Azure 리소스 사용을 쉽게 파악할 수 있도록 잘 알고 있는 도구 집합을 선택해야 합니다.

### Windows PowerShell

Windows PowerShell에는 동일한 절차를 수행하는 몇 가지 기본 명령이 있습니다.

- **[Invoke-WebRequest](https://technet.microsoft.com/library/hh849901%28v=wps.640%29)** cmdlet을 사용하여 파일(예: 리소스 그룹 템플릿 또는 매개 변수 JSON 파일)을 다운로드합니다.
- **[ConvertFrom-Json](https://technet.microsoft.com/library/hh849898%28v=wps.640%29.aspx)** cmdlet을 사용하여 JSON 문자열을 JSON 문자열의 각 필드에 대한 속성이 있는 사용자 지정 개체([PSCustomObject](https://msdn.microsoft.com/library/windows/desktop/system.management.automation.pscustomobject%28v=vs.85%29.aspx))로 변환합니다.

## Mac, Linux 및 Windows용 Azure CLI에서 오류 방지

Azure CLI에는 오류를 방지하고 수행 중에 잘못된 사항을 감지하는 데 도움이 되는 여러 가지 명령이 있습니다.

- **azure location list**. 이 명령은 각 리소스 유형(예: 가상 컴퓨터의 공급자)을 지원하는 위치를 가져옵니다. 리소스에 대한 위치를 입력하기 전에 이 명령을 사용하여 해당 위치에서 리소스 유형을 지원하는지 확인하세요.

    위치 목록이 길 수도 있고 많은 공급자가 있으므로, 아직 사용할 수 없는 위치를 사용하기 전에 도구를 사용하여 공급자와 위치를 검사할 수 있습니다. 다음 스크립트에서는 **jq**를 사용하여 Azure 가상 컴퓨터에 대한 리소스 공급자를 사용할 수 있는 위치를 검색합니다. ()

        azure location list --json | jq '.[] | select(.name == "Microsoft.Compute/virtualMachines")'
        {
          "name": "Microsoft.Compute/virtualMachines",
          "location": "East US,West US,West Europe,East Asia,Southeast Asia,North Europe"
        }

- **azure group template validate <resource group>**. 이 명령은 템플릿과 템플릿 매개 변수를 사용하기 전에 유효성을 검사합니다. 사용자 지정 또는 갤러리 템플릿과 사용할 템플릿 매개 변수 값을 입력합니다. 이 cmdlet은 템플릿이 내부적으로 일관되고 설정된 매개 변수 값이 템플릿과 일치하는지 여부를 테스트합니다.

    다음 예에서는 템플릿과 필수 매개 변수의 유효성을 검사하는 방법을 보여 주고, Azure CLI에서 필요한 매개 변수 값을 묻습니다.

        azure group template validate \
        > --template-uri "https://contoso.com/templates/azuredeploy.json" \
        > resource-group
        info:    Executing command group template validate
        info:    Supply values for the following parameters
        adminUserName: UserName
        adminPassword: PassWord
        + Initializing template configurations and parameters
        + Validating the template
        info:    group template validate command OK

## Azure CLI의 배포 문제를 해결하기 위해 정보 가져오기

- **azure group log show <resource group>**: 이 명령은 로그에서 리소스 그룹의 각 배포에 대한 항목을 가져옵니다. 오류가 있는 경우 배포 로그 검사를 시작합니다.

        info:    Executing command group log show
        info:    Getting group logs
        data:    ----------
        data:    EventId:              <guid>
        data:    Authorization:
        data:                          action: Microsoft.Network/networkInterfaces/write
        data:                          role:   Subscription Admin
        data:                          scope:  /subscriptions/xxxxxxxxxxx/resourcegroups/templates/
                                               providers/Microsoft.Network/
                                               networkInterfaces/myNic
        data:    ResourceUri:          /subscriptions/xxxxxxxxxxxx/resourcegroups/templates/providers/
                                       Microsoft.Network/networkInterfaces/myNic
        data:    SubscriptionId:       <guid>
        data:    EventTimestamp (UTC): Wed Apr 22 2015 05:53:31 GMT+0000 (UTC)
        data:    OperationName:        Microsoft.Network/networkInterfaces/write
        data:    OperationId:          <guid>
        data:    Status:               Started
        data:    SubStatus:
        data:    Caller:
        data:    CorrelationId:        <guid>
        data:    Description:
        data:    HttpRequest:          clientRequestId: <guid>
                                       clientIpAddress: 000.000.00.000
                                       method:          PUT

        data:    Level:                Informational
        data:    ResourceGroup:        templates
        data:    ResourceProvider:     Microsoft.Network
        data:    EventSource:          Microsoft Resources
        data:    Properties:           requestbody: {"location":"West US","properties
                                       ":{"ipConfigurations":[{"
                                       name":"ipconfig1","properties":{"
                                       privateIPAllocationMethod

                                       ":"Dynamic","publicIPAddress":{"id":"/
                                       subscriptions/
                                       <guid>/
                                       resourceGroups/
                                       templates/providers/Microsoft.Network/
                                       publicIPAddresses/
                                       myPublicIP"},"subnet":{"idThe AzureResourceManager module includes cmdlets that ":"/subscriptions/
                                       <guid>/resourceGroups/templates/
                                       providers/
                                       Microsoft.Network/virtualNetworks/myVNET/subnets/
                                       Subnet-1
                                       "}}}]}}

        Use the **--last-deployment** option to retrieve only the log for the most recent deployment. The following script uses the **--json** option and **jq** to search the log for deployment failures.

        azure group log show templates --json | jq '.[] | select(.status.value == "Failed")'

        {
          "claims": {
            "aud": "https://management.core.windows.net/",
            "iss": "https://sts.windows.net/<guid>/",
            "iat": "1429678549",
            "nbf": "1429678549",
            "exp": "1429682449",
            "ver": "1.0",
            "http://schemas.microsoft.com/identity/claims/tenantid": "<guid>",
            "http://schemas.microsoft.com/identity/claims/objectidentifier": "<guid>",
            "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "ahmet@contoso.onmicrosoft.com",
            "puid": "XXXXXXXXXXXXXX",
            "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "<hash string>",
            "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname": "ahmet",
            "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname": "",
            "name": "Friendly Name",
            "http://schemas.microsoft.com/claims/authnmethodsreferences": "pwd",
            "groups": "<guid>",
            "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name": "ahmet@contoso.onmicrosoft.com",
            "appid": "<guid>",
            "appidacr": "0",
            "http://schemas.microsoft.com/identity/claims/scope": "user_impersonation",
            "http://schemas.microsoft.com/claims/authnclassreference": "1"
          },
          "properties": {},
          "authorization": {
            "action": "Microsoft.Resources/subscriptions/resourcegroups/deployments/write",
            "role": "Subscription Admin",
            "scope": "/subscriptions/<guid>/resourcegroups/templates/deployments/basic-vm-version-0.1"
          },
          "eventChannels": "Operation",
          "eventDataId": "<guid>",
          "correlationId": "<guid>",
          "eventName": {
            "value": "EndRequest",
            "localizedValue": "End request"
          },
          "eventSource": {
            "value": "Microsoft.Resources",
            "localizedValue": "Microsoft Resources"
          },
          "level": "Error",
          "resourceGroupName": "templates",
          "resourceProviderName": {
            "value": "Microsoft.Resources",
            "localizedValue": "Microsoft Resources"
          },
          "resourceUri": "/subscriptions/<guid>/resourcegroups/templates/deployments/basic-vm-version-0.1",
          "operationId": "<guid>",
          "operationName": {
            "value": "Microsoft.Resources/subscriptions/resourcegroups/deployments/write",
            "localizedValue": "Update deployment"
          },
          "status": {
            "value": "Failed",
            "localizedValue": "Failed"
          },
          "subStatus": {},
          "eventTimestamp": "2015-04-22T05:53:40.8150293Z",
          "submissionTimestamp": "2015-04-22T05:54:00.6728843Z",10037FFE8E80BB65
          "subscriptionId": "<guid>"
        }


- **--verbose and -vv options**: **--verbose** 옵션을 사용하여 세부 정보 표시 모드로 설정하고 `stdout`에서 작업 진행 단계를 표시합니다. **-verbose**가 활성화하는 단계를 포함하는 전체 요청 기록을 보려면 **-vv** 옵션을 사용합니다. 메시지에 오류의 원인에 대한 중요한 단서가 제공되는 경우도 있습니다.

- **Azure 자격 증명이 설정되지 않았거나 만료된 경우**: Azure CLI 세션에서 자격 증명을 새로 고치려면 `azure login`을 입력합니다. 인증 오류에 대한 도움말을 보려면 [Azure CLI를 올바르게 구성](../xplat-cli-connect.md)했는지 확인합니다.

## Windows PowerShell에서 오류 방지

AzureResourceManager 모듈에는 오류를 방지하는 데 유용한 cmdlet이 포함되어 있습니다.


- **Get-AzureLocation**: 이 cmdlet은 각 리소스 유형을 지원하는 위치를 가져옵니다. 리소스에 대한 위치를 입력하기 전에 이 cmdlet을 사용하여 해당 위치에서 리소스 유형을 지원하는지 확인하세요.


- **Test-AzureResourceGroupTemplate**: 템플릿과 템플릿 매개 변수를 사용하기 전에 테스트합니다. 사용자 지정 또는 갤러리 템플릿과 사용할 템플릿 매개 변수 값을 입력합니다. 이 cmdlet은 템플릿이 내부적으로 일관되고 설정된 매개 변수 값이 템플릿과 일치하는지 여부를 테스트합니다.

## Windows PowerShell의 배포 문제를 해결하기 위해 정보 가져오기

- **Get-AzureResourceGroupLog**: 이 cmdlet은 로그에서 리소스 그룹의 각 배포에 대한 항목을 가져옵니다. 오류가 있는 경우 배포 로그 검사를 시작합니다.

- **세부 정보 표시 및 디버그**: AzureResourceManager 모듈의 cmdlet은 실제 작업을 수행하는 REST API를 호출합니다. API가 반환하는 메시지를 표시하려면 $DebugPreference 변수를 "Continue"로 설정하고 명령에서 Verbose 공통 매개 변수를 사용합니다. 메시지에 오류의 원인에 대한 중요한 단서가 제공되는 경우도 있습니다.

- **Azure 자격 증명이 설정되지 않았거나 만료된 경우**: Windows PowerShell 세션에서 자격 증명을 새로 고치려면 Add-AzureAccount cmdlet을 사용합니다. 게시 설정 파일의 자격 증명으로는 AzureResourceManager 모듈의 cmdlet을 지원할 수 없습니다.

## 인증, 구독, 역할 및 할당량 문제

인증, 권한 부여, Azure Active Directory를 비롯하여 성공적인 배포를 방해하는 하나 이상의 문제가 존재할 수 있습니다. Azure 리소스 그룹을 관리하는 방법에 상관없이 계정에 로그인하는 데 사용하는 ID는 Azure Active Directory 개체이거나 서비스 사용자여야 합니다. 이를 회사 또는 학교 계정, 조직 ID라고도 합니다.

개발자 또는 관리자는 Azure Active Directory를 사용하여 ID와 해당 ID가 액세스할 수 있는 리소스를 자세히 제어할 수 있습니다. 배포가 실패할 경우 요청에 인증 또는 권한 부여 문제가 있는지 확인하고 배포 로그에서 리소스 그룹을 검사합니다. 일부 리소스에 대해서는 권한이 있지만, 다른 리소스에 대해서는 권한이 없을 수도 있습니다. Azure CLI를 사용하여 `azure ad` 명령으로 Azure Active Directory 테넌트 및 사용자를 검사할 수 있습니다. Azure CLI 명령에 대한 전체 목록은 [Azure 리소스 관리에서 Mac, Linux 및 Windows용 Azure CLI 사용](azure-cli-arm-commands.md)을 참조하세요.

또한 리소스 그룹, 구독, 계정 및 기타 범위당 배포 기본 할당량에 도달할 경우 문제가 발생할 수 있습니다. 적절하게 배포할 수 있는 리소스가 있는지 확인합니다. 전체 할당량 정보는 [Azure 구독 및 서비스 제한, 할당량 및 제약 조건](../azure-subscription-service-limits.md)을 참조하세요.


## Azure CLI 및 PowerShell 모드 문제

서비스 관리 API 또는 클래식 포털을 사용하여 배포한 Azure 리소스를 리소스 관리 API 또는 Azure 포털을 사용하여 표시할 수 없는 경우도 있습니다. 리소스를 만들 때 사용한 것과 동일한 관리 API 또는 포털을 사용하여 리소스를 관리하는 것이 중요합니다. 리소스가 사리진 경우 다른 관리 API 또는 포털을 사용하여 나타낼 수 있는지 확인합니다.

## Azure 리소스 공급자 등록 문제

리소스 공급자가 리소스를 관리하고, 계정 또는 구독이 특정 공급자를 사용하도록 설정되었을 수 있습니다. 공급자를 사용하도록 설정한 경우 공급자가 사용하도록 등록되어 있어야 합니다. 대부분의 공급자는 Azure 포털이나 사용 중인 명령줄 인터페이스에 의해 자동으로 등록되지만, 그렇지 않은 경우도 있습니다.

Azure CLI를 사용하여 공급자가 사용하도록 등록되어 있는지 여부를 확인하려면 `azure provider list` 명령을 사용합니다. 다음 예에서는 출력이 잘려져 있습니다.

        azure provider list
        info:    Executing command provider list
        + Getting ARM registered providers
        data:    Namespace                        Registered
        data:    -------------------------------  -------------
        data:    Microsoft.Compute                Registered
        data:    Microsoft.Network                Registered  
        data:    Microsoft.Storage                Registered
        data:    microsoft.visualstudio           Registered
        data:    Microsoft.Authorization          Registered
        data:    Microsoft.Automation             NotRegistered
        data:    Microsoft.Backup                 NotRegistered
        data:    Microsoft.BizTalkServices        NotRegistered
        data:    Microsoft.Features               Registered
        data:    Microsoft.Search                 NotRegistered
        data:    Microsoft.ServiceBus             NotRegistered
        data:    Microsoft.Sql                    Registered
        info:    provider list command OK

    Again, if you want more information about providers, including their regional availability, type `azure provider list --json`. The following selects only the first one in the list to view:

        azure provider list --json | jq '.[0]'
        {
          "resourceTypes": [
            {
              "apiVersions": [
                "2014-02-14"
              ],
              "locations": [
                "North Central US",
                "East US",
                "West US",
                "North Europe",
                "West Europe",
                "East Asia"
              ],
              "properties": {},
              "name": "service"
            }
          ],
          "id": "/subscriptions/<guid>/providers/Microsoft.ApiManagement",
          "namespace": "Microsoft.ApiManagement",
          "registrationState": "Registered"
        }


공급자를 등록해야 하는 경우 `azure provider register <namespace>` 명령을 사용합니다. 여기서 *namespace* 값은 이전 목록에서 가져옵니다.

## 사용자 지정 템플릿에 대해 배포가 성공하는 경우 이해

직접 만든 템플릿을 사용하는 경우 Azure 리소스 관리 시스템에서는 모든 공급자가 배포에서 성공적으로 반환될 때 배포가 성공한 것으로 보고한다는 것을 알고 있어야 합니다. 즉, 모든 템플릿 항목이 사용하도록 배포된 것을 의미합니다.

하지만 반드시 리소스 그룹이 **활성 상태이고 사용자를 위해 준비된** 것을 의미하는 것은 아닙니다. 예를 들어 대부분의 배포에서는 업그레이드를 다운로드하거나, 다른 템플릿이 아닌 리소스를 대기하거나, 공급자가 추적하는 활동이 아니므로 Azure에서 알지 못하는 다른 실행 가능한 활동이나 복잡한 스크립트를 설치해야 합니다. 이러한 경우 리소스를 실제로 사용하기 위해 준비하는 데 시간이 걸릴 수 있습니다. 따라서 배포를 사용할 수 있기 이전에 배포 상태가 성공한 것으로 간주해야 합니다.

하지만 전체 배포에서 시스템 전체 준비 상태를 모니터링하는 방법을 알려주고 사용자가 전체 배포를 조작할 수 있는 경우에만 성공적으로 반환되는 사용자 지정 템플릿에 대한 사용자 지정 스크립트를 작성하여(예: [CustomScriptExtension](http://azure.microsoft.com/blog/2014/08/20/automate-linux-vm-customization-tasks-using-customscript-extension/) 사용) Azure에서 배포 성공을 보고하지 못하도록 차단할 수 있습니다. 확장이 마지막에 실행되도록 하려면 템플릿에서 **dependsOn** 속성을 사용합니다. 예제는 [여기서](https://msdn.microsoft.com/library/azure/dn790564.aspx) 확인할 수 있습니다.

## 템플릿 병합

경우에 따라 두 템플릿을 병합하거나 부모에서 자식 템플릿을 시작해야 할 수 있습니다. 이렇게 하려면 마스터 템플릿 내의 배포 리소스를 사용하여 자식 템플릿을 배포할 수 있습니다.


    {
            "name": "instance01",
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2015-01-01",
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                    "uri": "https://mystore.blob.windows.net/azurermtemplates/my-child-template.json",
                    "contentVersion": "1.0.0.0"
                },
                "parameters": {
                    "storageAccountName": { "value": "[variables('stgAcctName1')]" },
                    "adminUsername": { "value": "[parameters('adminUsername')]" },
                    "adminPassword": { "value": "[parameters('adminPassword')]" }
                }
            }
    }


## 리소스 그룹 교차

템플릿을 배포 중인 현재 리소스 그룹의 외부에서 리소스를 사용하는 것이 일반적입니다. 이 동작의 경우 대체 리소스 그룹에서 저장소 계정 또는 가상 네트워크를 사용하는 것이 가장 일반적입니다. 이렇게 하면 가상 컴퓨터를 포함하는 리소스 그룹을 삭제해도 여러 리소스 그룹에서 사용되는 VNet 또는 vhd bolb가 삭제되지 않습니다. 다음 예에서는 외부 리소스 그룹의 리소스를 쉽게 사용할 수 있는 방법을 보여 줍니다.


    {
      "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
      "contentVersion": "1.0.0.0",
      "parameters": {
          "virtualNetworkName": {
              "type": "string"
          },
          "virtualNetworkResourceGroup": {
              "type": "string"
          },
          "subnet1Name": {
              "type": "string"
          },
          "nicName": {
              "type": "string"
          }
      },
      "variables": {
          "vnetID": "[resourceId(parameters('virtualNetworkResourceGroup'), 'Microsoft.Network/virtualNetworks', parameters('virtualNetworkName'))]",
          "subnet1Ref": "[concat(variables('vnetID'),'/subnets/', parameters('subnet1Name'))]"
      },
      "resources": [
      {
          "apiVersion": "2015-05-01-preview",
          "type": "Microsoft.Network/networkInterfaces",
          "name": "[parameters('nicName')]",
          "location": "[parameters('location')]",
          "properties": {
              "ipConfigurations": [{
                  "name": "ipconfig1",
                  "properties": {
                      "privateIPAllocationMethod": "Dynamic",
                      "subnet": {
                          "id": "[variables('subnet1Ref')]"
                      }
                  }
              }]
           }
      }]

    }



## 다음 단계

템플릿 만들기를 마스터하려면 [Azure 리소스 관리자 템플릿 작성](../resource-group-authoring-templates.md)을 읽고 [AzureRMTemplates 리포지토리](https://github.com/azurermtemplates/azurermtemplates)에서 배포 가능한 예제를 살펴보세요. **dependsOn** 속성의 예로는 [인바운드 NAT 규칙을 통한 부하 분산 장치 템플릿](https://github.com/azurermtemplates/azurermtemplates/blob/master/101-create-internal-loadbalancer/azuredeploy.json)이 있습니다.

<!--Image references-->
[5]: ./media/markdown-template-for-new-articles/octocats.png
[6]: ./media/markdown-template-for-new-articles/pretty49.png
[7]: ./media/markdown-template-for-new-articles/channel-9.png
[8]: ./media/markdown-template-for-new-articles/copytemplate.png

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[gog]: http://google.com/
[yah]: http://search.yahoo.com/
[msn]: http://search.msn.com/

<!--HONumber=52-->
 