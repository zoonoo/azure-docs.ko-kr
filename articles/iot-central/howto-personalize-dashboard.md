---
title: Azure IoT Central 개인 대시보드 만들기 | Microsoft Docs
description: 사용자를 만들고 개인 대시보드를 관리 하는 방법에 알아봅니다.
author: dominicbetts
ms.author: dobett
ms.date: 06/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: c048ae8c0daba0e467a9243f4dd83f8d95921e10
ms.sourcegitcommit: 837dfd2c84a810c75b009d5813ecb67237aaf6b8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2019
ms.locfileid: "67502638"
---
# <a name="create-and-manage-personal-dashboards"></a>개인 대시보드를 만들고 설정 합니다.

합니다 **대시보드** 먼저 응용 프로그램으로 이동 하는 경우 로드 하는 페이지입니다. A **작성기** 응용 프로그램에서 모든 사용자에 대 한 기본 응용 프로그램 대시보드를 정의 합니다. 이 기본 대시보드, 사용자 고유의 개인 설정 된 응용 프로그램 대시보드를 사용 하 여 바꿀 수 있습니다. 다른 데이터를 표시 하 고 전환할 대시보드가 여러 개 있을 수 있습니다.

## <a name="create-dashboard"></a>대시보드 만들기

다음 스크린샷은 대시보드에에서 만든 응용 프로그램에는 **샘플 Contoso** 템플릿. 개인 대시보드를 사용 하 여 기본 응용 프로그램 대시보드를 바꿀 수 있습니다. 이 위해 선택 **+ 새로 만들기** 맨 위에 있는 페이지의 오른쪽입니다.

!["Contoso 샘플" 템플릿을 기반으로 하는 응용 프로그램에 대 한 대시보드](media/howto-personalize-dashboard/defaultdashboard.png)

선택 **+ 새로 만들기**, 대시보드 편집기가 열립니다. 편집기에서 대시보드 이름을 지정 하 고 라이브러리에서 항목을 선택 합니다. 타일 및 대시보드를 사용자 지정 하 여 대시보드 기본 형식 라이브러리에 포함 되어 있습니다.

![대시보드 라이브러리](media/howto-personalize-dashboard/dashboardeditor.png)

예를 들어, 추가할 수 있습니다는 **장치 설정 및 속성** 를 관리 하는 장치 중 하나에 대 한 설정 및 속성 값을 표시 합니다. 이렇게 하려면 먼저 **디바이스 템플릿**을 선택한 다음, **디바이스 인스턴스**를 선택합니다. 그런 다음 선택한 제목 타일을 제공을 **설정은** 또는 **속성** 표시할 합니다. 다음 스크린 샷에 표시 된 **팬 속도** 타일에 추가 하기 위해 선택한 설정 합니다. 선택 **수행** 대시보드에 변경 내용을 저장 합니다.

![설정 및 속성에 대한 세부 정보가 있는 "디바이스 세부 정보 구성" 양식](media/howto-personalize-dashboard/dashboardsetting.png)

사용 하 여 새 타일이 표시 개인 대시보드를 볼 때 이제는 **팬 속도** 장치에 대 한 설정:

![타일에 대한 설정 및 속성이 표시된 “대시보드” 탭](media/howto-personalize-dashboard/personaldashboard.png)

개인 대시보드를 추가로 사용자 지정 하는 방법을 알아봅니다 라이브러리의 다른 타일 형식을 탐색할 수 있습니다.

Azure IoT Central의 타일을 사용 하는 방법에 대 한 자세한 내용은 참조 하세요 [대시보드 타일을 사용 하 여](howto-use-tiles.md)입니다.

## <a name="manage-dashboards"></a>대시보드 관리

여러 개인 대시보드를 할 수 있습니다 및 사이 전환 하거나 기본 응용 프로그램 대시보드를 선택 합니다.

![스위치 대시보드](media/howto-personalize-dashboard/switchdashboards.png)

개인 대시보드를 편집 하 고 더 이상 필요를 삭제할 수 없습니다.

![대시보드 삭제](media/howto-personalize-dashboard/managedashboards.png)

## <a name="next-steps"></a>다음 단계

이제 만들고 개인 대시보드를 관리 하는 방법을 배웠으므로 다음 작업을 수행할 수 있습니다.

> [!div class="nextstepaction"]
> [응용 프로그램 기본 설정을 관리 하는 방법 알아보기](howto-manage-preferences.md)
