---
title: Azure Portal에서 IoT Central 관리 | Microsoft Docs
description: 이 문서에서는 Azure Portal에서 IoT Central 애플리케이션을 만들고 관리하는 방법을 설명합니다.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 04/17/2021
ms.topic: how-to
ms.openlocfilehash: f84aa2c6e89c0a40249423ec8e45ac804cf03a66
ms.sourcegitcommit: b35c7f3e7f0e30d337db382abb7c11a69723997e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2021
ms.locfileid: "109685102"
---
# <a name="manage-iot-central-from-the-azure-portal"></a>Azure Portal에서 IoT Central 관리

[!INCLUDE [iot-central-selector-manage](../../../includes/iot-central-selector-manage.md)]

[Azure Portal](https://portal.azure.com)을 사용하여 IoT Central 애플리케이션을 만들고 관리할 수 있습니다.

## <a name="create-iot-central-applications"></a>IoT Central 애플리케이션 만들기

[!INCLUDE [Warning About Access Required](../../../includes/iot-central-warning-contribitorrequireaccess.md)]

애플리케이션을 만들려면 Azure Portal의 [IoT Central 애플리케이션](https://ms.portal.azure.com/#create/Microsoft.IoTCentral) 페이지로 이동합니다.

![IoT Central 양식 만들기](media/howto-manage-iot-central-from-portal/image6a.png)

* **리소스 이름** 은 Azure 리소스 그룹의 IoT Central 애플리케이션에 대해 선택할 수 있는 고유한 이름입니다.

* **애플리케이션 URL** 은 애플리케이션 액세스에 사용할 수 있는 URL입니다.

* **템플릿** 은 만들려는 IoT Central 애플리케이션의 형식입니다. 업계 관련 템플릿 목록에서 새 애플리케이션을 만들어 빠르게 시작하거나, **사용자 지정 애플리케이션** 템플릿을 사용하여 처음부터 시작할 수 있습니다.

* **위치** 는 애플리케이션을 만들려는 [지리](https://azure.microsoft.com/global-infrastructure/geographies/)입니다. 일반적으로 최적의 성능을 얻으려면 디바이스와 물리적으로 가장 가까운 위치를 선택해야 합니다. Azure IoT Central는 현재 다음 위치에서 사용할 수 있습니다.

  * 아시아 태평양
  * 오스트레일리아
  * 유럽
  * 일본
  * 영국
  * 미국

  위치를 선택하면 나중에 다른 위치로 애플리케이션을 이동할 수 없습니다.

모든 필드를 채운 후 **만들기** 를 선택합니다. 자세한 내용은 [IoT Central 애플리케이션 만들기](howto-create-iot-central-application.md)를 참조하세요.

## <a name="manage-existing-iot-central-applications"></a>기존 IoT Central 애플리케이션 관리

Azure IoT Central 애플리케이션이 이미 있으면 삭제하거나 Azure Portal의 다른 구독 또는 리소스 그룹으로 이동할 수 있습니다.

> [!NOTE]
> *무료* 요금제를 사용하여 만든 애플리케이션에는 Azure 구독이 필요하지 않으므로 Azure Portal의 Azure 구독에 나열되지 않습니다. IoT Central 포털에서만 무료 앱을 보고 관리할 수 있습니다.

시작하려면 Azure Portal 맨 위에 있는 검색 창에서 애플리케이션을 검색합니다. _IoT Central 애플리케이션_ 을 검색하고 서비스를 선택하여 모든 애플리케이션을 볼 수도 있습니다.

![첫 번째 서비스를 선택한 상태에서 "IoT Central 애플리케이션"에 대한 검색 결과를 보여 주는 스크린샷](media/howto-manage-iot-central-from-portal/search-iot-central.png)

검색 결과에서 애플리케이션을 선택하면 Azure Portal에 해당 개요가 표시됩니다. **IoT Central 애플리케이션 URL** 을 선택하여 해당 애플리케이션으로 이동할 수 있습니다.

!["IoT Central 애플리케이션 URL"이 강조 표시된 "개요" 페이지를 보여주는 스크린샷.](media/howto-manage-iot-central-from-portal/image3.png)

다른 리소스 그룹으로 애플리케이션을 이동하려면 리소스 그룹 옆에 있는 **변경** 을 선택합니다. **리소스 이동** 페이지에서 이 애플리케이션을 이동할 리소스 그룹을 선택합니다.

!["리소스 그룹(변경)"이 강조 표시된 "개요" 페이지를 보여주는 스크린샷.](media/howto-manage-iot-central-from-portal/image4a.png)

다른 구독으로 애플리케이션을 이동하려면 구독 옆에 있는 **변경** 을 선택합니다. **리소스 이동** 페이지에서 이 애플리케이션을 이동할 구독을 선택합니다.

![관리 포털: 리소스 관리](media/howto-manage-iot-central-from-portal/image5a.png)

## <a name="next-steps"></a>다음 단계

Azure Portal에서 Azure IoT Central 애플리케이션을 관리하는 방법을 알아보았으므로 다음 단계를 진행하는 것이 좋습니다.

> [!div class="nextstepaction"]
> [애플리케이션 관리](howto-administer.md)