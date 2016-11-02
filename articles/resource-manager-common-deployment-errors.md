<properties
   pageTitle="일반적인 Azure 배포 오류 해결 | Microsoft Azure"
   description="Azure Resource Manager를 사용하여 Azure에 리소스를 배포할 때 발생하는 일반적인 오류를 해결하는 방법을 설명합니다."
   services="azure-resource-manager"
   documentationCenter=""
   tags="top-support-issue"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"
   keywords="배포 오류 Azure 배포, Azure에 배포"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/14/2016"
   ms.author="tomfitz"/>

# Azure Resource Manager를 사용한 일반적인 Azure 배포 오류 해결

이 항목에서는 발생할 수 있는 일반적인 Azure 배포 오류 중 일부를 해결할 수 있는 방법에 대해 설명합니다. 배포에 무엇이 잘못되었는지에 대한 세부 정보가 필요한 경우 우선 [배포 작업 보기](resource-manager-troubleshoot-deployments-portal.md)를 참조하고 오류를 해결하는 도움말을 찾아보려면 이 문서로 돌아옵니다.

## 잘못된 템플릿 또는 리소스

템플릿을 배포할 때 다음이 발생할 수 있습니다.

    Code=InvalidTemplate 
    Message=Deployment template validation failed

리소스에서 템플릿 또는 속성이 잘못되었다는 오류를 수신하는 경우 템플릿에 구문 오류가 있을 수 있습니다. 이 오류는 템플릿 식이 복잡할 수 있기 때문에 쉽게 발생합니다. 예를 들어 저장소 계정에 대한 다음 이름 할당에는 대괄호 집합 1개, 함수 3개, 괄호 집합 3개, 작은 따옴표 집합 1개, 속성 1개가 포함됩니다.

    "name": "[concat('storage', uniqueString(resourceGroup().id))]",

일치하는 모든 구문을 제공하지 않으면 템플릿에서 의도와 매우 다른 값을 생성합니다.

이러한 오류 유형을 수신하면 식 구문을 주의 깊게 검토합니다. [Visual Studio](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) 또는 [Visual Studio 코드](resource-manager-vs-code.md)와 같이 구문 오류에 대해 경고를 표시할 수 있는 JSON 편집기를 사용하는 것을 고려하세요.

## 잘못된 세그먼트 길이

리소스 이름이 올바른 형식이 아닐 경우 다른 잘못된 템플릿 오류가 발생합니다.

    Code=InvalidTemplate
    Message=Deployment template validation failed: 'The template resource {resource-name}' 
    for type {resource-type} has incorrect segment lengths.

루트 수준 리소스는 리소스 종류에 포함된 세그먼트보다 이름에 포함된 세그먼트 수가 1개 더 적어야 합니다. 각 세그먼트는 슬래시로 구분됩니다. 다음 예제에서 형식에는 2개의 세그먼트가 있고 이름에는 1개의 세그먼트가 있으므로 **유효한 이름**입니다.

    {
      "type": "Microsoft.Web/serverfarms",
      "name": "myHostingPlanName",

하지만 그 다음 예제의 경우 형식과 이름의 세그먼트 수가 같으므로 **유효한 이름이 아닙니다**.

    {
      "type": "Microsoft.Web/serverfarms",
      "name": "appPlan/myHostingPlanName",

자식 리소스의 경우 형식 및 이름의 세그먼트 수는 같아야 합니다. 자식의 전체 이름 및 형식은 부모 이름 및 형식을 포함하게 되고, 전체 이름은 여전히 전체 형식보다 하나 더 적은 세그먼트를 포함하게 되므로 문제가 없습니다.

    "resources": [
        {
            "type": "Microsoft.KeyVault/vaults",
            "name": "contosokeyvault",
            ...
            "resources": [
                {
                    "type": "secrets",
                    "name": "appPassword",

리소스 공급자 간에 적용되는 Resource Manager 형식에서는 세그먼트를 제대로 파악하는 것이 특히 어려울 수 있습니다. 예를 들어 웹 사이트에 리소스 잠금을 적용하려면 4개의 세그먼트가 있는 형식이 필요합니다. 따라서 이름은 다음과 같이 3개의 세그먼트를 포함합니다.

    {
        "type": "Microsoft.Web/sites/providers/locks",
        "name": "[concat(variables('siteName'),'/Microsoft.Authorization/MySiteLock')]",

## 리소스 이름은 다른 리소스에서 이미 사용되었습니다.

일부 리소스, 가장 주목할 만한 저장소 계정, 데이터베이스 서버 및 웹사이트의 경우 Azure의 모든 고유한 리소스에 이름을 제공해야 합니다. 고유한 이름을 제공하지 않으면 다음과 같은 오류가 나타날 수 있습니다.

    Code=StorageAccountAlreadyTaken 
    Message=The storage account named mystorage is already taken.

명명 규칙과 [uniqueString](resource-group-template-functions.md#uniquestring) 함수 결과를 연결하여 고유한 이름을 만들 수 있습니다.

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

## 개체에서 멤버 'copy'를 찾을 수 없음

**copy** 요소를 이 요소를 지원하지 않는 템플릿의 일부에 적용했을 때 이 오류가 발생합니다. copy 요소는 리소스 종류에만 적용할 수 있습니다. 리소스 형식 내에서 속성에 복사를 적용할 수 없습니다. 예를 들어 가상 컴퓨터에 복사를 적용하지만 가상 컴퓨터에 대한 OS 디스크에는 적용할 수 없습니다. 경우에 따라 자식 리소스를 부모 리소스로 변환하여 복사 루프를 만들 수 있습니다. 복사 사용에 대한 자세한 내용은 [Azure Resource Manager에서 리소스의 여러 인스턴스 만들기](resource-group-create-multiple.md)를 참조하세요.

## SKU 사용할 수 없음

리소스를 배포할 때(일반적으로 가상 컴퓨터) 다음 오류 코드 및 오류 메시지가 나타날 수 있습니다.

    Code: SkuNotAvailable
    Message: The requested tier for resource '<resource>' is currently not available in location '<location>' for subscription '<subscriptionID>'. Please try another tier or deploy to a different location.

선택한 리소스 SKU(예: VM 크기)를 선택한 위치에 사용할 수 없는 경우 이 오류가 나타납니다. 이 문제를 해결하기 위한 두 가지 옵션이 있습니다.

1.	포털에 로그인하고 UI를 통해 새 리소스 추가를 시작합니다. 값을 설정하면 해당 리소스에 사용 가능한 SKU가 표시됩니다.

    ![사용 가능한 sku](./media/resource-manager-common-deployment-errors/view-sku.png)

2.	해당 위치 또는 대체 위치에서 비즈니스 요구를 충족하는 적합한 SKU를 찾을 수 없는 경우 [Azure 지원](https://portal.azure.com/#create/Microsoft.Support)에 문의하세요.


## 등록된 공급자 찾을 수 없음

리소스를 배포할 때 다음 오류 코드 및 메시지가 나타날 수 있습니다.

    Code: NoRegisteredProviderFound
    Message: No registered resource provider found for location '<location>' and API version '<api-version>' for type '<resource-type>'.

세 가지 이유 중 하나로 이 오류가 나타납니다.

1. 해당 리소스 종류에 대해 위치가 지원되지 않습니다.
2. 해당 리소스 종류에 대해 API 버전이 지원되지 않습니다.
3. 리소스 공급자가 구독에 대해 등록되지 않았습니다.

오류 메시지는 지원되는 위치 및 API 버전에 대해 제안을 제공해야 합니다. 템플릿을 제안된 값 중 하나로 변경할 수 있습니다. 대부분의 공급자는 Azure 포털이나 사용 중인 명령줄 인터페이스에 의해 자동으로 등록되지만, 그렇지 않은 경우도 있습니다. 특정 리소스 공급자를 전에 사용하지 않은 경우 해당 공급자를 등록해야 합니다. 다음 명령으로 리소스 공급자에 대해 자세히 확인할 수 있습니다.

### PowerShell

등록 상태를 보려면 **Get-AzureRmResourceProvider**를 사용합니다.

    Get-AzureRmResourceProvider -ListAvailable

공급자를 등록하려면 **Register-AzureRmResourceProvider**를 사용하여 등록할 리소스 공급자의 이름을 제공합니다.

    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Cdn

특정 종류의 리소스에 대해 지원되는 위치를 확인하려면 다음을 사용합니다.

    ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).Locations

특정 종류의 리소스에 대해 지원되는 API 버전을 확인하려면 다음을 사용합니다.

    ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).ApiVersions

### Azure CLI

공급자가 등록되어 있는지 확인하려면 `azure provider list` 명령을 사용합니다.

    azure provider list

리소스 공급자를 등록하려면 `azure provider register` 명령을 사용하고 등록할 *네임스페이스*를 지정합니다.

    azure provider register Microsoft.Cdn

리소스 공급자에 대해 지원되는 위치 및 API 버전을 보려면 다음을 사용합니다.

    azure provider show -n Microsoft.Compute --json > compute.json

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

또는 PowerShell에서 **Get-AzureRmVMUsage** Cmdlet을 사용할 수 있습니다.

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

## 가상 컴퓨터 문제 해결

| 오류 | 문서 |
| -------- | ----------- |
| 사용자 지정 스크립트 확장 오류 | [Windows VM 확장 오류](./virtual-machines/virtual-machines-windows-extensions-troubleshoot.md)<br />또는<br />[Linux VM 확장 오류](./virtual-machines/virtual-machines-linux-extensions-troubleshoot.md) |
| OS 이미지 프로비전 오류 | [새 Windows VM 오류](./virtual-machines/virtual-machines-windows-troubleshoot-deployment-new-vm.md)<br />또는<br />[새 Linux VM 오류](./virtual-machines/virtual-machines-linux-troubleshoot-deployment-new-vm.md) |
| 할당 오류 | [Windows VM 할당 오류](./virtual-machines/virtual-machines-windows-allocation-failure.md)<br />또는<br />[Linux VM 할당 오류](./virtual-machines/virtual-machines-linux-allocation-failure.md) |
| 연결 시도 시 SSH(secure Shell) 오류 | [Linux VM에 대한 Secure Shell 연결](./virtual-machines/virtual-machines-linux-troubleshoot-ssh-connection.md) |
| VM에서 실행 중인 응용 프로그램 연결 오류 | [Windows VM에서 실행 중인 응용 프로그램](./virtual-machines/virtual-machines-windows-troubleshoot-app-connection.md)<br />또는<br />[Linux VM에서 실행 중인 응용 프로그램](./virtual-machines/virtual-machines-linux-troubleshoot-app-connection.md) |
| 원격 데스크톱 연결 오류 | [Windows VM에 대한 원격 데스크톱 연결](./virtual-machines/virtual-machines-windows-troubleshoot-rdp-connection.md) |
| 다시 배포로 해결되는 연결 오류 | [새 Azure 노드로 가상 컴퓨터 다시 배포](./virtual-machines/virtual-machines-windows-redeploy-to-new-node.md) |
| 클라우드 서비스 오류 | [클라우드 서비스 배포 문제](./cloud-services/cloud-services-troubleshoot-deployment-problems.md) |

## 기타 서비스 문제 해결

다음 표는 Azure에 대한 문제 해결 항목의 전체 목록은 아닙니다. 대신, 리소스를 배포 또는 구성하는 것과 관련된 문제를 중점적으로 설명합니다. 리소스와 함께 런타임 문제를 해결하는 데 도움이 필요한 경우 Azure 서비스에 대한 설명서를 참조하세요.

| 부여 | 문서 |
| -------- | -------- |
| 자동화 | [Azure 자동화의 일반 오류에 대한 문제 해결 팁](./automation/automation-troubleshooting-automation-errors.md) |
| Azure 스택 | [Microsoft Azure 스택 문제 해결](./azure-stack/azure-stack-troubleshooting.md) |
| Azure 스택 | [웹앱 및 Azure 스택](./azure-stack/azure-stack-webapps-troubleshoot-known-issues.md) |
| 데이터 팩터리 | [데이터 팩터리 문제 해결](./data-factory/data-factory-troubleshoot.md) |
| 서비스 패브릭 | [Azure 서비스 패브릭에서 서비스 배포 시 일반적인 문제 해결](./service-fabric/service-fabric-diagnostics-troubleshoot-common-scenarios.md) |
| 사이트 복구 | [가상 컴퓨터 및 물리적 서버를 위한 보호 모니터링 및 문제 해결](./site-recovery/site-recovery-monitoring-and-troubleshooting.md) |
| 저장소 | [Microsoft Azure 저장소 모니터링, 진단 및 문제 해결](./storage/storage-monitoring-diagnosing-troubleshooting.md) |
| StorSimple | [StorSimple 장치 배포 문제 해결](./storsimple/storsimple-troubleshoot-deployment.md) |
| SQL 데이터베이스 | [Azure SQL 데이터베이스에 대한 연결 문제 해결](./sql-database/sql-database-troubleshoot-common-connection-issues.md) |
| SQL 데이터 웨어하우스 | [Azure SQL 데이터 웨어하우스 문제 해결](./sql-data-warehouse/sql-data-warehouse-troubleshoot.md) |

## 배포 준비 시기 이해

Azure Resource Manager는 모든 공급자가 배포에서 성공적으로 반환할 때 배포에 성공한 것으로 보고합니다. 하지만 반드시 리소스 그룹이 "활성 상태이고 사용자를 위해 준비된" 것을 의미하는 것은 아닙니다. 예를 들어 배포에서는 업그레이드를 다운로드하거나, 다른 템플릿이 아닌 리소스를 대기하거나, 공급자가 추적하는 활동이 아니므로 Azure에서 알지 못하는 일부 다른 실행 가능한 활동이나 복잡한 스크립트를 설치해야 할 수 있습니다. 이러한 경우 리소스를 실제로 사용하기 위해 준비하는 데 시간이 걸릴 수 있습니다. 따라서 배포를 사용할 수 있기 이전에 배포 상태가 성공한 것으로 간주해야 합니다.

하지만 전체 배포에서 시스템 전체 준비 상태를 모니터링하는 방법을 알려주고 사용자가 전체 배포를 조작할 수 있는 경우에만 성공적으로 반환되는 사용자 지정 템플릿에 대한 사용자 지정 스크립트를 작성하여(예: [CustomScriptExtension](https://azure.microsoft.com/blog/2014/08/20/automate-linux-vm-customization-tasks-using-customscript-extension/) 사용) Azure에서 배포 성공을 보고하지 못하도록 차단할 수 있습니다. 확장이 마지막에 실행되도록 하려면 템플릿에서 **dependsOn** 속성을 사용합니다.

## 다음 단계

- 감사 작업에 대해 알아보려면 [리소스 관리자로 작업 감사](resource-group-audit.md)를 참조하세요.
- 배포 중 오류를 확인하는 작업에 대해 알아보려면 [배포 작업 보기](resource-manager-troubleshoot-deployments-portal.md)를 참조하세요.

<!---HONumber=AcomDC_0720_2016-->