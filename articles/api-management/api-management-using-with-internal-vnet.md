---
title: 내부 가상 네트워크에서 Azure API Management를 사용하는 방법 | Microsoft Docs
description: 내부 가상 네트워크에서 Azure API Management를 설정하고 구성하는 방법에 대해 알아봅니다.
services: api-management
documentationcenter: ''
author: vladvino
manager: kjoshi
editor: ''
ms.assetid: dac28ccf-2550-45a5-89cf-192d87369bc3
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/11/2019
ms.author: apimpm
ms.openlocfilehash: a5d8a724a0b4dd6899a71187176b9d444e5fe19c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67051685"
---
# <a name="using-azure-api-management-service-with-an-internal-virtual-network"></a>내부 가상 네트워크에서 Azure API Management를 사용하는 방법
Azure Virtual Networks에서 Azure API Management에서는 인터넷에서 액세스할 수 없는 API를 관리할 수 있습니다. 다양한 VPN 기술은 연결을 만드는 데 사용할 수 있습니다. API Management는 가상 네트워크 내의 두 가지 주요 모드로 배포됩니다.
* 외부
* 내부

API Management를 내부 가상 네트워크 모드로 배포, 모든 서비스 끝점 (프록시 게이트웨이, 개발자 포털, 직접 관리 및 Git)은 표시에 대 한 액세스를 제어 하는 가상 네트워크 내 에서만 있습니다. 서비스 엔드포인트는 공용 DNS 서버에 등록되지 않습니다.

> [!NOTE]
> 서비스 끝점에 대 한 DNS 항목이 없는 이기 때문에 이러한 끝점에 액세스할 수 없습니다 될 때까지 [DNS가 구성 된](#apim-dns-configuration) 가상 네트워크에 대 한 합니다.

내부 모드에서 API Management를 사용하면 다음 시나리오를 달성할 수 있습니다.

* 사이트 간 연결 또는 Azure ExpressRoute VPN 연결을 사용하여 타사의 외부에서 프라이빗 데이터 센터에 호스팅된 API에 안전하게 액세스할 수 있게 합니다.
* 공통 게이트웨이를 통해 클라우드 기반 API와 온-프레미스 API를 공개함으로써 하이브리드 클라우드 시나리오를 사용하도록 설정합니다.
* 단일 게이트웨이 엔드포인트를 사용하여 여러 지리적 위치에서 호스팅되는 API를 관리합니다.

[!INCLUDE [premium-dev.md](../../includes/api-management-availability-premium-dev.md)]

## <a name="prerequisites"></a>필수 조건

이 문서에 설명한 단계를 수행하려면 다음 항목이 있어야 합니다.

+ **활성 Azure 구독**

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ **API Management 인스턴스** 자세한 내용은 [Azure API Management 인스턴스 만들기](get-started-create-service-instance.md)를 참조하세요.
+ API Management 서비스를 가상 네트워크에 배포 되 면을 [방식별 포트 목록](./api-management-using-with-vnet.md#required-ports) 되 고 열어야 합니다. 

## <a name="enable-vpn"> </a>내부 가상 네트워크에서 API Management 만들기
뒤 내부 가상 네트워크에서 API Management 서비스를 호스팅하는 [내부 부하 분산 장치 (클래식)](https://docs.microsoft.com/azure/load-balancer/load-balancer-get-started-ilb-classic-cloud)합니다. 이 옵션만 사용할 수 있습니다 이며 변경할 수 없습니다.

### <a name="enable-a-virtual-network-connection-using-the-azure-portal"></a>Azure Portal을 사용하여 가상 네트워크 연결 사용

1. [Azure Portal](https://portal.azure.com/)에서 Azure API Management 인스턴스를 찾습니다.
2. **가상 네트워크**를 선택합니다.
3. API Management 인스턴스를 가상 네트워크 내부에 배포되도록 구성합니다.

    ![내부 가상 네트워크에서 Azure API Management를 설정하는 메뉴][api-management-using-internal-vnet-menu]

4. **저장**을 선택합니다.

배포에 성공한 경우 표시 되어야 **사설** 가상 IP 주소 및 **공용** 개요 블레이드에서 API Management 서비스의 가상 IP 주소입니다. **사설** 가상 IP 주소는 부하를 API Management 내에서 분산 된 IP 주소는 서브넷을 위임 `gateway`, `portal`를 `management` 및 `scm` 끝점에 액세스할 수 있습니다. **공개** 가상 IP 주소를 사용 **만** 대 한 트래픽 제어 평면에 대 한 `management` 넘는 끝점 포트 3443 및으로 잠글 수는 [ApiManagement] [ ServiceTags] servicetag 합니다.

![내부 가상 네트워크를 구성한 API Management 대시보드][api-management-internal-vnet-dashboard]

> [!NOTE]
> Azure Portal에서 사용할 수 있는 테스트 콘솔의 경우 해당 게이트웨이 URL이 공용 DNS에 등록되어 있지 않으므로 **내부** VNET 배포 서비스에서 작동하지 않습니다. 대신, **개발자 포털**에 제공된 테스트 콘솔을 사용해야 합니다.

### <a name="enable-a-virtual-network-connection-by-using-powershell-cmdlets"></a>PowerShell cmdlet을 사용하여 가상 네트워크 연결 사용

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

또한 PowerShell cmdlet을 사용하여 가상 네트워크 연결을 사용할 수 있습니다.

* 가상 네트워크 내에서 API Management 서비스를 만듭니다. Cmdlet을 사용 하 여 [새로 만들기-AzApiManagement](/powershell/module/az.apimanagement/new-azapimanagement) 가상 네트워크 내에서 Azure API Management 서비스를 만들고 내부 가상 네트워크 형식을 사용 하도록 구성 합니다.

* 가상 네트워크 내에서 API Management 서비스의 기존 배포를 업데이트 합니다. Cmdlet을 사용 하 여 [업데이트 AzApiManagementRegion](/powershell/module/az.apimanagement/update-azapimanagementregion) 가상 네트워크 내부에 기존 API Management 서비스를 이동 및 내부 가상 네트워크 형식을 사용 하도록 구성 합니다.

## <a name="apim-dns-configuration"></a>DNS 구성
API Management가 외부 가상 네트워크 모드인 경우 Azure에서 DNS를 관리합니다. 내부 가상 네트워크 모드의 경우 자체의 라우팅을 관리해야 합니다.

> [!NOTE]
> API Management 서비스는 IP 주소에서 오는 요청을 수신 대기하지 않습니다. 해당 서비스 엔드포인트에 구성된 호스트 이름에 대한 요청에만 응답합니다. 이러한 엔드포인트에는 게이트웨이, Azure Portal, 개발자 포털, 직접 관리 엔드포인트 및 Git가 포함됩니다.

### <a name="access-on-default-host-names"></a>기본 호스트 이름에 대한 액세스
예를 들어, "contosointernalvnet" 라는 API Management 서비스를 만들 때 기본적으로 다음 서비스 끝점이 구성 됩니다.

   * 게이트웨이 또는 프록시: contosointernalvnet.azure api.net

   * Azure portal 및 개발자 포털: contosointernalvnet.portal.azure api.net

   * 직접 관리 끝점: contosointernalvnet.management.azure api.net

   * Git: contosointernalvnet.scm.azure-api.net

이러한 API Management 서비스 엔드포인트에 액세스하려면 API Management를 배포한 가상 네트워크에 연결된 서브넷에 가상 머신을 만들 수 있습니다. 서비스에 대 한 내부 가상 IP 주소가 10.1.0.5를 가정 하 고 %SystemDrive%\drivers\etc\hosts, 호스트 파일을 다음과 같이 매핑할 수 있습니다.

   * 10.1.0.5     contosointernalvnet.azure-api.net

   * 10.1.0.5     contosointernalvnet.portal.azure-api.net

   * 10.1.0.5     contosointernalvnet.management.azure-api.net

   * 10.1.0.5     contosointernalvnet.scm.azure-api.net

그런 다음 만든 가상 머신에서 모든 서비스 엔드포인트에 액세스할 수 있습니다.
가상 네트워크에서 사용자 지정 DNS 서버를 사용하는 경우 DNS 레코드를 만들고 가상 네트워크의 어느 곳에서나 이러한 엔드포인트에 액세스할 수 있습니다.

### <a name="access-on-custom-domain-names"></a>사용자 지정 도메인 이름에 대한 액세스

1. 기본 호스트 이름으로 API Management 서비스에 액세스하지 않으려면 다음 이미지에 표시된 대로 모든 서비스 엔드포인트에 대해 사용자 지정 도메인 이름을 설정하면 됩니다.

   ![API Management를 위한 사용자 지정 도메인 설정][api-management-custom-domain-name]

2. 그런 다음 DNS 서버에 레코드를 만들어 가상 네트워크 내에서만 액세스할 수 있는 이러한 엔드포인트에 액세스할 수 있습니다.

## <a name="routing"> </a> 라우팅

* 부하 분산 *개인* 가상 IP 주소 서브넷 범위에서 예약 및 가상 네트워크 내에서 API Management 서비스 끝점에 액세스 하는 데 사용 됩니다. 이렇게 *개인* Azure portal에서 서비스에 대 한 개요 블레이드에서 IP 주소를 찾을 수 있습니다. 이 주소는 가상 네트워크에서 사용 하는 DNS 서버를 사용 하 여 등록 되어야 합니다.
* 부하 분산 *공용* IP 주소 (VIP)는도 포트 3443에서 관리 서비스 끝점에 대 한 액세스를 제공 하도록 예약 됩니다. 이렇게 *공용* Azure portal에서 서비스에 대 한 개요 블레이드에서 IP 주소를 찾을 수 있습니다. *공용* 대 한 트래픽 제어 평면에만 IP 주소를 사용 합니다 `management` 조치 끝점 포트 3443 및으로 잠글 수 있습니다는 [ApiManagement] [ ServiceTags] servicetag .
* 서브넷 IP 범위 (DIP)에서 IP 주소는 서비스의 각 VM에 할당 됩니다 및 가상 네트워크 내의 리소스에 액세스 하는 데 사용 됩니다. 가상 네트워크 외부의 리소스에 액세스 하는 공용 IP 주소 (VIP) 사용 됩니다. IP 제한 나열 가상 네트워크 내에서 리소스 보안 유지를 사용 하는 경우 여기서 API Management 서비스를 배포 해야 하는 서브넷에 대 한 전체 범위 권한을 부여 하거나 서비스에서 액세스를 제한 하려면 지정 합니다.
* 부하 분산 된 공용 및 개인 IP 주소는 Azure portal의 개요 블레이드에서 찾을 수 있습니다.
* 서비스가에서 제거 되 고 그런 다음 가상 네트워크에 다시 추가 하는 경우 공용 및 개인 액세스에 할당 된 IP 주소를 변경할 수 있습니다. 이 경우 DNS 등록, 라우팅 규칙 및 가상 네트워크 내에서 IP 제한 목록을 업데이트 해야 할 수도 있습니다.

## <a name="related-content"> </a>관련 콘텐츠
자세한 내용은 다음 문서를 참조하세요.
* [가상 네트워크에서 Azure API Management를 설정하는 동안 발생하는 공통 네트워크 구성 문제][Common network configuration problems]
* [가상 네트워크 FAQ](../virtual-network/virtual-networks-faq.md)
* [DNS에서 레코드 만들기](/previous-versions/windows/it-pro/windows-2000-server/bb727018(v=technet.10))

[api-management-using-internal-vnet-menu]: ./media/api-management-using-with-internal-vnet/api-management-using-with-internal-vnet.png
[api-management-internal-vnet-dashboard]: ./media/api-management-using-with-internal-vnet/api-management-internal-vnet-dashboard.png
[api-management-custom-domain-name]: ./media/api-management-using-with-internal-vnet/api-management-custom-domain-name.png

[Create API Management service]: get-started-create-service-instance.md
[Common network configuration problems]: api-management-using-with-vnet.md#network-configuration-issues

[ServiceTags]: ../virtual-network/security-overview.md#service-tags

