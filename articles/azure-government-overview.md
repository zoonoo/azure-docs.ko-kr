<properties 
	pageTitle="Azure 정부 개요" 
	description="이 문서에서는 Azure 정부 클라우드 기능 신뢰할 수 있는 디자인 및 규정 준수 연방 적용할, 상태 및 지방 정부 조직 및 해당 파트너를 지원 하기 위해 사용 되는 보안에 대 한 개요를 제공 합니다. " 
	services="azure-government" 
	documentationCenter="" 
	authors="joharve2" 
	manager="required"/>

<tags 
	ms.service="multiple" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="azure-government" 
	ms.date="1/9/2014" 
	ms.author="jharve"/>

#  Microsoft Azure 정부 개요 

<p> Microsoft Azure Government는 Microsoft Azure의 물리적으로 격리되고 네트워크에서 격리된 인스턴스입니다.  이 개발자 가이드는 응용 프로그램 개발자와 관리자가 이러한 Azure의 별도 지역에 대한 작업을 수행하고 조작해야 하는 차이점에 대한 세부 정보를 제공합니다.


## 항목 내용


+ [개요](#Overview)
+ [Microsoft Azure Government에서 현재 사용할 수 있는 기능](#Features)
+ [다음 단계](#next)


## <a name="Overview"></a>개요

Azure 정부는 미국 정부 조직에 대 한 속도, 확장성, 보안, 규정 준수 및 경제성을 필요로 하는 전략적 정부 시나리오를 지원 하도록 설계 하는 정부 커뮤니티 클라우드 (GCC).   Microsoft의 광범위 한 경험을 바탕으로 소프트웨어, 보안, 규정 준수 및 컨트롤의 공용 Azure, Office 365, O365 GCC 등 Microsoft CRM Online과 같은 다른 Microsoft 클라우드 제품에 따라 개발 되었습니다. 

또한 Azure 정부 더 높은 수준의 보안을 충족 하도록 디자인 되었습니다 및 규정 준수 필요한 중요 한, 전용, 공공 부문 작업용 발견 미국 연방 위험 및 권한 부여 관리 프로그램 (FedRAMP) 부서의 방어 엔터프라이즈 클라우드 Service Broker (ECSB), 범죄자 Justice 정보 서비스 (CJIS)와 같은 규정에 보안 정책 및 Health Insurance Portability and Accountability Act (HIPAA).     

다음은 Azure 정부 클라우드 인프라, 패브릭, 서비스 및 정부 조직 팀의 목표에 맞게 하이브리드 클라우드 솔루션을 구축 하는데 도움이 되는 프레임 워크에 대 한 요약 보기입니다.  일부 서비스 아래의 미리 보기에서 사용할 수 있는이 참조 하십시오는 [영역 페이지](http://azure.microsoft.com/regions/#services) 날짜까지 가장 일반적으로 사용할 수 있는 서비스 나열 됩니다.

![][2]

Azure 정부 인프라-as a Service (IaaS) 및 플랫폼-as a Service (PaaS)의 핵심 구성 요소를 포함합니다.  인프라, 네트워크, 저장소, 데이터 관리, id 관리 및 다른 많은 서비스 포함 됩니다.  Azure 정부 지리적 동기 데이터 복제 및 자동 크기 조정 같은 공용 Azure 고객을 충분히 활용 하는 동일한 뛰어난 기능을 지원 합니다. 두 분야의 선두 기업으로 Microsoft 식별 되어 <a href="https://www.gartner.com/doc/2575715/magic-quadrant-cloud-infrastructure-service" target="_new">IaaS</a> 및 <a href="https://www.gartner.com/doc/2645317/magic-quadrant-enterprise-application-platform" target="_new">PaaS<a/>선행 업계 분석가 따르면 여 합니다.

강력한 서비스 및 공용 Azure의 기능을 제공 하는 것 외에도 Azure 정부에서는 다양을 한 보장 미국 정부 엔터티를 제공 하 여 해당 데이터를 보호 하기 위한 기능을 제공 합니다.

- **물리적 및 네트워크 격리 인스턴스** - The Azure 정부 환경 공용 및 전용 Microsoft Azure에서 완전히 별개의 인스턴스를 정규화 된 미국 정부 조직 및 솔루션 공급자가 사용 됩니다.

- **보안, 개인정보 보호 및 규정 준수** -Microsoft의 강력한 보안, 개인정보 보호 및 규정 준수 컨트롤 프레임 워크를 구현 했습니다 plus 높을수록에 맞게 추가 엄격한 제어 수준 요구 사항을 ECSB 영향 수준 및 CJIS에서 찾을 수 있습니다.

- **데이터 저장소** - The Azure 정부 환경에서는 유지 관리 2 데이터 센터 500 마일 이상 떨어져 있습니다. 대륙 United States (CONUS) 데이터 센터 내 모든 관리 되는 고객 데이터에 저장 된

- **미국 담당자** - 모든 Azure 정부 연산자와 관리자는 스크린 된 미국 시민 합니다.

- **Id 관리** - Azure Active Directory의 별도 인스턴스가 Azure 정부 환경 내에서 Id 관리 합니다.

- **규정 준수** - Microsoft 지속적으로 투자를 감행 하 충족 엄격 하 게 유지 하 고 상태 연방, 변경 및 미국 정부에 대 한 FedRAMP, CJIS, ECSB, 및 HIPAA와 같은 로컬 규정 준수 요구 사항을 클라우드 솔루션입니다.

- **통합 클라우드** - Azure 정부 통합된 환경을 제공 O365 정부와 클라우드 서비스 및 1TB OneDrive 저장 공간 등의 향상 된 서비스에 걸쳐 single sign-on에 대 한 허용 합니다.

Azure 정부는 또한 조직을 기존 기술 투자를 유지 관리 하 고 클라우드 서비스의 이점을 얻을 수 있습니다.  이후 Azure 정부는 상호 운용 가능한 클라우드 플랫폼, 제품 및 기술을 조직에서는 응용 프로그램을 구축 하는 것 처음부터 엽니다.  기관 도구, 서비스, 운영 체제, 아키텍처 및 클라우드 솔루션에 대 한 Windows, Linux, Oracle, SharePoint,.NET, Java, PHP 및 Node.js를 포함 하 여 프레임 워크를 선택할 수 있습니다. 정부 Azure 플랫폼의 유연성, 새로운 형태의 에이전시 간 공동 작업, 응용 프로그램 개발 및 통합을 허용합니다.  

클라우드 서비스에 관심이 미국 정부 조직 Azure 정부 엄청난 눈금과 변하는 요구에 맞게 엄격한 보안 사례를 제공 하는지 확신할 수 있습니다. 







## <a name="Features"></a>Microsoft Azure 정부에서 현재 사용 가능한 기능
현재 Azure Government에는 미국 정부 아이오와 및 미국 정부 버지니아 지역에서 사용할 수 있는 다음과 같은 서비스가 포함되어 있습니다.

- 가상 컴퓨터
- 클라우드 서비스
- 저장소
- Active Directory
- 스케줄러
- 가상 네트워킹
- SQL 데이터베이스

기타 서비스도 사용할 수 있으며, 계속해서 서비스가 더 추가될 예정입니다.  서비스의 최신 목록을 참조 하십시오는 [영역 페이지](http://azure.microsoft.com/regions/#services) 사용 가능한 각 지역 및 해당 서비스를 강조 표시 됩니다.  

현재 미국 정부 아이오와 및 미국 정부 버지니아는 Azure Government를 지원하는 데이터 센터입니다.  현재 사용 가능한 데이터 센터 및 서비스에 대해서는 위의 지역 페이지를 참조하세요.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged -->

## <a name="next"></a>다음 단계

점점 Azure 정부에 대 한 교육에 관심이 있는 경우 아래 링크 중 일부 활용 하십시오.

- **<A href="http://azure.com/gov">가져오기 및 Azure 정부에 액세스</a>**

- **<A href="/azure-government-developer-guide">Azure 정부 개발자 가이드</a>**

<!--- **<A href="/azure-government-service-description">Azure Government Service Descriptions</a>**-->




<!-- Images. -->

[1]: ./media/azure-government-developer-guide/publisherguide.png
[2]: ./media/azure-government-overview/azure-gov-overview.jpg

<!--Link references-->
[다른 azure.microsoft.com 설명서 항목의 링크 1]: ../virtual-machines-windows-tutorial/
[다른 azure.microsoft.com 설명서 항목으로 링크 2]: ../web-sites-custom-domain-name/
[다른 azure.microsoft.com 설명서 항목으로 링크 3]: ../storage-whatis-account/

<!--HONumber=47-->
