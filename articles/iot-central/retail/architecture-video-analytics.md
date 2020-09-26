---
title: Azure IoT Central video analytics 개체 및 동작 감지 | Microsoft Docs
description: IoT Central에서 비디오 분석-개체 및 동작 검색 응용 프로그램 템플릿을 사용 하 여 IoT Central 응용 프로그램을 빌드하는 방법을 알아봅니다. 이 템플릿은 라이브 비디오 분석과 연결 된 카메라를 사용 합니다.
author: KishorIoT
ms.author: nandab
ms.date: 07/27/2020
ms.topic: conceptual
ms.service: iot-central
ms.subservice: iot-central-retail
services: iot-central
ms.openlocfilehash: 916d491c45a2979c59580328a721c11bd79d49c0
ms.sourcegitcommit: 5dbea4631b46d9dde345f14a9b601d980df84897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91372207"
---
# <a name="video-analytics---object-and-motion-detection-application-architecture"></a>비디오 분석-개체 및 동작 검색 응용 프로그램 아키텍처

**비디오 분석-개체 및 동작 검색** 응용 프로그램 템플릿을 사용 하 여 IoT 솔루션을 빌드할 수 있습니다. 라이브 비디오 분석 기능을 포함 합니다.

:::image type="content" source="media/architecture-video-analytics/architecture.png" alt-text="비디오 분석 개체 및 동작 검색 구성 요소 개요의 다이어그램입니다.":::

비디오 분석 솔루션의 주요 구성 요소는 다음과 같습니다.

## <a name="live-video-analytics-lva"></a>LVA (라이브 비디오 분석)

LVA는 edge 및 클라우드를 포괄 하는 지능적인 비디오 응용 프로그램을 빌드할 수 있는 플랫폼을 제공 합니다. 플랫폼을 사용 하 여에 지 및 클라우드에 걸친 인텔리전트 비디오 응용 프로그램을 빌드할 수 있습니다. 이 플랫폼은 라이브 비디오를 캡처, 녹화 및 분석하는 기능과 비디오 또는 비디오 분석일 수 있는 결과를 Azure 서비스에 게시하는 기능을 제공합니다. Azure 서비스는 클라우드 또는 에지에서 실행할 수 있습니다. 이 플랫폼을 사용하여 비디오 분석을 통해 IoT 솔루션을 개선할 수 있습니다.

자세한 내용은 GitHub의 [Live Video Analytics](https://github.com/Azure/live-video-analytics) 를 참조 하세요.

## <a name="iot-edge-lva-gateway-module"></a>IoT Edge LVA 게이트웨이 모듈

IoT Edge LVA 게이트웨이 모듈은 카메라를 새 장치로 인스턴스화하고 IoT 장치 클라이언트 SDK를 사용 하 여 IoT Central에 직접 연결 합니다.

이 참조 구현에서 장치는에 지에서 대칭 키를 사용 하 여 솔루션에 연결 합니다. 장치 연결에 대 한 자세한 내용은 [Azure에 연결 IoT Central을](../core/concepts-get-connected.md) 참조 하세요.

## <a name="media-graph"></a>미디어 그래프

미디어 그래프를 사용 하 여 미디어를 캡처할 위치, 처리 방법 및 결과를 전달할 위치를 정의할 수 있습니다. 원하는 방식으로 구성 요소 또는 노드를 연결 하 여 미디어 그래프를 구성 합니다. 자세한 내용은 GitHub의 [미디어 그래프](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph) 를 참조 하세요.

## <a name="next-steps"></a>다음 단계

제안 된 다음 단계는 [Azure IoT Central에서 video analytics 응용 프로그램을 만드는](tutorial-video-analytics-create-app.md)방법을 설명 하는 것입니다.
