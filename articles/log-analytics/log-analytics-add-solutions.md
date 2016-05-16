<properties
	pageTitle="솔루션 갤러리에서 Log Analytics 솔루션 추가 | Microsoft Azure"
	description="Log Analytics 솔루션은 특정 문제 영역을 중심으로 피벗된 메트릭을 제공하는 논리, 시각화 및 데이터 획득 규칙의 모음입니다."
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
	ms.date="04/28/2016"
	ms.author="banders"/>

# 솔루션 갤러리에서 Log Analytics 솔루션 추가

Log Analytics 솔루션은 특정 문제 영역을 중심으로 피벗된 메트릭을 제공하는 **논리**, **시각화** 및 **데이터 획득 규칙**의 모음입니다. 이 문서에서는 Log Analytics에서 지원되는 솔루션을 나열하고 솔루션 갤러리를 사용하여 솔루션을 추가 및 제거하는 방법을 보여 줍니다.

솔루션은
- 운영 문제를 보다 빠르게 조사 및 해결하고,
- 다양한 유형의 컴퓨터 데이터를 수집 및 상호 연결하며,
- 용량 계획, 패치 상태 보고 및 보안 감사와 같은 활동으로 문제를 예방하는 데 도움이 되는 보다 심층적인 통찰력을 제공합니다.


>[AZURE.NOTE] OMS에는 기본 로그 검색 기능이 포함되어 있으므로 이 기능을 사용하기 위해 별도의 솔루션을 설치할 필요가 없습니다. 그러나 솔루션 갤러리 페이지에서 솔루션을 추가하여 추가 기능을 얻을 수 있습니다.

솔루션을 추가한 후에는 데이터가 사용자 인프라의 서버에서 수집되고 OMS 서비스로 전송됩니다. OMS 서비스에서 처리하는 데 몇 분 내지 몇 시간이 걸릴 수 있습니다. 서비스에서 데이터를 처리한 후에는 OMS에서 볼 수 있습니다.

더 이상 필요 없는 경우는 솔루션을 쉽게 제거할 수 있습니다. 솔루션을 제거하면 해당 데이터가 OMS로 전송되지 않으므로 일일 할당량(있는 경우)에 의해 사용되는 데이터의 양이 줄어듭니다.


## Microsoft Monitoring Agent에서 지원하는 솔루션

현재 Microsoft Monitoring Agent를 사용하여 OMS에 연결된 서버는 다음을 포함하여 사용 가능한 솔루션의 대부분을 사용할 수 있습니다.

- 시스템 업데이트
- 맬웨어 방지
- 변경 내용 추적
- SQL 평가
- Active Directory 평가
- 경고 관리(SCOM 경고 제외)

그러나 다음 솔루션은 Microsoft Monitoring Agent에서 지원되지 *않으며* SCOM(System Center Operations Manager)이 필요합니다.

- 용량 관리
- 경고 관리(SCOM 경고 포함)
- 구성 평가

SCOM 에이전트를 Log Analytics에 연결하는 방법에 대한 자세한 내용은 [Log Analytics에 Operations Manager 연결](log-analytics-om-agents.md)을 참조하세요.

### 솔루션 갤러리를 사용하여 솔루션을 추가하려면

1. OMS의 개요 페이지에서 **솔루션 갤러리** 타일을 클릭합니다. ![솔루션 갤러리](./media/log-analytics-add-solutions/sol-gallery.png)
2. OMS 솔루션 갤러리 페이지에서 사용 가능한 각 솔루션에 대해 알아봅니다. OMS에 추가할 솔루션의 이름을 클릭합니다.
3. 선택한 솔루션에 대한 페이지에서 솔루션에 대한 자세한 정보가 표시됩니다. **추가**를 클릭합니다.
4. 추가한 솔루션의 새 타일이 OMS의 개요 페이지에 표시되며 OMS 서비스가 데이터를 처리한 후 새 타일을 사용하여 시작할 수 있습니다.

## 솔루션을 구성하려면
1. 몇 가지 솔루션을 구성해야 합니다. 예를 들어 자동화, Azure Site Recovery 및 백업을 사용하려면 먼저 구성해야 합니다.
2. 이러한 솔루션에 대해 개요 페이지에서 해당 타일을 클릭합니다. ![솔루션 구성](./media/log-analytics-add-solutions/configure-additional.png)
3. 그런 다음 필요한 정보와 함께 솔루션을 구성하고 **저장**을 클릭합니다. ![솔루션 구성](./media/log-analytics-add-solutions/configure.png)

### 솔루션 갤러리를 사용하여 솔루션을 제거하려면

1. OMS의 개요 페이지에서 **설정** 타일을 클릭합니다.
2. 설정 페이지의 솔루션 탭에서 제거할 솔루션에 대해 **제거**를 클릭합니다.
3. 확인 대화 상자에서 **예**를 클릭하여 솔루션을 제거합니다.

## OMS 기능 및 솔루션에 대한 데이터 수집 정보

다음 표에서는 데이터 수집 방법 및 OMS 기능과 솔루션에 대해 데이터가 수집되는 방식에 대한 기타 세부 정보를 보여 줍니다.

|데이터 형식| 플랫폼 | 직접 에이전트 | SCOM 에이전트 | Azure 저장소 | SCOM 필요 여부 | 관리 그룹을 통해 전송되는 SCOM 에이전트 데이터 | 수집 빈도 |
|---|---|---|---|---|---|---|---|
|AD 평가|Windows|![예](./media/log-analytics-add-solutions/oms-bullet-green.png)|![예](./media/log-analytics-add-solutions/oms-bullet-green.png)|![아니요](./media/log-analytics-add-solutions/oms-bullet-red.png)|![아니요](./media/log-analytics-add-solutions/oms-bullet-red.png)|![예](./media/log-analytics-add-solutions/oms-bullet-green.png)|	7 일|
|AD 복제 상태|Windows|![예](./media/log-analytics-add-solutions/oms-bullet-green.png)|![예](./media/log-analytics-add-solutions/oms-bullet-green.png)|![아니요](./media/log-analytics-add-solutions/oms-bullet-red.png)|![아니요](./media/log-analytics-add-solutions/oms-bullet-red.png)|![아니요](./media/log-analytics-add-solutions/oms-bullet-red.png)|5일|
|경고(Nagios)|Linux|![예](./media/log-analytics-add-solutions/oms-bullet-green.png)|![아니요](./media/log-analytics-add-solutions/oms-bullet-red.png)|![아니요](./media/log-analytics-add-solutions/oms-bullet-red.png)|![아니요](./media/log-analytics-add-solutions/oms-bullet-red.png)|![아니요](./media/log-analytics-add-solutions/oms-bullet-red.png)|도착 시|
|경고(Zabbix)|Linux|![예](./media/log-analytics-add-solutions/oms-bullet-green.png)|![아니요](./media/log-analytics-add-solutions/oms-bullet-red.png)|![아니요](./media/log-analytics-add-solutions/oms-bullet-red.png)|![아니요](./media/log-analytics-add-solutions/oms-bullet-red.png)|![아니요](./media/log-analytics-add-solutions/oms-bullet-red.png)|1분|
|경고(Operations Manager)|Windows|![아니요](./media/log-analytics-add-solutions/oms-bullet-red.png)|![예](./media/log-analytics-add-solutions/oms-bullet-green.png)|![아니요](./media/log-analytics-add-solutions/oms-bullet-red.png)|![예](./media/log-analytics-add-solutions/oms-bullet-green.png)|![예](./media/log-analytics-add-solutions/oms-bullet-green.png)|3분|
|맬웨어 방지|Windows|![예](./media/log-analytics-add-solutions/oms-bullet-green.png)|![예](./media/log-analytics-add-solutions/oms-bullet-green.png)|![아니요](./media/log-analytics-add-solutions/oms-bullet-red.png)|![아니요](./media/log-analytics-add-solutions/oms-bullet-red.png)|![예](./media/log-analytics-add-solutions/oms-bullet-green.png)| 매시간|
|용량 관리|Windows|![아니요](./media/log-analytics-add-solutions/oms-bullet-red.png)|![예](./media/log-analytics-add-solutions/oms-bullet-green.png)|![아니요](./media/log-analytics-add-solutions/oms-bullet-red.png)|![예](./media/log-analytics-add-solutions/oms-bullet-green.png)|![예](./media/log-analytics-add-solutions/oms-bullet-green.png)| 매시간|
|변경 내용 추적|Windows|![예](./media/log-analytics-add-solutions/oms-bullet-green.png)|![예](./media/log-analytics-add-solutions/oms-bullet-green.png)|![아니요](./media/log-analytics-add-solutions/oms-bullet-red.png)|![아니요](./media/log-analytics-add-solutions/oms-bullet-red.png)|![예](./media/log-analytics-add-solutions/oms-bullet-green.png)| 매시간|
|변경 내용 추적|Linux|![예](./media/log-analytics-add-solutions/oms-bullet-green.png)|![아니요](./media/log-analytics-add-solutions/oms-bullet-red.png)|![아니요](./media/log-analytics-add-solutions/oms-bullet-red.png)|![아니요](./media/log-analytics-add-solutions/oms-bullet-red.png)|![아니요](./media/log-analytics-add-solutions/oms-bullet-red.png)|매시간|
|구성 평가(레거시 관리자)|Windows|![아니요](./media/log-analytics-add-solutions/oms-bullet-red.png)|![예](./media/log-analytics-add-solutions/oms-bullet-green.png)|![아니요](./media/log-analytics-add-solutions/oms-bullet-red.png)|![예](./media/log-analytics-add-solutions/oms-bullet-green.png)|![예](./media/log-analytics-add-solutions/oms-bullet-green.png)| 하루에 2회|
|ETW|Windows|![아니요](./media/log-analytics-add-solutions/oms-bullet-red.png)|![아니요](./media/log-analytics-add-solutions/oms-bullet-red.png)|![예](./media/log-analytics-add-solutions/oms-bullet-green.png)|![아니요](./media/log-analytics-add-solutions/oms-bullet-red.png)|![아니요](./media/log-analytics-add-solutions/oms-bullet-red.png)|5분|
|IIS 로그|Windows|![예](./media/log-analytics-add-solutions/oms-bullet-green.png)|![예](./media/log-analytics-add-solutions/oms-bullet-green.png)|![예](./media/log-analytics-add-solutions/oms-bullet-green.png)|![아니요](./media/log-analytics-add-solutions/oms-bullet-red.png)|![아니요](./media/log-analytics-add-solutions/oms-bullet-red.png)|5분|
|네트워크 보안 그룹|Windows|![아니요](./media/log-analytics-add-solutions/oms-bullet-red.png)|![아니요](./media/log-analytics-add-solutions/oms-bullet-red.png)|![예](./media/log-analytics-add-solutions/oms-bullet-green.png)|![아니요](./media/log-analytics-add-solutions/oms-bullet-red.png)|![아니요](./media/log-analytics-add-solutions/oms-bullet-red.png)|10분|
|Office 365|Windows|![아니요](./media/log-analytics-add-solutions/oms-bullet-red.png)|![아니요](./media/log-analytics-add-solutions/oms-bullet-red.png)|![아니요](./media/log-analytics-add-solutions/oms-bullet-red.png)|![아니요](./media/log-analytics-add-solutions/oms-bullet-red.png)|![아니요](./media/log-analytics-add-solutions/oms-bullet-red.png)|알림 시|
|성능 카운터|Windows|![예](./media/log-analytics-add-solutions/oms-bullet-green.png)|![예](./media/log-analytics-add-solutions/oms-bullet-green.png)|![아니요](./media/log-analytics-add-solutions/oms-bullet-red.png)|![아니요](./media/log-analytics-add-solutions/oms-bullet-red.png)|![아니요](./media/log-analytics-add-solutions/oms-bullet-red.png)|예약된 대로, 최소 10초|
|성능 카운터|Linux|![예](./media/log-analytics-add-solutions/oms-bullet-green.png)|![아니요](./media/log-analytics-add-solutions/oms-bullet-red.png)|![아니요](./media/log-analytics-add-solutions/oms-bullet-red.png)|![아니요](./media/log-analytics-add-solutions/oms-bullet-red.png)|![아니요](./media/log-analytics-add-solutions/oms-bullet-red.png)|예약된 대로, 최소 10초|
|서비스 패브릭|Windows|![아니요](./media/log-analytics-add-solutions/oms-bullet-red.png)|![아니요](./media/log-analytics-add-solutions/oms-bullet-red.png)|![예](./media/log-analytics-add-solutions/oms-bullet-green.png)|![아니요](./media/log-analytics-add-solutions/oms-bullet-red.png)|![아니요](./media/log-analytics-add-solutions/oms-bullet-red.png)|5분|
|SQL 평가|Windows|![예](./media/log-analytics-add-solutions/oms-bullet-green.png)|![예](./media/log-analytics-add-solutions/oms-bullet-green.png)|![아니요](./media/log-analytics-add-solutions/oms-bullet-red.png)|![아니요](./media/log-analytics-add-solutions/oms-bullet-red.png)|![예](./media/log-analytics-add-solutions/oms-bullet-green.png)|	7 일|
|SurfaceHub|Windows|![예](./media/log-analytics-add-solutions/oms-bullet-green.png)|![아니요](./media/log-analytics-add-solutions/oms-bullet-red.png)|![아니요](./media/log-analytics-add-solutions/oms-bullet-red.png)|![아니요](./media/log-analytics-add-solutions/oms-bullet-red.png)|![아니요](./media/log-analytics-add-solutions/oms-bullet-red.png)|도착 시|
|Syslog|Linux|![예](./media/log-analytics-add-solutions/oms-bullet-green.png)|![아니요](./media/log-analytics-add-solutions/oms-bullet-red.png)|![아니요](./media/log-analytics-add-solutions/oms-bullet-red.png)|![아니요](./media/log-analytics-add-solutions/oms-bullet-red.png)|![아니요](./media/log-analytics-add-solutions/oms-bullet-red.png)|Azure 저장소: 10분, 에이전트: 도착 시|
|시스템 업데이트|Windows|![예](./media/log-analytics-add-solutions/oms-bullet-green.png)|![예](./media/log-analytics-add-solutions/oms-bullet-green.png)|![아니요](./media/log-analytics-add-solutions/oms-bullet-red.png)|![아니요](./media/log-analytics-add-solutions/oms-bullet-red.png)|![예](./media/log-analytics-add-solutions/oms-bullet-green.png)| 하루에 최소 2회 및 업데이트를 설치하고 15분 후|
|Windows 보안 이벤트 로그|Windows|![예](./media/log-analytics-add-solutions/oms-bullet-green.png)|![예](./media/log-analytics-add-solutions/oms-bullet-green.png)|![예](./media/log-analytics-add-solutions/oms-bullet-green.png)|![아니요](./media/log-analytics-add-solutions/oms-bullet-red.png)|![아니요](./media/log-analytics-add-solutions/oms-bullet-red.png)| Azure 저장소: 10분, 에이전트: 도착 시|
|Windows 방화벽 로그|Windows|![예](./media/log-analytics-add-solutions/oms-bullet-green.png)|![예](./media/log-analytics-add-solutions/oms-bullet-green.png)|![아니요](./media/log-analytics-add-solutions/oms-bullet-red.png)|![아니요](./media/log-analytics-add-solutions/oms-bullet-red.png)|![아니요](./media/log-analytics-add-solutions/oms-bullet-red.png)| 도착 시|
|Windows 이벤트 로그|Windows|![예](./media/log-analytics-add-solutions/oms-bullet-green.png)|![예](./media/log-analytics-add-solutions/oms-bullet-green.png)|![예](./media/log-analytics-add-solutions/oms-bullet-green.png)|![아니요](./media/log-analytics-add-solutions/oms-bullet-red.png)|![예](./media/log-analytics-add-solutions/oms-bullet-green.png)| Azure 저장소: 1분, 에이전트: 도착 시|
|실시간 데이터|Windows(2012 R2/8.1 이상)|![예](./media/log-analytics-add-solutions/oms-bullet-green.png)|![예](./media/log-analytics-add-solutions/oms-bullet-green.png)|![아니요](./media/log-analytics-add-solutions/oms-bullet-red.png)|![아니요](./media/log-analytics-add-solutions/oms-bullet-red.png)|![아니요](./media/log-analytics-add-solutions/oms-bullet-red.png)| 1분마다|


## 다음 단계

- [로그를 검색](log-analytics-log-searches.md)하여 솔루션에서 수집한 자세한 정보를 확인합니다.

<!---HONumber=AcomDC_0504_2016-->