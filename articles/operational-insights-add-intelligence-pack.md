<properties 
    pageTitle="Operational Insights 인텔리전스 팩" 
    description="인텔리전스 팩을 사용하여 추가 기능을 Operational Insights에 추가할 수 있습니다." 
    services="operational-insights" 
    documentationCenter="" 
    authors="bandersmsft" 
    manager="jwhit" 
    editor=""/>

<tags 
    ms.service="operational-insights" 
    ms.workload="operational-insights" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="03/20/2015" 
    ms.author="banders"/>

# Operational Insights 인텔리전스 팩

Microsoft Azure Operational Insights는 기본 구성 평가 모듈을 포함합니다. 그러나 개요 페이지에서 인텔리전스 팩을 추가하여 추가 기능을 얻을 수 있습니다.

![인텔리전스 팩 아이콘 이미지](./media/operational-insights-add-intelligence-pack/overview-intelpacks.png)

인텔리전스 팩을 추가한 후 데이터는 인프라의 서버에서 수집하고 Operational Insights 서비스로 보내집니다. Operational Insights 서비스에서의 처리하려면 몇 분에서 몇 시간까지 걸릴 수 있습니다. 서비스가 데이터를 처리한 후 Operational Insights에서 볼 수 있습니다.

더 이상 필요 없는 경우는 인텔리전스 팩을 쉽게 제거할 수 있습니다. 인텔리전스 팩을 제거하면 해당 데이터가 Operational Insights에 보내지지 않아 일일 할당량에 의해 사용되는 데이터의 양이 줄어듭니다.

## Microsoft Monitoring Agent에서 지원하는 인텔리전스 팩

이때 Microsoft Monitoring Agent를 사용하여 Microsoft Azure Operational Insights에 직접 연결된 서버는 다음을 포함하여 사용 가능한 인텔리전스 팩의 대부분을 사용할 수 있습니다.

- [시스템 업데이트](operational-insights-updates.md)

- [로그 관리](operational-insights-log-collection.md)

- [맬웨어 방지](operational-insights-antimalware.md)

- [변경 내용 추적](operational-insights-change-tracking.md)

- [SQL 및 Active Directory 평가](operational-insights-assessment.md)

그러나 다음 인텔리전스 팩은 Microsoft Monitoring Agent로 지원되지  *않습*니다.

- [용량 관리](operational-insights-capacity.md)

- [구성 평가](operational-insights-configuration-assessment.md)

IIS 로그 수집은 다음이 설치된 컴퓨터에서 지원됩니다.

- Windows Server 2012

- Windows Server 2012 R2

### 인텔리전스 팩을 추가 하려면


1. Operational Insights의 개요 페이지에서 **인텔리전스 팩** 타일을 클릭합니다.


2. Operational Insights 인텔리전스 팩 갤러리 페이지에서 사용 가능한 각 인텔리전스 팩에 대해 배울 수 있습니다. Operational Insights에 추가할 인텔리전스 팩의 이름을 클릭합니다.


3. 선택한 인텔리전스 팩에 대한 페이지에서 인텔리전스 팩에 대한 자세한 정보가 표시됩니다. **추가**를 클릭합니다.


4. 확인 페이지를 클릭하여 **동의**를 클릭하여 개인정보 취급 방침 및 사용 조건에 동의 합니다.


5. 추가한 인텔리전스 팩의 새 타일이 Operational Insights의 개요 페이지에 표시되며 Operational Insights 서비스가 데이터를 처리한 후 새 타일을 사용하여 시작할 수 있습니다.




### 인텔리전스 팩을 제거하려면



1. Operational Insights의 개요 페이지에서 **인텔리전스 팩** 타일을 클릭합니다.


2. Operational Insights 인텔리전스 팩 갤러리 페이지에서, 제거할 인텔리전스 팩 아래에서 **제거**를 클릭합니다.


3. 확인 페이지에서 **예**를 클릭하여 인텔리전스 팩을 제거합니다.



<!--HONumber=52-->