---
title: Azure Portal에서 IoT Central 관리 | Microsoft Docs
description: Azure Portal에서 IoT Central을 관리합니다.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 01/14/2019
ms.topic: conceptual
manager: philmea
ms.openlocfilehash: 89109dec83342a8f4b5962778b1803eb36656e42
ms.sourcegitcommit: a1cf88246e230c1888b197fdb4514aec6f1a8de2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/16/2019
ms.locfileid: "54352218"
---
# <a name="manage-iot-central-from-the-azure-portal"></a>Azure Portal에서 IoT Central 관리

IoT Central [Application Manager](https://aka.ms/iotcentral) 페이지에서 IoT Central 애플리케이션을 만들고 관리할 수 있을 뿐 아니라 [Azure Portal](https://portal.azure.com)을 사용하여 애플리케이션을 관리할 수도 있습니다.

## <a name="create-iot-central-applications"></a>IoT Central 애플리케이션 만들기

애플리케이션을 만들려면 [Azure Portal](https://ms.portal.azure.com)로 이동하여 왼쪽의 주 탐색 메뉴에서 **리소스 만들기**를 클릭합니다.

![관리 포털: 탐색 메뉴](media/howto-manage-iot-central-from-portal/image0.png)

검색 창에 **IoT Central**을 입력합니다.

![관리 포털: 검색](media/howto-manage-iot-central-from-portal/image0a.png)

검색 결과에서 **IoT Central 애플리케이션** 항목을 클릭합니다.

![관리 포털: 검색 결과](media/howto-manage-iot-central-from-portal/image0b.png)

이제 **만들기** 단추를 클릭합니다.

![관리 포털: IoT Central 리소스](media/howto-manage-iot-central-from-portal/image0c.png)

양식에 있는 모든 필드를 작성합니다. 이 양식은 IoT Central [Application Manager](https://aka.ms/iotcentral) 페이지에서 애플리케이션을 만들려면 작성해야 하는 양식과 비슷합니다. 자세한 내용은 [IoT Central 애플리케이션 만들기](quick-deploy-iot-central.md) 빠른 시작을 참조하세요.

![관리 포털: IoT Central 리소스 만들기](media/howto-manage-iot-central-from-portal/image1.png)  

모든 필드에 내용을 입력한 후에 **만들기** 단추를 클릭합니다.

## <a name="manage-existing-iot-central-applications"></a>기존 IoT Central 애플리케이션 관리

Azure IoT Central 애플리케이션이 이미 있으면 삭제하거나 Azure Portal의 다른 구독 또는 리소스 그룹으로 이동할 수 있습니다.

> [!NOTE]
> 평가판 애플리케이션은 구독과 연결되어 있지 않으므로 Azure Portal에 표시되지 않습니다.

관리를 시작하려면 왼쪽의 주 탐색 메뉴에서 **모든 리소스**를 클릭합니다. 검색 상자에 애플리케이션 이름을 입력하여 리소스 목록에서 애플리케이션을 찾습니다. 그런 다음 관리할 IoT Central 애플리케이션을 클릭합니다.

![관리 포털: 리소스 관리](media/howto-manage-iot-central-from-portal/image2.png)

애플리케이션으로 이동하려면 [IoT Central Application URL]\(IoT Central 애플리케이션 URL)을 클릭합니다.

![관리 포털: 리소스 관리](media/howto-manage-iot-central-from-portal/image3.png)

다른 리소스 그룹으로 애플리케이션을 이동하려면 리소스 그룹 옆의 **변경**을 클릭합니다. **리소스 이동** 페이지에서 이 애플리케이션을 마이그레이션하려는 리소스 그룹을 선택합니다.

![관리 포털: 리소스 관리](media/howto-manage-iot-central-from-portal/image4.png)

다른 구독으로 애플리케이션을 이동하려면 구독 옆의 **변경** 링크를 클릭합니다. 표시되는 대화 상자에서 이 애플리케이션을 마이그레이션할 구독을 선택합니다.

![관리 포털: 리소스 관리](media/howto-manage-iot-central-from-portal/image5.png)

## <a name="next-steps"></a>다음 단계

Azure Portal에서 Azure IoT Central 애플리케이션을 관리하는 방법을 알아보았으므로 다음 단계를 진행하는 것이 좋습니다.

> [!div class="nextstepaction"]
> [애플리케이션 관리](howto-administer.md)