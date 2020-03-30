---
title: Azure IoT 중앙 개인 대시보드 만들기 | 마이크로 소프트 문서
description: 개인 대시보드를 만들고 관리하는 방법을 알아봅니다.
author: mavoge
ms.author: mavoge
ms.date: 10/17/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: db886006ff5b9adf3de0932951f6cce4958e8ebd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80158132"
---
# <a name="create-and-manage-multiple-dashboards"></a>여러 대시보드 생성 및 관리

**대시보드는** 응용 프로그램으로 처음 이동할 때 로드되는 페이지입니다. 응용 프로그램의 **빌더는** 모든 사용자에 대한 기본 응용 프로그램 대시보드를 정의합니다. 이 기본 대시보드를 사용자 고유의 개인 화된 응용 프로그램 대시보드로 바꿀 수 있습니다. 서로 다른 데이터를 표시하고 서로 전환하는 여러 대시보드를 가질 수 있습니다. 

응용 프로그램의 **관리자인** 경우 최대 10개의 응용 프로그램 수준 대시보드를 만들어 응용 프로그램의 다른 사용자와 공유할 수도 있습니다. **관리자만** 응용 프로그램 수준 대시보드를 생성, 편집 및 삭제할 수 있습니다. 

## <a name="create-dashboard"></a>대시보드 만들기

다음 스크린샷은 **사용자 지정 응용** 프로그램 템플릿에서 만든 응용 프로그램의 대시보드를 보여 주며 있습니다. 기본 응용 프로그램 대시보드를 개인 대시보드로 바꾸거나 관리자인 경우 다른 응용 프로그램 수준 대시보드를 사용할 수 있습니다. 이렇게 하려면 페이지 왼쪽 상단에서 **+New를** 선택합니다.
 
> [!div class="mx-imgBorder"]
> !["사용자 지정 응용 프로그램" 템플릿을 기반으로 하는 응용 프로그램에 대 한 대시보드](media/howto-create-personal-dashboards/dashboard-custom-app.png)

**+ 새로** 을 선택하면 대시보드 편집기가 열립니다. 편집기에서 대시보드에 이름을 지정하고 라이브러리에서 항목을 선택할 수 있습니다. 라이브러리에는 대시보드를 사용자 지정하는 데 사용할 수 있는 타일 및 대시보드 기본 내용이 포함되어 있습니다.

> [!div class="mx-imgBorder"]
> ![대시보드 라이브러리](media/howto-create-personal-dashboards/dashboard-library.png)

응용 프로그램의 **관리자인** 경우 개인 수준 대시보드 또는 응용 프로그램 수준 대시보드를 만들려는 경우 전환할 수 있는 옵션이 제공됩니다. 개인 수준 대시보드를 만들면 본인만 볼 수 있습니다. 응용 프로그램 수준 대시보드를 만들면 응용 프로그램의 모든 사용자가 볼 수 있습니다. 제목을 입력하고 만들 려는 대시보드 유형을 선택한 후 나중에 타일을 저장하고 추가할 수 있습니다. 또는 지금 준비가 되어 있고 장치 템플릿 및 장치 인스턴스를 추가한 경우 첫 번째 타일을 만들 수 있습니다. 

> [!div class="mx-imgBorder"]
> ![온도에 대한 세부 정보가 있는 장치 세부 정보 구성" 양식](media/howto-create-personal-dashboards/device-details.png)

예를 들어 장치의 현재 온도에 대한 **원격 분석** 타일을 추가할 수 있습니다. 이렇게 하려면 다음을 수행합니다.
1. 장치 **템플릿** 선택
1. 대시보드 타일에 표시할 장치의 **장치 인스턴스를** 선택합니다. 그런 다음 타일에서 사용할 수 있는 장치의 속성 목록이 표시됩니다.
1. 대시보드에서 타일을 만들려면 **온도를** 클릭하고 대시보드 영역으로 드래그합니다. **온도** 옆의 확인란을 클릭하고 **[결합]을**클릭할 수도 있다. 다음 스크린샷은 장치 템플릿 및 장치 인스턴스를 선택한 다음 대시보드에서 온도 원격 분석 타일을 만드는 것을 보여 주었습니다.
1. 타일을 대시보드에 저장하려면 왼쪽 상단에 **저장을** 선택합니다.

> [!div class="mx-imgBorder"]
> ![온도 타일에 대한 세부 정보가 있는 대시보드" 탭](media/howto-create-personal-dashboards/temperature-tile-edit.png)

이제 개인 대시보드를 볼 때 장치의 **온도** 설정이 있는 새 타일이 표시됩니다.

> [!div class="mx-imgBorder"]
> ![온도 타일에 대한 세부 정보가 있는 대시보드" 탭](media/howto-create-personal-dashboards/temperature-tile-complete.png)

라이브러리의 다른 타일 유형을 탐색하여 개인 대시보드를 추가로 사용자 지정하는 방법을 알아볼 수 있습니다.

Azure IoT Central에서 타일을 사용하는 방법에 대한 자세한 내용은 대시보드에 타일 추가 를 [참조하세요.](howto-add-tiles-to-your-dashboard.md)

## <a name="manage-dashboards"></a>대시보드 관리

여러 개인 대시보드를 가지고 대시보드 간에 전환하거나 기본 응용 프로그램 대시보드 중 하나를 선택할 수 있습니다.

> [!div class="mx-imgBorder"]
> ![대시보드 사이 전환](media/howto-create-personal-dashboards/switch-dashboards.png)

개인 대시보드를 편집하고 더 이상 필요하지 않은 대시보드를 삭제할 수 있습니다. **관리자인**경우 응용 프로그램 수준 대시보드도 편집하거나 삭제할 수 있습니다.

> [!div class="mx-imgBorder"]
> ![대시보드 삭제](media/howto-create-personal-dashboards/delete-dashboards.png)

## <a name="next-steps"></a>다음 단계

개인 대시보드를 만들고 관리하는 방법을 배웠으니 응용 프로그램 기본 설정을 관리하는 방법을 배울 수 [있습니다.](howto-manage-preferences.md)
