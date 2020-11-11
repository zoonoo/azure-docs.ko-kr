---
title: Azure VMware 솔루션에서 공용 IP 기능을 사용 하는 방법
description: 이 문서에서는 Azure 가상 WAN에서 공용 IP 기능을 사용 하는 방법을 설명 합니다.
ms.topic: how-to
ms.date: 10/28/2020
ms.openlocfilehash: 7ff1debe7b52599a2e4f20378f385359325be2f7
ms.sourcegitcommit: 4bee52a3601b226cfc4e6eac71c1cb3b4b0eafe2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2020
ms.locfileid: "94504410"
---
# <a name="how-to-use-the-public-ip-functionality-in-azure-vmware-solution"></a>Azure VMware 솔루션에서 공용 IP 기능을 사용 하는 방법

공용 IP는 Azure VMware 솔루션 연결의 새로운 기능입니다. 웹 서버, Vm (가상 컴퓨터) 및 공용 네트워크를 통해 액세스할 수 있는 호스트와 같은 리소스를 만듭니다. 

두 가지 방법으로 공용 인터넷 액세스를 사용 하도록 설정 합니다. 

- 응용 프로그램은 HTTP/HTTPS 트래픽에 대 한 Application Gateway 부하 분산 장치에서 호스트 및 게시할 수 있습니다.
- Azure 가상 WAN의 공용 IP 기능을 통해 게시 됩니다.

Azure VMware 솔루션 사설 클라우드 배포의 일부로 공용 IP 기능을 사용 하도록 설정 하면 자동화 된 필수 구성 요소가 생성 되 고 사용 하도록 설정 됩니다.

-  Virtual WAN

-  Express 경로 연결을 사용 하는 가상 WAN 허브

-  공용 IP를 사용 하는 Azure 방화벽 서비스

이 문서에서는 가상 WAN에서 공용 IP 기능을 사용할 수 있는 방법을 자세히 설명 합니다.

## <a name="prerequisites"></a>필수 구성 요소

- Azure VMware 솔루션 환경
- Azure VMware 솔루션 환경에서 실행 되는 웹 서버.
- 가상 WAN 허브 배포에 대 한 새로운 겹치지 않는 IP 범위 (일반적으로) `/24` 입니다.

## <a name="reference-architecture"></a>참조 아키텍처

:::image type="content" source="media/public-ip-usage/public-ip-architecture-diagram.png" alt-text="공용 IP 아키텍처 다이어그램" border="false" lightbox="media/public-ip-usage/public-ip-architecture-diagram.png":::

아키텍처 다이어그램은 Azure VMware 솔루션 환경에서 호스트 되 고 RFC1918 개인 IP 주소를 사용 하 여 구성 된 웹 서버를 보여 줍니다.  웹 서비스는 가상 WAN 공용 IP 기능을 통해 인터넷에서 사용할 수 있게 됩니다.  공용 IP는 일반적으로 Azure 방화벽에서 변환 되는 대상 NAT입니다. DNAT 규칙을 사용 하는 경우 방화벽 정책은 공용 IP 주소 요청을 포트가 있는 개인 주소 (웹 서버)로 변환 합니다.

사용자 요청은 Azure 방화벽의 규칙에 따라 DNAT 사용 하 여 개인 IP로 변환 된 공용 IP의 방화벽을 적중 합니다. 방화벽은 NAT 테이블을 확인 하 고, 요청이 항목과 일치 하는 경우 Azure VMware 솔루션 환경의 변환 된 주소 및 포트로 트래픽을 전달 합니다.

웹 서버는 요청을 수신 하 고 요청 된 정보 또는 페이지를 사용 하 여 방화벽에 회신 한 다음 사용자에 게 공용 IP 주소에 대 한 정보를 전달 합니다.

## <a name="test-case"></a>테스트 사례
이 시나리오에서는 IIS 웹 서버를 인터넷에 게시 해야 합니다. Azure VMware 솔루션의 공용 IP 기능을 사용 하 여 공용 IP 주소에 웹 사이트를 게시 합니다.  방화벽에서 NAT 규칙을 구성 하 고 공용 IP를 사용 하는 Azure VMware 솔루션 리소스 (웹 서버 포함)에 액세스 합니다.

## <a name="deploy-virtual-wan"></a>Virtual WAN을 배포합니다.

1. Azure Portal에 로그인 하 여 **Azure VMware 솔루션** 을 검색 하 고 선택 합니다.

1. Azure VMware 솔루션 사설 클라우드를 선택 합니다.

   :::image type="content" source="media/public-ip-usage/avs-private-cloud-resource.png" alt-text="Azure VMware 솔루션 사설 클라우드의 스크린샷." border="true" lightbox="media/public-ip-usage/avs-private-cloud-resource.png":::

1. **관리** 에서 **연결** 을 선택 합니다.

   :::image type="content" source="media/public-ip-usage/avs-private-cloud-manage-menu.png" alt-text="연결 섹션의 스크린샷" border="true" lightbox="media/public-ip-usage/avs-private-cloud-manage-menu.png":::

1. **공용 IP** 탭을 선택한 다음 **구성** 을 선택 합니다.

   :::image type="content" source="media/public-ip-usage/connectivity-public-ip-tab.png" alt-text="공용 IP 구성 시작 위치를 보여 주는 스크린샷" border="true" lightbox="media/public-ip-usage/connectivity-public-ip-tab.png":::

1. 기본값을 그대로 적용 하거나 변경 하 고 **만들기** 를 선택 합니다.

   - 가상 광역 네트워크 리소스 그룹

   - 가상 광역 네트워크 이름

   - 가상 허브 주소 블록 (겹치지 않는 새 IP 범위 사용)

   - 공용 Ip 수 (1-100)

모든 구성 요소의 배포를 완료 하는 데 약 1 시간이 걸립니다. 이 배포는이 Azure VMware 솔루션 환경에 대해 이후의 모든 공용 Ip를 지 원하는 경우에만 한 번만 수행 하면 됩니다.  

>[!TIP]
>**알림** 영역에서 상태를 모니터링할 수 있습니다. 

## <a name="view-and-add-public-ip-addresses"></a>공용 IP 주소 보기 및 추가

아래 단계를 수행 하 여 더 많은 공용 IP 주소를 확인 하 고 추가할 수 있습니다.

1. Azure Portal에서 **방화벽** 을 검색 하 고 선택 합니다.

1. 배포 된 방화벽을 선택 하 고 **Azure 방화벽 관리자 방문을 선택 하 여이 방화벽을 구성 하 고 관리** 합니다.

   :::image type="content" source="media/public-ip-usage/configure-manage-deployed-firewall.png" alt-text="방화벽을 구성 하 고 관리 하는 옵션을 보여 주는 스크린샷" border="true" lightbox="media/public-ip-usage/configure-manage-deployed-firewall.png":::

1. **보안 된 가상 허브** 를 선택 하 고, 목록에서 가상 허브를 선택 합니다.

   :::image type="content" source="media/public-ip-usage/select-virtual-hub.png" alt-text="방화벽 관리자의 스크린샷" lightbox="media/public-ip-usage/select-virtual-hub.png":::

1. 가상 허브 페이지에서 **공용 ip 구성** 을 선택 하 고, 공용 ip 주소를 더 추가 하려면 **추가** 를 선택 합니다. 

   :::image type="content" source="media/public-ip-usage/virtual-hub-page-public-ip-configuration.png" alt-text="방화벽 관리자에서 공용 IP 구성을 추가 하는 방법의 스크린샷" border="true" lightbox="media/public-ip-usage/virtual-hub-page-public-ip-configuration.png":::

1. 필요한 Ip 수를 제공 하 고 **추가** 를 선택 합니다.

   :::image type="content" source="media/public-ip-usage/add-number-of-ip-addresses-required.png" alt-text="지정 된 수의 공용 IP 구성을 추가 하는 스크린샷" border="true":::


## <a name="create-firewall-policies"></a>방화벽 정책 만들기

모든 구성 요소를 배포한 후에는 추가 된 리소스 그룹에서 구성 요소를 볼 수 있습니다. 다음 단계는 방화벽 정책을 추가 하는 것입니다.

1. Azure Portal에서 **방화벽** 을 검색 하 고 선택 합니다.

1. 배포 된 방화벽을 선택 하 고 **Azure 방화벽 관리자 방문을 선택 하 여이 방화벽을 구성 하 고 관리** 합니다.

   :::image type="content" source="media/public-ip-usage/configure-manage-deployed-firewall.png" alt-text="방화벽을 구성 하 고 관리 하는 옵션을 보여 주는 스크린샷" border="true" lightbox="media/public-ip-usage/configure-manage-deployed-firewall.png":::

1. **Azure 방화벽** 정책을 선택 하 고 **Azure 방화벽 정책 만들기** 를 선택 합니다.

   :::image type="content" source="media/public-ip-usage/create-firewall-policy.png" alt-text="방화벽 관리자에서 방화벽 정책을 만드는 방법의 스크린샷" border="true" lightbox="media/public-ip-usage/create-firewall-policy.png":::

1. **기본 사항** 탭에서 필요한 세부 정보를 입력 하 고 **다음: DNS 설정** 을 선택 합니다. 

1. **DNS** 탭에서 **사용 안 함** 을 선택 하 고 **다음: 규칙** 을 선택 합니다.

1. **규칙 컬렉션 추가** 를 선택 하 고 아래 세부 정보를 입력 한 후 **추가** 를 선택 하 고 **다음: 위협 인텔리전스** 를 선택 합니다.

   -  Name
   -  규칙 컬렉션 형식-DNAT
   -  우선 순위
   -  규칙 컬렉션 작업 – 허용
   -  규칙 이름
   -  원본 유형- **IPaddress**
   -  원본-* *\** _
   -  프로토콜-_ *TCP**
   -  대상 포트 – **80**
   -  대상 유형- **IP 주소**
   -  대상- **공용 IP 주소**
   -  변환 된 주소 – **Azure VMware 솔루션 웹 서버 개인 IP 주소**
   -  변환 된 포트- **Azure VMware 솔루션 웹 서버 포트**

1. 기본값을 그대로 두고 **다음: 허브** 를 선택 합니다.

1. **가상 허브 연결** 을 선택 합니다.

1. 목록에서 허브를 선택 하 고 **추가** 를 선택 합니다.

   :::image type="content" source="media/public-ip-usage/secure-hubs-with-azure-firewall-polcy.png" alt-text="Scecured 가상 허브로 변환 될 선택 된 허브를 보여 주는 스크린샷" border="true" lightbox="media/public-ip-usage/secure-hubs-with-azure-firewall-polcy.png":::

1. **다음: 태그** 를 선택합니다. 

1. 필드 리소스를 범주화 하는 이름 및 값 쌍을 만듭니다. 

1. **다음: 검토 + 만들기** 를 선택 하 고 **만들기** 를 선택 합니다.

## <a name="limitations"></a>제한 사항

SDDCs 당 100 공용 Ip가 있을 수 있습니다.

## <a name="next-steps"></a>다음 단계

[Azure 가상 WAN](../virtual-wan/virtual-wan-about.md)을 사용 하 여 공용 IP 주소를 사용 하는 방법에 대해 자세히 알아보세요.

