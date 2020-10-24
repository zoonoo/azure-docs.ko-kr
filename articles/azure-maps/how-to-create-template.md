---
title: ARM 템플릿을 사용 하 여 Azure Maps 계정 만들기 | Microsoft Azure 맵
description: ARM (Azure Resource Manager) 템플릿을 사용 하 여 Azure Maps 계정을 만드는 방법에 대해 알아봅니다.
author: philmea
ms.author: philmea
ms.date: 10/20/2020
ms.topic: how-to
ms.service: azure-maps
ms.openlocfilehash: c715c0639e962f76f669515c1d2c826c8cf6cc9e
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/24/2020
ms.locfileid: "92525120"
---
# <a name="create-your-azure-maps-account-using-an-arm-template"></a>ARM 템플릿을 사용 하 여 Azure Maps 계정 만들기

ARM (Azure Resource Manager) 템플릿을 사용 하 여 Azure Maps 계정을 만들 수 있습니다. 계정이 생기면 웹 사이트 또는 모바일 애플리케이션에서 API를 구현할 수 있습니다.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

환경이 필수 구성 요소를 충족하고 ARM 템플릿 사용에 익숙한 경우 **Azure에 배포** 단추를 선택합니다. 그러면 Azure Portal에서 템플릿이 열립니다.

[![Azure에 배포](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-maps-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>필수 구성 요소

이 문서를 완료하려면 다음이 필요합니다.

* Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="review-the-template"></a>템플릿 검토

이 빠른 시작에서 사용되는 템플릿은 [Azure 빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/101-maps-create/)에서 나온 것입니다.

:::code language="json" source="~/quickstart-templates/101-maps-create/azuredeploy.json":::

이 템플릿에 정의 된 Azure Maps 계정 리소스는 다음과 같습니다.

* [**Microsoft. Maps/accounts**](/azure/templates/microsoft.maps/accounts): Azure Maps 계정을 만듭니다.

## <a name="deploy-the-template"></a>템플릿 배포

1. 다음 이미지를 선택하고 Azure에 로그인하여 템플릿을 엽니다. 템플릿은 Azure Maps 계정을 만듭니다.

    [![Azure에 배포](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-maps-create%2Fazuredeploy.json)

2. 다음 값을 선택하거나 입력합니다.

    ![ARM 템플릿 배포 포털](./media/how-to-create-template/create-account-using-template-portal.png)

    지정 되지 않은 경우 기본값을 사용 하 여 Azure Maps 계정을 만듭니다.

    * **구독**: Azure 구독을 선택합니다.
    * **리소스 그룹**: **새로 만들기**를 선택하고 리소스 그룹에 고유한 이름을 입력한 다음, **확인**을 클릭합니다.
    * **위치**: 위치를 선택합니다. 예: **미국 서 부 2**.
    * **계정 이름**: 전역적으로 고유한 Azure Maps 계정의 이름을 입력 합니다.
    * **가격 책정 계층**: 적절 한 가격 책정 계층을 선택 합니다. 템플릿의 기본값은 S0입니다.

3. **검토 + 만들기**를 선택합니다. 
4. 검토 페이지에서 설정을 확인 하 고 **만들기**를 클릭 합니다. Azure Maps 성공적으로 배포 되 면 알림을 받게 됩니다.

    ![ARM 템플릿 배포 포털 알림](./media/how-to-create-template/resource-manager-template-portal-deployment-notification.png)

Azure Portal를 사용 하 여 템플릿을 배포 합니다. Azure PowerShell, Azure CLI 및 REST API를 사용할 수도 있습니다. 다른 배포 방법을 알아보려면 [템플릿 배포](../azure-resource-manager/templates/deploy-powershell.md)를 참조하세요.

## <a name="review-deployed-resources"></a>배포된 리소스 검토

Azure Portal를 사용 하 여 Azure Maps 계정을 확인 하 고 키를 볼 수 있습니다. 다음 Azure CLI 스크립트를 사용 하 여 계정 키를 나열할 수도 있습니다.

```azurecli-interactive
az maps account keys list --name MyMapsAccount --resource-group MyResourceGroup
```

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요 하지 않으면 리소스 그룹을 삭제 합니다. 그러면 Azure Maps 계정도 삭제 됩니다. Azure CLI를 사용 하 여 리소스 그룹을 삭제 하려면 다음을 수행 합니다.

```azurecli-interactive
az group delete --name MyResourceGroup
```

## <a name="next-steps"></a>다음 단계

Azure Maps 및 Azure Resource Manager에 대 한 자세한 내용은 아래 문서를 참조 하세요.

- Azure Maps [demo 응용 프로그램](quick-demo-map-app.md) 만들기
- [ARM 템플릿에](../azure-resource-manager/templates/overview.md) 대 한 자세한 정보