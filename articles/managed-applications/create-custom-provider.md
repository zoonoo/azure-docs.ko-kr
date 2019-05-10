---
title: Azure 사용자 지정 공급 기업으로 리소스 공급 기업 만들기 미리 보기
description: 리소스 공급 기업을 만들고 사용자 지정 리소스 유형을 배포하는 방법을 설명합니다.
services: managed-applications
author: MSEvanhi
ms.service: managed-applications
ms.topic: tutorial
ms.date: 05/01/2019
ms.author: evanhi
ms.openlocfilehash: 34dd5efda2c9f6cc9a7b5ddcde06e8f7d27de901
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65157341"
---
# <a name="tutorial-create-custom-provider-and-deploy-custom-resources"></a>자습서: 사용자 지정 공급 기업 만들기 및 사용자 지정 리소스 배포

이 자습서에서는 사용자의 고유한 리소스 공급 기업을 만들고 해당 리소스 공급 기업을 위한 사용자 지정 리소스 형식을 배포합니다. 사용자 지정 공급 기업에 대한 자세한 내용은 [Azure 사용자 지정 공급 기업 미리 보기 개요](custom-providers-overview.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

이 자습서에 나온 단계를 완료하려면 REST 작업을 호출해야 합니다. [REST 요청을 전송하는 여러 가지 방법](/rest/api/azure/)이 있습니다. REST 작업을 위한 도구가 아직 없는 경우 [ARMClient](https://github.com/projectkudu/ARMClient)를 설치합니다. 이는 Azure Resource Manager API를 호출하여 간소화하는 오픈 소스 명령줄 도구입니다.

## <a name="deploy-custom-provider"></a>사용자 지정 공급 기업 배포

사용자 지정 공급 기업을 설정하려면 [예제 템플릿](https://github.com/Azure/azure-docs-json-samples/blob/master/custom-providers/customprovider.json)을 사용자의 Azure 구독에 배포합니다.

템플릿을 배포한 후 사용자 구독에는 다음 리소스가 표시됩니다.

* 리소스 및 동작에 대한 작업을 사용하는 Function App.
* 사용자 지정 공급 기업을 통해 생성된 사용자를 저장하기 위한 스토리지 계정.
* 사용자 지정 리소스 형식 및 동작을 정의하는 사용자 지정 공급 기업. 요청을 보내기 위한 함수 앱 엔드포인트를 사용합니다.
* 사용자 지정 공급 기업의 사용자 지정 리소스.

PowerShell을 사용하여 사용자 지정 공급 기업을 배포하려면 다음을 사용합니다.

```azurepowershell-interactive
$rgName = "<resource-group-name>"
$funcName = "<function-app-name>"

New-AzResourceGroup -Name $rgName -Location eastus
New-AzResourceGroupDeployment -ResourceGroupName $rgName `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/custom-providers/customprovider.json `
  -funcname $funcName
```

또는 다음 단추를 사용하여 솔루션을 배포할 수 있습니다.

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-docs-json-samples%2Fmaster%2Fcustom-providers%2Fcustomprovider.json" target="_blank">
    <img src="https://azuredeploy.net/deploybutton.png"/>
</a>

## <a name="view-custom-provider-and-resource"></a>사용자 지정 공급 기업 및 리소스 보기

포털에서 사용자 지정 공급 기업은 숨겨진 리소스 형식입니다. 리소스 공급 기업이 배포되었는지 확인하려면 리소스 그룹으로 이동합니다. 옵션을 **숨겨진 형식 표시**로 선택합니다.

![숨겨진 리소스 형식 표시](./media/create-custom-providers/show-hidden.png)

배포한 사용자 지정 리소스 형식을 확인하려면 리소스 형식에 가져오기 작업을 사용합니다.

```
GET https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/users?api-version=2018-09-01-preview
```

ARMClient에서 다음을 사용합니다.

```powershell
$subID = (Get-AzContext).Subscription.Id
$requestURI = "https://management.azure.com/subscriptions/$subID/resourceGroups/$rgName/providers/Microsoft.CustomProviders/resourceProviders/$funcName/users?api-version=2018-09-01-preview"

armclient GET $requestURI
```

다음과 같은 응답이 수신됩니다.

```json
{
  "value": [
    {
      "properties": {
        "provisioningState": "Succeeded",
        "FullName": "Santa Claus",
        "Location": "NorthPole"
      },
      "id": "/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/users/santa",
      "name": "santa",
      "type": "Microsoft.CustomProviders/resourceProviders/users"
    }
  ]
}
```

## <a name="call-action"></a>호출 동작

또한 사용자 지정 공급 기업에는 **ping**이라는 동작이 있습니다. 요청을 처리하는 코드는 함수 앱에서 구현됩니다. ping 동작은 인사말을 사용하여 회신합니다.

ping 요청을 보내려면 사용자 지정 공급 기업에서 POST 작업을 사용합니다.

```
POST https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/ping?api-version=2018-09-01-preview
```

ARMClient에서 다음을 사용합니다.

```powershell
$pingURI = "https://management.azure.com/subscriptions/$subID/resourceGroups/$rgName/providers/Microsoft.CustomProviders/resourceProviders/$funcName/ping?api-version=2018-09-01-preview"

armclient POST $pingURI
```

다음과 같은 응답이 수신됩니다.

```json
{
  "pingcontent": {
    "source": "<function-name>.azurewebsites.net"
  },
  "message": "hello <function-name>.azurewebsites.net"
}
```

## <a name="create-resource-type"></a>리소스 형식 만들기

사용자 지정 리소스 형식을 만들기 위해 템플릿에서 리소스를 배포할 수 있습니다. 이 접근 방식은 이 자습서에서 배포한 템플릿에 표시되어 있습니다. 또한 리소스 형식에 대한 PUT 요청을 보낼 수 있습니다.

```
PUT https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/users/<resource-name>?api-version=2018-09-01-preview

{"properties":{"FullName": "Test User", "Location": "Earth"}}
```

ARMClient에서 다음을 사용합니다.

```powershell
$addURI = "https://management.azure.com/subscriptions/$subID/resourceGroups/$rgName/providers/Microsoft.CustomProviders/resourceProviders/$funcName/users/testuser?api-version=2018-09-01-preview"
$requestBody = "{'properties':{'FullName': 'Test User', 'Location': 'Earth'}}"

armclient PUT $addURI $requestBody
```

다음과 같은 응답이 수신됩니다.

```json
{
  "properties": {
    "provisioningState": "Succeeded",
    "FullName": "Test User",
    "Location": "Earth"
  },
  "id": "/subscriptions/<sub-ID>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/users/testuser",
  "name": "testuser",
  "type": "Microsoft.CustomProviders/resourceProviders/users"
}
```

## <a name="next-steps"></a>다음 단계

사용자 지정 공급 기업에 대한 소개는 [Azure 사용자 지정 공급 기업 미리 보기 개요](custom-providers-overview.md)를 참조하세요.
