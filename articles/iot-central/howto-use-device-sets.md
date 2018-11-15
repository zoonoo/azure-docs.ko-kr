---
title: Azure IoT Central 응용 프로그램에서 장치 집합 사용 | Microsoft Docs
description: 운영자로서 Azure IoT Central 응용 프로그램에서 장치 집합을 사용하는 방법을 알아봅니다.
author: ellenfosborne
ms.author: elfarber
ms.date: 01/21/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpfr
ms.openlocfilehash: a5dd018197ff78ff0563349fd941308c3684a456
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2018
ms.locfileid: "51004122"
---
# <a name="use-device-sets-in-your-azure-iot-central-application"></a>Azure IoT Central 응용 프로그램에 장치 집합 사용

이 문서에서는 운영자로서 Azure IoT Central 애플리케이션에서 디바이스 집합을 사용하는 방법을 설명합니다.

장치 집합은 지정된 특정 기준이 모두 일치하기 때문에 함께 그룹화된 장치 목록입니다. 장치 집합은 장치를 더 작은 논리 그룹으로 그룹화하여 장치를 대규모로 관리하고 시각화하고 분석하는 데 도움을 줍니다. 예를 들어 기술자가 자신의 책임 하에 있는 모든 장치디바이스를 찾을 수 있도록 시애틀의 모든 공조 디바이스 목록을 만들 수 있습니다. 이 문서에서는 장치 집합을 만들고 구성하는 방법을 보여 줍니다.

## <a name="create-a-device-set"></a>장치 집합 만들기

장치 집합을 만들려면:

1. 왼쪽 탐색 메뉴에서 **장치 집합**을 선택합니다.

1. **+새로 만들기**를 클릭합니다.

    ![새 장치 집합](media/howto-use-device-sets/image1.png)

1. 응용 프로그램 전체에서 고유한 장치 집합 이름을 지정합니다. 설명을 추가할 수도 있습니다. 한 장치 집합에는 단일 장치 템플릿의 장치만 포함될 수 있습니다. 이 장치 집합에 사용할 장치 템플릿을 선택합니다.

1. 속성, 비교 연산자 및 값을 선택하여 장치 집합의 장치를 식별하는 쿼리를 만듭니다. 여러 쿼리를 추가할 수 있으며 **모든** 조건을 충족하는 장치는 장치 집합에 배치됩니다. 응용 프로그램에 대한 액세스 권한을 가진 사람이라면 누구든지 생성된 장치 집합에 액세스할 수 있으며, 따라서 누구든지 장치 집합을 보고, 수정하고, 삭제할 수 있습니다.

    ![장치 집합 쿼리](media/howto-use-device-sets/image2.png)

    > [!NOTE]
    > 장치 집합은 동적 쿼리입니다. 장치 목록을 볼 때마다 목록의 장치가 달라질 수 있습니다. 현재 쿼리 조건을 충족하는 장치에 따라 목록이 달라집니다.

1. **저장**을 선택합니다.

## <a name="configure-the-dashboard-for-your-device-set"></a>장치 집합에 대한 대시보드를 구성합니다.

장치 집합을 만든 후에는 **대시보드**를 구성할 수 있습니다. **대시보드**는 이미지 및 링크를 배치할 수 있는 홈페이지입니다. 장치 집합에 장치를 나열하는 그리드를 추가할 수도 있습니다.

1. 왼쪽 탐색 메뉴에서 **장치 집합**을 선택합니다.

1. **대시보드** 탭을 선택합니다.

1. **템플릿 편집**을 클릭합니다.

    ![디자인 모드 켜기](media/howto-use-device-sets/image3.png)

1. 이미지 추가에 대한 내용은 [이미지를 준비하여 Azure IoT Central 응용 프로그램에 업로드](howto-prepare-images.md)를 참조하세요.

1. 링크 타일 추가:
    1. 오른쪽 창에서 **링크**를 선택합니다.
    1. 링크 **제목**을 지정합니다.
    1. 링크를 클릭할 때 열리는 URL을 선택합니다.
    1. **제목** 아래에 표시될 링크 설명을 입력합니다.
    1. **저장**을 선택합니다.

        ![링크 저장](media/howto-use-device-sets/image7.png)

    1. **대시보드**에서 링크 타일을 이동하고 크기를 조정할 수 있습니다.

1. 그리드를 추가합니다. 그리드는 사용자가 선택하는 열이 포함된 장치 집합의 장치 표입니다.
    1. 오른쪽 창에서 **그리드**를 선택합니다.

        ![그리드 선택](media/howto-use-device-sets/image8.png)

    1. 그리드 **제목**을 지정합니다.
    1. **추가/제거**를 선택하여 표시할 열을 선택합니다. 나타나는 패널에서 표시할 열을 선택하고 오른쪽 화살표를 클릭하여 해당 열을 선택합니다.
    1. **확인**을 선택합니다.
    1. **저장**을 선택합니다.

        ![그리드 저장](media/howto-use-device-sets/image9.png)

    1. **대시보드**에 그리드를 끌어서 놓아 배치합니다.

    > [!NOTE]
    > 여러 이미지, 링크 및 그리드를 추가할 수 있습니다.
  
    1. **Done**을 클릭합니다.

    ![디자인 모드 끄기](media/howto-use-device-sets/image10.png)


### <a name="configuring-location-map-in-your-device-sets-dashboard"></a>장치 집합 대시보드에서 위치 맵 구성 
맵에서 장치 집합 위치를 시각화하려면 위치 맵을 추가할 수 있습니다.

장치 템플릿에서 위치 속성을 구성한 장치 집합 대시보드에 위치 맵을 추가하려면 [Azure Maps에서 제공하는 위치 속성 만들기](howto-set-up-template.md)를 참조합니다.


1. 장치 집합 대시보드의 라이브러리에서 맵을 선택합니다.

    ![장치 집합 대시보드 맵](media/howto-use-device-sets/LocationMaps1.png)

2. 제목을 지정하고 전에 장치 속성의 일부로 구성한 위치 속성을 선택합니다.
3. 저장하면 장치 집합에서 장치 위치를 표시하는 지도 타일이 표시됩니다.
4. 이제는 운영자가 디바이스 집합 대시보드를 볼 때 위치 맵을 포함해 지금까지 구성한 모든 타일을 볼 수 있어 한 번에 모든 디바이스의 위치를 시각화할 수 있습니다. 
    
> [!NOTE] 
> 지도를 원하는 크기로 크기를 조정할 수 있습니다. 맵에서 핀을 클릭하면 장치 정보, 이름 및 위치가 표시됩니다. 팝업 항목을 클릭하여 장치 속성 페이지로 이동할 수 있습니다.  


## <a name="configure-the-list-for-your-device-set"></a>장치 집합에 대한 목록 구성

장치 집합을 만든 후에는 **목록**을 구성할 수 있습니다. **목록**에는 사용자가 선택하는 열이 포함된 장치 집합의 모든 장치가 나열됩니다.

1. 왼쪽 탐색 메뉴에서 **장치 집합**을 선택합니다.

1. **목록** 탭을 선택합니다.

1. **열 옵션**을 선택합니다.

    ![열 옵션](media/howto-use-device-sets/image11.png)

1. 표시할 열을 선택하고 오른쪽 화살표를 클릭한 후 해당 열을 선택하여 표시할 열을 선택합니다.

    ![열 선택](media/howto-use-device-sets/image12.png)

1. **확인**을 선택합니다.

## <a name="analytics"></a>분석

장치 집합의 분석은 왼쪽 탐색 메뉴의 기본 분석 탭과 동일합니다. 분석에 대한 자세한 내용은 [분석을 만드는 방법](howto-create-analytics.md) 문서에서 알아볼 수 있습니다.

## <a name="next-steps"></a>다음 단계

Azure IoT Central 응용 프로그램에서 장치 집합을 사용하는 방법을 알아보았으니, 다음과 같은 후속 단계를 진행하시기 바랍니다.

> [!div class="nextstepaction"]
> [원격 분석 규칙을 만드는 방법](howto-create-telemetry-rules.md)
