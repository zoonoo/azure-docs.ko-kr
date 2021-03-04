---
title: Azure Percept AI 모델
description: 프로토타입 및 배포에 사용할 수 있는 AI 모델에 대해 자세히 알아보기
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: conceptual
ms.date: 02/16/2021
ms.custom: template-concept
ms.openlocfilehash: 28a8de231f179cf69342da81e6a2ae1989d2a5d6
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102041589"
---
# <a name="azure-percept-ai-models"></a>Azure Percept AI 모델

Azure Percept를 사용 하면 azure [Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819)에서 AZURE Percept 진한 사용자에 게 직접 AI 모델을 개발 하 고 배포할 수 있습니다. 모델 배포는 [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/) 및 [Azure IoT Edge](https://azure.microsoft.com/services/iot-edge/#iotedge-overview)를 활용 합니다.

## <a name="sample-ai-models"></a>샘플 AI 모델

Azure Percept Studio에는 다음 응용 프로그램에 대 한 샘플 모델이 포함 되어 있습니다.

- 사용자 검색
- 차량 감지
- 일반 개체 검색
- 제품-선반 검색

미리 학습 된 모델을 사용 하는 경우 코딩 또는 학습 데이터 수집이 필요 하지 않습니다. 포털에서 Azure Percept 진한 모델을 배포 하 고 devkit의 비디오 스트림을 열어 작업 추론 모델을 확인 하면 됩니다. [Azure IoT 탐색기](https://github.com/Azure/azure-iot-explorer/releases) 도구를 통해 모델 추론 원격 분석에 액세스할 수도 있습니다.

## <a name="pre-built-solutions"></a>미리 작성 된 솔루션

[사용자 검색을 위한 공간 분석 미리 작성 된 솔루션](https://github.com/george-moore/Santa-Cruz-AI-App) 을 사용할 수도 있습니다. 미리 작성 된 솔루션은 사용자 정의 영역 진입/종료 이벤트로 계산 되는에 지 기반 사용자를 제공 하는 오픈 소스 AI 응용 프로그램입니다. 온-프레미스에 지 장치의 비디오 및 AI 출력은 Azure 웹 사이트로 실행 되는 사용자 인터페이스를 사용 하 여 [Azure Data Lake](https://azure.microsoft.com/solutions/data-lake/)egressed 됩니다. AI 추론는 사용자 검색을 위한 오픈 소스 AI 모델에 의해 제공 됩니다.

:::image type="content" source="./media/overview-ai-models/people-detector.gif" alt-text="공간 분석 미리 작성 된 솔루션 gif.":::

## <a name="custom-no-code-solutions"></a>사용자 지정 코드 없는 솔루션

Azure Percept Studio를 통해 코딩을 요구 하지 않고 사용자 지정 [비전과](./tutorial-nocode-vision.md) 음성 솔루션을 개발할 수 있습니다.

사용자 지정 비전 솔루션의 경우 개체 검색 및 분류 AI 모델을 모두 사용할 수 있습니다. 원할 경우 Azure Percept 진한의 Azure Percept 비전 SoM을 직접 사용 하 여 학습 이미지를 업로드 하 고 태그를 지정할 수 있습니다. 모델 학습 및 평가는 [Azure Cognitive Services](https://azure.microsoft.com/services/cognitive-services/#overview)의 일부인 [Custom Vision](https://www.customvision.ai/)에서 쉽게 수행할 수 있습니다.

사용자 지정 음성 솔루션의 경우에는 현재 다음과 같은 응용 프로그램에 대해 음성 도우미 템플릿을 사용할 수 있습니다.

- 숙박: 호텔 방에는 음성 제어 된 스마트 장치가 장착 되어 있습니다.
- 의료 분야: 음성 제어 스마트 장치를 포함 하는 관리 기능입니다.
- 인벤토리: 음성 제어 스마트 장치를 포함 하는 인벤토리 허브.
- 자동차: 자동차 허브는 음성 제어 된 스마트 장치를 장착 합니다.

미리 빌드된 음성 도우미 키워드 및 명령은 포털을 통해 직접 사용할 수 있습니다. 사용자 지정 키워드 및 명령은 Azure Cognitive Services의 일부 이기도 한 [Speech Studio](https://speech.microsoft.com/)에서 만들고 학습 될 수 있습니다.

## <a name="advanced-development"></a>고급 개발

고급 개발자를 위해 사용 가능한 [Jupyter 노트북](https://github.com/microsoft/Project-Santa-Cruz-Preview/blob/main/Sample-Scripts-and-Notebooks/Official/Machine%20Learning%20Notebooks/Transferlearningusing_SSDLiteV2%20Model.ipynb) 은 개체 검색을 위해 사용자 지정 데이터 집합을 사용 하 여 Python의 미리 학습 된 TensorFlow 모델 (MobileNetSSDV2Lite)을 사용 하 여 전송 학습을 수행 합니다. 노트북은 [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/#product-overview) 를 통해 원격 계산 인스턴스를 활용 하며, AzureML 포털을 사용 하거나 [Visual Studio Code](https://code.visualstudio.com/)에서 로컬로 실행할 수 있습니다.

또한 고급 AI 솔루션을 개발 하는 데 필요한 모든 도구를 설치 하 고 구성 하는 데이터 집합 및 [개발 도구 팩 설치 관리자](https://github.com/microsoft/Project-Santa-Cruz-Preview/blob/main/Sample-Scripts-and-Notebooks/Official/Machine%20Learning%20Notebooks/dev-tools-installer.md)를 관리 하기 위한 몇 가지 유용한 Python [스크립트가](https://github.com/microsoft/Project-Santa-Cruz-Preview/tree/main/Sample-Scripts-and-Notebooks/Official/Scripts) 포함 되어 있습니다.
