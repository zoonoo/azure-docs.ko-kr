---
title: Azure Percept AI 모델
description: 프로토타입 및 배포에 사용할 수 있는 AI 모델에 대해 자세히 알아보기
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: conceptual
ms.date: 02/16/2021
ms.custom: template-concept
ms.openlocfilehash: 8a9ed564012e8b530436152d931f5a5f6470ff98
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102098369"
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

최신 지침, 자습서 및 다음과 같은 항목에 대 한 자세한 내용은 [Azure PERCEPT 진한 고급 개발 GitHub](https://github.com/microsoft/azure-percept-advanced-development) 를 참조 하세요.

* 장치에 사용자 지정 AI 모델 가져오기
* 모델을 업데이트 하는 과정은 이미 전송 학습을 통해 지원 됩니다.
* 기타
