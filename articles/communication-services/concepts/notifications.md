---
title: Azure Communication Services의 알림
titleSuffix: An Azure Communication Services concept document
description: Azure Communication Services를 기반으로 하는 앱의 사용자에게 알림을 보냅니다.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 09/30/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 3e68e65a5c2ed73a8fb6d8e5d01c645e05ca5157
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/21/2020
ms.locfileid: "92320712"
---
# <a name="communication-services-notifications"></a>Communication Services 알림

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

Azure Communication Services 채팅 및 통화 클라이언트 라이브러리는 신호 메시지를 효율적이고 안정적인 방식으로 연결된 클라이언트에 푸시할 수 있는 실시간 메시징 채널을 만듭니다. 이를 통해 복잡한 HTTP 폴링 로직을 구현하지 않고도 다양한 실시간 통신 기능을 애플리케이션에 구축할 수 있습니다. 하지만 모바일 애플리케이션에서 이 신호 채널은 애플리케이션이 포그라운드에서 활성 상태인 경우에만 연결된 상태를 유지합니다. 애플리케이션이 백그라운드에 있는 동안 걸려오는 전화나 채팅 메시지를 사용자가 받도록 하려면 푸시 알림을 사용해야 합니다.

푸시 알림을 사용하면 애플리케이션에서 사용자의 모바일 디바이스로 정보를 보낼 수 있습니다. 푸시 알림을 사용하여 대화 상자를 표시하거나 소리를 재생하거나 걸려오는 전화 UI를 표시할 수 있습니다. Azure Communication Services는 앱에 푸시 알림을 추가하는 데 사용할 수 있는 [Azure Event Grid](https://docs.microsoft.com/azure/event-grid/overview) 및 [Azure Notification Hubs](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-push-notification-overview)와의 통합을 제공합니다.

## <a name="trigger-push-notifications-via-azure-event-grid"></a>Azure Event Grid를 통해 푸시 알림 트리거

Azure Communication Services는 [Azure Event Grid](https://azure.microsoft.com/services/event-grid/)와 통합되어 안정적이고 확장 가능하며 안전한 방식으로 실시간 이벤트 알림을 제공합니다. 이 통합을 활용하여 [Azure 함수](https://docs.microsoft.com/azure/azure-functions/functions-overview) 또는 웹후크를 트리거하는 이벤트 그리드 구독을 만들어서 사용자에게 모바일 푸시 알림을 제공하는 알림 서비스를 만들 수 있습니다.

:::image type="content" source="./media/notifications/acs-events-int.png" alt-text="Communication Services가 Event Grid와 통합되는 방식을 보여주는 다이어그램":::

[Azure Communication Services에서 이벤트 처리](./event-handling.md)에 대해 자세히 알아보세요.

## <a name="deliver-push-notifications-via-azure-notification-hubs"></a>Azure Notification Hubs를 통해 푸시 알림 제공

걸려오는 전화를 받을 때 사용자의 모바일 디바이스에 푸시 알림을 자동으로 보내기 위해 Azure Notification Hub를 Communication Services 리소스에 연결할 수 있습니다. 이러한 푸시 알림을 사용하여 백그라운드에서 애플리케이션을 깨우고 사용자가 통화를 수락하거나 거절할 수 있는 UI를 표시해야 합니다. 

:::image type="content" source="./media/notifications/acs-anh-int.png" alt-text="Communication Services가 Event Grid와 통합되는 방식을 보여주는 다이어그램":::

Communication Services는 Azure Notification Hub를 통과 서비스로 사용하여 [Direct Send](https://docs.microsoft.com/rest/api/notificationhubs/direct-send) API를 통해 다양한 플랫폼별 푸시 알림 서비스와 통신합니다. 이렇게 하면 기존 Azure Notification Hub 리소스 및 구성을 다시 사용하여 대기 시간이 짧고 안정적인 통화 알림을 애플리케이션에 전달할 수 있습니다.

> [!NOTE]
> 현재는 푸시 알림 호출만 지원됩니다.

### <a name="notification-hub-provisioning"></a>Notification Hub 프로비저닝 

Notification Hubs를 사용하여 클라이언트 디바이스에 푸시 알림을 전달하려면 Communication Services 리소스와 동일한 구독 내에서 [Notification Hub를 만듭니다](https://docs.microsoft.com/azure/notification-hubs/create-notification-hub-portal). Azure Notification Hubs는 사용할 플랫폼 알림 서비스에 대해 구성되어야 합니다. Notification Hubs에서 클라이언트 앱의 푸시 알림을 받는 방법을 알아보려면 [Notification Hubs 시작](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-android-push-notification-google-fcm-get-started)을 참조하고 페이지 위쪽의 드롭다운 목록에서 대상 클라이언트 플랫폼을 선택하세요.

> [!NOTE]
> 현재 APN 및 FCM 플랫폼이 지원됩니다.

알림 허브가 구성되면 Azure Resource Manager 클라이언트를 사용하여 또는 Azure Portal을 통해 허브에 대한 연결 문자열을 제공하여 Communication Services 리소스에 연결할 수 있습니다. 연결 문자열에는 "보내기" 권한이 포함되어야 합니다. 허브 전용으로 "보내기" 권한이 있는 액세스 정책을 따로 만드는 것이 좋습니다. [Notification Hubs 보안 및 액세스 정책](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-push-notification-security)에 대한 자세한 정보

> [!IMPORTANT]
> Apple Push Notification Service VOIP 알림을 사용하도록 설정하려면 알림 허브의 이름을 접미사가 `.voip`인 애플리케이션 번들 ID로 설정해야 합니다. [Notification Hubs를 통해 APNS VOIP 사용](https://docs.microsoft.com/azure/notification-hubs/voip-apns)을 참조하세요.

#### <a name="using-the-azure-resource-manager-client-to-configure-the-notification-hub"></a>Azure Resource Manager 클라이언트를 사용하여 Notification Hub 구성

Azure Resource Manager에 로그인하려면 다음을 실행하고 자격 증명을 사용하여 로그인합니다.

```console
armclient login
```

 로그인이 성공적으로 완료되면 다음을 실행하여 알림 허브를 프로비저닝합니다.

```console
armclient POST /subscriptions/<sub_id>/resourceGroups/<resource_group>/providers/Microsoft.Communication/CommunicationServices/<resource_id>/linkNotificationHub?api-version=2020-08-20-preview "{'connectionString': '<connection_string>','resourceId': '<resource_id>'}"
```

#### <a name="using-the-azure-portal-to-configure-the-notification-hub"></a>Azure Portal을 사용하여 알림 허브 구성

포털에서 Azure Communication Services 리소스로 이동합니다. Communication Services 리소스 내에서 Communication Services 페이지의 왼쪽 메뉴에 있는 푸시 알림을 선택하고 이전에 프로비저닝한 Notification Hub를 연결합니다. 여기에 연결 문자열과 리소스 ID를 제공해야 합니다.

:::image type="content" source="./media/notifications/acs-anh-portal-int.png" alt-text="Communication Services가 Event Grid와 통합되는 방식을 보여주는 다이어그램":::

> [!NOTE]
> Azure Notification Hub 연결 문자열이 업데이트되면 Communication Services 리소스도 업데이트해야 합니다.

#### <a name="device-registration"></a>디바이스 등록 

Communication Services에 디바이스 핸들을 등록하는 방법을 알아보려면 [음성 통화 빠른 시작](../quickstarts/voice-video-calling/getting-started-with-calling.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

* Azure Event Grid에 대한 소개는 [Event Grid란?](https://docs.microsoft.com/azure/event-grid/overview)을 참조하세요.
* Azure Notification Hub 개념에 대해 자세히 알아보려면 [Azure Notification Hubs 설명서](https://docs.microsoft.com/azure/notification-hubs/)를 참조하세요.
