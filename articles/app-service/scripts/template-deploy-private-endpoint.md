---
title: Azure Resource Manager 템플릿을 사용하여 웹앱용 프라이빗 엔드포인트 배포
description: ARM 템플릿을 사용하여 웹앱용 프라이빗 엔드포인트를 배포하는 방법을 알아봅니다.
author: ericgre
ms.assetid: 49e460d0-7759-4ceb-b5a4-f1357e4fde56
ms.topic: sample
ms.date: 07/08/2020
ms.author: ericg
ms.service: app-service
ms.workload: web
ms.openlocfilehash: acf7198477ce0c55f9321fd3f8e57294a60c0961
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89652024"
---
# <a name="create-an-app-service-app-and-deploy-a-private-endpoint-by-using-an-azure-resource-manager-template"></a>Azure Resource Manager 템플릿을 사용하여 App Service 앱 만들기 및 프라이빗 엔드포인트 배포

이 빠른 시작에서는 ARM(Azure Resource Manager) 템플릿을 사용하여 웹앱을 만들고 프라이빗 엔드포인트로 노출합니다.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisite"></a>필수 요소

활성 구독이 있는 Azure 계정이 필요합니다. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-a-private-endpoint"></a>프라이빗 엔드포인트 만들기

이 템플릿은 Azure 웹앱용 프라이빗 엔드포인트를 만듭니다.

### <a name="review-the-template"></a>템플릿 검토

:::code language="json" source="~/quickstart-templates/101-private-endpoint-webapp/azuredeploy.json" :::

### <a name="deploy-the-template"></a>템플릿 배포

Azure Resource Manager 템플릿을 Azure에 배포하는 방법은 다음과 같습니다.

1. Azure에 로그인하고 템플릿을 열려면 다음 링크를 선택합니다.  [Azure에 배포](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-private-endpoint-webapp%2Fazuredeploy.json). 템플릿은 가상 네트워크, 웹앱, 프라이빗 엔드포인트 및 프라이빗 DNS 영역을 만듭니다.
2. 리소스 그룹을 선택하거나 만듭니다.
3. 웹앱, Azure App Service 계획 및 프라이빗 엔드포인트의 이름을 입력합니다.
5. 사용 약관에 대한 설명을 읽습니다. 동의하는 경우 **위에 명시된 사용 약관에 동의함** > **구매**를 선택합니다. 배포가 완료하는 데 몇 분 정도 걸릴 수 있습니다.

## <a name="clean-up-resources"></a>리소스 정리

프라이빗 엔드포인트로 만든 리소스가 더 이상 필요하지 않으면 리소스 그룹을 삭제합니다. 이렇게 하면 프라이빗 엔드포인트와 모든 관련 리소스가 제거됩니다.

리소스 그룹을 삭제하려면 `Remove-AzResourceGroup` cmdlet을 호출합니다.

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>다음 단계

- [ARM 템플릿 샘플](../samples-resource-manager-templates.md)에서 Azure App Service 웹앱용 Azure Resource Manager 템플릿을 더 많이 찾을 수 있습니다.
