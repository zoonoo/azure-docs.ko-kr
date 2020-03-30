---
title: Azure Service Bus 인증 및 권한 부여 | Microsoft Docs
description: SAS(공유 액세스 서명) 인증을 사용하여 Service Bus에 대해 앱을 인증합니다.
services: service-bus-messaging
documentationcenter: na
author: axisc
editor: spelluru
ms.assetid: 18bad0ed-1cee-4a5c-a377-facc4785c8c9
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/22/2019
ms.author: aschhab
ms.openlocfilehash: 7234e33c04e742c77630f8d87481c7831fb00bf2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "70013239"
---
# <a name="service-bus-authentication-and-authorization"></a>Service Bus 인증 및 권한 부여

애플리케이션은 SAS(공유 액세스 서명) 토큰 인증을 사용하여 Azure Service Bus 리소스에 액세스할 수 있습니다. SAS를 사용하면 애플리케이션은 토큰 발급자 및 Service Bus(또한 “공유”) 모두에 알려진 대칭 키를 사용하여 서명된 Service Bus에 대한 토큰 및 메시지를 수신/수신 대기하거나 보내는 사용 권한 등 특정 액세스 권한을 부여하는 규칙과 직접 연결된 키를 나타냅니다. SAS 규칙은 네임스페이스 또는 큐나 항목과 같은 엔터티에 직접 구성되어 세분화된 액세스 제어를 허용합니다.

SAS 토큰은 Service Bus 클라이언트에 의해 직접 생성되거나 또는 클라이언트에서 상호 작용하는 엔드포인트를 발급하는 일부 중간 토큰에 의해 생성될 수 있습니다. 예를 들어, 시스템은 해당 ID 및 시스템 액세스 권한을 증명하기 위해 클라이언트에서 Active Directory 권한 부여 보호 웹 서비스 엔드포인트를 호출해야 할 수 있습니다. 웹 서비스는 적절한 Service Bus 토큰을 반환합니다. 이 SAS 토큰은 Azure SDK에 포함된 Service Bus 토큰 공급자를 사용하여 쉽게 생성할 수 있습니다. 

> [!IMPORTANT]
> Azure Active Directory Access Control(Access Control Service 또는 ACS라고도 함)을 Service Bus와 함께 사용하는 경우 이제 이 메서드에 대한 지원이 제한되고 SAS를 사용하려면 애플리케이션을 마이그레이션해야 합니다. 자세한 내용은 [이 블로그 게시물](https://blogs.msdn.microsoft.com/servicebus/2017/06/01/upcoming-changes-to-acs-enabled-namespaces/) 및 [이 문서](service-bus-migrate-acs-sas.md)를 참조하세요.

## <a name="azure-active-directory"></a>Azure Active Directory
서비스 버스 리소스에 대한 Azure Active Directory(Azure AD) 통합은 클라이언트의 리소스에 대한 액세스에 대한 세분화된 제어를 위한 역할 기반 액세스 제어(RBAC)를 제공합니다. RBAC(역할 기반 액세스 제어)를 사용하여 사용자, 그룹 또는 응용 프로그램 서비스 주체일 수 있는 보안 주체에 권한을 부여할 수 있습니다. 보안 주체는 Azure AD에서 인증되어 OAuth 2.0 토큰을 반환합니다. 토큰은 Service Bus 리소스(큐, 토픽 등)에 액세스하는 요청을 승인하는 데 사용할 수 있습니다.

Azure AD인증에 대한 자세한 내용은 다음 문서를 참조하세요.

- [관리되는 ID를 사용하여 인증](service-bus-managed-service-identity.md)
- [애플리케이션에서 인증](authenticate-application.md)

> [!IMPORTANT]
> Azure AD에서 반환되는 OAuth 2.0 토큰을 사용하여 사용자 또는 응용 프로그램에 권한을 부여하면 SAS(공유 액세스 서명)를 통해 뛰어난 보안과 사용 편의성을 제공합니다. Azure AD를 사용하면 코드에 토큰을 저장하고 잠재적인 보안 취약점을 위험에 빠뜨릴 필요가 없습니다. 가능하면 Azure 서비스 버스 응용 프로그램과 함께 Azure AD를 사용하는 것이 좋습니다. 


## <a name="shared-access-signature"></a>공유 액세스 서명
[SAS 인증](service-bus-sas.md)을 사용하면 특정 권한으로 Service Bus 리소스에 사용자 액세스 권한을 부여할 수 있습니다. Service Bus에서 SAS 인증은 Service Bus 리소스에 연결된 권한이 있는 암호화 키의 구성을 포함합니다. 클라이언트는 액세스된 리소스 URI 및 구성된 키로 서명된 만료로 구성된 SAS 토큰을 제공하여 해당 리소스에 대한 액세스를 얻을 수 있습니다.

Service Bus 네임 스페이스에서 SAS에 대한 키를 구성할 수 있습니다. 키는 해당 네임스페이스의 모든 메시징 엔터티에 적용됩니다. 또한 Service Bus 큐 및 항목에 키를 구성할 수 있습니다. SAS는 [Azure Relay](../service-bus-relay/relay-authentication-and-authorization.md)에서도 지원됩니다.

SAS를 사용하려면 네임스페이스, 큐 또는 토픽에서 [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) 개체를 구성할 수 있습니다. 이 규칙은 다음 요소로 구성됩니다.

* *KeyName*: 규칙을 식별합니다.
* *PrimaryKey*: SAS 토큰을 서명/확인하는 데 사용되는 암호화 키입니다.
* *SecondaryKey*: SAS 토큰을 서명/확인하는 데 사용되는 암호화 키입니다.
* *Rights*: **수신**, **보내기** 또는 **관리**의 컬렉션을 나타냅니다.

네임 스페이스 수준에서 구성된 권한 부여 규칙은 해당 키를 사용하여 서명된 토큰으로 클라이언트에 대한 네임 스페이스의 모든 엔터티에 액세스를 부여할 수 있습니다. 이러한 권한 부여 규칙을 Service Bus 네임스페이스, 큐 또는 항목에서 최대 12개까지 구성할 수 있습니다. 기본적으로 모든 권한이 있는 [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) 은 처음으로 프로비전될 때 모든 네임스페이스에 대해 구성됩니다.

엔터티에 액세스하려면 클라이언트는 특정 [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule)을 사용하여 생성된 SAS 토큰이 필요합니다. 액세스를 요청하는 리소스 URI로 구성된 리소스 문자열의 HMAC-SHA256 및 권한 부여 규칙에 연결된 암호화 키가 있는 만료를 사용하여 SAS 토큰을 생성합니다.

Service Bus에 대한 SAS 인증 지원은 Azure.NET SDK 버전 2.0 이후에 포함됩니다. SAS는 [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule)에 대한 지원을 포함합니다. 연결 문자열을 매개 변수로 허용하는 모든 API는 SAS 연결 문자열에 대한 지원을 포함합니다.

## <a name="next-steps"></a>다음 단계

- SAS에 대한 자세한 내용은 [공유 액세스 서명을 사용한 Service Bus 인증](service-bus-sas.md)을 계속 읽으세요.
- [Azure Active Directory Access Control(ACS)에서 공유 액세스 서명 권한 부여로 마이그레이션](service-bus-migrate-acs-sas.md)하는 방법
- [ACS 지원 네임스페이스로 변경](https://blogs.msdn.microsoft.com/servicebus/2017/06/01/upcoming-changes-to-acs-enabled-namespaces/)
- Azure Relay 인증 및 권한 부여에 관한 해당 정보는 [Azure Relay 인증 및 권한 부여](../service-bus-relay/relay-authentication-and-authorization.md)를 참조하세요. 

