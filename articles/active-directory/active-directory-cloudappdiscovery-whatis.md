<properties 
	pageTitle="조직 내에서 사용되고 있는 허용되지 않은 클라우드 앱을 검색하는 방법" 
	description="이 항목에서는 클라우드 앱 검색이 무엇이며 왜 사용해야 하는지를 설명합니다." 
	services="active-directory" 
	documentationCenter="" 
	authors="markusvi" 
	manager="swadhwa" 
	editor="lisatoft"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/23/2015" 
	ms.author="markusvi"/>

# 조직 내에서 사용되고 있는 허용되지 않은 클라우드 앱을 검색하는 방법

오늘날 기업에서 IT 부서가 사용자들이 일을 위해 사용하고 있는 모든 클라우드 앱에 대해 인지하지 못하는 경우가 가끔 있습니다. 이 결과로 관리자는 기업 데이터에 대한 권한 없는 액세스, 데이터 누출 가능성 및 응용 프로그램에 내재된 다른 보안 위험에 대해 염려하게 됩니다. 어떤 앱이 몇 개나 사용되고 있는지 모르기 때문에 이러한 위험에 대응하기 위한 계획을 세우는 것조차 어려워 보입니다.

클라우드 앱 검색을 사용하여 이러한 문제를 해결할 수 있습니다. 클라우드 앱 검색은 Azure Active Directory의 프리미엄 기능으로 조직의 직원들이 사용하는 클라우드 응용 프로그램을 검색할 수 있도록 합니다.


**클라우드 앱 검색으로 다음을 할 수 있습니다.**

* 사용되고 있는 응용 프로그램을 검색하고 사용자 수, 트래픽 볼륨 또는 응용 프로그램에 대한 웹 요청 수로 사용 현황을 측정합니다. 
* 응용 프로그램을 사용하는 사용자를 식별합니다. 
* 추가 오프라인 분석을 위해 데이터를 내보냅니다. 
* 응용 프로그램의 우선 순위를 정하여 IT 제어에 두고 응용 프로그램을 쉽게 통합하여 Single Sign-on과 사용자 관리가 가능하게 합니다. 

클라우드 앱 검색으로 사용자 환경에서 컴퓨터를 실행하는 에이전트에 의한 데이터 검색이 가능해졌습니다. 에이전트에 의해 캡처되는 앱 사용 현황 정보는 암호화 채널로 클라우드 앱 검색 서비스에 보안 전송됩니다. 클라우드 앱 검색 서비스는 데이터를 평가하고 사용자 환경 분석에 사용할 수 있는 보고서를 생성합니다.


<center>![클라우드 앱 검색의 작동 원리](./media/active-directory-cloudappdiscovery/cad01.png)</center>

##다음 단계


* 클라우드 앱 검색 작동 원리를 알아보려면 [클라우드 앱 검색 시작하기](http://social.technet.microsoft.com/wiki/contents/articles/30962.getting-started-with-cloud-app-discovery.aspx)를 참조하세요. 
* 보안 및 개인정보 취급 방침 고려 사항은 [클라우드 앱 검색 보안 및 개인정보 취급 방침 고려 사항](active-directory-cloudappdiscovery-security-and-privacy-considerations.md)을 참조하세요. 
* 기업 환경에서의 클라우드 앱 검색 에이전트 배포에 대한 정보: 
 * Active Directory 그룹 정책 관리는 [클라우드 앱 검색 그룹 정책 배포 가이드](http://social.technet.microsoft.com/wiki/contents/articles/30965.cloud-app-discovery-group-policy-deployment-guide.aspx)를 참조하세요. 
 * Microsoft System Center 구성 관리자는 [클라우드 앱 검색 System Center 배포 가이드](http://social.technet.microsoft.com/wiki/contents/articles/30968.cloud-app-discovery-system-center-deployment-guide.aspx)를 참조하세요. 
 * 사용자 지정 포트를 사용한 프록시 서버는 [사용자 지정 포트를 사용한 프록시에 대한 클라우드 앱 검색 레지스트리 설정](active-directory-cloudappdiscovery-registry-settings-for-proxy-services.md)을 참조하세요. 





**추가 리소스**


* [클라우드 앱 검색 - 에이전트 Changelog](http://social.technet.microsoft.com/wiki/contents/articles/24616.cloud-app-discovery-agent-changelog.aspx)
* [클라우드 앱 검색 - 질문과 대답](http://social.technet.microsoft.com/wiki/contents/articles/24037.cloud-app-discovery-frequently-asked-questions.aspx)

<!---HONumber=Oct15_HO3-->