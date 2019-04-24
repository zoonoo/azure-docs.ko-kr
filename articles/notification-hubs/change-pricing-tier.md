---
title: Notification Hubs 네임스페이스의 가격 책정 계층 변경 | Microsoft Docs
description: Azure Notification Hubs 네임스페이스의 가격 책정 계층을 변경하는 방법을 알아봅니다.
services: notification-hubs
author: jwargo
manager: patniko
editor: spelluru
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 01/28/2019
ms.author: jowargo
ms.openlocfilehash: 99ea21b3eb01a674a89c70a1b923f02e600cc3c5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60234512"
---
# <a name="change-pricing-tier-of-an-azure-notification-hubs-namespace"></a>Azure Notification Hubs 네임스페이스의 가격 책정 계층 변경
Notification Hubs는 **무료**, **기본** 및 **표준**의 세 가지 계층으로 제공됩니다. 이 문서에서는 Azure Notification Hubs 네임스페이스의 가격 책정 계층을 변경하는 방법을 설명합니다. 

## <a name="overview"></a>개요
Azure Notification Hubs에서 **허브**는 최소 리소스/엔터티입니다. 허브는 대개 애플리케이션 하나에 매핑되며 앱용으로 지원되는 각 플랫폼 알림 시스템용 인증서 하나를 포함할 수 있습니다. 애플리케이션은 하이브리드 또는 네이티브 및 플랫폼 간 애플리케이션일 수 있습니다.

**네임스페이스**는 Notification Hubs 컬렉션입니다. 일반적으로 각 네임스페이스는 서로 관련이 있으며 특정 용도로 사용되는 허브로 구성됩니다. 예를 들어 각기 다른 네임스페이스 3개(개별, 테스트, 프로덕션용으로 하나씩)를 사용할 수 있습니다. 

네임스페이스 수준에서 가격 책정 계층을 연결할 수 있습니다. Notification Hubs는 **무료**, **기본** 및 **표준**의 세 가지 계층을 지원합니다. 요구 사항에 맞는 네임스페이스용 계층을 사용할 수 있습니다. 다음 섹션에서는 Notification Hubs 네임스페이스의 가격 책정 계층을 변경하는 방법을 설명합니다. 

## <a name="use-azure-portal"></a>Azure Portal 사용 
Azure Portal 사용 시에는 네임스페이스 페이지 또는 허브 페이지에서 네임스페이스의 가격 책정 계층을 변경할 수 있습니다.  허브 페이지에서 계층을 변경하더라도 실제로는 네임스페이스 수준에서 계층이 변경됩니다. 이 경우 네임스페이스 및 해당 네임스페이스에 포함된 모든 허브의 가격 책정 계층이 변경됩니다. 

### <a name="change-tier-on-the-namespace-page"></a>네임스페이스 페이지에서 계층 변경
다음 절차에서는 네임스페이스 페이지에서 네임스페이스의 가격 책정 계층을 변경하는 단계를 제공합니다. 네임스페이스의 계층을 변경하면 해당 네임스페이스의 모든 허브에 변경 내용이 적용됩니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 왼쪽 메뉴에서 **모든 서비스**를 선택합니다. 
3. **사물 인터넷** 섹션에서 **Notification Hub Namespaces**를 선택합니다. 텍스트 옆의 별표(`*`)를 선택하면 왼쪽 탐색 모음의 **즐겨찾기** 아래에 해당 항목이 추가됩니다. 그러면 다음부터는 네임스페이스 페이지에 더 빠르게 액세스할 수 있습니다. 즐겨찾기에 추가한 **Notification Hub Namespaces**를 선택합니다. 

    ![모든 서비스 -> Notification Hub Namespaces](./media/change-pricing-tier/all-services-nhub.png)
1. **Notification Hub Namespaces** 페이지에서 가격 책정 계층을 변경할 네임스페이스를 선택합니다. 
2. 해당 네임스페이스의 **Notification Hub Namespace** 페이지 **Essentials** 섹션에서 네임스페이스의 현재 가격 책정 계층을 확인할 수 있습니다. 다음 이미지에서는 네임스페이스의 가격 책정 계층이 **무료**임을 확인할 수 있습니다. 

    ![네임스페이스 페이지의 현재 가격 책정 계층](./media/change-pricing-tier/pricing-tier-before.png)
1. 네임스페이스의 **Notification Hub Namespace** 페이지에서 **관리** 섹션의 **가격 책정 계층**을 선택합니다. 

    ![네임스페이스 페이지에서 가격 책정 계층 선택](./media/change-pricing-tier/namespace-select-pricing-menu.png)
6. 가격 책정 계층을 변경하고 **선택** 단추를 클릭합니다.    
7. **경고**에 계층 변경 작업 상태가 표시됩니다. 
8. **개요** 페이지로 전환합니다. **Essentials** 섹션의 **가격 책정 계층** 필드에 새 계층이 표시되는지 확인합니다.     
1. 이 단계는 선택 사항입니다. 네임스페이스에서 아무 허브나 선택합니다. **Essentials** 섹션에 같은 가격 책정 계층이 표시되는지 확인합니다. 네임스페이스의 모든 허브에서 같은 가격 책정 계층이 표시되어야 합니다. 

### <a name="change-tier-on-the-hub-page"></a>허브 페이지에서 계층 변경
다음 절차에서는 허브 페이지에서 네임스페이스의 가격 책정 계층을 변경하는 단계를 제공합니다. 허브 페이지에서부터 이러한 단계를 수행하더라도 실제로는 네임스페이스 및 해당 네임스페이스의 모든 허브에서 가격 책정 계층이 변경됩니다. 

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 왼쪽 메뉴에서 **모든 서비스**를 선택합니다.
3. **사물 인터넷** 섹션에서 **Notification Hubs**를 선택합니다. 
4. Notification **Hub**를 선택합니다. 
5. 왼쪽 메뉴에서 **가격 책정 계층**을 선택합니다. 
6. 가격 책정 계층을 변경하고 **선택** 단추를 클릭합니다. 이 작업을 수행하면 허브가 포함된 네임스페이스의 가격 책정 계층 설정이 변경됩니다. 따라서 네임스페이스 페이지와 모든 허브 페이지에 새 가격 책정 계층이 표시됩니다. 

## <a name="use-rest-api"></a>REST API 사용
다음의 리소스 공급자 REST API를 사용하여 현재 가격 책정 계층을 가져온 다음 업데이트할 수 있습니다. 

### <a name="get-current-pricing-tier-for-a-namespace"></a>네임스페이스의 현재 가격 책정 계층 가져오기
**현재 네임스페이스 계층**을 가져오려면 다음 예제와 같이 GET 명령을 전송합니다. 

```REST
GET: https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/notificationhubplan
```

### <a name="update-pricing-tier-for-a-namespace"></a>네임스페이스의 가격 책정 계층 업데이트
**네임스페이스 계층을 업데이트**하려면 다음 예제와 같이 PUT 명령을 전송합니다. 

```REST
PUT: https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/notificationhubplan
Body: <NotificationHubPlan xmlns:i="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/netservices/2010/10/servicebus/connect"><SKU>Standard</SKU></NotificationHubPlan>
```



## <a name="next-steps"></a>다음 단계
이러한 계층 및 가격 책정과 관련된 자세한 내용은 [Notification Hubs 가격 책정](https://azure.microsoft.com/pricing/details/notification-hubs/)을 참조하세요.
