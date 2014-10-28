<properties title="Step 6: Access the Azure Machine Learning web service" pageTitle="Step 6: Access the Machine Learning web service | Azure" description="Step 6: Access an active Azure Machine Learning API web service" metaKeywords="" services="" solutions="" documentationCenter="" authors="garye" videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="garye"></tags>

[Azure 기계 학습을 사용한 예측 솔루션 개발][Azure 기계 학습을 사용한 예측 솔루션 개발] 자습서의 마지막 단계입니다.

1.  [기계 학습 작업 영역 만들기][기계 학습 작업 영역 만들기]
2.  [기존 데이터 업로드][기존 데이터 업로드]
3.  [새로운 실험 만들기][새로운 실험 만들기]
4.  [모델 학습 및 평가][모델 학습 및 평가]
5.  [웹 서비스 게시][웹 서비스 게시]
6.  **웹 서비스 액세스**

------------------------------------------------------------------------

# 6단계: Azure 기계 학습 웹 서비스 액세스

웹 서비스를 활용하려면 사용자가 서비스에 데이터를 보내고 결과를 받을 수 있어야 합니다. 웹 서비스는 다음 두 방법의 하나로 데이터를 받고 반환할 수 있는 Azure 웹 서비스입니다.

-   **요청/응답** - 사용자가 HTTP 프로토콜을 사용하여 단일 신용 데이터 집합을 서비스에 보내고 서비스에서는 단일 결과 집합으로 응답합니다.
-   **일괄 처리 실행** - 사용자가 신용 데이터 행 하나 이상이 포함된 Azure Blob의 URL을 서비스에 보냅니다. 서비스에서는 결과를 다른 Blob에 저장하고 해당 컨테이너의 URL을 반환합니다.

웹 서비스에 대한 **대시보드** 탭에는 개발자가 이 서비스에 액세스하기 위한 코드를 작성하는 데 도움이 되는 정보 링크 두 개가 있습니다. **요청/응답** 행에서 **API 도움말 페이지** 링크를 클릭하면 서비스의 요청/응답 프로토콜을 사용할 샘플 코드가 포함된 페이지가 열립니다. 마찬가지로 **일괄 처리 실행** 행의 링크는 서비스에 대한 일괄 처리 요청을 만들기 위한 예제 코드를 제공합니다.

API 도움말 페이지에는 R, C# 및 Python 프로그래밍 언어에 대한 샘플이 포함됩니다. 예를 들어 게시한 웹 서비스에 액세스하는 데 사용할 수 있는 R 코드는 다음과 같습니다(실제 서비스 URL은 샘플 코드에 표시됨).

    library("RCurl")
    library("RJSONIO")

    h = basicTextGatherer()
    req = list(Id="score00001",
     Instance=list(FeatureVector=list(
        "row.names"= "0",
        "Status of checking account"= "0",
        "Duration in months"= "0",
        "Credit history"= "0",
        "Purpose"= "0",
        "Credit amount"= "0",
        "Savings account/bond"= "0",
        "Present employment since"= "0",
        "Installment rate in percentage of disposable income"= "0",
        "Personal status and sex"= "0",
        "Other debtors"= "0",
        "Present residence since"= "0",
        "Property"= "0",
        "Age in years"= "0",
        "Other installment plans"= "0",
        "Housing"= "0",
        "Number of existing credits"= "0",
        "Job"= "0",
        "Number of people providing maintenance for"= "0",
        "Telephone"= "0",
        "Foreign worker"= "0",
        "Credit risk"= "0"
     ),GlobalParameters=fromJSON('{}')))

    body = toJSON(req)
    api_key = "abc123" # You can obtain the API key from the publisher of the web service

    h$reset()
    curlPerform(url = "http://xxx.cloudapp.net/workspaces/xxx/services/xxx/score",
                httpheader=c('Content-Type' = "application/json", 'Authorization' = "Bearer " + api_key),
                postfields=body,
                writefunction = h$update,
                verbose = TRUE
                )

    result = h$value()

  [Azure 기계 학습을 사용한 예측 솔루션 개발]: ../machine-learning-walkthrough-develop-predictive-solution/
  [기계 학습 작업 영역 만들기]: ../machine-learning-walkthrough-1-create-ml-workspace/
  [기존 데이터 업로드]: ../machine-learning-walkthrough-2-upload-data/
  [새로운 실험 만들기]: ../machine-learning-walkthrough-3-create-new-experiment/
  [모델 학습 및 평가]: ../machine-learning-walkthrough-4-train-and-evaluate-models/
  [웹 서비스 게시]: ../machine-learning-walkthrough-5-publish-web-service/
