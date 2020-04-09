---
title: 구독, 리소스 그룹 또는 리전에서 논리 앱 마이그레이션
description: 논리 앱 또는 통합 계정을 다른 Azure 구독, 리소스 그룹 또는 위치(영역)로 마이그레이션합니다.
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: 065bbc62d65d7e91728b10cd9f95b2e73ea03abc
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878734"
---
# <a name="move-logic-app-resources-to-other-azure-resource-groups-regions-or-subscriptions"></a>논리 앱 리소스를 다른 Azure 리소스 그룹, 지역 또는 구독으로 이동

논리 앱 또는 관련 리소스를 다른 Azure 리소스 그룹, 지역 또는 구독으로 마이그레이션하려면 Azure 포털, Azure PowerShell, Azure CLI 및 REST API와 같은 다양한 방법으로 이러한 작업을 완료할 수 있습니다. 리소스를 이동하기 전에 다음 사항을 검토합니다. 

* Azure 리소스 그룹 또는 구독 간에 [특정 논리 앱 리소스 유형만](../azure-resource-manager/management/move-support-resources.md#microsoftlogic) 이동할 수 있습니다.

* Azure 구독 및 각 Azure 리전에서 가질 수 있는 논리 앱 리소스 수에 대한 [제한을](../logic-apps/logic-apps-limits-and-config.md) 확인합니다. 이러한 제한은 지역이 구독 또는 리소스 그룹 간에 동일하게 유지될 때 특정 리소스 유형을 이동할 수 있는지 여부에 영향을 미칩니다. 예를 들어 각 Azure 구독의 각 Azure 지역에 대해 하나의 프리 계층 통합 계정만 가질 수 있습니다.

* 리소스를 이동하면 Azure에서 새 리소스 를 만듭니다. 따라서 새 아이디를 대신 사용하고 이동된 리소스와 연결된 스크립트 나 도구를 업데이트해야 합니다.

* 구독, 리소스 그룹 또는 지역 간에 논리 앱을 마이그레이션한 후 OAuth(개방형 인증)가 필요한 연결을 다시 만들거나 다시 인증해야 합니다.

* [ISE(통합 서비스 환경)를](connect-virtual-network-vnet-isolated-environment-overview.md) 동일한 Azure 지역 또는 Azure 구독에 있는 다른 리소스 그룹으로만 이동할 수 있습니다. ISE를 다른 Azure 지역 또는 Azure 구독에 있는 리소스 그룹으로 이동할 수 없습니다. 또한 이러한 이동 후에는 논리 앱 워크플로, 통합 계정, 연결 등에서 ISE에 대한 모든 참조를 업데이트해야 합니다.

## <a name="prerequisites"></a>사전 요구 사항

* 이동하려는 논리 앱 또는 통합 계정을 만드는 데 사용된 동일한 Azure 구독

* 리소스 소유자가 원하는 리소스를 이동하고 설정할 수 있는 권한입니다. [RBAC(역할 기반 액세스 제어)에](../role-based-access-control/built-in-roles.md#owner)대해 자세히 알아봅니다.

<a name="move-subscription"></a>

## <a name="move-resources-between-subscriptions"></a>구독 간에 리소스 이동

논리 앱 또는 통합 계정과 같은 리소스를 다른 Azure 구독으로 이동하려면 Azure 포털, Azure PowerShell, Azure CLI 또는 REST API를 사용할 수 있습니다. 이 단계는 리소스의 영역이 동일하게 유지될 때 사용할 수 있는 Azure 포털을 다룹니다. 다른 단계 및 일반 준비는 [리소스를 새 리소스 그룹 또는 구독으로 이동을](../azure-resource-manager/management/move-resource-group-and-subscription.md)참조하십시오.

1. Azure [포털에서](https://portal.azure.com)이동하려는 논리 앱 리소스를 찾아 선택합니다.

1. 리소스의 **개요** 페이지에서 **구독**옆의 **변경** 링크를 선택합니다.

1. 리소스 **이동** 페이지에서 논리 앱 리소스 및 이동하려는 관련 리소스를 선택합니다.

1. **구독** 목록에서 대상 구독을 선택합니다.

1. 리소스 **그룹** 목록에서 대상 리소스 그룹을 선택합니다. 또는 다른 리소스 그룹을 만들려면 **새 그룹 만들기를**선택합니다.

1. 이동된 리소스와 연결된 스크립트 또는 도구가 새 리소스 아이디로 업데이트할 때까지 작동하지 않는다는 것을 확인하려면 확인 상자를 선택한 다음 **확인을**선택합니다.

<a name="move-resource-group"></a>

## <a name="move-resources-between-resource-groups"></a>리소스 그룹 간에 리소스 이동

논리 앱, 통합 계정 또는 [ISE(통합 서비스 환경)와](connect-virtual-network-vnet-isolated-environment-overview.md)같은 리소스를 다른 Azure 리소스 그룹으로 이동하려면 Azure 포털, Azure PowerShell, Azure CLI 또는 REST API를 사용할 수 있습니다. 이 단계는 리소스의 영역이 동일하게 유지될 때 사용할 수 있는 Azure 포털을 다룹니다. 다른 단계 및 일반 준비는 [리소스를 새 리소스 그룹 또는 구독으로 이동을](../azure-resource-manager/management/move-resource-group-and-subscription.md)참조하십시오.

실제로 그룹 간에 리소스를 이동하기 전에 리소스를 다른 그룹으로 성공적으로 이동할 수 있는지 여부를 테스트할 수 있습니다. 자세한 내용은 [이동 유효성 검사](../azure-resource-manager/management/move-resource-group-and-subscription.md#validate-move)를 참조하십시오.

1. Azure [포털에서](https://portal.azure.com)이동하려는 논리 앱 리소스를 찾아 선택합니다.

1. 리소스 의 **개요** 페이지에서 **리소스 그룹**옆의 **변경** 링크를 선택합니다.

1. 리소스 **이동** 페이지에서 논리 앱 리소스 및 이동하려는 관련 리소스를 선택합니다.

1. 리소스 **그룹** 목록에서 대상 리소스 그룹을 선택합니다. 또는 다른 리소스 그룹을 만들려면 **새 그룹 만들기를**선택합니다.

1. 이동된 리소스와 연결된 스크립트 또는 도구가 새 리소스 아이디로 업데이트할 때까지 작동하지 않는다는 것을 확인하려면 확인 상자를 선택한 다음 **확인을**선택합니다.

<a name="move-location"></a>

## <a name="move-resources-between-regions"></a>리전 간 리소스 이동

논리 앱을 다른 지역으로 이동하려는 경우 옵션은 논리 앱을 만든 방법에 따라 달라집니다. 선택한 옵션에 따라 논리 앱에서 연결을 다시 만들거나 다시 인증해야 합니다.

* Azure 포털에서 새 리전에서 논리 앱을 다시 만들고 워크플로 설정을 다시 구성합니다. 시간을 절약하기 위해 기본 워크플로 정의 및 원본 앱에서 대상 앱으로 의 연결을 복사할 수 있습니다. 논리 앱 뒤에 있는 "코드"를 보려면 논리 앱 디자이너 도구 모음에서 **코드 보기를 선택합니다.**

* Visual Studio 및 Azure 논리 앱 도구를 사용하여 Azure [포털에서](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md) [논리 앱을 열고 다운로드할](../logic-apps/manage-logic-apps-with-visual-studio.md) 수 있습니다. 이 템플릿은 대부분 배포할 준비가 되어 있으며 워크플로 자체 및 연결을 포함하여 논리 앱에 대한 리소스 정의를 포함합니다. 또한 템플릿은 배포 시 사용할 값에 대한 매개 변수를 선언합니다. 이렇게 하면 필요에 따라 논리 앱을 배포하는 위치와 방법을 보다 쉽게 변경할 수 있습니다. 배포에 필요한 위치 및 기타 필요한 정보를 지정하려면 별도의 매개 변수 파일을 사용할 수 있습니다.

* CI(연속 통합) 및 Azure DevOps의 Azure 파이프라인과 같은 CD(연속 배달) 도구를 사용하여 논리 앱을 만들고 배포한 경우 이러한 도구를 사용하여 다른 지역에 앱을 배포할 수 있습니다.

논리 앱의 배포 템플릿에 대한 자세한 내용은 다음 항목을 참조하십시오.

* [개요: Azure 리소스 관리자 템플릿을 사용하여 Azure 논리 앱에 대한 배포 자동화](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)
* [Azure 포털에서 Visual Studio로 논리 앱을 찾아, 열고, 다운로드합니다.](../logic-apps/manage-logic-apps-with-visual-studio.md)
* [Azure 논리 앱에 대한 Azure 리소스 관리자 템플릿 만들기](../logic-apps/logic-apps-create-azure-resource-manager-templates.md)
* [Azure 논리 앱에 대한 Azure 리소스 관리자 템플릿 배포](../logic-apps/logic-apps-deploy-azure-resource-manager-templates.md)

### <a name="related-resources"></a>관련 참고 자료

Azure의 온-프레미스 데이터 게이트웨이 리소스와 같은 일부 Azure 리소스는 해당 리소스를 사용하는 논리 앱과 다른 지역에 존재할 수 있습니다. 그러나 연결된 통합 계정과 같은 다른 Azure 리소스는 논리 앱과 동일한 지역에 있어야 합니다. 시나리오에 따라 논리 앱이 앱이 동일한 지역에 존재할 것으로 예상되는 리소스에 액세스할 수 있는지 확인합니다.

예를 들어 논리 앱을 통합 계정에 연결하려면 두 리소스가 동일한 지역에 있어야 합니다. 재해 복구와 같은 시나리오에서는 일반적으로 구성 및 아티팩트가 동일한 통합 계정을 원합니다. 다른 시나리오에서는 구성 및 아티팩트가 다른 통합 계정이 필요할 수 있습니다.

Azure 논리 앱의 사용자 지정 커넥터는 동일한 Azure 구독및 동일한 Azure Active Directory 테넌트를 가진 커넥터의 작성자 및 사용자에게 표시됩니다. 이러한 커넥터는 논리 앱이 배포되는 동일한 지역에서 사용할 수 있습니다. 자세한 내용은 [조직에서 사용자 지정 커넥터 공유](https://docs.microsoft.com/connectors/custom-connectors/share)를 참조하세요.

Visual Studio에서 얻는 템플릿에는 논리 앱 및 해당 연결에 대한 리소스 정의만 포함됩니다. 따라서 논리 앱에서 파트너, 계약 및 스키마와 같은 통합 계정 및 B2B 아티팩트와 같은 다른 리소스를 사용하는 경우 Azure 포털을 사용하여 해당 통합 계정의 템플릿을 내보내야 합니다. 이 템플릿에는 통합 계정과 아티팩트모두에 대한 리소스 정의가 포함되어 있습니다. 그러나 템플릿이 완전히 매개 변수화되지는 않습니다. 따라서 배포에 사용할 값을 수동으로 매개 변수화해야 합니다.

### <a name="export-templates-for-integration-accounts"></a>통합 계정에 대한 템플릿 내보내기

1. Azure [포털에서](https://portal.azure.com)통합 계정을 찾아 엽니다.

1. 통합 계정의 메뉴에서 **설정에서** **템플릿 내보내기를**선택합니다.

1. 도구 모음에서 **다운로드를**선택하고 템플릿을 저장합니다.

1. 템플릿을 열고 편집하여 배포에 필요한 값을 매개변수화합니다.

## <a name="next-steps"></a>다음 단계

[Azure 리소스를 새 리소스 그룹 또는 구독으로 이동](../azure-resource-manager/management/move-resource-group-and-subscription.md)
