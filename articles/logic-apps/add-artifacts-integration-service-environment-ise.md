---
title: 통합 서비스 환경에 리소스 추가
description: ISE(통합 서비스 환경)에 논리 앱, 통합 계정, 사용자 지정 커넥터 및 관리형 커넥터를 추가합니다.
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 02/28/2020
ms.openlocfilehash: 147247c663311cfb3e05a986c6fb2bffbb41158b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92675205"
---
# <a name="add-resources-to-your-integration-service-environment-ise-in-azure-logic-apps"></a>Azure Logic Apps에서 ISE(통합 서비스 환경)에 리소스 추가

[ISE(통합 서비스 환경)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)를 만든 후에는 Azure 가상 네트워크의 리소스에 액세스할 수 있도록 논리 앱, 통합 계정, 커넥터와 같은 리소스를 추가합니다. 예를 들어, ISE를 만든 후 사용 가능한 관리형 ISE 커넥터는 논리 앱 디자이너에 자동으로 나타나지 않습니다. 이러한 ISE 커넥터를 사용할 수 있으려면 먼저 논리 앱 디자이너에 표시되도록 [이러한 커넥터를 ISE에 추가 및 배포](#add-ise-connectors-environment)해야 합니다.

> [!IMPORTANT]
> 논리 앱과 통합 계정이 ISE에서 함께 작동하려면 둘 다 각자 위치와 동일한 ISE를 사용해야 합니다.

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독 Azure 구독이 없는 경우 [체험 Azure 계정에 등록](https://azure.microsoft.com/free/)합니다.

* 논리 앱을 실행하기 위해 만든 ISE입니다. ISE가 없으면 [먼저 ISE를 만듭니다](../logic-apps/connect-virtual-network-vnet-isolated-environment.md).

* ISE에 배포된 리소스를 생성, 추가 또는 업데이트하려면 해당 ISE에 소유자 또는 기여자 역할을 할당하거나 ISE와 연결된 Azure 구독 또는 Azure 리소스 그룹을 통해 상속된 사용 권한이 있어야 합니다. 소유자 또는 기여자 역할이나 상속된 사용 권한이 없는 사용자의 경우 통합 서비스 환경 기여자 역할 또는 통합 서비스 환경 개발자 역할에 할당될 수 있습니다. 자세한 내용은 [Azure RBAC(Azure 역할 기반 액세스 제어)란?](../role-based-access-control/overview.md)을 참조하세요.

<a name="create-logic-apps-environment"></a>

## <a name="create-logic-apps"></a>Logic Apps 만들기

ISE(통합 서비스 환경)에서 실행되는 논리 앱을 빌드하려면 다음 단계를 수행합니다.

1. 아직 열지 않은 경우 ISE를 찾아 엽니다. ISE 메뉴의 **설정** 에서 **논리 앱** > **추가** 를 선택합니다.

   ![ISE에 새 논리 앱 추가](./media/add-artifacts-integration-service-environment-ise/add-logic-app-to-ise.png)

1. 만들려는 논리 앱에 대한 정보를 입력합니다. 예를 들면 다음과 같습니다.

   ![예제 정보가 입력된 “논리 앱” “만들기” 창을 보여 주는 스크린샷](./media/add-artifacts-integration-service-environment-ise/create-logic-app-integration-service-environment.png)

   | 속성 | 필수 | 설명 |
   |----------|----------|-------------|
   | **이름** | 예 | 만들 논리 앱의 이름 |
   | **구독** | 예 | 사용할 Azure 구독의 이름입니다. |
   | **리소스 그룹** | 예 | 사용할 (신규 또는 기존) Azure 리소스 그룹의 이름 |
   | **위치** | 예 | **통합 서비스 환경** 에서 아직 선택하지 않은 경우 사용할 ISE를 선택합니다. <p><p> **중요**: 논리 앱을 통합 계정과 함께 사용하려면 둘 다 동일한 ISE를 사용해야 합니다. |
   ||||

1. 완료되면 **만들기** 를 선택합니다.

1. [일반적인 방법으로 논리 앱을 계속 만듭니다](../logic-apps/quickstart-create-first-logic-app-workflow.md).

   다중 테넌트 Logic Apps 서비스에 비해 ISE를 사용하는 경우 트리거와 작업의 작동 방식과 트리거와 작업에 레이블이 지정되는 방식에 대한 차이점은 [ISE 개요에서 격리된 테넌트와 다중 테넌트 비교](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#difference)를 참조하세요.

1. ISE에서 논리 앱과 API 연결을 관리하려면 [통합 서비스 환경 관리](../logic-apps/ise-manage-integration-service-environment.md)를 참조하세요.

<a name="create-integration-account-environment"></a>

## <a name="create-integration-accounts"></a>통합 계정 만들기

생성 시 선택한 [ISE SKU](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level)에 따라 ISE에 추가 비용 없이 특정 통합 계정 사용이 포함됩니다. ISE(통합 서비스 환경)에 있는 논리 앱은 동일한 ISE에 있는 통합 계정만 참조할 수 있습니다. 따라서 통합 계정이 ISE에서 논리 앱과 작동하려면 통합 계정과 논리 앱 모두 해당 위치와 *동일한 환경을* 사용해야 합니다. 통합 계정과 ISE에 대한 자세한 내용은 [ISE와 통합 계정](connect-virtual-network-vnet-isolated-environment-overview.md#create-integration-account-environment)을 참조하세요.

ISE를 사용하는 통합 계정을 만들려면 다음 단계를 수행합니다.

1. 아직 열지 않은 경우 ISE를 찾아 엽니다. ISE 메뉴의 **설정** 에서 **통합 계정** > **추가** 를 선택합니다.

   ![ISE에 새 통합 계정 추가](./media/add-artifacts-integration-service-environment-ise/add-integration-account-to-ise.png)

1. 만들려는 논리 앱에 대한 정보를 입력합니다. 예를 들면 다음과 같습니다.

   ![통합 서비스 환경 선택](./media/add-artifacts-integration-service-environment-ise/create-integration-account-integration-service-environment.png)

   | 속성 | 필수 | 설명 |
   |----------|----------|-------------|
   | **이름** | 예 | 만들려는 통합 계정의 이름 |
   | **구독** | 예 | 사용하려는 Azure 구독의 이름 |
   | **리소스 그룹** | 예 | 사용할 (신규 또는 기존) Azure 리소스 그룹의 이름 |
   | **가격 책정 계층** | 예 | 통합 계정에 사용할 가격 책정 계층 |
   | **위치** | 예 | **통합 서비스 환경** 에서 아직 선택하지 않은 경우 논리 앱에서 사용하는 것과 동일한 ISE를 선택합니다. <p><p> **중요**: 논리 앱과 함께 통합 계정을 사용하려면 둘 다 동일한 ISE를 사용해야 합니다. |
   ||||

1. 완료되면 **만들기** 를 선택합니다.

1. [일반적인 방법으로 통합 계정에 논리 앱을 연결합니다](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account).

1. [거래 업체](../logic-apps/logic-apps-enterprise-integration-partners.md)와 [계약](../logic-apps/logic-apps-enterprise-integration-agreements.md) 등과 같은 통합 계정에 리소스를 추가하여 계속합니다.

1. ISE에서 통합 계정을 관리하려면 [통합 서비스 환경 관리](../logic-apps/ise-manage-integration-service-environment.md)를 참조하세요.

<a name="add-ise-connectors-environment"></a>

## <a name="add-ise-connectors"></a>ISE 커넥터 추가

ISE를 만든 후 관리형 ISE 커넥터는 논리 앱 디자이너의 커넥터 선택기에 자동으로 나타나지 않습니다. 이러한 ISE 커넥터를 사용할 수 있으려면 먼저 논리 앱 디자이너에 표시되도록 이러한 커넥터를 ISE에 추가 및 배포해야 합니다.

> [!IMPORTANT]
> 관리형 ISE 커넥터는 현재 [태그](../azure-resource-manager/management/tag-support.md)를 지원하지 않습니다. 태그 지정을 적용하는 정책을 설정하는 경우 ISE 커넥터를 추가하려고 하면 다음과 같은 오류와 함께 실패할 수 있습니다.
> 
> ```json
> {
>    "error": { 
>       "code": "IntergrationServiceEnvironmentManagedApiDefinitionTagsNotSupported", 
>       "message": "The tags are not supported in the managed API 'azureblob'."
>    }
> }
> ```
> 
> 따라서 ISE 커넥터를 추가하려면 정책을 사용하지 않도록 설정 하거나 제거해야 합니다. 

1. ISE 메뉴의 **설정** 에서 **관리형 커넥터** 를 선택합니다. 도구 모음에서 **추가** 를 선택합니다.

   ![관리형 커넥터 보기](./media/add-artifacts-integration-service-environment-ise/ise-view-managed-connectors.png)

1. **새 관리형 커넥터 추가** 창에서 **커넥터 찾기** 목록을 엽니다. 사용할 ISE 커넥터를 선택하지만 ISE에서 아직 배포되지 않았습니다. **만들기** 를 선택합니다.

   ![ISE에서 배포할 ISE 커넥터를 선택합니다.](./media/add-artifacts-integration-service-environment-ise/add-managed-connector.png)

   적합하지만 ISE에 아직 배포되지 않은 ISE 커넥터만 사용자가 선택할 수 있는 항목으로 나타납니다. ISE에서 이미 배포된 커넥터는 선택할 수 없는 항목으로 나타납니다.

<a name="create-custom-connectors-environment"></a>

## <a name="create-custom-connectors"></a>사용자 지정 커넥터 만들기

ISE에서 사용자 지정 커넥터를 사용하려면 ISE 내에서 직접 사용자 지정 커넥터를 만듭니다.

1. 아직 열지 않은 경우 ISE를 찾아 엽니다. ISE 메뉴의 **설정** 에서 **사용자 지정 커넥터** > **추가** 를 선택합니다.

   ![사용자 지정 커넥터 만들기](./media/add-artifacts-integration-service-environment-ise/add-custom-connector-to-ise.png)

1. 사용자 지정 커넥터에 사용할 이름, Azure 구독 및 Azure 리소스 그룹(신규 또는 기존)을 제공합니다.

1. **위치** 목록의 **통합 서비스 환경** 섹션에서 논리 앱이 사용하는 동일한 ISE를 선택하고 **만들기** 를 선택합니다. 예를 들면 다음과 같습니다.

   ![예제 정보를 선택한 상태로 “Logic Apps 사용자 지정 커넥터 만들기” 창을 보여 주는 스크린샷](./media/add-artifacts-integration-service-environment-ise/create-custom-connector-integration-service-environment.png)

1. 새 사용자 지정 커넥터를 선택하고 **편집** 을 선택합니다. 예를 들면 다음과 같습니다.

   ![사용자 지정 커넥터 선택 및 편집](./media/add-artifacts-integration-service-environment-ise/edit-custom-connectors.png)

1. [OpenAPI 정의](/connectors/custom-connectors/define-openapi-definition#import-the-openapi-definition) 또는 [SOAP](/connectors/custom-connectors/create-register-logic-apps-soap-connector#2-define-your-connector)에서 일반적인 방법으로 커넥터를 만들어 계속합니다.

1. ISE에서 사용자 지정 커넥터를 관리하려면 [통합 서비스 환경 관리](../logic-apps/ise-manage-integration-service-environment.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

* [통합 서비스 환경 관리](../logic-apps/ise-manage-integration-service-environment.md)
