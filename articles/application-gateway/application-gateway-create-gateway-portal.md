---
title: "Application Gateway 만들기 - Azure Portal | Microsoft Docs"
description: "포털을 사용하여 응용 프로그램 게이트웨이를 만드는 방법을 알아봅니다."
services: application-gateway
documentationcenter: na
author: davidmu1
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 54dffe95-d802-4f86-9e2e-293f49bd1e06
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/31/2017
ms.author: davidmu
ms.translationtype: HT
ms.sourcegitcommit: fff84ee45818e4699df380e1536f71b2a4003c71
ms.openlocfilehash: d3c39cfe3159cd4059a81f966fb551175188278b
ms.contentlocale: ko-kr
ms.lasthandoff: 08/01/2017

---
# <a name="create-an-application-gateway-with-the-portal"></a>포털을 사용하여 Application Gateway 만들기

[Application Gateway](application-gateway-introduction.md)는 ADC(응용 프로그램 배달 컨트롤러)를 서비스로 제공하여 다양한 7계층 부하 분산 기능을 제공하는 전용 가상 어플라이언스입니다. 이 문서에서는 Azure Portal을 사용하여 Application Gateway를 만들고 기존 서버를 백 엔드 멤버로 추가하는 단계를 안내합니다.

## <a name="log-in-to-azure"></a>Azure에 로그인

Azure Portal([http://portal.azure.com](http://portal.azure.com))에 로그인합니다.

## <a name="create-application-gateway"></a>응용 프로그램 게이트웨이 만들기

Application Gateway를 만들려면 다음 단계를 완료하세요. Application Gateway에는 자체 서브넷이 필요합니다. 가상 네트워크를 만들 때 여러 서브넷을 둘 수 있는 충분한 주소 공간이 있는지 확인합니다. 응용 프로그램 게이트웨이가 서브넷에 배포된 후에는 다른 응용 프로그램 게이트웨이만 추가할 수 있습니다.

1. 포털의 즐겨찾기 창에서 **새로 만들기**를 클릭합니다.
1. **새로 만들기** 블레이드에서 **네트워킹**을 클릭합니다. 다음 그림과 같이 **네트워킹** 블레이드에서 **Application Gateway**를 클릭합니다.

    ![Application Gateway 만들기][1]

1. 표시되는 **기본 사항** 블레이드에서 다음 값을 입력한 다음 **확인**을 클릭합니다.

   | **설정** | **값** | **세부 정보**|
   |---|---|---|
   |**Name**|AdatumAppGateway|Application Gateway의 이름|
   |**계층**|Standard|사용 가능한 값은 표준 또는 WAF입니다. [웹 응용 프로그램 방화벽](application-gateway-web-application-firewall-overview.md)을 방문하여 WAF에 대해 자세히 알아보세요.|
   |**SKU 크기**|중간|표준 계층을 선택할 때 제공되는 옵션으로 소형, 중형 및 대형이 있습니다. WAF 계층을 선택할 때 옵션은 중간 및 대형 뿐입니다.|
   |**인스턴스 수**|2|고가용성을 위한 Application Gateway의 인스턴스 수입니다. 인스턴스 수 1은 테스트 목적으로만 사용해야 합니다.|
   |**구독**|[구독 이름]|응용 프로그램 게이트웨이를 만들 구독을 선택합니다.|
   |**리소스 그룹**|**새로 만들기:** AdatumAppGatewayRG|리소스 그룹을 만듭니다. 리소스 그룹 이름은 선택한 구독 내에서 고유해야 합니다. 리소스 그룹에 대해 자세히 알아보려면 [Resource Manager](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fapplication-gateway%2ftoc.json#resource-groups) 개요 문서를 참조하세요.|
   |**위치**:|미국 서부||

1. **가상 네트워크** 아래에 표시되는 **설정** 블레이드에서 **가상 네트워크 선택**을 클릭합니다. **가상 네트워크 선택** 블레이드가 열립니다.  **새로 만들기**를 클릭하여 **가상 네트워크 만들기** 블레이드를 엽니다.

   ![가상 네트워크 선택][2]

1. **가상 네트워크 만들기** 블레이드에서 다음 값을 입력한 다음 **확인**을 클릭합니다. **가상 네트워크 만들기** 및 **가상 네트워크 선택** 블레이드가 닫힙니다. 이 단계에서는 **설정** 블레이드의 **서브넷** 필드를 선택한 서브넷으로 채웁니다.

   | **설정** | **값** | **세부 정보**|
   |---|---|---|
   |**Name**|AdatumAppGatewayVNET|Application Gateway의 이름|
   |**주소 공간**|10.0.0.0/16|가상 네트워크에 대한 주소 공간|
   |**서브넷 이름**|AppGatewaySubnet|Application Gateway의 서브넷 이름|
   |**서브넷 주소 범위**|10.0.0.0/28|이 서브넷을 사용하면 백 엔드 풀 멤버가 가상 네트워크에서 추가 서브넷을 사용할 수 있습니다.|

1. **설정** 블레이드의 **프런트 엔드 IP 구성** 아래에서 **IP 주소 유형**으로 **공용**을 선택합니다.

1. **설정** 블레이드의 **공용 IP 주소** 아래에서 **공용 IP 주소 선택**을 클릭하여 **공용 IP 주소 선택** 블레이드를 열고, **새로 만들기**를 클릭합니다.

   ![공용 IP 선택][3]

1. **공용 IP 주소 만들기** 블레이드에서 기본값을 그대로 적용하고 **확인**을 클릭합니다. 블레이드를 닫고 **공용 IP 주소**를 선택된 공용 IP 주소로 채웁니다.

1. **설정** 블레이드의 **수신기 구성** 아래에서 **프로토콜** 아래에 있는 **HTTP**를 클릭합니다. **포트** 필드에 사용할 포트를 입력합니다.

2. **설정** 블레이드에서 **확인**을 클릭하여 계속합니다.

1. **요약** 블레이드에서 설정을 검토하고 **확인**을 클릭하여 Application Gateway 만들기를 시작합니다. 응용 프로그램 게이트웨이 만들기는 장기 실행 작업이므로 완료하는 데 시간이 걸립니다.

## <a name="add-servers-to-backend-pools"></a>백 엔드 풀에 서버 추가

응용 프로그램 게이트웨이를 만든 후에는 부하 분산을 위해 응용 프로그램을 호스팅하는 시스템도 응용 프로그램 게이트웨이에 추가해야 합니다. 이러한 서버의 IP 주소, FQDN 또는 NIC는 백 엔드 주소 풀에 추가됩니다.

### <a name="ip-address-or-fqdn"></a>IP 주소 또는 FQDN

1. Application Gateway를 만든 후 Azure Portal의 **즐겨찾기** 창에서 **모든 리소스**를 클릭합니다. 모든 리소스 블레이드에서 **AdatumAppGateway** Application Gateway를 클릭합니다. 선택한 구독에 이미 여러 개의 리소스가 있는 경우 **이름을 기준으로 필터링...**에 **AdatumAppGateway**를 입력합니다. 응용 프로그램 게이트웨이에 간편하게 액세스할 수 있는 상자입니다.

1. 만든 Application Gateway가 표시됩니다. **백 엔드 풀**을 클릭하고 현재 백 엔드 풀인 **appGatewayBackendPool**을 선택합니다. 그러면 **appGatewayBackendPool** 블레이드가 열립니다.

   ![Application Gateway 백 엔드 풀][4]

1. **대상 추가**를 클릭하여 FQDN 값의 IP 주소를 추가합니다. **IP 주소 또는 FQDN**을 **형식**으로 선택하고 필드에 IP 주소 또는 FQDN을 입력합니다. 추가 백 엔드 풀 멤버에 대해 이 단계를 반복합니다. 완료하면 **저장**을 클릭합니다.

### <a name="virtual-machine-and-nic"></a>가상 컴퓨터 및 NIC

백 엔드 풀 구성원으로 가상 컴퓨터 NIC를 추가할 수도 있습니다. Application Gateway로써 같은 가상 네트워크에 있는 가상 컴퓨터만 드롭다운을 통해 사용할 수 있습니다.

1. Application Gateway를 만든 후 Azure Portal의 **즐겨찾기** 창에서 **모든 리소스**를 클릭합니다. 모든 리소스 블레이드에서 **AdatumAppGateway** Application Gateway를 클릭합니다. 선택한 구독에 이미 여러 개의 리소스가 있는 경우 **이름을 기준으로 필터링...**에 **AdatumAppGateway**를 입력합니다. 응용 프로그램 게이트웨이에 간편하게 액세스할 수 있는 상자입니다.

1. 만든 Application Gateway가 표시됩니다. **백 엔드 풀**을 클릭하고 현재 백 엔드 풀인 **appGatewayBackendPool**을 선택합니다. 그러면 **appGatewayBackendPool** 블레이드가 열립니다.

   ![Application Gateway 백 엔드 풀][5]

1. **대상 추가**를 클릭하여 FQDN 값의 IP 주소를 추가합니다. **가상 컴퓨터**를 **형식**으로 선택하고 사용할 가상 컴퓨터 및 NIC를 선택합니다. 완료하면 **저장**을 클릭합니다.

   > [!NOTE]
   > Application Gateway와 같은 가상 네트워크에 있는 가상 컴퓨터만 드롭다운 상자를 통해 사용할 수 있습니다.

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 리소스 그룹, 가상 컴퓨터 및 모든 관련 리소스를 삭제합니다. 이렇게 하려면 응용 프로그램 게이트웨이 블레이드에서 해당 리소스 그룹을 선택하고 **삭제**를 클릭합니다.

## <a name="next-steps"></a>다음 단계

이 시나리오에서는 응용 프로그램 게이트웨이를 배포하고 서버를 백 엔드에 추가했습니다. 다음 단계에서는 설정을 수정하고, 게이트웨이의 규칙을 조정하여 Application Gateway를 구성합니다. 이러한 단계는 다음 문서에서 확인할 수 있습니다.

[사용자 지정 상태 프로브 만들기](application-gateway-create-probe-portal.md)

[SSL 오프로드 구성](application-gateway-ssl-portal.md)

응용 프로그램 게이트웨이의 기능인 [웹 응용 프로그램 방화벽](application-gateway-webapplicationfirewall-overview.md)을 사용하여 웹 사이트를 보호하는 방법을 알아봅니다.

<!--Image references-->
[1]: ./media/application-gateway-create-gateway-portal/figure1.png
[2]: ./media/application-gateway-create-gateway-portal/figure2.png
[3]: ./media/application-gateway-create-gateway-portal/figure3.png
[4]: ./media/application-gateway-create-gateway-portal/figure4.png
[5]: ./media/application-gateway-create-gateway-portal/figure5.png
[scenario]: ./media/application-gateway-create-gateway-portal/scenario.png

