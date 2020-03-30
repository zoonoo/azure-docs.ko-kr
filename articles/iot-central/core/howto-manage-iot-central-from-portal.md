---
title: Azure Portal에서 IoT Central 관리 | Microsoft Docs
description: 이 문서에서는 Azure 포털에서 IoT Central 응용 프로그램을 만들고 관리하는 방법을 설명합니다.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 02/11/2020
ms.topic: how-to
manager: philmea
ms.openlocfilehash: e7efda8efa27044168386e3ebbc557bf7fb74e8c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80157928"
---
# <a name="manage-iot-central-from-the-azure-portal"></a>Azure Portal에서 IoT Central 관리

[!INCLUDE [iot-central-selector-manage](../../../includes/iot-central-selector-manage.md)]

Azure IoT Central 응용 프로그램 [관리자](https://aka.ms/iotcentral) 웹 사이트에서 IoT Central 응용 프로그램을 만들고 관리하는 대신 [Azure Portal을](https://portal.azure.com) 사용하여 응용 프로그램을 관리할 수 있습니다.

## <a name="create-iot-central-applications"></a>IoT Central 애플리케이션 만들기

응용 프로그램을 만들려면 [Azure 포털로](https://ms.portal.azure.com) 이동하여 **리소스 만들기를**선택합니다.

**마켓플레이스** 막대 검색에서 *IoT Central을*입력합니다.

![관리 포털: 검색](media/howto-manage-iot-central-from-portal/image0a1.png)

검색 결과에서 **IoT 중앙 응용 프로그램** 타일을 선택합니다.

![관리 포털: 검색 결과](media/howto-manage-iot-central-from-portal/image0b1.png)

이제 **: 만들기를 선택합니다.**

![관리 포털: IoT Central 리소스](media/howto-manage-iot-central-from-portal/image0c1.png)

양식에 있는 모든 필드를 작성합니다. 이 양식은 [Azure IoT Central 응용 프로그램 관리자](https://aka.ms/iotcentral) 웹 사이트에서 응용 프로그램을 만들기 위해 작성한 양식과 유사합니다. 자세한 내용은 [IoT Central 애플리케이션 만들기](quick-deploy-iot-central.md) 빠른 시작을 참조하세요.

![IoT 중앙 양식 만들기](media/howto-manage-iot-central-from-portal/image6a.png)

**위치**는 애플리케이션을 만들려는 [지리](https://azure.microsoft.com/global-infrastructure/geographies/)입니다. 일반적으로 최적의 성능을 얻으려면 디바이스와 물리적으로 가장 가까운 위치를 선택해야 합니다. Azure IoT Central은 현재 **오스트레일리아,** **아시아 태평양,** **유럽,** **미국,** **영국**및 **일본** 지역에서 사용할 수 있습니다. 위치를 선택하면 나중에 다른 위치로 애플리케이션을 이동할 수 없습니다.

모든 필드를 입력한 후 **에서 만들기를**선택합니다.

## <a name="manage-existing-iot-central-applications"></a>기존 IoT Central 애플리케이션 관리

Azure IoT Central 애플리케이션이 이미 있으면 삭제하거나 Azure Portal의 다른 구독 또는 리소스 그룹으로 이동할 수 있습니다.

> [!NOTE]
> Azure 포털에서 구독과 연결되지 않았기 때문에 무료 가격 책정 요금제에서 만든 응용 프로그램을 볼 수 없습니다.

시작하려면 포털의 **모든 리소스를** 선택합니다. **숨겨진 형식 표시를** 선택하고 **이름으로 필터에** 응용 프로그램의 이름을 입력하여 찾아보십시오. 그런 다음 관리하려는 IoT 중앙 응용 프로그램을 선택합니다.

응용 프로그램으로 이동하려면 **IoT 중앙 응용 프로그램 URL을**선택합니다.

![관리 포털: 리소스 관리](media/howto-manage-iot-central-from-portal/image3.png)

응용 프로그램을 다른 리소스 그룹으로 이동하려면 리소스 그룹 옆에 **있는 변경을** 선택합니다. 리소스 **이동** 페이지에서 이 응용 프로그램을 다음으로 이동할 리소스 그룹을 선택합니다.

![관리 포털: 리소스 관리](media/howto-manage-iot-central-from-portal/image4a.png)

응용 프로그램을 다른 구독으로 이동하려면 구독 옆에 **있는 변경을** 선택합니다. 리소스 **이동** 페이지에서 이 응용 프로그램을 다음으로 이동할 구독을 선택합니다.

![관리 포털: 리소스 관리](media/howto-manage-iot-central-from-portal/image5a.png)

## <a name="next-steps"></a>다음 단계

Azure Portal에서 Azure IoT Central 애플리케이션을 관리하는 방법을 알아보았으므로 다음 단계를 진행하는 것이 좋습니다.

> [!div class="nextstepaction"]
> [애플리케이션 관리](howto-administer.md)