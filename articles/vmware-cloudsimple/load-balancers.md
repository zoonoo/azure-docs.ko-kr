---
title: Azure VMware 솔루션 (AVS)-AVS 사설 클라우드에 대 한 부하 분산 솔루션 선택
description: AVS 사설 클라우드에 응용 프로그램을 배포 하는 부하 분산 옵션에 대해 설명 합니다.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 405bc9d95b8d82e2181e2fb828d6bcc00c8c4639
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77014881"
---
# <a name="choose-a-load-balancing-solution-for-avs-private-clouds"></a>AVS 사설 클라우드에 대 한 부하 분산 솔루션 선택

AVS 사설 클라우드에 응용 프로그램을 배포 하는 경우 부하 분산에 대 한 몇 가지 옵션을 선택할 수 있습니다.

AVS 사설 클라우드에서 가상 또는 소프트웨어 기반 부하 분산 장치를 선택 하거나 Azure 구독에서 실행 중인 Azure L7 부하 분산 장치를 사용 하 여 AVS 사설 클라우드에서 실행 중인 웹 계층 Vm을 프런트 엔드 할 수도 있습니다. 몇 가지 옵션을 나열 합니다.

## <a name="virtual-load-balancers"></a>가상 부하 분산 장치

VCenter 인터페이스를 통해 VMware 환경에 가상 부하 분산 장치 어플라이언스를 배포 하 고 응용 프로그램 트래픽을 프런트 엔드 하도록 구성할 수 있습니다.

널리 사용 되는 공급 업체는 다음과 같습니다. NginX: http://nginx.org/en/docs/http/load_balancing.html F5-Traffic Manager: https://www.f5.com/products/big-ip-services/virtual-editions Citrix ADC: https://www.citrix.com/products/citrix-adc/

## <a name="azure-l7-load-balancer"></a>Azure L7 부하 분산 장치

Azure 애플리케이션 게이트웨이를 AVS 사설 클라우드에서 실행 중인 응용 프로그램에 대 한 L7 부하 분산 장치로 사용 하는 경우 부하 분산 장치 소프트웨어를 관리할 필요가 없습니다. 부하 분산 장치 소프트웨어는 Azure에서 관리 됩니다. AVS 사설 클라우드의 모든 웹 계층 Vm은 개인 IP 주소를 사용 하며 이름을 확인 하는 데 추가 NAT 규칙 또는 공용 Ip 주소가 필요 하지 않습니다. 웹 계층 Vm은 낮은 대기 시간, 낮은 대역폭의 전용 연결을 통해 Azure 애플리케이션 게이트웨이와 통신 합니다.

이 솔루션을 구성 하는 방법에 대해 자세히 알아보려면 Azure 애플리케이션 Gateway를 L7 부하 분산 장치로 사용에 대 한 솔루션 가이드를 참조 하세요.

## <a name="azure-internal-load-balancer"></a>Azure 내부 부하 분산 장치

웹 프런트 엔드 계층이 Azure 구독의 Azure vNet 내에서 실행 되 고 응용 프로그램의 DB 계층이 AVS 사설 클라우드의 VMware Vm에서 실행 되는 하이브리드 배포에서 응용 프로그램을 실행 하도록 선택한 경우 Azure 내부 부하 분산 장치 (L)를 사용할 수 있습니다. 4 부하 분산 장치) 트래픽 관리를 위한 DB 계층 Vm 앞에 있습니다.

자세히 알아보려면 Azure [내부 Load Balancer](../load-balancer/concepts-limitations.md#internalloadbalancer) 설명서를 참조 하세요.

## <a name="global-server-load-balancer"></a>글로벌 서버 부하 분산 장치

DNS 기반 부하 분산 장치를 찾고 있는 경우 Azure Marketplace에서 사용할 수 있는 타사 솔루션을 사용 하거나 네이티브 Azure 솔루션으로 이동할 수 있습니다.

Azure Traffic Manager는 고가용성 및 응답성을 제공 하면서 글로벌 Azure 지역 및 온-프레미스에서 서비스에 최적의 트래픽을 분산할 수 있도록 하는 DNS 기반 트래픽 부하 분산 장치입니다. 자세히 알아보려면 Azure [Traffic Manager](../traffic-manager/traffic-manager-configure-geographic-routing-method.md) 설명서를 참조 하세요.
