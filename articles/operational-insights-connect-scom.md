<properties 
   pageTitle="System Center Operations Manager에서 Operational Insights에 연결" 
   description="Operations Manager를 통해 Operational Insights에 연결하는 방법을 알아봅니다." 
   services="operational-insights" 
   documentationCenter="" 
   authors="lauracr" 
   manager="jwhit" 
   editor="bandersmsft"/>

<tags
   ms.service="operational-insights"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="02/20/2015"
   ms.author="lauracr"/>

# System Center Operations Manager에서 Operational Insights에 연결 

기존 System Center Operations Manager 환경에 Operational Insights를 연결할 수 있습니다. 이렇게 하면 기존 Operations Manager 에이전트를 Operational Insights 에이전트로 사용할 수 있습니다.

 >[AZURE.NOTE] Operational Insights 지원은 Operations Manager 2012 SP1 UR6 및 Operations Manager 2012 R2 UR2 기준으로 사용 가능합니다. SCOM 2012 SP1 UR7 및 SCOM 2012 R2 UR3 에서 프록시 지원이 추가되었습니다.

## SCOM을 Operational Insights에 연결하고 에이전트를 추가합니다.

1. Operations Manager 콘솔에서 **관리**를 클릭합니다.

2. **Operational Insights** 노드를 확장하고 **Operational Insights 연결**을 클릭합니다.

3. **Operational Insights로 등록** 링크를 클릭하고 화면의 지시에 따릅니다. 

4. 등록 마법사를 완료한 후 **컴퓨터/그룹 추가**를 클릭합니다.

5. **컴퓨터 검색** 대화 상자에서, Operations Manager에서 모니터링하는 그룹 또는 컴퓨터를 검색할 수 있습니다. Operational Insights에 등록할 컴퓨터 또는 그룹을 선택하고 **추가**를 클릭한 다음 **확인**을 클릭합니다.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 다음 단계

[프록시 및 방화벽 설정 구성(선택 사항)](https://msdn.microsoft.com/library/azure/dn884643.aspx)

<!--HONumber=52-->