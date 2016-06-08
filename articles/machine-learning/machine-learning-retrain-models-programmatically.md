<properties
	pageTitle="프로그래밍 방식으로 기계 학습 모델 다시 학습 | Microsoft Azure"
	description="Azure 기계 학습에서 프로그래밍 방식으로 모델을 다시 학습하고 새로 학습된 모델을 사용하도록 웹 서비스를 업데이트하는 방법을 알아봅니다."
	services="machine-learning"
	documentationCenter=""
	authors="raymondlaghaeian"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/23/2016"
	ms.author="raymondl;garye"/>


#프로그래밍 방식으로 기계 학습 모델 다시 학습  

Azure 기계 학습에서 기계 학습 모델 운영 프로세스의 일부로 모델을 학습 및 저장한 다음 서술적 웹 서비스를 만드는 데 사용해야 합니다. 그러면 웹 사이트, 대시보드 및 모바일 앱에서 웹 서비스를 사용할 수 있습니다.

첫 번째 단계에서 만든 모델을 새 데이터로 다시 학습해야 하는 경우가 많습니다. 이전에는 Azure ML UI를 통해서만 이 작업이 가능했지만 이제는 프로그래밍 방식의 다시 학습 API 기능이 도입되어 다시 학습 API를 사용하여 프로그래밍 방식으로 모델을 다시 학습시키고 새로 학습된 모델로 웹 서비스를 업데이트할 수 있습니다.

이 문서에서는 위 프로세스를 설명하고 다시 학습 API를 사용하는 방법을 보여 줍니다.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]


##다시 학습 이유: 문제 정의  
ML 학습 프로세스의 일부로, 데이터 집합을 사용하여 모델을 학습합니다. 새 데이터를 사용할 수 있거나, API 소비자에게 모델을 학습할 고유한 데이터가 있거나, 데이터를 필터링하고 데이터 하위 집합으로 모델을 학습해야 하는 시나리오 등에서는 모델을 다시 학습해야 합니다.

이러한 시나리오에서 개발자나 API 소비자는 프로그래밍 방식의 API를 통해 일회성이나 주기적으로 고유한 데이터를 사용하여 모델을 다시 학습할 수 있는 클라이언트를 만들 수 있습니다. 그런 후에 다시 학습의 결과를 평가하고 새로 학습된 모델을 사용하도록 웹 서비스 API를 업데이트할 수 있습니다.

##다시 학습하는 방법: 종단 간 프로세스  
처음에는 프로세스에 학습 실험 및 웹 서비스로 게시된 서술적 실험이 구성 요소로 포함됩니다. 학습된 모델을 다시 학습할 수 있으려면 학습 실험도 학습된 모델의 출력을 사용하여 웹 서비스로 게시해야 합니다. 그러면 API가 다시 학습을 위해 모델에 액세스할 수 있습니다. 다시 학습을 설정하는 프로세스에는 다음 단계가 포함됩니다.

![][1]

다이어그램 1: 다시 학습 프로세스 개요

1. *학습 실험 만들* 이 예제에서는 Azure ML 샘플 실험의 "샘플 5(이진 분류에 대한 학습, 테스트, 평가: 성인 데이터 집합)"를 사용합니다. 아래에서 볼 수 있듯이, 일부 모듈을 제거하여 샘플을 단순화했습니다. 또한 실험의 이름을 “Census Model”로 지정했습니다.

 	![][2]

	이러한 작업이 완료되면 이제 화면 맨 아래에서 실행을 클릭하여 이 실험을 실행할 수 있습니다.  
2. *점수 매기기 실험을 만들고 웹 서비스로 게시*  

	![][3]

	실험 실행이 완료되면 서술적 실험 만들기를 클릭합니다. 서술적 실험이 만들어지고 모델이 학습된 모델로 저장된 다음 웹 서비스 입력 및 출력 모듈이 아래와 같이 추가됩니다. 실행을 클릭합니다.

	실험 실행이 완료된 후 "웹 서비스 게시"를 클릭하면 서술적 실험이 웹 서비스로 게시되고 기본 끝점이 만들어집니다. 이 웹 서비스의 학습된 모델은 아래와 같이 업데이트할 수 있습니다. 이 끝점에 대한 세부 정보가 화면에 표시됩니다.
3. *학습 실험을 웹 서비스로 게시*: 학습된 모델을 다시 학습하려면 위의 1단계에서 웹 서비스로 만든 학습 실험을 게시해야 합니다. 이 웹 서비스가 학습된 모델을 새로 생성할 수 있으려면 웹 서비스 출력 모듈이 [학습 모델][train-model] 모듈에 연결되어야 합니다. 왼쪽 창에서 실험 아이콘을 클릭한 다음 Census Model이라는 실험을 클릭하여 학습 실험으로 돌아갑니다.  

	그런 다음 웹 서비스 입력 모듈 1개와 웹 서비스 출력 모듈 2개를 워크플로에 추가합니다. 학습 모델에 대한 웹 서비스 출력에 새로 학습된 모델이 표시됩니다. 평가 모델에 연결된 출력에는 해당 모듈의 모델 평가 출력이 반환됩니다.

	이제 실행을 클릭할 수 있습니다. 실험 실행이 완료된 후 결과 워크플로는 다음과 같이 표시됩니다.

	![][4]

	웹 서비스 배포 단추를 클릭한 다음 예를 클릭합니다. 학습 실험이 학습된 모델 및 모델 평가 결과를 생성하는 웹 서비스로 배포됩니다. 웹 서비스 대시보드가 일괄 처리 실행을 위한 API 키 및 API 도움말 페이지와 함께 표시됩니다. 학습된 모델을 만들 때는 일괄 처리 실행 방법만 사용할 수 있습니다.  
4. *새 끝점 추가* 위의 2단계에서 게시한 서술적 웹 서비스는 기본 점수 매기기 끝점입니다. 기본 끝점은 원래 학습 및 점수 매기기 실험과 동기화 상태를 유지하므로 기본 끝점의 학습된 모델을 바꿀 수 없습니다. 업데이트할 수 있는 모델을 사용하여 새 점수 매기기 끝점을 만들려면 Azure 클래식 포털을 방문하여 끝점 추가를 클릭합니다(자세한 내용은 [여기](machine-learning-create-endpoint.md) 참조). [여기](https://github.com/raymondlaghaeian/AML_EndpointMgmt/blob/master/Program.cs)에 제공된 샘플 코드를 사용하여 점수 매기기 끝점을 추가할 수도 있습니다.

5. *새로운 데이터 및 BES를 사용하여 모델 다시 학습* 다시 학습 API를 호출하려면 Visual Studio에서 새로운 C# 콘솔 응용 프로그램 만듭니다(새로 만들기->프로젝트->Windows 데스크톱->콘솔 응용 프로그램).

	위의 3단계에서 만든, 일괄 처리 실행을 위한 학습 웹 서비스 API 도움말 페이지에서 샘플 C# 코드를 복사한 다음 네임스페이스를 그대로 유지하여 Program.cs 파일에 붙여넣습니다.

	샘플 코드에는 업데이트가 필요한 코드 부분을 나타내는 주석이 있습니다. 또한 요청 페이로드에 "output1" 위치를 지정할 때 "RelativeLocation"의 파일 확장명을 다음처럼 ".ileaner"로 변경해야 합니다.

	```c#
	Outputs = new Dictionary<string, AzureBlobDataReference>()
	{
		{
			"output1",
			new AzureBlobDataReference()
			{
				ConnectionString = "DefaultEndpointsProtocol=https;AccountName=mystorageacct;AccountKey=Dx9WbMIThAvXRQWap/aLnxT9LV5txxw==",
				RelativeLocation = "mycontainer/output1results.ilearner"
			}
		},
	},
	```
	1. Azure 저장소 정보 제공 BES 샘플 코드는 로컬 드라이브(예: "C:\\temp\\CensusIpnput.csv")에서 Azure 저장소로 파일을 업로드하고 처리한 다음 결과를 Azure 저장소에 씁니다.  

		이렇게 하려면 Azure 클래식 포털에서 저장소 계정에 대한 저장소 계정 이름, 키 및 컨테이너 정보를 검색한 다음 여기서 코드를 업데이트해야 합니다. 또한 코드에 지정한 위치에서 입력 파일을 사용할 수 있는지 확인해야 합니다.

		이 학습 실험은 두 개의 출력으로 설정되었으므로 아래와 같이 두 출력에 대한 저장소 위치 정보가 결과에 모두 포함됩니다. "output1"은 학습된 모델의 출력이고, "output2"는 평가 모델의 출력입니다. 또한 학습된 모델(Output1)에 대한 출력의 파일 확장명은 ".csv"가 아닌 ".ilearner"입니다.

		![][6]

6. *다시 학습 결과 평가* "output2"에 대한 위 출력 결과의 BaseLocation, RelativeLocation, SasBlobToken을 조합하여 브라우저 주소 표시줄에 전체 URL을 붙여넣어 다시 학습된 모델의 성능 결과를 확인할 수 있습니다.

	이 결과는 새로 학습된 모델이 기존 모델을 대체할 만큼 성능이 뛰어난지 여부를 알려줍니다.

7. *추가된 끝점의 학습된 모델 업데이트* 프로세스를 완료하려면 위의 4단계에서 만든 서술적 (점수 매기기) 끝점의 학습된 모델을 업데이트해야 합니다.

	(Azure 포털을 사용하여 새 끝점을 추가한 경우 새 끝점의 이름을 클릭한 다음 UpdateResource 링크를 클릭하여 끝점의 모델을 업데이트하는 데 필요한 URL을 가져올 수 있습니다. 코드를 사용하는 끝점을 추가한 경우 해당 호출의 출력에 끝점 URL이 포함됩니다.)

	위의 BES 출력은 학습된 모델 위치 정보를 포함하는 "output1"에 대한 다시 학습 결과 정보를 보여 줍니다. 이제 이 학습된 모델을 사용하여 위의 4단계에서 만든 점수 매기기 끝점을 업데이트해야 합니다. 샘플 코드 다음과 같습니다.

	```C#
	private async Task OverwriteModel()
	{
		var resourceLocations = new
		{
			Resources = new[]
			{
				new
				{
					Name = "Census Model [trained model]",
					Location = new AzureBlobDataReference()
					{
						BaseLocation = "https://esintussouthsus.blob.core.windows.net/",
						RelativeLocation = "your endpoint relative location", //from the output, for example: “experimentoutput/8946abfd-79d6-4438-89a9-3e5d109183/8946abfd-79d6-4438-89a9-3e5d109183.ilearner”
						SasBlobToken = "your endpoint SAS blob token" //from the output, for example: “?sv=2013-08-15&sr=c&sig=37lTTfngRwxCcf94%3D&st=2015-01-30T22%3A53%3A06Z&se=2015-01-31T22%3A58%3A06Z&sp=rl”
					}
				}
			}
		};

		using (var client = new HttpClient())
		{
			client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", apiKey);

			using (var request = new HttpRequestMessage(new HttpMethod("PATCH"), endpointUrl))
			{
				request.Content = new StringContent(JsonConvert.SerializeObject(resourceLocations), System.Text.Encoding.UTF8, "application/json");
				HttpResponseMessage response = await client.SendAsync(request);

				if (!response.IsSuccessStatusCode)
				{
					await WriteFailedResponse(response);
				}

				// Do what you want with a successful response here.
			}
		}
	}
	```

	이 호출에 대한 "apiKey" 및 "endpointUrl"이 끝점 대시보드에 표시됩니다. 리소스의 "Name" 매개 변수가 예측 실험의 저장된 학습된 모델의 이름과 일치해야 합니다.
	
	SAS 토큰은 1시간(55분) 후에 만료됩니다. 새 토큰을 가져오려면 작업 ID를 가진 GET을 수행해야 합니다.

	이 호출이 성공하면 약 15초 내에 새 끝점이 다시 학습된 모델을 사용하기 시작합니다.

##요약  
다시 학습 API를 통해 예측 웹 서비스의 학습된 모델을 업데이트하여 새 데이터를 사용한 주기적 모델 다시 학습 또는 고객이 고유한 데이터를 사용하여 모델을 다시 학습할 수 있도록 고객에게 모델 배포와 같은 시나리오를 지원할 수 있습니다.

[1]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE01.png
[2]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE02.png
[3]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE03.png
[4]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE04.png
[5]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE05.png
[6]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE06.png


<!-- Module References -->
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/

<!---HONumber=AcomDC_0525_2016-->