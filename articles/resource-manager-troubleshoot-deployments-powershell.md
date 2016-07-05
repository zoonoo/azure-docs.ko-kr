<properties
   pageTitle="PowerShell을 통해 배포 작업 보기 | Microsoft Azure"
   description="Azure PowerShell을 사용하여 리소스 관리자 배포의 문제를 감지하는 방법에 대해 설명합니다."
   services="azure-resource-manager,virtual-machines"
   documentationCenter=""
   tags="top-support-issue"
   authors="tfitzmac"
   manager="timlt"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-multiple"
   ms.workload="infrastructure"
   ms.date="06/14/2016"
   ms.author="tomfitz"/>

# Azure PowerShell을 통해 배포 작업 보기

> [AZURE.SELECTOR]
- [포털](resource-manager-troubleshoot-deployments-portal.md)
- [PowerShell](resource-manager-troubleshoot-deployments-powershell.md)
- [Azure CLI](resource-manager-troubleshoot-deployments-cli.md)
- [REST API](resource-manager-troubleshoot-deployments-rest.md)

Azure PowerShell을 통해 배포에 대한 작업을 볼 수 있습니다. 배포 중에 오류가 나타날 때 작업을 보는 데 가장 많은 관심을 가질 수 있으므로 이 문서에서는 실패한 작업을 보는 것에 대해 중점적으로 설명합니다. PowerShell은 쉽게 오류를 찾고 잠재적 해결 방법을 확인할 수 있는 cmdlet을 제공합니다.

[AZURE.INCLUDE [resource-manager-troubleshoot-introduction](../includes/resource-manager-troubleshoot-introduction.md)]

배포하기 전에 템플릿 및 인프라의 유효성을 검사하여 몇 가지 오류를 방지할 수 있습니다. 배포 중에 이후 문제 해결에 도움이 될 만한 추가 요청과 응답 정보를 기록할 수 있습니다. 유효성 검사와 요청 및 응답 정보 기록에 관한 자세한 내용은 [Azure Resource Manager 템플릿으로 리소스 그룹 배포](resource-group-template-deploy.md)를 참조하세요.

## 배포 작업을 사용하여 문제 해결

1. 배포의 전반적인 상태를 가져오려면 **Get-AzureRmResourceGroupDeployment** 명령을 사용합니다. 실패한 배포에 대해서만 결과를 필터링할 수 있습니다.

        Get-AzureRmResourceGroupDeployment -ResourceGroupName ExampleGroup | Where-Object ProvisioningState -eq Failed
        
    실패한 배포는 다음과 같은 형식으로 반환됩니다.
        
        DeploymentName          : Microsoft.Template
        ResourceGroupName       : ExampleGroup
        ProvisioningState       : Failed
        Timestamp               : 6/14/2016 9:55:21 PM
        Mode                    : Incremental
        TemplateLink            :
        Parameters              :
                    Name                Type                 Value
                    ===============     ===================  ==========
                    storageAccountName  String               tfstorage9855
                    adminUsername       String               tfadmin
                    adminPassword       SecureString
                    dnsNameforLBIP      String               dns
                    vmNamePrefix        String               myVM
                    imagePublisher      String               MicrosoftWindowsServer
                    imageOffer          String               WindowsServer
                    imageSKU            String               2012-R2-Datacenter
                    lbName              String               myLB
                    nicNamePrefix       String               nic
                    publicIPAddressName String               myPublicIP
                    vnetName            String               myVNET
                    vmSize              String               Standard_D1

        Outputs                 :
        DeploymentDebugLogLevel :

2. 각 배포는 일반적으로 배포 프로세스의 단계를 나타내는 각 작업을 사용하여 여러 작업으로 구성됩니다. 배포에서 무엇이 잘못 되었는지 검색하려면 일반적으로 배포 작업에 대한 세부 정보를 확인해야 합니다. **Get-AzureRmResourceGroupDeploymentOperation**을 사용하여 작업의 상태를 확인할 수 있습니다.

        Get-AzureRmResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -DeploymentName Microsoft.Template
        
    여러 작업이 각각 다음과 같은 형식으로 반환됩니다.
        
        Id             : /subscriptions/{guid}/resourceGroups/ExampleGroup/providers/Microsoft.Resources/deployments/Microsoft.Template/operations/A3EB2DA598E0A780
        OperationId    : A3EB2DA598E0A780
        Properties     : @{provisioningOperation=Create; provisioningState=Succeeded; timestamp=2016-06-14T21:55:15.0156208Z;
                         duration=PT23.0227078S; trackingId=11d376e8-5d6d-4da8-847e-6f23c6443fbf;
                         serviceRequestId=0196828d-8559-4bf6-b6b8-8b9057cb0e23; statusCode=OK; targetResource=}
        PropertiesText : {duration:PT23.0227078S, provisioningOperation:Create, provisioningState:Succeeded,
                         serviceRequestId:0196828d-8559-4bf6-b6b8-8b9057cb0e23...}

3. 실패한 작업에 대한 자세한 정보를 얻으려면 상태가 **Failed**인 작업에 대한 속성을 검색합니다.

        (Get-AzureRmResourceGroupDeploymentOperation -DeploymentName Microsoft.Template -ResourceGroupName ExampleGroup).Properties | Where-Object ProvisioningState -eq Failed
        
    모든 실패한 작업이 각각 다음과 같은 형식으로 반환됩니다.
        
        provisioningOperation : Create
        provisioningState     : Failed
        timestamp             : 2016-06-14T21:54:55.1468068Z
        duration              : PT3.1449887S
        trackingId            : f4ed72f8-4203-43dc-958a-15d041e8c233
        serviceRequestId      : a426f689-5d5a-448d-a2f0-9784d14c900a
        statusCode            : BadRequest
        statusMessage         : @{error=}
        targetResource        : @{id=/subscriptions/{guid}/resourceGroups/ExampleGroup/providers/
                                Microsoft.Network/publicIPAddresses/myPublicIP;
                                resourceType=Microsoft.Network/publicIPAddresses; resourceName=myPublicIP}

    작업에 대한 추적 ID를 적어둡니다. 특정 작업에 집중하기 위해 다음 단계에서 사용할 예정입니다.

4. 특정 실패한 작업에 대한 상태 메시지를 얻으려면 다음 명령을 사용합니다.

        ((Get-AzureRmResourceGroupDeploymentOperation -DeploymentName Microsoft.Template -ResourceGroupName ExampleGroup).Properties | Where-Object trackingId -eq f4ed72f8-4203-43dc-958a-15d041e8c233).StatusMessage.error
        
    반환하는 내용은 다음과 같습니다.
        
        code           message                                                                        details
        ----           -------                                                                        -------
        DnsRecordInUse DNS record dns.westus.cloudapp.azure.com is already used by another public IP. {}

## 감사 로그를 사용하여 문제 해결

[AZURE.INCLUDE [resource-manager-audit-limitations](../includes/resource-manager-audit-limitations.md)]

배포 오류를 확인하려면 다음 단계를 사용합니다.

1. 로그 항목을 검색하려면 **Get-AzureRmLog** 명령을 실행합니다. **ResourceGroup** 및 **Status** 매개 변수를 사용하여 단일 리소스 그룹에 대해 실패한 이벤트만 반환할 수 있습니다. 시작 및 종료 시간을 지정하지 않으면 지난 시간에 대한 항목이 반환됩니다. 예를 들어 지난 시간 동안 실패한 작업을 검색하려면 다음을 실행합니다.

        Get-AzureRmLog -ResourceGroup ExampleGroup -Status Failed

    특정 시간 범위를 지정할 수 없습니다. 다음 예제에서는 마지막 날 실패한 작업을 찾아봅니다.

        Get-AzureRmLog -ResourceGroup ExampleGroup -StartTime (Get-Date).AddDays(-1) -Status Failed
      
    또는 실패한 작업의 정확한 시작 시간 및 종료 시간을 설정할 수 있습니다.

        Get-AzureRmLog -ResourceGroup ExampleGroup -StartTime 2015-08-28T06:00 -EndTime 2015-09-10T06:00 -Status Failed

2. 이 명령이 너무 많은 항목 및 속성을 반환하는 경우 **Properties** 속성을 검색하여 감사에 집중할 수 있습니다. 또한 **DetailedOutput** 매개 변수를 포함하여 오류 메시지를 참조합니다.

        (Get-AzureRmLog -Status Failed -ResourceGroup ExampleGroup -StartTime (Get-Date).AddDays(-1) -DetailedOutput).Properties
        
    로그 항목의 속성은 다음과 같은 형식으로 반환됩니다.
        
        Content
        -------
        {} 
        {[statusCode, BadRequest], [statusMessage, {"error":{"code":"DnsRecordInUse","message":"DNS record dns.westus.clouda...
        {[statusCode, BadRequest], [serviceRequestId, a426f689-5d5a-448d-a2f0-9784d14c900a], [statusMessage, {"error":{"code...

3. 이러한 결과를 기반으로 두 번째 요소를 집중적으로 살펴보겠습니다. 해당 항목에 대한 상태 메시지를 보고 결과를 더욱 구체화할 수 있습니다.

        ((Get-AzureRmLog -Status Failed -ResourceGroup ExampleGroup -DetailedOutput -StartTime (Get-Date).AddDays(-1)).Properties[1].Content["statusMessage"] | ConvertFrom-Json).error
        
    반환하는 내용은 다음과 같습니다.
        
        code           message                                                                        details
        ----           -------                                                                        -------
        DnsRecordInUse DNS record dns.westus.cloudapp.azure.com is already used by another public IP. {}



## 다음 단계

- 특정 배포 오류에 대한 도움말은 [ Azure Resource Manager를 사용하여 Azure에 리소스를 배포할 때 발생한 일반적인 오류 해결](resource-manager-common-deployment-errors.md)을 참조하세요.
- 감사 로그를 사용하여 다른 유형의 작업을 모니터링하는 방법을 알아보려면 [Resource Manager를 사용하여 작업 감사](resource-group-audit.md)를 참조하세요.
- 실행하기 전에 배포의 유효성을 검사하려면 [Azure Resource Manager 템플릿을 사용하여 리소스 그룹 배포](resource-group-template-deploy.md)를 참조하세요.

<!---HONumber=AcomDC_0622_2016-->