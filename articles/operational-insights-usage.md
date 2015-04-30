<properties 
   pageTitle="서버 및 사용 현황 데이터 관리"
   description="서버에서 Operational Insights 서비스로 전송된 데이터의 크기를 알아봅니다."
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
   ms.date="03/20/2015"
   ms.author="banders" />

# 서버 및 사용 현황 데이터 관리

Operational Insights는 주기적으로 데이터를 수집하여 Operational Insights에 보냅니다.  **사용량** 대시보드를 사용하여 Operational Insights 서비스에 보내지는 데이터 크기를 볼 수 있습니다. **사용량** 대시보드는 인텔리전스 팩에서 매일 전송되는 데이터 크기 및 관리 그룹이 데이터를 보내는 빈도도 표시합니다.

>[AZURE.NOTE] 무료 계정이 있는 경우, 매일 Operational Insights 서비스에 500MB의 데이터를 전송하도록 제한됩니다. 일일 제한에 도달하면 데이터 분석이 중지되 고 다음날 시작 시 다시 재개됩니다.

Operational Insights의 **개요** 대시보드에서 **서버 및 사용량** 타일을 사용하여 사용량을 볼 수 있습니다.

![서버 및 사용량 타일의 이미지](./media/operational-insights-usage/overview-servers-usage.png)

일일 사용량 한도를 초과하거나 거의 한도에 도달한 경우, 선택적으로 인텔리전스 팩을 제거하여 Operational Insights 서비스에 보내는 데이터의 크기를 줄일 수 있습니다. 인텔리전스 팩을 제거하는 방법에 대한 자세한 내용은 [갤러리를 사용하여 인텔리전스 팩 추가 또는 제거 ](operational-insights-add-intelligence-packs.md)를 참조하세요.

Operations Manager 관리 그룹에서 Operational Insights 서비스에 데이터를 전송하는 데 문제가 있는 경우, 문제를 해결할 수 있거나 필요한 경우 Operational Insights에서 그룹을 제거할 수 있습니다.

![사용량 대시보드의 이미지](./media/operational-insights-usage/usage-dash.png)

**사용량** 대시보드에 다음 정보가 표시됩니다.

- 하루 평균 사용량

- 오늘까지의 각 인텔리전스 팩에 대한 데이터 사용

- 각 관리 그룹의 서비스가 데이터를 Operational Insights 서비스에 보내는 빈도

## 사용률 데이터를 작업하려면

1. **개요** 페이지에서 **서버 및 사용량** 타일을 클릭합니다.

2. **사용량** 대시보드에서 염려되는 영역을 표시하는 사용량 범주를 나타냅니다.

3. 불필요하게 할당량을 많이 사용 중인 인텔리전스 팩에 대한 데이터가 있으면 해당 인텔리전스 팩을 제거하는 것이 좋습니다.

## 문제를 해결하거나 관리 그룹을 제거하려면

1. **개요** 페이지에서 **서버 및 사용량** 타일을 클릭합니다.

2. **사용량** 대시보드에서, 데이터를 전송하지 않는 관리 그룹에 대한 정보를 봅니다.

3. 데이터를 전송하지 않는 관리 그룹이 있는 경우, **문제 해결**를 클릭하면 자세한 문제 해결 정보를 얻을 수 있습니다. 더 이상 문제를 보고하는 관리 그룹 및 모든 에이전트를 유지하지 않으려면 **제거**를 클릭합니다.


<!--HONumber=52-->