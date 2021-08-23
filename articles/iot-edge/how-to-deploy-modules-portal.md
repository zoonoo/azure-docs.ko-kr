---
title: Azure Portal에서 모듈 배포 - Azure IoT Edge
description: Azure Portal에서 IoT Hub를 사용하여 배포 매니페스트에 구성된 대로 IoT Hub의 IoT Edge 모듈을 IoT Edge 디바이스로 푸시합니다.
author: kgremban
ms.author: kgremban
ms.date: 10/13/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: df229db3f68de7bdd59a3074e3011bebc8676178
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122567359"
---
# <a name="deploy-azure-iot-edge-modules-from-the-azure-portal"></a>Azure Portal에서 Azure IoT Edge 모듈 배포

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

비즈니스 논리를 사용하여 IoT Edge 모듈을 만들면 디바이스에 배포하여 에지에서 작동시킵니다. 데이터를 수집하고 처리하기 위해 다중 모듈을 사용한 경우 한 번에 모두 배포하고 여기에 연결된 회람 규칙을 선언할 수 있습니다.

이 아티클에서는 배포 매니페스트를 만들고 IoT Edge 디바이스에 배포를 푸시하는 방법을 Azure Portal에서 어떻게 설명하는지를 보여줍니다. 해당 공유 태그에 따라 다중 디바이스를 대상으로 지정하는 배포를 만드는 방법에 대한 정보는 [대규모 IoT Edge 모듈 배포 및 모니터링](how-to-deploy-at-scale.md)을 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소

* Azure 구독의 [IoT Hub](../iot-hub/iot-hub-create-through-portal.md)
* IoT Edge 디바이스.

  IoT Edge 디바이스를 설정하지 않은 경우 Azure 가상 머신에서 만들 수 있습니다. 빠른 시작 문서 중 하나에 있는 단계에 따라 [가상 Linux 디바이스를 만들거나](quickstart-linux.md) [가상 Windows 디바이스를 만듭니다](quickstart.md).

## <a name="configure-a-deployment-manifest"></a>배포 매니페스트 구성

배포 매니페스트는 배포할 모듈, 모듈 간의 데이터 흐름 및 모듈 쌍의 desired 속성을 설명하는 JSON 문서입니다. 배포 매니페스트의 작동 방식 및 생성 방법에 대한 자세한 내용은 [IoT Edge 모듈을 사용, 구성 및 다시 사용하는 방법에 대한 이해](module-composition.md)를 참조하세요.

Azure Portal에는 JSON 문서를 수동으로 빌드하지 않고 배포 매니페스트를 만드는 방법을 설명하는 마법사가 포함됩니다. **모듈 추가**, **경로 지정** 및 **배포 검토** 등 세 가지 단계가 있습니다.

>[!NOTE]
>이 문서의 단계는 IoT Edge 에이전트 및 허브의 최신 스키마 버전을 반영합니다. 스키마 버전 1.1은 IoT Edge 버전 1.0.10 함께 출시되었으며 모듈 시작 순서 및 경로 우선 순위 지정 기능을 제공합니다.
>
>1\.0.9 또는 이전 버전을 실행하는 디바이스에 배포하는 경우 마법사의 **모듈** 단계에서 **런타임 설정** 을 편집하여 스키마 버전 1.0을 사용합니다.

### <a name="select-device-and-add-modules"></a>디바이스 선택 및 모듈 추가

1. [Azure Portal](https://portal.azure.com)에 로그인하고 IoT Hub로 이동합니다.
1. 왼쪽 창의 메뉴에서 **IoT Edge** 를 선택합니다.
1. 디바이스 목록에서 대상 디바이스의 ID를 클릭합니다.
1. 위쪽 막대에서 **모듈 설정** 을 선택합니다.
1. 페이지의 **컨테이너 레지스트리 설정** 섹션에서는 모듈 이미지를 포함하는 프라이빗 컨테이너 레지스트리에 액세스할 수 있는 자격 증명을 제공합니다.
1. 페이지의 **IoT Edge 모듈** 섹션에서 **추가** 를 선택합니다.
1. 드롭다운 메뉴에서 다음 세 가지 유형의 모듈 중 하나를 선택합니다.

   * **IoT Edge 모듈** - 모듈 이름과 컨테이너 이미지 URI를 제공합니다. 예를 들어 샘플 SimulatedTemperatureSensor 모듈에 대한 이미지 URI는 `mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0`입니다. 모듈 이미지가 프라이빗 컨테이너 레지스트리에 저장되어 있는 경우 이 페이지에서 자격 증명을 추가하여 이미지에 액세스합니다.
   * **Marketplace 모듈** - Azure Marketplace에서 호스트되는 모듈입니다. 일부 마켓플레이스 모듈에는 추가 구성이 필요하므로 [Azure Marketplace IoT Edge 모듈](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules) 목록에서 모듈 세부 정보를 검토하세요.
   * **Azure Stream Analytics 모듈** - Azure Stream Analytics 워크로드에서 생성된 모듈입니다.

1. 모듈을 추가한 후 목록에서 모듈 이름을 선택하여 모듈 설정을 엽니다. 필요한 경우 선택적 필드를 작성합니다.

   사용 가능한 모듈 설정에 대한 자세한 내용은 [모듈 구성 및 관리](module-composition.md#module-configuration-and-management)를 참조하세요.

   모듈 쌍에 대한 자세한 내용은 [desired 속성 정의 또는 업데이트](module-composition.md#define-or-update-desired-properties)을 참조하세요.

1. 6 ~ 8 단계를 반복하여 배포에 모듈을 추가합니다.
1. **다음: 경로** 를 선택하여 경로 섹션으로 진행합니다.

### <a name="specify-routes"></a>경로 지정

**경로** 탭에서 모듈과 IoT Hub 사이에서 메시지가 전달되는 방식을 정의합니다. 메시지는 이름/값 쌍을 사용하여 생성됩니다. 기본적으로 새로운 디바이스에 대한 최초 배포는 이름이 **route** 이고 **FROM /messaages/\* INTO $upstream** 으로 정의된 경로를 포함합니다. 즉, 모든 모듈에 의한 모든 메시지 출력은 IoT Hub에 전송됩니다.  

**우선 순위** 및 **TTL(Time to Live)** 매개 변수는 경로 정의에 포함할 수 있는 선택 사항 매개 변수입니다. 우선 순위 매개 변수를 사용하여 메시지를 먼저 처리해야 하는 경로 또는 마지막으로 처리해야 하는 경로를 선택할 수 있습니다. 우선 순위는 숫자 0~9을 설정하여 결정됩니다. 여기서 0은 우선 순위가 가장 높습니다. TTL(Time to Live) 매개 변수를 사용하여 해당 경로에 있는 메시지를 큐에서 처리되거나 삭제될 때까지 보유해야 하는 기간을 선언할 수 있습니다.

경로를 만드는 방법에 대한 자세한 내용은 [경로 선언](module-composition.md#declare-routes)을 참조하세요.

경로가 설정되면 **다음: 검토 + 만들기** 를 선택하여 마법사의 다음 단계로 진행합니다.

### <a name="review-deployment"></a>배포 검토

검토 섹션에서는 이전 두 개의 섹션에서 선택한 항목에 따라 생성된 JSON 배포 매니페스트를 보여줍니다. 추가하지 않고 선언된 **$edgeAgent** 및 **$edgeHub** 라는 두 개의 모듈이 있습니다. 이 두 개의 모듈은 [IoT Edge 런타임](iot-edge-runtime.md)을 구성하며 모든 배포에서 필수 기본값입니다.

배포 정보를 검토한 다음 **제출** 을 선택합니다.

## <a name="view-modules-on-your-device"></a>디바이스에서 모듈 보기

디바이스에 모듈을 배포하면 IoT Hub의 디바이스 세부 정보 페이지에서 모든 모듈을 확인할 수 있습니다. 이 페이지에서는 배포 상태 및 종료 코드와 같은 유용한 정보뿐만 아니라 배포된 각 모듈의 이름을 표시합니다.

## <a name="deploy-modules-from-azure-marketplace"></a>Azure Marketplace에서 모듈 배포

[Azure Marketplace](https://azuremarketplace.microsoft.com/)는 [IoT Edge 모듈](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules)을 포함하여 Azure에서 실행되도록 인증되고 최적화된 광범위한 엔터프라이즈 애플리케이션 및 솔루션을 검색할 수 있는 온라인 애플리케이션 및 서비스 마켓플레이스입니다.

Azure Marketplace 및 IoT Hub에서 IoT Edge 모듈을 배포할 수 있습니다.

### <a name="deploy-from-azure-marketplace"></a>Azure Marketplace에서 배포

Marketplace에서 IoT Edge 모듈을 검토하여 원하는 모듈을 찾으면 **만들기** 또는 **지금 가져오기** 를 선택하여 배포할 수 있습니다. 선택한 IoT Edge 모듈에 따라 달라질 수 있는 배포 마법사 단계를 진행합니다.

1. **계속** 을 선택하여 공급자의 사용 약관 및 개인정보처리방침을 확인합니다. 먼저 연락처 정보를 제공해야 할 수 있습니다.
1. 대상 디바이스가 연결된 구독 및 IoT Hub를 선택합니다.
1. **디바이스에 배포** 를 선택합니다.
1. 디바이스 이름을 입력하거나 **디바이스 찾기** 를 선택하여 허브에 등록된 디바이스 중에서 찾습니다.
1. 원하는 경우 다른 모듈 추가를 포함하여 배포 매니페스트를 구성하는 표준 프로세스를 계속하려면 **만들기** 를 선택합니다. 이미지 URI, 만들기 옵션 및 desired 속성과 같은 새 모듈에 대한 세부 정보가 미리 정의되어 있지만 변경할 수 있습니다.

모듈이 Azure Portal의 IoT Hub에 배포되었는지 확인합니다. 디바이스를 선택하고 **모듈 설정** 을 선택하면 모듈이 **IoT Edge 모듈** 섹션에 나열됩니다.

### <a name="deploy-from-azure-iot-hub"></a>Azure IoT Hub에서 배포

Azure Marketplace에서 Azure Portal의 IoT Hub 내 디바이스에 모듈을 신속하게 배포할 수 있습니다.

1. Azure Portal에서 IoT Hub로 이동합니다.
1. 왼쪽 창의 **자동 디바이스 관리** 에서 **IoT Edge** 를 선택합니다.
1. 배포를 수신할 IoT Edge 디바이스를 선택합니다.
1. 위쪽 막대에서 **모듈 설정** 을 선택합니다.
1. **IoT Edge 모듈** 섹션에서 **추가** 를 클릭하고 드롭다운 메뉴에서 **Marketplace 모듈** 을 선택합니다.

![IoT Hub에서 모듈 추가](./media/how-to-deploy-modules-portal/iothub-add-module.png)

**IoT Edge 모듈 마켓플레이스** 페이지에서 모듈을 선택합니다. 선택한 모듈은 구독, 리소스 그룹, 디바이스에 대해 자동으로 구성됩니다. 그런 다음 IoT Edge 모듈 목록에 표시됩니다. 일부 모듈에는 추가 구성이 필요할 수 있습니다.

> [!TIP]
> Azure IoT Hub의 IoT Edge 모듈에 대한 정보는 제한적입니다. 먼저 Azure Marketplace의 [IoT Edge 모듈](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules)에 대해 자세히 알아볼 수 있습니다.

본 문서 앞부분의 [경로 지정](#specify-routes) 및 [배포 검토](#review-deployment)에서 설명한 대로 **다음: 경로** 를 선택하고 배포를 진행합니다.

## <a name="next-steps"></a>다음 단계

[대규모 IoT Edge 모듈을 배포 및 모니터링](how-to-deploy-at-scale.md)하는 방법 알아보기
