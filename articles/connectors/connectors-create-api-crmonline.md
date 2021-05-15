---
title: Dynamics 365에 연결
description: Azure Logic Apps를 사용하여 Dynamics 365 레코드 만들기 및 관리
services: logic-apps
ms.suite: integration
ms.reviewer: jdaly, logicappspm
ms.topic: conceptual
ms.date: 05/09/2020
tags: connectors
ms.openlocfilehash: fe1e31ce0bc842e1da18327d12e7a52562d84b53
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "87284033"
---
# <a name="create-and-manage-records-in-dynamics-365-by-using-azure-logic-apps"></a>Azure Logic Apps를 사용하여 Dynamics 365의 레코드 만들기 및 관리

Dynamics 365는 [Common Data Service](/powerapps/maker/common-data-service/data-platform-intro)를 사용합니다. Dynamics 365에 연결하는 경우 [Common Data Service 커넥터](/connectors/commondataservice/)를 사용합니다.

> [!IMPORTANT]
> [Dynamics 365 커넥터](/connectors/dynamicscrmonline/)는 더 이상 사용되지 않지만 제거할 때까지 계속 작동합니다. 새 논리 앱에는 Dynamics 365 커넥터를 사용하지 않아야 합니다. Dynamics 365 커넥터 제거에 대한 타임라인이 아직 발표되지 않았습니다. 기존 논리 앱을 Common Data Service 커넥터 또는 계획된 다른 새 커넥터로 변환할 필요가 없지만 커넥터가 제거될 때 논리 앱을 변환해야 합니다. 자세한 내용은 [Dynamics 365 커넥터가 더 이상 사용되지 않음](/power-platform/important-changes-coming)을 참조하세요.
>
> [Common Data Service 커넥터](/connectors/commondataservice/)는 사용되지 않는 Dynamics 365 커넥터와 동일한 기능을 제공하지만 안정성을 높이는 향상된 기능이 포함되어 있습니다. 논리 앱에서 Common Data Service 커넥터를 사용하는 방법에 대한 자세한 내용은 [Azure Logic Apps를 사용하여 Common Data Service 레코드 만들기 및 관리](../connectors/connect-common-data-service.md)를 참조하세요.

Common Data Service에 대한 자세한 내용은 다음 항목을 참조하세요.

* [알아보기: Common Data Service 시작](/learn/modules/get-started-with-powerapps-common-data-service/)
* [알아보기: Power Platform 및 Common Data Service를 사용하여 Dynamics 365 데이터 연결 및 분석](/learn/wwl/connect-analyze-dynamics-365-data/)
