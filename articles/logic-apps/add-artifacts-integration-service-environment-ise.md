---
title: 통합 서비스 환경에 리소스 추가
description: ISE(통합 서비스 환경)에 논리 앱, 통합 계정, 사용자 지정 커넥터 및 관리형 커넥터 추가
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 02/28/2020
ms.openlocfilehash: 58d2efd0c61045739930ce36ba317b1aa6a40ce8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79164879"
---
# <a name="add-resources-to-your-integration-service-environment-ise-in-azure-logic-apps"></a>Azure 논리 앱의 ISE(통합 서비스 환경)에 리소스 추가

[ISE(통합 서비스 환경)를](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)만든 후 Azure 가상 네트워크의 리소스에 액세스할 수 있도록 논리 앱, 통합 계정 및 커넥터와 같은 리소스를 추가합니다. 예를 들어 ISE를 만든 후 사용할 수 있는 관리되는 ISE 커넥터는 논리 앱 디자이너에 자동으로 나타나지 않습니다. 이러한 ISE 커넥터를 사용하려면 논리 앱 디자이너에 표시되도록 [해당 커넥터를 ISE에](#add-ise-connectors-environment) 수동으로 추가하고 배포해야 합니다.

> [!IMPORTANT]
> 논리 앱과 통합 계정이 ISE에서 함께 작동하려면 둘 다 위치와 *동일한 ISE를* 사용해야 합니다.

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독 Azure 구독이 없는 경우 [체험 Azure 계정에 등록](https://azure.microsoft.com/free/)합니다.

* 논리 앱을 실행하기 위해 만든 ISE입니다. ISE가 없는 경우 먼저 [ISE를 만듭니다.](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)

* ISE에 배포된 리소스를 만들거나 추가하거나 업데이트하려면 해당 ISE에서 소유자 또는 기여자 역할을 할당해야 하거나 ISE와 연결된 Azure 구독 또는 Azure 리소스 그룹을 통해 상속된 사용 권한이 있어야 합니다. 소유자, 참여자 또는 상속된 사용 권한이 없는 사용자의 경우 통합 서비스 환경 기여자 역할 또는 통합 서비스 환경 개발자 역할을 할당할 수 있습니다. RBAC(역할 기반 액세스 제어)에 대한 자세한 내용은 [Azure 리소스에 대한 역할 기반 액세스 제어(RBAC)란 무엇입니까?](../role-based-access-control/overview.md)

<a name="create-logic-apps-environment"></a>

## <a name="create-logic-apps"></a>논리 앱 만들기

ISE(통합 서비스 환경)에서 실행되는 논리 앱을 빌드하려면 다음 단계를 따르십시오.

1. 아직 열리지 않은 경우 ISE를 찾아 엽니다. ISE 메뉴에서 **설정에서** **논리 앱** > **추가를**선택합니다.

   ![ISE에 새 논리 앱 추가](./media/add-artifacts-integration-service-environment-ise/add-logic-app-to-ise.png)

1. 다음과 같은 만들려는 논리 앱에 대한 정보를 제공합니다.

   ![통합 서비스 환경 선택](./media/add-artifacts-integration-service-environment-ise/create-logic-app-integration-service-environment.png)

   | 속성 | 필수 | 설명 |
   |----------|----------|-------------|
   | **이름** | yes | 만들 논리 앱의 이름 |
   | **구독** | yes | 사용할 Azure 구독의 이름입니다. |
   | **리소스 그룹** | yes | 사용할 Azure 리소스 그룹(신규 또는 기존)의 이름 |
   | **위치** | yes | **통합 서비스 환경에서**아직 선택하지 않은 경우 사용할 ISE를 선택합니다. <p><p> **중요**: 통합 계정과 논리 앱을 사용하려면 둘 다 동일한 ISE를 사용해야 합니다. |
   ||||

1. 완료되면 **만들기**를 선택합니다.

1. 일반적인 방법으로 논리 앱을 계속 [만듭니다.](../logic-apps/quickstart-create-first-logic-app-workflow.md)

   트리거 및 작업의 작동 방식과 다중 테넌트 논리 앱 서비스와 비교하여 ISE를 사용할 때 레이블이 지정되는 방식의 차이점은 [ISE 개요에서 격리 대 다중 테넌트를](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#difference)참조하십시오.

1. ISE에서 논리 앱 및 API 연결을 관리하려면 [통합 서비스 환경 관리를](../logic-apps/ise-manage-integration-service-environment.md)참조하십시오.

<a name="create-integration-account-environment"></a>

## <a name="create-integration-accounts"></a>통합 계정 만들기

생성 시 선택한 [ISE SKU에](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level) 따라 ISE에는 추가 비용 없이 특정 통합 계정 사용량이 포함됩니다. ISE(통합 서비스 환경)에 있는 논리 앱은 동일한 ISE에 있는 통합 계정만 참조할 수 있습니다. 따라서 통합 계정이 ISE의 논리 앱과 함께 작동하려면 통합 계정과 논리 앱 모두 해당 위치와 *동일한 환경을* 사용해야 합니다. 통합 계정 및 ISE에 대한 자세한 내용은 [ISE와의 통합 계정을](connect-virtual-network-vnet-isolated-environment-overview.md#create-integration-account-environment)참조하십시오.

ISE를 사용하는 통합 계정을 만들려면 다음 단계를 따르십시오.

1. 아직 열리지 않은 경우 ISE를 찾아 엽니다. 설정에서 **Settings**ISE 메뉴에서 통합 **계정** > **추가를**선택합니다.

   ![ISE에 새 통합 계정 추가](./media/add-artifacts-integration-service-environment-ise/add-integration-account-to-ise.png)

1. 다음과 같은 만들려는 논리 앱에 대한 정보를 제공합니다.

   ![통합 서비스 환경 선택](./media/add-artifacts-integration-service-environment-ise/create-integration-account-integration-service-environment.png)

   | 속성 | 필수 | 설명 |
   |----------|----------|-------------|
   | **이름** | yes | 만들려는 통합 계정의 이름입니다. |
   | **구독** | yes | 사용할 Azure 구독의 이름입니다. |
   | **리소스 그룹** | yes | 사용할 Azure 리소스 그룹(신규 또는 기존)의 이름 |
   | **가격 책정 계층** | yes | 통합 계정에 사용할 가격 책정 계층 |
   | **위치** | yes | **통합 서비스 환경에서**논리 앱이 아직 선택하지 않은 경우 사용하는 것과 동일한 ISE를 선택합니다. <p><p> **중요**: 논리 앱과 통합 계정을 사용하려면 둘 다 동일한 ISE를 사용해야 합니다. |
   ||||

1. 완료되면 **만들기**를 선택합니다.

1. [논리 앱을 일반적인 방법으로 통합 계정에 연결합니다.](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account)

1. [거래 업체](../logic-apps/logic-apps-enterprise-integration-partners.md) 및 [계약과](../logic-apps/logic-apps-enterprise-integration-agreements.md)같은 리소스를 통합 계정에 추가하여 계속합니다.

1. ISE의 통합 계정을 관리하려면 [통합 서비스 환경 관리를](../logic-apps/ise-manage-integration-service-environment.md)참조하십시오.

<a name="add-ise-connectors-environment"></a>

## <a name="add-ise-connectors"></a>ISE 커넥터 추가

ISE를 만든 후 사용할 수 있는 Microsoft 관리 커넥터는 논리 앱 디자이너의 커넥터 선택기에 자동으로 나타나지 않습니다. 이러한 ISE 커넥터를 사용하려면 이러한 커넥터를 수동으로 추가하고 ISE에 배포하여 논리 앱 디자이너에 표시해야 합니다.

1. ISE 메뉴에서 **설정에서** **관리되는 커넥터를 선택합니다.** 도구 모음에서 **추가**를 선택합니다.

   ![관리형 커넥터 보기](./media/add-artifacts-integration-service-environment-ise/ise-view-managed-connectors.png)

1. **관리되는 새 커넥터 추가** 창에서 **커넥터 찾기** 목록을 엽니다. 사용하지만 아직 ISE에 배포되지 않은 ISE 커넥터를 선택합니다. **만들기**를 선택합니다.

   ![ISE에 배포할 ISE 커넥터를 선택합니다.](./media/add-artifacts-integration-service-environment-ise/add-managed-connector.png)

   ISE에 배포되었지만 아직 배포되지 않은 ISE 커넥터만 선택할 수 있습니다. ISE에 이미 배포된 커넥터를 선택할 수 없는 것으로 나타납니다.

<a name="create-custom-connectors-environment"></a>

## <a name="create-custom-connectors"></a>사용자 지정 커넥터 만들기

ISE에서 사용자 지정 커넥터를 사용하려면 ISE 내부에서 직접 사용자 지정 커넥터를 만듭니다.

1. 아직 열리지 않은 경우 ISE를 찾아 엽니다. 설정에서 ISE 메뉴에서 **사용자 지정 커넥터** > **추가를**선택합니다. **Settings**

   ![사용자 지정 커넥터 만들기](./media/add-artifacts-integration-service-environment-ise/add-custom-connector-to-ise.png)

1. 사용자 지정 커넥터에 사용할 이름, Azure 구독 및 Azure 리소스 그룹(새 또는 기존)을 제공합니다.

1. **위치** 목록에서 **통합 서비스 환경** 섹션에서 논리 앱에서 사용하는 것과 동일한 ISE를 선택하고 다음과 같은 **만들기를**선택합니다.

   ![통합 서비스 환경 선택](./media/add-artifacts-integration-service-environment-ise/create-custom-connector-integration-service-environment.png)

1. 새 사용자 지정 커넥터를 선택한 다음 다음과 같은 **편집을**선택합니다.

   ![사용자 지정 커넥터 선택 및 편집](./media/add-artifacts-integration-service-environment-ise/edit-custom-connectors.png)

1. [OpenAPI 정의](https://docs.microsoft.com/connectors/custom-connectors/define-openapi-definition#import-the-openapi-definition) 또는 [SOAP에서](https://docs.microsoft.com/connectors/custom-connectors/create-register-logic-apps-soap-connector#2-define-your-connector)일반적인 방법으로 커넥터를 만들어 계속합니다.

1. ISE에서 사용자 지정 커넥터를 관리하려면 [통합 서비스 환경 관리를](../logic-apps/ise-manage-integration-service-environment.md)참조하십시오.

## <a name="next-steps"></a>다음 단계

* [통합 서비스 환경 관리](../logic-apps/ise-manage-integration-service-environment.md)
