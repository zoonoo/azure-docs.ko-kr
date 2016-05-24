<properties
   pageTitle="일반적인 Azure 배포 오류 | Microsoft Azure"
   description="Azure Resource Manager를 사용하여 배포 중 발생한 일반적인 오류를 해결하는 방법을 설명합니다."
   services="azure-resource-manager"
   documentationCenter=""
   tags=""
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/19/2016"
   ms.author="tomfitz"/>

# Azure Resource Manager를 사용하여 Azure에 리소스를 배포할 때 발생한 일반적인 오류 해결

이 항목에서는 Azure에 리소스를 배포할 때 발생할 수 있는 일반적인 오류 중 일부를 해결할 수 있는 방법에 대해 설명합니다. 배포 문제 해결에 대한 자세한 내용은 [리소스 그룹 배포 문제 해결](resource-manager-troubleshoot-deployments-portal.md)을 참조하세요.

배포하기 전에 템플릿 및 매개 변수의 유효성을 검사하여 몇 가지 오류를 방지할 수 있습니다. 템플릿 유효성 검사에 대한 예제는 [Azure Resource Manager 템플릿을 사용하여 리소스 배포](resource-group-template-deploy.md)를 참조하세요.

## 잘못된 템플릿 또는 리소스

리소스에서 템플릿 또는 속성이 잘못되었다는 오류를 수신하는 경우 템플릿에 누락된 문자가 있을 수 있습니다. 이 오류는 식을 따옴표로 묶는 템플릿 식을 사용할 때 쉽게 발생하므로 JSON에서 계속 유효성을 검사하고 편집기에서 오류를 감지하지 못할 수 있습니다. 예를 들어 저장소 계정에 대한 다음 이름 할당에는 대괄호 집합 1개, 함수 3개, 괄호 집합 3개, 작은 따옴표 집합 1개, 속성 1개가 포함됩니다.

    "name": "[concat('storage', uniqueString(resourceGroup().id))]",

일치하는 모든 구문을 제공하지 않으면 템플릿에서 의도와 매우 다른 값을 생성합니다.

템플릿에서 누락된 문자가 있는 위치에 따라 템플릿 또는 리소스가 잘못되었다는 오류를 수신합니다. 또한 배포 프로세스에서 템플릿 언어 식을 처리할 수 없다는 오류가 발생할 수 있습니다. 이러한 오류 유형을 수신하면 식 구문을 주의 깊게 검토합니다.

## 리소스 이름이 이미 있음

일부 리소스, 가장 주목할 만한 저장소 계정, 데이터베이스 서버 및 웹사이트의 경우 Azure의 모든 고유한 리소스에 이름을 제공해야 합니다. 명명 규칙과 [uniqueString](./resource-group-template-functions/#uniquestring) 함수 결과를 연결함으로써 고유한 이름을 만들 수 있습니다.
 
    "name": "[concat('contosostorage', uniqueString(resourceGroup().id))]", 
    "type": "Microsoft.Storage/storageAccounts", 

## 배포하는 동안 리소스를 찾을 수 없음

리소스 관리자는 가능한 경우 리소스를 병렬로 만들어 배포를 최적화합니다. 한 리소스가 다른 리소스 뒤에 배포되어야 하는 경우 템플릿에서 **dependsOn** 요소를 사용하여 다른 리소스에 대한 종속성을 만들어야 합니다. 예를 들어 웹앱을 배포할 때는 앱 서비스 계획이 반드시 존재해야 합니다. 웹앱이 앱 서비스 계획에 종속되어 있다고 지정하지 않으면 리소스 관리자가 두 리소스를 동시에 만듭니다. 웹앱에 속성을 설정하려고 할 때 앱 서비스 계획 리소스가 아직 없기 때문에 앱 서비스 계획 리소스를 찾을 수 없다는 오류가 발생합니다. 웹앱에서 종속성을 설정하여 이러한 오류를 방지할 수 있습니다.

    {
      "apiVersion": "2015-08-01",
      "type": "Microsoft.Web/sites",
      ...
      "dependsOn": [
        "[variables('hostingPlanName')]"
      ],
      ...
    }

## 리소스에 사용할 수 없는 위치

리소스에 위치를 지정하는 경우 리소스를 지원하는 위치 중 하나를 사용해야 합니다. 리소스에 대한 위치를 입력하기 전에 다음 명령 중 하나를 사용하여 해당 위치에서 리소스 형식을 지원하는지 확인하세요.

### PowerShell

**Get-AzureRmResourceProvider**를 사용하여 특정 리소스 공급자에 대한 지원되는 종류 및 위치를 가져옵니다.

    Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web

해당 리소스 공급자에 대한 리소스 종류 목록을 얻게 됩니다.

    ProviderNamespace RegistrationState ResourceTypes               Locations
    ----------------- ----------------- -------------               ---------
    Microsoft.Web     Registered        {sites/extensions}          {Brazil South, ...
    Microsoft.Web     Registered        {sites/slots/extensions}    {Brazil South, ...
    Microsoft.Web     Registered        {sites/instances}           {Brazil South, ...
    ...

다음 명령으로 특정 종류의 리소스에 대한 위치를 확인할 있습니다.

    ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).Locations

지원되는 위치 반환:

    Brazil South
    East Asia
    East US
    Japan East
    Japan West
    North Central US
    North Europe
    South Central US
    West Europe
    West US
    Southeast Asia

### Azure CLI

Azure CLI의 경우 **azure location list**를 사용할 수 있습니다. 위치 목록이 길 수도 있고 많은 공급자가 있으므로, 아직 사용할 수 없는 위치를 사용하기 전에 도구를 사용하여 공급자와 위치를 검사할 수 있습니다. 다음 스크립트에서는 **jq**를 사용하여 Azure 가상 컴퓨터에 리소스 공급자를 사용할 수 있는 위치를 검색합니다.

    azure location list --json | jq '.[] | select(.name == "Microsoft.Compute/virtualMachines")'
    
지원되는 위치 반환:
    
    {
      "name": "Microsoft.Compute/virtualMachines",
      "location": "East US,East US 2,West US,Central US,South Central US,North Europe,West Europe,East Asia,Southeast Asia,Japan East,Japan West"
    }

### REST API

REST API의 경우 [리소스 공급자에 대한 정보 가져오기](https://msdn.microsoft.com/library/azure/dn790534.aspx)를 참조하세요.

## 할당량이 초과됨

또한 배포가 리소스 그룹, 구독, 계정 및 기타 범위당 할당량을 초과할 경우 문제가 발생할 수 있습니다. 예를 들어 지역에 대한 코어 수를 제한하도록 구독을 구성할 수 있습니다. 허용된 양보다 많은 코어가 있는 가상 컴퓨터를 배포하려는 경우 할당량을 초과했다는 오류 메시지가 표시됩니다. 전체 할당량 정보는 [Azure 구독 및 서비스 제한, 할당량 및 제약 조건](azure-subscription-service-limits.md)을 참조하세요.

코어에 대한 구독 할당량을 검사하려면 Azure CLI의 `azure vm list-usage` 명령을 사용할 수 있습니다. 다음 예제에서는 무료 평가판 계정에 대한 코어 할당량이 4개임을 보여 줍니다.

    azure vm list-usage
    
반환하는 내용은 다음과 같습니다.
    
    info:    Executing command vm list-usage
    Location: westus
    data:    Name   Unit   CurrentValue  Limit
    data:    -----  -----  ------------  -----
    data:    Cores  Count  0             4
    info:    vm list-usage command OK

상기 구독을 미국 서부지역에서 4개 이상의 코어를 만드는 템플릿을 배포하려고 한다면, (포털에서나 혹은 배포 로그를 조사하여) 이와 같은 오류를 얻게 됩니다

    statusCode:Conflict
    serviceRequestId:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
    statusMessage:{"error":{"code":"OperationNotAllowed","message":"Operation results in exceeding quota limits of Core. Maximum allowed: 4, Current in use: 4, Additional requested: 2."}}

또는 PowerShell에서 **Get-AzureRmVMUsage** cmdlet을 사용할 수 있습니다.

    Get-AzureRmVMUsage
    
반환하는 내용은 다음과 같습니다.

    ...
    CurrentValue : 0
    Limit        : 4
    Name         : {
                     "value": "cores",
                     "localizedValue": "Total Regional Cores"
                   }
    Unit         : null
    ...

이러한 경우 포털로 이동하여 사용자가 배포하고 싶은 지역의 할당량을 올려서 지원 문제를 해결합니다.

> [AZURE.NOTE] 리소스 그룹에 대해서는 이것을 기억하세요. 할당량은 개별적인 지역을 위한 것이지, 전체 구독을 위한 것이 아닙니다. 사용자가 미국 서부에 30개의 코어를 배포해야 하면 미국 서부에 30개의 리소스 관리자 코어를 요청해야 합니다. 사용자가 액세스하는 임의적인 지역에 30개의 코어를 배포해야 하는 경우 모든 지역에 30개의 리소스 관리자 코어를 요청해야 합니다.


## 권한 부여 실패

리소스를 배포하려고 하는 계정 또는 서비스 주체에게 작업 수행을 위한 액세스 권한이 없으므로 배포 중에 오류가 발생할 수 있습니다. 개발자 또는 관리자는 Azure Active Directory를 사용하여 ID와 해당 ID가 액세스할 수 있는 리소스를 자세히 제어할 수 있습니다. 예를 들어 계정이 읽기 역할에 할당되면 그 계정은 새 리소스를 만들 수 없습니다. 이 경우 권한 부여에 실패했다는 오류 메시지가 표시됩니다.

역할 기반 액세스 제어에 대한 자세한 내용은 [Azure 역할 기반 액세스 제어](./active-directory/role-based-access-control-configure.md)를 참조하세요.

역할 기반 액세스 제어 외에도 배포 작업은 구독 정책에 의해 제한될 수 있습니다. 정책을 통해 관리자는 구독에 배포된 모든 리소스에 규칙을 적용할 수 있습니다. 예를 들어 관리자는 리소스 종류에 대해 특정 태그 값을 제공하도록 요구할 수 있습니다. 정책 요구 사항을 충족하지 못하는 경우 배포 중에 오류가 발생합니다. 정책에 대한 자세한 내용은 [정책을 사용하여 리소스 및 컨트롤 액세스 관리](resource-manager-policy.md)를 참조하세요.

## 리소스 공급자 등록 확인

리소스 공급자가 리소스를 관리하고, 계정 또는 구독이 특정 공급자를 사용하도록 등록되어야 합니다. 대부분의 공급자는 Azure 포털이나 사용 중인 명령줄 인터페이스에 의해 자동으로 등록되지만, 그렇지 않은 경우도 있습니다.

### PowerShell

등록 상태를 보려면 **Get-AzureRmResourceProvider**를 사용합니다.

    Get-AzureRmResourceProvider -ListAvailable

사용 가능한 모든 리소스 공급자 및 사용자 등록 상태 반환:

    ProviderNamespace               RegistrationState ResourceTypes
    -----------------               ----------------- -------------
    Microsoft.ApiManagement         Unregistered      {service, validateServiceName, checkServiceNameAvailability}
    Microsoft.AppService            Registered        {apiapps, appIdentities, gateways, deploymenttemplates...}
    Microsoft.Batch                 Registered        {batchAccounts}

공급자를 등록하려면 **Register-AzureRmResourceProvider**를 사용하여 등록할 리소스 공급자의 이름을 제공합니다.

    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Cdn

등록을 확인하라는 메시지가 표시된 다음 상태를 반환합니다.

    Confirm
    Are you sure you want to register the provider 'Microsoft.Cdn'
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y

    ProviderNamespace RegistrationState ResourceTypes
    ----------------- ----------------- -------------
    Microsoft.Cdn     Registering       {profiles, profiles/endpoints,...

### Azure CLI

Azure CLI를 사용하여 공급자가 사용하도록 등록되어 있는지 여부를 확인하려면 `azure provider list` 명령을 사용합니다. 다음 예제에서는 출력이 잘려져 있습니다.

    azure provider list
        
사용 가능한 모든 리소스 공급자 및 사용자 등록 상태 반환:
        
    info:    Executing command provider list
    + Getting ARM registered providers
    data:    Namespace                        Registered
    data:    -------------------------------  -------------
    data:    Microsoft.Compute                Registered
    data:    Microsoft.Network                Registered  
    data:    Microsoft.Storage                Registered
    data:    microsoft.visualstudio           Registered
    ...
    info:    provider list command OK

리소스 공급자를 등록하려면 `azure provider register` 명령을 사용하고 등록할 *네임스페이스*를 지정합니다.

    azure provider register Microsoft.Cdn

### REST API

등록 상태를 가져오려면 [리소스 공급자에 대한 정보 가져오기](https://msdn.microsoft.com/library/azure/dn790534.aspx)를 참조하세요.

공급자를 등록하려면 [리소스 공급자로 구독 등록](https://msdn.microsoft.com/library/azure/dn790548.aspx)을 참조하세요.

## 사용자 지정 스크립트 확장 오류

가상 컴퓨터를 배포할 때 사용자 지정 스크립트 확장에 오류가 발생하면 [Azure Windows VM 확장 오류 문제 해결](./virtual-machines/virtual-machines-windows-extensions-troubleshoot.md) 또는 [Azure Linux VM 확장 오류 문제 해결](./virtual-machines/virtual-machines-linux-extensions-troubleshoot.md)을 참조하세요.

## 배포 준비 시기 이해 

Azure Resource Manager는 모든 공급자가 배포에서 성공적으로 반환할 때 배포에 성공한 것으로 보고합니다. 하지만 반드시 리소스 그룹이 "활성 상태이고 사용자를 위해 준비된" 것을 의미하는 것은 아닙니다. 예를 들어 배포에서는 업그레이드를 다운로드하거나, 다른 템플릿이 아닌 리소스를 대기하거나, 공급자가 추적하는 활동이 아니므로 Azure에서 알지 못하는 일부 다른 실행 가능한 활동이나 복잡한 스크립트를 설치해야 할 수 있습니다. 이러한 경우 리소스를 실제로 사용하기 위해 준비하는 데 시간이 걸릴 수 있습니다. 따라서 배포를 사용할 수 있기 이전에 배포 상태가 성공한 것으로 간주해야 합니다.

하지만 전체 배포에서 시스템 전체 준비 상태를 모니터링하는 방법을 알려주고 사용자가 전체 배포를 조작할 수 있는 경우에만 성공적으로 반환되는 사용자 지정 템플릿에 대한 사용자 지정 스크립트를 작성하여(예: [CustomScriptExtension](https://azure.microsoft.com/blog/2014/08/20/automate-linux-vm-customization-tasks-using-customscript-extension/) 사용) Azure에서 배포 성공을 보고하지 못하도록 차단할 수 있습니다. 확장이 마지막에 실행되도록 하려면 템플릿에서 **dependsOn** 속성을 사용합니다.

## 다음 단계

- 감사 작업에 대해 알아보려면 [리소스 관리자로 작업 감사](resource-group-audit.md)를 참조하세요.
- 배포 중 오류를 확인하는 작업에 대해 알아보려면 [리소스 그룹 배포 문제 해결](resource-manager-troubleshoot-deployments-portal.md)을 참조하세요.

<!---HONumber=AcomDC_0511_2016-->