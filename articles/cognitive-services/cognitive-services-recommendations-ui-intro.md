---
title: "Recommendations UI를 사용하여 모델 빌드 | Microsoft Docs"
description: "Azure Machine Learning 권장 사항 - Recommendations UI를 통한 모델 빌드"
services: cognitive-services
documentationcenter: 
author: luiscabrer
manager: jhubbard
editor: cgronlun
ms.assetid: b264fe44-f94e-40ae-9754-60ad7d6cfeb9
ms.service: cognitive-services
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/11/2016
ms.author: luisca
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 5f98395e2beba671192e50f8e53c6198e1efed29


---
# <a name="building-a-model-with-the-recommendations-ui"></a>Recommendations UI를 통한 모델 빌드
이 문서는 단계별 가이드입니다. 여기서의 목표는 [Recommendations UI](https://recommendations-portal.azurewebsites.net/)를 통한 모델링을 학습하는 데 필요한 절차를 안내하는 것입니다.
연습을 통해 프로그래밍 방식으로 사용하기 전에 모델을 빌드하는 프로세스를 이해할 수 있습니다. 또한 UI에 익숙해질 수 있으므로 서비스를 사용하기 시작하는 데 유용합니다.

이 연습에는 30분 정도 걸립니다.

<a name="Step1"></a>

## <a name="step-1---sign-in-to-the-recommendations-ui"></a>1 단계 - Recommendations UI 로그인
1. 아직 가입하지 않은 경우 새 [권장 API 구독](https://portal.azure.com/#create/Microsoft.CognitiveServices/apitype/Recommendations/pricingtier/S1)에 [가입](https://www.microsoft.com/cognitive-services/en-us/recommendations-api)해야 합니다. [http://portal.azure.com/](https://portal.azure.com/#create/Microsoft.CognitiveServices/apitype/Recommendations/pricingtier/S1)에서 **Azure**의 서비스에 가입하고 Azure 계정에 로그인할 수 있습니다. 가입 프로세스에 관한 상세 지침은 *시작 가이드*의 [작업 1](cognitive-services-recommendations-quick-start.md)에서 제공합니다. 
2. 권장 API 구독에 대한 **키**를 구한 후에는 [Recommendations UI](https://recommendations-portal.azurewebsites.net/)로 이동합니다. 
3. **계정 키** 필드에서 키를 입력하여 포털에 로그인한 다음 **로그인** 단추를 클릭합니다.
   
    ![Recommendations UI: 로그인 대화 상자.][reco_signin]

<a name="Step2"></a>

## <a name="step-2---lets-gather-some-training-data"></a>2단계 - 몇 가지 학습 데이터를 수집해 보겠습니다.
빌드를 만들기 전에 엔진에는 카탈로그 파일과 사용 현황 파일 집합이라는 두 종류의 정보가 필요합니다. 

카탈로그 파일은 고객에게 제공하는 항목에 대한 정보를 포함합니다. 사용 파일에는 해당 항목을 사용하는 방법 또는 비즈니스에서 트랜잭션에 대한 정보가 있습니다.

일반적으로 이러한 정보에 대한 저장소 데이터베이스를 쿼리하게 됩니다. 현재 권장 API의 사용 방법을 학습할 수 있게 몇 가지 샘플 데이터를 제공하고 있습니다.

이 데이터는 [http://aka.ms/RecoSampleData](http://aka.ms/RecoSampleData)에서 다운로드할 수 있습니다. 로컬 컴퓨터의 폴더에 **Books.Zip**을 복사하고 압축을 풉니다. 예를 들어 **c:\data**입니다.

카탈로그 및 사용 방법 파일의 스키마에 대한 상세 정보는 [모델 학습을 위한 데이터 수집](cognitive-services-recommendations-collecting-data.md) 문서에 있습니다.

이 연습에서는 작은 파일을 사용하므로 학습을 위해 오래 기다리지 않아도 됩니다. 더 현실적인 파일을 테스트해볼 수 있게 [동일한 위치](http://aka.ms/RecoSampleData)에서 Microsoft Store의 샘플 트랜잭션이 담긴 **MsStoreData.zip**도 제공하고 있습니다.

<a name="Step3"></a>

## <a name="step-3---create-a-project-and-upload-catalog-and-usage-data"></a>3단계 - 프로젝트 만들기 및 카탈로그와 사용 현황 데이터 업로드
[Recommendations UI](https://recommendations-portal.azurewebsites.net/)에 로그인할 때 프로젝트 페이지가 표시됩니다. 이전에 프로젝트를 만든 경우 여기에 나타나야 합니다.
프로젝트(API 참조에서 *모델*이라고도 함)는 카탈로그 및 사용 현황 데이터의 컨테이너입니다. 프로젝트 안에 몇 가지 *빌드*를 만들 수 있습니다. 다음 단계에서 이 프로세스를 안내합니다.

1. 새 프로젝트를 만들려면 텍스트 상자에 이름(“MyFirstModel” 같은 이름)을 입력하고 **프로젝트 추가**를 클릭합니다.
   
    ![Recommendations UI: 프로젝트 페이지.][reco_projects]
2. 프로젝트가 만들어지면 **카탈로그 파일 추가** 섹션에서 **파일 찾기** 버튼을 클릭합니다. 
   2단계에서 얻은 카탈로그를 업로드합니다. *c:\data*에 저장했다면 이 폴더로 이동해야 합니다.
   
     ![Recommendations UI: 프로젝트에 데이터 추가][reco_firstmodel]
3. 카탈로그가 업로드된 후 **사용 현황 파일 추가** 섹션에서 **사용 현황 파일 추가** 단추를 클릭합니다. usage_large.txt 파일을 추가합니다.

> **사용 현황 데이터 파일이 크면 어떻게 해야 합니까?**
> 
> 200MB 미만인 사용 현황 파일만 업로드할 수 있습니다. 즉 시스템은 최대 2GB 분량의 트랜잭션 데이터를 담을 수 있으므로 필요한 경우 여러 개의 파일을 업로드할 수 있습니다.
> 좋은 모델을 만드는 데 많은 데이터가 필요하지 않으며, 중요한 것은 데이터의 크기가 아니라 데이터의 품질입니다. 대부분의 트랜잭션이 소수 항목에 대한 것이며 다른 항목에 대해서는 "거의 신호가 없는" 것이 일반적인 사용 현황입니다.
> 
> 

<a name="Step4"></a>

## <a name="step-4---lets-do-some-training"></a>4 단계 - 몇 가지 교육 과정을 실습해 보겠습니다.
이제 카탈로그와 사용 현황 데이터를 모두 업로드했으니 엔진을 통해 데이터의 패턴을 학습할 준비가 되었습니다.

1. **지금 빌드** 단추를 클릭합니다.
2. **권장 사항**을 빌드 형식으로 선택합니다. 순위 빌드 및 FBT(함께 구입하는 경우가 많은 물품) 빌드 유형을 모두 지원합니다.
   
   ![Recommendations UI: 대화 상자 빌드][reco_build_dialog.png]

    FBT 빌드를 사용하면 동일한 트랜잭션에서 일반적으로 구매/소비되는 제품의 패턴을 식별할 수 있습니다.
    순위 빌드는 관심 특징을 식별하는 데 사용됩니다. 
    FBT나 순위 빌드를 이 연습에서 상세히 다루지는 않습니다. 그러나 관심이 있다면 [빌드 형식 및 모델 품질 설명서 페이지](cognitive-services-recommendations-buildtypes.md)를 확인해 보세요.

1. **빌드** 단추를 클릭합니다. Books 데이터를 사용할 경우 빌드 프로세스는 5분 정도 걸립니다. 데이터 집합이 크면 더 길어집니다.

<a name="Step5"></a>

## <a name="step-5---lets-find-out-what-the-machine-learned"></a>5 단계 - 컴퓨터가 무엇을 학습했는지 확인해 보겠습니다.
빌드가 완료되면 빌드 목록에 새 빌드가 표시될 것입니다. 항목 및 사용자 권장 사항에 대해 이 빌드를 쿼리할 수 있습니다.

1. 빌드가 완료되면 **점수**를 클릭합니다. 이렇게 하면 모델을 활용해 보고 어떤 항목이 권장되는지 살펴볼 수 있습니다.
   
    ![Recommendations UI: 점수 빌드][reco_score_button]
2. 해당 항목에 대해 권장 사항으로 반환되는 항목을 보려면 항목을 선택합니다. 특정 항목에 대한 권장 사항을 예측하는 데 트랜잭션이 충분하지 않다면 시스템이 해당 항목에 대한 권장 사항을 반환하지 않습니다.  어떤 이유로 권장 사항이 반환되지 않는 항목이 있다면 다른 항목을 평가해 봅니다.

<a name="Step6"></a>

## <a name="step-6---next-steps"></a>6단계 - 다음 단계
축하합니다. 모델을 학습했고 해당 모델에서 권장 사항을 가져왔습니다.  Recommendations UI는 프로젝트와 빌드의 상태를 볼 수 있는 유용한 도구입니다.  

이제 모델이 있으므로 위의 모든 단계를 프로그래밍 방식으로 수행하는 방법을 학습하고자 할 수 있습니다. API를 프로그래밍 방식으로 호출하는 방법을 학습하려면 [Recommendations API 참조](http://go.microsoft.com/fwlink/?LinkId=759348)를 확인하고 [권장 샘플 응용 프로그램](http://go.microsoft.com/fwlink/?LinkID=759344)을 다운로드합니다.

[reco_signin]:../media/cognitive-services/reco_signin.PNG
[reco_projects]:../media/cognitive-services/reco_projects.PNG
[reco_firstmodel]:../media/cognitive-services/reco_firstmodel.png
[reco_build_dialog.png]:../media/cognitive-services/reco_build_dialog.png
[reco_score_button]:../media/cognitive-services/reco_score_button.png



<!--HONumber=Dec16_HO2-->


