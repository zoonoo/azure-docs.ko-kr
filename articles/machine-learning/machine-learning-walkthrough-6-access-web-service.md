<properties 
	pageTitle="6단계: 기계 학습 웹 서비스 액세스 | Microsoft Azure" 
	description="예측 솔루션 연습 개발의 6단계: 활성 Azure 기계 학습 웹 서비스 액세스" 
	services="machine-learning" 
	documentationCenter="" 
	authors="garyericson" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/10/2015" 
	ms.author="garye"/>


# 연습 6단계: Azure 기계 학습 웹 서비스 액세스

[Azure 기계 학습을 사용한 예측 솔루션 개발](machine-learning-walkthrough-develop-predictive-solution.md) 자습서의 마지막 단계입니다.


1.	[기계 학습 작업 영역 만들기](machine-learning-walkthrough-1-create-ml-workspace.md)
2.	[기존 데이터 업로드](machine-learning-walkthrough-2-upload-data.md)
3.	[새 실험 만들기](machine-learning-walkthrough-3-create-new-experiment.md)
4.	[모델 학습 및 평가](machine-learning-walkthrough-4-train-and-evaluate-models.md)
5.	[웹 서비스 게시](machine-learning-walkthrough-5-publish-web-service.md)
6.	**웹 서비스 액세스**

----------

웹 서비스를 활용하려면 사용자가 서비스에 데이터를 보내고 결과를 받을 수 있어야 합니다. 웹 서비스는 다음 두 방법의 하나로 데이터를 받고 반환할 수 있는 Azure 웹 서비스입니다.

-	**요청/응답** - 사용자가 HTTP 프로토콜을 사용하여 단일 신용 데이터 집합을 서비스에 보내고 서비스에서는 단일 결과 집합으로 응답합니다.
-	**일괄 처리 실행** - 사용자가 신용 데이터 행 하나 이상이 포함된 Azure Blob의 URL을 서비스에 보냅니다. 서비스에서는 결과를 다른 Blob에 저장하고 해당 컨테이너의 URL을 반환합니다.  

웹 서비스에 대한 **대시보드** 탭에는 개발자가 이 서비스에 액세스하기 위한 코드를 작성하는 데 도움이 되는 정보 링크 두 개가 있습니다. **요청/응답** 행에서 **API 도움말 페이지** 링크를 클릭하면 서비스의 요청/응답 프로토콜을 사용할 샘플 코드가 포함된 페이지가 열립니다. 마찬가지로 **일괄 처리 실행** 행의 링크는 서비스에 대한 일괄 처리 요청을 만들기 위한 예제 코드를 제공합니다.

API 도움말 페이지에는 R, C# 및 Python 프로그래밍 언어에 대한 샘플이 포함됩니다.

웹 서비스 액세스 및 사용 방법에 대한 자세한 내용은 [컴퓨터 학습 실험에서 게시된 Azure 컴퓨터 학습 웹 서비스 사용 방법](machine-learning-consume-web-services.md)을 참조하세요.

<!---HONumber=July15_HO5-->