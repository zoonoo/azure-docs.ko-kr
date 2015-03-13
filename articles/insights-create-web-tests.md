<properties 
	pageTitle="웹 테스트를 만드는 방법" 
	description="Azure에서 웹 테스트를 만드는 방법에 대해 알아봅니다." 
	services="application-insights" 
	authors="alancameronwills" 
	manager="kamrani"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="2015-01-17" 
	ms.author="awills"/>

# Microsoft Azure 웹 사이트에 대한 웹 테스트
Azure 웹 사이트가 여전히 실행 중입니까? 빠른 속도로, 제대로 응답하고 있습니까? 웹 테스트를 구성하여 정기적으로 웹 사이트를 테스트합니다. 사이트가 가동 중지하거나 사이트 응답이 느리거나 잘못된 경우, 메일 경고를 수신합니다. 또한 시간 경과에 따라 사이트 가용성 및 응답성을 보여 주는 그래프를 확인할 수 있습니다.  

*다른 웹 사이트를 테스트하시겠습니까? 비 Azure 웹 앱의 경우 [Application Insights 웹 테스트][가용성]을 사용하세요.*

![Browse Hub](./media/insights-create-web-tests/Inisghts_WebTestBlade.png)

Basic 또는 Standard 요금제를 사용하는 Azure 웹 사이트에 대해 가용성 모니터링을 설정할 수 있습니다.  최대 3개의 웹 테스트를 만들고 최대 3개의 지역에서 각 테스트를 실행할 수 있습니다. 웹 사이트를 어떤 방식으로든 변경할 필요가 없습니다.

배포 또는 알 수 없는 중단 시간 동안 전체적인 가용성이 영향을 받지 않도록 웹 테스트를 일시 중지할 수도 있습니다.  전체적인 가용성은 선택한 여러 위치를 포함하여 모든 웹 테스트에 대해 계산됩니다.

## 웹 테스트를 설정하는 방법
1. 웹 테스트를 구성하려면 먼저 웹 사이트가 **Basic** 또는 **Standard**인지 확인합니다.
2. 그런 다음, **웹 사이트** 블레이드에서 **웹 테스트** 파트를 선택합니다.  
    ![Configure Web Tests](./media/insights-create-web-tests/Insights_ConfigurePart.png)
3. **Create web test** 블레이드에서 웹 테스트 이름을 지정하고 테스트를 실행할 URL을 지정합니다.  
    ![Create Web Test](./media/insights-create-web-tests/Insights_CreateTest.png)
4. 그런 다음, 8개 지역 중 최대 3개를 선택합니다.
5. HTTP 상태 코드 확인 또는 사이트 자체에 포함된 문자열을 포함한 성공 조건을 지정합니다.
6. 그런 다음, 민감도 및 메일 수신자를 포함한 경고 설정을 선택합니다.  
    ![Alerts](./media/insights-create-web-tests/Inisghts_AlertCreation.png)
    - 높은 민감도는 단 하나의 위치에서 테스트 실패가 감지될 때마다 경고를 만듭니다.
    - 보통 민감도는 10분 내에 위치 중 절반 이상에서 실패가 감지되어야 경고를 만듭니다.
    - 낮은 민감도는 15분 내에 모든 위치의 테스트에서 실패가 감지되어야 경고를 만듭니다.

마치면 **만들기** 단추를 클릭합니다. 웹 테스트가 만들어진 후에는 지정된 위치에서 5분마다 테스트가 실행되므로, 데이터가 표시되는 동안 시간이 약간 걸릴 수 있습니다.

### 지역을 고려하는 이유
해당 위치에서 웹 사이트에 요청을 보내며, 이는 사용자들이 세계 여러 위치에서 사이트에 액세스하는 방식과 동일합니다. 사이트가 미국에서는 사용 불가능하지만 유럽에서는 여전히 사용 가능한 경우 서버에 문제가 있는 것이 아니라 네트워크 문제임을 알 수 있습니다.

### 성공 조건 사용
일반적으로 HTTP 상태 코드가 200인지 테스트합니다. 이 코드는 서버가 URI를 인식했으며 페이지를 반환했음을 나타냅니다.

일치하는 콘텐츠 문자열에 와일드카드를 사용할 수 없지만 일반 텍스트를 테스트할 수 있습니다.

## 사이트 작동 중단
웹 테스트가 성공 조건에 부합하지 않는 경우 실패한 테스트로 표시되며 웹 사이트의 전체적인 가용성을 낮춥니다. 실패한 테스트(및 성공한 테스트)는 특정 웹 테스트 블레이드에서 분산형 차트에 표시됩니다.  

![Failed Test](./media/insights-create-web-tests/Insights_FailedWebTest.png)

실패한 테스트를 분석하여 실패한 이유를 확인할 수 있습니다.  실패한 웹 테스트를 상세하게 확인하고 Visual Studio 웹 테스트 결과 파일을 다운로드한 후 열어 테스트 실패 이유를 분석하고 이해합니다.

![Open in VS](./media/insights-create-web-tests/Insights_OpenInVS.png)

[availability]: ../app-insights-monitor-web-app-availability/

<!--HONumber=46--> 
