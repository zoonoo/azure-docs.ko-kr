<properties
	pageTitle="Log Analytics에서 시스템 업데이트 평가 솔루션 | Microsoft Azure"
	description="서버 인프라에 누락된 업데이트를 적용할 수 있도록 Log Analytics에서 시스템 업데이트 솔루션을 사용할 수 있습니다."
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
	ms.date="05/06/2016"
	ms.author="banders"/>

# Log Analytics에서 시스템 업데이트 평가 솔루션


서버 인프라에 누락된 업데이트를 적용할 수 있도록 Log Analytics에서 시스템 업데이트 솔루션을 사용할 수 있습니다. 솔루션을 설치하면 OMS의 **개요** 페이지의 **시스템 업데이트 평가** 타일을 사용하여 모니터링된 서버에서 누락된 업데이트를 볼 수 있습니다.

누락된 업데이트가 발견되면 세부 정보에 **업데이트** 대시보드에 표시됩니다. **업데이트** 대시보드를 사용하여 누락된 업데이트를 작업하고 필요로 하는 서버에 적용하도록 계획할 수 있습니다.

## 솔루션 설치 및 구성
다음 정보를 사용하여 솔루션을 설치하고 구성합니다.

- [솔루션 갤러리에서 Log Analytics 솔루션 추가](log-analytics-add-solutions.md)에 설명된 프로세스를 사용하여 OMS 작업 영역에 시스템 업데이트 평가 솔루션을 추가합니다. 추가 구성은 필요 없습니다.

## 시스템 업데이트 데이터 수집 세부 정보

다음 표에서는 데이터 수집 방법 및 시스템 업데이트 평가를 위해 데이터가 수집되는 방식에 대한 기타 세부 정보를 보여 줍니다.

| 플랫폼 | 직접 에이전트 | SCOM 에이전트 | Azure 저장소 | SCOM 필요? | 관리 그룹을 통해 전송되는 SCOM 에이전트 데이터 | 수집 빈도 |
|---|---|---|---|---|---|---|
|Windows|![예](./media/log-analytics-system-update/oms-bullet-green.png)|![예](./media/log-analytics-system-update/oms-bullet-green.png)|![아니요](./media/log-analytics-system-update/oms-bullet-red.png)| ![아니요](./media/log-analytics-system-update/oms-bullet-red.png)|![예](./media/log-analytics-system-update/oms-bullet-green.png)| 하루에 최소 2회 및 업데이트를 설치하고 15분 후|


### 업데이트를 작성하려면

1. **개요** 페이지에서 **시스템 업데이트 평가** 타일을 클릭합니다.![개요 페이지 이미지](./media/log-analytics-system-update/oms-updates01.png)
2. **업데이트** 대시보드에서 업데이트 범주를 봅니다.![업데이트 페이지 이미지](./media/log-analytics-system-update/oms-updates02.png)
3. **업데이트 누락의 종류** 블레이드를 보려면 페이지의 오른쪽으로 스크롤한 다음 **보안 업데이트**를 클립합니다. ![업데이트 페이지 이미지](./media/log-analytics-system-update/oms-updates03.png)
4. 검색 페이지에서 인프라의 서버 중 누락이 발견된 보안 업데이트의 목록이 표시됩니다. 기술 자료 문서 ID(KBID)를 클릭하여 누락된 업데이트에 대한 자세한 내용을 봅니다. 이 예제에서는 *KBID 3032323*입니다.![업데이트 페이지 이미지](./media/log-analytics-system-update/oms-updates04.png)
5. 웹 브라우저는 업데이트를 설명하는 기술 자료 문서를 엽니다.![기술 자료 문서의 이미지](./media/log-analytics-system-update/oms-updates05.png)
6. 발견한 정보를 사용하여 누락된 업데이트를 적용하는 계획을 만들 수 있습니다.

## 다음 단계

- [로그를 검색](log-analytics-log-searches.md)하여 자세한 시스템 업데이트 데이터를 볼 수 있습니다.

<!---HONumber=AcomDC_0518_2016-->