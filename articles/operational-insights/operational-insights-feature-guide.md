<properties 
	pageTitle="Operational Insights 기능 가이드" 
	description="Operational Insights는 IT 관리자가 온-프레미스 및 클라우드 환경 전반을 심층적으로 파악할 수 있게 해주는 분석 서비스입니다. 실시간 및 과거 컴퓨터 데이터를 조작하여 사용자 지정 통찰력을 신속하게 개발할 수 있게 해주며 데이터 분석을 위해 Microsoft와 커뮤니티에서 개발한 패턴을 제공합니다." 
	services="operational-insights" 
	documentationCenter="" 
	authors="bandersmsft" 
	manager="jwhit" 
	editor=""/>

<tags 
	ms.service="operational-insights" 
	ms.workload="appservices" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/06/2014" 
	ms.author="banders"/>

<h1>Operational Insights 기능 가이드</h1>

이 가이드는 Operational Insights를 통해 해결할 수 있는 문제, Operational Insights 환경의 구성 요소 및 Operational Insights 계정을 만들고 서비스에 로그인하는 방법을 이해하는 데 도움이 됩니다.

<h2 id="whatisaad">Operational Insights 정의</h2>

Operational Insights는 IT 관리자가 온-프레미스 및 클라우드 환경 전반을 심층적으로 파악할 수 있게 해주는 분석 서비스입니다. 실시간 및 과거 컴퓨터 데이터를 조작하여 사용자 지정 통찰력을 신속하게 개발할 수 있게 해주며 데이터 분석을 위해 Microsoft와 커뮤니티에서 개발한 패턴을 제공합니다.

Operational Insights를 사용하면 컴퓨터 데이터를 운영 인텔리전스로 변환할 수 있습니다. 특히 다음 작업을 수행할 수 있습니다.

- 서버 인프라의 용량 관리
- 시스템 업데이트로 서버 업데이트
- 서버 로그 파일의 데이터 간 관계 이해
- 맬웨어 방지 프로그램을 사용하여 서버 보호
- 보안 보증을 사용하여 보안 위험 관리
- 서버 전체의 구성 변경 내용 추적
- SQL Server 최적화
- 이벤트 및 IIS 로그 검색
- Microsoft Azure IaaS 가상 컴퓨터에 에이전트 설치
- Operations Manager 없이 Operational Insights 사용  

<h2 id="">Operational Insights 환경</h2>

Operational Insights 환경은 다음 구성 요소로 이루어져 있습니다.

- Azure 계정에 대한 컨테이너인 Microsoft Azure 호스티드 작업 영역
- 클라우드에 호스트된 Operational Insights 웹 서비스
- 웹 서비스에 직접 연결하는 개별 에이전트
- 또는 System Center Operations Manager에 연결된 서비스(필수 아님)




System Center Advisor라는 이전 버전의 Operational Insights를 사용한 경우 Advisor 소프트웨어가 로컬 환경에 설치되어 있을 수 있습니다. 그러나 Advisor 소프트웨어는 Operational Insights에서 지원되지 않습니다.

Operational Insights 소프트웨어를 Operations Manager 서비스로 사용하는 경우 하나 이상의 관리 그룹과 관리 그룹당 하나 이상의 에이전트로 구성됩니다. Operations Manager 에이전트는 서버에서 데이터를 수집하고 인텔리전스 팩(System Center Operations Manager의 관리 팩과 유사함)을 사용하여 분석합니다. 분석된 데이터는 다른 서버를 무시하고 프록시 서버를 사용하여 Operations Manager에서 Operational Insights 웹 서비스로 주기적으로 전송되므로 추가 부하가 발생하지 않습니다.

마찬가지로, 개별 컴퓨터에 설치된 에이전트가 웹 서비스에 직접 연결하여 처리를 위해 수집된 데이터를 보낼 수 있습니다.

각 인텔리전스 팩의 데이터가 분석되어 Operational Insights 포털에 표시됩니다. 모든 경고 및 관련된 수정 지침, 구성 평가, 인프라 용량 문제, 시스템 업데이트 상태, 맬웨어 방지 프로그램 경고, 로그 데이터를 볼 수 있습니다. 자세한 검색을 수행할 수도 있습니다.

<h2 id="">Operational Insights 계정 만들기 및 로그인</h2>

다음 정보를 사용하여 Microsoft Azure Operational Insights에서 사용할 계정을 설정하고 Operational Insights에 로그인합니다.



<h3>조직 계정 또는 Microsoft 계정 만들기</h3>

조직 계정을 만들려는 경우 <a href="http://go.microsoft.com/fwlink/?LinkId=396866" target="_blank">조직을 위한 Microsoft 계정</a>으로 이동하세요. 그런 다음 <a href="http://aka.ms/Mr1dtz" target="_blank">Microsoft Azure Active Directory</a>으로 이동하세요.


Microsoft 계정(이전의 "Windows Live ID")을 만들려는 경우 <a href="http://go.microsoft.com/fwlink/?LinkId=396868" target="_blank">등록 - Microsoft 계정</a>으로 이동하세요. Microsoft 계정을 사용하면 Operational Insights에 대한 액세스 권한이 자동으로 부여됩니다.


<h3>Operational Insights에 로그인</h3>

1. 로그인하려면 <a href="preview.opinsights.azure.com" target="_blank">Microsoft Azure Operational Insights</a>  로 이동하여 페이지 맨 위에 있는 로그인을 선택합니다.
2. **Microsoft 계정** 또는 **조직 계정**을 선택하고 자격 증명을 사용하여 **로그인**합니다.
3. 메시지가 표시되면 로그인 정보에 연결할 Operational Insights 계정을 만듭니다.
4. 사용할 Operational Insights 버전을 선택하라는 메시지가 표시되면 **Try Preview(Preview 사용해 보기)**를 선택합니다.
5. 메시지가 표시되면 초대 코드를 입력하고 **적용**을 클릭하여 미리 보기에 가입합니다. 초대 코드가 없는 경우 화면 맨 아래에서 정보를 입력하여 초대 코드를 받으세요.

<!--HONumber=45--> 
 