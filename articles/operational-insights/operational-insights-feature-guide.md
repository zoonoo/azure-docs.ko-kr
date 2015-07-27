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
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/02/2015"
	ms.author="banders"/>

# Operational Insights 기능 가이드

[AZURE.INCLUDE [operational-insights-note-moms](../../includes/operational-insights-note-moms.md)]

이 문서는 Operational Insights를 통해 해결할 수 있는 문제, Operational Insights 환경의 구성 요소 및 Operational Insights 계정을 만들고 서비스에 로그인하는 방법을 이해하는 데 도움이 됩니다.

## 컴퓨터 데이터 변환

Operational Insights는 IT 관리자가 온-프레미스 및 클라우드 환경 전반을 심층적으로 파악할 수 있게 해주는 분석 서비스입니다. 실시간 및 과거 컴퓨터 데이터를 조작하여 사용자 지정 통찰력을 신속하게 개발할 수 있게 해주며 데이터 분석을 위해 Microsoft와 커뮤니티에서 개발한 패턴을 제공합니다.

Operational Insights를 사용하면 다음 기능으로 컴퓨터 데이터를 운영 인텔리전스로 변환할 수 있습니다.


|**아이콘** | **사용할 기능** | **수행하는 작업**|
|---|---|---|
|![](./media/operational-insights-feature-guide/cap-plan.png) | [용량 계획](operational-insights-capacity.md) | Microsoft Azure Operational Insights의 용량 계획 솔루션을 사용하여 서버 인프라의 용량을 이해할 수 있습니다. |
| ![](./media/operational-insights-feature-guide/update.png) | [시스템 업데이트 평가](operational-insights-updates.md) | 서버 인프라에 누락된 업데이트를 적용할 수 있도록 Microsoft Azure Operational Insights의 시스템 업데이트 솔루션을 사용할 수 있습니다. |
| ![](./media/operational-insights-feature-guide/log-mgt.png) | [로그 관리](operational-insights-search.md) | Operational Insights 전체의 로그 검색에 대해 이벤트 및 IIS 로그를 수집하려면 로그 관리 솔루션을 사용합니다. |
| ![](./media/operational-insights-feature-guide/malware.png) | [맬웨어 평가](operational-insights-antimalware.md) | Microsoft Azure Operational Insights의 맬웨어 방지 솔루션을 사용하여 맬웨어로부터 인프라의 서버를 보호할 수 있습니다. |
| ![](./media/operational-insights-feature-guide/sec-audit.png) | [보안 및 감사](operational-insights-security-audit.md) | 보안 및 감사 솔루션을 사용하여 주의가 필요한 주요 문제에 대한 기본 제공 검색 쿼리를 통해 조직의 IT 보안 상태에 대한 포괄적인 뷰를 가져올 수 있습니다. |
| ![](./media/operational-insights-feature-guide/assessment.png) | [Active Directory 및 SQL 평가](operational-insights-assessment.md) | 평가 솔루션을 사용하여 일정한 간격으로 서버 환경의 위험 및 상태를 평가할 수 있습니다. |
| ![](./media/operational-insights-feature-guide/alert.png) | [경고 관리](operational-insights-alerts.md) | 경고 관리 솔루션을 사용하여 Operations Manager가 모니터링하는 서버의 경고를 관리할 수 있습니다. |


또한 다음을 수행할 수 있습니다.

- **에이전트를 사용하거나 사용하지 않고 또는 System Center Operations Manager를 함께 사용하여 컴퓨터 데이터를 시스템으로 보낼 수 있습니다**. 자세한 내용은 다음을 참조하세요.
	- [System Center Operations Manager에서 Operational Insights에 연결](operational-insights-connect-scom.md)
	- [Operational Insights에 컴퓨터를 직접 연결](operational-insights-direct-agent.md)
	- [Microsoft Azure의 서버에서 데이터 분석](operational-insights-analyze-data-azure.md)
- **위의 모든 작업을 이동 중에 모바일 응용 프로그램으로 수행할 수 있습니다**.
	- Windows Phone 응용 프로그램에 대한 자세한 내용은 [Operational Insights 모바일 앱](http://www.windowsphone.com/ko-kr/store/app/operational-insights/4823b935-83ce-466c-82bb-bd0a3f58d865)을 참조하세요.

## Operational Insights 환경

Operational Insights 환경은 다음 구성 요소로 이루어져 있습니다.

- Azure 계정에 대한 컨테이너인 Microsoft Azure 호스티드 작업 영역
- 클라우드에 호스트된 Operational Insights 웹 서비스
- 웹 서비스에 직접 연결하는 개별 에이전트
- 또는 System Center Operations Manager에 연결된 서비스(필수 아님)


System Center Advisor라는 이전 버전의 Operational Insights를 사용한 경우 Advisor 소프트웨어가 로컬 환경에 설치되어 있을 수 있습니다. 그러나 Advisor 소프트웨어는 Operational Insights에서 지원되지 않습니다.

Operational Insights 소프트웨어를 Operations Manager 서비스로 사용하는 경우 하나 이상의 관리 그룹과 관리 그룹당 하나 이상의 에이전트로 구성됩니다. Operations Manager 에이전트는 서버에서 데이터를 수집하고 솔루션(System Center Operations Manager의 관리 팩과 유사함)을 사용하여 분석합니다. 데이터는 정기적으로 Operations Manager에서 Operational Insights 웹 서비스로 전송되며(필요한 경우 프록시 서버를 통해 전달) Operations Manager 데이터베이스에 어떤 정보도 저장되지 않으므로 추가 부하가 발생하지 않습니다.

마찬가지로, 개별 컴퓨터에 설치된 에이전트가 웹 서비스에 직접 연결하여 처리를 위해 수집된 데이터를 보낼 수 있습니다.

각 솔루션의 데이터가 분석되고, 인덱싱되고, Operational Insights 포털에 표시됩니다. 모든 경고 및 관련된 수정 지침, 구성 평가, 인프라 용량 문제, 시스템 업데이트 상태, 맬웨어 방지 프로그램 경고, 로그 데이터를 볼 수 있습니다. 자세한 임시 로그 검색 및 탐색을 수행할 수도 있습니다.

![Operational Insights 개요 다이어그램 이미지](./media/operational-insights-feature-guide/environment.png)

### Operational Insights는 어디에서 사용할 수 있나요?
Microsoft Azure Operational Insights는 미국에서 호스트됩니다. Operational Insights 언어가 영어이기는 하지만 여러 다양한 시장에서 이 서비스를 사용할 수 있습니다. 자세한 내용은 [국가별 가용성](http://go.microsoft.com/fwlink/?LinkId=229842)을 참조하세요.

<!---HONumber=July15_HO3-->