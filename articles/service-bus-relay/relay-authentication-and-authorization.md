---
title: "Azure Relay 인증 및 권한 부여 | Microsoft Docs"
description: "Azure Relay의 SAS(공유 액세스 서명) 인증 개요"
services: service-bus-relay
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/21/2017
ms.author: sethm
translationtype: Human Translation
ms.sourcegitcommit: 356de369ec5409e8e6e51a286a20af70a9420193
ms.openlocfilehash: 64874564c54dd37108f1075fb54181f2324cb229
ms.lasthandoff: 03/27/2017


---
# <a name="azure-relay-authentication-and-authorization"></a>Azure Relay 인증 및 권한 부여
응용 프로그램은 SAS(공유 액세스 서명) 인증을 사용하여 Azure Relay에 인증할 수 있습니다. [Service Bus 메시징](../service-bus-messaging/service-bus-authentication-and-authorization.md)과 유사하게 공유 액세스 서명 인증을 사용하면 응용 프로그램을 Relay 네임스페이스에서 구성된 선택키를 사용하여 Azure Relay 서비스에 인증할 수 있습니다. 그런 다음 이 키를 사용하여 클라이언트가 Relay 서비스를 인증하는 데 사용할 수 있는 공유 액세스 서명 토큰을 생성할 수 있습니다.

## <a name="shared-access-signature-authentication"></a>공유 액세스 서명 인증
[SAS 인증](../service-bus-messaging/service-bus-sas.md)을 사용하면 특정 권한으로 Service Bus Relay 리소스에 사용자 액세스 권한을 부여할 수 있습니다. SAS 인증은 리소스에 연결된 권한이 있는 암호화 키의 구성을 포함합니다. 클라이언트는 액세스된 리소스 URI 및 구성된 키로 서명된 만료로 구성된 SAS 토큰을 제공하여 해당 리소스에 대한 액세스를 얻을 수 있습니다.

Relay 네임스페이스에서 SAS에 대한 키를 구성할 수 있습니다. Service Bus 메시징과 달리 [Relay 하이브리드 연결](relay-hybrid-connections-protocol.md)은 무단 또는 익명으로 보낸 사람을 지원합니다. 포털에서 다음 화면에 표시된 것처럼 만들 때 엔터티에 대한 익명 액세스를 허용할 수 있습니다.

![][0]

SAS를 사용하려면 다음을 구성하는 Relay 네임스페이스에서 [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) 개체를 구성할 수 있습니다.

* *KeyName* 입니다.
* *PrimaryKey* 는 SAS 토큰을 서명/확인하는 데 사용되는 암호화 키입니다.
* *SecondaryKey* 는 SAS 토큰을 서명/확인하는 데 사용되는 암호화 키입니다.
* *권한* 입니다.

네임스페이스 수준에서 구성된 권한 부여 규칙은 해당 키를 사용하여 서명된 토큰으로 클라이언트에 대한 네임스페이스의 모든 relay 연결에 액세스를 부여할 수 있습니다. 권한 부여 규칙은 Relay 네임스페이스에서 최대 12개까지 구성할 수 있습니다. 기본적으로 모든 권한이 있는 [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) 은 처음으로 프로비전될 때 모든 네임스페이스에 대해 구성됩니다.

엔터티에 액세스하려면 클라이언트는 특정 [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule)을 사용하여 생성된 SAS 토큰이 필요합니다. 액세스를 요청하는 리소스 URI로 구성된 리소스 문자열의 HMAC-SHA256 및 권한 부여 규칙에 연결된 암호화 키가 있는 만료를 사용하여 SAS 토큰을 생성합니다.

Azure Relay에 대한 SAS 인증 지원은 Azure.NET SDK 버전 2.0 이상에 포함됩니다. SAS는 [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule)에 대한 지원을 포함합니다. 연결 문자열을 매개 변수로 허용하는 모든 API는 SAS 연결 문자열에 대한 지원을 포함합니다.

## <a name="next-steps"></a>다음 단계
- SAS에 대한 자세한 내용은 [공유 액세스 서명을 사용한 Service Bus 인증](../service-bus-messaging/service-bus-sas.md)을 계속 읽으세요.
- 하이브리드 연결 기능에 대한 자세한 내용은 [Azure Relay 하이브리드 연결 프로토콜 가이드](relay-hybrid-connections-protocol.md)를 참조하세요.
- Service Bus 메시징 인증 및 권한 부여에 관한 해당 정보는 [Service Bus 인증 및 권한 부여](../service-bus-messaging/service-bus-authentication-and-authorization.md)를 참조하세요. 

[0]: ./media/relay-authentication-and-authorization/hcanon.png
