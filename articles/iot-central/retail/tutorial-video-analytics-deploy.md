---
title: '자습서: 비디오 분석 - 개체 및 동작 감지 Azure IoT Central 애플리케이션 템플릿 배포 방법'
description: 자습서 - 이 가이드에는 비디오 분석 - 개체 및 동작 감지 애플리케이션 템플릿을 사용하여 Azure IoT Central 애플리케이션을 배포하는 단계가 요약되어 있습니다.
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.author: nandab
author: KishorIoT
ms.date: 07/31/2020
ms.openlocfilehash: c578da7e83a39f84e72b550038bd87dde3c61d28
ms.sourcegitcommit: a67b972d655a5a2d5e909faa2ea0911912f6a828
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2021
ms.locfileid: "101727467"
---
# <a name="tutorial-how-to-deploy-an-iot-central-application-using-the-video-analytics---object-and-motion-detection-application-template"></a>자습서: 비디오 분석 - 개체 및 동작 감지 애플리케이션 템플릿을 사용하여 IoT Central 애플리케이션을 배포하는 방법

주요 *비디오 분석 - 개체 및 동작 감지* 애플리케이션 구성 요소에 대한 개요는 [개체 및 동작 감지 비디오 분석 애플리케이션 아키텍처](architecture-video-analytics.md)를 참조하세요.

다음 비디오에서는 _비디오 분석 - 개체 및 동작 감지 애플리케이션 템플릿_ 을 사용하여 IoT Central 솔루션을 배포하는 방법을 연습합니다.

> [!VIDEO https://www.youtube.com/embed/Bo3FziU9bSA]

이 자습서 세트에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 애플리케이션 배포
> * 애플리케이션에 연결하는 IoT Edge 인스턴스 배포
> * 애플리케이션 모니터링 및 관리

## <a name="prerequisites"></a>사전 요구 사항

Azure 구독이 권장됩니다. 대신 무료 7일 평가판을 사용할 수 있습니다. Azure 구독이 아직 없는 경우 [Azure 가입 페이지](https://aka.ms/createazuresubscription)에서 만들 수 있습니다.

## <a name="deploy-the-application"></a>애플리케이션 배포

다음 단계에 따라 비디오 분석 애플리케이션 템플릿을 사용하여 IoT Central 애플리케이션을 배포합니다.

1. [Azure IoT Central(YOLO v3)에서 비디오 분석 애플리케이션 만들기](tutorial-video-analytics-create-app-yolo-v3.md) 또는 [Azure IoT Central(OpenVINO&trade;)에서 비디오 분석 만들기](tutorial-video-analytics-create-app-openvino.md) 자습서 중 하나를 완료하여 다음을 수행합니다.
    - Azure Media Services 계정을 만듭니다.
    - 비디오 분석 - 개체 및 동작 감지 애플리케이션 템플릿에서 IoT Central 애플리케이션을 만듭니다.
    - IoT Central 애플리케이션에서 게이트웨이 디바이스를 구성합니다. 게이트웨이를 구성하면 카메라 디바이스를 애플리케이션에 연결할 수 있습니다.

1. [비디오 분석용 IoT Edge 인스턴스 만들기(Linux VM)](tutorial-video-analytics-iot-edge-vm.md) 또는 [자습서: 비디오 분석용 IoT Edge 인스턴스 만들기(Intel NUC)](tutorial-video-analytics-iot-edge-nuc.md) 자습서를 완료하여 다음을 수행합니다.
    - Azure IoT Edge 런타임이 설치된 Azure VM을 만듭니다. 비디오 분석 모듈을 호스트할 IoT Edge 설치를 준비합니다.
    - IoT Edge 디바이스를 IoT Central 애플리케이션에 연결합니다.

1. [비디오 분석 애플리케이션 모니터링 및 관리](tutorial-video-analytics-manage.md) 자습서를 완료하여 다음을 수행합니다.
    - IoT Central 애플리케이션의 게이트웨이에 개체 및 동작 감지 카메라를 추가합니다.
    - 카메라 처리를 시작합니다.
    - AMS에서 캡처한 비디오를 볼 수 있도록 로컬 미디어 플레이어를 설치합니다.
    - 감지된 개체를 보여주는 캡처된 비디오를 봅니다.
    - 정리합니다.

## <a name="clean-up-resources"></a>리소스 정리

애플리케이션 사용을 마친 후에는 앞에서 만든 모든 리소스를 다음과 같이 제거할 수 있습니다.

1. IoT Central 애플리케이션의 **관리** 섹션에서 **사용자의 애플리케이션** 페이지로 이동합니다. 그런 다음, **삭제** 를 선택합니다.
1. Azure Portal에서 **lva-rg** 리소스 그룹을 삭제합니다.
1. 로컬 머신에서 **amp-viewer** Docker 컨테이너를 중지합니다.

## <a name="next-steps"></a>다음 단계

비디오 분석 애플리케이션 템플릿을 배포하고 사용하는 단계를 알아보았으므로, 이제

> [!div class="nextstepaction"]
> [Azure IoT Central(YOLO v3)에서 비디오 분석 애플리케이션 만들기](tutorial-video-analytics-create-app-yolo-v3.md) 또는

> [!div class="nextstepaction"]
> [Azure IoT Central(OpenVINO&trade;)에서 비디오 분석 만들기](tutorial-video-analytics-create-app-openvino.md) 자습서에 따라 시작하세요.