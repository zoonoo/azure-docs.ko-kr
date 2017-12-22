---
title: "웹 응용 프로그램 방화벽이 있는 응용 프로그램 게이트웨이 만들기 또는 업데이트 | Microsoft Docs"
description: "포털을 사용하여 웹 응용 프로그램 방화벽이 있는 응용 프로그램 게이트웨이를 만드는 방법에 대해 알아보기"
services: application-gateway
documentationcenter: na
author: davidmu1
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: b561a210-ed99-4ab4-be06-b49215e3255a
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/03/2017
ms.author: davidmu
ms.openlocfilehash: bfc06c1b44974fd17a3794654503d21d6407a917
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/21/2017
---
# <a name="create-an-application-gateway-with-a-web-application-firewall-by-using-the-portal"></a>포털을 사용하여 웹 응용 프로그램 방화벽이 있는 응용 프로그램 게이트웨이 만들기

> [!div class="op_single_selector"]
> * [Azure 포털](application-gateway-web-application-firewall-portal.md)
> * [PowerShell](application-gateway-web-application-firewall-powershell.md)
> * [Azure CLI](application-gateway-web-application-firewall-cli.md)

WAF(웹 응용 프로그램 방화벽)를 사용하도록 설정된 응용 프로그램 게이트웨이를 만드는 방법에 대해 알아봅니다.

Azure Application Gateway의 WAF는 SQL 삽입 공격, 사이트 간 스크립팅 공격, 세션 하이재킹 등의 일반적인 웹 기반 공격으로부터 웹 응용 프로그램을 보호합니다. WAF는 다양한 OWASP 상위 10 일반적인 웹 취약점으로부터 보호합니다.

## <a name="scenarios"></a>시나리오

이 문서에서는 두 가지 시나리오를 제공합니다. 첫 번째 시나리오에서는 [WAF를 사용하여 응용 프로그램 게이트웨이를 만드는](#create-an-application-gateway-with-web-application-firewall) 방법에 대해 알아봅니다. 두 번째 시나리오에서는 [기존 응용 프로그램 게이트웨이에 WAF를 추가하는](#add-web-application-firewall-to-an-existing-application-gateway) 방법에 대해 알아봅니다.

![예제 시나리오][scenario]

> [!NOTE]
> 응용 프로그램 게이트웨이에 사용자 지정 상태 프로브, 백 엔드 풀 주소 및 추가 규칙을 추가할 수 있습니다. 이러한 응용 프로그램은 초기 배포 중이 아니라 응용 프로그램 게이트웨이가 구성된 후 구성됩니다.

## <a name="before-you-begin"></a>시작하기 전에

 응용 프로그램 게이트웨이에는 자체 서브넷이 필요합니다. 가상 네트워크를 만들 때 여러 서브넷을 둘 수 있는 충분한 주소 공간이 있는지 확인합니다. 응용 프로그램 게이트웨이를 서브넷에 배포한 후 추가 응용 프로그램 게이트웨이를 서브넷에 추가할 수 있습니다.

## <a name="add-web-application-firewall-to-an-existing-application-gateway"></a>기존 응용 프로그램 게이트웨이에 웹 응용 프로그램 방화벽 추가

이 예제에서는 **방지** 모드에서 WAF를 지원하도록 기존 응용 프로그램 게이트웨이를 업데이트합니다.

1. Azure Portal의 **즐겨찾기** 창에서 **모든 리소스**를 선택합니다. **모든 리소스** 블레이드에서 기존 응용 프로그램 게이트웨이를 선택합니다. 선택한 구독에 이미 여러 개의 리소스가 있는 경우 **이름을 기준으로 필터링** 상자에 이름을 입력하여 DNS 영역에 간편하게 액세스합니다.

   ![기존 응용 프로그램 게이트웨이 선택][1]

2. **웹 응용 프로그램 방화벽**을 선택하고 응용 프로그램 게이트웨이 설정을 업데이트합니다. 업데이트가 끝나면 **저장**을 선택합니다. 

3. WAF를 지원하도록 기존 응용 프로그램 게이트웨이를 업데이트하는 다음 설정을 사용합니다.

   | **설정** | **값** | **세부 정보**
   |---|---|---|
   |**WAF 계층으로 업그레이드**| 선택 | 이 옵션은 응용 프로그램 게이트웨이의 계층을 WAF 계층으로 설정합니다.|
   |**방화벽 상태**| 사용 | 이 설정은 WAF에서 방화벽을 사용합니다.|
   |**방화벽 모드** | 방지 | 이 설정은 WAF가 악의적인 트래픽을 처리하는 방식입니다. **검색** 모드에서만 이벤트를 기록합니다. **방지** 모드는 이벤트를 기록하고 악의적인 트래픽을 중지합니다.|
   |**규칙 집합**|3.0|이 설정은 백 엔드 풀 멤버를 보호하는 데 사용되는 [코어 규칙 집합](application-gateway-web-application-firewall-overview.md#core-rule-sets)을 결정합니다.|
   |**사용하지 않는 규칙 구성**|다름|가능한 가양성을 방지하기 위해 이 설정을 사용하여 특정 [규칙 및 규칙 그룹](application-gateway-crs-rulegroups-rules.md)을 사용하지 않도록 설정할 수 있습니다.|

    >[!NOTE]
    > 기존 응용 프로그램 게이트웨이를 WAF SKU로 업그레이드할 때 SKU 크기는 **중형**으로 변경됩니다. 구성이 완료된 후 이 설정을 재구성할 수 있습니다.

    ![기본 설정][2-1]

    > [!NOTE]
    > WAF 로그를 보려면 진단을 사용하도록 설정하고 **ApplicationGatewayFirewallLog**를 선택합니다. 테스트 목적으로만 인스턴스 수 **1**을 선택합니다. **2** 아래의 인스턴스 수는 SLA에서 다루지 않기 때문에 권장하지 않습니다. WAF를 사용하는 경우 작은 게이트웨이를 사용할 수 없습니다.

## <a name="create-an-application-gateway-with-a-web-application-firewall"></a>웹 응용 프로그램 방화벽이 있는 응용 프로그램 게이트웨이 만들기

이 시나리오에서는 다음을 수행합니다.

* 두 인스턴스를 사용하여 중간 WAF 응용 프로그램 게이트웨이를 만듭니다.
* 예약된 CIDR 블록이 10.0.0.0/16이고 이름이 AdatumAppGatewayVNET인 가상 네트워크를 만듭니다.
* CIDR 블록으로 10.0.0.0/28을 사용하는 Appgatewaysubnet이라고 하는 서브넷을 만듭니다.
* SSL 오프로드용 인증서를 구성합니다.

1. [Azure 포털](https://portal.azure.com)에 로그인합니다. 아직 계정이 없는 경우 [1개월 평가판](https://azure.microsoft.com/free)을 등록할 수 있습니다.

2. 포털의 **즐겨찾기** 창에서 **새로 만들기**를 선택합니다.

3. **새** 블레이드에서 **네트워킹**을 선택합니다. 다음 그림과 같이 **네트워킹** 블레이드에서 **Application Gateway**를 선택합니다.

    ![응용 프로그램 게이트웨이 생성][1]

4. 표시되는 **기본 사항** 블레이드에서 다음 값을 입력한 다음 **확인**을 선택합니다.

   | **설정** | **값** | **세부 정보**
   |---|---|---|
   |**Name**|AdatumAppGateway|응용 프로그램 게이트웨이의 이름입니다.|
   |**계층**|WAF|사용 가능한 값은 표준 또는 WAF입니다. WAF에 대해 자세히 알아보려면 [웹 응용 프로그램 방화벽](application-gateway-web-application-firewall-overview.md)을 참조하세요.|
   |**SKU 크기**|중간|표준 계층 옵션은 **소형**, **중간** 및 **대형**입니다. WAF 계층 옵션은 **중간** 및 **대형** 뿐입니다.|
   |**인스턴스 수**|2|고가용성을 위한 응용 프로그램 게이트웨이의 인스턴스 수입니다. 테스트 목적으로만 인스턴스 수 1을 사용합니다.|
   |**구독**|[구독 이름]|응용 프로그램 게이트웨이를 만드는 데 사용할 구독을 선택합니다.|
   |**리소스 그룹**|**새로 만들기:** AdatumAppGatewayRG|리소스 그룹을 만듭니다. 리소스 그룹 이름은 선택한 구독 내에서 고유해야 합니다. 리소스 그룹에 대해 자세히 알아보려면 [Resource Manager](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fapplication-gateway%2ftoc.json#resource-groups) 개요 문서를 참조하세요.|
   |**위치**:|미국 서부||

   ![기본 설정 구성][2-2]

5. **가상 네트워크** 아래에 표시되는 **설정** 블레이드에서 **가상 네트워크 선택**을 선택합니다. **가상 네트워크 선택** 블레이드에서 **새로 만들기**를 선택합니다.

   ![가상 네트워크 선택][2]

6. **가상 네트워크 만들기** 블레이드에서 다음 값을 입력한 다음 **확인**을 선택합니다. **설정** 블레이드의 **서브넷** 필드가 선택한 서브넷으로 채워집니다.

   |**설정** | **값** | **세부 정보** |
   |---|---|---|
   |**Name**|AdatumAppGatewayVNET|응용 프로그램 게이트웨이의 이름입니다.|
   |**주소 공간**|10.0.0.0/16| 이 값은 가상 네트워크에 대한 주소 공간입니다.|
   |**서브넷 이름**|AppGatewaySubnet|응용 프로그램 게이트웨이의 서브넷 이름입니다.|
   |**서브넷 주소 범위**|10.0.0.0/28 | 이 서브넷을 사용하면 백 엔드 풀 멤버가 가상 네트워크에서 추가 서브넷을 사용할 수 있습니다.|

7. **프런트 엔드 IP 구성** 아래의 **설정** 블레이드에서 **IP 주소 유형**으로 **공용**을 선택합니다.

8. **공용 IP 주소** 아래의 **설정** 블레이드에서 **공용 IP 주소 선택**을 선택합니다. **공용 IP 주소 선택** 블레이드에서 **새로 만들기**를 선택합니다.

   ![공용 IP 주소 선택][3]

9. **공용 IP 주소 만들기** 블레이드에서 기본값을 그대로 적용하고 **확인**을 선택합니다. **공용 IP 주소** 필드가 선택한 공용 IP 주소로 채워집니다.

10. **수신기 구성** 아래의 **설정** 블레이드에서 **프로토콜** 아래에 있는 **HTTP**를 선택합니다. **HTTPS**를 사용하려면 인증서가 필요합니다. 인증서에 대한 개인 키가 필요합니다. 인증서의 .pfx 내보내기를 제공하고 파일의 암호를 입력합니다.

11. **WAF**에 대한 특정 설정을 구성합니다.

   |**설정** | **값** | **세부 정보** |
   |---|---|---|
   |**방화벽 상태**| 사용| 이 설정은 WAF를 켜고 끕니다.|
   |**방화벽 모드** | 방지| 이 설정은 WAF가 악의적인 트래픽에 대해 수행하는 작업을 결정합니다. **검색** 모드에서만 트래픽을 기록합니다. **방지** 모드는 403 권한 없음 응답을 사용하여 트래픽을 기록하고 중지합니다.|


12. **요약** 페이지를 검토하고 **확인**을 선택합니다. 이제 응용 프로그램 게이트웨이가 만들어지고 대기 상태가 됩니다.

13. 응용 프로그램 게이트웨이를 만든 후에는 포털에서 해당 응용 프로그램 게이트웨이로 이동하여 응용 프로그램 게이트웨이 구성을 계속합니다.

    ![응용 프로그램 게이트웨이 리소스 보기][10]

이러한 단계에서는 수신기, 백 엔드 풀, 백 엔드 HTTP 설정 및 규칙에 대한 기본 설정으로 기본 응용 프로그램 게이트웨이를 만듭니다. 프로비전을 성공적으로 마친 후 배포에 맞게 이러한 설정을 수정할 수 있습니다.

> [!NOTE]
> 기본 WAF 구성을 사용하여 만든 응용 프로그램 게이트웨이는 보호를 위해 CRS 3.0으로 구성됩니다.

## <a name="next-steps"></a>다음 단계

[공용 IP 주소](../dns/dns-custom-domain.md#public-ip-address)에 대한 사용자 지정 도메인 별칭을 구성하려면 Azure DNS 또는 다른 DNS 공급자를 사용할 수 있습니다.

진단 로깅을 구성하여 WAF를 통해 검색 또는 방지되는 이벤트를 기록하려면 [Application Gateway 진단](application-gateway-diagnostics.md)을 참조하세요.

사용자 지정 상태 프로브를 만들려면 [사용자 지정 상태 프로브 만들기](application-gateway-create-probe-portal.md)를 참조하세요.

SSL 오프로딩을 구성하고 비용이 많은 드는 SSL 구독을 웹 서버에서 제거하려면 [SSL 오프로드 구성](application-gateway-ssl-portal.md)을 참조하세요.

<!--Image references-->
[1]: ./media/application-gateway-web-application-firewall-portal/figure1.png
[2]: ./media/application-gateway-web-application-firewall-portal/figure2.png
[2-1]: ./media/application-gateway-web-application-firewall-portal/figure2-1.png
[2-2]: ./media/application-gateway-web-application-firewall-portal/figure2-2.png
[3]: ./media/application-gateway-web-application-firewall-portal/figure3.png
[10]: ./media/application-gateway-web-application-firewall-portal/figure10.png
[scenario]: ./media/application-gateway-web-application-firewall-portal/scenario.png
