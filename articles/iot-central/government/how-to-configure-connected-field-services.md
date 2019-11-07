---
title: Dynamics 365 Field Service에 Azure IoT Central 애플리케이션 연결 | Microsoft Docs
description: Azure IoT Central 및 Dynamics 365 Field Service를 사용하여 종단 간 솔루션을 빌드하는 방법 알아보기
author: miriambrus
ms.author: miriamb
ms.date: 10/23/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 957e9337bf8ea5941b140ba4f3266417d36df6a7
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73498769"
---
# <a name="build-end-to-end-solution-with-azure-iot-central-and-dynamics-365-field-service"></a>Azure IoT Central 및 Dynamics 365 Field Service를 사용하여 종단 간 솔루션 빌드 

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

빌더는 Azure IoT Central 애플리케이션을 다른 비즈니스 시스템에 연결할 수 있습니다. 


예를 들어, 연결된 폐기물 관리 솔루션에서 연결된 폐기물 용기의 IoT 센서 데이터를 기준으로 쓰레기 수집 트럭의 배차를 최적화할 수 있습니다. [IoT Central 연결된 폐기물 관리 애플리케이션](./tutorial-connected-waste-management.md)에서 규칙 및 작업을 구성하고, Dynamics Field Service에서 경고 만들기를 트리거하도록 설정할 수 있습니다. 이 시나리오는 애플리케이션 및 서비스에서 워크플로를 자동화하기 위해 IoT Central에서 직접 구성할 수 있는 Microsoft Flow를 사용하여 수행됩니다. 또한 Field Service의 서비스 활동에 따라, 정보를 Azure IoT Central로 다시 보낼 수 있습니다. 

## <a name="how-to-connect-your-azure-iot-central-application-with-dynamics-365-field-services"></a>Dynamics 365 Field Service에 Azure IoT Central 애플리케이션을 연결하는 방법 

순수한 구성 환경에서 아래와 같은 종단 간 통합 프로세스를 쉽게 구현할 수 있습니다.
* Azure IoT Central은 진단을 위해 디바이스 이상에 대한 정보를 Connected Field Service(IoT 경고로)에 보낼 수 있습니다.
* Connected Field Service는 디바이스 이상에 따라 트리거되는 케이스 또는 작업 주문을 만들 수 있습니다.
* Connected Field Service는 가동 중지 시간 문제를 방지하기 위해 기술자의 검사를 예약할 수 있습니다.
* Azure IoT Central 디바이스 대시보드는 관련 서비스 및 일정 정보로 업데이트할 수 있습니다.


## <a name="next-steps"></a>다음 단계
* [IoT Central 정부 템플릿](./overview-iot-central-government.md)에 대해 자세히 알아보기
* [IoT Central](https://docs.microsoft.com/azure/iot-central/core/overview-iot-central)에 대해 자세히 알아보기
* [Dynamics 365 Field Services](https://docs.microsoft.com/dynamics365/field-service/cfs-iot-overview)에 대해 자세히 알아보기
