<properties 
   pageTitle="서비스 버스 인증 및 권한 부여 | Microsoft Azure"
   description="공유 액세스 서명(SAS) 인증의 개요입니다."
   services="service-bus"
   documentationCenter="na"
   authors="sethmanheim"
   manager="timlt"
   editor="tysonn" />
<tags 
   ms.service="service-bus"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/04/2015"
   ms.author="sethm" />

# 서비스 버스 인증 및 권한 부여

응용 프로그램은 공유 액세스 서명 (SAS) 인증을 사용하거나 Azure Active Directory 액세스 제어(액세스 제어 서비스 또는 ACS 라고도 함)를 통해 Azure 서비스 버스에 인증할 수 있습니다. 공유 액세스 서명 인증을 사용하면 응용 프로그램을 네임 스페이스 또는 특정 권한이 연관된 엔터티에서 구성된 선택키를 사용하여 서비스 버스에 인증할 수 있습니다. 그런 다음 이 키를 사용하여 클라이언트가 서비스 버스를 인증하는 데 사용할 수 있는 공유 액세스 서명 토큰을 생성할 수 있습니다.

서비스 버스에 대한 단순하고 유연하며 사용이 쉬운 인증 체계를 제공 하므로 ACS에 SAS를 사용하는 것이 좋습니다. 응용 프로그램은 권한 있는 "사용자"의 개념을 관리할 필요가 없는 시나리오에서 SAS를 사용할 수 있습니다.

## 공유 액세스 서명 인증

[SAS 인증](service-bus-sas-overview.md)을 사용하면 특정 권한으로 서비스 버스 리소스에 사용자 액세스 권한을 부여할 수 있습니다. 서비스 버스에서 SAS 인증은 서비스 버스 리소스에 연결된 권한이 있는 암호화 키의 구성을 포함합니다. 클라이언트는 액세스된 리소스 URI 및 구성된 키로 서명된 만료로 구성된 SAS 토큰을 제공하여 해당 리소스에 액세스를 얻을 수 있습니다.

서비스 버스 네임 스페이스에서 SAS에 대한 키를 구성할 수 있습니다. 키는 해당 네임 스페이스의 모든 메시징 엔터티에 적용됩니다. 또한 서비스 버스 큐 및 항목에 키를 구성할 수 있습니다. 또한 SAS는 서비스 버스 릴레이에서 지원됩니다.

SAS를 사용하려면 다음을 구성하는 네임 스페이스, 큐 또는 항목의 [SharedAccessAuthorizationRule](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.sharedaccessauthorizationrule.aspx) 개체를 구성할 수 있습니다.

- 응용 프로그램을 식별하는 *KeyName*입니다.

- *PrimaryKey*는 SAS 토큰을 서명/확인하는 데 사용되는 암호화 키입니다.

- *SecondaryKey*는 SAS 토큰을 서명/확인하는 데 사용되는 암호화 키입니다.

- 권한이 부여된 듣기, 보내기 또는 관리 권한의 컬렉션을 나타내는 *권한*입니다.

네임 스페이스 수준에서 구성된 권한 부여 규칙은 해당 키를 사용하여 서명된 토큰으로 클라이언트에 대한 네임 스페이스의 모든 엔터티에 액세스를 부여할 수 있습니다. 이러한 권한 부여 규칙을 서비스 버스 네임 스페이스, 큐 또는 항목에서 최대 12개까지 구성할 수 있습니다. 기본적으로 모든 권한이 있는 [SharedAccessAuthorizationRule](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.sharedaccessauthorizationrule.aspx)은 처음으로 프로비전될 때 모든 네임 스페이스에 대해 구성됩니다.

엔터티에 액세스하려면 클라이언트는 특정 [SharedAccessAuthorizationRule](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.sharedaccessauthorizationrule.aspx)을 사용하여 생성된 SAS 토큰이 필요합니다. 액세스를 요청하는 리소스 URI로 구성된 리소스 문자열의 HMAC-SHA256 및 권한 부여 규칙에 연결된 암호화 키가 있는 만료를 사용하여 SAS 토큰을 생성합니다.

서비스 버스에 대한 SAS 인증 지원은 Azure.NET SDK 버전 2.0 이후에 포함됩니다. SAS는 [SharedAccessAuthorizationRule](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.sharedaccessauthorizationrule.aspx)에 대한 지원을 포함합니다. 연결 문자열을 매개 변수로 허용하는 모든 API는 SAS 연결 문자열에 대한 지원을 포함합니다.

## ACS 인증

ACS 통한 서비스 버스 인증은 동반 "-sb" ACS 네임 스페이스를 통해 관리됩니다. 서비스 버스 네임 스페이스에 생성될 동반 ACS 네임 스페이스가 필요하면 Azure 포털을 사용하여 서비스 버스 네임 스페이스를 만들 수 없습니다. [New-AzureSBNamespace](https://msdn.microsoft.com/library/azure/dn495165.aspx) PowerShell cmdlet를 사용하여 네임 스페이스를 만들어야 합니다. 예:

```
New-AzureSBNamespace <namespaceName> "<Region>” -CreateACSNamespace $true
```

ACS 네임 스페이스 만들기를 방지하려면 다음 명령을 사용합니다.

```
New-AzureSBNamespace <namespaceName> "<Region>” -CreateACSNamespace $false
```

예를 들어 **contoso.servicebus.windows.net**이라는 서비스 버스 네임 스페이스를 만들면 **contoso sb.accesscontrol.windows.net**이라는 동반 ACS 네임 스페이스는 자동으로 프로비전됩니다. 또한 2014년 8월 이전에 만들어진 모든 네임 스페이스의 경우 함께 제공된 ACS 네임 스페이스를 생성했습니다.

이 동반 ACS 네임 스페이스에는 기본적으로 모든 권한을 가진 기본 서비스 ID "소유자"를 프로비전합니다. 적절한 트러스트 관계를 구성하여 ACS를 통해 서비스 버스 엔터티를 미세부적으로 제어할 수 있습니다. 서비스 버스 엔터티에 대한 액세스를 관리하기 위해 추가 서비스 ID를 구성할 수 있습니다.

엔터티에 액세스하려면 클라이언트는 해당 자격 증명을 제공하여 적절한 클레임으로 ACS에서 SWT 토큰을 요청합니다. SWT 토큰을 서비스 버스에 대한 요청의 일부로 보내어 엔터티에 액세스하기 위해 클라이언트의 권한 부여를 사용하도록 설정해야 합니다.

서비스 버스에 대한 ACS 인증 지원은 Azure.NET SDK 버전 2.0 이후에 포함됩니다. 이 인증은 [SharedSecretTokenProvider](https://msdn.microsoft.com/library/azure/microsoft.servicebus.sharedsecrettokenprovider.aspx)에 대한 지원을 포함합니다. 연결 문자열을 매개 변수로 허용하는 모든 API는 ACS 연결 문자열에 대한 지원을 포함합니다.

## 다음 단계

SAS에 대한 자세한 내용은 [서비스 버스를 사용한 공유 액세스 서명 인증](service-bus-shared-access-signature-authentication.md)을 계속 읽습니다.

서비스 버스에서 SAS에 대한 높은 수준의 개요는 [공유 액세스 서명](service-bus-sas-overview.md)을 참조하세요.

[방법: OAuth WRAP 프로토콜을 통해 ACS에서 토큰 요청](https://msdn.microsoft.com/library/hh674475.aspx)에서 ACS 토큰에 대한 자세한 정보를 찾을 수 있습니다.

<!---HONumber=Nov15_HO3-->