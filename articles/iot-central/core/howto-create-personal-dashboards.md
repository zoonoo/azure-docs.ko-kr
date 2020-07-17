---
title: Azure IoT Central 개인 대시보드 만들기 | Microsoft Docs
description: 사용자로 개인 대시보드를 만들고 관리하는 방법에 대해 알아봅니다.
author: mavoge
ms.author: mavoge
ms.date: 10/17/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: f7dca7352a49e668231f64632371a445985e4de3
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.contentlocale: ko-KR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83634602"
---
# <a name="create-and-manage-multiple-dashboards"></a>여러 대시보드 만들기 및 관리

**대시보드**는 애플리케이션을 처음 탐색할 때 로드되는 페이지입니다. 애플리케이션의 **작성기**는 모든 사용자에 대한 기본 애플리케이션 대시보드를 정의합니다. 사용자 고유의 개인 설정된 애플리케이션 대시보드를 추가로 만들 수 있습니다. 여러 대시보드를 만들어 서로 다른 데이터를 표시하고 서로 전환할 수 있습니다.

애플리케이션의 **관리자**인 경우 애플리케이션의 다른 사용자와 공유할 수 있는 애플리케이션 수준 대시보드를 10개까지 만들 수도 있습니다. **관리자**만 애플리케이션 수준 대시보드를 만들고 편집하고 삭제할 수 있습니다.  

## <a name="create-dashboard"></a>대시보드 만들기

다음 스크린샷은 **사용자 지정 애플리케이션** 템플릿에서 만든 애플리케이션의 대시보드를 보여 줍니다. 기본 애플리케이션 대시보드를 개인 대시보드로 바꾸거나, 관리자인 경우 다른 애플리케이션 수준 대시보드로 바꿀 수 있습니다. 이렇게 하려면 페이지의 왼쪽 위에서 **+ 새로 만들기**를 선택합니다.

> [!div class="mx-imgBorder"]
> !["사용자 지정 애플리케이션" 템플릿을 기반으로 하는 애플리케이션용 대시보드](media/howto-create-personal-dashboards/dashboard-custom-app.png)

**+ 새로 만들기**를 선택하면 대시보드 편집기가 열립니다. 편집기에서 대시보드에 이름을 지정하고 라이브러리에서 항목을 선택할 수 있습니다. 라이브러리에는 대시보드를 사용자 지정하는 데 사용할 수 있는 타일 및 대시보드 기본 형식이 포함되어 있습니다.

> [!div class="mx-imgBorder"]
> ![대시보드 라이브러리](media/howto-create-personal-dashboards/dashboard-library.png)

애플리케이션의 **관리자**인 경우 개인 수준 대시보드 또는 애플리케이션 수준 대시보드를 만드는 옵션이 제공됩니다. 개인 수준 대시보드를 만드는 경우에는 사용자만 볼 수 있습니다. 애플리케이션 수준 대시보드를 만드는 경우 애플리케이션의 모든 사용자가 애플리케이션 수준 대시보드를 볼 수 있습니다. 제목을 입력하고 만들려는 대시보드 유형을 선택한 후 나중에 타일을 저장하고 추가할 수 있습니다. 또는 지금 준비가 되어 있고 디바이스 템플릿 및 디바이스 인스턴스를 추가한 경우 계속해서 첫 번째 타일을 만들 수 있습니다.  

> [!div class="mx-imgBorder"]
> ![온도에 대한 세부 정보가 있는 디바이스 세부 정보 구성" 양식](media/howto-create-personal-dashboards/device-details.png)

예를 들어 디바이스의 현재 온도에 대한 **원격 분석** 타일을 추가할 수 있습니다. 이렇게 하려면 다음을 수행합니다.

1. **디바이스 템플릿** 선택
1. 대시보드 타일에서 보려는 디바이스에 대해 **디바이스**에서 디바이스를 선택합니다. 그러면 타일에서 사용할 수 있는 디바이스 속성의 목록이 표시됩니다.
1. 대시보드에 타일을 만들려면 **온도**를 클릭하고 대시보드 영역으로 끕니다. 또한 **온도** 옆의 확인란을 클릭하고 **타일 추가**를 클릭할 수도 있습니다. 다음 스크린샷에서는 디바이스 템플릿 및 디바이스를 선택한 다음, 대시보드에서 온도 원격 분석 타일을 만드는 방법을 보여 줍니다.
1. 왼쪽 위에 있는 **저장**을 선택하여 변경 내용을 대시보드에 저장합니다.

> [!div class="mx-imgBorder"]
> ![온도 타일에 대한 세부 정보가 포함된 대시보드" 탭](media/howto-create-personal-dashboards/temperature-tile-edit.png)

이제 개인 대시보드를 볼 때 디바이스에 대한 **온도** 설정을 사용하여 새 타일이 표시됩니다.

> [!div class="mx-imgBorder"]
> ![온도 타일에 대한 세부 정보가 포함된 대시보드" 탭](media/howto-create-personal-dashboards/temperature-tile-complete.png)

라이브러리의 다른 타일 유형을 탐색하여 개인 대시보드를 추가로 사용자 지정하는 방법을 찾을 수 있습니다.

Azure IoT Central에서 타일을 사용하는 방법에 대해 자세히 알아보려면 [대시보드에 타일 추가](howto-add-tiles-to-your-dashboard.md)를 참조하세요.

## <a name="manage-dashboards"></a>대시보드 관리

여러 개인 대시보드를 사용하고 서로 전환하거나 기본 애플리케이션 대시보드 중 하나를 선택할 수 있습니다.

> [!div class="mx-imgBorder"]
> ![대시보드 간 전환](media/howto-create-personal-dashboards/switch-dashboards.png)

개인 대시보드를 편집하고 더 이상 필요하지 않은 대시보드를 삭제할 수 있습니다. **관리자**인 경우 애플리케이션 수준 대시보드도 편집하거나 삭제할 수 있습니다.

> [!div class="mx-imgBorder"]
> ![대시보드 삭제](media/howto-create-personal-dashboards/delete-dashboards.png)

## <a name="next-steps"></a>다음 단계

이제 개인 대시보드를 만들고 관리하는 방법을 배웠으므로 [애플리케이션 기본 설정을 관리하는 방법을 알아볼](howto-manage-preferences.md) 수 있습니다.
