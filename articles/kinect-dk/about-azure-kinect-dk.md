---
title: Azure Kinect DK 정보
description: Azure Kinect DK(개발자 키트) 도구 및 통합 서비스에 대한 개요입니다.
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.topic: overview
ms.date: 06/26/2019
keywords: azure, kinect, 개요, 개발 키트, DK, 디바이스, 깊이, 신체 추적, 음성, cognitive services, SDKs, SDK, 펌웨어
ms.openlocfilehash: 6b3215ba6761521214ebffa2a100c5a1a030ab4c
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2020
ms.locfileid: "85277864"
---
# <a name="about-azure-kinect-dk"></a>Azure Kinect DK 정보

 ![Azure Kinect DK](./media/index/device-image.jpg)

Azure Kinect DK는 정교한 컴퓨터 비전과 음성 모델을 제공하는 고급 AI 센서가 포함된 개발자 키트입니다.  Kinect에는 깊이 센서, 비디오 카메라가 장착된 공간 마이크 배열 그리고 여러 모드, 옵션, SDK(소프트웨어 개발 키트)가 포함된 일체형 소형 디바이스인 방향 센서가 포함되어 있습니다. [Microsoft 온라인 스토어](https://www.microsoft.com/p/azure-kinect-dk/8pp5vxmd9nhq)에서 구매할 수 있습니다.

Azure Kinect DK 개발 환경은 다음과 같은 여러 SDK로 구성됩니다.

- 하위 센서 및 디바이스 액세스용 센서 SDK
- 신체를 3D로 추적하는 신체 추적 SDK
- 마이크 액세스 및 Azure 클라우드 기반 Speech Service를 사용하기 위한 음성 Cognitive Services SDK

또한 인식 비전 서비스는 디바이스 RGB 카메라와 함께 사용할 수 있습니다.

   ![Azure Kinect SDK 다이어그램](./media/quickstarts/sdk-diagram.jpg)

## <a name="azure-kinect-sensor-sdk"></a>Azure Kinect 센서 SDK

Azure Kinect 센서 SDK는 Azure Kinect DK 하드웨어 센서 및 디바이스 구성을 위한 하위 센서 액세스를 제공합니다.

Azure Kinect 센서 SDK에 대한 자세한 내용은 [센서 SDK 사용](about-sensor-sdk.md)을 참조하세요.

### <a name="azure-kinect-sensor-sdk-features"></a>Azure Kinect 센서 SDK 기능

센서 SDK는 Azure Kinect DK에 설치하고 실행하면 작동하는 다음과 같은 기능을 갖추고 있습니다.

- 깊이 카메라 액세스 및 모드 제어(수동 IR 모드, 넓은/좁은 보기 필드 깊이 모드) 
- RGB 카메라 액세스 및 제어(예: 노출 및 화이트 균형) 
- 동작 센서(자이로스코프 및 가속도계) 액세스 
- 카메라 간에 구성 가능한 지연을 사용하는 동기화된 깊이-RGB 카메라 스트리밍 
- 디바이스 간에 구성 가능한 지연 오프셋을 사용하여 외부 디바이스 동기화 제어 
- 이미지 해상도, 타임스탬프 등에 대한 카메라 프레임 메타데이터 액세스 
- 디바이스 보정 데이터 액세스 

### <a name="azure-kinect-sensor-sdk-tools"></a>Azure Kinect 센서 SDK 도구

센서 SDK에서 다음 도구를 사용할 수 있습니다.

- 디바이스 데이터 스트림을 모니터링하고 다양한 모드를 구성할 수 있는 뷰어 도구
- Matroska 컨테이너 형식을 사용하는 센서 기록 도구 및 재생 판독기 API
- Azure Kinect DK 펌웨어 업데이트 도구

## <a name="azure-kinect-body-tracking-sdk"></a>Azure Kinect 신체 추적 SDK

신체 추적 SDK에는 Azure Kinect DK 하드웨어와 함께 사용하면 신체를 3D로 추적하는 Windows 라이브러리 및 런타임이 포함되어 있습니다.

### <a name="azure-kinect-body-tracking-features"></a>Azure Kinect 신체 추적 기능

함께 제공되는 SDK에서 다음과 같은 신체 추적 기능을 사용할 수 있습니다.

- 신체를 분할합니다.
- FOV에서 각 신체 부위 또는 전신에 대한 올바른 해부학적 골격을 포함합니다.
- 각 신체 부위에 대한 고유 ID를 제공합니다.
- 시간별로 신체를 추적할 수 있습니다.

### <a name="azure-kinect-body-tracking-tools"></a>Azure Kinect 신체 추적 도구

- 신체 추적기에는 신체를 3D로 추적하는 뷰어 도구가 포함되어 있습니다.

## <a name="speech-cognitive-services-sdk"></a>음성 Cognitive Services SDK

음성 SDK를 통해 Azure 연결 Speech Service를 사용할 수 있습니다.

### <a name="speech-services"></a>Speech Services

- 음성 텍스트 변환
- 음성 번역
- 텍스트 음성 변환

>[!NOTE]
>Azure Kinect DK에는 스피커가 없습니다.

자세한 내용은 [Speech Service 설명서](https://docs.microsoft.com/azure/cognitive-services/speech-service/)를 참조하세요.

## <a name="vision-services"></a>비전 서비스

다음 [Azure Cognitive Vision Services](https://azure.microsoft.com/services/cognitive-services/directory/vision/)는 이미지와 비디오 속의 콘텐츠를 식별하고 분석할 수 있는 Azure 서비스를 제공합니다.

- [Computer vision](https://azure.microsoft.com/services/cognitive-services/computer-vision/)
- [Face](https://azure.microsoft.com/services/cognitive-services/face/)
- [Video indexer](https://azure.microsoft.com/services/media-services/video-indexer/)
- [Content moderator](https://azure.microsoft.com/services/cognitive-services/content-moderator/)
- [Custom vision](https://azure.microsoft.com/services/cognitive-services/custom-vision-service/)

서비스는 지속적으로 발전하고 개선되므로, 신규 또는 추가 [인식 서비스](https://azure.microsoft.com/services/cognitive-services/)를 정기적으로 확인하여 애플리케이션을 개선하세요. 떠오르는 신규 서비스에 대한 최신 내용은 [Cognitive Services 랩](https://labs.cognitive.microsoft.com/)을 확인하세요.

## <a name="azure-kinect-hardware-requirements"></a>Azure Kinect 하드웨어 요구 사항

Azure Kinect DK는 Microsoft의 최신 센서 기술을 USB로 연결된 액세서리에 통합합니다. 자세한 내용은 [Azure Kinect DK 하드웨어 사양](hardware-specification.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

Azure Kinect DK의 개요를 살펴봤습니다. 다음 단계로 Azure Kinect DK를 좀 더 자세히 살펴보고 설정하겠습니다.

> [!div class="nextstepaction"]
>[빠른 시작: Azure Kinect DK 설정](set-up-azure-kinect-dk.md)
