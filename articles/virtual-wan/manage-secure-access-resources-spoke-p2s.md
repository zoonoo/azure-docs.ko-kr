---
title: P2S 클라이언트용 스포크 Vnet의 리소스에 대 한 보안 액세스 관리
titleSuffix: Azure Virtual WAN
description: 이 문서에서는 Azure 가상 WAN 및 Azure 방화벽 규칙을 사용 하 여 사용자 VPN (지점 및 사이트 간) 클라이언트에 대 한 가상 네트워크에 대 한 보안 액세스를 관리 하는 방법을 설명 합니다.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 12/11/2020
ms.author: cherylmc
ms.openlocfilehash: b0937bbd72460b1d46ce0394af1933e858424966
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/12/2020
ms.locfileid: "97360325"
---
# <a name="manage-secure-access-to-resources-in-spoke-vnets-for-user-vpn-clients"></a>사용자 VPN 클라이언트용 스포크 Vnet의 리소스에 대 한 보안 액세스 관리

이 문서에서는 가상 WAN 및 Azure 방화벽 규칙 및 필터를 사용 하 여 지점 및 사이트 간 IKEv2를 통해 Azure의 리소스에 대 한 연결에 대 한 보안 액세스를 관리 하 고 VPN 연결을 여는 방법을 보여 줍니다. 이 구성은 Azure 리소스에 대 한 액세스를 제한 하려는 원격 사용자가 있거나 Azure에서 리소스를 보호 하는 경우에 유용 합니다.

이 문서의 단계를 수행 하면 다음 다이어그램에서 아키텍처를 만들어 사용자 VPN 클라이언트가 가상 허브에 연결 된 스포크 VNet의 특정 리소스 (VM1)에 액세스할 수 있지만 다른 리소스 (V M 2)에는 액세스할 수 없습니다. 이 아키텍처 예를 기본 지침으로 사용 합니다.

:::image type="content" source="./media/manage-secure-access-resources-spoke-p2s/diagram.png" alt-text="다이어그램: 보안 된 가상 허브" :::

## <a name="prerequisites"></a>사전 요구 사항

[!INCLUDE [Prerequisites](../../includes/virtual-wan-before-include.md)]

* 사용 하려는 인증 구성에 사용할 수 있는 값이 있습니다. 예를 들어 RADIUS 서버는 인증을 Azure Active Directory 하거나 [인증서를 생성 및 내보냅니다](../vpn-gateway/vpn-gateway-certificates-point-to-site.md).

## <a name="create-a-virtual-wan"></a>가상 WAN 만들기

[!INCLUDE [Create virtual WAN](../../includes/virtual-wan-create-vwan-include.md)]

## <a name="define-p2s-configuration-parameters"></a><a name= "p2s-config"></a>P2S 구성 매개 변수 정의

지점 및 사이트 간 (P2S) 구성은 원격 클라이언트 연결에 대 한 매개 변수를 정의 합니다. 이 섹션에서는 P2S 구성 매개 변수를 정의한 다음 VPN 클라이언트 프로필에 사용할 구성을 만듭니다. 따라야 하는 지침은 사용 하려는 인증 방법에 따라 달라 집니다.

### <a name="authentication-methods"></a>인증 방법

인증 방법을 선택 하는 경우 세 가지 옵션을 선택할 수 있습니다. 각 메서드에는 특정 요구 사항이 있습니다. 다음 방법 중 하나를 선택 하 고 단계를 완료 합니다.

* **Azure Active Directory 인증:** 다음을 가져옵니다.

   * Azure AD 테 넌 트에 등록 된 Azure VPN 엔터프라이즈 응용 프로그램의 **응용 프로그램 ID** 입니다.
   * **발급자** 입니다. 예: `https://sts.windows.net/your-Directory-ID`.
   * **AZURE AD 테 넌 트**. 예: `https://login.microsoftonline.com/your-Directory-ID`.

* **Radius 기반 인증:** Radius 서버 IP, Radius 서버 비밀 및 인증서 정보를 가져옵니다.

* **Azure 인증서:** 이 구성의 경우 인증서가 필요 합니다. 인증서를 생성 하거나 가져와야 합니다. 클라이언트 인증서는 각 클라이언트에 필요 합니다. 또한 루트 인증서 정보 (공개 키)를 업로드 해야 합니다. 필요한 인증서에 대 한 자세한 내용은 [인증서 생성 및 내보내기](../vpn-gateway/vpn-gateway-certificates-point-to-site.md)를 참조 하세요.

다음 예제에서는 Azure 인증서 인증을 보여 줍니다.

[!INCLUDE [Define parameters](../../includes/virtual-wan-p2s-configuration-include.md)]

## <a name="create-the-hub-and-gateway"></a><a name="hub"></a>허브 및 게이트웨이 만들기

이 섹션에서는 지점 및 사이트 간 게이트웨이를 사용 하 여 가상 허브를 만듭니다. 를 구성할 때 다음 예제 값을 사용할 수 있습니다.

* **허브 개인 IP 주소 공간:** 10.0.0.0/24
* **클라이언트 주소 풀:** 10.5.0.0/16
* **사용자 지정 DNS 서버:** 최대 5 대의 DNS 서버를 나열할 수 있습니다.

[!INCLUDE [Create hub](../../includes/virtual-wan-p2s-hub-include.md)]

## <a name="generate-vpn-client-configuration-files"></a><a name="generate"></a>VPN 클라이언트 구성 파일 생성

이 섹션에서는 구성 프로필 파일을 생성 하 고 다운로드 합니다. 이러한 파일은 클라이언트 컴퓨터에서 기본 VPN 클라이언트를 구성 하는 데 사용 됩니다. 클라이언트 프로필 파일의 내용에 대 한 자세한 내용은 지점 및 [사이트 간 구성-인증서](../vpn-gateway/point-to-site-vpn-client-configuration-azure-cert.md)를 참조 하세요.

[!INCLUDE [Download profile](../../includes/virtual-wan-p2s-download-profile-include.md)]

## <a name="configure-vpn-clients"></a><a name="clients"></a>VPN 클라이언트 구성

다운로드한 프로필을 사용하여 원격 액세스 클라이언트를 구성합니다. 각 운영 체제에 대한 프로시저가 다르므로 시스템에 적용되는 지침을 따르세요.

[!INCLUDE [Configure clients](../../includes/virtual-wan-p2s-configure-clients-include.md)]

## <a name="connect-the-spoke-vnet"></a><a name="connect-spoke"></a>스포크 VNet 연결

이 섹션에서는 가상 WAN 허브에 스포크 가상 네트워크를 연결 합니다.

[!INCLUDE [Connect spoke virtual network](../../includes/virtual-wan-connect-vnet-hub-include.md)]

## <a name="create-virtual-machines"></a><a name="create-vm"></a>가상 머신 만들기

이 섹션에서는 VNet (VM1 및 V M 2)에 두 개의 Vm을 만듭니다. 네트워크 다이어그램에서 10.18.0.4 및 10.18.0.5를 사용 합니다. Vm을 구성할 때 만든 가상 네트워크를 선택 해야 합니다 (네트워킹 탭에 있음). VM을 만드는 단계는 [빠른 시작: Vm 만들기](../virtual-machines/windows/quick-create-portal.md)를 참조 하세요.

## <a name="secure-the-virtual-hub"></a><a name="secure"></a>가상 허브 보안

표준 가상 허브에는 스포크 가상 네트워크의 리소스를 보호 하는 기본 제공 보안 정책이 없습니다. 보안 가상 허브는 azure 방화벽 또는 타사 공급자를 사용 하 여 Azure에서 리소스를 보호 하는 들어오고 나가는 트래픽을 관리 합니다.

[가상 WAN 허브에서 Azure 방화벽 구성](howto-firewall.md)문서를 사용 하 여 허브를 보안 허브로 변환 합니다.

## <a name="create-rules-to-manage-and-filter-traffic"></a><a name= "create-rules"></a> 트래픽을 관리 하 고 필터링 하는 규칙 만들기

Azure 방화벽의 동작을 지시 하는 규칙을 만듭니다. 허브를 보호 하 여 Azure 리소스에 액세스 하기 전에 가상 허브에 입력 하는 모든 패킷에 방화벽 처리가 적용 되도록 합니다.

이러한 단계를 완료 한 후에는 VPN 사용자가 개인 IP 주소 10.18.0.4를 사용 하 여 VM에 액세스 하도록 허용 하지만 개인 IP 주소를 사용 하 여 VM **에 액세스 하** 는 것을 허용 하는 아키텍처를 만들었습니다 10.18.0.5

1. Azure Portal에서 **방화벽 관리자** 로 이동 합니다.
1. 보안 아래에서 **Azure 방화벽 정책** 을 선택 합니다.
1. **Azure Firewall 정책 만들기** 를 선택합니다.
1. **정책 세부 정보** 에서 이름을 입력 하 고 가상 허브가 배포 된 지역을 선택 합니다.
1. **다음: DNS 설정(미리 보기)** 을 선택합니다.
1. **다음: 규칙** 을 선택합니다.
1. **규칙** 탭에서 **규칙 컬렉션 추가** 를 선택합니다.
1. 컬렉션의 이름을 제공 합니다. 유형을 **네트워크** 로 설정 합니다. 우선 순위 값 **100** 을 추가 합니다.
1. 아래 예제와 같이 규칙, 원본 유형, 원본, 프로토콜, 대상 포트 및 대상 유형의 이름을 입력 합니다. 그런 다음 **추가** 를 선택 합니다. 이 규칙은 VPN 클라이언트 풀의 모든 IP 주소가 개인 IP 주소 10.18.04를 사용 하 여 VM에 액세스 하도록 허용 하지만 가상 허브에 연결 된 다른 리소스는 허용 하지 않습니다. 원하는 아키텍처 및 사용 권한 규칙에 맞게 원하는 규칙을 만듭니다.

   :::image type="content" source="./media/manage-secure-access-resources-spoke-p2s/rules.png" alt-text="방화벽 규칙" :::

1. 완료되면 **다음: 위협 인텔리전스** 를 선택합니다.
1. 완료되면 **다음: 허브** 를 선택합니다.
1. **허브** 탭에서 **가상 허브 연결** 을 선택합니다.
1. 이전에 만든 가상 허브를 선택 하 고 **추가** 를 선택 합니다.
1. **검토 + 만들기** 를 선택합니다.
1. **만들기** 를 선택합니다.

이 프로세스를 완료 하는 데 5 분 이상 걸릴 수 있습니다.

## <a name="route-traffic-through-azure-firewall"></a><a name="send"></a>Azure 방화벽을 통한 트래픽 라우팅

이 섹션에서는 트래픽이 Azure 방화벽을 통해 라우팅되고 있는지 확인 해야 합니다.

1. 포털의 **방화벽 관리자** 에서 **보안 가상 허브** 를 선택 합니다.
1. 만든 가상 허브를 선택 합니다.
1. **설정** 에서 **보안 구성** 을 선택합니다.
1. **프라이빗 트래픽** 에서 **Azure Firewall을 통해 전송** 을 선택합니다.
1. VNet 연결 및 분기 연결 개인 트래픽이 Azure 방화벽으로 보호 되는지 확인 합니다.
1. **저장** 을 선택합니다.

## <a name="validate"></a><a name="validate"></a>유효화

보안 허브의 설정을 확인 합니다.

1. 클라이언트 장치에서 VPN을 통해 **보안 가상 허브** 에 연결 합니다.
1. 클라이언트에서 IP 주소 **10.18.0.4** 를 Ping 합니다. 응답이 표시 되어야 합니다.
1. 클라이언트에서 IP 주소 **10.18.0.5** 를 Ping 합니다. 응답을 볼 수 없습니다.

### <a name="considerations"></a>고려 사항

* 보호 된 가상 허브의 **유효 경로 테이블** 에 방화벽의 개인 트래픽에 대 한 다음 홉이 있는지 확인 합니다. 유효 경로 테이블에 액세스 하려면 **가상 허브** 리소스로 이동 합니다. **연결** 에서 **라우팅** 을 선택 하 고 **유효 경로** 를 선택 합니다. 여기에서 **기본** 경로 테이블을 선택 합니다.
* 규칙 [만들기](#create-rules) 섹션에서 규칙을 만들었는지 확인 합니다. 이러한 단계를 누락 하는 경우 생성 된 규칙은 실제로 허브에 연결 되지 않으며, 경로 테이블 및 패킷 흐름은 Azure 방화벽을 사용 하지 않습니다.

## <a name="next-steps"></a>다음 단계

* 가상 WAN에 대한 자세한 내용은 [가상 WAN FAQ](virtual-wan-faq.md)를 참조하세요.
* Azure 방화벽에 대 한 자세한 내용은 [Azure 방화벽 FAQ](../firewall/firewall-faq.md)를 참조 하세요.
