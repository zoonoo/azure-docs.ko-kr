---
title: Azure IoT Central 비디오 분석 개체 및 동작 감지 | Microsoft Docs
description: IoT Central에서 비디오 분석 - 개체 및 동작 감지 애플리케이션 템플릿을 사용하여 IoT Central 애플리케이션을 빌드하는 방법을 알아봅니다. 이 템플릿은 Live Video Analytics 및 연결된 카메라를 사용합니다.
author: KishorIoT
ms.author: nandab
ms.date: 07/27/2020
ms.topic: conceptual
ms.service: iot-central
ms.subservice: iot-central-retail
services: iot-central
ms.openlocfilehash: 48808f762536390287bae40e8af3849da20b81c2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91874291"
---
# <a name="video-analytics---object-and-motion-detection-application-architecture"></a>비디오 분석 - 개체 및 동작 감지 애플리케이션 아키텍처

**비디오 분석 - 개체 및 동작 감지** 애플리케이션 템플릿을 사용하여 Live Video Analytics 기능이 포함된 IoT 솔루션을 빌드할 수 있습니다.

:::image type="content" source="media/architecture-video-analytics/architecture.png" alt-text="비디오 분석 개체 및 동작 감지 구성 요소 개요 다이어그램":::

비디오 분석 솔루션의 주요 구성 요소는 다음과 같습니다.

## <a name="live-video-analytics-lva"></a>LVA(Live Video Analytics)

LVA는 에지 및 클라우드를 포괄하는 지능형 비디오 애플리케이션을 빌드하기 위한 플랫폼을 제공합니다. 이 플랫폼을 사용하여 에지 및 클라우드를 포괄하는 지능형 비디오 애플리케이션을 빌드할 수 있습니다. 이 플랫폼은 라이브 비디오를 캡처, 녹화 및 분석하는 기능과 비디오 또는 비디오 분석일 수 있는 결과를 Azure 서비스에 게시하는 기능을 제공합니다. Azure 서비스는 클라우드 또는 에지에서 실행할 수 있습니다. 이 플랫폼을 사용하여 비디오 분석을 통해 IoT 솔루션을 개선할 수 있습니다.

자세한 내용은 GitHub에서 [Live Video Analytics](https://github.com/Azure/live-video-analytics)를 참조하세요.

## <a name="iot-edge-lva-gateway-module"></a>IoT Edge LVA 게이트웨이 모듈

IoT Edge LVA 게이트웨이 모듈은 카메라를 새 디바이스로 인스턴스화하고 IoT 디바이스 클라이언트 SDK를 사용하여 IoT Central에 직접 연결합니다.

이 참조 구현에서 디바이스는 에지에서 대칭 키를 사용하여 솔루션에 연결합니다. 디바이스 연결에 대한 자세한 내용은 [Azure IoT Central에 연결](../core/concepts-get-connected.md)을 참조하세요.

## <a name="media-graph"></a>미디어 그래프

미디어 그래프를 사용하면 미디어를 캡처할 위치, 처리 방법, 결과를 전달할 위치를 정의할 수 있습니다. 원하는 방식으로 구성 요소 또는 노드를 연결하여 미디어 그래프를 구성합니다. 자세한 내용은 GitHub에서 [미디어 그래프](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph)를 참조하세요.

## <a name="next-steps"></a>다음 단계

제안된 다음 단계는 [비디오 분석 - 개체 및 동작 감지 애플리케이션 템플릿을 사용하여 IoT Central 애플리케이션을 배포](tutorial-video-analytics-deploy.md)하는 방법을 알아보는 것입니다.
