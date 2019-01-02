---
title: Azure Portal에서 IoT Central 관리 | Microsoft Docs
description: Azure Portal에서 IoT Central을 관리합니다.
services: iot-central
ms.service: iot-central
author: tbhagwat3
ms.author: tanmayb
ms.date: 08/30/2018
ms.topic: conceptual
manager: peterpr
ms.openlocfilehash: 20b1c7500587324f6f7dbb5cc679a3603eff56bd
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/06/2018
ms.locfileid: "52963830"
---
# <a name="manage-iot-central-from-the-azure-portal"></a>Azure Portal에서 IoT Central 관리 
IoT Central 웹 사이트에서 IoT Central 애플리케이션을 만들고 관리할 수 있을 뿐만 아니라 Azure Portal에서도 IoT Central을 관리할 수 있습니다. 이 문서에서는 가능한 작업과 그 수행 방법을 설명합니다.

## <a name="create-iot-central-applications"></a>IoT Central 애플리케이션 만들기
새 애플리케이션을 만들려면 [Azure Portal](https://ms.portal.azure.com)로 이동하여 왼쪽의 주 탐색 메뉴에서 “리소스 만들기”를 클릭합니다. 

![관리 포털: 탐색 메뉴](media/howto-manage-iot-central-from-portal/image0.png)

검색 창에 “IoT Central”이라는 용어를 입력합니다.

![관리 포털: 검색](media/howto-manage-iot-central-from-portal/image0a.png)

검색 결과에서 IoT Central 애플리케이션 항목을 클릭합니다.

![관리 포털: 검색 결과](media/howto-manage-iot-central-from-portal/image0b.png)

이제 “만들기” 단추를 클릭하여 작성해야 하는 양식을 표시합니다.

![관리 포털: IoT Central 리소스](media/howto-manage-iot-central-from-portal/image0c.png)

양식에 있는 모든 필드를 작성합니다. 이 양식은 IoT Central 웹 사이트에서 애플리케이션을 만들기 위해 채워야 하는 양식과 비슷합니다. 각 필드를 작성하는 방법을 자세히 알아보려면 [IoT Central 애플리케이션 만들기](quick-deploy-iot-central.md) 빠른 시작을 확인하세요. 

![관리 포털: IoT Central 리소스 만들기](media/howto-manage-iot-central-from-portal/image1.png)  

모든 필드를 작성한 후 “만들기” 단추를 클릭합니다.

## <a name="manage-existing-iot-central-applications"></a>기존 IoT Central 애플리케이션 관리
Azure IoT Central 애플리케이션이 이미 있는 경우 해당 애플리케이션을 삭제하거나 Azure Portal에 있는 다른 구독 또는 리소스 그룹으로 이동할 수 있습니다. 평가판 애플리케이션을 지원하는 구독이 없으므로 Azure Portal에서 이러한 평가판 애플리케이션이 표시되지 않습니다.

시작하려면 왼쪽의 주 탐색 메뉴에서 “모든 리소스”를 클릭하세요. 검색 상자를 사용하여 애플리케이션 이름을 입력하고 리소스 목록에서 찾습니다. 그런 다음, 관리하려는 IoT Central 애플리케이션을 클릭합니다.

![관리 포털: 리소스 관리](media/howto-manage-iot-central-from-portal/image2.png)

애플리케이션으로 이동하려면 [IoT Central Application URL]\(IoT Central 애플리케이션 URL)을 클릭합니다.

![관리 포털: 리소스 관리](media/howto-manage-iot-central-from-portal/image3.png)

애플리케이션을 다른 리소스 그룹으로 이동하려면 [리소스 그룹] 옆에 있는 **변경** 링크를 클릭합니다. 표시되는 대화 상자에서 이 애플리케이션을 마이그레이션할 리소스 그룹을 선택합니다.

![관리 포털: 리소스 관리](media/howto-manage-iot-central-from-portal/image4.png)

애플리케이션을 다른 구독으로 이동하려면 [구독] 옆에 있는 **변경** 링크를 클릭합니다. 표시되는 대화 상자에서 이 애플리케이션을 마이그레이션할 구독을 선택합니다.

![관리 포털: 리소스 관리](media/howto-manage-iot-central-from-portal/image5.png)

## <a name="next-steps"></a>다음 단계

이제 Azure Portal에서 Azure IoT Central 애플리케이션을 관리하는 방법을 모두 알아보았습니다. 다음은 추천하는 단계입니다.

> [!div class="nextstepaction"]
> [응용 프로그램 관리](howto-administer.md)