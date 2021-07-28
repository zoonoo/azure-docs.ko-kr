---
title: Microsoft 연결된 캐시를 사용하여 연결이 끊긴 디바이스 업데이트에 대한 지원 이해하기 | Microsoft Docs
titleSuffix: Device Update for Azure IoT Hub
description: Microsoft 연결된 캐시를 사용하여 연결이 끊긴 디바이스 업데이트에 대한 지원 이해하기
author: andyriv
ms.author: andyriv
ms.date: 2/16/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: e216d42ff1f279d87e657126514fcfb50960f806
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107811907"
---
# <a name="understand-support-for-disconnected-device-updates"></a>연결이 끊긴 디바이스 업데이트에 대한 지원 이해하기

투명 게이트웨이 시나리오에서는 하나 이상의 디바이스가 Azure IoT 허브와의 연결을 유지하는 단일 게이트웨이 디바이스를 통하여 메시지를 보낼 수 있습니다. 이러한 경우 자식 디바이스에 인터넷이 연결되어 있지 않거나 인터넷에서 콘텐츠를 다운로드할 수 없는 상태일 수 있습니다. Microsoft 연결된 캐시 미리 보기 IoT Edge 모듈은 Azure IoT 허브 고객에게 Linux OS 기반 디바이스 이후 및 IoT Edge 게이트웨이(다운스트림 IoT 디바이스)의 이미지 기반 및 패키지 기반 업데이트를 사용할 수 있도록 하는 지능형 네트워크 내 캐시 기능에 대한 디바이스 업데이트를 제공하고 Azure IoT 허브 고객을 위한 디바이스 업데이트 대역폭을 저장할 수 있게 돕습니다.

## <a name="how-does-microsoft-connected-cache-preview-for-device-update-for-azure-iot-hub-work"></a>Azure IoT Hub용 디바이스 업데이트에 대한 Microsoft 연결된 캐시 미리 보기의 작동 방식은 무엇인가요?

Microsoft 연결된 캐시 미리 보기는 Azure IoT Hub 콘텐츠에 대한 디바이스 업데이트용으로 게시되는 콘텐츠를 위한 지능형 투명 캐시로, 패키지 리포지토리 같은 기타 원본의 캐시 콘텐츠에 대해서도 사용자 지정할 수 있습니다. Microsoft 연결된 캐시는 콜드 캐시로 배달 최적화 클라이언트가 요청하는 정확한 파일 범위에 대한 클라이언트 요청으로 인하여 준비되며 콘텐츠를 미리 시드하지 않습니다. 다음의 다이어그램 및 단계별 설명에서는 Microsoft 연결된 캐시가 Azure IoT 허브 인프라용 디바이스 업데이트에서 작동하는 방식을 설명합니다.

>[!Note]
>이러한 흐름을 정의하는 데 있어 IoT Edge 게이트웨이가 인터넷에 연결되어 있는 것으로 먼저 가정합니다. 다운스트림 IoT Edge 게이트웨이(중첩된 에지) 시나리오에서 CDN(Content Delivery Network)은 부모 IoT Edge 게이트웨이에서 호스트된 MCC라고 간주할 수 있습니다.

  :::image type="content" source="media/connected-cache-overview/disconnected-device-update.png" alt-text="연결되지 않은 디바이스 업데이트" lightbox="media/connected-cache-overview/disconnected-device-update.png":::

1. Microsoft 연결된 캐시는 온-프레미스 서버에 IoT Edge 모듈로서 배포됩니다.
2. Azure IoT 허브용 디바이스 업데이트 클라이언트는 IoT 리프 디바이스 **또는** IoT Edge 자식 디바이스용 config.toml에 설정된 parent_hostname에 대한 디바이스 연결 문자열의 GatewayHostName 특성에 의하여 Microsoft 연결된 캐시에서 콘텐츠를 다운로드하도록 구성됩니다.
3. 양쪽 경우 모두에서 Azure IoT 허브용 디바이스 업데이트 클라이언트는 Azure IoT 허브 서비스용 디바이스 업데이트에서 업데이트 콘텐츠 다운로드 명령을 수신하며 CDN 대신 Microsoft 연결된 캐시에 업데이트 콘텐츠를 요청합니다. Microsoft 연결된 캐시는 기본적으로 http 포트 80에서 수신 대기하도록 구성되어 있으며 배달 최적화 클라이언트는 포트 80에서 콘텐츠를 요청하므로 부모가 포트 80를 수신 대기하도록 구성하여야 합니다.  현재는 http 프로토콜만 지원합니다.
4. Microsoft 연결된 캐시 서버는 CDN에서 콘텐츠를 다운로드하고 디스크에 저장한 로컬 캐시를 시드하며 콘텐츠를 Azure IoT 허브용 디바이스 업데이트 클라이언트에 전달합니다.
   
>[!Note]
>패키지 기반 업데이트를 사용하는 경우, 관리자가 Microsoft 연결된 캐시 서버를 필요한 패키지 호스트 이름으로 구성합니다.

5. 동일한 업데이트 콘텐츠에 대한 Azure IoT 허브용 디바이스 업데이트의 다른 클라이언트에서 오는 후속 요청은 캐시를 통하여 전달되며 Microsoft 연결된 캐시는 동일한 콘텐츠에 대하여서는 CDN에 요청하지 않습니다.

### <a name="supporting-industrial-iot-iiot-with-parentchild-hosting-scenarios"></a>부모/자식 호스팅 시나리오를 통한 IIoT(산업용 IoT) 지원

Microsoft 연결된 캐시 서버를 호스트하는 다운스트림 IoT Edge 게이트웨이 또는 자식 IoT Edge 게이트웨이는 Microsoft 연결된 캐시 서버를 호스트하는 부모 IoT Edge 게이트웨이에서 업데이트 콘텐츠를 요청하도록 구성됩니다. 이는 인터넷에 연결된 Microsoft 연결된 캐시 서버를 호스트하는 부모 IoT Edge 게이트웨이에 도달하기 전에 필요한 수준 수만큼 필요합니다. 인터넷이 연결된 서버에서 콘텐츠는 원래 그 콘텐츠를 요청하였던 자식 IoT Edge 게이트웨이로 그 콘텐츠가 반환될 때 CDN의 요청을 받습니다. 콘텐츠는 모든 수준에서 디스크에 저장됩니다.

## <a name="access-to-the-microsoft-connected-cache-preview-for-device-update-for-azure-iot-hub"></a>Azure IoT 허브용 디바이스 업데이트에 대한 Microsoft 연결된 캐시 액세스 미리 보기

Microsoft 연결된 캐시 IoT Edge 모듈은 Azure IoT 허브용 디바이스 업데이트를 사용하여 솔루션을 배포하는 고객들을 위하여 미리 보기로 릴리스되었습니다. 초대를 통하여 미리 보기에 액세스합니다. IoT 허브용 디바이스 업데이트를 위한 Microsoft 연결된 캐시 미리 보기에 [액세스를 요청](https://aka.ms/MCCForDeviceUpdateForIoT)하고, 해당 모듈에 액세스하고 싶으면 요청된 정보를 제공하세요.
