<properties
   pageTitle="System Center Operations Manager에서 Operational Insights에 연결"
   description="Operations Manager를 통해 Operational Insights에 연결하는 방법을 알아봅니다."
   services="operational-insights"
   documentationCenter=""
   authors="lauracr"
   manager="jwhit"
   editor=""/>

<tags
   ms.service="operational-insights"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/02/2015"
   ms.author="lauracr"/>

# System Center Operations Manager에서 Operational Insights에 연결


[AZURE.INCLUDE [operational-insights-note-moms](../../includes/operational-insights-note-moms.md)]

기존 System Center Operations Manager 환경에 Operational Insights를 연결할 수 있습니다. 이렇게 하면 기존 Operations Manager 에이전트를 데이터 수집에 사용할 수 있습니다. Operational Insights로 Operations Manager 사용에 대한 추가 정보는[ Operational Insights를 사용한 Operations Manager 고려 사항](operational-insights-operations-manager.md)을 참조하십시오.

Operations Manager를 사용하여 다음 작업을 모니터링하는 경우 Operations Manager 실행 계정을 설정해야 합니다. 계정 설정에 대한 자세한 정보는 [Operational Insights와 작업 관리자 고려 사항](operational-insights-operations-manager.md)을 참조하십시오.

- SQL 평가
- Virtual Machine Manager
- Lync Server
- SharePoint

 >[AZURE.NOTE]Operational Insights 지원은 Operations Manager 2012 SP1 UR6 및 Operations Manager 2012 R2 UR2 기준으로 사용 가능합니다. 프록시 지원은 System Center Operations Manager 2012 SP1 UR7 및 System Center Operations Manager 2012 R2 UR3에 추가되었습니다.

## Operations Manager를 Operational Insights에 연결하고 에이전트를 추가하려면

1. Operations Manager 콘솔에서 **관리**를 클릭합니다.

2. **Operational Insights** 노드를 확장하고 **Operational Insights 연결**을 클릭합니다.

3. **Operational Insights로 등록** 링크를 클릭하고 화면의 지시에 따릅니다.

4. 등록 마법사를 완료한 후 **컴퓨터/그룹 추가**를 클릭합니다.![Operations Manager 컴퓨터/그룹 추가](./media/operational-insights-connect-scom/om01.png)
5. **컴퓨터 검색** 대화 상자에서, Operations Manager에서 모니터링하는 그룹 또는 컴퓨터를 검색할 수 있습니다. Operational Insights에 등록할 컴퓨터 또는 그룹을 선택하고 **추가**를 클릭한 다음 **확인**을 클릭합니다.![Operations Manager 컴퓨터 추가](./media/operational-insights-connect-scom/om02.png)
## 다음 단계

[프록시 및 방화벽 설정 구성(선택 사항)](operational-insights-proxy-firewall.md)

<!---HONumber=July15_HO2-->