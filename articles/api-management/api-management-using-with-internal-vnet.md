---
title: 내부 가상 네트워크에서 Azure API Management 사용
titleSuffix: Azure API Management
description: 내부 가상 네트워크에서 Azure API Management를 설정하고 구성하는 방법에 대해 알아봅니다.
services: api-management
documentationcenter: ''
author: vladvino
editor: ''
ms.service: api-management
ms.topic: how-to
ms.date: 04/12/2021
ms.author: apimpm
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: e901f73f3a1751e0f2af7e0788a7b4846452225e
ms.sourcegitcommit: 32ee8da1440a2d81c49ff25c5922f786e85109b4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/12/2021
ms.locfileid: "109783944"
---
# <a name="using-azure-api-management-service-with-an-internal-virtual-network"></a>내부 가상 네트워크에서 Azure API Management를 사용하는 방법
Azure Virtual Networks에서 Azure API Management에서는 인터넷에서 액세스할 수 없는 API를 관리할 수 있습니다. 다양한 VPN 기술은 연결을 만드는 데 사용할 수 있습니다. API Management는 가상 네트워크 내의 두 가지 주요 모드로 배포됩니다.
* 외부
* 내부

API Management를 내부 가상 네트워크 모드로 배포하는 경우 모든 서비스 엔드포인트(프록시 게이트웨이, 개발자 포털, 직접 관리, Git)는 액세스를 제어하는 가상 네트워크 내부에서만 볼 수 있습니다. 서비스 엔드포인트는 공용 DNS 서버에 등록되지 않습니다.

> [!NOTE]
> 서비스 엔드포인트에 대한 DNS 항목이 없으므로 이러한 엔드포인트는 가상 네트워크에 대해 [DNS가 구성](#apim-dns-configuration)될 때까지 액세스할 수 없습니다.

내부 모드에서 API Management를 사용하면 다음 시나리오를 달성할 수 있습니다.

* 사이트 간 연결 또는 Azure ExpressRoute VPN 연결을 사용하여 타사의 외부에서 프라이빗 데이터 센터에 호스팅된 API에 안전하게 액세스할 수 있게 합니다.
* 공통 게이트웨이를 통해 클라우드 기반 API와 온-프레미스 API를 공개함으로써 하이브리드 클라우드 시나리오를 사용하도록 설정합니다.
* 단일 게이트웨이 엔드포인트를 사용하여 여러 지리적 위치에서 호스팅되는 API를 관리합니다.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [premium-dev.md](../../includes/api-management-availability-premium-dev.md)]

## <a name="prerequisites"></a>필수 구성 요소

이 문서에 설명한 단계를 수행하려면 다음 항목이 있어야 합니다.

+ **활성 Azure 구독**

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ **API Management 인스턴스** 자세한 내용은 [Azure API Management 인스턴스 만들기](get-started-create-service-instance.md)를 참조하세요.

[!INCLUDE [api-management-public-ip-for-vnet](../../includes/api-management-public-ip-for-vnet.md)]

API Management 서비스가 가상 네트워크에 배포된 경우 [포트 목록](./api-management-using-with-vnet.md#required-ports)이 사용되며 포트 목록을 열어야 합니다.

## <a name="creating-an-api-management-in-an-internal-virtual-network"></a><a name="enable-vpn"> </a>내부 가상 네트워크에서 API Management 만들기
클라이언트 API 버전 2020-12-01을 사용하여 서비스를 만든 경우 내부 가상 네트워크의 API Management 서비스는 내부 부하 분산 장치 기본 SKU 뒤에 호스트됩니다. API 버전 2021-01-01-preview가 있고 고객의 구독에서 공용 IP 주소가 있는 클라이언트를 사용하여 만든 서비스의 경우 내부 부하 분산 장치 표준 SKU 뒤에 호스트됩니다. 자세한 내용은 [Azure Load Balancer SKU](../load-balancer/skus.md)를 참조하세요.

### <a name="enable-a-virtual-network-connection-using-the-azure-portal"></a>Azure Portal을 사용하여 가상 네트워크 연결 사용

1. [Azure Portal](https://portal.azure.com/)에서 Azure API Management 인스턴스를 찾습니다.
1. **가상 네트워크** 를 선택합니다.
1. **내부** 액세스 형식을 구성합니다. 자세한 단계는 [Azure Portal을 사용하여 VNET 연결 사용](api-management-using-with-vnet.md#enable-vnet-connectivity-using-the-azure-portal)을 참조하세요.

    ![내부 가상 네트워크에서 Azure API Management를 설정하는 메뉴][api-management-using-internal-vnet-menu]

4. **저장** 을 선택합니다.

배포에 성공하면 개요 블레이드에 API Management 서비스의 **개인** 가상 IP 주소와 **공용** 가상 IP 주소가 표시됩니다. **개인** 가상 IP 주소는 `gateway`, `portal`, `management`, `scm` 엔드포인트에 액세스할 수 있는 API Management 위임 서브넷 내에서 부하가 분산된 IP 주소입니다. **공용** 가상 IP 주소는 포트 3443을 통해 `management` 엔드포인트에 대한 컨트롤 플레인 트래픽용으로 **만** 사용되며 [ApiManagement][ServiceTags] 서비스 태그로 잠글 수 있습니다.

![내부 가상 네트워크를 구성한 API Management 대시보드][api-management-internal-vnet-dashboard]

> [!NOTE]
> Azure Portal에서 사용할 수 있는 테스트 콘솔의 경우 해당 게이트웨이 URL이 공용 DNS에 등록되어 있지 않으므로 **내부** VNET 배포 서비스에서 작동하지 않습니다. 대신, **개발자 포털** 에 제공된 테스트 콘솔을 사용해야 합니다.

### <a name="deploy-api-management-into-virtual-network"></a><a name="deploy-apim-internal-vnet"> </a>가상 네트워크에 API Management 배포

다음 방법을 사용하여 가상 네트워크 연결을 사용하도록 설정할 수도 있습니다.


### <a name="api-version-2020-12-01"></a>API 버전 2020-12-01

* Azure Resource Manager [템플릿](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.apimanagement/api-management-create-with-internal-vnet)

     [![Azure에 배포](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.apimanagement%2Fapi-management-create-with-internal-vnet%2Fazuredeploy.json)

* Azure PowerShell cmdlet - 가상 네트워크에서 API Management 인스턴스를 [만들거나](/powershell/module/az.apimanagement/new-azapimanagement) [업데이트](/powershell/module/az.apimanagement/update-azapimanagementregion)합니다.

## <a name="dns-configuration"></a><a name="apim-dns-configuration"></a>DNS 구성
API Management가 외부 가상 네트워크 모드인 경우 Azure에서 DNS를 관리합니다. 내부 가상 네트워크 모드의 경우 자체의 DNS를 관리해야 합니다. Azure DNS 프라이빗 영역을 구성하고 서비스가 배포된 가상 네트워크 API Management에 연결하는 것이 권장 옵션입니다. [Azure DNS에서 프라이빗 영역을 설정](../dns/private-dns-getstarted-portal.md)하는 방법을 알아봅니다.

> [!NOTE]
> API Management 서비스는 IP 주소에서 오는 요청을 수신 대기하지 않습니다. 해당 서비스 엔드포인트에 구성된 호스트 이름에 대한 요청에만 응답합니다. 이러한 엔드포인트에는 게이트웨이, Azure Portal, 개발자 포털, 직접 관리 엔드포인트 및 Git가 포함됩니다.

### <a name="access-on-default-host-names"></a>기본 호스트 이름에 대한 액세스
예를 들어 "contosointernalvnet"이라는 API Management 서비스를 만들면 기본적으로 다음 서비스 엔드포인트가 구성됩니다.

   * 게이트웨이 또는 프록시: contosointernalvnet.azure-api.net

   * 개발자 포털: contosointernalvnet.portal.azure-api.net

   * 새 개발자 포털: contosointernalvnet.developer.azure-api.net

   * 직접 관리 엔드포인트: contosointernalvnet.management.azure-api.net

   * Git: contosointernalvnet.scm.azure-api.net

이러한 API Management 서비스 엔드포인트에 액세스하려면 API Management를 배포한 가상 네트워크에 연결된 서브넷에 가상 머신을 만들 수 있습니다. 서비스의 내부 가상 IP 주소가 10.1.0.5라고 가정하면 다음과 같이 호스트 파일을 매핑할 수 있습니다(%SystemDrive%\drivers\etc\hosts).

   * 10.1.0.5     contosointernalvnet.azure-api.net

   * 10.1.0.5     contosointernalvnet.portal.azure-api.net

   * 10.1.0.5     contosointernalvnet.developer.azure-api.net

   * 10.1.0.5     contosointernalvnet.management.azure-api.net

   * 10.1.0.5     contosointernalvnet.scm.azure-api.net

그런 다음 만든 가상 머신에서 모든 서비스 엔드포인트에 액세스할 수 있습니다.
가상 네트워크에서 사용자 지정 DNS 서버를 사용하는 경우 DNS 레코드를 만들고 가상 네트워크의 어느 곳에서나 이러한 엔드포인트에 액세스할 수 있습니다.

### <a name="access-on-custom-domain-names"></a>사용자 지정 도메인 이름에 대한 액세스

1. 기본 호스트 이름으로 API Management 서비스에 액세스하지 않으려면 다음 이미지에 표시된 대로 모든 서비스 엔드포인트에 대해 사용자 지정 도메인 이름을 설정하면 됩니다.

   ![API Management를 위한 사용자 지정 도메인 설정][api-management-custom-domain-name]

2. 그런 다음 DNS 서버에 레코드를 만들어 가상 네트워크 내에서만 액세스할 수 있는 이러한 엔드포인트에 액세스할 수 있습니다.

## <a name="routing"></a><a name="routing"> </a> 라우팅

* 서브넷 범위의 부하 분산된 ‘개인’ 가상 IP 주소는 예약되며 가상 네트워크 내에서 API Management 서비스 엔드포인트에 액세스하는 데 사용됩니다. 이 ‘개인’ IP 주소는 Azure Portal 서비스에 대한 개요 블레이드에서 찾을 수 있습니다. 이 주소는 가상 네트워크에서 사용하는 DNS 서버에 등록되어야 합니다.
* 또한 부하 분산된 ‘공용’ IP 주소(VIP)는 포트 3443을 통해 관리 서비스 엔드포인트에 대한 액세스를 제공하도록 예약됩니다. 부하 분산된 ‘공용’ IP 주소는 Azure Portal의 개요 블레이드에서 확인할 수 있습니다. *공용* IP 주소는 포트 3443을 통해 `management` 엔드포인트에 대한 컨트롤 플레인 트래픽용으로만 사용되며 [ApiManagement][ServiceTags] 서비스 태그로 잠글 수 있습니다.
* 서브넷 IP 범위(DIP)의 IP 주소는 서비스의 각 VM에 할당되며, 가상 네트워크 내의 리소스에 액세스하는 데 사용됩니다. 공용 IP 주소(VIP)는 가상 네트워크 외부의 리소스에 액세스하는 데 사용됩니다. IP 제한 목록이 가상 네트워크 내의 리소스를 보호하는 데 사용되는 경우 서비스에서 액세스 권한을 부여하거나 제한하도록 API Management 서비스가 배포된 서브넷의 전체 범위가 지정되어야 합니다.
* 부하 분산된 공용 및 개인 IP 주소는 Azure Portal의 개요 블레이드에서 확인할 수 있습니다.
* 서비스가 가상 네트워크에서 제거되거나 다시 추가될 경우 공용 및 개인 액세스를 위해 할당된 IP 주소는 변경될 수 있습니다. 이 문제가 발생하면 가상 네트워크 내에서 DNS 등록, 라우팅 규칙 및 IP 제한 목록을 업데이트해야 할 수 있습니다.

## <a name="related-content"></a><a name="related-content"> </a>관련 콘텐츠
자세한 내용은 다음 문서를 참조하세요.
* [가상 네트워크에서 Azure API Management를 설정하는 동안 발생하는 공통 네트워크 구성 문제][Common network configuration problems]
* [가상 네트워크 FAQ](../virtual-network/virtual-networks-faq.md)
* [DNS에서 레코드 만들기](/previous-versions/windows/it-pro/windows-2000-server/bb727018(v=technet.10))

[api-management-using-internal-vnet-menu]: ./media/api-management-using-with-internal-vnet/updated-api-management-using-with-internal-vnet.png
[api-management-internal-vnet-dashboard]: ./media/api-management-using-with-internal-vnet/updated-api-management-internal-vnet-dashboard.png
[api-management-custom-domain-name]: ./media/api-management-using-with-internal-vnet/updated-api-management-custom-domain-name.png

[Create API Management service]: get-started-create-service-instance.md
[Common network configuration problems]: api-management-using-with-vnet.md#network-configuration-issues

[ServiceTags]: ../virtual-network/network-security-groups-overview.md#service-tags
