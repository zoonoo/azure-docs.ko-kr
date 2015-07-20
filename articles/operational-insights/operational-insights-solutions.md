<properties
	pageTitle="Operational Insights 솔루션"
	description="Operational Insights는 IT 관리자가 온-프레미스 및 클라우드 환경 전반을 심층적으로 파악할 수 있게 해주는 분석 서비스입니다. 실시간 및 과거 컴퓨터 데이터를 조작하여 사용자 지정 통찰력을 신속하게 개발할 수 있게 해주며 데이터 분석을 위해 Microsoft와 커뮤니티에서 개발한 패턴을 제공합니다."
	services="operational-insights"
	documentationCenter="n/a"
	authors="bandersmsft"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="operational-insights"
	ms.workload="operational-insights"
	ms.tgt_pltfrm="NA"
	ms.devlang="NA"
	ms.topic="article"
	ms.date="07/02/2015"
	ms.author="alfran"/>

# Operational Insights의 솔루션

[AZURE.INCLUDE [operational-insights-note-moms](../../includes/operational-insights-note-moms.md)]

솔루션은 오늘 주요 고객 활동을 지정하는 **논리**, **시각화** 및 **데이터 취득 규칙**의 컬렉션입니다. 솔루션은 Operational Insights 로그 검색에서 지원되어 특정 문제 영역 주위에 피벗된 메트릭을 가져옵니다. 심층적인 통찰력을 조사하고 더 빠르게 작동 문제를 해결하고, 다양한 유형의 컴퓨터 데이터를 수집하고 상관 관계를 지정하여 용량 계획, 패치 상태 보고 및 보안 감사와 같은 작업으로 문제를 예방할 수 있습니다.

이 가이드에서는 사용할 수 있는 다양한 솔루션 및 용도를 안내합니다.

>[AZURE.NOTE]솔루션 *추가*에 대한 자세한 내용은 [솔루션 추가](operational-insights-add-solution.md)를 참조하세요.

## SQL 평가

SQL 평가 솔루션은 일정한 간격으로 SQL Server 환경의 위험 및 상태를 평가합니다. 기본적으로 매주 SQL 시스템을 스캔하며 매월 롤업으로 정보가 표시 됩니다. 배포 방식에 맞게 우선순위가 지정된 권장 사항 목록을 제공합니다. 이 권장 사항은 사용자와 사용자 팀이 다음을 수행할 수 있는 6개의 주요 영역으로 분류됩니다.

- 사용자 환경의 **위험과 상태**를 **신속하게** 이해
- 위험을 줄이고 상태를 개선하도록 손쉬운 **조치** 수행
- 작업의 **우선 순위**를 정하면 **생산성**이 개선됨

SQL 평가를 각 에이전트에서 실행하려면 .NET 4가 필요합니다. 현재 지원되는 모든 버전의 SQL Server의 Standard, Developer 및 Enterprise edition을 지원합니다. [평가 솔루션으로 환경 최적화](operational-insights-assessment.md)를 참조하세요.


## 구성 평가

구성 평가는 서버 인프라의 현재 상태에 대한 자세한 정보를 제공합니다.

솔루션과 달리 처음 시작하면 System Center Operations Manager와 함께 Operational Insights를 사용하여 구성 평가를 사용할 수 있습니다. 에이전트 직접 연결만을 사용 하는 경우 사용할 수 없습니다.

구성 데이터는 모니터링되는 서버에서 수집되고 처리를 위해 클라우드의 Operational Insights 서비스로 전송합니다. 논리는 수신된 데이터에 적용되며 클라우드 서비스는 데이터를 기록합니다. 서버에 대해 처리된 데이터는 다음 영역에 대해 표시됩니다.

- **경고:** 모니터링되는 서버에 대해 발생한 구성 관련 예방적 경고를 표시합니다. 이러한 경고는 Microsoft 고객 지원 조직(CSS)이 현장의 모범 사례를 통해 작성한 규칙에 의해 생성됩니다.
- **기술 권장 사항:** 인프라에서 발견되는 작업에 대해 권장되는 Microsoft 기술 자료 문서를 표시합니다. 기계 학습의 사용을 통해 구성에 따라 자동으로 제안됩니다.
- **서버와 작업 분석:** Operational Insights에서 모니터링되는 서버 및 작업 부하를 표시합니다.
- **현재 스냅숏:** Operational Insights 서비스에 데이터를 보고하는 서버에 대한 최신 정보를 표시합니다.
- **변경 내용:** 모니터링된 서버에 작성된 구성 변경 목록을 표시합니다.

> [AZURE.IMPORTANT]**Operations Manager**에서 관리되는 컴퓨터에 대해서만 구성 평가를 사용할 수 있습니다. 자세한 내용은 [Operations Manager 연결](operational-insights-connect-scom.md)을 참조하세요.


## 맬웨어 평가
보호가 부족하다면, 위협이 활성화된 서버 및 보호가 부족한 서버가 **맬웨어** 페이지에 표시됩니다. **멜웨어** 페이지에서 정보를 사용하여 필요한 서버에 보호를 적용하도록 계획할 수 있습니다.

> [AZURE.IMPORTANT]맬웨어 평가는 현재 Windows Defender 및 System Center Endpoint Protection(SCEP) 실시간 클라이언트만을 지원합니다. 이러한 클라이언트가 없는 경우, 실시간 보호되지 않는 것으로 악성 소프트웨어 제거 도구 및 기호 서버에서 데이터를 사용합니다. 자세한 내용은 [여기](http://feedback.azure.com/forums/267889-azure-operational-insights/suggestions/6519202-support-other-antivirus-products-in-malware-assess)에서 확인할 수 있습니다. [Windows 7 및 Windows Server 2008 R2 컴퓨터에 있는 문제](http://feedback.azure.com/forums/267889-azure-operational-insights/suggestions/6519211-windows-server-2008-r2-sp1-servers-are-shown-as-n)도 추적합니다.





## 경고 관리

경고 관리 솔루션을 사용하여, 모든 서버에서 **Operations Manager**를 볼 수 있습니다. 이 도구는 경고를 쉽게 분류하는 데 도움이 되며 매우 빠르고 유연한 방식으로 사용자 환경에서 근본 원인을 식별할 수 있습니다.

다음을 포함하여 주요 시나리오로 통찰력을 얻을 수 있습니다.

- 지정된 시간 프레임 내에서 얼마나 많은 경고가 발생하나요?
- 지정된 시간 프레임 내에서 발생하는 활성 경고가 가장 많은 상위 원본입니다.
- 지정된 시간 프레임 내에서 발생하는 상위 활성 위험 및 경고 알림입니다.
- 모든 경고를 통해 검색하고 세부 정보에서 모든 경고를 볼 수 있는 기능

> [AZURE.IMPORTANT]Operational Insights가 **System Center Operations Manager**와 함께 사용된 경우에만 경고 관리를 사용할 수 있습니다. 이 에이전트에서 모든 데이터를 보내지 않지만, 단순히 Operations Manager 경고를 클라우드에 동기화하여 Operational Insights에서 심사하고 로그 검색을 사용할 수 있도록 합니다. 자세한 내용은 [Operations Manager 연결](operational-insights-connect-scom.md)을 참조하세요.


## 용량 계획

Microsoft Azure Operational Insights의 용량 관리 솔루션을 사용하여 서버 인프라의 용량을 이해할 수 있습니다. 이 솔루션은 모니터링되는 서버에서 성능 카운터를 읽고 처리하도록 클라우드의 Operational Insights 서비스로 사용 현황 데이터를 보냅니다. 논리는 사용 현황 데이터에 적용되며 클라우드 서비스는 데이터를 기록합니다. 시간이 지남에 따라 사용 패턴이 식별되고 현재 사용량에 따라 용량이 프로젝션됩니다.

예를 들어, 개별 서버에 대한 추가 프로세서 코어 또는 추가 메모리가 필요한 시기를 프로젝션이 식별할 수 있습니다. 이 예에서는 프로젝션이 30일 후 서버에 추가 메모리가 필요함을 나타낼 수 있습니다. 서버의 다음 유지 관리 기간 동안 메모리 업그레이드를 계획할 수 있으며 2주마다 한번 발생할 수 있습니다.

>[AZURE.IMPORTANT]용량 관리는 Operational Insights가 **System Center Operations Manager**와 함께 사용하는 경우에만 사용할 수 있으며 VMM(Virtual Machine Manager)와 Operations Manager 커넥터를 사용하도록 설정해야 합니다. 시스템 연결에 대한 자세한 내용은 [Operations Manager와 VMM을 연결하는 방법](https://technet.microsoft.com/library/hh882396.aspx)을 참조하세요.

용량 관리 솔루션 사용에 대한 자세한 내용은 다음을 참조하세요.

- [계산 페이지를 사용하는 방법](operational-insights-capacity/#compute-page)
- [직접 연결 저장소 페이지를 사용하는 방법](operational-insights-capacity/#direct-attached-storage-page)


## 변경 내용 추적

구성 변경 내용 추적 솔루션을 사용하여 사용자 환경에서 발생하는 소프트웨어 및 Windows 서비스 변경 내용을 쉽게 식별할 수 있습니다. 이러한 구성 변경 내용을 식별하면 운영 문제를 쉽게 파악할 수 있습니다. 변경 내용 추적 페이지의 정보를 사용하여 서버 인프라에서 수행한 변경 내용을 쉽게 확인할 수 있습니다. 인프라 에 대한 변경 내용을 본 후 다음 범주에 대한 세부 정보를 드릴인투할 수 있습니다.

- 소프트웨어 및 Windows 서비스에 대한 구성 유형별 변경 내용
- 개별 서버에 대한 응용 프로그램 및 업데이트에 대한 소프트웨어 변경 내용
- 각 응용 프로그램에 대한 전체 소프트웨어 변경 내용 수
- 개별 서버에 대한 Windows 서비스 변경 내용


## 시스템 업데이트 평가

서버 인프라에 누락된 업데이트를 적용할 수 있도록 Microsoft Azure Operational Insights의 시스템 업데이트 솔루션을 사용할 수 있습니다. 누락된 업데이트가 발견되면 **업데이트** 페이지에 표시됩니다. **업데이트** 페이지를 사용하여 누락된 업데이트를 작업하고 필요로 하는 서버에 적용하도록 계획할 수 있습니다.

업데이트 페이지는 다음 범주를 자세히 설명합니다.

- 보안 업데이트가 누락된 서버
- 최근에 업데이트 하지 않은 서버
- 특정 서버에 적용되어야 하는 업데이트
- 누락된 업데이트의 유형

타일이나 항목을 클릭하여 **검색** 페이지에서 세부 정보를 보고 누락된 업데이트에 대한 자세한 정보를 얻습니다. 검색 결과는 다음을 포함합니다.

- 서버
- 업데이트 제목
- 기술 자료 ID
- 제품 업데이트
- 업데이트 중요도
- 게시 날짜

서버 검색 결과는 다음을 포함합니다.

- 서버 이름
- 운영 체제 버전 이름
- 메서드를 사용하도록 설정하는 자동 업데이트
- 마지막 업데이트 이후 기간(일)
- Windows Update 에이전트 버전

<!---HONumber=July15_HO2-->