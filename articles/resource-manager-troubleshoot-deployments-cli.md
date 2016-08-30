<properties
   pageTitle="Azure CLI를 사용하여 배포 작업 보기 | Microsoft Azure"
   description="Azure CLI를 사용하여 리소스 관리자 배포의 문제를 감지하는 방법에 대해 설명합니다."
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
   ms.date="08/15/2016"
   ms.author="tomfitz"/>

# Azure CLI를 통해 배포 작업 보기

> [AZURE.SELECTOR]
- [포털](resource-manager-troubleshoot-deployments-portal.md)
- [PowerShell](resource-manager-troubleshoot-deployments-powershell.md)
- [Azure CLI](resource-manager-troubleshoot-deployments-cli.md)
- [REST API](resource-manager-troubleshoot-deployments-rest.md)

Azure에 리소스를 배포할 때 오류가 발생하는 경우 실행된 배포 작업에 대한 더욱 자세한 정보가 필요할 수 있습니다. Azure CLI는 오류를 찾고 잠재적 해결 방법을 확인할 수 있도록 하는 명령을 제공합니다.

[AZURE.INCLUDE [resource-manager-troubleshoot-introduction](../includes/resource-manager-troubleshoot-introduction.md)]

배포하기 전에 템플릿 및 인프라의 유효성을 검사하여 몇 가지 오류를 방지할 수 있습니다. 배포 중에 이후 문제 해결에 도움이 될 만한 추가 요청과 응답 정보를 기록할 수 있습니다. 유효성 검사와 요청 및 응답 정보 기록에 관한 자세한 내용은 [Azure Resource Manager 템플릿으로 리소스 그룹 배포](resource-group-template-deploy-cli.md)를 참조하세요.

## 감사 로그를 사용하여 문제 해결

[AZURE.INCLUDE [resource-manager-audit-limitations](../includes/resource-manager-audit-limitations.md)]

배포 오류를 확인하려면 다음 단계를 사용합니다.

1. 감사 로그를 확인하려면 **azure group log show** 명령을 실행합니다. **--last-deployment** 옵션을 포함하여 가장 최근의 배포에 대한 로그만 검색할 수 있습니다.

        azure group log show ExampleGroup --last-deployment

2. **azure group log show** 명령은 많은 정보를 반환합니다. 문제 해결을 위해 일반적으로 실패한 작업에 초점을 둡니다. 다음 스크립트는 **--json** 옵션 및 [jq](https://stedolan.github.io/jq/) JSON 유틸리티를 사용하여 배포 오류에 대한 로그를 검색합니다.

        azure group log show ExampleGroup --json | jq '.[] | select(.status.value == "Failed")'
        
        {
        "claims": {
          "aud": "https://management.core.windows.net/",
          "iss": "https://sts.windows.net/<guid>/",
          "iat": "1442510510",
          "nbf": "1442510510",
          "exp": "1442514410",
          "ver": "1.0",
          "http://schemas.microsoft.com/identity/claims/tenantid": "{guid}",
          "http://schemas.microsoft.com/identity/claims/objectidentifier": "{guid}",
          "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress": "someone@example.com",
          "puid": "XXXXXXXXXXXXXXXX",
          "http://schemas.microsoft.com/identity/claims/identityprovider": "example.com",
          "altsecid": "1:example.com:XXXXXXXXXXX",
          "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "<hash string="">",
          "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname": "Tom",
          "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname": "FitzMacken",
          "name": "Tom FitzMacken",
          "http://schemas.microsoft.com/claims/authnmethodsreferences": "pwd",
          "groups": "{guid}",
          "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name": "example.com#someone@example.com",
          "wids": "{guid}",
          "appid": "{guid}",
          "appidacr": "0",
          "http://schemas.microsoft.com/identity/claims/scope": "user_impersonation",
          "http://schemas.microsoft.com/claims/authnclassreference": "1",
          "ipaddr": "000.000.000.000"
        },
        "properties": {
          "statusCode": "Conflict",
          "statusMessage": "{"Code":"Conflict","Message":"Website with given name mysite already exists.","Target":null,"Details":[{"Message":"Website with given name
            mysite already exists."},{"Code":"Conflict"},{"ErrorEntity":{"Code":"Conflict","Message":"Website with given name mysite already exists.","ExtendedCode":
            "54001","MessageTemplate":"Website with given name {0} already exists.","Parameters":["mysite"],"InnerErrors":null}}],"Innererror":null}"
        },
        ...

    실패한 작업에 대한 json의 정보를 포함하는 **속성**을 확인할 수 있습니다.

    **--verbose** 및 **-vv** 옵션을 사용하여 로그에서 자세한 정보를 볼 수 있습니다. **--verbose** 옵션을 사용하여 작업이 `stdout`에 대해 통과하는 단계를 표시합니다. 전체 요청 기록을 보려면 **-vv** 옵션을 사용합니다. 메시지에 오류의 원인에 대한 중요한 단서가 제공되는 경우도 있습니다.

3. 실패한 항목에 대한 상태 메시지를 중점적으로 확인하려면 다음 명령을 사용합니다.

        azure group log show ExampleGroup --json | jq -r ".[] | select(.status.value == "Failed") | .properties.statusMessage"


## 배포 작업을 사용하여 문제 해결

1. **azure group deployment show** 명령을 사용하여 배포의 전반적인 상태를 가져옵니다. 아래 예제에서는 배포에 실패했습니다.

        azure group deployment show --resource-group ExampleGroup --name ExampleDeployment
        
        info:    Executing command group deployment show
        + Getting deployments
        data:    DeploymentName     : ExampleDeployment
        data:    ResourceGroupName  : ExampleGroup
        data:    ProvisioningState  : Failed
        data:    Timestamp          : 2015-08-27T20:03:34.9178576Z
        data:    Mode               : Incremental
        data:    Name             Type    Value
        data:    ---------------  ------  ------------
        data:    siteName         String  ExampleSite
        data:    hostingPlanName  String  ExamplePlan
        data:    siteLocation     String  West US
        data:    sku              String  Free
        data:    workerSize       String  0
        info:    group deployment show command OK

2. 배포의 실패한 작업에 대한 메시지를 보려면 다음을 사용합니다.

        azure group deployment operation list --resource-group ExampleGroup --name ExampleDeployment --json  | jq ".[] | select(.properties.provisioningState == "Failed") | .properties.statusMessage.Message"


## 다음 단계

- 특정 배포 오류에 대한 도움말은 [ Azure Resource Manager를 사용하여 Azure에 리소스를 배포할 때 발생한 일반적인 오류 해결](resource-manager-common-deployment-errors.md)을 참조하세요.
- 감사 로그를 사용하여 다른 유형의 작업을 모니터링하는 방법을 알아보려면 [Resource Manager를 사용하여 작업 감사](resource-group-audit.md)를 참조하세요.
- 실행하기 전에 배포의 유효성을 검사하려면 [Azure Resource Manager 템플릿을 사용하여 리소스 그룹 배포](resource-group-template-deploy.md)를 참조하세요.

<!---HONumber=AcomDC_0817_2016-->