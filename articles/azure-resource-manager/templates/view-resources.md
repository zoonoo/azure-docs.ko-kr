---
title: 리소스 속성 검색
description: 리소스 속성을 검색 하는 방법을 설명 합니다.
ms.topic: conceptual
ms.date: 06/10/2020
ms.openlocfilehash: c8bbe0dcb1bc9dc9751a1dc0d0b98a6368473546
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91327751"
---
# <a name="discover-resource-properties"></a>리소스 속성 검색

리소스 관리자 템플릿을 만들기 전에 사용할 수 있는 리소스 종류와 템플릿에서 사용할 값을 이해 해야 합니다. 이 문서에서는 템플릿에 포함할 속성을 찾을 수 있는 몇 가지 방법을 보여 줍니다.

## <a name="find-resource-provider-namespaces"></a>리소스 공급자 네임 스페이스 찾기

ARM 템플릿의 리소스는 리소스 공급자 네임 스페이스 및 리소스 유형으로 정의 됩니다. 예를 들어 Microsoft. Storage/storageAccounts는 저장소 계정 리소스 형식의 전체 이름입니다. Microsoft Storage는 네임 스페이스입니다. 사용 하려는 리소스 종류에 대 한 네임 스페이스를 아직 모르는 경우 [Azure 서비스에 대 한 리소스 공급자](../management/azure-services-resource-providers.md)를 참조 하세요.

![리소스 관리자 리소스 공급자 네임 스페이스 매핑](./media/view-resources/resource-provider-namespace-and-azure-service-mapping.png)

## <a name="export-templates"></a>템플릿 내보내기

기존 리소스에 대 한 템플릿 속성을 가져오는 가장 쉬운 방법은 템플릿을 내보내는 것입니다. 자세한 내용은 [Azure Portal 템플릿에 대 한 단일 및 다중 리소스 내보내기](./export-template-portal.md)를 참조 하세요.

## <a name="use-resource-manager-tools-extension"></a>리소스 관리자 도구 확장 사용

Visual Studio Code 및 Azure Resource Manager 도구 확장은 각 리소스 유형에 필요한 속성을 정확 하 게 확인 하는 데 도움이 됩니다. 템플릿에서 리소스를 정의 하는 방법을 간소화 하는 intellisense 및 코드 조각을 제공 합니다. 자세한 내용은 [빠른 시작: Visual Studio Code를 사용 하 여 템플릿 만들기 Azure Resource Manager](./quickstart-create-templates-use-visual-studio-code.md#add-an-azure-resource)를 참조 하세요.

다음 스크린샷은 저장소 계정 리소스를 템플릿에 추가 하는 방법을 보여 줍니다.

![리소스 관리자 도구 확장 코드 조각](./media/view-resources/resource-manager-tools-extension-snippets.png)

또한 확장은 구성 속성에 대 한 옵션 목록을 제공 합니다.

![리소스 관리자 도구 확장 구성 가능 값](./media/view-resources/resource-manager-tools-extension-configurable-properties.png)

## <a name="use-template-reference"></a>템플릿 참조 사용

Azure Resource Manager 템플릿 참조는 템플릿 스키마를 위한 가장 포괄적인 리소스입니다. API 버전, 템플릿 형식 및 속성 정보를 찾을 수 있습니다.

1. [Azure Resource Manager 템플릿 참조](/azure/templates/)로 이동 합니다.
1. 왼쪽 탐색 영역에서 **저장소**를 선택 하 고 **모든 리소스**를 선택 합니다. 모든 리소스 페이지에는 리소스 유형과 버전이 요약 되어 있습니다.

    ![템플릿 참조 리소스 버전](./media/view-resources/resource-manager-template-reference-resource-versions.png)

    리소스 종류를 알고 있는 경우 다음 URL 형식으로이 페이지로 직접 이동할 수 `https://docs.microsoft.com/azure/templates/{provider-namespace}/{resource-type}` 있습니다.

1. 최신 버전을 선택 합니다. 최신 API 버전을 사용 하는 것이 좋습니다.

    **템플릿 형식** 섹션에는 저장소 계정에 대 한 모든 속성이 나열 됩니다. **sku** 는 목록에 있습니다.

    ![템플릿 참조 저장소 계정 형식](./media/view-resources/resource-manager-template-reference-storage-account-sku.png)

    아래로 스크롤하여 **속성 값** 섹션에서 **Sku 개체** 를 확인 합니다. 이 문서에서는 SKU 이름에 대해 허용 되는 값을 보여 줍니다.

    ![템플릿 참조 저장소 계정 SKU 값](./media/view-resources/resource-manager-template-reference-storage-account-sku-values.png)

    페이지의 끝에는 **빠른 시작 템플릿** 섹션에 리소스 종류를 포함 하는 몇 가지 Azure 빠른 시작 템플릿이 나열 됩니다.

    ![템플릿 참조 저장소 계정 빠른 시작 템플릿](./media/view-resources/resource-manager-template-reference-quickstart-templates.png)

템플릿 참조는 각 Azure 서비스 설명서 사이트에서 연결 됩니다.  예를 들어 [Key Vault 설명서 사이트](../../key-vault/general/overview.md)는 다음과 같습니다.

![리소스 관리자 템플릿 참조 Key Vault](./media/view-resources/resource-manager-template-reference-key-vault.png)

## <a name="use-resource-explorer"></a>리소스 탐색기 사용

리소스 탐색기는 Azure Portal에 포함 되어 있습니다. 이 방법을 사용 하기 전에 저장소 계정이 필요 합니다. 없는 경우 다음 단추를 선택 하 여 하나를 만듭니다.

[![Azure에 배포](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-storage-account-create%2fazuredeploy.json)

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 검색 상자에 **리소스 탐색기**를 입력 한 다음 **리소스 탐색기**를 선택 합니다.

    ![Azure Portal에서 리소스 탐색기 검색을 보여 주는 스크린샷](./media/view-resources/azure-portal-resource-explorer.png)

1. 왼쪽에서 **구독**을 확장 한 다음 Azure 구독을 확장 합니다. **공급자** 또는 **resourcegroups**에서 저장소 계정을 찾을 수 있습니다.

    ![Azure Portal 리소스 탐색기](./media/view-resources/azure-portal-resource-explorer-home.png)

    - **공급자**: **providers**  ->  **Microsoft. Storage**  ->  **storageaccounts**를 확장 한 다음 저장소 계정을 선택 합니다.
    - **Resourcegroups**: 저장소 계정이 포함 된 리소스 그룹을 선택 하 고 **리소스**를 선택한 다음, 저장소 계정을 선택 합니다.

    오른쪽에 다음과 유사한 기존 저장소 계정에 대 한 SKU 구성이 표시 됩니다.

    ![Azure Portal 리소스 탐색기 저장소 계정 sku](./media/view-resources/azure-portal-resource-explorer-sku.png)

## <a name="use-resourcesazurecom"></a>Resources.azure.com 사용

Resources.azure.com은 Azure 구독을 사용 하는 모든 사용자가 공용 웹 사이트에 액세스할 수 있습니다. 미리 보기 상태입니다.  대신 [리소스 탐색기](#use-resource-explorer) 를 사용 하는 것이 좋습니다. 이 도구는 다음과 같은 기능을 제공 합니다.

- Azure 리소스 관리 Api를 검색 합니다.
- API 설명서 및 스키마 정보를 가져옵니다.
- 직접 구독에서 API 호출을 직접 만듭니다.

이 도구를 사용 하 여 스키마 정보를 검색 하는 방법을 보여 주려면 저장소 계정이 필요 합니다. 없는 경우 다음 단추를 선택 하 여 하나를 만듭니다.

[![Azure에 배포](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-storage-account-create%2fazuredeploy.json)

1. [Resources.azure.com](https://resources.azure.com/)으로 이동 합니다. 도구가 왼쪽 창에서 널리 사용 되는 데 몇 분 정도 걸립니다.
1. **구독**을 선택 합니다.

    ![resource.azure.com api 매핑](./media/view-resources/resources-azure-com-api-mapping.png)

    왼쪽의 노드는 오른쪽의 API 호출과 일치 합니다. **GET** 단추를 선택 하 여 API 호출을 수행할 수 있습니다.
1. 왼쪽에서 **구독**을 확장 한 다음 Azure 구독을 확장 합니다. **공급자** 또는 **resourcegroups**에서 저장소 계정을 찾을 수 있습니다.

    - **공급자**: **providers**  ->  **Microsoft. storage**  ->  **storageaccounts**를 확장 한 다음 저장소 계정으로 이동 합니다.
    - **Resourcegroups**: 저장소 계정이 포함 된 리소스 그룹을 선택 하 고 **리소스**를 선택 합니다.

    오른쪽에 다음과 유사한 기존 저장소 계정에 대 한 sku 구성이 표시 됩니다.

    ![Azure Portal 리소스 탐색기 저장소 계정 sku](./media/view-resources/azure-portal-resource-explorer-sku.png)

## <a name="next-steps"></a>다음 단계

이 문서에서는 템플릿 스키마 정보를 찾는 방법에 대해 알아보았습니다. 리소스 관리자 템플릿 만들기에 대 한 자세한 내용은 [ARM 템플릿의 구조 및 구문 이해](./template-syntax.md)를 참조 하세요.
