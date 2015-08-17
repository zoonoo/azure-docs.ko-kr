<properties 
	pageTitle="Azure Government 개발자 가이드" 
	description="Azure Government용 응용 프로그램 개발에 대한 지침 및 기능 비교를 제공합니다." 
	services="" 
	documentationCenter="" 
	authors="Joharve2" 
	manager="carolz" 
	editor=""/>

<tags 
	ms.service="multiple" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="azure-government" 
	ms.date="01/21/2014" 
	ms.author="jharve"/>


#  Microsoft Azure Government 개발자 가이드 

<p> Microsoft Azure Government는 Microsoft Azure의 물리적으로 격리되고 네트워크에서 격리된 인스턴스입니다. 이 개발자 가이드는 응용 프로그램 개발자와 관리자가 이러한 Azure의 별도 지역에 대한 작업을 수행하고 조작해야 하는 차이점에 대한 세부 정보를 제공합니다.

<!--Table of contents for topic, the words in brackets must match the heading wording exactly-->


## 항목 내용


+ [개요](#Overview)
+ [개발자에 대한 지침](#Guidance)
+ [Microsoft Azure Government에서 현재 사용할 수 있는 기능](#Features)
+ [끝점 매핑](#Endpoint)
+ [다음 단계](#next)


## <a name="Overview"></a>개요

Microsoft Azure Government는 미국 연방 기관, 주 및 지방 정부와 해당 솔루션 공급자의 보안 및 규정 준수 요구를 처리하는 Microsoft Azure 서비스의 별도 인스턴스입니다. Azure Government는 비 미국 정부 배포에서 물리적 격리 및 네트워크 격리를 제공하며 선별된 미국 시민을 제공합니다.

Microsoft는 클라우드 응용 프로그램을 만들고 배포할 수 있는 다양한 도구를 Microsoft의 글로벌 Microsoft Azure 서비스(“글로벌 서비스") 및 Microsoft Azure Government 서비스에 제공합니다.

응용 프로그램을 만들어 글로벌 서비스가 아니라 Azure Government 서비스에 배포하는 경우 개발자는 두 서비스의 주요 차이점을 알아야 합니다. 특히, 프로그래밍 환경을 설정 및 구성하고, 끝점을 구성하며, 응용 프로그램을 작성하고, 응용 프로그램을 서비스로 Azure Government에 배포하는 경우에 그렇습니다.

이 문서의 정보는 그러한 차이점을 요약하고 [Azure Government](http://www.azure.com/gov "Azure Government") 사이트 및 MSDN의 [Microsoft Azure 기술 라이브러리](http://msdn.microsoft.com/cloud-app-development-msdn "MSDN")에서 제공하는 정보를 보완합니다. 또한 공식 정보는 [Microsoft Azure 보안 센터](http://azure.microsoft.com/support/trust-center/ "Microsoft Azure 보안 센터"), [Azure 설명서 센터](http://azure.microsoft.com/documentation/) 및 [Azure 블로그](http://azure.microsoft.com/blog/ "Azure 블로그") 등의 여러 다른 위치에서도 제공할 수 있습니다.

이 내용은 Microsoft Azure Government에 배포하는 파트너 및 개발자를 위한 것입니다.



## <a name="Guidance"></a>개발자에 대한 지침
현재 사용할 수 있는 대부분의 기술 콘텐츠에서는 응용 프로그램이 Microsoft Azure Government가 아니라 글로벌 서비스용으로 개발되고 있다고 가정하므로 개발자가 Azure Government에서 호스트되도록 개발된 응용 프로그램의 주요 차이점을 알도록 해야 합니다.

- 첫째, 서비스 및 기능 차이점이 있습니다. 즉, 글로벌 서비스의 특정 지역에 있는 특정 기능을 Azure Government에서는 사용하지 못할 수도 있습니다.

- 둘째, Azure Government에서 제공되는 기능의 경우 글로벌 서비스와 구성 차이점이 있습니다. 따라서 샘플 코드, 구성 및 Azure Government 클라우드 서비스 환경 내에서 빌드 및 실행하고 있는지를 확인하는 단계를 검토해야 합니다.


## <a name="Features"></a> Microsoft Azure Government에서 현재 사용할 수 있는 기능
현재 Azure Government에는 미국 정부 아이오와 및 미국 정부 버지니아 지역에서 사용할 수 있는 다음과 같은 서비스가 포함되어 있습니다.

- 가상 컴퓨터
- 클라우드 서비스
- 저장소
- Active Directory
- 스케줄러
- 가상 네트워킹
- SQL 데이터베이스

기타 서비스도 사용할 수 있으며, 계속해서 서비스가 더 추가될 예정입니다. 서비스의 최신 목록은 사용 가능한 각 지역과 해당 서비스가 강조 표시되어 있는 [지역 페이지](http://azure.microsoft.com/regions/#services)를 참조하세요.

현재 미국 정부 아이오와 및 미국 정부 버지니아는 Azure Government를 지원하는 데이터 센터입니다. 현재 사용 가능한 데이터 센터 및 서비스에 대해서는 위의 지역 페이지를 참조하세요.

## <a name="Endpoint"></a>끝점 매핑

공용 Microsoft Azure 및 SQL 데이터베이스 끝점을 Azure Government 특정 끝점에 매핑할 경우 다음 표를 참조하세요.


서비스 유형|Azure 공용|Azure Government
---|---|---
Azure Government 홈|windowsazure.com|microsoftazure.us
관리 포털|manage.windowsazure.com|manage.windowsazure.us
일반|*.windows.net|*.usgovcloudapi.net
코어|*.core.windows.net|*.core.usgovcloudapi.net
계산|*.cloudapp.net|*.usgovcloudapp.net
Blob 저장소|*.blob.core.windows.net| *.blob.core.usgovcloudapi.net Queue Storage|*.queue.core.windows.net|*.queue.core.usgovcloudapi.net
테이블 저장소|*.table.core.windows.net|*.table.core.usgovcloudapi.net
서비스 관리|management.core.windows.net|management.core.usgovcloudapi.net
SQL 데이터베이스|*.database.windows.net|*.database.usgovcloudapi.net

## <a name="next"></a>다음 단계
Azure Government에 대한 자세한 내용과 조직이 액세스하기 위한 자격을 갖추는 방법은 <A href="http://azure.com/gov">http://www.azure.com/gov</a>를 참조하십시오.

<!--Anchors-->



<!-- Images. -->

[1]: ./media/azure-government-developer-guide/publisherguide.png


<!--Link references-->
[Link 1 to another azure.microsoft.com documentation topic]: virtual-machines-windows-tutorial.md
[Link 2 to another azure.microsoft.com documentation topic]: web-sites-custom-domain-name.md
[Link 3 to another azure.microsoft.com documentation topic]: storage-whatis-account.md

<!---HONumber=August15_HO6-->