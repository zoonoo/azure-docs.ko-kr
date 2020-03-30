---
title: Azure 포털에서 모듈 배포 - Azure IoT Edge
description: Azure 포털에서 IoT Hub를 사용하여 배포 매니페스트에 의해 구성된 IoT Hub에서 IoT Edge 장치로 IoT Edge 모듈을 푸시합니다.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 12/30/2019
ms.topic: conceptual
ms.reviewer: menchi
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 5c44561895bc1905328ec0eb357bee1c68a8eb55
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79271448"
---
# <a name="deploy-azure-iot-edge-modules-from-the-azure-portal"></a>Azure Portal에서 Azure IoT Edge 모듈 배포

비즈니스 논리를 사용하여 IoT Edge 모듈을 만들면 디바이스에 배포하여 에지에서 작동시킵니다. 데이터를 수집하고 처리하기 위해 다중 모듈을 사용한 경우 한 번에 모두 배포하고 여기에 연결된 회람 규칙을 선언할 수 있습니다.

이 아티클에서는 배포 매니페스트를 만들고 IoT Edge 디바이스에 배포를 푸시하는 방법을 Azure Portal에서 어떻게 설명하는지를 보여줍니다. 공유 태그를 기반으로 여러 장치를 대상으로 하는 배포를 만드는 방법에 대한 자세한 내용은 [IoT Edge 모듈을 대규모로 배포 및 모니터링합니다.](how-to-deploy-monitor.md)

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독의 [IoT 허브입니다.](../iot-hub/iot-hub-create-through-portal.md)
* IoT Edge 런타임이 설치된 [IoT Edge 디바이스](how-to-register-device.md#register-in-the-azure-portal)

## <a name="configure-a-deployment-manifest"></a>배포 매니페스트 구성

배포 매니페스트는 배포할 모듈, 모듈 간의 데이터 흐름 및 모듈 쌍의 desired 속성을 설명하는 JSON 문서입니다. 배포 매니페스트의 작동 방식 및 생성 방법에 대한 자세한 내용은 [IoT Edge 모듈을 사용, 구성 및 다시 사용하는 방법에 대한 이해](module-composition.md)를 참조하세요.

Azure Portal에는 JSON 문서를 수동으로 빌드하지 않고 배포 매니페스트를 만드는 방법을 설명하는 마법사가 포함됩니다. **모듈 추가**, **경로 지정** 및 **배포 검토** 등 세 가지 단계가 있습니다.

### <a name="select-device-and-add-modules"></a>장치 선택 및 모듈 추가

1. [Azure Portal](https://portal.azure.com)에 로그인하고 IoT Hub로 이동합니다.
1. 왼쪽 창에서 메뉴에서 **IoT 가장자리를** 선택합니다.
1. 디바이스 목록에서 대상 디바이스의 ID를 클릭합니다.
1. 위쪽 막대에서 **모듈 설정**을 선택합니다.
1. 페이지의 **컨테이너 레지스트리 설정** 섹션에서 모듈 이미지가 포함된 개인 컨테이너 레지스트리에 액세스할 수 있는 자격 증명을 제공합니다.
1. 페이지의 **IoT 에지 모듈** 섹션에서 **추가를**선택합니다.
1. 드롭다운 메뉴에서 모듈 유형을 살펴보십시오.

   * **IoT 에지 모듈** - 모듈 이름 및 컨테이너 이미지 URI를 제공합니다. 예를 들어 샘플 시뮬레이션온도센서 모듈의 이미지 `mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0`URI는 . 모듈 이미지가 개인 컨테이너 레지스트리에 저장된 경우 이 페이지에 자격 증명을 추가하여 이미지에 액세스합니다.
   * **마켓플레이스 모듈** - Azure 마켓플레이스에서 호스팅되는 모듈입니다. 일부 마켓플레이스 모듈에는 추가 구성이 필요하므로 Azure Marketplace IoT Edge 모듈 목록의 모듈 세부 정보를 [검토합니다.](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules)
   * **Azure 스트림 분석 모듈** - Azure 스트림 분석 워크로드에서 생성된 모듈입니다.

1. 모듈을 추가한 후 목록에서 모듈 이름을 선택하여 모듈 설정을 엽니다. 필요한 경우 선택적 필드를 작성합니다. 컨테이너 생성 옵션, 다시 시작 정책 및 원하는 상태에 대한 자세한 내용은 [EdgeAgent desired 속성](module-edgeagent-edgehub.md#edgeagent-desired-properties)을 참조하세요. 모듈 쌍에 대한 자세한 내용은 [desired 속성 정의 또는 업데이트](module-composition.md#define-or-update-desired-properties)을 참조하세요.
1. 필요한 경우 5단계부터 8단계까지 반복하여 배포에 모듈을 추가합니다.
1. **다음: 경로** 섹션으로 계속할 경로를 선택합니다.

### <a name="specify-routes"></a>경로 지정

**경로** 탭에서 모듈과 IoT Hub 사이에서 메시지가 전달되는 방식을 정의합니다. 메시지는 이름/값 쌍을 사용하여 생성됩니다. 기본적으로 경로는 **경로라고** 하며 **from\* /messages/INTO $upstream**정의되며, 이는 모든 모듈에서 출력하는 모든 메시지가 IoT hub로 전송된다는 것을 의미합니다.  

[경로 선언의](module-composition.md#declare-routes)정보로 경로를 추가하거나 업데이트한 다음 **다음: 검토 + 만들기를** 선택하여 마법사의 다음 단계로 계속 합니다.

### <a name="review-deployment"></a>배포 검토

검토 섹션에서는 이전 두 개의 섹션에서 선택한 항목에 따라 생성된 JSON 배포 매니페스트를 보여줍니다. 추가하지 않고 선언된 **$edgeAgent** 및 **$edgeHub**라는 두 개의 모듈이 있습니다. 이 두 개의 모듈은 [IoT Edge 런타임](iot-edge-runtime.md)을 구성하며 모든 배포에서 필수 기본값입니다.

배포 정보를 검토한 다음 **을 선택합니다.**

## <a name="view-modules-on-your-device"></a>디바이스에서 모듈 보기

장치에 모듈을 배포한 후에는 IoT Hub의 장치 세부 정보 페이지에서 모듈을 모두 볼 수 있습니다. 이 페이지에서는 배포 상태 및 종료 코드와 같은 유용한 정보뿐만 아니라 배포된 각 모듈의 이름을 표시합니다.

## <a name="deploy-modules-from-azure-marketplace"></a>Azure Marketplace에서 모듈 배포

[Azure Marketplace는](https://azuremarketplace.microsoft.com/) [IoT Edge 모듈을](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules)포함하여 Azure에서 실행하도록 인증되고 최적화된 광범위한 엔터프라이즈 응용 프로그램 및 솔루션을 탐색할 수 있는 온라인 응용 프로그램 및 서비스 마켓플레이스입니다.

Azure 마켓플레이스 및 IoT Hub에서 IoT Edge 모듈을 배포할 수 있습니다.

### <a name="deploy-from-azure-marketplace"></a>Azure Marketplace에서 배포

마켓플레이스에서 IoT Edge 모듈을 정독하고 원하는 모듈을 찾으면 지금 **만들기** 또는 **받기를**선택하여 배포할 수 있습니다. 선택한 IoT Edge 모듈에 따라 달라질 수 있는 배포 마법사 단계를 진행합니다.

1. **계속**을 선택하여 공급자의 사용 약관 및 개인정보처리방침을 확인합니다. 먼저 연락처 정보를 제공해야 할 수 있습니다.
1. 대상 디바이스가 연결된 구독 및 IoT Hub를 선택합니다.
1. **디바이스에 배포**를 선택합니다.
1. 디바이스 이름을 입력하거나 **디바이스 찾기**를 선택하여 허브에 등록된 디바이스 중에서 찾습니다.
1. 원하는 경우 다른 모듈 추가를 포함하여 배포 매니페스트를 구성하는 표준 프로세스를 계속하려면 **만들기**를 선택합니다. 이미지 URI, 만들기 옵션 및 desired 속성과 같은 새 모듈에 대한 세부 정보가 미리 정의되어 있지만 변경할 수 있습니다.

모듈이 Azure 포털의 IoT Hub에 배포되었는지 확인합니다. 장치를 선택하고 **모듈 설정을** 선택하면 **모듈이 IoT Edge 모듈** 섹션에 나열되어야 합니다.

### <a name="deploy-from-azure-iot-hub"></a>Azure IoT 허브에서 배포

Azure 마켓플레이스에서 Azure 포털의 IoT Hub에서 장치에 모듈을 신속하게 배포할 수 있습니다.

1. Azure Portal에서 IoT Hub로 이동합니다.
1. 왼쪽 창에서 자동 **장치 관리**에서 **IoT Edge를**선택합니다.
1. 배포를 받을 IoT Edge 장치를 선택합니다.
1. 위쪽 막대에서 **모듈 설정**을 선택합니다.
1. **IoT 에지 모듈** 섹션에서 **추가**를 클릭하고 드롭다운 메뉴에서 **마켓플레이스 모듈을** 선택합니다.

![IoT Hub에 모듈 추가](./media/how-to-deploy-modules-portal/iothub-add-module.png)

**IoT 에지 모듈 마켓플레이스** 페이지에서 모듈을 선택합니다. 선택한 모듈은 구독, 리소스 그룹 및 장치에 대해 자동으로 구성됩니다. 그런 다음 IoT Edge 모듈 목록에 나타납니다. 일부 모듈은 추가 구성이 필요할 수 있습니다.

> [!TIP]
> Azure IoT Hub의 IoT Edge 모듈에 대한 정보는 제한되어 있습니다. 먼저 Azure 마켓플레이스에서 [IoT Edge 모듈에](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules) 대해 자세히 알아볼 수 있습니다.

**다음 을 선택: 경로** [지정](#specify-routes) 및 배포 검토이 문서의 앞에서 설명한 대로 경로를 지정하고 [배포를](#review-deployment) 계속합니다.

## <a name="next-steps"></a>다음 단계

[대규모 IoT Edge 모듈을 배포 및 모니터링](how-to-deploy-monitor.md)하는 방법 알아보기
