---
title: Azure 네트워크 보안 기능을 사용하여 개인 데이터 보호 | Microsoft Docs
description: Azure 네트워크 보안 기능을 사용하여 개인 데이터를 보호합니다. 이 기능을 GDPR(일반 데이터 보호 규정)을 준수하기 위해 사용할 수도 있습니다.
services: security
documentationcenter: na
author: Barclayn
manager: MBaldwin
editor: TomSh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/06/2018
ms.author: barclayn
ms.custom: ''
ms.openlocfilehash: 54aeb22a501e248105931df341d23e524448155a
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/08/2018
---
# <a name="protect-personal-data-with-network-security-features-azure-application-gateway-and-network-security-groups"></a>네트워크 보안 기능을 사용하여 개인 데이터 보호: Azure Application Gateway 및 네트워크 보안 그룹

이 문서는 Azure Application Gateway와 네트워크 보안 그룹을 사용하여 개인 데이터를 보호하는 데 도움이 되는 정보와 절차를 제공합니다. 이 정보는 GDPR(일반 데이터 보호 규정)을 준수하는 데 도움이 될 수 있습니다.

개인 데이터의 개인 정보를 보호하기 위한 다계층 보안 전략의 중요한 요소는 SQL 삽입 또는 교차 사이트 스크립팅과 같은 일반적인 취약성 악용에 대한 방어입니다. Azure 가상 네트워크에서 원하지 않는 네트워크 트래픽을 차단하면 중요한 데이터가 손상되지 않도록 보호할 수 있으며, Microsoft Azure는 공격자로부터 데이터를 보호할 수 있는 도구를 제공합니다.

## <a name="scenario"></a>시나리오

미국에 본사를 둔 대형 크루즈 회사는 영국 제도뿐만 아니라 지중해, 아드리아해 및 발트해의 여정을 제공하기 위해 운영을 확대하고 있습니다. 이러한 노력을 촉진하기 위해 이탈리아, 독일, 덴마크 및 영국에 기반을 둔 몇 개의 소형 크루즈 라인을 인수했습니다.

회사에서 Microsoft Azure를 사용하여 클라우드에 회사 데이터를 저장하고 이 데이터를 처리하고 액세스하는 가상 머신에서 응용 프로그램을 실행합니다. 이 데이터에는 전 세계 고객 기반의 이름, 주소, 전화 번호 및 신용 카드 정보와 같이 식별 가능한 개인 정보가 포함됩니다. 주소, 전화 번호, 납세자 번호 및 모든 위치의 회사 직원에 대한 기타 정보와 같이 기존의 인적 자원 정보도 포함됩니다. 또한 크루즈 라인에서 현재 및 과거 고객과의 관계를 추적하기 위해 개인 정보가 포함된 보상 및 충성도 프로그램 구성원에 대한 대규모 데이터베이스를 유지하고 있습니다.

회사 직원은 회사의 원격 사무실에서 네트워크에 액세스하고, 전 세계에 위치한 여행사는 일부 회사 리소스에 액세스하고 Azure VM에서 호스팅되는 웹 기반 응용 프로그램을 사용하여 상호 작용합니다.

## <a name="problem-statement"></a>문제 설명

회사의 클라우드 기반 응용 프로그램에서 저장하거나 사용하는 개인 데이터를 노출시킬 수 있는 악의적인 코드를 실행하기 위해 소프트웨어 취약성을 악용하는 공격자로부터 고객 및 직원의 개인 데이터를 회사에서 보호해야 합니다.

## <a name="company-goal"></a>회사 목표

권한 없는 사람이 Azure Virtual Network 및 일반적인 취약성을 악용하여 Azure Virtual Network에 있는 응용 프로그램과 데이터에 액세스할 수 없도록 하는 것이 회사의 목표입니다. 

## <a name="solutions"></a>솔루션

Microsoft Azure는 원하지 않는 트래픽이 Azure Virtual Network에 입력되지 않도록 방지하는 보안 메커니즘을 제공합니다. 인바운드 및 아웃바운드 트래픽 제어는 일반적으로 방화벽에 의해 수행됩니다. Azure에서는 간단한 분산 방화벽 역할을 하는 웹 응용 프로그램 방화벽 및 NSG(네트워크 보안 그룹)과 함께 Application Gateway를 사용할 수 있습니다. 이러한 도구를 사용하면 원하지 않는 네트워크 트래픽을 검색하고 차단할 수 있습니다.

### <a name="application-gatewayweb-application-firewall"></a>Application Gateway/웹 응용 프로그램 방화벽

[Azure Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction)의 [WAF(웹 응용 프로그램 방화벽)](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) 구성 요소는 알려진 일반 취약성을 악용하는 악의적 공격의 대상이 되고 있는 웹 응용 프로그램을 보호합니다. 중앙 집중식 WAF는 웹 공격으로부터 보호하고, 응용 프로그램을 변경하지 않고도 보안 관리를 간소화합니다.

Azure WAF는 SQL 삽입, 교차 사이트 스크립팅, HTTP 프로토콜 위반 및 변칙, 봇, 크롤러, 스캐너, 일반적인 응용 프로그램 구성 오류, HTTP 서비스 거부 및 기타 일반적 공격(예: 명령 삽입, HTTP 요청 밀반입, HTTP 응답 분할 및 원격 파일 포함 공격)을 포함하여 다양한 공격 범주를 처리합니다. 

WAF를 사용하여 응용 프로그램 게이트웨이를 만들거나 기존 응용 프로그램 게이트웨이에 WAF를 추가할 수 있습니다. 두 경우 모두 Azure Application Gateway에는 자체 서브넷이 필요합니다.

#### <a name="how-do-i-create-an-application-gateway-with-waf"></a>WAF를 사용하여 응용 프로그램 게이트웨이를 만들려면 어떻게 할까요? 

WAF를 사용하도록 설정된 새 응용 프로그램 게이트웨이를 만들려면 다음을 수행합니다.

1. [Azure 포털](https://portal.azure.com) 에 로그인합니다.
2. **리소스 만들기** > **네트워킹** > **Application Gateway**를 선택합니다.
5. 표시되는 **기본 사항** 블레이드에서 이름, 계층(표준 또는 WAF), SKU 크기(소형, 중형 또는 대형), 인스턴스 수(고가용성의 경우 2), 구독, 리소스 그룹 및 위치 필드에 대한 값을 입력합니다.
6. **가상 네트워크** 아래에 표시되는 **설정** 블레이드에서 **가상 네트워크 선택**을 클릭합니다. 이 단계에서는 [가상 네트워크 선택] 블레이드가 열립니다.

7. **새로 만들기**를 클릭하여 **가상 네트워크 만들기** 블레이드를 엽니다.

8. 이름, 주소 공간, 서브넷 이름, 서브넷 주소 범위 값을 입력합니다. **확인**을 클릭합니다.

9. **설정** 블레이드의 **프런트 엔드 IP 구성** 아래에서 IP 주소 유형을 선택합니다.

10. **공용 IP 주소 선택**, **새로 만들기**를 차례로 클릭합니다.

11. 기본값을 그대로 적용하고 **확인**을 클릭합니다

12. **설정** 블레이드의 **수신기 구성** 아래에 있는 **프로토콜**에서 HTTP 또는 HTTPS를 사용하도록 클릭합니다. HTTPS를 사용하려면 인증서가 필요합니다.

13. WAF 특정 설정, 즉 **방화벽 상태**(**사용**) 및 **방화벽 모드**(**방지**)를 구성합니다. 모드로 **검색**을 선택하면 트래픽만 로깅됩니다.

14. **요약** 페이지를 검토하고 **확인**을 클릭합니다. 이제 응용 프로그램 게이트웨이가 만들어지고 대기 상태가 됩니다.

응용 프로그램 게이트웨이를 만들었으면 포털에서 해당 응용 프로그램 게이트웨이로 이동하여 해당 구성을 계속할 수 있습니다.

![만든 응용 프로그램 게이트웨이](media/protect-netsec/adatum-app-gateway.png)

#### <a name="how-do-i-add-waf-to-an-existing-application"></a>기존 응용 프로그램에 WAF를 추가하려면 어떻게 할까요?

방지 모드에서 WAF를 지원하도록 기존 응용 프로그램 게이트웨이를 업데이트하려면 다음을 수행합니다.

1. Azure Portal의 **즐겨찾기** 창에서 **모든 리소스**를 클릭합니다.

2. **모든 리소스** 블레이드에서 기존 Application Gateway를 클릭합니다. 
>[!NOTE]
참고: 선택한 구독에 이미 여러 리소스가 있는 경우 [이름으로 필터링...]에서 이름 입력합니다. DNS 영역에 간편하게 액세스할 수 있는 상자입니다.
3. **웹 응용 프로그램 방화벽**을 클릭하고, 응용 프로그램 게이트웨이 설정, 즉 **WAF 계층으로 업그레이드**(선택), **방화벽 상태**(사용), **방화벽 모드**(방지)를 업데이트합니다. 또한 규칙 집합을 구성하고 사용하지 않는 규칙을 구성해야 합니다.

WAF를 사용하여 새 응용 프로그램 게이트웨이를 만드는 방법과 기존 응용 프로그램 게이트웨이에 WAF를 추가하는 방법에 대한 자세한 내용은 [포털을 사용하여 웹 응용 프로그램 방화벽이 있는 Application Gateway 만들기](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal)를 참조하세요.

### <a name="network-security-groups"></a>네트워크 보안 그룹

[NSG(네트워크 보안 그룹)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg)에는 [Azure VNet(Virtual Network)](https://docs.microsoft.com/azure/virtual-network/)에 연결된 리소스에 대한 네트워크 트래픽을 허용하거나 거부하는 보안 규칙 목록이 포함되어 있습니다. NSG는 서브넷 또는 개별 VM에 연결할 수 있습니다. NSG를 서브넷에 연결하면 규칙이 서브넷에 연결된 모든 리소스에 적용됩니다. 또한 NSG를 VM 또는 NIC에 연결하여 트래픽을 더욱 제한할 수 있습니다.

NSG에는 네 가지 속성, 즉 이름, 지역, 리소스 그룹 및 규칙이 포함됩니다.
>[!Note]
NSG는 하나의 리소스 그룹에 속하지만, NSG와 동일한 Azure 지역의 일부인 리소스는 모든 리소스 그룹의 리소스에 연결될 수 있습니다.

NSG 규칙에는 9가지 속성, 즉 이름, 프로토콜(TCP, UDP 또는 \*(UDP 및 TCP 외에도 ICMP 포함)), 원본 포트 범위, 대상 포트 범위, 원본 주소 접두사, 대상 주소 접두사, 방향(인바운드 또는 아웃바운드), 우선 순위(100-4096) 및 액세스 형식(허용 또는 거부)이 포함됩니다. 모든 NSG에는 만든 규칙으로 삭제되거나 재정의될 수 있는 기본 규칙 집합이 포함됩니다.

#### <a name="how-do-i-implement-nsgs"></a>NSG를 구현하려면 어떻게 할까요?

NSG를 구현하려면 계획이 필요하며 고려해야 할 몇 가지 설계 고려 사항이 있습니다. 여기에는 구독당 NSG 수 및 NSG당 규칙 수에 대한 제한, VNet 및 서브넷 디자인, 특별 규칙, ICMP 트래픽, 서브넷을 통한 계층 격리, 부하 분산 장치 등이 포함됩니다.

NSG 계획 및 구현에 대한 자세한 지침과 샘플 배포 시나리오는 [네트워크 보안 그룹을 사용하여 네트워크 트래픽 필터링](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg)을 참조하세요.

#### <a name="how-do-i-create-rules-in-an-nsg"></a>NSG에서 규칙을 만들려면 어떻게 할까요?

기존 NSG에서 인바운드 규칙을 만들려면 다음을 수행합니다.

1. **모든 서비스**, **네트워크 보안 그룹**을 차례로 클릭합니다.

2. NSG 목록에서 **NSG-FrontEnd**, **인바운드 보안 규칙**을 차례로 클릭합니다.

3. 인바운드 보안 규칙 목록에서 **추가**를 클릭합니다.

4. 이름, 우선 순위, 원본, 프로토콜, 원본 범위, 대상, 대상 포트 범위 및 작업 필드에 값을 입력합니다.

몇 초 후에 새 규칙이 NSG에 표시됩니다.

![네트워크 보안 규칙](media/protect-netsec/inbound-security.png)

서브넷에 NSG를 만들고, 규칙을 만들고, NSG를 프런트 엔드/백 엔드 서브넷과 연결하는 방법에 대한 자세한 지침은 [Azure Portal을 사용하여 네트워크 보안 그룹 만들기](https://docs.microsoft.com/azure/virtual-network/virtual-networks-create-nsg-arm-pportal)를 참조하세요.

## <a name="next-steps"></a>다음 단계

[Azure 네트워크 보안(영문)](https://azure.microsoft.com/blog/azure-network-security/)

[Azure 네트워크 보안 모범 사례](https://docs.microsoft.com/azure/security/azure-security-network-security-best-practices)

[네트워크 보안 그룹에 대한 정보 가져오기](https://docs.microsoft.com/rest/api/network/virtualnetwork/get-information-about-a-network-security-group)

[WAF(웹 응용 프로그램 방화벽)](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview)
