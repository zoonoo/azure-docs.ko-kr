---
title: Azure IoT Central 개인 대시보드 만들기 | Microsoft Docs
description: 사용자는 개인 대시보드를 만들고 관리 하는 방법에 대해 알아봅니다.
author: mavoge
ms.author: mavoge
ms.date: 10/17/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 06225e284000d7f10f575be08cd683488abec339
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80985493"
---
# <a name="create-and-manage-multiple-dashboards"></a>여러 대시보드 만들기 및 관리

**대시보드** 는 응용 프로그램을 처음 탐색할 때 로드 되는 페이지입니다. 응용 프로그램의 **작성기** 는 모든 사용자에 대 한 기본 응용 프로그램 대시보드를 정의 합니다. 사용자 고유의 개인 설정 된 응용 프로그램 대시보드를 추가로 만들 수 있습니다. 여러 대시보드를 만들어 서로 다른 데이터를 표시 하 고 서로 전환할 수 있습니다.

응용 프로그램의 **관리자** 인 경우 응용 프로그램의 다른 사용자와 공유할 응용 프로그램 수준 대시보드를 10 개까지 만들 수도 있습니다. **관리자** 만 응용 프로그램 수준 대시보드를 만들고 편집 하 고 삭제할 수 있습니다. 

## <a name="create-dashboard"></a>대시보드 만들기

다음 스크린샷은 **사용자 지정 응용 프로그램** 템플릿에서 만든 응용 프로그램의 대시보드를 보여 줍니다. 기본 응용 프로그램 대시보드를 개인 대시보드로 바꾸거나, 관리자 인 경우 다른 응용 프로그램 수준 대시보드를 사용할 수 있습니다. 이렇게 하려면 페이지의 왼쪽 위에서 **+ 새로 만들기** 를 선택 합니다.
 
> [!div class="mx-imgBorder"]
> !["사용자 지정 응용 프로그램" 템플릿을 기반으로 하는 응용 프로그램 대시보드](media/howto-create-personal-dashboards/dashboard-custom-app.png)

**+ 새로 만들기** 를 선택 하면 대시보드 편집기가 열립니다. 편집기에서 대시보드에 이름을 지정 하 고 라이브러리에서 항목을 선택할 수 있습니다. 라이브러리에는 대시보드를 사용자 지정 하는 데 사용할 수 있는 타일 및 대시보드 기본 형식이 포함 되어 있습니다.

> [!div class="mx-imgBorder"]
> ![대시보드 라이브러리](media/howto-create-personal-dashboards/dashboard-library.png)

응용 프로그램의 **관리자** 인 경우 개인 수준 대시보드 또는 응용 프로그램 수준 대시보드를 만들려는 경우 설정/해제 하는 옵션이 제공 됩니다. 개인 수준 대시보드를 만드는 경우에는 사용자만 볼 수 있습니다. 응용 프로그램 수준 대시보드를 만들면 응용 프로그램의 모든 사용자가 응용 프로그램 수준 대시보드를 볼 수 있습니다. 제목을 입력 하 고 만들려는 대시보드 유형을 선택한 후 나중에 타일을 저장 하 고 추가할 수 있습니다. 또는 지금 준비가 되어 있고 장치 템플릿 및 장치 인스턴스를 추가한 경우 계속 해 서 첫 번째 타일을 만들 수 있습니다. 

> [!div class="mx-imgBorder"]
> ![온도에 대 한 세부 정보가 포함 된 장치 세부 정보 구성](media/howto-create-personal-dashboards/device-details.png)

예를 들어 장치의 현재 온도에 대 한 **원격 분석** 타일을 추가할 수 있습니다. 확인 방법은 다음과 같습니다.
1. **장치 템플릿** 선택
1. 대시보드 타일에 표시 하려는 장치에 대 한 **장치 인스턴스** 를 선택 합니다. 그러면 타일에서 사용할 수 있는 장치 속성의 목록이 표시 됩니다.
1. 대시보드에 타일을 만들려면 **온도** 를 클릭 하 고 대시보드 영역으로 끕니다. **온도** 옆의 확인란을 클릭 하 고 **결합**을 클릭 해도 됩니다. 다음 스크린샷에서는 장치 템플릿 및 장치 인스턴스를 선택 하 고 대시보드에서 온도 원격 분석 타일을 만드는 방법을 보여 줍니다.
1. 왼쪽 상단에서 **저장** 을 선택 하 여 타일을 대시보드에 저장 합니다.

> [!div class="mx-imgBorder"]
> ![온도 타일에 대 한 세부 정보가 포함 된 대시보드 "탭](media/howto-create-personal-dashboards/temperature-tile-edit.png)

이제 개인 대시보드를 볼 때 장치의 **온도** 설정이 포함 된 새 타일이 표시 됩니다.

> [!div class="mx-imgBorder"]
> ![온도 타일에 대 한 세부 정보가 포함 된 대시보드 "탭](media/howto-create-personal-dashboards/temperature-tile-complete.png)

라이브러리의 다른 타일 유형을 탐색 하 여 개인 대시보드를 추가로 사용자 지정 하는 방법을 찾을 수 있습니다.

Azure IoT Central에서 타일을 사용 하는 방법에 대해 자세히 알아보려면 [대시보드에 타일 추가](howto-add-tiles-to-your-dashboard.md)를 참조 하세요.

## <a name="manage-dashboards"></a>대시보드 관리

여러 개인 대시보드를 포함 하 고 서로 전환 하거나 기본 응용 프로그램 대시보드 중 하나를 선택할 수 있습니다.

> [!div class="mx-imgBorder"]
> ![대시보드 사이 전환](media/howto-create-personal-dashboards/switch-dashboards.png)

개인 대시보드를 편집 하 고 더 이상 필요 하지 않은 대시보드를 삭제할 수 있습니다. **관리자**인 경우 응용 프로그램 수준 대시보드도 편집 하거나 삭제할 수 있습니다.

> [!div class="mx-imgBorder"]
> ![대시보드 삭제](media/howto-create-personal-dashboards/delete-dashboards.png)

## <a name="next-steps"></a>다음 단계

개인 대시보드를 만들고 관리 하는 방법을 배웠으므로 이제 [응용 프로그램 기본 설정을 관리 하는 방법을 배울](howto-manage-preferences.md) 수 있습니다.
