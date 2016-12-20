---
title: "가상 네트워크에서 Azure API 관리를 사용하는 방법"
description: "Azure API 관리에서 가상 네트워크 연결을 설정하고 웹 서비스에 액세스하는 방법에 대해 알아봅니다."
services: api-management
documentationcenter: 
author: antonba
manager: erikre
editor: 
ms.assetid: 64b58f7b-ca22-47dc-89c0-f6bb0af27a48
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/20/2016
ms.author: antonba
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 7dfbf0e460dc8de13a4a71c1f925c93f6238df5d


---
# <a name="how-to-use-azure-api-management-with-virtual-networks"></a>가상 네트워크에서 Azure API 관리를 사용하는 방법
Azure VNET(가상 네트워크)을 사용하면 인터넷에서 사용할 수 없고 라우팅할 있는 네트워크(액세스를 제어하는)에 다수의 Azure 리소스를 배치할 수 있습니다. 이러한 네트워크는 다양한 VPN 기술을 사용하여 온-프레미스 네트워크에 연결될 수 있습니다. Azure 가상 네트워크에 대해 자세히 알아보려면 [Azure 가상 네트워크 개요](../virtual-network/virtual-networks-overview.md)부터 참조하세요.

Azure API 관리를 VNET(가상 네트워크)에 연결하여 네트워크 내에서 백 엔드 서비스에 액세스하고 개발자 포털 및 API 게이트웨이에 액세스할 수 있습니다.

## <a name="enable-vpn"> </a>VNET 연결 사용
> VNET 연결은 **프리미엄** 및 **개발자** 계층에서만 사용할 수 있습니다. 계층 간에 전환하려면 Azure Portal 포털에서 API 관리 서비스를 열고 **크기 조정 및 가격 책정** 탭을 엽니다. **가격 책정 계층** 섹션에서 프리미엄 계층을 선택하고 저장을 클릭합니다.
> 
> 

VNET 연결을 사용하려면 Azure Portal 포털에서 API 관리 서비스를 열고 **가상 네트워크** 페이지를 엽니다.

![API 관리의 가상 네트워크 메뉴][api-management-using-vnet-menu]

원하는 액세스 유형을 선택합니다.

* **외부**: 외부 부하 분산 장치를 통해 공용 인터넷에서 API 관리 게이트웨이 및 개발자 포털에 액세스할 수 있습니다. 게이트웨이에서 가상 네트워크 내의 리소스에 액세스할 수 있습니다.

![공용 피어링][api-management-vnet-public]

* **내부**: 내부 부하 분산 장치를 통해 가상 네트워크 내에서만 API 관리 게이트웨이 및 개발자 포털에 액세스할 수 있습니다. 게이트웨이에서 가상 네트워크 내의 리소스에 액세스할 수 있습니다.

![개인 피어링][api-management-vnet-private]

이제 API 관리 서비스가 프로비전되는 모든 지역 목록이 보입니다. VNET 및 모든 지역에 대한 서브넷을 선택합니다. 이 목록은 사용자가 구성하고 있는 지역에 설정된 Azure 구독에서 사용할 수 있는 클래식 및 ARM 가상 네트워크로 채워집니다.

![VPN 선택][api-management-setup-vpn-select]

화면 위쪽에서 **저장**을 클릭합니다. 

> 업데이트하는 동안 API 관리 서비스에서 관리 작업을 수행할 수 없습니다. API 관리 게이트웨이 및 개발자 포털은 계속 사용할 수 있습니다.
> API 관리 인스턴스의 VIP 주소는 VNET이 활성화되거나 비활성화될 때마다 변경될 수 있습니다.
> 
> 

## <a name="enable-vnet-powershell"> </a>PowerShell commandlet을 사용하여 VNET 연결 사용
PowerShell commandlet [Set-AzureRmApiManagementVirtualNetworks](https://msdn.microsoft.com/library/mt619277.aspx)를 사용하여 VNET 연결을 사용하도록 설정할 수도 있습니다.

## <a name="connect-vnet"> </a>가상 네트워크 내에서 호스트되는 웹 서비스에 연결
API 관리 서비스가 VNET에 연결된 후에는 공용 서비스에 액세스하는 것과 동일하게 VNET 내에서 백 엔드 웹 서비스에 액세스할 수 있습니다. 새 API를 만들거나 기존 API를 편집할 때 **웹 서비스 URL** 필드에 웹 서비스의 로컬 IP 주소 또는 호스트 이름(DNS 서버가 VNET에 대해 구성된 경우)을 입력하면 됩니다.

![VPN에서 API 추가][api-management-setup-vpn-add-api]

## <a name="custom-dns"> </a>사용자 지정 DNS 서버 설정
API Management는 다양한 Azure 서비스에 따라 달라집니다. API Management가 사용자 지정 DNS 서버를 사용하는 VNET에서 호스트되는 경우 해당 Azure 서비스의 호스트 이름을 확인할 수 있어야 합니다. 사용자 지정 DNS 설정에 대한 [이](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server) 지침을 따르세요. 자세한 내용은 아래 다이어그램 및 포트 표를 참조하세요.

## <a name="ports-required"> </a>API 관리에 필요한 포트
> 이러한 포트를 사용할 수 없는 경우 API 관리가 정상적으로 작동하지 않고 액세스하지 못하게 될 수 있습니다. 이러한 포트가 하나 이상 차단되는 것은 VNET에서 API 관리를 사용하는 경우 가장 일반적인 잘못된 구성 문제입니다.
> 
> 

API 관리 서비스 인스턴스가 VNET에 호스트된 경우 다음 표의 포트가 사용됩니다.

| 포트 | 방향 | 전송 프로토콜 | 목적 | 원본/대상 | 액세스 유형 |
| --- | --- | --- | --- | --- | --- |
| 80, 443 |인바운드 |TCP |API 관리에 대한 클라이언트 통신 |인터넷 / VIRTUAL_NETWORK |외부 |
| 3443 |인바운드 |TCP |관리 끝점 |인터넷 / VIRTUAL_NETWORK |외부 및 내부 |
| 80, 443 |아웃바운드 |TCP |Azure 저장소 및 Azure Service Bus에 대한 종속성 |VIRTUAL_NETWORK / 인터넷 |외부 및 내부 |
| 1433 |아웃바운드 |TCP |Azure SQL에 대한 종속성 |VIRTUAL_NETWORK / 인터넷 |외부 및 내부 |
| 9350, 9351, 9352, 9353, 9354 |아웃바운드 |TCP |Service Bus에 대한 종속성 |VIRTUAL_NETWORK / 인터넷 |외부 및 내부 |
| 5671 |아웃바운드 |AMQP |이벤트 허브 정책에 대한 로그의 종속성 |VIRTUAL_NETWORK / 인터넷 |외부 및 내부 |
| 6381, 6382, 6383 |인바운드/아웃바운드 |UDP |Redis 캐시에 대한 종속성 |VIRTUAL_NETWORK / VIRTUAL_NETWORK |외부 및 내부 |
| 445 |아웃바운드 |TCP |GIT의 Azure 파일 공유에 대한 종속성 |VIRTUAL_NETWORK / 인터넷 |외부 및 내부 |

## <a name="limitations"> </a>제한 사항
* API 관리 인스턴스가 포함된 서브넷은 다른 Azure 리소스 종류를 포함할 수 없습니다.
* 서브넷과 API 관리 서비스는 동일한 구독에 있어야 합니다.
* API 관리 인스턴스가 포함된 서브넷은 구독 간에 이동할 수 없습니다.
* 내부 가상 네트워크를 사용할 때는 [RFC 1918](https://tools.ietf.org/html/rfc1918)에 명시된 범위의 내부 IP 주소만 사용할 수 있으며 공용 IP 주소를 제공할 수 없습니다.
* 내부 가상 네트워크가 구성된 다중 지역 API 관리 배포의 경우 사용자가 DNS를 소유하므로 자신의 부하 분산을 직접 관리해야 합니다.

## <a name="related-content"> </a>관련 콘텐츠
* [Azure Portal을 사용하여 사이트 간 VPN 연결로 가상 네트워크 만들기][Azure Portal을 사용하여 사이트 간 VPN 연결로 가상 네트워크 만들기]
* [API 검사기를 사용하여 Azure API 관리에서 호출을 추적하는 방법][API 검사기를 사용하여 Azure API 관리에서 호출을 추적하는 방법]

[api-management-using-vnet-menu]: ./media/api-management-using-with-vnet/api-management-menu-vnet.png
[api-management-setup-vpn-select]: ./media/api-management-using-with-vnet/api-management-using-vnet-type.png
[api-management-setup-vpn-select]: ./media/api-management-using-with-vnet/api-management-using-vnet-select.png
[api-management-setup-vpn-add-api]: ./media/api-management-using-with-vnet/api-management-using-vnet-add-api.png
[api-management-vnet-private]: ./media/api-management-using-with-vnet/api-management-vnet-private.png
[api-management-vnet-public]: ./media/api-management-using-with-vnet/api-management-vnet-public.png

[VPN 연결 사용]: #enable-vpn
[VPN 뒤에서 웹 서비스에 연결]: #connect-vpn
[관련 콘텐츠]: #related-content


[Azure Portal을 사용하여 사이트 간 VPN 연결로 가상 네트워크 만들기]: ../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md
[API 검사기를 사용하여 Azure API 관리에서 호출을 추적하는 방법]: api-management-howto-api-inspector.md



<!--HONumber=Nov16_HO3-->


