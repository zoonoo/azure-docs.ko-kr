---
title: Dynamics 365에 연결
description: Azure Logic Apps를 사용 하 여 Dynamics 365 레코드 만들기 및 관리
services: logic-apps
ms.suite: integration
ms.reviewer: jdaly, logicappspm
ms.topic: conceptual
ms.date: 05/09/2020
tags: connectors
ms.openlocfilehash: 00bf8ea2b783e09711a95f203bdfcce0e6b90b2c
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/09/2020
ms.locfileid: "82994319"
---
# <a name="create-and-manage-records-in-dynamics-365-by-using-azure-logic-apps"></a>Azure Logic Apps를 사용 하 여 Dynamics 365에서 레코드 만들기 및 관리

Dynamics 365은 [Common Data Service](https://docs.microsoft.com/powerapps/maker/common-data-service/data-platform-intro)를 사용 합니다. Dynamics 365에 연결 하는 경우 [Common Data Service 커넥터](https://docs.microsoft.com/connectors/commondataservice/)를 사용 합니다.

> [!IMPORTANT]
> [Dynamics 365 커넥터](https://docs.microsoft.com/connectors/dynamicscrmonline/) 는 더 이상 사용 되지 않지만 제거 될 때까지 계속 작동 합니다. 새 논리 앱에는 Dynamics 365 커넥터를 사용 하지 마세요. Dynamics 365 커넥터 제거에 대 한 타임 라인이 아직 발표 되지 않았습니다. 기존 논리 앱을 Common Data Service 커넥터 또는 계획 된 다른 새 커넥터로 변환할 필요가 없지만 커넥터가 제거 될 때 논리 앱을 변환 해야 합니다. 자세한 내용은 [Dynamics 365 Connector가 더 이상 사용 되지 않음](https://docs.microsoft.com/power-platform/important-changes-coming)을 참조 하세요.
>
> [Common Data Service 커넥터](https://docs.microsoft.com/connectors/commondataservice/) 는 사용 되지 않는 Dynamics 365 커넥터와 동일한 기능을 제공 하지만 안정성을 높이는 향상 된 기능을 포함 합니다. 논리 앱에서 Common Data Service 커넥터를 사용 하는 방법에 대 한 자세한 내용은 [Azure Logic Apps를 사용 하 여 Common Data Service 레코드 만들기 및 관리](../connectors/connect-common-data-service.md)를 참조 하세요.

Common Data Service에 대 한 자세한 내용은 다음 항목을 참조 하세요.

* [자세한 정보: Common Data Service 시작](https://docs.microsoft.com/learn/modules/get-started-with-powerapps-common-data-service/)
* [자세한 정보: 전원 플랫폼 및 Common Data Service를 사용 하 여 Dynamics 365 데이터를 연결 하 고 분석 합니다.](https://docs.microsoft.com/learn/wwl/connect-analyze-dynamics-365-data/)