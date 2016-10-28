<properties
	pageTitle="기계 학습용 PowerShell 모듈 | Microsoft Azure"
	description="Azure 기계 학습용 PowerShell 모듈은 공개 미리 보기 모드로 사용할 수 있습니다. PowerShell을 사용하여 작업 영역, 실험, 웹 서비스 등을 만들고 관리합니다."
	keywords="실험, 선형 회귀, 기계 학습 알고리즘, 기계 학습 자습서, 예측 모델링 기술, 데이터 과학 실험"
	services="machine-learning"
	documentationCenter=""
	authors="hning86"
	manager="jhubbard"
	editor="cgronlun"/>

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="08/05/2016"
	ms.author="garye;haining"/>

# Microsoft Azure 기계 학습용 PowerShell 모듈

Azure 기계 학습용 PowerShell 모듈은 Windows PowerShell을 사용하여 작업 영역, 실험, 데이터 집합, 웹 서비스 등을 관리할 수 있는 강력한 도구입니다.

[https://aka.ms/amlps](https://aka.ms/amlps)에서 설명서를 보고 전체 소스 코드와 함께 모듈을 다운로드할 수 있습니다.

## 기계 학습 PowerShell 모듈이란?

기계 학습 PowerShell 모듈은 Windows PowerShell에서 Azure 기계 학습 작업 영역, 실험, 데이터 집합, 웹 서비스 및 웹 서비스 끝점을 완벽하게 관리할 수 있는 .NET 기반 DLL 모듈입니다. 모듈과 함께 명확하게 구분된 [C# API 계층](https://github.com/hning86/azuremlps/blob/master/code/AzureMLSDK.cs)을 포함하는 전체 소스 코드를 다운로드할 수 있습니다. 즉, 고유한 .NET 프로젝트에서 이 DLL을 참조하고 .NET 코드를 통해 Azure 기계 학습을 관리할 수 있습니다. 또한 DLL은 자주 사용하는 클라이언트에서 직접 활용할 수 있는 기본 REST API에 따라 달라집니다.

## PowerShell 모듈을 사용해서 무엇을 할 수 있나요?

다음은 이 PowerShell 모듈로 수행할 수 있는 일부 작업입니다. 다음을 포함한 여러 기능은 [전체 설명서](https://aka.ms/amlps)를 확인합니다.

- 관리 인증서를 사용한 새 작업 영역 프로비전([New-AmlWorkspace](https://github.com/hning86/azuremlps#new-amlworkspace))
- 실험 그래프를 나타내는 JSON 파일을 내보내기 및 가져오기([Export-AmlExperimentGraph](https://github.com/hning86/azuremlps#export-amlexperimentgraph) 및 [Import-AmlExperimentGraph](https://github.com/hning86/azuremlps#import-amlexperimentgraph))
- 실험 실행([Start-AmlExperiment](https://github.com/hning86/azuremlps#start-amlexperiment))
- 예측 실험에서 웹 서비스 만들기([New-AmlWebService](https://github.com/hning86/azuremlps#new-amlwebservice))
- 게시된 웹 서비스에 새 끝점 만들기([Add-AmlWebServiceEndpoint](https://github.com/hning86/azuremlps#add-amlwebserviceendpoint))
- RRS 또는 BES 웹 서비스 끝점 호출([Invoke-AmlWebServiceRRSEndpoint](https://github.com/hning86/azuremlps#invoke-amlwebservicerrsendpoint) 및 [Invoke-AmlWebServicBESEndpoint](https://github.com/hning86/azuremlps#invoke-amlwebservicebesendpoint))

기존 실험을 실행하기 위해 PowerShell을 사용하는 간단한 예는 다음과 같습니다.

		#Find the first Experiment named “xyz”
		$exp = (Get-AmlExperiment | where Description -eq ‘xyz’)[0]
		#Run the Experiment
		Start-AmlExperiment -ExperimentId $exp.ExperimentId 

매우 일반적으로 요청된 작업을 자동화하도록 PowerShell 모듈을 사용하는 방법에 대한 자세한 사용 사례는 [PowerShell을 사용하여 한 실험에서 여러 기계 학습 모델 및 웹 서비스 끝점 만들기](machine-learning-create-models-and-endpoints-with-powershell.md) 문서를 참조하세요.

## 어떻게 시작하나요?

기계 학습 PowerShell을 시작하려면 GitHub에서 [릴리스 패키지](https://github.com/hning86/azuremlps/releases)를 다운로드하고 [설치에 대한 지침](https://github.com/hning86/azuremlps/blob/master/README.md)을 따릅니다. 다운로드/압축 해제된 DLL의 차단을 해제한 다음 PowerShell 환경으로 가져와야 합니다. 대부분의 cmdlet에는 작업 영역 ID, 작업 영역 권한 부여 토큰 및 작업 영역이 위치한 Azure 지역을 제공해야 합니다. 기본 config.json 파일을 통해서 가장 간단하게 이를 제공할 수 있고 이는 설치 지침에서 자세히 다룹니다. 물론 Visual Studio를 사용하여 git 트리를 복제하고 로컬로 코드를 수정/컴파일할 수도 있습니다.

## 다음 단계

PowerShell 모듈은 이 미리 보기 기간 중에 계속 개선되고 확장됩니다. 더 많은 뉴스 및 정보는 [Cortana 인텔리전스 및 기계 학습 블로그](https://blogs.technet.microsoft.com/machinelearning/)를 확인하세요.

<!---HONumber=AcomDC_0914_2016-->