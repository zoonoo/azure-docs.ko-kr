<properties 
	pageTitle="Operational Insights 보안" 
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





<h1 id="">Operational Insights 보안</h1>


Microsoft는 조직의 IT 인프라 관리에 유용한 소프트웨어 및 서비스를 제공하는 동시에 개인 정보와 데이터를 보호하기 위해 노력하고 있습니다. 데이터를 타인에게 맡길 때 해당 신뢰에는 엄격한 보안이 필요함을 알고 있습니다. Microsoft는 코딩부터 서비스에 이르기까지 엄격한 규정 준수 및 보안 지침을 따릅니다.

데이터 보안 및 보호는 Microsoft의 최우선 과제입니다. 보안 정책을 포함하여 다음 정보와 관련된 질문, 제안 사항 또는 문제가 있을 경우 <a href="mailto:scdata@microsoft.com" target="_blank">scdata@microsoft.com</a>으로 이동하세요.

이 문서에서는 Microsoft Azure Operational Insights에서 데이터를 수집, 처리 및 보호하는 방법에 대해 설명합니다. 에이전트를 사용하여 웹 서비스에 직접 연결하거나 System Center Operations Manager를 사용하여 Operational Insights 서비스에 대한 운영 데이터를 수집할 수 있습니다. 수집된 데이터는 인터넷을 통해 Microsoft Azure에 호스트된 Operational Insights 서비스로 전송됩니다.

Operational Insights 서비스는 다음 방법을 사용하여 데이터를 안전하게 관리합니다.

**데이터 분리:** Operational Insights 서비스 전반에 걸쳐 고객 데이터가 논리적으로 각 구성 요소에 별도로 유지됩니다. 모든 데이터에 조직별 태그가 지정됩니다. 이 태그 지정은 데이터 수명 주기 동안 유지되며 각 서비스 계층에 적용됩니다. 

각 고객에게 장기 데이터를 저장하는 전용 Azure Blob이 있습니다. Blob은 90일마다 변경되는 고객별 고유 키로 암호화됩니다.

**데이터 보존:** 각 인텔리전스 팩에 대해 집계된 메트릭이 Microsoft Azure에 호스트된 SQL 데이터베이스에 저장됩니다. 이 데이터는 390일 동안 저장됩니다. 인덱싱된 검색 데이터는 영구 제거되기 전에 평균 10일 동안 저장됩니다. 각 데이터 형식에 대한 상한인 2천만 개에 더 빨리 도달할 경우 Operational Insights에서 10일보다 일찍 데이터를 영구 제거합니다. 10일 후까지 데이터 한도에 도달하지 않을 경우 Operational Insights에서 한도에 도달할 때까지 기다린 후 영구 제거합니다.

**물리적 보안:** Operational Insights 서비스는 Microsoft 담당자에 의해 운영되며 모든 활동이 로깅되고 감사될 수 있습니다. Operational Insights 서비스는 완전히 Azure에서 실행되며 Azure Common Engineering Criteria를 준수합니다.  <a href="http://download.microsoft.com/download/6/0/2/6028B1AE-4AEE-46CE-9187-641DA97FC1EE/Windows%20Azure%20Security%20Overview%20v1.01.pdf" target="_blank">Microsoft Azure 보안 개요</a>으로 이동하세요.

**규정 준수 및 인증:** Operational Insights 소프트웨어 개발 및 서비스 팀은 Operational Insights 서비스를 일반적으로 사용할 수 있기 전에 ISO를 비롯한 다양한 인증을 받기 위해 Microsoft 법률 및 규정 준수 팀은 물론 다른 업계 파트너와도 적극적으로 협력하고 있습니다.

현재 다음 보안 표준을 충족합니다.

- Windows Common Engineering Criteria
- Microsoft Trustworthy Computing Certification



<!--HONumber=45--> 
