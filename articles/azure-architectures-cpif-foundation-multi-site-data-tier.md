<properties 
   pageTitle="다중 사이트 데이터 계층(Azure 아키텍처 패턴)" 
   description="다중 사이트 데이터 계층 패턴은 Foundation 영역의 일부이며, CPIF 아키텍처 문서에서 광범위하게 설명됩니다." 
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

# 다중 사이트 데이터 계층(Azure 아키텍처 패턴)

[클라우드 플랫폼 통합 프레임워크(CPIF)](azure-architectures-cpif-overview.md)는 Microsoft 클라우드 솔루션으로 온보딩 응용 프로그램을 통합하는 워크로드 통합 지침을 제공합니다.

CPIF는 조직, 고객 및 파트너가 Azure, System Center 및 Windows Server의 하이브리드 클라우드 플랫폼 및 관리 기능을 활용하여 클라우드를 대상으로 하는 작업 부하 설계 및 배포에 대해 설명합니다.

**다중 사이트 데이터 계층** 패턴은 **Foundation** 영역의 일부이며, CPIF 아키텍처 문서에서 광범위하게 설명됩니다.

## 다중 사이트 데이터 계층

다중 사이트 데이터 계층 디자인 패턴은 지리적 경계를 넘어 예측 가능한 성능 및 고가용성을 제공할 수 있는 데이터 계층 서비스를 지원하는데 필요한 Azure 기능과 서비스를 자세히 설명합니다. 이 디자인 패턴의 목적을 위해 데이터 계층은 격리된 방식 또는 다중 계층 응용 프로그램의 일부로 기존의 데이터 플랫폼 서비스를 제공하는 서비스 계층으로 정의됩니다. 이 패턴 내에서 데이터 계층의 부하 분산은 지역 내 및 지역에 걸쳐 모두 로컬로 제공됩니다.

SQL Server 2012와 함께 도입 AlwaysOn 가용성 그룹은 Azure 인프라 서비스에서 완벽하게 지원되는 고가용성 및 재해 복구 기능입니다. Windows Azure 인프라 서비스에서 AlwaysOn 가용성 그룹에 대한 세부 정보 및 공식 지원 발표는 AlwaysOn 가용성 그룹 문서에 있습니다.

이 문서에서는 SQL AlwaysOn 가용성 그룹을 활용하여 Azure에서 다중 사이트 데이터 계층의 아키텍처를 간략하게 설명합니다. 추가 기능 및 재해 복구는 추가 Azure 데이터 센터의 선택적 읽기 전용 보조 노드에 있습니다. Azure에서 SQL AlwaysOn을 사용하면 웹 또는 응용 프로그램 계층에서 사용할 수 있는 고가용성 데이터 계층을 제공합니다.

이 문서는 아키텍처 패턴 및 사례를 중점적으로 수행을 하는 동안, Azure의 AlwaysOn 가용성 그룹의 구성 및 AlwaysOn 가용성 그룹 수신기의 구성을 설명하는 공식 자습서에서 전체 배포 지침을 찾을 수 있습니다.

## 아키텍처 패턴 개요 

이 문서는 가용성 및 중복성을 위해 여러 지리적 위치에서 Microsoft SQL Server에 대한 액세스를 제공하는 패턴을 설명합니다. 중요한 서비스는 데이터 자체에 액세스하는 응용 프로그램이나 웹 계층에 대한 고려 없이 아래와 같이 설명됩니다. 아래 다이어그램은 관련 서비스의 간단한 설명 및 이 패턴의 일부로 사용되는 방법을 보여줍니다.

각 기본 서비스 분야는 다음 다이어그램에서 자세히 설명됩니다.
 
![리소스 및 리소스 그룹 블레이드에서 태그 파트](./media/azure-architectures-cpif-foundation-multi-site-data-tier/overview.png)

##  추가 리소스
[부하 분산된 데이터 계층(pdf)](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-dfb09e41)

## 참고 항목
[CPIF 아키텍처](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-bd1e434a)

[전역 부하 분산된 웹 계층](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-2c3c663a)

[하이브리드 네트워킹](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-5e401f38)

[Azure 검색 계층](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-e581d65d)

[일괄 처리 계층](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-0bc3f8b1)

<!---HONumber=August15_HO6-->