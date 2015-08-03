<properties 
   pageTitle="Azure 검색 계층 (Azure 아키텍처 패턴)" 
   description="Azure 검색 계층 패턴은 Foundation 영역의 일부이며, CPIF 아키텍처 문서에서 광범위하게 설명됩니다." 
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

# Azure 검색 계층 (Azure 아키텍처 패턴)

[클라우드 플랫폼 통합 프레임워크(CPIF)](azure-architectures-cpif-overview.md)는 Microsoft 클라우드 솔루션으로 온보딩 응용 프로그램을 통합하는 워크로드 통합 지침을 제공합니다.

CPIF는 조직, 고객 및 파트너가 Azure, System Center 및 Windows Server의 하이브리드 클라우드 플랫폼 및 관리 기능을 활용하여 클라우드를 대상으로 하는 작업 부하 설계 및 배포에 대해 설명합니다.

**Azure 검색 계층** 패턴은 **Foundation** 영역의 일부이며 CPIF 아키텍처 문서에서 광범위하게 설명됩니다.

##  Azure 검색 계층

Azure 검색 계층 디자인 패턴은 지리적 경계를 넘어 예측 가능한 성능 및 고가용성을 제공할 수 있는 검색 서비스를 지원하는 데 필요한 Azure 기능 및 서비스를 자세히 설명하며 검색 솔루션 제공에 Azure 검색을 사용하기 위한 아키텍처 패턴을 제공합니다. Azure 검색은 Microsoft Azure 내에서 작성된 "서비스로서의 검색"으로, 개발자가 인프라 서비스를 배포, 관리 또는 유지하지 않고도 검색 기능을 응용 프로그램에 통합하여 이 기능을 응용 프로그램에 제공할 수 있습니다. 이 패턴의 목적은 다양한 상황 및 디자인에 사용하기 위해 반복 가능한 솔루션을 제공하는 것입니다.

## 아키텍처 패턴 개요 

이 문서에서는 핵심의 두 변형을 Azure 검색을 사용하여 서비스의 아키텍처 범위를 보여주기 위한 핵심 패턴을 설명합니다. 핵심 패턴은 Azure 검색 및 주변 Azure 서비스로 구성되며 종단 간 디자인을 만들기 위한 지침을 제공 하고자 합니다. 패턴의 변형, 특히 공유 서비스 및 동시성 패턴이 다른 요구 사항, 서비스 수준 계약(SLA) 및 기타 특정 조건에 따라 지침을 제공하도록 이 섹션에도 포함됩니다.

##  추가 리소스
[Azure 검색 계층(pdf)](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-e581d65d)

## 참고 항목
[CPIF 아키텍처](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-bd1e434a)

[전역 부하 분산된 웹 계층](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-2c3c663a)

[부하 분산된 데이터 계층](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-dfb09e41)

[하이브리드 네트워킹](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-5e401f38)

[일괄 처리 계층](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-0bc3f8b1)

<!---HONumber=July15_HO4-->