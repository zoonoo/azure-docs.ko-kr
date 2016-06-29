<properties 
	pageTitle="Application Insights 개발팀에서 기술 지원을 받는 방법 | Microsoft Azure" 
	description="Application Insights 개발팀으로부터 특수한 지원이 필요한 경우 지원을 받기 위해 세부 정보를 제출하는 방법입니다." 
	services="application-insights" 
    documentationCenter=""
	authors="alexbulankou" 
	manager="douge"/>
 
<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/01/2016" 
	ms.author="albulank"/>
	
# Application Insights 개발팀에서 기술 지원을 받는 방법
	
[Visual Studio Application Insights](app-insights-overview.md)에 대한 기술적 문제가 있는 경우 도움을 얻을 수 있는 옵션이 있습니다.

## 1\. 문서 확인

* 누락된 데이터가 있나요? 확인: [샘플링](app-insights-sampling.md), [할당량 및 제한](app-insights-pricing.md).
* 문제 해결: [ASP.NET](app-insights-troubleshoot-faq.md) | [Java](app-insights-java-troubleshoot.md)

## 2\. 포럼 검색

* [MSDN 포럼](https://social.msdn.microsoft.com/Forums/vstudio/home?forum=ApplicationInsights)
* [StackOverflow](http://stackoverflow.com/questions/tagged/ms-application-insights)

## 3\. Azure 지원 플랜

개발자가 특정 사례를 조사하도록 하려는 상황이 있습니다.

[Microsoft Azure에 대한 지원 플랜](https://azure.microsoft.com/support/plans/)이 있는 경우 [지원 티켓을 열면](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) 됩니다.

## 4\. Application Insights 팀에 문의

지원 플랜이 없는 경우 일반 공급 기준이 마련됨에 따라 개발팀에서 Application Insights 고객에게 최대한의 지원을 제공합니다. **새로운 지원 옵션**을 도입 중: Azure 포털에서 피드백 양식을 제출하여 사례에 대한 설명을 제공할 수 있으며 Application Insights 팀의 개발자가 다시 연락하여 문제 해결을 지원합니다.


1. [Application Insights 포털](https://portal.azure.com)의 오른쪽 위에서 웃는 얼굴을 클릭합니다.  

    ![피드백 단추](./media/app-insights-get-dev-support/01.png)

2. 설명 상자에서 첫 번째 줄로 **AppInsights**를 지정한 후 다음 정보를 포함해야 합니다.

    ```

    AppInsights   
    ikey: <instrumentation key>   
    sdk: <SDK that you are using, including name and version>  
    issue: <please describe the problem you are having>

    ```   

    ![피드백 대화 상자](./media/app-insights-get-dev-support/02.png)

3. "예, 전자 메일을 받아도 좋습니다."를 선택합니다.

    ![섹션 제출](./media/app-insights-get-dev-support/03.png)

Application Insights 팀의 엔지니어가 곧 연락드립니다. 이 서비스를 최상의 기준으로 제공하고 있으므로 현재 공식 SLA는 제공할 수 없습니다.

<!---HONumber=AcomDC_0615_2016-->