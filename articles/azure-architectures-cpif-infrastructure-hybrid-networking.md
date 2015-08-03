<properties 
   pageTitle="하이브리드 네트워킹(Azure 아키텍처 패턴)" 
   description="하이브리드 네트워킹 패턴은 Infrastructure 영역의 일부이며, CPIF 아키텍처 문서에서 광범위하게 설명됩니다." 
   services="" 
   documentationCenter="" 
   authors="arynes" 
   manager="fredhar" 
   editor=""/>

<tags
   ms.service="cloud-services"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple" 
   ms.date="03/25/2015"
   ms.author="arynes"/>

# 하이브리드 네트워킹(Azure 아키텍처 패턴)

[클라우드 플랫폼 통합 프레임워크(CPIF)](azure-architectures-cpif-overview.md)는 Microsoft 클라우드 솔루션으로 온보딩 응용 프로그램을 통합하는 워크로드 통합 지침을 제공합니다.

CPIF는 조직, 고객 및 파트너가 Azure, System Center 및 Windows Server의 하이브리드 클라우드 플랫폼 및 관리 기능을 활용하여 클라우드를 대상으로 하는 작업 부하 설계 및 배포에 대해 설명합니다.

**하이브리드 네트워킹** 패턴은 **Infrastructure** 영역의 일부이며, CPIF 아키텍처 문서에서 광범위하게 설명됩니다.

##  하이브리드 네트워킹

하이브리드 네트워킹 디자인 패턴은 지리적 경계를 넘어 예측 가능한 성능 및 고가용성을 제공할 수 있는 네트워크 기능을 지원하는데 필요한 Azure 기능과 서비스를 자세히 설명합니다. Microsoft Azure 설명서 사이트 내에서 Microsoft Azure 지역 및 각 지역 내에서 사용 가능한 서비스의 전체 목록이 제공됩니다. 이 문서에서는 하이브리드 환경에 대한 Microsoft Azure 네트워킹 기능을 간략하게 설명합니다. Microsoft Azure 가상 네트워킹을 사용하면 안전하게 인터넷 또는 개인 네트워크 연결을 통해 온-프레미스 데이터 센터에 연결하고 Azure에서 논리적으로 분리된 네트워크를 만들 수 있습니다. 또한 개별 클라이언트 컴퓨터는 IPsec VPN 연결을 사용하여 격리된 Azure 네트워크에 연결할 수 있습니다.

하이브리드 네트워킹 아키텍처 패턴은 다음 포커스 영역을 포함합니다.

- Azure에 대한 온-프레미스 네트워크 연결 
- 지역에 걸쳐 Azure 가상 네트워크 확장 
- 구독 간에 Azure 가상 네트워크 확장 
- 개발자에게 원격 네트워크 액세스 제공 

## 아키텍처 패턴 개요 

하이브리드 네트워킹 아키텍처 패턴은 만들 수 있는 시나리오의 수로 인해 복잡합니다. 이 아키텍처 패턴은 다음 네가지 시나리오에 중점을 둡니다.

- 단일 구독 및 지역 내의 다중 홉 가상 네트워크 라우팅을 사용하여 사이트 간 하이브리드 네트워킹 
- 구독 및 지역에 걸쳐 다중 홉 가상 네트워크 라우팅을 사용하여 사이트 간 하이브리드 네트워킹 
- MPLS 연결을 사용하여 ExpressRoute 하이브리드 네트워킹 
- IXP 연결을 사용하여 ExpressRoute 하이브리드 네트워킹 

##  추가 리소스
[하이브리드 네트워킹(pdf)](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-5e401f38)

## 참고 항목
[CPIF 아키텍처](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-bd1e434a)

[전역 부하 분산된 웹 계층](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-2c3c663a)

[부하 분산된 데이터 계층](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-dfb09e41)

[Azure 검색 계층](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-e581d65d)

[일괄 처리 계층](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-0bc3f8b1)

<!---HONumber=July15_HO4-->