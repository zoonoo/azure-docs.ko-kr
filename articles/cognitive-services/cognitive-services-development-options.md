---
title: Azure Cognitive Services 개발 옵션
description: 클라이언트 라이브러리, REST Api, Logic Apps, 전원 자동화, Azure Functions, Azure App Service, Azure Databricks 등과 같은 다양 한 개발 및 배포 옵션을 사용 하 여 Azure Cognitive Services를 사용 하는 방법을 알아봅니다.
services: cognitive-services
manager: nitinme
author: erhopf
ms.author: erhopf
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 10/22/2020
ms.openlocfilehash: 05d3ca7cf532b739b943e2a87d5ab29ae66cabd7
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/26/2020
ms.locfileid: "92548574"
---
# <a name="cognitive-services-development-options"></a>Cognitive Services 개발 옵션

이 문서에서는 Azure Cognitive Services를 시작 하는 데 도움이 되는 개발 및 배포 옵션에 대 한 개략적인 개요를 제공 합니다.

Azure Cognitive Services는 개발자가 기계 학습에 대 한 심층 지식 없이 응용 프로그램 및 제품에 인텔리전스를 구축할 수 있도록 하는 클라우드 기반 AI 서비스입니다. Cognitive Services를 사용 하면 응용 프로그램에서 사용할 수 있도록 Microsoft에서 빌드하고 학습 하 고 업데이트 하는 AI 기능 또는 모델에 액세스할 수 있습니다. 대부분의 경우 비즈니스 요구 사항에 맞게 모델을 사용자 지정 하는 옵션도 있습니다. 

Cognitive Services는 의사 결정, 언어, 음성 및 비전의 네 가지 범주로 구성 됩니다. 일반적으로 Microsoft에서 제공 하는 REST Api, 클라이언트 라이브러리 및 사용자 지정 도구 (예: 명령줄 인터페이스)를 통해 이러한 서비스에 액세스 합니다. 그러나이는 성공의 유일한 경로입니다. Azure를 통해 다음과 같은 몇 가지 개발 옵션에 액세스할 수도 있습니다.

* Logic Apps 및 파워 자동화와 같은 자동화 및 통합 도구
* Azure Functions 및 App Service와 같은 배포 옵션입니다. 
* 보안 액세스를 위해 Docker 컨테이너를 Cognitive Services 합니다.
* 빅 데이터 시나리오에 대 한 Apache Spark, Azure Databricks, Azure Synapse Analytics 및 Azure Kubernetes Service와 같은 도구입니다. 

을 (를) 시작 하기 전에 Cognitive Services는 두 개의 고유한 작업에 주로 사용 되는 것을 알고 있어야 합니다. 수행 하려는 작업에 따라 다양 한 개발 및 배포 옵션을 선택할 수 있습니다. 

* [예측 및 분석을 위한 개발 옵션](#development-options-for-prediction-and-analysis)
* [모델을 사용자 지정 및 구성 하는 도구](#tools-to-customize-and-configure-models)

## <a name="development-options-for-prediction-and-analysis"></a>예측 및 분석을 위한 개발 옵션 

모델을 사용자 지정 하 고 구성 하는 데 사용 하는 도구는 Cognitive Services 호출 하는 데 사용 하는 도구와 다릅니다. 기본적으로 대부분의 Cognitive Services 사용자 지정 없이 데이터를 보내고 정보를 받을 수 있습니다. 예를 들면 다음과 같습니다. 

* Computer Vision 서비스에 이미지를 보내 단어와 구를 검색 하거나 프레임의 사용자 수를 계산할 수 있습니다.
* 음성 서비스에 오디오 파일을 전송 하 고, 음성을 동시에 텍스트로 변환할 수 있습니다.
* 양식 인식기 서비스에 PDF를 전송 하 고 해당 셀 내에서 테이블, 셀 및 텍스트를 검색할 수 있으며, 좌표 및 세부 정보가 포함 된 JSON 출력이 표시 됩니다.

Azure는 다양 한 유형의 사용자를 위해 설계 된 다양 한 도구를 제공 하며,이 중 상당수는 Cognitive Services에서 사용할 수 있습니다. 디자이너 기반 도구는 사용 하기 쉽고, 설정 하 고 자동화 하는 데 빠르게 적용 되지만 사용자 지정에 대 한 제한 사항이 있을 수 있습니다. REST Api 및 클라이언트 라이브러리는 사용자에 게 더 많은 제어와 유연성을 제공 하지만 솔루션을 빌드하는 데 더 많은 노력, 시간 및 전문 지식이 필요 합니다. REST Api 및 클라이언트 라이브러리를 사용 하는 경우 c #, Java, Python, JavaScript 또는 기타 인기 있는 프로그래밍 언어와 같은 최신 프로그래밍 언어를 사용 하는 것이 좋습니다. 

Cognitive Services 작업할 수 있는 다양 한 방법을 살펴보겠습니다.

### <a name="client-libraries-and-rest-apis"></a>클라이언트 라이브러리 및 REST API

Cognitive Services 클라이언트 라이브러리와 REST Api를 통해 서비스에 직접 액세스할 수 있습니다. 이러한 도구는 Cognitive Services에 대 한 프로그래밍 방식 액세스를 제공 하며, 대부분의 경우 모델 및 솔루션을 프로그래밍 방식으로 사용자 지정할 수 있습니다. 

* **대상 사용자** : 개발자 및 데이터 과학자
* **이점** : 모든 언어 및 환경에서 서비스를 호출 하는 가장 큰 유연성을 제공 합니다. 
* **UI** : 해당 없음-코드 전용
* **구독** : Azure 계정 + Cognitive Services 리소스

사용 가능한 클라이언트 라이브러리 및 REST Api에 대 한 자세한 내용을 보려면 [Cognitive Services 개요](index.yml) 를 사용 하 여 비전, 의사 결정, 언어 및 음성에 대 한 빠른 시작 중 하나를 선택 하 고 서비스를 시작 하세요.

### <a name="cognitive-services-for-big-data"></a>빅 데이터에 대한 Cognitive Services

빅 데이터용 Cognitive Services를 사용하면 지속적으로 향상되는 인텔리전트 모델을 Apache Spark&trade; 및 SQL 컴퓨팅에 직접 포함할 수 있습니다. 이러한 도구를 사용하면 개발자가 낮은 수준의 네트워킹 세부 정보를 알고 있을 필요가 없으므로 스마트 분산 애플리케이션을 만드는 데 집중할 수 있습니다. 빅 데이터에 대 한 Cognitive Services는 Azure Databricks, Azure Synapse, Azure Kubernetes Service 및 데이터 커넥터와 같은 플랫폼 및 커넥터를 지원 합니다.

* **대상 사용자** : 데이터 과학자 및 데이터 엔지니어
* **이점** : 빅 데이터에 대 한 Azure Cognitive Services는 사용자가 Apache Spark를 사용 하 여 Cognitive Services를 통해 채널당 2tb의 데이터를 제공 합니다 &trade; . 모든 데이터 저장소를 사용 하 여 대규모 인텔리전트 응용 프로그램을 쉽게 만들 수 있습니다.
* **UI** : 해당 없음-코드 전용
* **구독** : Azure 계정 + Cognitive Services 리소스

Cognitive Services에 대 한 빅 데이터에 대해 자세히 알아보려면 [개요](./big-data/cognitive-services-for-big-data.md)를 사용 하 여 시작 하는 것이 좋습니다. 빌드를 시작할 준비가 되 면 [Python](./big-data/samples-python.md) 또는 [Scala](./big-data/samples-scala.md) 샘플을 사용해 보세요.

### <a name="azure-functions-and-azure-service-web-jobs"></a>Azure Functions 및 Azure 서비스 웹 작업

[Azure Functions](https://docs.microsoft.com/azure/azure-functions/) 및 [Azure App Service 웹 작업](https://docs.microsoft.com/azure/app-service/) 은 모두 개발자를 위해 설계 되었으며 [Azure 앱 서비스](https://docs.microsoft.com/azure/app-service/)를 기반으로 하는 코드 우선 integration services를 제공 합니다. 이러한 제품은 서버를 사용 하지 않는 인프라를 제공 하 여 코드를 작성 합니다. 해당 코드 내에서 클라이언트 라이브러리와 REST Api를 사용 하 여 서비스에 대 한 호출을 수행할 수 있습니다. 

* **대상 사용자** : 개발자 및 데이터 과학자
* **이점** : 서버를 사용 하지 않는 계산 서비스를 사용 하 여 이벤트 트리거 코드를 실행할 수 있습니다. 
* **UI** : 예
* **구독** : Azure 계정 + Cognitive Services 리소스 + Azure Functions 구독

### <a name="azure-logic-apps"></a>Azure Logic Apps 

[Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/) 는 전원 자동화와 동일한 워크플로 디자이너 및 커넥터를 공유 하지만 Visual Studio 및 devops와의 통합을 포함 하 여 고급 및 제어를 제공 합니다. 전원 자동화를 사용 하면 Api에 대 한 프록시 또는 래퍼를 제공 하는 서비스별 커넥터를 통해 인식 서비스 리소스와 쉽게 통합할 수 있습니다. 이러한 커넥터는 전원 자동화에서 사용할 수 있는 커넥터와 동일 합니다. 

* **대상 사용자** : 개발자, 통합자, IT 전문가, devops
* **이점** : 디자이너 중심 (선언적) 개발 모델은 낮은 코드 솔루션의 고급 옵션 및 통합을 제공 합니다.
* **UI** : 예
* **구독** : Azure 계정 + Cognitive Services 리소스 + Logic Apps 배포

### <a name="power-automate"></a>Power Automate 

전원 자동화는 코드를 작성 하지 않고도 앱과 서비스 간에 자동화 된 워크플로를 만드는 데 도움이 되는 [강력한 플랫폼](https://docs.microsoft.com/power-platform/) 의 서비스입니다. 여러 커넥터를 제공 하 여 전원 자동화 솔루션에서 Cognitive Services 리소스와 쉽게 상호 작용할 수 있도록 합니다. Power Automate는 Logic Apps를 기반으로 빌드됩니다. 

* **대상 사용자** : 비즈니스 사용자 (분석가) 및 Sharepoint 관리자
* **이점** : 바탕 화면에서 마우스 클릭, 키 입력 및 복사 붙여넣기 단계를 기록 하기만 하면 반복적인 수동 작업을 자동화 합니다.
* **Ui 도구** : 예-ui 전용
* **구독** : Azure 계정 + Cognitive Services 리소스 + 전원 자동화 구독 + Office 365 구독

### <a name="ai-builder"></a>AI Builder 

[AI Builder](https://docs.microsoft.com/ai-builder/overview) 는 프로세스를 자동화 하 고 결과를 예측 하 여 비즈니스 성능을 개선 하는 데 사용할 수 있는 Microsoft의 강력한 플랫폼 기능입니다. AI 작성기는 특정 시점에서 클릭 환경을 통해 솔루션에 AI 기능을 제공 합니다. 양식 인식기, Text Analytics 및 Computer Vision와 같은 여러 인식 서비스가 여기에 직접 통합 되었으며 고유한 Cognitive Services를 만들 필요가 없습니다. 

* **대상 사용자** : 비즈니스 사용자 (분석가) 및 Sharepoint 관리자
* **이점** : 지점 및 클릭 환경을 통해 AI 기능을 제공 하는 턴키 솔루션입니다. 코딩 또는 데이터 과학 기술이 필요 하지 않습니다.
* **Ui 도구** : 예-ui 전용
* **구독** : AI 빌더

### <a name="continuous-integration-and-deployment"></a>연속 통합 및 배포

Azure DevOps 및 GitHub 작업을 사용 하 여 배포를 관리할 수 있습니다. 이에 대해 설명 하는 [아래 섹션](#continuous-integration-and-delivery-with-devops-and-github-actions) 에는 음성 및 LANGUAGE UNDERSTANDING (LUIS) 서비스를 위한 사용자 지정 모델을 학습 하 고 배포 하는 CI/CD 통합의 두 가지 예가 있습니다. 

* **대상 사용자** : 개발자, 데이터 과학자 및 데이터 엔지니어
* **이점** : 프로그래밍 방식으로 응용 프로그램 및 모델을 지속적으로 조정, 업데이트 및 배포할 수 있습니다. 정기적으로 데이터를 사용 하 여 음성, 비전, 언어 및 의사 결정을 위한 모델을 개선 하 고 업데이트할 때 상당한 이점을 누릴 수 있습니다. 
* **UI 도구** : 해당 없음-코드 전용 
* **구독** : Azure 계정 + Cognitive Services 리소스 + GitHub 계정

## <a name="tools-to-customize-and-configure-models"></a>모델을 사용자 지정 및 구성 하는 도구

Cognitive Services를 사용 하 여 응용 프로그램 또는 워크플로를 빌드하는 과정을 진행 하면서 원하는 성능을 얻기 위해 모델을 사용자 지정 해야 하는 경우가 있습니다. 대부분의 서비스에서는 특정 비즈니스 요구 사항을 충족 하기 위해 미리 작성 된 모델을 기반으로 빌드할 수 있습니다. 사용자 지정이 가능한 모든 서비스에 대해 코드 기반 학습을 위한 Api 뿐만 아니라 프로세스를 탐색 하기 위한 UI 기반 환경을 제공 합니다. 예를 들면 다음과 같습니다.

* 3% 미만으로 WER (단어 오류 요금)를 사용 하 여 의료 약관을 올바르게 인식 하도록 Custom Speech 모델을 학습 하려고 합니다.
* 침 엽 수와 낙 엽의 차이를 알 수 있는 Custom Vision를 사용 하 여 이미지 분류자를 빌드하려고 합니다.
* 향상 된 자동화 된 고객 환경을 위한 개인 음성 데이터로 사용자 지정 신경망을 빌드하 려 합니다.

모델을 학습 하 고 구성 하는 데 사용 하는 도구는 Cognitive Services 호출 하는 데 사용 하는 도구와 다릅니다. 대부분의 경우 사용자 지정을 지 원하는 Cognitive Services은 모델을 학습, 평가 및 배포 하는 데 도움이 되도록 설계 된 포털 및 UI 도구를 제공 합니다. 몇 가지 옵션에 대해 간단히 살펴보겠습니다.<br><br>

| 핵심 요소 | 서비스 | 사용자 지정 UI | 빠른 시작 |
|--------|---------|------------------|------------|
| 시각 | Custom Vision | https://www.customvision.ai/ | [빠른 시작](https://docs.microsoft.com/azure/cognitive-services/Custom-Vision-Service/quickstarts/image-classification?pivots=programming-language-csharp) | 
| 시각 | Form Recognizer | 샘플 레이블 지정 도구 | [빠른 시작](https://docs.microsoft.com/azure/cognitive-services/form-recognizer/quickstarts/label-tool?tabs=v2-0) |
| 의사 결정 | Content Moderator | https://contentmoderator.cognitive.microsoft.com/dashboard | [빠른 시작](https://docs.microsoft.com/azure/cognitive-services/content-moderator/review-tool-user-guide/human-in-the-loop) |
| 의사 결정 | Metrics Advisor | https://metricsadvisor.azurewebsites.net/  | [빠른 시작](https://docs.microsoft.com/azure/cognitive-services/metrics-advisor/quickstarts/web-portal) |
| 의사 결정 | Personalizer | UI는 Personalizer 리소스의 Azure Portal에서 사용할 수 있습니다. | [빠른 시작](https://docs.microsoft.com/azure/cognitive-services/personalizer/quickstart-personalizer-sdk) |
| 언어 | 언어 이해(LUIS) | https://www.luis.ai/ | |
| 언어 | QnA Maker | https://www.qnamaker.ai/ | [빠른 시작](https://docs.microsoft.com/azure/cognitive-services/qnamaker/quickstarts/create-publish-knowledge-base) |
| 언어 | 변환기/사용자 지정 변환기 | https://portal.customtranslator.azure.ai/ | [빠른 시작](https://docs.microsoft.com/azure/cognitive-services/translator/custom-translator/quickstart-build-deploy-custom-model) |
| 음성 | 사용자 지정 명령 | https://speech.microsoft.com/ | [빠른 시작](https://docs.microsoft.com/azure/cognitive-services/speech-service/custom-commands) |
| 음성 | Custom Speech | https://speech.microsoft.com/ | [빠른 시작](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-custom-speech) |
| 음성 | Custom Voice | https://speech.microsoft.com/ | [빠른 시작](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-custom-voice) |  

### <a name="continuous-integration-and-delivery-with-devops-and-github-actions"></a>DevOps 및 GitHub 작업을 사용한 연속 통합 및 제공

Language Understanding 및 음성 서비스는 Azure DevOps 및 GitHub 작업으로 구동 되는 연속 통합 및 지속적인 배포 솔루션을 제공 합니다. 이러한 도구는 사용자 지정 모델의 자동화 된 학습, 테스트 및 릴리스 관리에 사용 됩니다. 

* [Custom Speech용 CI/CD](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-custom-speech-continuous-integration-continuous-deployment)
* [LUIS 용 CI/CD](https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-devops-automation)

## <a name="on-prem-containers"></a>온-프레미스 컨테이너 

프레미스 액세스 및 사용을 위해 많은 Cognitive Services를 컨테이너에 배포할 수 있습니다. 이러한 컨테이너를 사용 하면 규정 준수, 보안 또는 기타 운영 상의 이유로 데이터에 더 가깝게 Cognitive Services를 제공할 수 있습니다. Cognitive Services 컨테이너의 전체 목록은 [Cognitive Services에 대 한 온-프레미스 컨테이너](./cognitive-services-container-support.md)를 참조 하세요.

## <a name="next-steps"></a>다음 단계
<!--
* Learn more about low code development options for Cognitive Services -->
* [Cognitive Services 리소스를 만들고 빌드를 시작 합니다.](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account?tabs=multiservice%2Clinux)