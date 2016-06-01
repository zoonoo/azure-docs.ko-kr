<properties
	pageTitle="Log Analytics의 구성 평가 솔루션 | Microsoft Azure"
	description="Log Analytics의 구성 평가 솔루션은 Operations Manager 에이전트 또는 Operations Manager 관리 그룹을 사용할 때 System Center Operations Manager 서버 인프라의 현재 상태에 관한 자세한 정보를 제공합니다."
	services="log-analytics"
	documentationCenter=""
	authors="bandersmsft"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="log-analytics"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/04/2016"
	ms.author="banders"/>

# Log Analytics의 구성 평가 솔루션

Log Analytics의 구성 평가 솔루션은 경고 및 기술 권장 사항을 통해 잠재적인 서버 구성 문제를 찾는 데 도움이 됩니다.

이 솔루션을 사용하려면 System Center Operations Manager가 필요합니다. 구성 평가는 직접 연결 에이전트만을 사용하는 경우 사용할 수 없습니다.

구성 평가 솔루션의 일부 정보를 보려면 브라우저에 대한 Silverlight 플러그 인이 필요합니다.

>[AZURE.NOTE] 구성 평가 솔루션은 미국 동부 지역의 작업 영역에서만 사용할 수 있으며 추가 지역에 추가되지 않습니다. 구성 평가 솔루션은 Active Directory 평가 및 SQL Server 평가를 비롯한 워크로드별 평가로 대체되고 있습니다.

![구성 평가 타일](./media/log-analytics-configuration-assessment/oms-config-assess-tile.png)

구성 데이터는 모니터링되는 서버에서 수집되고 처리를 위해 클라우드의 OMS 서비스로 전송합니다. 논리는 수신된 데이터에 적용되며 클라우드 서비스는 데이터를 기록합니다. 서버에 대해 처리된 데이터는 다음 영역에 대해 표시됩니다.

- **경고:** 모니터링되는 서버에 대해 발생한 구성 관련 예방적 경고를 표시합니다. 이러한 경고는 Microsoft 고객 지원 조직(CSS)이 현장의 모범 사례를 통해 작성한 규칙에 의해 생성됩니다.
- **기술 권장 사항:** 인프라에서 발견되는 작업에 대해 권장되는 Microsoft 기술 자료 문서를 표시합니다. 기계 학습의 사용을 통해 구성에 따라 자동으로 제안됩니다.
- **서버와 작업 분석:** OMS에서 모니터링되는 서버 및 작업 부하를 표시합니다.

![구성 평가 대시보드](./media/log-analytics-configuration-assessment/oms-config-assess-dash01.png)

### 구성 평가를 사용하여 분석할 수 있는 기술

OMS 구성 평가는 다음과 같은 작업을 분석합니다.

- Windows Server 2012 및 Microsoft Hyper-v Server 2012
- 다음을 포함하는 Windows Server 2008 및 Windows Server 2008 R2
    - Active Directory
	- Hyper-V 호스트
	- 일반 운영 체제
- SQL Server 2008 이상
    - SQL Server 데이터베이스 엔진
- Microsoft SharePoint 2010
- Microsoft Exchange Server 2010 및 Microsoft Exchange Server 2013
- Microsoft Lync Server 2013 및 Lync Server 2010
- System Center 2012 SP1 - Virtual Machine Manager

SQL Server의 경우 분석에 대해 다음과 같은 32비트 및 64비트 버전이 지원됩니다.

- SQL Server 2016 - 모든 버전
- SQL Server 2014 - 모든 버전
- SQL Server 2008 및 2008 R2 - 모든 버전

SQL Server 데이터베이스 엔진은 모든 지원되는 버전에서 분석됩니다. 또한 WOW64 구현에서 실행하는 경우 SQL Server 32비트 버전이 지원됩니다.

## 솔루션 설치 및 구성
다음 정보를 사용하여 솔루션을 설치하고 구성합니다.

- 구성 평가 솔루션을 사용하려면 Operations Manager가 필요합니다.
- 구성을 평가할 각 컴퓨터에 Operations Manager 에이전트가 있어야 합니다.
- [솔루션 갤러리에서 Log Analytics 솔루션 추가](log-analytics-add-solutions.md)에 설명된 프로세스를 사용하여 OMS 작업 영역에 구성 평가 솔루션을 추가합니다. 추가 구성은 필요 없습니다.

## 구성 평가 데이터 수집 정보

다음 표에서는 데이터 수집 방법 및 구성 평가를 위해 데이터가 수집되는 방식에 대한 기타 세부 정보를 보여 줍니다.

| 플랫폼 | 직접 에이전트 | SCOM 에이전트 | Azure 저장소 | SCOM 필요? | 관리 그룹을 통해 전송되는 SCOM 에이전트 데이터 | 수집 빈도 |
|---|---|---|---|---|---|---|
|Windows|![아니요](./media/log-analytics-configuration-assessment/oms-bullet-red.png)|![예](./media/log-analytics-configuration-assessment/oms-bullet-green.png)|![아니요](./media/log-analytics-configuration-assessment/oms-bullet-red.png)| ![예](./media/log-analytics-configuration-assessment/oms-bullet-green.png)|![예](./media/log-analytics-configuration-assessment/oms-bullet-green.png)| 하루에 2회|


## 구성 평가 경고
경고 페이지를 통해 구성 평가의 경고를 보고 관리할 수 있습니다. 경고는 감지된 문제, 원인 및 문제 해결 방법을 알려 줍니다. 또한 성능 문제를 일으킬 수 있는 환경의 구성 설정에 관한 정보도 제공합니다.

![경고 보기](./media/log-analytics-configuration-assessment/oms-config-assess-alerts01.png)

>[AZURE.NOTE] 구성 평가 경고는 Log Analytics의 다른 경고와 다릅니다. 경고를 보려면 브라우저에 대 한 Silverlight 플러그 인이 필요합니다.

경고 페이지에서 항목 또는 항목 범주를 선택하면 각 항목에 적용되는 서버 또는 워크로드 목록이 나타납니다.

![경고 목록](./media/log-analytics-configuration-assessment/oms-config-assess-alerts-view-config.png)

각 경고는 Microsoft 기술 자료에 대한 링크를 포함하고 있습니다. 이러한 문서는 경고에 대한 추가 정보를 제공합니다.

>[AZURE.TIP] 기본적으로 표시되는 경고의 최대 개수는 2,000개입니다. 더 많은 경고를 보려면 경고 목록 위의 알림 표시줄을 클릭합니다.

목록의 항목을 클릭하면 경고를 발생시킨 문제의 원인을 해결하는 데 도움이 될 수 있는 기술 자료 문서를 볼 수 있습니다.

![기술 자료 문서](./media/log-analytics-configuration-assessment/oms-config-assess-alerts-details-kb.png)

경고 규칙을 관리하여 특정 규칙 또는 규칙 클래스를 무시할 수 있습니다.

![경고 규칙 관리](./media/log-analytics-configuration-assessment/oms-config-assess-alert-rules.png)

## 기술 권장 사항
기술 권장 사항을 보는 경우 로그 검색 결과가 표시되며, 여기에 경고에 관한 추가 정보를 제공하는 워크로드 및 컴퓨터에 대해 권장되는 Microsoft 기술 자료 문서가 나열됩니다.

![기술 권장 사항에 대한 검색 결과](./media/log-analytics-configuration-assessment/oms-config-assess-knowledge-recommendations.png)

## 분석한 서버 및 워크로드
기술 권장 사항을 보는 경우 로그 검색 결과가 표시되며, 여기에 Operations Manager에서 OMS에 알려진 모든 서버 및 워크로드가 나열됩니다.

![서버 및 워크로드](./media/log-analytics-configuration-assessment/oms-config-assess-servers-workloads.png)

## 다음 단계

- [Log Analytics의 검색 결과](log-analytics-log-searches.md)를 사용하여 자세한 구성 평가 데이터를 볼 수 있습니다.

<!---HONumber=AcomDC_0518_2016-->