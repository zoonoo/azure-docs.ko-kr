<properties 
	pageTitle="클라우드에서 온-프레미스 ID 인프라 모니터링" 
	description="사용되는 항목 및 이유를 설명하는 Azure AD Connect 상태 페이지입니다." 
	services="active-directory" 
	documentationCenter="" 
	authors="billmath" 
	manager="swadhwa" 
	editor="curtand"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/28/2015" 
	ms.author="billmath"/>

# 클라우드에서 온-프레미스 ID 인프라 모니터링

Azure AD Connect Health를 사용하면 온-프레미스 ID 인프라를 모니터링하고 통찰력을 얻을 수 있습니다. 경고, 성능, 사용 패턴, 구성 설정을 볼 수 있는 기능을 제공하며, Office 365에 대한 신뢰할 수 있는 연결을 유지할 수 있습니다. 이 대상 서버에 설치된 에이전트를 사용하여 수행됩니다. 자세한 내용은 [MSDN의Azure AD Connect Health](https://msdn.microsoft.com/library/azure/dn906722.aspx)를 참조하세요.

![Azure AD Connect Health 정의](./media/active-directory-aadconnect-health/aadconnecthealth2.png)


이 정보는 Azure AD Connect Health 포털에 모두 표시됩니다. Azure AD Connect Health 포털을 사용하여 경고, 성능 모니터링 및 사용 현황 분석을 볼 수 있습니다. 이 정보는 필요한 정보를 찾느냐 시간을 낭비할 필요가 없도록 사용하기 쉽게 한 곳에 표시됩니다.

![Azure AD Connect Health 정의](./media/active-directory-aadconnect-health/usage.png)

Azure AD Connect Health에 대한 이후 업데이트는 추가 모니터링 및 다른 ID 구성 요소 및 Azure AD Connect Sync services와 같은 서비스에 대한 통찰력을 포함합니다. ID의 렌즈를 통한 단일 대시보드를 제공하고 사용자가 작업을 완료하는 기능을 향상시킬 수 있도록 하는 훨씬 더 강력하고 정상적이며 통합된 환경을 활용합니다.

추가 정보는 [MSDN의 Azure AD Connect Health](https://msdn.microsoft.com/library/azure/dn906722.aspx)를 참조하세요.


![Azure AD Connect Health 정의](./media/active-directory-aadconnect-health/logo1.png)




## Azure AD Connect Health를 사용하는 이유

Azure AD와 온-프레미스 디렉터리를 통합하면 온-프레미스 및 클라우드 리소스 모두에 액세스하기 위한 일반적인 ID를 제공하므로 사용자가 더 생산성을 높일 수 있습니다. 그러나 이 통합을 통해 사용자가 모든 장치에서 온-프레미스와 클라우드 모두의 리소스에 안정적으로 액세스할 수 있도록 이 환경이 정상적인지 확인해야 하는 문제가 있습니다. Azure AD Connect Health는 온-프레미스 ID 인프라에 대해 쉽게 모니터링하고 통찰력을 얻을 수 있는 클라우드 기반 접근 방식을 제공하여 Office 365 또는 다른 Azure AD 응용 프로그램에 액세스 하는데 사용됩니다. 각 온-프레미스 ID 서버에 에이전트를 설치하는 것만큼 간단합니다.

AD FS에 대한 Azure AD Connect Health는 Windows Server 2008/2008 R2에서 AD FS 2.0, Windows Server 2012/2012R2에서 AD FS를 지원합니다. 엑스트라넷 액세스에 대한 인증 지원을 제공하는 AD FS 프록시 또는 웹 응용 프로그램 프록시 서버도 포함됩니다. AD FS에 대한 Azure AD Connect Health는 다음과 같은 주요 기능 집합을 제공합니다.

- Azure AD를 포함하여 AD FS 보호된 응용 프로그램에 대한 안정적인 액세스에 대 한 경고를 보고 작업 수행
- 중요한 경고에 대한 전자 메일 알림
- 용량 계획을 확인하려면 성능 데이터 보기
- 잘못된 부분을 확인하거나 용량 계획에 대한 기준을 설정하여 AD FS 로그인 패턴의 상세 보기



----------------------------------------------------------------------------------------------------------
## Azure AD Connect Health 에이전트 다운로드

Azure AD Connect Health를 사용하여 시작하려면 [Azure AD Connect Health 에이전트 다운로드](http://go.microsoft.com/fwlink/?LinkID=518973)를 사용하여 최신 버전을 다운로드할 수 있습니다. 에이전트를 설치하기 전에 Marketplace에서 추가 확인합니다.

----------------------------------------------------------------------------------------------------------





**추가 리소스**

* [MSDN의 Azure AD Connect Health](https://msdn.microsoft.com/library/azure/dn906722.aspx)


 

<!---HONumber=62-->