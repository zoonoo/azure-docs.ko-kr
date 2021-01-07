---
title: 이벤트 처리기에 대 한 이벤트 배달 인증 (Azure Event Grid)
description: 이 문서에서는 Azure Event Grid에서 이벤트 처리기에 대 한 배달을 인증 하는 다양 한 방법을 설명 합니다.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: abe16c9598c8c10caa832150aafac997dd7f1624
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87460646"
---
# <a name="authenticate-event-delivery-to-event-handlers-azure-event-grid"></a>이벤트 처리기에 대 한 이벤트 배달 인증 (Azure Event Grid)
이 문서에서는 이벤트 처리기에 대 한 이벤트 전달 인증에 대 한 정보를 제공 합니다. 또한 Azure Active Directory (Azure AD) 또는 공유 암호를 사용 하 여 Event Grid에서 이벤트를 수신 하는 데 사용 되는 webhook 끝점의 보안을 설정 하는 방법을 보여 줍니다.

## <a name="use-system-assigned-identities-for-event-delivery"></a>이벤트 배달에 시스템 할당 id 사용
토픽 또는 도메인에 대해 시스템 할당 관리 id를 사용 하도록 설정 하 고이 id를 사용 하 여 Service Bus 큐 및 토픽, event hubs, 저장소 계정 등의 지원 되는 대상으로 이벤트를 전달할 수 있습니다.

단계는 다음과 같습니다. 

1. 시스템이 할당 한 id를 사용 하 여 토픽 또는 도메인을 만들거나, id를 사용 하도록 기존 토픽 또는 도메인을 업데이트 합니다. 
1. 대상의 적절 한 역할 Service Bus (예: Service Bus 큐)에 id를 추가 합니다.
1. 이벤트 구독을 만들 때 id를 사용 하 여 대상에 이벤트를 전달 하도록 설정 합니다. 

자세한 단계별 지침은 [관리 id를 사용 하 여 이벤트 전달](managed-service-identity.md)을 참조 하세요.


## <a name="authenticate-event-delivery-to-webhook-endpoints"></a>웹후크 엔드포인트에 대한 이벤트 전달 인증
다음 섹션에서는 웹후크 엔드포인트에 대한 이벤트 전달을 인증하는 방법을 설명합니다. 사용하는 방법에 관계 없이 유효성 검사 핸드셰이크 메커니즘을 사용해야 합니다. 자세한 내용은 [웹후크 이벤트 전달](webhook-event-delivery.md)을 참조하세요. 


### <a name="using-azure-active-directory-azure-ad"></a>Azure AD(Azure Active Directory) 사용
Azure AD를 사용하여 Event Grid에서 이벤트를 수신하는 데 사용되는 Webhook 엔드포인트를 보호할 수 있습니다. Azure AD 애플리케이션을 만들고 애플리케이션에서 Event Grid 권한을 부여하는 역할 및 서비스 주체를 만들고 Azure AD 애플리케이션을 사용하도록 이벤트 구독을 구성해야 합니다. [Azure Active Directory를 Event Grid로 구성하는 방법에 대해 알아봅니다](secure-webhook-delivery.md).

### <a name="using-client-secret-as-a-query-parameter"></a>클라이언트 암호를 쿼리 매개 변수로 사용
또한 이벤트 구독을 만들 때 지정된 Webhook 대상 URL에 쿼리 매개 변수를 추가하여 Webhook 엔드포인트를 보호할 수 있습니다. 쿼리 매개 변수 중 하나를 [액세스 토큰](https://en.wikipedia.org/wiki/Access_token)과 같은 클라이언트 암호 또는 공유 비밀로 설정합니다. Event Grid 서비스는 Webhook에 대한 모든 이벤트 전달 요청에 모든 쿼리 매개 변수를 포함합니다. Webhook 서비스는 암호를 검색하고 유효성을 검사할 수 있습니다. 클라이언트 암호를 업데이트하는 경우에는 이벤트 구독도 업데이트해야 합니다. 이 비밀 순환 동안 전달 오류가 발생하지 않도록 하려면 Webhook가 새 암호로 이벤트 구독을 업데이트하기 전에 제한된 기간 동안 이전 및 새 비밀을 모두 수락하도록 설정합니다. 

쿼리 매개 변수는 클라이언트 암호를 포함할 수 있으므로 신중하게 처리됩니다. 암호화된 상태로 저장되며 서비스 운영자가 액세스할 수 없습니다. 또한 서비스 로그/추적의 일부로 기록되지 않습니다. 이벤트 구독 속성을 검색할 때 기본적으로 대상 쿼리 매개 변수가 반환되지 않습니다. 예를 들어 [--include-full-endpoint-url](/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az-eventgrid-event-subscription-show) 매개 변수는 Azure [CLI](/cli/azure?view=azure-cli-latest)에서 사용해야 합니다.

웹후크에 이벤트를 전달하는 방법에 대한 자세한 내용은 [웹후크 이벤트 전달](webhook-event-delivery.md)을 참조하세요.

> [!IMPORTANT]
Azure Event Grid는 **HTTPS** 웹후크 엔드포인트만 지원합니다. 


## <a name="next-steps"></a>다음 단계
게시 클라이언트 인증 항목 또는 도메인에 이벤트를 게시 하는 방법에 대해 알아보려면 [게시 클라이언트 인증](security-authenticate-publishing-clients.md) 을 참조 하세요. 
