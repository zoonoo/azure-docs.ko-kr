---
title: "내부 가상 네트워크에서 Azure API Management를 사용하는 방법 | Microsoft Docs"
description: "내부 가상 네트워크에서 Azure API Management를 설정하고 구성하는 방법에 대해 알아봅니다."
services: api-management
documentationcenter: 
author: vladvino
manager: kjoshi
editor: 
ms.assetid: dac28ccf-2550-45a5-89cf-192d87369bc3
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2017
ms.author: apimpm
ms.translationtype: HT
ms.sourcegitcommit: 07e5e15f4f4c4281a93c8c3267c0225b1d79af45
ms.openlocfilehash: a4c2bda1226ca05c775d011fba7bc59d4dab8998
ms.contentlocale: ko-kr
ms.lasthandoff: 08/31/2017

---
# <a name="using-azure-api-management-service-with-internal-virtual-network"></a>내부 가상 네트워크에서 Azure API Management를 사용하는 방법
Azure VNETs(Virtual Networks)를 사용하면 API Management를 통해 인터넷에서 액세스할 수 없는 API를 관리할 수 있습니다. 여러 가지 VPN 기술을 연결하는 데 사용할 수 있으며, API Management를 다음 두 가지 주요 모드로 VNET 내에 배포할 수 있습니다.
* 외부
* 내부

## <a name="overview"> </a>개요
API Management를 내부 가상 네트워크 모드로 배포하는 경우 모든 서비스 끝점(게이트웨이, 개발자 포털, 게시자 포털, 직접 관리 및 Git)은 액세스를 제어하는 가상 네트워크 내부에서만 볼 수 있습니다. 서비스 끝점은 공용 DNS 서버에 등록되지 않습니다.

내부 모드에서 API Management를 사용하면 다음 시나리오를 달성할 수 있습니다.
* 사이트 간 연결 또는 ExpressRoute VPN 연결을 사용하여 외부의 타사에서 개인 데이터 센터에 호스팅한 API에 안전하게 액세스할 수 있게 합니다.
* 공통 게이트웨이를 통해 클라우드 기반 API와 온-프레미스 API를 공개함으로써 하이브리드 클라우드 시나리오를 사용하도록 설정합니다.
* 단일 게이트웨이 끝점을 사용하여 여러 지리적 위치에서 호스팅되는 API를 관리합니다. 

## <a name="enable-vpn"> </a>내부 가상 네트워크에 API Management 만들기
내부 가상 네트워크의 API Management 서비스는 ILB(내부 부하 분산 장치) 뒤에 호스팅됩니다. ILB의 IP 주소는 [RFC1918](http://www.faqs.org/rfcs/rfc1918.html) 범위에 있습니다.  

### <a name="enable-vnet-connection-using-azure-portal"></a>Azure Portal을 사용하여 VNET 연결 사용
먼저 [API Management 서비스 만들기][Create API Management service] 단계에 따라 API Management 서비스를 만듭니다. 그런 다음 API Management를 설정하여 가상 네트워크 내에 배포합니다.

![내부 가상 네트워크에서 APIM 설정을 위한 메뉴][api-management-using-internal-vnet-menu]

배포가 성공하면 대시보드에 서비스의 내부 가상 IP 주소가 표시됩니다.

![내부 VNET이 구성된 API Management 대시보드][api-management-internal-vnet-dashboard]

### <a name="enable-vnet-connection-using-powershell-cmdlets"></a>PowerShell cmdlet을 사용하여 VNET 연결 사용
PowerShell cmdlet을 사용하여 VNET 연결을 사용하도록 설정할 수도 있습니다.

* **VNET 내부에 API Management 서비스 만들기**: [New-AzureRmApiManagement](/powershell/module/azurerm.apimanagement/new-azurermapimanagement) cmdlet을 사용하여 VNET 내에 Azure API Management 서비스를 만들고 내부 VNET 유형을 사용하도록 구성합니다.

* **VNET 내부에 기존 API Management 서비스 배포**: [Update-AzureRmApiManagementDeployment](/powershell/module/azurerm.apimanagement/update-azurermapimanagementdeployment) cmdlet을 사용하여 가상 네트워크 내부로 기존 Azure API Management 서비스를 이동하고 내부 VNET 유형을 사용하도록 구성합니다.

## <a name="apim-dns-configuration"></a>DNS 구성
외부 가상 네트워크 모드에서 API Management를 사용하는 경우 Azure에서 DNS를 관리합니다. 내부 가상 네트워크 모드의 경우 자체의 DNS를 관리해야 합니다.

> [!NOTE]
> API Management 서비스는 IP 주소에서 오는 요청을 수신 대기하지 않습니다. 서비스 끝점(게이트웨이, 개발자 포털, 게시자 포털, 직접 관리 끝점 및 Git 포함)에 구성된 호스트 이름에 대한 요청에만 응답합니다.

### <a name="access-on-default-host-names"></a>기본 호스트 이름에 대한 액세스
예를 들어 "contoso"라는 공용 Azure 클라우드에서 API Management 서비스를 만들면 기본적으로 다음 서비스 끝점이 구성됩니다.

>   게이트웨이/프록시 - contoso.azure-api.net

> 게시자 포털 및 개발자 포털 - contoso.portal.azure-api.net

> 직접 관리 끝점 - contoso.management.azure-api.net

>   GIT - contoso.scm.azure-api.net

이러한 API Management 서비스 끝점에 액세스하려면 API Management를 배포한 가상 네트워크에 연결된 서브넷에 Virtual Machine을 만들 수 있습니다. 서비스의 내부 가상 IP 주소가 10.0.0.5라고 가정하면 다음과 같이 호스트 파일 매핑(%SystemDrive%\drivers\etc\hosts)을 수행할 수 있습니다.

> 10.0.0.5    contoso.azure-api.net

> 10.0.0.5    contoso.portal.azure-api.net

> 10.0.0.5    contoso.management.azure-api.net

> 10.0.0.5    contoso.scm.azure-api.net

그런 다음 만든 Virtual Machine에서 모든 서비스 끝점에 액세스할 수 있습니다. 가상 네트워크에서 사용자 지정 DNS 서버를 사용하는 경우 A DNS 레코드를 만들고 가상 네트워크의 어느 곳에서나 이러한 끝점에 액세스할 수 있습니다. 

### <a name="access-on-custom-domain-names"></a>사용자 지정 도메인 이름에 대한 액세스
기본 호스트 이름으로 API Management 서비스에 액세스하지 않으려면 아래와 같이 모든 서비스 끝점에 대해 사용자 지정 도메인 이름을 설정하면 됩니다.

![API Management를 위한 사용자 지정 도메인 설정][api-management-custom-domain-name]

그런 다음 DNS 서버에 A 레코드를 만들어 가상 네트워크 내에서만 액세스할 수 있는 이러한 끝점에 액세스할 수 있습니다.

## <a name="related-content"> </a>관련 콘텐츠
* [VNET에 APIM을 설정하는 동안의 일반 네트워크 구성 문제][Common Network Configuration Issues]
* [가상 네트워크 FAQ](../virtual-network/virtual-networks-faq.md)
* [DNS에 A 레코드 만들기](https://msdn.microsoft.com/en-us/library/bb727018.aspx)(영문)

[api-management-using-internal-vnet-menu]: ./media/api-management-using-with-internal-vnet/api-management-internal-vnet-menu.png
[api-management-internal-vnet-dashboard]: ./media/api-management-using-with-internal-vnet/api-management-internal-vnet-dashboard.png
[api-management-custom-domain-name]: ./media/api-management-using-with-internal-vnet/api-management-custom-domain-name.png

[Create API Management service]: api-management-get-started.md#create-service-instance
[Common Network Configuration Issues]: api-management-using-with-vnet.md#network-configuration-issues

