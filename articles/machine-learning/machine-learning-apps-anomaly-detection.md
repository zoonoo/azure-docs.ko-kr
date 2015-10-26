<properties 
	pageTitle="기계 학습 앱: 이상 감지 서비스 | Microsoft Azure" 
	description="이상 감지 API는 Microsoft Azure 기계 학습을 사용하여 빌드한 예로서, 시간 간격이 불균일한 숫자 값이 있는 시계열 데이터에서 이상을 감지합니다." 
	services="machine-learning" 
	documentationCenter="" 
	authors="LuisCabrer" 
	manager="paulettm"
	editor="cgronlun" />

<tags 
	ms.service="machine-learning" 
	ms.devlang="na" 
	ms.topic="reference" 
	ms.tgt_pltfrm="na" 
	ms.workload="multiple" 
	ms.date="07/28/2015" 
	ms.author="luisca"/>


# 기계 학습 이상 감지 서비스#

##개요

이상 감지 API는 Azure 기계 학습을 사용하여 빌드한 예로서, 시간 간격이 불균일한 숫자 값이 있는 시계열 데이터에서 이상을 감지합니다.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

이 이상 감지 서비스는 시계열 데이터에서 다음과 같은 여러 유형의 이상을 감지할 수 있습니다.

1. 긍정적인 추세 및 부정적인 추세: 예를 들어 계산에서 메모리 사용량을 모니터링할 때 상승 추세는 메모리 누수를 나타냅니다.

2. 값의 동적 범위 증가: 예를 들어 서비스에서 발생하는 예외를 모니터링할 때 값의 동적 범위 증가는 서비스 상태의 불안정성을 나타낼 수 있습니다.

3. 급증 및 급락: 예를 들어 서비스 로그인 실패 횟수 또는 전자 상거래 사이트의 체크 아웃 수를 모니터링할 때 급증 또는 급락은 이상 동작을 나타낼 수 있습니다.


이러한 감지기는 시간에 따른 값의 변화를 추적하여 해당 값의 지속적인 변화를 보고합니다. 임시 임계값을 조정할 필요 없이 해당 점수를 사용하여 가양성 비율을 제어할 수 있습니다. 이상 감지 API는 시간별 KPI, 검색 횟수, 클릭 수 등의 사용 현황 메트릭, 메모리, CPU, 파일 읽기 수 등의 시간별 성능 카운터 등을 추적하여 서비스를 모니터링하는 것과 같은 여러 시나리오에 유용합니다.

##API 정의

이 서비스는 웹 또는 모바일 응용 프로그램, R, Python, Excel 등 다양한 방식으로 사용될 수 있는 REST 기반 API over HTTPS를 제공합니다. [Azure 웹 응용 프로그램](http://anomalydetection-aml.azurewebsites.net/)은 데이터에 대해 이상 감지 웹 서비스를 실행하고 결과를 시각화하도록 도와줍니다.

또한 REST API 호출을 통해 시계열 데이터를 이 서비스로 보내 위에 설명된 세 가지 이상 유형의 조합을 실행하도록 할 수 있습니다. 이 서비스는 비즈니스 요구 사항에 맞게 원활하게 확장되며 99.95%의 SLA를 제공하는 AzureML 기계 학습 플랫폼에서 실행됩니다.

아래 그림에는 위 프레임워크를 사용하여 시계열에서 감지한 이상에 대한 예가 나와 있습니다. 시계열에 두 개의 수준 변화와 세 개의 급증이 있습니다. 빨간색 점은 수준 변화가 감지된 시간을 표시하며, 빨간색 위쪽 화살표는 감지된 급증을 표시합니다.


![][1]

##입력

이 API에서는 두 가지 입력 매개 변수를 사용합니다.

1. "data"는 t1=v1;t2=v2;... 형식의 입력 시계열을 나타냅니다. 

 
	샘플 데이터:
		
		"9/21/2014 11:05:00 AM=3;9/21/2014 11:10:00 AM=9.09;9/21/2014 11:15:00 AM=0;"

2. "SpikeDetector.TukeyThresh=3; SpikeDetector.ZscoreThresh=3"으로 설정된 "params"는 급증 감지기의 민감도를 제어합니다. 값이 높을수록 더 높은 급증을 포착합니다.

	위에서 언급한 입력 매개 변수를 포함하는 샘플 URL:

		https://api.datamarket.azure.com/data.ashx/aml_labs/anomalydetection/v1/Score?data=%279%2F21%2F2014%2011%3A05%3A00%20AM%3D3%3B9%2F21%2F2014%2011%3A10%3A00%20AM%3D9.09%3B9%2F21%2F2014%2011%3A15%3A00%20AM%3D0%3B%27&params=%27SpikeDetector.TukeyThresh%3D3%3B%20SpikeDetector.ZscoreThresh%3D3%27



###출력###

이 API는 시계열 데이터에서 이러한 감지기를 실행하여 각 시점에서 이상 점수를 반환합니다. [https://adresultparser.codeplex.com/](https://adresultparser.codeplex.com/)에 표시된 대로 간단한 구문 분석기를 사용하여 이 출력을 구문 분석할 수 있습니다. 여기에서는 API에 연결하여 출력을 구문 분석하는 방법을 보여 주는 샘플 코드를 제공합니다. 생성된 알림을 대시보드에서 시각화하거나 수정 작업을 수행할 수 있는 전문가에게 전달할 수 있습니다.

위의 샘플 입력에 대한 샘플 출력은 다음과 같습니다.

	"Time,Data,TSpike,ZSpike,Martingale values,Alert indicator,Martingale values(2),Alert indicator(2),;9/21/2014 11:05:00 AM,3,0,0,-0.687952590518378,0,-0.687952590518378,0,;9/21/2014 11:10:00 AM,9.09,0,0,-1.07030497733224,0,-0.884548154298423,0,;9/21/2014 11:15:00 AM,0,0,0,-1.05186237440962,0,-1.173800281031,0,;"

이는 다음 표로 표시됩니다.

Time|Data|Tspike|Zspike|Martingale values|Alert indicator|Martingale values (2)|Alert indicator (2)
---|---|---|---|---|---|---|---
9/21/2014 11:05|3|0|0|-0.687952591|0|-0.687952591|0|   
9/21/2014 11:10|9\.09|0|0|-1.070304977|0|-0.884548154|0|    
9/21/2014 11:15|0|0|0|-1.051862374|0|-1.1738002814|0|   
   

[1]: ./media/machine-learning-apps-anomaly-detection/anomaly-detection.jpg

 

 

<!---HONumber=Oct15_HO3-->