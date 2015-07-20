<properties
   pageTitle="시스템 업데이트로 서버 업데이트"
   description="서버 인프라에 누락된 업데이트를 적용할 수 있도록 Microsoft Azure Operational Insights의 시스템 업데이트 솔루션을 사용하는 방법을 알아봅니다."
   services="operational-insights"
   documentationCenter=""
   authors="bandersmsft"
   manager="jwhit"
   editor="tysonn" />
<tags
   ms.service="operational-insights"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/02/2015"
   ms.author="banders" />

# 시스템 업데이트로 서버 업데이트

[AZURE.INCLUDE [operational-insights-note-moms](../../includes/operational-insights-note-moms.md)]

서버 인프라에 누락된 업데이트를 적용할 수 있도록 Microsoft Azure Operational Insights의 시스템 업데이트 솔루션을 사용할 수 있습니다. 솔루션을 설치하여 Operational Insights용 기본 구성 모듈 및 Operations Manager 에이전트를 업데이트합니다. 업데이트 정보는 모니터링 되는 서버에서 읽은 다음 처리를 위해 클라우드의 Operational Insights 서비스로 보내집니다. 논리는 업데이트 데이터에 적용되며 클라우드 서비스는 데이터를 기록합니다. 누락된 업데이트가 발견되면 **업데이트** 대시보드에 표시됩니다. **업데이트** 대시보드를 사용하여 누락된 업데이트를 작업하고 필요로 하는 서버에 적용하도록 계획할 수 있습니다.

## 시스템 업데이트를 사용하여 서버를 업데이트합니다.

Microsoft Azure Operational Insights에서 시스템 업데이트를 사용하려면 솔루션이 설치되어 있어야 합니다. 솔루션 설치에 대한 더 알려면 [솔루션 갤러리를 사용하여 솔루션 추가 및 제거](operational-insights-add-solution.md)를 참조하십시오. Operational Insights의 **개요** 페이지의 **시스템 업데이트 평가** 타일을 사용하여 모니터링된 서버에서 누락된 업데이트를 보고 설치할 수 있습니다.

### 업데이트를 작성하려면

1. **개요** 페이지에서 **시스템 업데이트 평가** 타일을 클릭합니다.![개요 페이지 이미지](./media/operational-insights-updates/updates01.png)
2. **업데이트** 대시보드에서 업데이트 범주를 봅니다.![업데이트 페이지 이미지](./media/operational-insights-updates/updates02.png)
3. **업데이트 누락의 종류** 블레이드를 보려면 페이지의 오른쪽으로 스크롤한 다음 **보안 업데이트**를 클립합니다. ![업데이트 페이지 이미지](./media/operational-insights-updates/updates03.png)
4. 검색 페이지에서 인프라의 서버 중 누락이 발견된 보안 업데이트의 목록이 표시됩니다. 기술 자료 문서 ID(KBID)를 클릭하여 누락된 업데이트에 대한 자세한 내용을 봅니다. 이 예제에서는 *KBID 3032323*입니다.![업데이트 페이지 이미지](./media/operational-insights-updates/updates04.png)
5. 웹 브라우저는 업데이트를 설명하는 기술 자료 문서를 엽니다.![업데이트 페이지 이미지](./media/operational-insights-updates/updates05.png)
6. 발견한 정보를 사용하여 누락된 업데이트를 적용하는 계획을 만들 수 있습니다.

<!---HONumber=July15_HO2-->