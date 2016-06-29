<properties
   pageTitle="REST API 및 템플릿으로 리소스 배포 | Microsoft Azure"
   description="Azure Resource Manager와 REST API를 사용하여 Azure에 리소스를 배포합니다. 리소스는 Resource Manager 템플릿에 정의됩니다."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="06/08/2016"
   ms.author="tomfitz"/>

# 리소스 관리자 템플릿과 리소스 관리자 REST API로 리소스 배포

> [AZURE.SELECTOR]
- [PowerShell](resource-group-template-deploy.md)
- [Azure CLI](resource-group-template-deploy-cli.md)
- [포털](resource-group-template-deploy-portal.md)
- [Visual Studio](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md)
- [REST API](resource-group-template-deploy-rest.md)

이 문서에서는 리소스 관리자 템플릿으로 리소스 관리자 REST API를 사용하여 Azure에 리소스를 배포하는 방법을 설명합니다.

> [AZURE.TIP] 배포 중 발생하는 오류 디버깅에 대한 도움을 받으려면 다음을 참조하세요.
>
> - [REST API를 사용한 배포 작업 보기](resource-manager-troubleshoot-deployments-rest.md)에서 오류를 해결하는 데 유용한 정보를 알 수 있습니다.
> - [Azure Resource Manager로 Azure에 리소스를 배포할 때 발생하는 일반적인 오류 해결](resource-manager-common-deployment-errors.md)에서 일반적인 배포 오류를 해결하는 방법을 알 수 있습니다.

[AZURE.INCLUDE [resource-manager-deployments](../includes/resource-manager-deployments.md)]

## REST API를 사용하여 배포
1. 인증 토큰을 포함하여 [공통 매개 변수 및 헤더](https://msdn.microsoft.com/library/azure/8d088ecc-26eb-42e9-8acc-fe929ed33563#bk_common)를 설정합니다.
2. 기본 리소스 그룹이 없는 경우 새 리소스 그룹을 만듭니다. 솔루션에 필요한 구독 ID, 새 리소스 그룹 이름 및 위치를 제공합니다. 자세한 내용은 [리소스 그룹 만들기](https://msdn.microsoft.com/library/azure/dn790525.aspx)를 참조하세요.

        PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>?api-version=2015-01-01
          <common headers>
          {
            "location": "West US",
            "tags": {
               "tagname1": "tagvalue1"
            }
          }
   
3. 배포를 실행하기 전에 [템플릿 배포 유효성 검사](https://msdn.microsoft.com/library/azure/dn790547.aspx) 작업을 실행하여 배포 유효성을 검사합니다. 배포를 테스트할 때는 배포를 실행할 때처럼 정확하게 매개 변수를 제공합니다(다음 단계에 표시됨).

3. 새 배포 만들기 템플릿의 구독 ID, 배포할 리소스 그룹 이름, 배포 이름 및 템플릿 링크를 제공합니다. 템플릿 파일에 대한 정보는 [매개 변수 파일](./#parameter-file)을 참조하세요. 리소스 그룹을 만드는 REST API에 대한 정보는 [템플릿 배포 만들기](https://msdn.microsoft.com/library/azure/dn790564.aspx)를 참조하세요. **mode**가 **Incremental**로 설정되어 있습니다. 전체 배포를 실행하려면 **mode**를 **Complete**로 설정합니다. 이 완전한 모드를 사용할 때는 템플릿에 없는 리소스를 실수로 삭제할 수 있으므로 주의해야 합니다.
    
        PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>/providers/Microsoft.Resources/deployments/<YourDeploymentName>?api-version=2015-01-01
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
              }
            }
          }
   
      요청 콘텐츠와 응답 콘텐츠 중 하나 또는 둘 다를 기록하려면 요청에 **debugSetting**을 포함합니다.

        "debugSetting": {
          "detailLevel": "requestContent, responseContent"
        }

      공유 액세스 서명(SAS) 토큰을 사용하여 저장소 계정을 설정할 수 있습니다. 자세한 내용은 [공유 액세스 서명을 사용하여 액세스 위임](https://msdn.microsoft.com/library/ee395415.aspx)을 참조하세요.

4. 템플릿 배포의 상태를 가져옵니다. 자세한 내용은 [템플릿 배포에 대한 정보 가져오기](https://msdn.microsoft.com/library/azure/dn790565.aspx)를 참조하세요.

          GET https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>/providers/Microsoft.Resources/deployments/<YourDeploymentName>?api-version=2015-01-01
           <common headers>

[AZURE.INCLUDE [resource-manager-parameter-file](../includes/resource-manager-parameter-file.md)]

## 다음 단계
- .NET 클라이언트 라이브러리를 통한 리소스 배포의 예제를 보려면 [.NET 라이브러리 및 템플릿을 사용하여 리소스 배포](virtual-machines/virtual-machines-windows-csharp-template.md)를 참조하세요.
- 템플릿에서 매개 변수를 정의하려면 [템플릿 작성](resource-group-authoring-templates.md#parameters)을 참조하세요.
- 다른 환경에 솔루션 배포에 관한 지침은 [Microsoft Azure의 개발 및 테스트 환경](solution-dev-test-environments.md)을 참조하세요.
- 보안 값을 전달하기 위한 주요 자격 증명을 사용하는 방법에 관한 자세한 내용은 [배포 중 보안 값 전달](resource-manager-keyvault-parameter.md)을 참조하세요.

<!---HONumber=AcomDC_0615_2016-->