---
title: 클라우드에 의한 Azure VMware 솔루션 간단함 - CloudSimple 프라이빗 클라우드를 위한 부하 분산 솔루션 선택
description: 프라이빗 클라우드에서 애플리케이션을 배포하는 부하 분산 옵션에 대해 설명합니다.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 6c98b699b1d3aba15ce69c519d35d7ce3e90d123
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77014881"
---
# <a name="choose-a-load-balancing-solution-for-cloudsimple-private-clouds"></a>CloudSimple 프라이빗 클라우드를 위한 부하 분산 솔루션 선택

CloudSimple 프라이빗 클라우드에서 응용 프로그램을 배포할 때 로드 균형 조정을 위한 몇 가지 옵션 중 하나를 선택할 수 있습니다.

CloudSimple 프라이빗 클라우드에서 가상 또는 소프트웨어 기반 로드 밸런서를 선택하거나 Azure 구독에서 실행 중인 Azure L7 로드 밸런서를 사용하여 CloudSimple 프라이빗 클라우드에서 실행되는 웹 계층 VM을 프런트 엔드에 사용할 수도 있습니다. 여기에, 우리는 몇 가지 옵션을 나열합니다 :

## <a name="virtual-load-balancers"></a>가상 로드 밸러저

vCenter 인터페이스를 통해 VMware 환경에 가상 로드 밸러터 어플라이언스를 배포하고 응용 프로그램 트래픽을 프런트 엔드하도록 구성할 수 있습니다.

일부 인기있는 공급 업체는 다음과 http://nginx.org/en/docs/http/load_balancing.html 같습니다 NginX : F5- BigIP - 트래픽 관리자 : https://www.f5.com/products/big-ip-services/virtual-editions Citrix ADC :https://www.citrix.com/products/citrix-adc/

## <a name="azure-l7-load-balancer"></a>Azure L7 로드 밸러터

Azure 응용 프로그램 게이트웨이를 사설 클라우드에서 실행되는 응용 프로그램에 대한 L7 로드 밸런서로 사용하는 경우 로드 밸런서 소프트웨어를 관리할 필요가 없습니다. 로드 밸러버 소프트웨어는 Azure에서 관리합니다. 프라이빗 클라우드의 모든 웹 계층 VM은 개인 IP 주소를 사용하며 이름을 해결하기 위해 추가 NAT 규칙이나 공용 IP 주소가 필요하지 않습니다. 웹 계층 VM은 개인, 대기 시간이 짧은 고대역폭 연결을 통해 Azure 응용 프로그램 게이트웨이와 통신합니다.

이 솔루션을 구성하는 방법에 대한 자세한 내용은 Azure 응용 프로그램 게이트웨이를 L7 로드 밸러커로 사용하는 솔루션 가이드를 참조하십시오.

## <a name="azure-internal-load-balancer"></a>Azure 내부 로드 밸러터

웹 프런트 엔드 계층이 Azure 구독의 Azure vNet 내에서 실행되고 응용 프로그램의 DB 계층이 CloudSimple 프라이빗 클라우드의 VMware VM에서 실행중인 하이브리드 배포에서 응용 프로그램을 실행하도록 선택한 경우 Azure 내부 로드를 사용할 수 있습니다. 트래픽 관리를 위해 DB 계층 VM 앞에 있는 밸런서(L4 로드 밸런서)

자세한 내용은 Azure [내부 부하 분산 검사](../load-balancer/concepts-limitations.md#internalloadbalancer) 문서를 참조하세요.

## <a name="global-server-load-balancer"></a>글로벌 서버 로드 밸러블러

DNS 기반 로드 밸러블러를 찾고 있는 경우 Azure Marketplace에서 사용할 수 있는 타사 솔루션을 사용하거나 기본 Azure 솔루션으로 이동하여 사용할 수 있습니다.

Azure 트래픽 관리자는 DNS 기반 트래픽 로드 밸러서로, 높은 가용성과 응답성을 제공하면서 글로벌 Azure 지역 및 온-프레미스에서 서비스에 트래픽을 최적으로 배포할 수 있습니다. 자세한 내용은 Azure [트래픽 관리자](../traffic-manager/traffic-manager-configure-geographic-routing-method.md) 설명서를 참조하십시오.
