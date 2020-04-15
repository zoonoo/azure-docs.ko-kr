---
title: Azure Resource Manager 템플릿을 사용하여 서비스 배포
titleSuffix: Azure Cognitive Search
description: Azure Resource Manager 템플릿을 사용하여 Azure Cognitive Search 서비스 인스턴스를 빠르게 배포할 수 있습니다.
manager: nitinme
author: tchristiani
ms.author: terrychr
ms.service: cognitive-search
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 03/20/2020
ms.openlocfilehash: 6da2c324872a86c2c0ce6f55801bc7b0ee8d713e
ms.sourcegitcommit: 515482c6348d5bef78bb5def9b71c01bb469ed80
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80611817"
---
# <a name="quickstart-deploy-cognitive-search-using-a-resource-manager-template"></a>빠른 시작: Resource Manager 템플릿을 사용하여 Cognitive Search 배포

이 문서에서는 Azure Portal에서 Resource Manager 템플릿을 사용하여 Azure Cognitive Search 리소스를 배포하는 과정을 안내합니다.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>사전 요구 사항

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="create-a-cognitive-search-service"></a>Cognitive Search 서비스 만들기

### <a name="review-the-template"></a>템플릿 검토

이 빠른 시작에 사용되는 템플릿은 [Azure 템플릿](https://docs.microsoft.com/azure/templates/Microsoft.Search/2015-08-19/searchservices)에서 가져온 것입니다.

:::code language="json"source="~/quickstart-templates/101-azure-search-create/azuredeploy.json" range="1-86" highlight="4-50":::

이 템플릿에 정의된 Azure 리소스는 다음과 같습니다.

- [Microsoft.Search/searchServices](https://docs.microsoft.com/azure/templates/Microsoft.Search/2015-08-19/searchServices): Azure Cognitive Search 서비스 만들기

### <a name="deploy-the-template"></a>템플릿 배포

다음 이미지를 선택하고 Azure에 로그인하여 템플릿을 엽니다. 이 템플릿은 Azure Cognitive Search 리소스를 만듭니다.

[![Azure에 배포](./media/search-get-started-arm/arm-deploybuttona.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fazure%2Fazure-quickstart-templates%2Fmaster%2F101-azure-search-create%2Fazuredeploy.json)

포털에는 매개 변수 값을 쉽게 입력할 수 있는 양식이 표시됩니다. 일부 매개 변수는 템플릿의 기본값으로 미리 채워져 있습니다. 구독, 리소스 그룹, 위치 및 서비스 이름을 입력해야 합니다. [AI 보강](https://docs.microsoft.com/azure/search/cognitive-search-concept-intro) 파이프라인에서 Cognitive Services를 사용하려면(예: 텍스트의 이진 이미지 파일 분석) Cognitive Search 및 Cognitive Services를 모두 제공하는 위치를 선택합니다. 두 서비스가 AI 보강 워크로드에 대한 동일한 지역에 있어야 합니다. 양식을 모두 작성한 후에는 사용 약관에 동의하고 구매 단추를 선택하여 배포를 완료해야 합니다.

> [!div class="mx-imgBorder"]
> ![Azure Portal에 표시되는 템플릿](./media/search-get-started-arm/arm-portalscrnsht.png)

## <a name="review-deployed-resources"></a>배포된 리소스 검토

배포가 완료되면 포털에서 새 리소스 그룹 및 새 검색 서비스에 액세스할 수 있습니다.

## <a name="clean-up-resources"></a>리소스 정리

다른 Cognitive Search 빠른 시작과 자습서는 이 빠른 시작을 기반으로 작성됩니다. 후속 빠른 시작과 자습서를 계속 진행할 계획이라면 이 리소스를 그대로 두는 것이 좋습니다. 이 리소스가 더 이상 필요 없으면 리소스 그룹을 삭제해도 됩니다. 그러면 Cognitive Search 서비스 및 관련 리소스가 삭제됩니다.

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Azure Resource Manager 템플릿을 사용하여 Cognitive Search 서비스를 만들고 배포의 유효성을 검사했습니다. Cognitive Search 및 Azure Resource Manager에 대해 자세히 알아보려면 아래 문서를 계속 진행하세요.

 - [Azure Cognitive Search 개요](https://docs.microsoft.com/azure/search/search-what-is-azure-search) 읽기
 - 검색 서비스에 대한 [인덱스 만들기](https://docs.microsoft.com/azure/search/search-get-started-portal)
 - 포털 마법사를 사용하여 [검색 앱 만들기](https://docs.microsoft.com/azure/search/search-create-app-portal)
 - 데이터에서 정보를 추출하는 [기술 세트 만들기](https://docs.microsoft.com/azure/search/cognitive-search-quickstart-blob)


