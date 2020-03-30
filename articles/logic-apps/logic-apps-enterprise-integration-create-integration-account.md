---
title: B2B 통합 계정 생성 또는 관리
description: Azure Logic Apps를 사용한 엔터프라이즈 통합을 위한 통합 계정 생성, 연결 및 관리
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, logicappspm
ms.topic: conceptual
ms.date: 07/26/2019
ms.openlocfilehash: 083ed0001adb5524c124295eb3bc31f4afad99cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79270330"
---
# <a name="create-and-manage-integration-accounts-for-b2b-enterprise-integrations-in-azure-logic-apps"></a>Azure 논리 앱에서 B2B 엔터프라이즈 통합을 위한 통합 계정 생성 및 관리

[Azure Logic Apps](../logic-apps/logic-apps-overview.md)를 사용하여 [엔터프라이즈 통합 및 B2B 솔루션](../logic-apps/logic-apps-enterprise-integration-overview.md)을 빌드하려면 먼저 통합 계정을 만들어야 합니다. 이 계정은 논리 앱 워크플로에서 정의하고 사용할 수 있는 통합 아티팩트에 대한 안전하고 확장 가능하며 관리할 수 있는 컨테이너를 제공하는 별도의 Azure 리소스입니다.

예를 들어 거래 파트너, 계약, 맵, 스키마, 인증서 및 일괄 처리 구성과 같은 B2B 아티팩트를 생성, 저장 및 관리할 수 있습니다. 또한 논리 앱이 이러한 아티팩트로 작업하고 논리 앱 B2B 커넥터를 사용하려면 먼저 통합 계정을 논리 앱에 [연결해야](#link-account) 합니다. 통합 계정과 논리 앱은 *모두 동일한* 위치 또는 지역에 있어야 합니다.

> [!TIP]
> [통합 서비스 환경](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)내에서 통합 계정을 만들려면 [ISE의 통합 계정 만들기를](../logic-apps/add-artifacts-integration-service-environment-ise.md#create-integration-account-environment)참조하십시오.

이 항목에서는 이러한 작업을 수행하는 방법을 보여 주며 다음과 같은 작업을 수행합니다.

* 통합 계정을 만듭니다.
* 통합 계정을 논리 앱에 연결합니다.
* 통합 계정의 가격 책정 계층을 변경합니다.
* 논리 앱에서 통합 계정 연결을 해제합니다.
* 통합 계정을 다른 Azure 리소스 그룹 또는 구독으로 이동합니다.
* 통합 계정을 삭제합니다.

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독 Azure 구독이 없는 경우 [체험 Azure 계정에 등록](https://azure.microsoft.com/free/)합니다.

## <a name="create-integration-account"></a>통합 계정 만들기

이 작업의 경우 이 섹션의 단계, [Azure PowerShell](/powershell/module/Az.LogicApp/New-AzIntegrationAccount)또는 [Azure CLI의](https://docs.microsoft.com/cli/azure/resource?view=azure-cli-latest#az-resource-create)단계를 수행하여 Azure 포털을 사용할 수 있습니다.

1. Azure 계정 자격 증명을 사용하여 [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. Azure 기본 메뉴에서 **리소스 만들기**를 선택합니다. 검색 상자에서 필터로 "통합 계정"을 입력하고 **통합 계정을**선택합니다.

   ![새 통합 계정 만들기](./media/logic-apps-enterprise-integration-create-integration-account/create-integration-account.png)

1. **통합 계정에서** **만들기를**선택합니다.

   ![통합 계정을 만들기 위한 "추가" 선택](./media/logic-apps-enterprise-integration-create-integration-account/add-integration-account.png)

1. 통합 계정에 대한 이 정보를 제공합니다.

   ![통합 계정 세부 정보 제공](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-details.png)

   | 속성 | 필수 | 값 | 설명 |
   |----------|----------|-------|-------------|
   | **이름** | yes | <*통합-계정 이름*> | 문자, 숫자, 하이픈`-`(), 밑줄 (),`_`괄호 (,`(` `)`괄호) 및 마침표`.`()만 포함 할 수있는 통합 계정의 이름입니다. 이 예제에서는 "Fabrikam-통합"을 사용합니다. |
   | **구독** | yes | <*Azure 구독 이름*> | Azure 구독의 이름 |
   | **리소스 그룹** | yes | <*Azure-리소스 그룹 이름*> | 관련 [리소스를](../azure-resource-manager/management/overview.md) 구성하는 데 사용할 Azure 리소스 그룹의 이름입니다. 이 예제에서는 "FabrikamIntegration-RG"라는 이름으로 새 리소스 그룹을 만듭니다. |
   | **가격 책정 계층** | yes | <*가격 수준*> | 나중에 변경할 수 있는 통합 계정의 가격 책정 계층입니다. 이 예제에서는 무료 를 **선택합니다.** 자세한 내용은 다음 항목을 참조하세요. <p>- [논리 앱 가격 책정 모델](../logic-apps/logic-apps-pricing.md#integration-accounts) <p>- [로직 앱 제한 및 구성](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits) <p>- [로직 앱 가격 책정](https://azure.microsoft.com/pricing/details/logic-apps/) |
   | **위치** | yes | <*Azure 지역*> | 통합 계정 메타데이터를 저장할 리전입니다. 논리 앱과 동일한 위치를 선택하거나 통합 계정과 동일한 위치에 논리 앱을 만듭니다. 이 예제에서는 "미국 서부"를 사용합니다. <p>**참고**: [통합 서비스 환경(ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)내에서 통합 계정을 만들려면 해당 ISE를 위치로 선택합니다. 자세한 내용은 [ISE의 통합 계정 만들기를](../logic-apps/add-artifacts-integration-service-environment-ise.md#create-integration-account-environment)참조하십시오. |
   | **Log Analytics** | 예 | 꺼기, 켜기 | 이 예제의 **꺼기** 설정을 유지합니다. |
   |||||

1. 완료되면 에서 **만들기를**선택합니다.

   배포가 완료되면 Azure는 통합 계정을 엽니다.

   ![Azure가 통합 계정을 엽니다.](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-created.png)

1. 논리 앱에서 통합 계정을 사용하려면 다음 단계를 수행하여 통합 계정과 논리 앱을 함께 연결합니다.

<a name="link-account"></a>

## <a name="link-to-logic-app"></a>논리 앱에 연결

논리 앱에 B2B 아티팩트가 포함된 통합 계정에 대한 액세스 권한을 부여하려면 먼저 통합 계정을 논리 앱에 연결해야 합니다. 논리 앱과 통합 계정은 모두 동일한 지역에 있어야 합니다. 이 작업을 완료하려면 Azure 포털을 사용할 수 있습니다. Visual Studio를 사용하고 논리 앱이 [Azure 리소스 그룹 프로젝트에](../azure-resource-manager/templates/create-visual-studio-deployment-project.md)있는 경우 [Visual Studio를 사용하여 논리 앱을 통합 계정에 연결할](../logic-apps/manage-logic-apps-with-visual-studio.md#link-integration-account)수 있습니다.

1. Azure Portal에서 논리 앱을 찾아서 엽니다.

1. Azure [Portal에서](https://portal.azure.com)기존 논리 앱을 열거나 새 논리 앱을 만듭니다.

1. 논리 앱의 메뉴에 있는 **설정** 아래에서 **워크플로 설정**을 선택합니다. **통합 계정에서**통합 계정 선택 목록을 **엽니다.** 논리 앱에 연결하려면 통합 계정을 선택합니다.

   ![통합 계정 선택](./media/logic-apps-enterprise-integration-create-integration-account/select-integration-account.png)

1. 연결을 완료하려면 **저장을**선택합니다.

   ![통합 계정 선택](./media/logic-apps-enterprise-integration-create-integration-account/save-link.png)

   통합 계정이 성공적으로 연결되면 Azure에 확인 메시지가 표시됩니다.

   ![Azure에서 성공적인 연결 확인](./media/logic-apps-enterprise-integration-create-integration-account/link-confirmation.png)

이제 논리 앱은 통합 계정의 아티팩트와 XML 유효성 검사 및 플랫 파일 인코딩 또는 디코딩과 같은 B2B 커넥터를 사용할 수 있습니다.  

<a name="change-pricing-tier"></a>

## <a name="change-pricing-tier"></a>가격 책정 계층 변경

통합 계정의 [한도를](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits) 늘리려면 사용 가능한 경우 [더 높은 가격 책정 계층으로 업그레이드할](#upgrade-pricing-tier)수 있습니다. 예를 들어 프리 티어에서 기본 계층 또는 표준 계층으로 업그레이드할 수 있습니다. 사용 가능한 경우 [하위 계층으로 다운그레이드할](#downgrade-pricing-tier)수도 있습니다. 가격 책정 에 대한 자세한 내용은 다음 항목을 참조하십시오.

* [로직 앱 가격 책정](https://azure.microsoft.com/pricing/details/logic-apps/)
* [Logic Apps 가격 책정 모델](../logic-apps/logic-apps-pricing.md#integration-accounts)

<a name="upgrade-pricing-tier"></a>

### <a name="upgrade-pricing-tier"></a>가격 책정 계층 업그레이드

이렇게 하려면 이 섹션의 단계 또는 [Azure CLI](#upgrade-tier-azure-cli)의 단계를 수행하여 Azure 포털을 사용할 수 있습니다.

#### <a name="azure-portal"></a>Azure portal

1. Azure 계정 자격 증명을 사용하여 [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. 기본 Azure 검색 상자에서 "통합 계정"을 필터로 입력하고 **통합 계정을**선택합니다.

   ![통합 계정 찾기](./media/logic-apps-enterprise-integration-create-integration-account/find-integration-account.png)

   Azure는 Azure 구독의 모든 통합 계정을 표시합니다.

1. **통합 계정** 아래에서 이동하려는 통합 계정을 선택합니다. 통합 계정 메뉴에서 **개요를**선택합니다.

   ![통합 계정 메뉴에서 "개요"를 선택합니다.](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-overview.png)

1. 개요 창에서 사용 가능한 상위 계층을 나열하는 **가격 책정 계층 업그레이드를**선택합니다. 티어를 선택하면 변경 이 즉시 적용됩니다.

<a name="upgrade-tier-azure-cli"></a>

#### <a name="azure-cli"></a>Azure CLI

1. 아직 설치하지 않은 경우 [Azure CLI 필수 구성 조건을 설치합니다.](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest)

1. Azure 포털에서 Azure [**클라우드 셸**](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest) 환경을 엽니다.

   ![Azure Cloud Shell 열기](./media/logic-apps-enterprise-integration-create-integration-account/open-azure-cloud-shell-window.png)

1. 명령 프롬프트에서 [ **az 리소스** 명령을](https://docs.microsoft.com/cli/azure/resource?view=azure-cli-latest#az-resource-update)입력하고 원하는 상위 계층으로 설정합니다. `skuName`

   ```azurecli
   az resource update --resource-group {ResourceGroupName} --resource-type Microsoft.Logic/integrationAccounts --name {IntegrationAccountName} --subscription {AzureSubscriptionID} --set sku.name={SkuName}
   ```
  
   예를 들어 기본 계층이 있는 경우 `skuName` 다음으로 `Standard`설정할 수 있습니다.

   ```azurecli
   az resource update --resource-group FabrikamIntegration-RG --resource-type Microsoft.Logic/integrationAccounts --name Fabrikam-Integration --subscription XXXXXXXXXXXXXXXXX --set sku.name=Standard
   ```

<a name="downgrade-pricing-tier"></a>

### <a name="downgrade-pricing-tier"></a>가격 책정 계층 하향 조정

이렇게 하려면 [Azure CLI를](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest)사용합니다.

1. 아직 설치하지 않은 경우 [Azure CLI 필수 구성 조건을 설치합니다.](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest)

1. Azure 포털에서 Azure [**클라우드 셸**](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest) 환경을 엽니다.

   ![Azure Cloud Shell 열기](./media/logic-apps-enterprise-integration-create-integration-account/open-azure-cloud-shell-window.png)

1. 명령 프롬프트에서 [ **az 리소스** 명령을](https://docs.microsoft.com/cli/azure/resource?view=azure-cli-latest#az-resource-update) 입력하고 원하는 하위 계층으로 설정합니다. `skuName`

   ```azurecli
   az resource update --resource-group <resourceGroupName> --resource-type Microsoft.Logic/integrationAccounts --name <integrationAccountName> --subscription <AzureSubscriptionID> --set sku.name=<skuName>
   ```
  
   예를 들어 표준 계층이 있는 경우 `skuName` 다음으로 `Basic`설정할 수 있습니다.

   ```azurecli
   az resource update --resource-group FabrikamIntegration-RG --resource-type Microsoft.Logic/integrationAccounts --name Fabrikam-Integration --subscription XXXXXXXXXXXXXXXXX --set sku.name=Basic
   ```

## <a name="unlink-from-logic-app"></a>논리 앱에서 연결 해제

논리 앱을 다른 통합 계정에 연결하거나 논리 앱과의 통합 계정을 더 이상 사용하지 않으려면 Azure Resource Explorer를 사용하여 링크를 삭제합니다.

1. 브라우저 창을 열고 Azure [리소스 탐색기https://resources.azure.com)(.](https://resources.azure.com) 동일한 Azure 계정 자격 증명으로 로그인합니다.

   ![Azure Resource Explorer](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer.png)

1. 검색 상자에 논리 앱의 이름을 입력하여 논리 앱을 찾고 선택할 수 있습니다.

   ![논리 앱 찾기 및 선택](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-find-logic-app.png)

1. 탐색기 제목 표시줄에서 **읽기/쓰기**를 선택합니다.

   !["읽기/쓰기" 모드 켜기](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-select-read-write.png)

1. **데이터** 탭에서 **편집을**선택합니다.

   !["데이터" 탭에서 "편집"을 선택합니다.](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-select-edit.png)

1. 편집기에서 개체를 `integrationAccount` 찾아 다음 형식이 있는 해당 속성을 삭제합니다.

   ```json
   {
      // <other-attributes>
      "integrationAccount": {
         "name": "<integration-account-name>",
         "id": "<integration-account-resource-ID>",
         "type": "Microsoft.Logic/integrationAccounts"  
   },
   ```

   예를 들어:

   ![찾기 "통합계정" 개체](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-delete-integration-account.png)

1. **데이터** 탭에서 변경 내용을 저장하려면 **넣기를** 선택합니다.

   ![변경 내용을 저장하려면 "넣기"를 선택합니다.](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-save-changes.png)

1. Azure Portal에서 논리 앱을 찾고 선택합니다. 앱의 **워크플로 설정에서**통합 **계정** 속성이 비어 있는지 확인합니다.

   ![통합 계정이 연결되어 있지 않은지 확인](./media/logic-apps-enterprise-integration-create-integration-account/unlinked-account.png)

## <a name="move-integration-account"></a>통합 계정 이동

통합 계정을 다른 Azure 리소스 그룹 또는 Azure 구독으로 이동할 수 있습니다. 리소스를 이동할 때 Azure는 새 리소스 를 생성하므로 새 아이디를 대신 사용하고 이동된 리소스와 연결된 스크립트 또는 도구를 업데이트해야 합니다. 구독을 변경하려면 기존 또는 새 리소스 그룹도 지정해야 합니다.

이 작업의 경우 이 섹션의 단계를 따르거나 [Azure CLI](https://docs.microsoft.com/cli/azure/resource?view=azure-cli-latest#az-resource-move)를 사용하여 Azure 포털을 사용할 수 있습니다.

1. Azure 계정 자격 증명을 사용하여 [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. 기본 Azure 검색 상자에서 "통합 계정"을 필터로 입력하고 **통합 계정을**선택합니다.

   ![통합 계정 찾기](./media/logic-apps-enterprise-integration-create-integration-account/find-integration-account.png)

   Azure는 Azure 구독의 모든 통합 계정을 표시합니다.

1. **통합 계정** 아래에서 이동하려는 통합 계정을 선택합니다. 통합 계정 메뉴에서 **개요를**선택합니다.

   ![통합 계정 메뉴에서 "개요"를 선택합니다.](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-overview.png)

1. **리소스 그룹** 또는 **구독 이름**옆에 있는 **변경을**선택합니다.

   ![리소스 그룹 또는 구독 변경](./media/logic-apps-enterprise-integration-create-integration-account/change-resource-group-subscription.png)

1. 이동하려는 관련 리소스를 선택합니다.

1. 선택한 단계에 따라 리소스 그룹 또는 구독을 변경합니다.

   * 리소스 그룹: **리소스 그룹** 목록에서 대상 리소스 그룹을 선택합니다. 또는 다른 리소스 그룹을 만들려면 **새 리소스 그룹 만들기를**선택합니다.

   * 구독: **구독** 목록에서 대상 구독을 선택합니다. 리소스 **그룹** 목록에서 대상 리소스 그룹을 선택합니다. 또는 다른 리소스 그룹을 만들려면 **새 리소스 그룹 만들기를**선택합니다.

1. 이동된 리소스와 연결된 스크립트 또는 도구가 새 리소스 아이디로 업데이트할 때까지 작동하지 않는다는 것을 이해하려면 확인 상자를 선택한 다음 **확인을**선택합니다.

1. 완료한 후에는 이동한 리소스에 대한 새 리소스 아이디로 모든 스크립트를 업데이트해야 합니다.  

## <a name="delete-integration-account"></a>통합 계정 삭제

이 작업의 경우 이 섹션의 단계, [Azure CLI](https://docs.microsoft.com/cli/azure/resource?view=azure-cli-latest#az-resource-delete)또는 [Azure PowerShell](/powershell/module/az.logicapp/remove-azintegrationaccount)의 단계를 수행하여 Azure 포털을 사용할 수 있습니다.

1. Azure 계정 자격 증명을 사용하여 [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. 기본 Azure 검색 상자에서 "통합 계정"을 필터로 입력하고 **통합 계정을**선택합니다.

   ![통합 계정 찾기](./media/logic-apps-enterprise-integration-create-integration-account/find-integration-account.png)

   Azure는 Azure 구독의 모든 통합 계정을 표시합니다.

1. **통합 계정** 아래에서 삭제하려는 통합 계정을 선택합니다. 통합 계정 메뉴에서 **개요를**선택합니다.

   ![통합 계정 메뉴에서 "개요"를 선택합니다.](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-overview.png)

1. 개요 창에서 **삭제를**선택합니다.

   !["개요" 창에서 "삭제"를 선택합니다.](./media/logic-apps-enterprise-integration-create-integration-account/delete-integration-account.png)

1. 통합 계정을 삭제하려는지 확인하려면 **예를**선택합니다.

   ![삭제를 확인하려면 "예"를 선택합니다.](./media/logic-apps-enterprise-integration-create-integration-account/confirm-delete.png)

## <a name="next-steps"></a>다음 단계

* [통합 계정에서 거래 파트너 만들기](../logic-apps/logic-apps-enterprise-integration-partners.md)
* [통합 계정의 파트너 간 계약 만들기](../logic-apps/logic-apps-enterprise-integration-agreements.md)
