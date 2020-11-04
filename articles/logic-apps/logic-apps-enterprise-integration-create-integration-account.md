---
title: B2B 통합 계정 만들기 또는 관리
description: Azure Logic Apps와 엔터프라이즈 통합을 위한 통합 계정 만들기, 연결 및 관리
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, logicappspm
ms.topic: conceptual
ms.date: 11/04/2020
ms.openlocfilehash: 191832ab227e854b40938183e335c1b6ea52199c
ms.sourcegitcommit: 99955130348f9d2db7d4fb5032fad89dad3185e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2020
ms.locfileid: "93347848"
---
# <a name="create-and-manage-integration-accounts-for-b2b-enterprise-integrations-in-azure-logic-apps"></a>Azure Logic Apps에서 B2B 엔터프라이즈 통합에 대한 통합 계정 만들기 및 관리

[Azure Logic Apps](../logic-apps/logic-apps-overview.md)를 사용하여 [엔터프라이즈 통합 및 B2B 솔루션](../logic-apps/logic-apps-enterprise-integration-overview.md)을 빌드하려면 먼저 통합 계정을 만들어야 합니다. 이 계정은 논리 앱 워크플로에서 정의하고 사용할 수 있는 통합 아티팩트에 대한 안전하고 확장 가능하며 관리할 수 있는 컨테이너를 제공하는 별도의 Azure 리소스입니다.

예를 들어 거래 업체, 규약, 맵, 스키마, 인증서 및 일괄 처리 구성과 같은 B2B 아티팩트를 생성, 저장 및 관리할 수 있습니다. 또한 논리 앱이 이러한 아티팩트를 사용 하 여 작동 하 고 Logic Apps B2B 커넥터를 사용 하기 전에 [통합 계정을](#link-account) 논리 앱에 연결 해야 합니다. 통합 계정 및 논리 앱은 모두 *동일한* 위치 또는 지역에 있어야 합니다.

> [!TIP]
> 통합 [서비스 환경](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)내에서 통합 계정을 만들려면 [ISE에서 통합 계정 만들기](../logic-apps/add-artifacts-integration-service-environment-ise.md#create-integration-account-environment)를 참조 하세요.

이 항목에서는 다음 작업을 수행 하는 방법을 보여 줍니다.

* 통합 계정을 만듭니다.
* 통합 계정을 논리 앱에 연결합니다.
* 통합 계정에 대 한 가격 책정 계층을 변경 합니다.
* 논리 앱에서 통합 계정 연결을 해제 합니다.
* 통합 계정을 다른 Azure 리소스 그룹 또는 구독으로 이동합니다.
* 통합 계정을 삭제합니다.

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독 Azure 구독이 없는 경우 [체험 Azure 계정에 등록](https://azure.microsoft.com/free/)합니다.

## <a name="create-integration-account"></a>통합 계정 만들기

### <a name="portal"></a>[포털](#tab/azure-portal)

이 작업의 경우이 섹션의 단계, [Azure PowerShell](/powershell/module/Az.LogicApp/New-AzIntegrationAccount)또는 [Azure CLI](/cli/azure/resource#az-resource-create)따라 Azure Portal를 사용할 수 있습니다.

1. Azure 계정 자격 증명을 사용하여 [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. Azure 기본 메뉴에서 **리소스 만들기** 를 선택합니다. 검색 상자에 "통합 계정"을 필터로 입력 하 고 **통합 계정** 을 선택 합니다.

   ![새 통합 계정 만들기](./media/logic-apps-enterprise-integration-create-integration-account/create-integration-account.png)

1. **통합 계정** 에서 **만들기** 를 선택 합니다.

   ![통합 계정을 만들기 위한 "추가" 선택](./media/logic-apps-enterprise-integration-create-integration-account/add-integration-account.png)

1. 통합 계정에 대해 다음과 같은 정보를 제공 합니다.

   ![통합 계정 세부 정보 제공](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-details.png)

   | 속성 | 필수 | 값 | Description |
   |----------|----------|-------|-------------|
   | **이름** | 예 | <*통합-계정 이름*> | 문자, 숫자, 하이픈 ( `-` ), 밑줄 ( `_` ), 괄호 ( `(` , `)` ) 및 마침표 ()만 포함할 수 있는 통합 계정의 이름 `.` 입니다. 이 예에서는 "Fabrikam-통합"을 사용 합니다. |
   | **구독** | 예 | <*Azure-subscription-name*> | Azure 구독의 이름 |
   | **리소스 그룹** | 예 | <*Azure-resource-group-name*> | 관련 리소스를 구성 하는 데 사용할 [Azure 리소스 그룹](../azure-resource-manager/management/overview.md) 의 이름입니다. 이 예에서는 "FabrikamIntegration-RG" 라는 이름으로 새 리소스 그룹을 만듭니다. |
   | **가격 책정 계층** | 예 | <*가격 책정 수준*> | 통합 계정에 대 한 가격 책정 계층으로, 나중에 변경할 수 있습니다. 이 예에서는 **Free** 를 선택 합니다. 자세한 내용은 다음 항목을 참조하세요. <p>- [Logic Apps 가격 책정 모델](../logic-apps/logic-apps-pricing.md#integration-accounts) <p>- [Logic Apps 제한 및 구성](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits) <p>- [Logic Apps 가격 책정](https://azure.microsoft.com/pricing/details/logic-apps/) |
   | **위치** | 예 | <*Azure-region*> | 통합 계정 메타 데이터를 저장할 지역입니다. 논리 앱과 동일한 위치를 선택 하거나 통합 계정과 동일한 위치에 논리 앱을 만듭니다. 이 예에서는 "미국 서 부"를 사용 합니다. <p>**참고** : [ise (integration service environment)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)내에서 통합 계정을 만들려면 해당 ise를 위치로 선택 합니다. 자세한 내용은 [ISE에서 통합 계정 만들기](../logic-apps/add-artifacts-integration-service-environment-ise.md#create-integration-account-environment)를 참조 하세요. |
   | **Log Analytics** | 아니요 | 꺼짐, 켜기 | 이 예에서는 **Off** 설정을 그대로 둡니다. |
   |||||

1. 작업이 완료되면 **만들기** 를 선택합니다.

   배포가 완료 되 면 Azure에서 통합 계정을 엽니다.

   ![Azure에서 통합 계정 열기](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-created.png)

1. 논리 앱에서 통합 계정을 사용 하기 전에 다음 단계를 수행 하 여 통합 계정 및 논리 앱을 함께 연결 합니다.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

이 섹션의 Azure CLI 명령을 사용 하 여 통합 계정을 만들 수 있습니다.

[!INCLUDE [azure-cli-prepare-your-environment-h3.md](../../includes/azure-cli-prepare-your-environment-h3.md)]

### <a name="create-an-integration-account"></a>통합 계정 만들기

이러한 명령을 사용 하 여 통합 계정을 만들 수 있습니다.

1. [Az 논리 통합-계정](/cli/azure/ext/logic/logic/integration-account) 확장을 추가 하려면 [az extension add](/cli/azure/extension#az_extension_add) 명령을 사용 합니다.

   ```azurecli
   az extension add –-name logic
   ```

1. 리소스 그룹을 만들거나 기존 리소스 그룹을 사용 하려면 [az group create](/cli/azure/group#az_group_create) 명령을 실행 합니다.

   ```azurecli
   az group create --name myresourcegroup --location westus
   ```

   리소스 그룹에 대 한 통합 계정을 나열 하려면 [az 논리 통합-계정 목록](/cli/azure/ext/logic/logic/integration-account#ext_logic_az_logic_integration_account_list) 명령을 사용 합니다.

   ```azurecli
   az logic integration-account list --resource-group myresourcegroup
   ```

1. 통합 계정을 만들려면 [az 논리 integration-account create](/cli/azure/ext/logic/logic/integration-account#ext_logic_az_logic_integration_account_create) 명령을 실행 합니다.

   ```azurecli
   az logic integration-account create --resource-group myresourcegroup \
       --name integration_account_01 --location westus --sku name=Standard
   ```

   통합 계정 이름에는 문자, 숫자, 하이픈 (-), 밑줄 (_), 괄호 ((,)) 및 마침표 (.)만 사용할 수 있습니다.

   > [!TIP]
   > [Ise (통합 서비스 환경)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)내에서 통합 계정을 만들려면 해당 ise를 위치로 선택 합니다. 자세한 내용은 [ISE에서 통합 계정 만들기](../logic-apps/add-artifacts-integration-service-environment-ise.md#create-integration-account-environment)를 참조 하세요.

   특정 통합 계정을 보려면 [az 논리 integration-account show](/cli/azure/ext/logic/logic/integration-account#ext_logic_az_logic_integration_account_show) 명령을 사용 합니다.

   ```azurecli
   az logic integration-account show --name integration_account_01 --resource-group myresourcegroup
   ```

   [Az 논리 integration-account update](/cli/azure/ext/logic/logic/integration-account#ext_logic_az_logic_integration_account_update) 명령을 사용 하 여 SKU 또는 가격 책정 계층을 변경할 수 있습니다.

   ```azurecli
   az logic integration-account update --sku name=Basic --name integration_account_01 \
       --resource-group myresourcegroup
   ```

   가격 책정에 대 한 자세한 내용은 다음 리소스를 참조 하세요.

   * [Logic Apps 가격 책정 모델](../logic-apps/logic-apps-pricing.md#integration-accounts)
   * [Logic Apps 제한 및 구성](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits)
   * [Logic Apps 가격 책정](https://azure.microsoft.com/pricing/details/logic-apps/)

JSON 파일을 사용 하 여 통합 계정을 가져오려면 [az 논리 통합-계정 가져오기](/cli/azure/ext/logic/logic/integration-account#ext_logic_az_logic_integration_account_import) 명령을 사용 합니다.

```azurecli
az logic integration-account import --name integration_account_01 \
    --resource-group myresourcegroup --input-path integration.json
```

[Az 논리 integration-account delete](/cli/azure/ext/logic/logic/integration-account#ext_logic_az_logic_integration_account_delete) 명령을 사용 하 여 통합 계정을 삭제할 수 있습니다.

```azurecli
az logic integration-account delete --name integration_account_01 --resource-group myresourcegroup
```

논리 앱이 통합 계정을 사용 하기 전에 통합 계정 및 논리 앱을 함께 연결 합니다. 다음 섹션에서는 연결에 대해 설명 합니다.

---
<a name="link-account"></a>

## <a name="link-to-logic-app"></a>논리 앱에 연결

논리 앱에 B2B 아티팩트가 포함 된 통합 계정에 대 한 액세스 권한을 부여 하려면 먼저 통합 계정을 논리 앱에 연결 해야 합니다. 논리 앱과 통합 계정 모두 동일한 지역에 있어야 합니다. 이 작업을 완료 하려면 Azure Portal를 사용할 수 있습니다. Visual Studio를 사용 하 고 논리 앱이 [Azure 리소스 그룹 프로젝트](../azure-resource-manager/templates/create-visual-studio-deployment-project.md)에 있는 경우 [visual studio를 사용 하 여 논리 앱을 통합 계정에 연결할](../logic-apps/manage-logic-apps-with-visual-studio.md#link-integration-account)수 있습니다.

1. Azure Portal에서 논리 앱을 찾아서 엽니다.

1. [Azure Portal](https://portal.azure.com)에서 기존 논리 앱을 열거나 새 논리 앱을 만듭니다.

1. 논리 앱의 메뉴에 있는 **설정** 아래에서 **워크플로 설정** 을 선택합니다. **통합 계정** 에서 **통합 계정 선택** 목록을 엽니다. 논리 앱에 연결할 통합 계정을 선택 합니다.

   ![통합 계정 선택](./media/logic-apps-enterprise-integration-create-integration-account/select-integration-account.png)

1. 연결을 완료 하려면 **저장** 을 선택 합니다.

   ![저장을 선택 하 여 통합 계정을 선택 하는 위치를 보여 주는 스크린샷](./media/logic-apps-enterprise-integration-create-integration-account/save-link.png)

   통합 계정이 성공적으로 연결 되 면 Azure에서 확인 메시지를 표시 합니다.

   ![Azure에서 성공적인 연결 확인](./media/logic-apps-enterprise-integration-create-integration-account/link-confirmation.png)

이제 논리 앱에서 통합 계정의 아티팩트와 XML 유효성 검사, 플랫 파일 인코딩 또는 디코딩 등의 B2B 커넥터를 사용할 수 있습니다.  

<a name="change-pricing-tier"></a>

## <a name="change-pricing-tier"></a>가격 책정 계층 변경

통합 계정에 대 한 [제한을](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits) 늘리려면 사용 가능한 경우 [더 높은 가격 책정 계층으로 업그레이드할](#upgrade-pricing-tier)수 있습니다. 예를 들어 무료 계층에서 기본 계층 또는 표준 계층으로 업그레이드할 수 있습니다. 사용 가능한 경우 [하위 계층으로 다운 그레이드할](#downgrade-pricing-tier)수도 있습니다. 가격 책정에 대 한 자세한 내용은 다음 항목을 참조 하세요.

* [Logic Apps 가격 책정](https://azure.microsoft.com/pricing/details/logic-apps/)
* [Logic Apps 가격 책정 모델](../logic-apps/logic-apps-pricing.md#integration-accounts)

<a name="upgrade-pricing-tier"></a>

### <a name="upgrade-pricing-tier"></a>가격 책정 계층 업그레이드

이러한 변경을 위해 Azure Portal 또는 Azure CLI를 사용할 수 있습니다.

#### <a name="portal"></a>[포털](#tab/azure-portal)

1. Azure 계정 자격 증명을 사용하여 [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. 기본 Azure 검색 상자에 "통합 계정"을 필터로 입력 하 고 **통합 계정** 을 선택 합니다.

   ![통합 계정 찾기](./media/logic-apps-enterprise-integration-create-integration-account/find-integration-account.png)

   Azure는 Azure 구독의 모든 통합 계정을 표시 합니다.

1. **통합 계정** 아래에서 이동하려는 통합 계정을 선택합니다. 통합 계정 메뉴에서 **개요** 를 선택 합니다.

   ![통합 계정 메뉴에서 "개요"를 선택 합니다.](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-overview.png)

1. 개요 창에서 사용 가능한 상위 계층을 나열 하는 **업그레이드 가격 책정 계층** 을 선택 합니다. 계층을 선택 하면 변경 내용이 즉시 적용 됩니다.

<a name="upgrade-tier-azure-cli"></a>

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. 아직 수행 하지 않은 경우 [Azure CLI 필수 구성 요소를 설치](/cli/azure/get-started-with-azure-cli)합니다.

1. Azure Portal에서 [Azure Cloud Shell](../cloud-shell/overview.md) 환경을 엽니다.

   ![Azure Cloud Shell 열기](./media/logic-apps-enterprise-integration-create-integration-account/open-azure-cloud-shell-window.png)

1. 명령 프롬프트에서 [ **az resource** 명령을](/cli/azure/resource#az-resource-update)입력 하 고 `skuName` 를 원하는 상위 계층으로 설정 합니다.

   ```azurecli
   az resource update --resource-group {ResourceGroupName} --resource-type Microsoft.Logic/integrationAccounts --name {IntegrationAccountName} --subscription {AzureSubscriptionID} --set sku.name={SkuName}
   ```
  
   예를 들어 기본 계층이 있는 경우를로 설정할 수 있습니다 `skuName` `Standard` .

   ```azurecli
   az resource update --resource-group FabrikamIntegration-RG --resource-type Microsoft.Logic/integrationAccounts --name Fabrikam-Integration --subscription XXXXXXXXXXXXXXXXX --set sku.name=Standard
   ```

---

<a name="downgrade-pricing-tier"></a>

### <a name="downgrade-pricing-tier"></a>가격 책정 계층 다운 그레이드

이 변경 내용을 적용 하려면 [Azure CLI](/cli/azure/get-started-with-azure-cli)을 사용 합니다.

1. 아직 수행 하지 않은 경우 [Azure CLI 필수 구성 요소를 설치](/cli/azure/get-started-with-azure-cli)합니다.

1. Azure Portal에서 [Azure Cloud Shell](../cloud-shell/overview.md) 환경을 엽니다.

   ![Azure Cloud Shell 열기](./media/logic-apps-enterprise-integration-create-integration-account/open-azure-cloud-shell-window.png)

1. 명령 프롬프트에서 [ **az resource** 명령을](/cli/azure/resource#az-resource-update) 입력 하 고 `skuName` 원하는 하위 계층으로 설정 합니다.

   ```azurecli
   az resource update --resource-group <resourceGroupName> --resource-type Microsoft.Logic/integrationAccounts --name <integrationAccountName> --subscription <AzureSubscriptionID> --set sku.name=<skuName>
   ```
  
   예를 들어 표준 계층이 있는 경우를로 설정할 수 있습니다 `skuName` `Basic` .

   ```azurecli
   az resource update --resource-group FabrikamIntegration-RG --resource-type Microsoft.Logic/integrationAccounts --name Fabrikam-Integration --subscription XXXXXXXXXXXXXXXXX --set sku.name=Basic
   ```

## <a name="unlink-from-logic-app"></a>논리 앱에서 연결 해제

논리 앱을 다른 통합 계정에 연결 하거나 논리 앱과 함께 통합 계정을 더 이상 사용 하지 않으려면 Azure Resource Explorer를 사용 하 여 링크를 삭제 합니다.

1. 브라우저 창을 열고 [Azure Resource Explorer ( https://resources.azure.com) ](https://resources.azure.com)로 이동 합니다. 동일한 Azure 계정 자격 증명을 사용 하 여 로그인 합니다.

   ![Azure Resource Explorer](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer.png)

1. 검색 상자에서 논리 앱의 이름을 입력 하 여 논리 앱을 찾아 선택할 수 있습니다.

   ![논리 앱 찾기 및 선택](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-find-logic-app.png)

1. 탐색기 제목 표시줄에서 **읽기/쓰기** 를 선택 합니다.

   !["읽기/쓰기" 모드 켜기](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-select-read-write.png)

1. **데이터** 탭에서 **편집** 을 선택 합니다.

   !["데이터" 탭에서 "편집"을 선택 합니다.](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-select-edit.png)

1. 편집기에서 개체를 찾고 `integrationAccount` 해당 속성을 삭제 합니다 .이 형식을 가진 속성은 다음과 같습니다.

   ```json
   {
      // <other-attributes>
      "integrationAccount": {
         "name": "<integration-account-name>",
         "id": "<integration-account-resource-ID>",
         "type": "Microsoft.Logic/integrationAccounts"  
   },
   ```

   예를 들면 다음과 같습니다.

   !["IntegrationAccount" 개체 찾기](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-delete-integration-account.png)

1. **데이터** 탭에서 **Put** 을 선택 하 여 변경 내용을 저장 합니다.

   ![변경 내용을 저장 하려면 "Put"을 선택 합니다.](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-save-changes.png)

1. Azure Portal에서 논리 앱을 찾고 선택합니다. 앱의 **워크플로 설정** 에서 **통합 계정** 속성이 이제 빈 상태로 표시 되는지 확인 합니다.

   ![통합 계정이 연결되어 있지 않은지 확인](./media/logic-apps-enterprise-integration-create-integration-account/unlinked-account.png)

## <a name="move-integration-account"></a>통합 계정 이동

통합 계정을 다른 Azure 리소스 그룹 또는 Azure 구독으로 이동할 수 있습니다. 리소스를 이동 하면 Azure에서 새 리소스 Id를 만들기 때문에 새 Id를 대신 사용 하 고 이동 된 리소스와 관련 된 스크립트나 도구를 업데이트 해야 합니다. 구독을 변경 하려면 기존 또는 새 리소스 그룹도 지정 해야 합니다.

이 작업의 경우이 섹션의 단계 또는 [Azure CLI](/cli/azure/resource#az-resource-move)을 수행 하 여 Azure Portal를 사용할 수 있습니다.

1. Azure 계정 자격 증명을 사용하여 [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. 기본 Azure 검색 상자에 "통합 계정"을 필터로 입력 하 고 **통합 계정** 을 선택 합니다.

   ![통합 계정 찾기](./media/logic-apps-enterprise-integration-create-integration-account/find-integration-account.png)

   Azure는 Azure 구독의 모든 통합 계정을 표시 합니다.

1. **통합 계정** 아래에서 이동하려는 통합 계정을 선택합니다. 통합 계정 메뉴에서 **개요** 를 선택 합니다.

   ![통합 계정 메뉴에서 "개요"를 선택 합니다.](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-overview.png)

1. **리소스 그룹** 또는 **구독 이름** 옆에 있는 **변경** 을 선택 합니다.

   ![리소스 그룹 또는 구독 변경](./media/logic-apps-enterprise-integration-create-integration-account/change-resource-group-subscription.png)

1. 이동 하려는 관련 리소스를 선택 합니다.

1. 선택 사항에 따라 다음 단계를 수행 하 여 리소스 그룹 또는 구독을 변경 합니다.

   * 리소스 그룹: **리소스 그룹** 목록에서 대상 리소스 그룹을 선택 합니다. 또는 다른 리소스 그룹을 만들려면 **새 리소스 그룹 만들기** 를 선택 합니다.

   * 구독: **구독** 목록에서 대상 구독을 선택 합니다. **리소스 그룹** 목록에서 대상 리소스 그룹을 선택 합니다. 또는 다른 리소스 그룹을 만들려면 **새 리소스 그룹 만들기** 를 선택 합니다.

1. 이동 된 리소스와 관련 된 스크립트나 도구가 새 리소스 Id로 업데이트 될 때까지 작동 하지 않는다는 것을 이해 하려면 확인 상자를 선택 하 고 **확인** 을 선택 합니다.

1. 작업을 완료 한 후에는 이동 된 리소스에 대 한 새 리소스 Id를 사용 하 여 및 모든 스크립트를 업데이트 해야 합니다.  

## <a name="delete-integration-account"></a>통합 계정 삭제

이 작업의 경우이 섹션의 단계, [Azure CLI](/cli/azure/resource#az-resource-delete)또는 [Azure PowerShell](/powershell/module/az.logicapp/remove-azintegrationaccount)따라 Azure Portal를 사용할 수 있습니다.

1. Azure 계정 자격 증명을 사용하여 [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. 기본 Azure 검색 상자에 "통합 계정"을 필터로 입력 하 고 **통합 계정** 을 선택 합니다.

   ![통합 계정 찾기](./media/logic-apps-enterprise-integration-create-integration-account/find-integration-account.png)

   Azure는 Azure 구독의 모든 통합 계정을 표시 합니다.

1. **통합 계정** 아래에서 삭제하려는 통합 계정을 선택합니다. 통합 계정 메뉴에서 **개요** 를 선택 합니다.

   ![통합 계정 메뉴에서 "개요"를 선택 합니다.](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-overview.png)

1. 개요 창에서 **삭제** 를 선택 합니다.

   !["개요" 창에서 "삭제"를 선택 합니다.](./media/logic-apps-enterprise-integration-create-integration-account/delete-integration-account.png)

1. 통합 계정을 삭제할 것임을 확인 하려면 **예** 를 선택 합니다.

   ![삭제를 확인 하려면 "예"를 선택 합니다.](./media/logic-apps-enterprise-integration-create-integration-account/confirm-delete.png)

## <a name="next-steps"></a>다음 단계

* [통합 계정에 거래 파트너 만들기](../logic-apps/logic-apps-enterprise-integration-partners.md)
* [통합 계정에서 파트너 간의 규약 만들기](../logic-apps/logic-apps-enterprise-integration-agreements.md)
