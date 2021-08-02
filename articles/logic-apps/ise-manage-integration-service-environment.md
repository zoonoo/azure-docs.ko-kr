---
title: Azure Logic Apps에서 통합 서비스 환경 관리
description: Azure Logic Apps를 위한 ISE(통합 서비스 환경)에서 네트워크 상태를 확인하고 논리 앱, 연결, 사용자 지정 커넥터와 통합 계정 관리
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, azla
ms.topic: conceptual
ms.date: 10/25/2020
ms.openlocfilehash: f4cc844cc12383d5ccc3a3a12f48431d34b4872d
ms.sourcegitcommit: e39ad7e8db27c97c8fb0d6afa322d4d135fd2066
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111984827"
---
# <a name="manage-your-integration-service-environment-ise-in-azure-logic-apps"></a>Azure Logic Apps에서 ISE(통합 서비스 환경) 관리

이 문서에서는 [ISE(통합 서비스 환경)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)을 위한 관리 작업을 수행하는 방법을 보여 줍니다. 예를 들면 다음과 같습니다.

* ISE에서 다중 테넌트 기반 논리 앱, 연결, 통합 계정, 커넥터와 같은 리소스를 관리합니다.

* ISE의 네트워크 상태를 확인합니다.

* 용량을 추가하거나, ISE를 다시 시작하거나, ISE를 삭제하려면 이 항목의 단계를 따르세요. ISE에 해당 아티팩트를 추가하려면 [통합 서비스 환경에 아티팩트 추가](../logic-apps/add-artifacts-integration-service-environment-ise.md)를 참조하세요.

## <a name="view-your-ise"></a>ISE 보기

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. 포털의 검색 상자에 “integration service environment”(통합 서비스 환경)를 입력한 다음 **통합 서비스 환경** 을 선택합니다.

   ![통합 서비스 환경 찾기](./media/ise-manage-integration-service-environment/find-integration-service-environment.png)

1. 결과 목록에서 통합 서비스 환경을 선택합니다.

   ![통합 서비스 환경 선택](./media/ise-manage-integration-service-environment/select-integration-service-environment.png)

1. 다음 섹션으로 계속 진행하여 ISE에서 논리 앱, 연결, 커넥터 또는 통합 계정을 찾습니다.

<a name="check-network-health"></a>

## <a name="check-network-health"></a>네트워크 상태 검사

ISE 메뉴의 **설정** 에서 **네트워크 상태** 를 선택합니다. 이 창에는 서브넷의 상태와 다른 서비스에 대한 아웃바운드 종속성이 표시됩니다.

![네트워크 상태 검사](./media/ise-manage-integration-service-environment/ise-check-network-health.png)

> [!CAUTION]
> ISE 네트워크가 비정상 상태가 되면 ISE에서 사용되는 내부 ASE(App Service Environment)도 비정상 상태가 될 수 있습니다. ASE가 7일 이상 비정상 상태면 ASE가 일시 중단됩니다. 이 상태를 확인하려면 가상 네트워크 설정을 확인합니다. 발견된 모든 문제를 해결한 후 ISE를 다시 시작합니다. 그렇지 않으면 90일 후 일시 중단된 ASE가 삭제되고 ISE가 사용할 수 없게 됩니다. 따라서 필요한 트래픽을 허용하도록 ISE를 정상 상태로 유지해야 합니다.
> 
> 자세한 내용은 다음 항목을 참조하세요.
>
> * [Azure App Service 진단 개요](../app-service/overview-diagnostics.md)
> * [Azure App Service Environment를 위한 메시지 로깅](../app-service/environment/using-an-ase.md#logging)

<a name="find-logic-apps"></a>

## <a name="manage-your-logic-apps"></a>논리 앱 관리

ISE에 있는 논리 앱을 보고 관리할 수 있습니다.

1. ISE 메뉴의 **설정** 에서 **논리 앱** 을 선택합니다.

   ![논리 앱 보기](./media/ise-manage-integration-service-environment/ise-find-logic-apps.png)

1. ISE에서 더 이상 필요하지 않은 논리 앱을 제거하려면 해당 논리 앱을 선택한 다음, **삭제** 를 선택합니다. 삭제를 확인하려면 **예** 를 선택합니다.

> [!NOTE]
> 자식 논리 앱을 삭제하고 다시 만드는 경우 부모 논리 앱을 다시 저장해야 합니다. 다시 만들어진 자식 앱은 다른 메타데이터를 포함합니다.
> 자식을 다시 만든 후 부모 논리 앱을 다시 저장하지 않는 경우 "권한 없음" 오류와 함께 자식 논리 앱에 대한 호출이 실패합니다. 이 동작은 통합 계정에서 아티팩트를 사용하거나 Azure Functions를 호출하는 것과 같은 부모-자식 논리 앱에 적용됩니다.

<a name="find-api-connections"></a>

## <a name="manage-api-connections"></a>API 연결 관리

ISE에서 실행되는 논리 앱에서 만든 연결을 보고 관리할 수 있습니다.

1. ISE 메뉴의 **설정** 에서 **API 연결** 을 선택합니다.

   ![API 연결 보기](./media/ise-manage-integration-service-environment/ise-find-api-connections.png)

1. ISE에서 더 이상 필요하지 않은 연결을 제거하려면 해당 연결을 선택한 다음 **삭제** 를 선택합니다. 삭제를 확인하려면 **예** 를 선택합니다.

<a name="manage-api-connectors"></a>

## <a name="manage-ise-connectors"></a>ISE 커넥터 관리

ISE에 배포된 API 커넥터를 보고 관리할 수 있습니다.

1. ISE 메뉴의 **설정** 에서 **관리형 커넥터** 를 선택합니다.

   ![관리형 커넥터 보기](./media/ise-manage-integration-service-environment/ise-view-managed-connectors.png)

1. ISE에서 사용하지 않으려는 커넥터를 제거하려면 해당 커넥터를 선택한 다음, **삭제** 를 선택합니다. 삭제를 확인하려면 **예** 를 선택합니다.

<a name="find-custom-connectors"></a>

## <a name="manage-custom-connectors"></a>사용자 지정 커넥터 관리

ISE에 배포된 사용자 지정 커넥터를 보고 관리할 수 있습니다.

1. ISE 메뉴의 **설정** 에서 **사용자 지정 커넥터** 를 선택합니다.

   ![사용자 지정 커넥터 찾기](./media/ise-manage-integration-service-environment/ise-find-custom-connectors.png)

1. ISE에서 더 이상 사용하지 않으려는 사용자 지정 커넥터를 제거하려면 해당 커넥터를 선택한 다음, **삭제** 를 선택합니다. 삭제를 확인하려면 **예** 를 선택합니다.

<a name="find-integration-accounts"></a>

## <a name="manage-integration-accounts"></a>통합 계정 관리

1. ISE 메뉴의 **설정** 에서 **통합 계정** 을 선택합니다.

   ![통합 계정 찾기](./media/ise-manage-integration-service-environment/ise-find-integration-accounts.png)

1. 더 이상 필요하지 않을 때 ISE에서 통합 계정을 제거하려면 해당 통합 계정을 선택한 다음 **삭제** 를 선택합니다.

<a name="add-capacity"></a>

## <a name="add-ise-capacity"></a>ISE 용량 추가

Premium ISE 기준 단위는 용량이 고정되어 있음으로, 더 많은 처리량이 필요한 경우 생성 중 또는 나중에 스케일링 단위를 추가할 수 있습니다. 개발자 SKU에는 스케일링 단위를 추가하는 기능이 없습니다.

1. [Azure portal](https://portal.azure.com)에서 ISE로 이동합니다.

1. ISE에 대한 사용량 및 성능 메트릭을 검토하려면 ISE 메뉴에서 **개요** 를 선택합니다.

   ![ISE에 대한 사용량 보기](./media/ise-manage-integration-service-environment/integration-service-environment-usage.png)

1. **설정** 아래에서 **스케일 아웃** 을 선택합니다. **구성** 창에서 다음 옵션을 선택합니다.

   * [**수동 스케일링**](#manual-scale): 사용하려는 처리 단위 수를 기준으로 스케일링합니다.
   * [**사용자 지정 자동 스케일링**](#custom-autoscale): 다양한 기준 중에서 선택하고 해당 기준에 맞는 임계값 조건을 지정하여 성능 메트릭에 따라 스케일링합니다.

   ![“수동 스케일링”이 선택된 “스케일 아웃” 페이지를 보여 주는 스크린샷](./media/ise-manage-integration-service-environment/select-scale-out-options.png)

<a name="manual-scale"></a>

### <a name="manual-scale"></a>수동 크기 조정

1. **Manual scale**(수동 스케일링)을 선택한 후 **추가 용량** 에 사용할 스케일링 단위 수를 선택합니다.

   ![필요한 스케일링 유형 선택](./media/ise-manage-integration-service-environment/select-manual-scale-out-units.png)

1. 완료되면 **저장** 을 선택합니다.

<a name="custom-autoscale"></a>

### <a name="custom-autoscale"></a>사용자 지정 자동 스케일링

1. **사용자 지정 자동 스케일링** 을 선택한 후 **자동 스케일링 설정 이름** 에 설정의 이름을 입력하고 필요에 따라 설정이 속한 Azure 리소스 그룹을 선택합니다.

   ![자동 스케일링 설정의 이름을 입력하고 리소스 그룹을 선택합니다.](./media/ise-manage-integration-service-environment/select-custom-autoscale.png)

1. **기본** 조건의 경우 **메트릭 기준 스케일링** 또는 **Scale to a specific instance count**(특정 인스턴스 수로 스케일링) 중 하나를 선택합니다.

   * 인스턴스 기준을 선택하는 경우 처리 단위 수를 0에서 10 사이의 값으로 입력합니다.

   * 메트릭 기준을 선택하는 경우 다음 단계를 수행합니다.

     1. **규칙** 섹션에서 **규칙 추가** 를 선택합니다.

     1. 규칙 **스케일링 규칙** 창에서 규칙을 트리거할 때 사용할 조건 및 작업을 설정합니다.

     1. **인스턴스 제한** 의 경우 다음 값을 지정합니다.

        * **최소**: 사용할 최소 처리 단위 수
        * **최대**: 사용할 최대 처리 단위 수
        * **기본값**: 리소스 메트릭을 읽는 동안 문제가 발생하고 현재 용량이 기본 용량보다 적은 경우 자동 스케일링은 기본 처리 단위 수로 스케일 아웃됩니다. 그러나 현재 용량이 기본 용량을 초과하는 경우 자동 스케일링이 스케일 인되지 않습니다.

1. 다른 조건을 추가하려면 **Add scale condition**(스케일링 조건 추가)을 선택합니다.

1. 자동 스케일링 설정이 완료되면 변경 내용을 저장합니다.

<a name="restart-ISE"></a>

## <a name="restart-ise"></a>ISE 다시 시작

DNS 서버나 DNS 서버 설정을 변경하면 ISE에 변경 내용이 적용될 수 있도록 ISE를 다시 시작해야 합니다. 중복성과 재활용 중 한 번에 하나씩 다시 시작되는 구성 요소 덕분에 Premium SKU ISE를 다시 시작하면 가동 중지 시간이 발생하지 않습니다. 그러나 중복성이 없기 때문에 개발자 SKU ISE에는 가동 중지 시간이 발생합니다. 자세한 내용은 [ISE SKU](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level)를 참조하세요.

1. [Azure Portal](https://portal.azure.com)에서 ISE로 이동합니다.

1. ISE 메뉴에서 **개요** 를 선택합니다. 개요 도구 모음에서 **다시 시작** 을 선택합니다.

   ![통합 서비스 환경 다시 시작](./media/connect-virtual-network-vnet-isolated-environment/restart-integration-service-environment.png)

<a name="delete-ise"></a>

## <a name="delete-ise"></a>ISE 삭제

더 이상 필요하지 않은 ISE 또는 ISE가 포함된 Azure 리소스 그룹을 삭제하기 전에 해당 리소스를 포함하는 Azure 리소스 그룹과 Azure 가상 네트워크에 대해 삭제를 차단할 수 있는 정책 또는 잠금이 적용되지 않았는지 확인하세요.

ISE를 삭제한 후 최대 9시간을 기다려야 Azure 가상 네트워크 또는 서브넷을 삭제할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [통합 서비스 환경에 리소스 추가](../logic-apps/add-artifacts-integration-service-environment-ise.md)
