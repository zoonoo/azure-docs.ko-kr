<properties 
   pageTitle="전역 부하 분산된 웹 계층(Azure 아키텍처 패턴)" 
   description="전역 부하 분산된 웹 계층 패턴은 Foundation 영역의 일부이며, CPIF 아키텍처 문서에서 광범위하게 설명됩니다." 
   services="" 
   documentationCenter="" 
   authors="arynes" 
   manager="fredhar" 
   editor=""/>

<tags
   ms.service="multiple"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple" 
   ms.date="03/25/2015"
   ms.author="arynes"/>

# 전역 부하 분산된 웹 계층(Azure 아키텍처 패턴)

[클라우드 플랫폼 통합 프레임워크(CPIF)](azure-architectures-cpif-overview.md)는 Microsoft 클라우드 솔루션으로 온보딩 응용 프로그램을 통합하는 워크로드 통합 지침을 제공합니다.

CPIF는 조직, 고객 및 파트너가 Azure, System Center 및 Windows Server의 하이브리드 클라우드 플랫폼 및 관리 기능을 활용하여 클라우드를 대상으로 하는 작업 부하 설계 및 배포에 대해 설명합니다.

**전역 부하 분산된 웹 계층** 패턴은 **Foundation** 영역의 일부이며, CPIF 아키텍처 문서에서 광범위하게 설명됩니다.

##  전역 부하 분산된 웹 계층

전역 부하 분산된 웹 계층 디자인 패턴은 지리적 경계를 넘어 예측 가능한 성능 및 고가용성을 제공할 수 있는 웹 계층 서비스를 지원하는데 필요한 Azure 기능과 서비스를 자세히 설명합니다.

이 디자인 패턴의 목적을 위해 웹 계층은 격리된 방식 또는 다중 계층 웹 응용 프로그램의 일부로 기존의 HTTP/HTTPS 콘텐츠나 응용 프로그램 서비스를 제공하는 서비스 계층으로 정의됩니다. 이 패턴 내에서 웹 계층의 부하 분산은 지역 내 및 지역에 걸쳐 모두 로컬로 제공됩니다. 계산 측면에서 Microsoft Azure 가상 컴퓨터, 웹사이트 또는 둘의 조합을 통해 이러한 서비스를 제공할 수 있습니다. 웹 서비스를 제공하는 가상 컴퓨터는 Microsoft Azure 내에서 지원되는 Microsoft Windows 또는 Linux 배포 게스트 운영 체제를 사용하여 콘텐츠를 호스팅할 수 있습니다.


## 아키텍처 패턴 개요 

이 문서는 가용성 및 중복성을 위해 여러 지리적 위치에서 웹 서비스 또는 웹 서버 내용에 대한 액세스를 제공하는 패턴을 설명합니다. 중요한 서비스는 웹 플랫폼 제약 조건 또는 웹 서비스 자체 내 개발 방법론에 대한 고려 없이 아래와 같이 설명됩니다. 가상 컴퓨터에서의 웹 컨텐츠나 서비스를 호스팅하는 이 패턴에는 Azure 지원 운영 체제와 패밀리 사용 및 Azure 웹사이트 사용의 두 가지 변형이 있습니다. 아래 다이어그램은 간단한 관련 서비스 및 가상 컴퓨터의 예제를 사용하여 이 패턴의 일부로 사용되는 방법을 보여줍니다.

##  추가 리소스
[전역 부하 분산된 웹 계층(pdf)](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-2c3c663a)

## 참고 항목
[CPIF 아키텍처](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-bd1e434a)

[부하 분산된 데이터 계층](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-dfb09e41)

[하이브리드 네트워킹](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-5e401f38)

[Azure 검색 계층](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-e581d65d)

[일괄 처리 계층](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-0bc3f8b1)

<!---HONumber=Oct15_HO4-->