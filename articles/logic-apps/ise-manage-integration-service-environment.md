---
title: Azure Logic Apps에서 통합 서비스 환경 관리
description: Azure Logic Apps에 대 한 ISE (통합 서비스 환경)에서 네트워크 상태를 확인 하 고 논리 앱, 연결, 사용자 지정 커넥터 및 통합 계정 관리
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: 1d91813e0f39207bcf7768de89600a6bdee0fc53
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/05/2020
ms.locfileid: "78358998"
---
# <a name="manage-your-integration-service-environment-ise-in-azure-logic-apps"></a>Azure Logic Apps에서 ISE (통합 서비스 환경) 관리

Ise [(통합 서비스 환경)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) 의 네트워크 상태를 확인 하 고 ise에 존재 하는 논리 앱, 연결, 통합 계정 및 커넥터를 관리 하려면이 항목의 단계를 따르세요. ISE에 이러한 아티팩트를 추가 하려면 [통합 서비스 환경에 아티팩트 추가](../logic-apps/add-artifacts-integration-service-environment-ise.md)를 참조 하세요.

## <a name="view-your-ise"></a>ISE 보기

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. 포털의 검색 상자에 "integration service environment"를 입력 한 다음 **Integration Service environment**를 선택 합니다.

   ![통합 서비스 환경 찾기](./media/ise-manage-integration-service-environment/find-integration-service-environment.png)

1. 결과 목록에서 통합 서비스 환경을 선택 합니다.

   ![통합 서비스 환경 선택](./media/ise-manage-integration-service-environment/select-integration-service-environment.png)

1. 다음 섹션을 계속 진행 하 여 ISE에서 논리 앱, 연결, 커넥터 또는 통합 계정을 찾습니다.

<a name="check-network-health"></a>

## <a name="check-network-health"></a>네트워크 상태 확인

ISE 메뉴의 **설정**에서 **네트워크 상태**를 선택 합니다. 이 창에는 서브넷의 상태 및 다른 서비스에 대 한 아웃 바운드 종속성이 표시 됩니다.

![네트워크 상태 확인](./media/ise-manage-integration-service-environment/ise-check-network-health.png)

<a name="find-logic-apps"></a>

## <a name="manage-your-logic-apps"></a>논리 앱 관리

ISE에 있는 논리 앱을 보고 관리할 수 있습니다.

1. ISE 메뉴의 **설정**아래에서 **논리 앱**을 선택 합니다.

   ![논리 앱 보기](./media/ise-manage-integration-service-environment/ise-find-logic-apps.png)

1. ISE에서 더 이상 필요 하지 않은 논리 앱을 제거 하려면 해당 논리 앱을 선택한 다음 **삭제**를 선택 합니다. 삭제를 확인 하려면 **예**를 선택 합니다.

<a name="find-api-connections"></a>

## <a name="manage-api-connections"></a>API 연결 관리

ISE에서 실행 되는 논리 앱에서 만든 연결을 보고 관리할 수 있습니다.

1. ISE 메뉴의 **설정**에서 **API 연결**을 선택 합니다.

   ![API 연결 보기](./media/ise-manage-integration-service-environment/ise-find-api-connections.png)

1. ISE에서 더 이상 필요 하지 않은 연결을 제거 하려면 해당 연결을 선택한 다음 **삭제**를 선택 합니다. 삭제를 확인 하려면 **예**를 선택 합니다.

<a name="manage-api-connectors"></a>

## <a name="manage-ise-connectors"></a>ISE 커넥터 관리

ISE에 배포 된 API 커넥터를 보고 관리할 수 있습니다.

1. ISE 메뉴의 **설정**에서 **관리 되는 커넥터**를 선택 합니다.

   ![관리 되는 커넥터 보기](./media/ise-manage-integration-service-environment/ise-view-managed-connectors.png)

1. ISE에서 사용 하지 않으려는 커넥터를 제거 하려면 해당 커넥터를 선택한 다음 **삭제**를 선택 합니다. 삭제를 확인 하려면 **예**를 선택 합니다.

<a name="find-custom-connectors"></a>

## <a name="manage-custom-connectors"></a>사용자 지정 커넥터 관리

ISE에 배포한 사용자 지정 커넥터를 보고 관리할 수 있습니다.

1. ISE 메뉴의 **설정**에서 **사용자 지정 커넥터**를 선택 합니다.

   ![사용자 지정 커넥터 찾기](./media/ise-manage-integration-service-environment/ise-find-custom-connectors.png)

1. ISE에서 더 이상 필요 하지 않은 사용자 지정 커넥터를 제거 하려면 해당 커넥터를 선택한 다음 **삭제**를 선택 합니다. 삭제를 확인 하려면 **예**를 선택 합니다.

<a name="find-integration-accounts"></a>

## <a name="manage-integration-accounts"></a>통합 계정 관리

1. ISE 메뉴의 **설정**에서 **통합 계정**을 선택 합니다.

   ![통합 계정 찾기](./media/ise-manage-integration-service-environment/ise-find-integration-accounts.png)

1. 더 이상 필요 하지 않을 때 ISE에서 통합 계정을 제거 하려면 해당 통합 계정을 선택한 다음 **삭제**를 선택 합니다.

## <a name="next-steps"></a>다음 단계

* [격리된 논리 앱에서 Azure 가상 네트워크에 연결](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)하는 방법 알아보기
