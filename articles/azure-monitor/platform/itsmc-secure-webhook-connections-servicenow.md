---
title: ServiceNow를 사용 하 여 Azure Monitor에서 IT 서비스 관리 커넥터 보안 내보내기
description: 이 문서에서는 Azure Monitor에서 보안 내보내기의 ServiceNow를 사용 하 여 ITSM 제품/서비스를 연결 하는 방법을 보여 줍니다.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/31/2020
ms.openlocfilehash: fc384ffbc246f5ce9fa84b8161cbc4a5226fa5c8
ms.sourcegitcommit: 8be279f92d5c07a37adfe766dc40648c673d8aa8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/31/2020
ms.locfileid: "97841191"
---
# <a name="connect-servicenow-to-azure-monitor"></a>Azure Monitor에 ServiceNow 연결

다음 섹션에서는 Azure에서 ServiceNow 제품 및 보안 내보내기를 연결 하는 방법에 대 한 세부 정보를 제공 합니다.

## <a name="prerequisites"></a>필수 구성 요소

다음 필수 구성 요소를 충족 하는지 확인 합니다.

* Azure AD가 등록 됩니다.
* 지원 되는 버전의 ServiceNow 이벤트 관리-ITOM (버전 올랜도 이상)이 있습니다.

## <a name="configure-the-servicenow-connection"></a>ServiceNow 연결 구성

1. Https://(인스턴스 이름) 링크를 사용 합니다. service-이제 .com/api/sn_em_connector/em/inbound_event? source = azuremonitor는 보안 내보내기 정의에 대 한 URI를 모니터링 합니다.

2. 버전에 따라 다음 지침을 따르세요.
   * [파리](https://docs.servicenow.com/bundle/paris-it-operations-management/page/product/event-management/task/azure-events-authentication.html)
   * [올랜도](https://docs.servicenow.com/bundle/orlando-it-operations-management/page/product/event-management/task/azure-events-authentication.html)
   * [뉴욕](https://docs.servicenow.com/bundle/newyork-it-operations-management/page/product/event-management/task/azure-events-authentication.html)