---
title: "Azure Service Bus 인증 및 권한 부여 | Microsoft Docs"
description: "SAS(공유 액세스 서명) 인증을 사용하여 Service Bus에 대해 앱을 인증합니다."
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 18bad0ed-1cee-4a5c-a377-facc4785c8c9
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/09/2017
ms.author: sethm
ms.translationtype: HT
ms.sourcegitcommit: 14915593f7bfce70d7bf692a15d11f02d107706b
ms.openlocfilehash: 28fb41499c919e5006f1be7daa97610c2a0583af
ms.contentlocale: ko-kr
ms.lasthandoff: 08/10/2017

---
# <a name="service-bus-authentication-and-authorization"></a>Service Bus 인증 및 권한 부여

응용 프로그램은 SAS(공유 액세스 서명) 인증을 사용하여 Azure Service Bus에 인증할 수 있습니다. 공유 액세스 서명 인증을 사용하면 응용 프로그램을 네임 스페이스 또는 특정 권한이 연관된 엔터티에서 구성된 선택키를 사용하여 Service Bus에 인증할 수 있습니다. 그런 다음 이 키를 사용하여 클라이언트가 Service Bus를 인증하는 데 사용할 수 있는 공유 액세스 서명 토큰을 생성할 수 있습니다.

> [!IMPORTANT]
> ACS는 이제 사용되지 않으므로 Azure Active Directory Access Control(Access Control Service 또는 ACS라고도 함) 대신 SAS를 사용해야 합니다. SAS는 Service Bus에 대한 단순하고 유연하며 사용이 쉬운 인증 체계를 제공합니다. 응용 프로그램은 권한 있는 "사용자"의 개념을 관리할 필요가 없는 시나리오에서 SAS를 사용할 수 있습니다. 자세한 내용은 [이 블로그 게시물](https://blogs.msdn.microsoft.com/servicebus/2017/06/01/upcoming-changes-to-acs-enabled-namespaces/)에 게시해 주세요.

## <a name="shared-access-signature-authentication"></a>공유 액세스 서명 인증

[SAS 인증](service-bus-sas.md)을 사용하면 특정 권한으로 Service Bus 리소스에 사용자 액세스 권한을 부여할 수 있습니다. Service Bus에서 SAS 인증은 Service Bus 리소스에 연결된 권한이 있는 암호화 키의 구성을 포함합니다. 클라이언트는 액세스된 리소스 URI 및 구성된 키로 서명된 만료로 구성된 SAS 토큰을 제공하여 해당 리소스에 대한 액세스를 얻을 수 있습니다.

Service Bus 네임 스페이스에서 SAS에 대한 키를 구성할 수 있습니다. 키는 해당 네임 스페이스의 모든 메시징 엔터티에 적용됩니다. 또한 Service Bus 큐 및 항목에 키를 구성할 수 있습니다. SAS는 [Azure Relay](../service-bus-relay/relay-authentication-and-authorization.md)에서도 지원됩니다.

SAS를 사용하려면 네임스페이스, 큐 또는 토픽에서 [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) 개체를 구성할 수 있습니다. 이 규칙은 다음 요소로 구성됩니다.

* *KeyName* 입니다.
* *PrimaryKey* 는 SAS 토큰을 서명/확인하는 데 사용되는 암호화 키입니다.
* *SecondaryKey* 는 SAS 토큰을 서명/확인하는 데 사용되는 암호화 키입니다.
* *권한* 입니다.

네임 스페이스 수준에서 구성된 권한 부여 규칙은 해당 키를 사용하여 서명된 토큰으로 클라이언트에 대한 네임 스페이스의 모든 엔터티에 액세스를 부여할 수 있습니다. 이러한 권한 부여 규칙을 Service Bus 네임 스페이스, 큐 또는 항목에서 최대 12개까지 구성할 수 있습니다. 기본적으로 모든 권한이 있는 [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) 은 처음으로 프로비전될 때 모든 네임스페이스에 대해 구성됩니다.

엔터티에 액세스하려면 클라이언트는 특정 [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule)을 사용하여 생성된 SAS 토큰이 필요합니다. 액세스를 요청하는 리소스 URI로 구성된 리소스 문자열의 HMAC-SHA256 및 권한 부여 규칙에 연결된 암호화 키가 있는 만료를 사용하여 SAS 토큰을 생성합니다.

Service Bus에 대한 SAS 인증 지원은 Azure.NET SDK 버전 2.0 이후에 포함됩니다. SAS는 [SharedAccessAuthorizationRule](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule)에 대한 지원을 포함합니다. 연결 문자열을 매개 변수로 허용하는 모든 API는 SAS 연결 문자열에 대한 지원을 포함합니다.

## <a name="next-steps"></a>다음 단계

- SAS에 대한 자세한 내용은 [공유 액세스 서명을 사용한 Service Bus 인증](service-bus-sas.md)을 계속 읽으세요.
- [ACS 지원 네임스페이스로 변경](https://blogs.msdn.microsoft.com/servicebus/2017/06/01/upcoming-changes-to-acs-enabled-namespaces/)
- Azure Relay 인증 및 권한 부여에 관한 해당 정보는 [Azure Relay 인증 및 권한 부여](../service-bus-relay/relay-authentication-and-authorization.md)를 참조하세요. 


