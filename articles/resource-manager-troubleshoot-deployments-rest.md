<properties
   pageTitle="REST API를 사용하여 배포 작업 보기 | Microsoft Azure"
   description="Azure Resource Manager REST API를 사용하여 리소스 관리자 배포의 문제를 감지하는 방법에 대해 설명합니다."
   services="azure-resource-manager,virtual-machines"
   documentationCenter=""
   tags="top-support-issue"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-multiple"
   ms.workload="infrastructure"
   ms.date="05/19/2016"
   ms.author="tomfitz"/>

# Azure 리소스 관리자 REST API를 통해 배포 작업 보기

> [AZURE.SELECTOR]
- [포털](resource-manager-troubleshoot-deployments-portal.md)
- [PowerShell](resource-manager-troubleshoot-deployments-powershell.md)
- [Azure CLI](resource-manager-troubleshoot-deployments-cli.md)
- [REST API](resource-manager-troubleshoot-deployments-rest.md)

Azure에 리소스를 배포할 때 오류가 발생하는 경우 실행된 배포 작업에 대한 더욱 자세한 정보가 필요할 수 있습니다. REST API는 오류를 찾고 잠재적 해결 방법을 확인할 수 있도록 하는 작업을 제공합니다.

[AZURE.INCLUDE [resource-manager-troubleshoot-introduction](../includes/resource-manager-troubleshoot-introduction.md)]

## REST API 문제 해결

1. [템플릿 배포 만들기](https://msdn.microsoft.com/library/azure/dn790564.aspx) 작업을 사용하여 리소스를 배포합니다. 디버깅에 도움이 될 수 있는 정보를 유지하려면 JSON 요청의 **debugSetting** 속성을 **requestContent** 및/또는 **responseContent**로 설정합니다. 

        PUT https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}?api-version={api-version}
          <common headers>
          {
            "properties": {
              "templateLink": {
                "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
                "contentVersion": "1.0.0.0",
              },
              "mode": "Incremental",
              "parametersLink": {
                "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
                "contentVersion": "1.0.0.0",      
              },
              "debugSetting": {
                "detailLevel": "requestContent, responseContent"
              }
            }
          }

    기본적으로 **debugSetting** 값은 **none**으로 설정되어 있습니다. **debugSetting** 값을 지정하는 경우 배포 동안 제공할 정보 유형을 신중히 고려하세요. 요청 또는 응답에 대한 정보를 로깅하게 되면 배포 작업을 통해 검색되는 중요한 데이터가 노출될 가능성이 있기 때문입니다.

2. [템플릿 배포에 대한 정보 가져오기](https://msdn.microsoft.com/library/azure/dn790565.aspx) 작업을 사용하여 배포에 대한 정보를 가져옵니다.

        GET https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}?api-version={api-version}

    응답에서 **provisioningState**, **correlationId** 및 **error** 요소에 특히 유의합니다. **correlationId**는 관련 이벤트를 추적하는 데 사용되며 기술 지원과 함께 문제를 해결할 때 유용할 수 있습니다.
    
        { 
          ...
          "properties": {
            "provisioningState":"Failed",
            "correlationId":"d5062e45-6e9f-4fd3-a0a0-6b2c56b15757",
            ...
            "error":{
              "code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see http://aka.ms/arm-debug for usage details.",
              "details":[{"code":"Conflict","message":"{\r\n  "error": {\r\n    "message": "Conflict",\r\n    "code": "Conflict"\r\n  }\r\n}"}]
            }  
          }
        }

3. [모든 템플릿 배포 작업 나열](https://msdn.microsoft.com/library/azure/dn790518.aspx) 작업을 사용하여 배포 작업에 대한 정보를 가져오세요.

        GET https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}/operations?$skiptoken={skiptoken}&api-version={api-version}

    응답에는 배포 중에 **debugSetting**에 지정하는 설정을 기준으로 요청 및/또는 응답 정보가 포함됩니다.
    
        {
          ...
          "properties": 
          {
            ...
            "request":{
              "content":{
                "location":"West US",
                "properties":{
                  "accountType": "Standard_LRS"
                }
              }
            },
            "response":{
              "content":{
                "error":{
                  "message":"Conflict","code":"Conflict"
                }
              }
            }
          }
        }

4. [구독에서 관리 이벤트 나열](https://msdn.microsoft.com/library/azure/dn931934.aspx) 작업을 사용하여 배포에 대한 감사 로그에서 이벤트를 가져오세요.

        GET https://management.azure.com/subscriptions/{subscription-id}/providers/microsoft.insights/eventtypes/management/values?api-version={api-version}&$filter={filter-expression}&$select={comma-separated-property-names}


## 다음 단계

- 특정 배포 오류에 대한 도움말은 [ Azure Resource Manager를 사용하여 Azure에 리소스를 배포할 때 발생한 일반적인 오류 해결](resource-manager-common-deployment-errors.md)을 참조하세요.
- 감사 로그를 사용하여 다른 유형의 작업을 모니터링하는 방법을 알아보려면 [리소스 관리자를 사용하여 작업 감사](resource-group-audit.md)를 참조하세요.
- 실행하기 전에 배포의 유효성을 검사하려면 [Azure Resource Manager 템플릿을 사용하여 리소스 그룹 배포](resource-group-template-deploy.md)를 참조하세요.

<!---HONumber=AcomDC_0525_2016-->