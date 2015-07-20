<properties
   pageTitle="변경 내용 추적으로 차이를 식별합니다."
   description="Microsoft Azure Operational Insights의 구성 변경 내용 추적 솔루션을 사용하여 사용자 환경에서 발생하는 소프트웨어 및 Windows 서비스 변경 내용을 쉽게 식별할 수 있습니다."
   services="operational-insights"
   documentationCenter=""
   authors="bandersmsft"
   manager="jwhit"
   editor="" />
<tags
   ms.service="operational-insights"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/02/2015"
   ms.author="banders" />

# 변경 내용 추적으로 차이를 식별합니다.

[AZURE.INCLUDE [operational-insights-note-moms](../../includes/operational-insights-note-moms.md)]

Microsoft Azure Operational Insights에서 구성 변경 내용 추적 솔루션을 사용하여 사용자 환경에서 발생하는 소프트웨어 및 Windows 서비스 변경 내용을 쉽게 식별할 수 있습니다. 이러한 구성 변경 내용을 식별하면 운영 문제를 쉽게 파악할 수 있습니다.

솔루션을 설치하여 Operational Insights용 기본 구성 모듈 및 Operations Manager 에이전트를 업데이트합니다. 모니터링되는 서버에서 설치된 소프트웨어 및 Windows 서비스에 대한 변경 내용을 읽은 다음, 해당 데이터는 처리를 위해 클라우드의 Operational Insights 서비스로 보내집니다. 논리는 수신된 데이터에 적용되며 클라우드 서비스는 데이터를 기록합니다. 변경 내용이 발견되면 변경 내용이 있는 서버가 변경 내용 추적 대시보드에 표시됩니다. 변경 내용 추적 대시보드의 정보를 사용하여 서버 인프라에서 수행한 변경 내용을 쉽게 확인할 수 있습니다.

## 변경 내용 추적 사용

Operational Insights의 변경 내용 추적을 사용하려면 솔루션이 설치되어 있어야 합니다. 솔루션 설치에 대해 더 알려면 [솔루션 갤러리를 사용하여 솔루션 추가 및 제거](operational-insights-add-solution.md)를 참조하십시오.

설치된 후, Operational Insights의 **개요** 페이지에 있는 **변경 내용 추적** 타일을 사용하여 모니터링되는 서버에 대한 변경 내용 요약을 볼 수 있습니다.

![변경 내용 추적 타일의 이미지](./media/operational-insights-change-tracking/overview-change-track.png)

인프라 에 대한 변경 내용을 본 후 다음 범주에 대한 세부 정보를 드릴인투할 수 있습니다.

- 소프트웨어 및 Windows 서비스에 대한 구성 유형별 변경 내용

- 개별 서버에 대한 응용 프로그램 및 업데이트에 대한 소프트웨어 변경 내용

- 각 응용 프로그램에 대한 전체 소프트웨어 변경 내용 수

- 개별 서버에 대한 Windows 서비스 변경 내용

![변경 내용 추적 대시보드의 이미지](./media/operational-insights-change-tracking/gallery-changetracking-01.png) ![변경 내용 추적 대시보드의 이미지](./media/operational-insights-change-tracking/gallery-changetracking-02.png)

### 변경 내용 유형의 변경 내용을 보려면

1. **개요** 페이지에서 **변경 내용 추적** 타일을 클릭합니다.

2. **변경 내용 추적** 대시보드에서, 변경 유형 블레이드 중 하나에서 요약 정보를 검토한 다음 하나를 클릭하여 **로그 검색** 페이지에서 항목에 대한 세부 정보를 봅니다.

3. 로그 검색 페이지에서, 시간별 결과, 자세한 결과 및 로그 검색 기록을 볼 수 있습니다. 패싯으로 필터링하여 결과 범위를 좁힐 수 있습니다.

4. 로그 검색 페이지에서 보거나 수정할 수 있는 Excel 또는 다른 응용 프로그램으로 열 수 있는 CSV 파일로 결과 세부 정보를 **내보내기**할 수 있습니다.

<!---HONumber=July15_HO2-->