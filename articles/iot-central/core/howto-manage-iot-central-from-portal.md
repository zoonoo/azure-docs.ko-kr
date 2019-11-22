---
title: Azure Portal에서 IoT Central 관리 | Microsoft Docs
description: 이 문서에서는 Azure Portal에서 IoT Central 응용 프로그램을 만들고 관리 하는 방법을 설명 합니다.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 10/02/2019
ms.topic: conceptual
manager: philmea
ms.openlocfilehash: c9a8ed6d55fbbfcf59c98a86ac98cb4264d31ee4
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/21/2019
ms.locfileid: "74278811"
---
# <a name="manage-iot-central-from-the-azure-portal"></a>Azure Portal에서 IoT Central 관리

[!INCLUDE [iot-central-selector-manage](../../../includes/iot-central-selector-manage.md)]

[Azure IoT Central application manager](https://aka.ms/iotcentral) 웹 사이트에서 IoT Central 응용 프로그램을 만들고 관리 하는 대신 [Azure Portal](https://portal.azure.com) 를 사용 하 여 응용 프로그램을 관리할 수 있습니다.

## <a name="create-iot-central-applications"></a>IoT Central 애플리케이션 만들기

응용 프로그램을 만들려면 [Azure Portal](https://ms.portal.azure.com) 로 이동 하 고 왼쪽의 주 창에서 **리소스 만들기** 를 선택 합니다.

![관리 포털: 탐색 메뉴](media/howto-manage-iot-central-from-portal/image0.png)

검색 창에 **IoT Central**을 입력합니다.

![관리 포털: 검색](media/howto-manage-iot-central-from-portal/image0a1.png)

검색 결과에서 **IoT Central 응용 프로그램** 줄 항목을 선택 합니다.

![관리 포털: 검색 결과](media/howto-manage-iot-central-from-portal/image0b1.png)

이제 **만들기**를 선택합니다.

![관리 포털: IoT Central 리소스](media/howto-manage-iot-central-from-portal/image0c1.png)

양식에 있는 모든 필드를 작성합니다. 이 양식은 [Azure IoT Central application manager](https://aka.ms/iotcentral) 웹 사이트에서 응용 프로그램을 만들기 위해 작성 하는 양식과 비슷합니다. 자세한 내용은 [IoT Central 애플리케이션 만들기](quick-deploy-iot-central.md) 빠른 시작을 참조하세요.

**Location** 은 응용 프로그램을 만들려는 [지리](https://azure.microsoft.com/global-infrastructure/geographies/) 입니다. 일반적으로 최적의 성능을 얻으려면 장치에 물리적으로 가장 가까운 위치를 선택 해야 합니다. Azure IoT Central는 현재 **미국**, **오스트레일리아**, **아시아 태평양**또는 **유럽**에서 사용할 수 있습니다.  위치를 선택 하면 나중에 응용 프로그램을 다른 위치로 이동할 수 없습니다.

> [!NOTE]
> **Preview 응용 프로그램** 템플릿은 현재 **유럽** 및 **미국** 지역 에서만 사용할 수 있습니다.

![관리 포털: IoT Central 리소스 만들기](media/howto-manage-iot-central-from-portal/image1a.png)  

모든 필드를 채운 후 **만들기**를 선택 합니다.

## <a name="manage-existing-iot-central-applications"></a>기존 IoT Central 애플리케이션 관리

Azure IoT Central 애플리케이션이 이미 있으면 삭제하거나 Azure Portal의 다른 구독 또는 리소스 그룹으로 이동할 수 있습니다.

> [!NOTE]
> 평가판 애플리케이션은 구독과 연결되어 있지 않으므로 Azure Portal에 표시되지 않습니다.

시작 하려면 왼쪽의 주 창에서 **모든 리소스** 를 선택 합니다. 검색 상자에 애플리케이션 이름을 입력하여 리소스 목록에서 애플리케이션을 찾습니다. 그런 다음 관리 하려는 IoT Central 응용 프로그램을 선택 합니다.

![관리 포털: 리소스 관리](media/howto-manage-iot-central-from-portal/image2a.png)

응용 프로그램으로 이동 하려면 IoT Central 응용 프로그램 URL을 선택 합니다.

![관리 포털: 리소스 관리](media/howto-manage-iot-central-from-portal/image3.png)

응용 프로그램을 다른 리소스 그룹으로 이동 하려면 리소스 그룹 옆에서 **변경** 을 선택 합니다. **리소스 이동** 페이지에서 이 애플리케이션을 마이그레이션하려는 리소스 그룹을 선택합니다.

![관리 포털: 리소스 관리](media/howto-manage-iot-central-from-portal/image4a.png)

응용 프로그램을 다른 구독으로 이동 하려면 구독 옆의 **변경** 링크를 선택 합니다. 표시되는 대화 상자에서 이 애플리케이션을 마이그레이션할 구독을 선택합니다.

![관리 포털: 리소스 관리](media/howto-manage-iot-central-from-portal/image5a.png)

## <a name="next-steps"></a>다음 단계

Azure Portal에서 Azure IoT Central 애플리케이션을 관리하는 방법을 알아보았으므로 다음 단계를 진행하는 것이 좋습니다.

> [!div class="nextstepaction"]
> [애플리케이션 관리](howto-administer.md)