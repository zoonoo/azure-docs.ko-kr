<properties 
   pageTitle="오프 사이트 일괄 처리 계층(Azure 아키텍처 패턴)" 
   description="오프 사이트 일괄 처리 계층 패턴은 Infrastructure 영역의 일부이며 CPIF 아키텍처 문서에서 광범위하게 설명됩니다." 
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

# 오프 사이트 일괄 처리 계층(Azure 아키텍처 패턴)

[클라우드 플랫폼 통합 프레임워크(CPIF)](azure-architectures-cpif-overview.md)는 Microsoft 클라우드 솔루션으로 온보딩 응용 프로그램을 통합하는 워크로드 통합 지침을 제공합니다.

CPIF는 조직, 고객 및 파트너가 Azure, System Center 및 Windows Server의 하이브리드 클라우드 플랫폼 및 관리 기능을 활용하여 클라우드를 대상으로 하는 작업 부하 설계 및 배포에 대해 설명합니다.

**오프 사이트 일괄 처리 계층** 패턴은 **Infrastructure** 영역의 일부이며 CPIF 아키텍처 문서에서 광범위하게 설명됩니다.

##  오프 사이트 일괄 처리 계층

오프 사이트 일괄 처리 계층 디자인 패턴은 모두 오류를 허용하고 확장 가능한 백엔드 데이터 처리를 제공하기 위한 Azure 기능 및 서비스를 자세히 설명합니다. 이러한 서비스는 현재 모든 Azure 데이터 센터에 배포할 수 있는 Azure의 클라우드 서비스에서 작업자 역할로 실현됩니다.

일괄 처리 작업은 일반적으로 사용자 인터페이스를 제공하지 않거나 거의 제공하지 않는다는 점에서 고유합니다. 이러한 유형의 작업 온-프레미스의 예로 Windows 서버에서 실행 되는 Windows 서비스를 들 수 있습니다. 클라우드 환경에서 이 유형의 작업 부하를 고려할 때 실제로 필요한 것이 계산, 저장소 및 네트워크 연결이면, 작업 부하를 실행하는 데 전체 서버를 배포하는 것은 낭비입니다. 작업자 역할은 Azure에서 이 작업의 구현입니다.

Azure에서 실행되는 일괄 처리 작업은 리소스에 연결하는 작업이라고 정의하면 일부 비즈니스 논리(컴퓨팅)을 제공하고 일부 출력을 제공합니다. 입력 및 출력 리소스는 사용자가 정의하며 플랫 파일, Azure blob 저장소의 blob, NoSQL 데이터베이스 또는 관계형 데이터베이스까지입니다.

비즈니스 논리는 일반적으로 .NET 라이브러리에서 필요한 비즈니스 논리를 정의하여 Azure 작업자 역할에서 구현됩니다. Azure로의 작업자 역할 배포는 간단한 작업이지만, 오류를 허용하고 확장 가능한 작업자 역할을 배포하려면 Azure 내에서 서비스가 실행되고 유지되는 방법을 고려하는 디자인이 필요합니다. 이 패턴에서는 이 요구 사항을 고려하고 이를 구현할 수 있는 방법을 설명하는 디자인에 대해 자세히 설명합니다.

## 아키텍처 패턴 개요 

이 문서에서는 Azure의 클라우드 서비스 내에 포함된 작업자 역할 인스턴스를 활용하여 오프 사이트 일괄 처리에 대한 패턴을 설명합니다. 이 디자인에 중요한 구성 요소는 다음과 같습니다. 이 다이어그램에서는 오류를 허용하기 위해 필요한 최소 인스턴스를 보여줍니다. 서비스의 성능을 향상시키기 위해 추가 인스턴스를 배포할 수 있습니다. 또한 자동 크기 조정은 기간 또는 추가 서버 메트릭 기준의 인스턴스 크기를 조정하도록 설정할 수 있습니다.

##  추가 리소스
[일괄 처리 계층 (pdf)](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-0bc3f8b1)

## 참고 항목
[CPIF 아키텍처](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-bd1e434a)

[전역 부하 분산된 웹 계층](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-2c3c663a)

[부하 분산된 데이터 계층](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-dfb09e41)

[하이브리드 네트워킹](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-5e401f38)

[Azure 검색 계층](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-e581d65d)

<!---HONumber=July15_HO4-->