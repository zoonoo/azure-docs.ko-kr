---
title: Azure Logic Apps에서 integration service environment (ISEs)에 아티팩트 추가
description: Azure 가상 네트워크 (Vnet)에 액세스 하기 위해 ISE (integration service environment)에 논리 앱, 사용자 지정 커넥터 및 통합 계정을 추가 하는 동시에 공용 또는 "글로벌" Azure에서 비공개 및 격리
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: 6327f0c14b46ceaadbf7adaa58a70c32b39b7c2a
ms.sourcegitcommit: 4d177e6d273bba8af03a00e8bb9fe51a447196d0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/04/2019
ms.locfileid: "71960484"
---
# <a name="add-artifacts-to-your-integration-service-environment-ise-in-azure-logic-apps"></a>Azure Logic Apps에서 ISE (통합 서비스 환경)에 아티팩트를 추가 합니다.

[ISE (통합 서비스 환경)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)를 만든 후에는 Azure 가상 네트워크의 리소스에 액세스할 수 있도록 논리 앱, 통합 계정 및 커넥터와 같은 아티팩트를 추가 합니다.

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독. Azure 구독이 없는 경우 [체험 Azure 계정에 등록](https://azure.microsoft.com/free/)합니다.

* 논리 앱을 실행 하기 위해 만든 ISE입니다. ISE가 없으면 [먼저 ise를 만듭니다](../logic-apps/connect-virtual-network-vnet-isolated-environment.md).

<a name="create-logic-apps-environment"></a>

## <a name="create-logic-apps"></a>논리 앱 만들기

ISE (통합 서비스 환경)에서 실행 되는 논리 앱을 빌드하려면 다음 단계를 수행 합니다.

1. ISE를 찾아 엽니다 (아직 열지 않은 경우). ISE 메뉴의 **설정**에서 **논리 앱** > **추가**를 선택 합니다.

   ![ISE에 새 논리 앱 추가](./media/add-artifacts-integration-service-environment-ise/add-logic-app-to-ise.png)

   또는

   Azure 주 메뉴에서 **리소스 만들기** > **통합** > **논리 앱**을 차례로 선택합니다.

1. 논리 앱에 사용할 이름, Azure 구독 및 Azure 리소스 그룹 (신규 또는 기존)을 제공 합니다.

1. **위치** 목록의 **Integration service ENVIRONMENT** 섹션에서 ISE를 선택 합니다. 예를 들면 다음과 같습니다.

   ![통합 서비스 환경 선택](./media/add-artifacts-integration-service-environment-ise/create-logic-app-with-integration-service-environment.png)

   > [!IMPORTANT]
   > 통합 계정을 사용 하 여 논리 앱을 사용 하려는 경우 해당 논리 앱과 통합 계정은 동일한 ISE를 사용 해야 합니다.

1. [일반적인 방법으로 논리 앱](../logic-apps/quickstart-create-first-logic-app-workflow.md)을 계속 만듭니다.

   트리거 및 작업이 작동 하는 방식과 ISE를 사용 하 여 글로벌 Logic Apps 서비스에 표시 되는 방식에 대 한 차이점 [은 ise 개요에서 격리 된 vs global](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#difference)을 참조 하세요.

1. ISE에서 논리 앱 및 API 연결을 관리 하려면 [통합 서비스 환경 관리](../logic-apps/ise-manage-integration-service-environment.md)를 참조 하세요.

<a name="create-integration-account-environment"></a>

## <a name="create-integration-accounts"></a>통합 계정 만들기

생성 시 선택 된 [ISE SKU](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level) 에 따라 ise에 추가 비용 없이 특정 통합 계정 사용이 포함 됩니다. ISE (integration service environment)에 있는 논리 앱은 동일한 ISE에 있는 통합 계정만 참조할 수 있습니다. 따라서 통합 계정이 ISE에서 논리 앱과 작동 하려면 통합 계정 및 논리 앱 모두 해당 위치와 *동일한 환경을* 사용 해야 합니다. 통합 계정 및 ISEs에 대 한 자세한 내용은 [ISE와 통합 계정](connect-virtual-network-vnet-isolated-environment-overview.md#create-integration-account-environment)을 참조 하세요.

ISE를 사용 하는 통합 계정을 만들려면 다음 단계를 수행 합니다.

1. ISE를 찾아 엽니다 (아직 열지 않은 경우). ISE 메뉴의 **설정**에서 **통합 계정** > **추가**를 선택 합니다.

   ![ISE에 새 통합 계정 추가](./media/add-artifacts-integration-service-environment-ise/add-integration-account-to-ise.png)

   또는

   기본 Azure 메뉴에서 **리소스 만들기** > **통합** > **통합 계정**을 선택 합니다.

1. 통합 계정에 사용할 이름, Azure 구독, Azure 리소스 그룹 (신규 또는 기존) 및 가격 책정 계층을 제공 합니다.

1. **위치** 목록의 **Integration service environment** 섹션에서 논리 앱이 사용 하는 것과 동일한 ISE를 선택 합니다. 예를 들면 다음과 같습니다.

   ![통합 서비스 환경 선택](./media/add-artifacts-integration-service-environment-ise/create-integration-account-with-integration-service-environment.png)

1. [일반적인 방법으로 통합 계정에 논리 앱을 연결](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account)합니다.

1. [거래 파트너](../logic-apps/logic-apps-enterprise-integration-partners.md) 및 [계약과](../logic-apps/logic-apps-enterprise-integration-agreements.md)같은 통합 계정에 아티팩트를 추가 하 여 계속 합니다.

1. ISE에서 통합 계정을 관리 하려면 [integration service Environment 관리](../logic-apps/ise-manage-integration-service-environment.md)를 참조 하세요.

<a name="add-ise-connectors-environment"></a>

## <a name="add-ise-connectors"></a>ISE 커넥터 추가

ISE에서 사용 하기 위해 사용할 수 있지만 ISE에 배포 되지 않은 Microsoft 관리 커넥터를 추가할 수 있습니다.

1. ISE 메뉴의 **설정**에서 **관리 되는 커넥터**를 선택 합니다. 도구 모음에서 **추가**를 선택합니다.

   ![관리 되는 커넥터 보기](./media/add-artifacts-integration-service-environment-ise/ise-view-managed-connectors.png)

1. **새 관리 커넥터 추가** 창에서 **커넥터 찾기** 목록을 엽니다. 원하는 커넥터를 사용할 수 있는 경우 해당 커넥터를 선택한 다음 **만들기**를 선택 합니다.

   이 목록에는 적격 하지만 ISE에 배포 되지 않은 커넥터만 표시 됩니다. ISE에 이미 배포 된 커넥터는 선택할 수 없는 것으로 나타납니다.

   ![적격 커넥터 선택](./media/add-artifacts-integration-service-environment-ise/add-managed-connector.png)

<a name="create-custom-connectors-environment"></a>

## <a name="create-custom-connectors"></a>사용자 지정 커넥터 만들기

ISE에서 사용자 지정 커넥터를 사용 하려면 ISE 내에서 직접 사용자 지정 커넥터를 만듭니다.

1. ISE를 찾아 엽니다 (아직 열지 않은 경우). ISE 메뉴의 **설정**에서 **사용자 지정 커넥터** > **추가**를 선택 합니다.

   ![사용자 지정 커넥터 만들기](./media/add-artifacts-integration-service-environment-ise/add-custom-connector-to-ise.png)

1. 사용자 지정 커넥터에 사용할 이름, Azure 구독 및 Azure 리소스 그룹 (신규 또는 기존)을 제공 합니다.

1. **위치** 목록의 **Integration service environment** 섹션에서 논리 앱이 사용 하는 동일한 ISE를 선택 하 고 **만들기**를 선택 합니다. 예를 들면 다음과 같습니다.

   ![통합 서비스 환경 선택](./media/add-artifacts-integration-service-environment-ise/create-custom-connector-with-integration-service-environment.png)

1. 새 사용자 지정 커넥터를 선택 하 고 **편집**을 선택 합니다. 예를 들면 다음과 같습니다.

   ![사용자 지정 커넥터 선택 및 편집](./media/add-artifacts-integration-service-environment-ise/edit-custom-connectors.png)

1. [Openapi 정의](https://docs.microsoft.com/connectors/custom-connectors/define-openapi-definition#import-the-openapi-definition) 또는 [SOAP](https://docs.microsoft.com/connectors/custom-connectors/create-register-logic-apps-soap-connector#2-define-your-connector)에서 일반적인 방법으로 커넥터를 만들어 계속 합니다.

1. ISE에서 사용자 지정 커넥터를 관리 하려면 [통합 서비스 환경 관리](../logic-apps/ise-manage-integration-service-environment.md)를 참조 하세요.

## <a name="next-steps"></a>다음 단계

* [통합 서비스 환경 관리](../logic-apps/ise-manage-integration-service-environment.md)
