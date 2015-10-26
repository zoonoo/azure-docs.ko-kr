<properties 
   pageTitle="클라우드 플랫폼 통합 프레임워크 | Microsoft Azure" 
   description="클라우드 플랫폼 통합 프레임워크는 Microsoft Azure에 대한 아키텍처 패턴을 구성하는 Microsoft 클라우드 솔루션으로 온보딩 응용 프로그램을 통합하는 작업 통합 가이드를 제공합니다." 
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


# 클라우드 플랫폼 통합 프레임워크(Azure 아키텍처 패턴)

클라우드 플랫폼 통합 프레임워크(CPIF)는 Microsoft 클라우드 솔루션으로 온보딩 응용 프로그램을 통합하는 작업 통합 지침을 제공합니다.

CPIF는 조직, 고객 및 파트너가 Azure, System Center 및 Windows Server의 하이브리드 클라우드 플랫폼 및 관리 기능을 활용하여 클라우드를 대상으로 하는 작업 부하 설계 및 배포에 대해 설명합니다. CPIF 도메인은 다음 기능으로 분해됩니다.

![리소스 및 리소스 그룹 블레이드에서 태그 파트](./media/azure-architecture-cpif-overview/overview.png)

##  CPIF 아키텍처

두 공용 및 사설 클라우드 환경 모두는 복잡한 작업의 실행을 지원하기 위해 공통 요소를 제공합니다. 이러한 아키텍처는 기존의 온-프레미스 물리적 및 가상화된 환경에서 비교적 잘 이해되지만, Microsoft Azure 내 구문을 사용하려면 공용 클라우드 환경 내에 있는 인프라 및 플랫폼 기능을 합리화하도록 추가로 계획해야 합니다. Azure에서 호스팅되는 응용 프로그램 또는 서비스의 개발을 지원하려면 특정 작업 솔루션을 작성하는데 필요한 다양한 구성 요소를 간략하게 설명하는 일련의 패턴이 필요합니다.

이러한 아키텍처 패턴은 다음 범주로 분류됩니다.

- **인프라**– Microsoft Azure는 몇 가지 기본 서비스 및 기능으로 구성되는 인프라-및 서비스로서의 플랫폼 솔루션입니다. 이 서비스는 계산, 저장소 및 네트워크 서비스로 분해되지만 이러한 정의 범위에 속하지 않는 몇몇 기능이 있습니다. 인프라 패턴은 지정된 Azure 구독 내에서 호스팅되는 하나 이상의 솔루션에 지정된 서비스를 제공하는 데 필요한 Microsoft Azure의 특정 기능 영역을 자세히 설명합니다. 
- **Foundation** – Azure 내에 다중 계층 응용 프로그램 또는 서비스를 작성할 때 여러 구성 요소를 적절한 호스팅 환경을 제공하기 위해 함께 사용해야 합니다. Foundation 패턴은 응용 프로그램 내에서 지정된 계층 기능을 지원하도록 Microsoft Azure에서 하나 이상의 서비스를 구성합니다. 이 앞에서 설명한 인프라 패턴에 표시된 하나 이상의 구성 요소를 사용해야 합니다. 예를 들어 다중 계층 응용 프로그램의 프레젠테이션 계층을 사용하려면 Azure 내에서 계산, 네트워크 및 저장소 기능이 작동되어야 합니다. Foundation 패턴은 지정된 솔루션의 일부로서 다른 패턴과 함께 구성됩니다.
- **솔루션** – 솔루션 패턴은 개발 중인 엔드 응용 프로그램 또는 서비스를 나타내는 인프라 및/또는 foundation 패턴으로 구성됩니다. 복잡한 솔루션은 다른 패턴과 독립적으로 개발되지 않도록 구상합니다. 대신, 앞에서 설명한 각 패턴 범주에 정의된 구성 요소와 인터페이스를 사용해야 합니다.    

## Azure 아키텍처 패턴 개념

Azure 내 솔루션 아키텍처 개발을 지원하려면 일련의 일반적인 시나리오에 패턴 가이드가 제공됩니다. 이러한 시나리오 가이드는 시간이 지남에 따라 생성되며, 가시화 패턴은 다음과 같습니다.

- 전역 부하 분산된 웹 계층 
- 부하 분산된 데이터 계층
- 일괄 처리 계층
- 하이브리드 네트워킹
- Azure 검색 

##  추가 리소스
[CPIF 아키텍처(pdf)](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-bd1e434a)

## 참고 항목
[전역 부하 분산된 웹 계층](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-2c3c663a)

[부하 분산된 데이터 계층](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-dfb09e41)

[일괄 처리 계층](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-0bc3f8b1)

[Azure 네트워킹](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-5e401f38)

[Azure 검색](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-e581d65d)

<!---HONumber=Oct15_HO3-->