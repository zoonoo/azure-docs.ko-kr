<properties 
   pageTitle="트래픽 관리자란 | Microsoft Azure"
   description="이 문서는 트래픽 관리자란 무엇이며 응용 프로그램에 대한 올바른 트래픽 라우팅 선택인지를 이해하도록 도움을 줍니다."
   services="traffic-manager"
   documentationCenter=""
   authors="sdwheeler"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="06/09/2016"
   ms.author="sewhee" />

# 트래픽 관리자란?

Microsoft Azure 트래픽 관리자를 사용하면 전 세계 여러 데이터 센터에서 실행 중인 서비스 끝점에 대한 사용자 트래픽의 배포를 제어할 수 있습니다.

트래픽 관리자에서 지원되는 서비스 끝점은 Azure VM, 웹앱 및 클라우드 서비스를 포함합니다. 또한 외부, Azure가 아닌 끝점으로 트래픽 관리자를 사용할 수 있습니다.

트래픽 관리자는 구성된 트래픽 라우팅 방법 및 현재 끝점 상태 보기를 기반으로 가장 적합한 끝점에 최종 사용자 요청을 보내도록 DNS(Domain Name System)를 사용하여 작동합니다. 그러면 클라이언트는 적절한 서비스 끝점에 직접 연결됩니다.

트래픽 관리자는 다양한 응용 프로그램 요구에 맞게 [다양한 트래픽 라우팅 방법](traffic-manager-routing-methods.md)을 제공합니다. 트래픽 관리자는 전체 Azure 지역 장애를 포함하여 장애에 대해 복원력 있는 고가용성 응용 프로그램을 만들 수 있는 [끝점 상태 검사 및 자동 끝점 장애 조치(failover)](traffic-manager-monitoring.md)를 제공합니다.

## 트래픽 관리자 혜택

트래픽 관리자를 사용하여 다음을 수행할 수 있습니다.

- **중요 응용 프로그램의 가용성 향상** – 트래픽 관리자를 사용하면 끝점이 작동 중단되면 자동 장애 조치 기능을 제공하여 중요 응용 프로그램에 대한 고가용성을 제공할 수 있습니다.
- **고성능 응용 프로그램의 응답성 향상** – Azure를 사용하면 전 세계에 있는 데이터 센터에서 클라우드 서비스 또는 웹 사이트를 실행할 수 있습니다. 트래픽 관리자는 클라이언트에서 가장 낮은 네트워크 대기 시간으로 최종 사용자를 끝점에 연결하여 응용 프로그램의 응답성을 향상시킬 수 있습니다.
- **가동 중지 시간 없이 업그레이드 및 서비스 유지 관리 수행** - 유지 관리가 진행 중인 경우 대체 끝점으로 트래픽을 보내도록 트래픽 관리자를 사용하여 최종 사용자에 대한 가동 중지 시간 없이 응용 프로그램에서 업그레이드 및 기타 계획된 유지 관리 작업을 원활하게 수행할 수 있습니다.
- **온-프레미스 및 클라우드 기반 응용 프로그램 결합** – 트래픽 관리자는 "클라우드로 버스트", "클라우드로 마이그레이션" 및 "클라우드로 장애 조치(Failover)" 시나리오를 포함한 하이브리드 클라우드 및 온-프레미스 배포에 사용할 수 있도록 외부, Azure가 아닌 끝점을 지원합니다.
- **대규모의 복잡한 배포에 대한 트래픽 분산** – 트래픽 라우팅 방법은 [중첩 트래픽 관리자 프로필](traffic-manager-nested-profiles.md)을 사용하여 결합되어 대규모의 더욱 복잡한 배포의 요구를 충족하도록 정교하고 유연한 트래픽 라우팅 구성을 만들 수 있습니다.

[AZURE.INCLUDE [load-balancer-compare-tm-ag-lb-include.md](../../includes/load-balancer-compare-tm-ag-lb-include.md)]

## 다음 단계

- [트래픽 관리자 작동 방식](traffic-manager-how-traffic-manager-works.md)에 대해 자세히 알아봅니다.

- [트래픽 관리자 끝점 모니터링](traffic-manager-monitoring.md)을 사용하여 고가용성 응용 프로그램을 개발하는 방법을 알아봅니다.

- 트래픽 관리자가 지원하는 [트래픽 라우팅 방법](traffic-manager-routing-methods.md)에 대해 자세히 알아봅니다.

- [트래픽 관리자 프로필을 만듭니다](traffic-manager-manage-profiles.md).
 

<!---HONumber=AcomDC_0824_2016-->