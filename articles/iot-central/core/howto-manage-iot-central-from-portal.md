---
title: Azure Portal에서 IoT Central 관리 | Microsoft Docs
description: 이 문서에서는 Azure Portal에서 IoT Central 애플리케이션을 만들고 관리하는 방법을 설명합니다.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 02/11/2020
ms.topic: how-to
manager: philmea
ms.openlocfilehash: 2af97206db00d683ab409710bc71a3b5048bf6ae
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "104658468"
---
# <a name="manage-iot-central-from-the-azure-portal"></a>Azure Portal에서 IoT Central 관리

[!INCLUDE [iot-central-selector-manage](../../../includes/iot-central-selector-manage.md)]

[Azure IoT Central 애플리케이션 관리자](https://aka.ms/iotcentral) 웹 사이트에서 IoT Central 애플리케이션을 만들고 관리하는 대신 [Azure Portal](https://portal.azure.com)을 사용하여 애플리케이션을 관리할 수 있습니다.

## <a name="create-iot-central-applications"></a>IoT Central 애플리케이션 만들기

[!INCLUDE [Warning About Access Required](../../../includes/iot-central-warning-contribitorrequireaccess.md)]


애플리케이션을 만들려면 [Azure Portal](https://ms.portal.azure.com)로 이동하여 **리소스 만들기** 를 선택합니다.

**Marketplace 검색** 표시줄에 *IoT Central* 을 입력합니다.

![관리 포털: 검색](media/howto-manage-iot-central-from-portal/image0a1.png)

검색 결과에서 **IoT Central 애플리케이션** 타일을 선택합니다.

![관리 포털: 검색 결과](media/howto-manage-iot-central-from-portal/image0b1.png)

이제 **만들기** 를 선택합니다.

![관리 포털: IoT Central 리소스](media/howto-manage-iot-central-from-portal/image0c1.png)

양식에 있는 모든 필드를 작성합니다. 이 양식은 [Azure IoT Central 애플리케이션 관리자](https://aka.ms/iotcentral) 웹 사이트에서 애플리케이션을 만들기 위해 작성하는 양식과 유사합니다. 자세한 내용은 [IoT Central 애플리케이션 만들기](quick-deploy-iot-central.md) 빠른 시작을 참조하세요.

![IoT Central 양식 만들기](media/howto-manage-iot-central-from-portal/image6a.png)

**위치** 는 애플리케이션을 만들려는 [지리](https://azure.microsoft.com/global-infrastructure/geographies/)입니다. 일반적으로 최적의 성능을 얻으려면 디바이스와 물리적으로 가장 가까운 위치를 선택해야 합니다. Azure IoT Central은 현재 **오스트레일리아**, **아시아 태평양**, **유럽**, **미국**, **영국** 및 **일본** 지역에서 사용할 수 있습니다. 위치를 선택하면 나중에 다른 위치로 애플리케이션을 이동할 수 없습니다.

모든 필드를 채운 후 **만들기** 를 선택합니다.

## <a name="manage-existing-iot-central-applications"></a>기존 IoT Central 애플리케이션 관리

Azure IoT Central 애플리케이션이 이미 있으면 삭제하거나 Azure Portal의 다른 구독 또는 리소스 그룹으로 이동할 수 있습니다.

> [!NOTE]
> *무료* 요금제를 사용하여 만든 애플리케이션에는 Azure 구독이 필요하지 않으므로 Azure Portal의 Azure 구독에 나열되지 않습니다. IoT Central 포털에서만 무료 앱을 보고 관리할 수 있습니다.

시작하려면 포털의 **모든 리소스** 를 선택합니다. **숨겨진 형식 표시** 를 선택하고 **이름으로 필터링** 에 애플리케이션 이름을 입력하여 찾습니다. 그런 다음, 관리할 IoT Central 애플리케이션을 선택합니다.

애플리케이션으로 이동하려면 **IoT Central 애플리케이션 URL** 을 선택합니다.

!["IoT Central 애플리케이션 URL"이 강조 표시된 "개요" 페이지를 보여주는 스크린샷.](media/howto-manage-iot-central-from-portal/image3.png)

다른 리소스 그룹으로 애플리케이션을 이동하려면 리소스 그룹 옆에 있는 **변경** 을 선택합니다. **리소스 이동** 페이지에서 이 애플리케이션을 이동할 리소스 그룹을 선택합니다.

!["리소스 그룹(변경)"이 강조 표시된 "개요" 페이지를 보여주는 스크린샷.](media/howto-manage-iot-central-from-portal/image4a.png)

다른 구독으로 애플리케이션을 이동하려면 구독 옆에 있는 **변경** 을 선택합니다. **리소스 이동** 페이지에서 이 애플리케이션을 이동할 구독을 선택합니다.

![관리 포털: 리소스 관리](media/howto-manage-iot-central-from-portal/image5a.png)

## <a name="next-steps"></a>다음 단계

Azure Portal에서 Azure IoT Central 애플리케이션을 관리하는 방법을 알아보았으므로 다음 단계를 진행하는 것이 좋습니다.

> [!div class="nextstepaction"]
> [애플리케이션 관리](howto-administer.md)