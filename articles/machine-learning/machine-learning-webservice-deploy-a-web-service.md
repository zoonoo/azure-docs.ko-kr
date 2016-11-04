---
title: 새 웹 서비스 배포
description: ARM 기반 웹 서비스를 배포하는 워크플로
services: machine-learning
documentationcenter: ''
author: vDonGlover
manager: raymondl
editor: ''

ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/04/2016
ms.author: v-donglo

---
# <a name="deploy-a-new-web-service"></a>새 웹 서비스 배포
Microsoft Azure Machine Learning은 이제 새 청구 계획 옵션을 허용하고 여러 지역에 웹 서비스를 배포하는 [Azure Resource Manager](../resource-group-overview.md) 를 기반으로 하는 웹 서비스를 제공합니다.

Microsoft Azure 기계 학습 웹 서비스를 사용하여 웹 서비스를 배포하는 일반적인 워크플로는 다음과 같습니다.

* 예측 실험 만들기
* 배포
* 해당 이름 구성
* 요금제
* 테스트
* 사용

다음 그래픽에서는 워크플로를 보여 줍니다.

![웹 서비스 배포 워크플로][1]

## <a name="deploy-web-service-from-studio"></a>Studio에서 웹 서비스 배포
실험을 새 웹 서비스로 배포하려면. 기계 학습 스튜디오에 로그인하고 새 예측 웹 서비스를 만듭니다. 

**참고**: 실험을 기존 웹 서비스로 이미 배포한 경우 새 웹 서비스로 배포할 수 없습니다.

실험 캔버스의 맨 아래에서 **실행**을 클릭한 다음 **웹 서비스 배포** 및 **웹 서비스 배포[신규]**를 클릭합니다. 기계 학습 웹 서비스 관리자의 배포 페이지가 열립니다.

## <a name="machine-learning-web-service-manager-deploy-experiment-page"></a>기계 학습 웹 서비스 관리자 배포 실험 페이지
실험 배포 페이지에서 웹 서비스의 이름을 입력합니다.
가격 책정 계획을 선택합니다. 기존 가격 책정 계획이 있는 경우 해당 계획을 선택하고 그렇지 않으면 서비스에 대한 새 가격 계획을 만들어야 합니다. 

1. **가격 계획** 드롭다운에서 기존 계획을 선택하거나 **새 계획 선택** 옵션을 선택합니다.
2. **계획 이름**에 청구서의 계획을 식별하는 이름을 입력합니다.
3. **월별 계획 계층**중 하나를 선택합니다. 계획 계층이 기본적으로 기본 영역에 대한 계획으로 설정되고 웹 서비스는 해당 지역에 배포됩니다.

**배포** 를 클릭하면 웹 서비스에 대한 빠른 시작 페이지를 엽니다.

## <a name="quickstart-page"></a>빠른 시작 페이지
웹 서비스 빠른 시작 페이지를 사용하면 새 웹 서비스를 만든 후 수행할 가장 일반적인 작업에 대한 액세스 권한 및 지침을 제공합니다. 여기에서 **테스트** 페이지 및 **사용** 페이지에 쉽게 액세스할 수 있습니다.

## <a name="testing-your-web-service"></a>웹 서비스 테스트
빠른 시작 페이지에서 일반적인 작업의 웹 서비스 테스트를 클릭합니다.   

웹 서비스를 RRS(요청-응답 서비스)로 테스트하려면:

* 메뉴 모음에서 **테스트** 를 클릭합니다.
* **요청-응답**을 클릭합니다.
* 실험의 입력 열에 적절한 값을 입력합니다.
* **요청-응답**테스트를 클릭합니다.

결과는 페이지의 오른쪽에 표시됩니다.

BES(배치 실행 서비스) 웹 서비스를 테스트하려면 CSV 파일을 사용합니다.

* 메뉴 모음에서 **테스트** 를 클릭합니다.
* **배치**를 클릭합니다.
* 입력에서 찾아보기를 클릭하고 샘플 데이터 파일로 이동합니다.
* **테스트**를 클릭합니다.

테스트의 상태는 **배치 작업 테스트**에 표시됩니다.

## <a name="consuming-your-web-service"></a>웹 서비스 사용
웹 서비스로 배포된 경우 Azure 기계 학습 실험에서는 광범위한 장치 및 플랫폼에서 사용할 수 있는 REST API를 제공합니다. 이는 단순 REST API는 JSON 형식의 메시지를 허용하고 응답하기 때문입니다. Azure 기계 학습 포털에서는 웹 서비스를 호출하는 데 사용할 수 있는 R, C# 및 Python 코드를 제공합니다.

사용 페이지에서 다음을 찾을 수 있습니다.

* 앱에서 웹 서비스를 사용하기 위해 필요한 API 키 및 URI
* 사용 프로세스를 시작할 Excel 및 웹앱 템플릿
* 시작할 C#, Python 및 R의 샘플 코드

웹 서비스를 사용하는 방법에 대한 자세한 내용은 [기계 학습 실험에서 배포된 Azure 기계 학습 웹 서비스를 사용하는 방법](machine-learning-consume-web-services.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계
웹 서비스 사용에 대한 자세한 내용은 다음을 참조하세요.

[Azure 기계 학습 실험에서 배포된 기계 학습 웹 서비스를 사용하는 방법](machine-learning-consume-web-services.md)

[Azure 기계 학습 웹 서비스: 배포 및 사용](machine-learning-deploy-consume-web-service-guide.md)

<!--Image references-->
[1]: ./media/machine-learning-webservice-deploy-a-web-service/armdeploymentworkflow.png


<!--links-->



<!--HONumber=Oct16_HO2-->


