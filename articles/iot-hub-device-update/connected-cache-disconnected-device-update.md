---
title: Microsoft 연결 된 캐시를 사용 하 여 연결이 끊어진 장치 업데이트에 대 한 지원 이해 | Microsoft Docs
titleSuffix: Device Update for Azure IoT Hub
description: Microsoft 연결 캐시를 사용 하 여 연결이 끊어진 장치 업데이트에 대 한 지원 이해
author: andyriv
ms.author: andyriv
ms.date: 2/16/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: e2b27934f58402ecfb7dabf5560dc43e45f3f7dd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "101679546"
---
# <a name="understand-support-for-disconnected-device-updates"></a>연결 되지 않은 장치 업데이트에 대 한 지원 이해

투명 게이트웨이 시나리오에서 하나 이상의 장치는 Azure IoT Hub에 대 한 연결을 유지 하는 단일 게이트웨이 장치를 통해 메시지를 전달할 수 있습니다. 이러한 경우에는 자식 장치가 인터넷에 연결 되어 있지 않거나 인터넷에서 콘텐츠를 다운로드 하도록 허용 되지 않을 수 있습니다. Microsoft 연결 된 캐시 미리 보기 IoT Edge 모듈은 고객에 대 Azure IoT Hub 한 장치 업데이트를 제공 하 여 지능형 네트워크 내 캐시의 기능을 제공 합니다 .이 기능을 통해 지능형 네트워크 내 캐시의 기능을 사용할 수 있습니다 .이를 통해 Linux OS 기반 IoT Edge 장치의 이미지 기반 및 패키지 기반 업데이트를 사용할 수 있습니다 (다운스트림 IoT 장치 Azure IoT Hub).

## <a name="how-does-microsoft-connected-cache-preview-for-device-update-for-azure-iot-hub-work"></a>Azure IoT Hub의 장치 업데이트에 대 한 Microsoft 연결 된 캐시 미리 보기는 어떻게 작동 하나요?

Microsoft 연결 된 캐시는 Azure IoT Hub 콘텐츠에 대해 장치 업데이트를 위해 게시 된 콘텐츠에 대 한 지능적이 고 투명 한 캐시 이며, 패키지 리포지토리와 같은 다른 원본의 콘텐츠를 캐시 하도록 사용자 지정할 수 있습니다. Microsoft 연결 된 캐시는 배달 최적화 클라이언트에서 요청 하는 정확한 파일 범위에 대 한 클라이언트 요청에 의해 준비 콘텐츠를 사전에 가져오지 않는 콜드 캐시입니다. 아래 다이어그램 및 단계별 설명에서는 Microsoft 연결 된 캐시가 Azure IoT Hub 인프라의 장치 업데이트 내에서 작동 하는 방식을 설명 합니다.

>[!Note]
>이 흐름을 정의할 때 IoT Edge 게이트웨이가 인터넷에 연결 된 것으로 가정 합니다. 다운스트림 IoT Edge 게이트웨이 (중첩 된 Edge) 시나리오의 경우 CDN ("Content Delivery Network")은 부모 IoT Edge 게이트웨이에서 호스트 되는 MCC로 간주할 수 있습니다.

  :::image type="content" source="media/connected-cache-overview/disconnected-device-update.png" alt-text="연결 되지 않은 장치 업데이트" lightbox="media/connected-cache-overview/disconnected-device-update.png":::

1. Microsoft 연결 된 캐시는 IoT Edge 모듈로 온-프레미스 서버에 배포 됩니다.
2. Azure IoT Hub 클라이언트에 대 한 장치 업데이트는 IoT 리프 장치에 대 한 장치 연결 문자열의 Ghostname 특성 **또는** IoT Edge 자식 장치에 대 한 구성. toml에 설정 된 parent_hostname를 통해 Microsoft 연결 캐시에서 콘텐츠를 다운로드 하도록 구성 됩니다.
3. 두 경우 모두 Azure IoT Hub 클라이언트에 대 한 장치 업데이트는 Azure IoT Hub 서비스에 대 한 장치 업데이트에서 업데이트 콘텐츠 다운로드 명령을 수신 하 고 CDN 대신 Microsoft 연결 된 캐시에 업데이트 콘텐츠를 요청 합니다. 기본적으로 Microsoft 연결 된 캐시는 http 포트 80에서 수신 하도록 구성 되 고, 배달 최적화 클라이언트는 포트 80에서 콘텐츠 요청을 하므로 부모는이 포트에서 수신 하도록 구성 되어야 합니다.  지금은 http 프로토콜만 지원 됩니다.
4. Microsoft 연결 된 캐시 서버는 CDN에서 콘텐츠를 다운로드 하 고, 디스크에 저장 된 로컬 캐시를 시드 하 고 Azure IoT Hub 클라이언트에 대 한 장치 업데이트에 콘텐츠를 전달 합니다.
   
>[!Note]
>패키지 기반 업데이트를 사용 하는 경우 관리자는 필수 패키지 호스트 이름으로 Microsoft 연결 된 캐시 서버를 구성 합니다.

5. 동일한 업데이트 콘텐츠에 대 한 Azure IoT Hub 클라이언트에 대 한 다른 장치 업데이트의 후속 요청은 이제 캐시에서 제공 되며 Microsoft 연결 된 캐시는 동일한 콘텐츠에 대해 CDN에 요청을 수행 하지 않습니다.

### <a name="supporting-industrial-iot-iiot-with-parentchild-hosting-scenarios"></a>부모/자식 호스팅 시나리오로 IIoT (산업용 IoT) 지원

다운스트림 또는 자식 IoT Edge 게이트웨이가 Microsoft 연결 된 캐시 서버를 호스팅하는 경우 Microsoft 연결 된 캐시 서버를 호스트 하는 부모 IoT Edge 게이트웨이에서 업데이트 콘텐츠를 요청 하도록 구성 됩니다. 인터넷에 연결 된 Microsoft 연결 캐시 서버를 호스팅하는 부모 IoT Edge 게이트웨이에 도달 하기 전에 필요한 만큼 많은 수준에 필요 합니다. 인터넷에 연결 된 서버에서 콘텐츠는 CDN에서 요청 되며,이 시점에서 콘텐츠를 원래 요청한 자식 IoT Edge 게이트웨이로 다시 배달 합니다. 콘텐츠는 모든 수준에서 디스크에 저장 됩니다.

## <a name="access-to-the-microsoft-connected-cache-preview-for-device-update-for-azure-iot-hub"></a>Azure IoT Hub에 대 한 장치 업데이트에 대 한 Microsoft 연결 된 캐시 미리 보기에 액세스

Microsoft 연결 된 캐시 IoT Edge 모듈은 Azure IoT Hub 장치 업데이트를 사용 하 여 솔루션을 배포 하는 고객을 위한 미리 보기로 출시 되었습니다. 초대를 통해 미리 보기에 액세스할 수 있습니다. IoT Hut의 장치 업데이트에 대 한 Microsoft 연결 된 캐시 미리 보기에 대 한 [액세스를 요청](https://aka.ms/MCCForDeviceUpdateForIoT) 하 고, 모듈에 액세스 하려는 경우 요청 된 정보를 제공 합니다.
