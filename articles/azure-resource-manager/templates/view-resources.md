---
title: 리소스 속성 검색
description: 리소스 속성을 검색하는 방법을 설명합니다.
ms.topic: conceptual
ms.date: 06/10/2020
ms.openlocfilehash: 7f0267e4a5afaec0853b82624f4c2486fd8a70e0
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111959444"
---
# <a name="discover-resource-properties"></a>리소스 속성 검색

Resource Manager 템플릿을 만들기 전에 사용할 수 있는 리소스 종류와 템플릿에서 사용할 값을 이해해야 합니다. 이 문서에서는 템플릿에 포함할 속성을 찾을 수 있는 몇 가지 방법을 보여 줍니다.

## <a name="find-resource-provider-namespaces"></a>리소스 공급자 네임스페이스 찾기

ARM 템플릿의 리소스는 리소스 공급자 네임스페이스 및 리소스 종류로 정의됩니다. 예를 들어 Microsoft.Storage/storageAccounts는 스토리지 계정 리소스 종류의 전체 이름입니다. Microsoft.Storage는 네임스페이스입니다. 사용하려는 리소스 종류의 네임스페이스를 아직 모르는 경우 [Azure 서비스의 리소스 공급자](../management/azure-services-resource-providers.md)를 참조하세요.

![Resource Manager 리소스 공급자 네임스페이스 매핑](./media/view-resources/resource-provider-namespace-and-azure-service-mapping.png)

## <a name="export-templates"></a>템플릿 내보내기

기존 리소스의 템플릿 속성을 가져오는 가장 쉬운 방법은 템플릿을 내보내는 것입니다. 자세한 내용은 [Azure Portal에서 템플릿으로 단일 리소스 및 다중 리소스 내보내기](./export-template-portal.md)를 참조하세요.

## <a name="use-resource-manager-tools-extension"></a>Resource Manager 도구 확장 사용

Visual Studio Code 및 Azure Resource Manager 도구 확장은 각 리소스 종류에 필요한 속성을 정확하게 확인하는 데 도움이 됩니다. 템플릿에서 리소스를 정의하는 방법을 간소화하는 IntelliSense 및 코드 조각을 제공합니다. 자세한 내용은 [빠른 시작: Visual Studio Code를 사용하여 Azure Resource Manager 템플릿 만들기](./quickstart-create-templates-use-visual-studio-code.md#add-an-azure-resource)를 참조하세요.

다음 스크린샷은 스토리지 계정 리소스가 템플릿에 추가된 것을 보여 줍니다.

![Resource Manager 도구 확장 코드 조각](./media/view-resources/resource-manager-tools-extension-snippets.png)

확장은 구성 속성의 옵션 목록도 제공합니다.

![Resource Manager 도구 확장 구성 가능 값](./media/view-resources/resource-manager-tools-extension-configurable-properties.png)

## <a name="use-template-reference"></a>템플릿 참조 사용

Azure Resource Manager 템플릿 참조는 템플릿 스키마를 위한 가장 포괄적인 리소스입니다. API 버전, 템플릿 형식, 속성 정보를 찾을 수 있습니다.

1. [Azure Resource Manager 템플릿 참조](/azure/templates/)로 이동합니다.
1. 왼쪽 탐색에서 **스토리지** 를 선택한 다음 **모든 리소스** 를 선택합니다. 모든 리소스 페이지에는 리소스 종류와 버전이 요약되어 있습니다.

    ![템플릿 참조 리소스 버전](./media/view-resources/resource-manager-template-reference-resource-versions.png)

    리소스 종류를 알고 있는 경우 다음 URL 형식을 사용하여 이 페이지로 바로 이동할 수 있습니다. `https://docs.microsoft.com/azure/templates/{provider-namespace}/{resource-type}`

1. 최신 버전을 선택합니다. 최신 API 버전을 사용하는 것이 좋습니다.

    **템플릿 형식** 섹션에는 스토리지 계정의 모든 속성이 나와 있습니다. **sku** 가 목록에 있습니다.

    ![템플릿 참조 스토리지 계정 형식](./media/view-resources/resource-manager-template-reference-storage-account-sku.png)

    **속성 값** 섹션에 **SKU 개체** 가 표시될 때까지 아래로 스크롤합니다. 이 문서에서는 SKU 이름에 허용되는 값을 보여 줍니다.

    ![템플릿 참조 스토리지 계정 SKU 값](./media/view-resources/resource-manager-template-reference-storage-account-sku-values.png)

    페이지 끝에 있는 **빠른 시작 템플릿** 섹션에는 리소스 종류를 포함하는 몇 가지 Azure 빠른 시작 템플릿이 나와 있습니다.

    ![템플릿 참조 스토리지 계정 빠른 시작 템플릿](./media/view-resources/resource-manager-template-reference-quickstart-templates.png)

템플릿 참조는 각 Azure 서비스 설명서 사이트에서 연결됩니다.  예를 들어 [Key Vault 설명서 사이트](../../key-vault/general/overview.md)는 다음과 같습니다.

![Resource Manager 템플릿 참조 Key Vault](./media/view-resources/resource-manager-template-reference-key-vault.png)

## <a name="use-resource-explorer"></a>리소스 탐색기 사용

리소스 탐색기는 Azure Portal에 포함됩니다. 이 방법을 사용하려면 스토리지 계정이 필요합니다. 스토리지 계정이 없는 경우 다음 단추를 선택하여 새로 만듭니다.

[![Azure에 배포](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.storage%2Fstorage-account-create%2Fazuredeploy.json)

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 검색 상자에 **리소스 탐색기** 를 입력하고 **리소스 탐색기** 를 선택합니다.

    ![Azure Portal에서 리소스 탐색기 검색 스크린샷](./media/view-resources/azure-portal-resource-explorer.png)

1. 왼쪽에서 **구독** 을 펼친 다음 Azure 구독을 펼칩니다. **공급자** 또는 **ResourceGroups** 에서 스토리지 계정을 찾을 수 있습니다.

    ![Azure Portal 리소스 탐색기](./media/view-resources/azure-portal-resource-explorer-home.png)

    - **공급자**: **공급자** -> **Microsoft.Storage** -> **storageAccounts** 를 펼친 다음 스토리지 계정을 선택합니다.
    - **ResourceGroups**: 스토리지 계정이 포함된 리소스 그룹을 선택하고 **리소스** 를 선택한 다음, 스토리지 계정을 선택합니다.

    오른쪽에 다음과 유사한 기존 스토리지 계정의 SKU 구성이 표시됩니다.

    ![Azure Portal 리소스 탐색기 스토리지 계정 SKU](./media/view-resources/azure-portal-resource-explorer-sku.png)

## <a name="use-resourcesazurecom"></a>resources.azure.com 사용

resources.azure.com은 Azure 구독의 모든 사용자가 액세스할 수 있는 퍼블릭 웹 사이트입니다. 현재 미리 보기로 제공됩니다.  [리소스 탐색기](#use-resource-explorer)를 대신 사용하는 것이 좋습니다. 이 도구는 다음과 같은 기능을 제공합니다.

- Azure 리소스 관리 API를 검색합니다.
- API 설명서 및 스키마 정보를 가져옵니다.
- 사용자 고유의 구독에서 직접 API 호출을 수행합니다.

이 도구를 사용하여 스키마 정보를 검색하는 방법을 보여 주려면 스토리지 계정이 필요합니다. 스토리지 계정이 없는 경우 다음 단추를 선택하여 새로 만듭니다.

[![Azure에 배포](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.storage%2Fstorage-account-create%2Fazuredeploy.json)

1. [resources.azure.com](https://resources.azure.com/)으로 이동합니다. 도구가 왼쪽 창에 표시되는 데 몇 분 정도 걸립니다.
1. **구독** 을 선택합니다.

    ![resource.azure.com API 매핑](./media/view-resources/resources-azure-com-api-mapping.png)

    왼쪽의 노드는 오른쪽의 API 호출과 일치합니다. **가져오기** 단추를 선택하여 API 호출을 수행할 수 있습니다.
1. 왼쪽에서 **구독** 을 펼친 다음 Azure 구독을 펼칩니다. **공급자** 또는 **ResourceGroups** 에서 스토리지 계정을 찾을 수 있습니다.

    - **공급자**: **공급자** -> **Microsoft.Storage** -> **storageAccounts** 를 펼친 다음 스토리지 계정으로 이동합니다.
    - **ResourceGroups**: 스토리지 계정이 포함된 리소스 그룹을 선택한 다음, **리소스** 를 선택합니다.

    오른쪽에 다음과 유사한 기존 스토리지 계정의 SKU 구성이 표시됩니다.

    ![Azure Portal 리소스 탐색기 스토리지 계정 SKU](./media/view-resources/azure-portal-resource-explorer-sku.png)

## <a name="next-steps"></a>다음 단계

이 문서에서는 템플릿 스키마 정보를 찾는 방법을 배웠습니다. Resource Manager 템플릿을 만드는 방법을 자세히 알아보려면 [ARM 템플릿의 구조 및 구문 이해](./syntax.md)를 참조하세요.