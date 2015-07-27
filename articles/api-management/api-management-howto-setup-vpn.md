<properties
	pageTitle="Azure API 관리에서 VPN 연결을 설정하는 방법"
	description="Azure API 관리에서 VPN 연결을 설정하고 웹 서비스에 액세스하는 방법에 대해 알아봅니다."
	services="api-management"
	documentationCenter=""
	authors="antonba"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="api-management"
	ms.workload="mobile"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/24/2015"
	ms.author="antonba"/>

# Azure API 관리에서 VPN 연결을 설정하는 방법

API 관리의 VPN 지원을 사용하여 API 관리 프록스를 Azure 가상 네트워크에 연결할 수 있습니다. 이렇게 하면 API 관리 고객을 온-프레미스이거나 공용 인터넷에 액세스할 수 없는 해당 백엔드 웹 서비스에 안전하게 연결할 수 있습니다.

## <a name="enable-vpn"> </a>VPN 연결 사용

>VPN 연결은 **프리미엄** 계층에서만 사용할 수 있습니다. 전환하려면 [관리 포털][]에서 API 관리 서비스를 열고 **크기 조정** 탭을 엽니다. **일반** 섹션에서 프리미엄 계층을 선택하고 저장을 클릭합니다.

VPN 연결을 사용하려면 [관리 포털][]에서 API 관리 서비스를 열고 **구성** 탭으로 전환합니다.

VPN 섹션에서 **VPN 연결**을 **켜기**로 전환합니다.

![API 관리 인스턴스의 구성 탭][api-management-setup-vpn-configure]

이제 API 관리 서비스가 프로비전되는 모든 지역 목록이 보입니다.

VPN 및 모든 지역에 대한 서브넷을 선택합니다. VPN 목록은 사용자가 구성하고 있는 지역에서 설정되는 Azure 구독에서 사용할 수 있는 가상 네트워크에 기반하여 채워집니다.

![VPN 선택][api-management-setup-vpn-select]

화면 아래쪽에서 **저장**을 클릭합니다. 업데이트하는 동안 Azure 관리 포털에서 API 관리 서비스의 다른 작업을 수행할 수 없습니다. 서비스 프록시는 여전히 사용할 수 있으며 런타임 호출은 영향을 받지 않습니다.

프록시의 VIP 주소는 VPN이 사용되거나 사용 해제될 때마다 변경됩니다.

## <a name="connect-vpn"> </a>VPN 뒤에서 웹 서비스에 연결

API 관리 서비스가 VPN에 연결되면 가상 네트워크 내에서 웹 서비스 액세스는 공용 서비스 액세스와 다르지 않습니다. 새 API를 만들거나 기존 API를 편집할 때 **웹 서비스 URL** 필드에 웹 서비스의 로컬 주소 또는 호스트 이름(DNS 서버가 Azure 가상 네트워크에 대해 구성된 경우)을 입력하면 됩니다.

![VPN에서 API 추가][api-management-setup-vpn-add-api]


## <a name="related-content"> </a>관련 콘텐츠


 * [자습서: 사이트 간 연결용 프레미스 간 가상 네트워크 만들기][]
 * [API 검사기를 사용하여 Azure API 관리에서 호출을 추적하는 방법][]

[api-management-setup-vpn-configure]: ./media/api-management-howto-setup-vpn/api-management-setup-vpn-configure.png
[api-management-setup-vpn-select]: ./media/api-management-howto-setup-vpn/api-management-setup-vpn-select.png
[api-management-setup-vpn-add-api]: ./media/api-management-howto-setup-vpn/api-management-setup-vpn-add-api.png

[Enable VPN connections]: #enable-vpn
[Connect to a web service behind VPN]: #connect-vpn
[Related content]: #related-content

[관리 포털]: https://manage.windowsazure.com/

[자습서: 사이트 간 연결용 프레미스 간 가상 네트워크 만들기]: ../virtual-networks-create-site-to-site-cross-premises-connectivity
[API 검사기를 사용하여 Azure API 관리에서 호출을 추적하는 방법]: api-management-howto-api-inspector.md
 

<!---HONumber=July15_HO3-->