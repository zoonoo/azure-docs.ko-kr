<properties 
	pageTitle="정규 분포 웹 서비스 제품군 | Azure" 
	description="정규 분포 웹 서비스 제품군" 
	services="machine-learning" 
	documentationCenter="" 
	authors="jaymathe" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/11/2015" 
	ms.author="jaymathe"/> 

#정규 분포 웹 서비스 제품군



정규 분포 제품군은 샘플 웹 서비스, 특히 정규 분포 생성 및 처리를 도와주는 세 가지 서비스인 [생성기]( https://datamarket.azure.com/dataset/aml_labs/ndg7), [분위수 계산기]( https://datamarket.azure.com/dataset/aml_labs/ndq5), [확률 계산기]( https://datamarket.azure.com/dataset/aml_labs/ndp5)의 집합입니다. 서비스를 통해 무제한 정규 분포 시퀀스를 생성하고, 제공된 확률에서 분위수를 계산하고, 제공된 분위수에서 확률을 계산할 수 있습니다.  각 서비스는 선택된 서비스에 따라 다른 출력을 내보냅니다(아래 설명 참조). 정규 분포 제품군은 R stats 패키지에 포함된 R 함수 qnorm, rnorm 및 pnorm을 기반으로 합니다.

>사용자가 모바일 앱, 웹 사이트 또는 로컬 컴퓨터 등을 통해 이 웹 서비스를 사용할 수 있지만, 이 웹 서비스의 목적은 Azure 기계 학습을 사용하여 R 코드를 기반으로 한 웹 서비스를 만드는 방법의 예제를 제공하는 것이기도 합니다. Azure 기계 학습 스튜디오 내에서 R 코드 몇 줄을 포함하고 단추를 몇 번 클릭하면 R 코드를 사용하여 실험을 만들고 웹 서비스로 게시할 수 있습니다. 그런 다음 웹 서비스를 Azure 마켓플레이스에 게시하면 웹 서비스 작성자가 인프라를 설정하지 않고도 전 세계의 사용자와 장치에서 이러한 웹 서비스를 사용할 수 있습니다.  
 

##웹 서비스 사용
정규 분포 제품군에는 다음 3개 서비스가 포함됩니다.

###정규 분포 분위수 계산기:
이 서비스는 정규 분포의 인수 4개를 받아들여 연관된 변위치를 계산합니다.

입력 인수는 다음과 같습니다.

* p - 정규 분포를 통한 이벤트의 단일 확률 
* Mean - 정규 분포 평균
* SD - 정규 분포 표준 편차 
* Side - 분포의 아래쪽에 대한 L 및 분포의 위쪽에 대한 U

이 서비스에서는 제공된 확률과 연결된 계산된 분위수가 결과 값으로 표시됩니다.

###정규 분포 확률 계산기:
이 서비스는 정규 분포의 4개 인수를 수락하고 관련된 확률을 계산합니다.

입력 인수는 다음과 같습니다.

* q - 정규 분포를 통한 이벤트의 단일 분위수 
* Mean - 정규 분포 평균
* SD - 정규 분포 표준 편차 
* Side - 분포의 아래쪽에 대한 L 및 분포의 위쪽에 대한 U

이 서비스에서는 제공된 분위수와 연결된 계산된 확률이 결과 값으로 표시됩니다.

###정규 분포 생성기
이 서비스는 정규 분포의 3개 인수를 수락하고 정규적으로 분포되는 숫자의 임의 시퀀스를 생성합니다. 
요청 내에서 다음과 같은 인수가 서비스에 제공되어야 합니다.

* n - 관찰 수 
* mean - 정규 분포 평균
* sd - 정규 분포 표준 편차 

서비스의 출력은 mean 및 sd 인수에 따른 정규 분포를 사용한 길이 n의 시퀀스입니다.

>Microsoft Azure 마켓플레이스에서 호스트되는 이 서비스는 OData 서비스이고 POST 또는 GET 메서드를 통해 호출할 수 있습니다. 

서비스를 자동화된 방식으로 사용하는 방법은 여러 가지가 있습니다(예제 앱은 여기에 있음: [생성기](http://microsoftazuremachinelearning.azurewebsites.net/NormalDistributionGenerator.aspx),
[확률 계산기](http://microsoftazuremachinelearning.azurewebsites.net/NormalDistributionProbabilityCalculator.aspx),
[분위수 계산기](http://microsoftazuremachinelearning.azurewebsites.net/NormalDistributionQuantileCalculator.aspx).

###웹 서비스 사용의 C# 코드 시작:

	public class Input{
	public double Recency;
	public double Frequency;
	public double Monetary;
	public double Time;
	public double Class;
	}

	public AuthenticationHeaderValue CreateBasicHeader(string username, string password)
    {
        byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(username + ":" + password);
        System.Diagnostics.Debug.WriteLine("AuthenticationHeaderValue" + new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray)));
        return new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray));
    }
       
	void Main()
	{
  	var input = new Input(){Recency =1, Frequency=0,Monetary=0,Time=1, Class= 0};
	var json = JsonConvert.SerializeObject(input);
	var acitionUri =  "PutAPIURLHere,e.g.https://api.datamarket.azure.com/..../v1/Score";
       
  	var httpClient = new HttpClient();
   	httpClient.DefaultRequestHeaders.Authorization = CreateBasicHeader("PutEmailAddressHere","ChangeToAPIKey");
   	httpClient.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
  	var query = httpClient.PostAsync(acitionUri,new StringContent(json));
  	var result = query.Result.Content;
  	var scoreResult = result.ReadAsStringAsync().Result;
  	scoreResult.Dump();
	}

##웹 서비스 만들기 
>이 웹 서비스는 Azure 기계 학습을 사용하여 만들었습니다. 무료 평가판 및 실험을 만들고 [웹 서비스를 게시](http://azure.microsoft.com/documentation/articles/machine-learning-publish-web-service-to-azure-marketplace/)하는 방법에 대한 소개 비디오는 [azure.com/ml](http://azure.com/ml)을 참조하세요. 

다음은 웹 서비스를 만든 실험과 실험 내 각 모듈의 예제 코드의 스크린샷입니다.

###정규 분포 분위수 계산기:
실험 흐름
![Experiment flow][2]
 
	#data schema with example data (replaced with data from web service)
	data.set=data.frame(p=0.1,mean=0,sd=1,side='L');
	maml.mapOutputPort("data.set"); #send data to output port
	# Map 1-based optional input ports to variables
	dataset1 <- maml.mapInputPort(1) # class: data.frame

	param = dataset1
	if (param$p < 0 ) {
	print('Bad input: p must be between 0 and 1')
	param$p = 0
	} else if (param$p > 1) {
	print('Bad input: p must be between 0 and 1')
	param$p = 1
	}
	q = qnorm(param$p,mean=param$mean,sd=param$sd)

	if (param$side == 'U'){
	q = 2* param$mean - q
	} else if (param$side =='L') {
	q = q
	} else {
	print("Invalid side choice")
	}

	output = as.data.frame(q)
	# Select data.frame to be sent to the output Dataset port
	maml.mapOutputPort("output");
	
###정규 분포 확률 계산기:
실험 흐름
![Experiment flow][3]
 
 	#data schema with example data (replaced with data from web service)
	data.set=data.frame(q=-1,mean=0,sd=1,side='L');
	maml.mapOutputPort("data.set"); #send data to output port
	# Map 1-based optional input ports to variables
	dataset1 <- maml.mapInputPort(1) # class: data.frame

	param = dataset1
	prob = pnorm(param$q,mean=param$mean,sd=param$sd)

	if (param$side == 'U'){
	prob = 1 - prob
	} else if (param$side =='B') {
	prob = ifelse(prob<=0.5,prob * 2, 1)
	} else if (param$side =='L') {
	prob = prob
	} else {
	print("Invalid side choice")
	}

	output = as.data.frame(prob)
	# Select data.frame to be sent to the output Dataset port
	maml.mapOutputPort("output");
	
###정규 분포 생성기:
실험 흐름
![Experiment flow][4]

	#data schema with example data (replaced with data from web service)
	data.set=data.frame(n=50,mean=0,sd=1);
	maml.mapOutputPort("data.set"); #send data to output port
	# Map 1-based optional input ports to variables
	dataset1 <- maml.mapInputPort(1) # class: data.frame

	param = dataset1
	dist = rnorm(param$n,mean=param$mean,sd=param$sd)

	output = as.data.frame(t(dist))

	# Select data.frame to be sent to the output Dataset port
	maml.mapOutputPort("output");

##제한 사항 

이는 정규 분포와 관련된 매우 간단한 예제입니다. 위의 예제 코드에서 볼 수 있듯이 오류 catch가 거의 구현되어 있지 않습니다.

##FAQ
웹 서비스 사용 또는 마켓플레이스 게시 방법과 관련한 질문과 대답은 [여기](http://azure.microsoft.com/documentation/articles/machine-learning-marketplace-faq)를 참조하세요.

[1]: ./media/machine-learning-r-csharp-normal-distribution/normal-img1.png
[2]: ./media/machine-learning-r-csharp-normal-distribution/normal-img2.png
[3]: ./media/machine-learning-r-csharp-normal-distribution/normal-img3.png
[4]: ./media/machine-learning-r-csharp-normal-distribution/normal-img4.png

<!--HONumber=46--> 
 