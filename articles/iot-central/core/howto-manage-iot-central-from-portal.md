---
title: Azure Portal에서 IoT Central 관리 | Microsoft Docs
description: This article describes how to create and manage your IoT Central applications from the Azure portal.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 10/02/2019
ms.topic: conceptual
manager: philmea
ms.openlocfilehash: 2133819ce7c298e2f73fdc5a68b80b64f9e72ea7
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/25/2019
ms.locfileid: "74480423"
---
# <a name="manage-iot-central-from-the-azure-portal"></a>Azure Portal에서 IoT Central 관리

[!INCLUDE [iot-central-selector-manage](../../../includes/iot-central-selector-manage.md)]

Instead of creating and managing IoT Central applications on the [Azure IoT Central application manager](https://aka.ms/iotcentral) website, you can use the [Azure portal](https://portal.azure.com) to manage your applications.

## <a name="create-iot-central-applications"></a>IoT Central 애플리케이션 만들기

To create an application, navigate to the [Azure portal](https://ms.portal.azure.com) and select **Create a resource** in the main pane on the left.

![관리 포털: 탐색 메뉴](media/howto-manage-iot-central-from-portal/image0.png)

검색 창에 **IoT Central**을 입력합니다.

![관리 포털: 검색](media/howto-manage-iot-central-from-portal/image0a1.png)

Select the **IoT Central Application** line-item in the search results.

![관리 포털: 검색 결과](media/howto-manage-iot-central-from-portal/image0b1.png)

이제 **만들기**를 선택합니다.

![관리 포털: IoT Central 리소스](media/howto-manage-iot-central-from-portal/image0c1.png)

양식에 있는 모든 필드를 작성합니다. This form is similar to the form you fill out to create applications on the [Azure IoT Central application manager](https://aka.ms/iotcentral) website. 자세한 내용은 [IoT Central 애플리케이션 만들기](quick-deploy-iot-central.md) 빠른 시작을 참조하세요.

You can create IoT Central application with general features by selecting **Sample Contoso**, **Custom application** and **Sample Devkits** as application templates, all the other application templates uses public preview features.

![create IoT Central form](media/howto-manage-iot-central-from-portal/image6a.png)

**Location** is the [geography](https://azure.microsoft.com/global-infrastructure/geographies/) where you’d like to create your application. Typically, you should choose the location that's physically closest to your devices to get optimal performance. Azure IoT Central is currently available in the **United States**, **Australia**, **Asia Pacific**, or in **Europe**.  Once you choose a location, you can't move your application to a different location later.

> [!NOTE]
> The preview application templates are currently only available in the **Europe** and **United States** locations.

![관리 포털: IoT Central 리소스 만들기](media/howto-manage-iot-central-from-portal/image1a.png)  

After filling out all fields, select **Create**.

## <a name="manage-existing-iot-central-applications"></a>기존 IoT Central 애플리케이션 관리

Azure IoT Central 애플리케이션이 이미 있으면 삭제하거나 Azure Portal의 다른 구독 또는 리소스 그룹으로 이동할 수 있습니다.

> [!NOTE]
> 평가판 애플리케이션은 구독과 연결되어 있지 않으므로 Azure Portal에 표시되지 않습니다.

To get started, select **All resources** in the main pane on the left. 검색 상자에 애플리케이션 이름을 입력하여 리소스 목록에서 애플리케이션을 찾습니다. Then select the IoT Central application you'd like to manage.

![관리 포털: 리소스 관리](media/howto-manage-iot-central-from-portal/image2a.png)

To navigate to the application, select the IoT Central Application URL.

![관리 포털: 리소스 관리](media/howto-manage-iot-central-from-portal/image3.png)

To move the application to a different resource group, select **change** beside the resource group. **리소스 이동** 페이지에서 이 애플리케이션을 마이그레이션하려는 리소스 그룹을 선택합니다.

![관리 포털: 리소스 관리](media/howto-manage-iot-central-from-portal/image4a.png)

To move the application to a different subscription, select the **change** link beside the subscription. 표시되는 대화 상자에서 이 애플리케이션을 마이그레이션할 구독을 선택합니다.

![관리 포털: 리소스 관리](media/howto-manage-iot-central-from-portal/image5a.png)

## <a name="next-steps"></a>다음 단계

Azure Portal에서 Azure IoT Central 애플리케이션을 관리하는 방법을 알아보았으므로 다음 단계를 진행하는 것이 좋습니다.

> [!div class="nextstepaction"]
> [애플리케이션 관리](howto-administer.md)