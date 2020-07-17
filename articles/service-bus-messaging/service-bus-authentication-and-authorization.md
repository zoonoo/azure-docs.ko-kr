---
title: Azure Service Bus 인증 및 권한 부여 | Microsoft Docs
description: SAS(공유 액세스 서명) 인증을 사용하여 Service Bus에 대해 앱을 인증합니다.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 56461c13cf6589b5f66f05837e1bcaa6a49a58c7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85337728"
---
# <a name="service-bus-authentication-and-authorization"></a>Service Bus 인증 및 권한 부여
Azure AD (azure Activity Directory) 및 SAS (공유 액세스 서명) 라는 두 가지 방법으로 Azure Service Bus 리소스에 대 한 액세스를 인증 하 고 권한을 부여할 수 있습니다. 이 문서에서는 이러한 두 가지 유형의 보안 메커니즘을 사용 하는 방법에 대해 자세히 설명 합니다. 

## <a name="azure-active-directory"></a>Azure Active Directory
Service Bus 리소스에 대 한 Azure AD 통합은 클라이언트의 리소스 액세스를 세부적으로 제어 하기 위해 RBAC (역할 기반 액세스 제어)를 제공 합니다. RBAC (역할 기반 액세스 제어)를 사용 하 여 사용자, 그룹 또는 응용 프로그램 서비스 사용자 일 수 있는 보안 주체에 권한을 부여할 수 있습니다. 보안 주체는 OAuth 2.0 토큰을 반환 하기 위해 Azure AD에서 인증 됩니다. 토큰은 Service Bus 리소스 (큐, 토픽 등)에 액세스 하는 요청에 권한을 부여 하는 데 사용할 수 있습니다.

Azure AD를 사용 하 여 인증 하는 방법에 대 한 자세한 내용은 다음 문서를 참조 하세요.

- [관리되는 ID를 사용하여 인증](service-bus-managed-service-identity.md)
- [애플리케이션에서 인증](authenticate-application.md)

> [!NOTE]
> [Service Bus REST API](/rest/api/servicebus/) 는 Azure AD에서 OAuth 인증을 지원 합니다.

> [!IMPORTANT]
> Azure AD에서 반환 된 OAuth 2.0 토큰을 사용 하는 사용자 또는 응용 프로그램에 대 한 권한 부여는 SAS (공유 액세스 서명)를 통해 뛰어난 보안과 사용 편의성을 제공 합니다. Azure AD를 사용 하는 경우 코드에 토큰을 저장 하 고 잠재적인 보안 취약점을 초래할 필요가 없습니다. 가능 하면 Azure Service Bus 응용 프로그램에서 Azure AD를 사용 하는 것이 좋습니다. 

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

> [!IMPORTANT]
> Service Bus에서 Azure Active Directory Access Control (Access Control Service 또는 ACS 라고도 함)를 사용 하는 경우 이제이 메서드에 대 한 지원이 제한 됩니다. SAS를 사용 하거나 Azure AD에서 OAuth 2.0 인증 [을 사용 하도록 응용 프로그램을 마이그레이션해야](service-bus-migrate-acs-sas.md) 합니다 (권장). ACS 사용 중단에 대 한 자세한 내용은 [이 블로그 게시물](https://blogs.msdn.microsoft.com/servicebus/2017/06/01/upcoming-changes-to-acs-enabled-namespaces/)을 참조 하세요.

## <a name="next-steps"></a>다음 단계
Azure AD를 사용 하 여 인증 하는 방법에 대 한 자세한 내용은 다음 문서를 참조 하세요.

- [관리 id를 사용 하 여 인증](service-bus-managed-service-identity.md)
- [응용 프로그램에서 인증](authenticate-application.md)

SAS를 사용 하 여 인증 하는 방법에 대 한 자세한 내용은 다음 문서를 참조 하세요.

- [SAS를 사용한 인증](service-bus-sas.md)
