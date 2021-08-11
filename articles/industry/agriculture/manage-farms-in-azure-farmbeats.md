---
title: 팜 관리
description: 팜을 관리하는 방법에 대해 설명합니다.
author: RiyazPishori
ms.topic: article
ms.date: 11/04/2019
ms.author: riyazp
ms.openlocfilehash: 70d7ae985a56842f2b02c5af5a7c7c1e589b2b56
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2021
ms.locfileid: "108759818"
---
# <a name="manage-farms"></a>팜 관리

Azure FarmBeats에서 팜을 관리할 수 있습니다. 이 문서에서는 팜을 만들고 팜 관리에 도움이 되는 디바이스, 센서 및 드론을 설치하는 방법에 대한 정보를 제공합니다.

## <a name="create-farms"></a>팜 만들기

다음 단계를 사용합니다.

1. Farm Accelerator(팜 가속기)에 로그인하면 **팜** 페이지가 표시됩니다.
    **팜** 페이지에는 구독에서 이미 생성된 팜 목록이 표시됩니다.

    다음은 샘플 이미지입니다.

    ![팜 페이지를 보여주는 스크린샷](./media/create-farms-in-azure-farmbeats/create-farm-main-page-1.png)


2. **Create Farm(팜 만들기)** 을 선택하고 **이름**, **자르기** 및 **주소** 를 제공합니다.
3. **Define Farm Boundary(팜 경계 정의)** (필수 필드)에서 **Mark on Map(지도에 표시)** 또는 **Paste GeoJSON code(GeoJSON 코드 붙여넣기)** 를 선택합니다.

다음은 팜 경계를 정의하는 두 가지 방법입니다.

1. **Mark on Map(지도에 표시)** : 지도 컨트롤 도구를 사용하여 팜의 경계를 그리거나 표시합니다. 경계를 표시하려면 ![지도에 경계를 그리기 위한 연필 아이콘을 보여주는 스크린샷](./media/create-farms-in-azure-farmbeats/pencil-icon-1.png) 정확한 경계를 표시합니다.

    ![지도에 그려진 경계를 보여주는 스크린샷](./media/create-farms-in-azure-farmbeats/create-farm-mark-on-map-1.png)

2. **Paste GeoJson Code(GeoJson 코드 붙여넣기)** : GeoJson은 JSON(JavaScript Object Notation)을 사용하여 지리 데이터 구조를 인코딩하기 위한 형식입니다. 이 옵션은 팜 경계를 표시하기 위해 GeoJSON 문자열을 입력할 수 있는 텍스트 상자를 표시합니다. GeoJSON.io에서 GeoJSON 코드를 만들 수도 있습니다.
도구 설명을 사용하여 정보를 입력합니다.

    ![팜 만들기 화면에서 Paste GeoJson Code(GeoJson 코드 붙여넣기) 옵션이 강조 표시된 스크린샷](./media/create-farms-in-azure-farmbeats/create-new-farm-1.png)

3.  **제출** 을 선택하여 팜을 만듭니다. 새 팜이 만들어지고 **팜** 페이지에 표시됩니다.

## <a name="view-farm"></a>팜 보기

Farm list(팜 목록) 페이지에는 만들어진 팜 목록이 표시됩니다. 목록을 볼 팜을 선택합니다.

 - **디바이스 수** - 팜 내에 배포된 디바이스의 수와 상태를 표시합니다.
 - **지도** - 디바이스가 배포된 팜의 지도입니다.
 - **원격 분석** -팜에 배포된 센서의 원격 분석을 표시합니다.
 - **Latest Precision Maps(최신 정밀 지도)** - 최신 위성 인덱스 지도(EVI, NDWI), 토양 수분 열 지도 및 센서 배치 지도를 표시합니다.

## <a name="edit-farm"></a>팜 편집

**팜** 페이지에는 만들어진 팜 목록이 표시됩니다.

1.  보고 편집할 팜을 선택합니다.
2.  **팜 편집** 을 선택하여 팜 정보를 편집합니다. **Farm Details(팜 세부 정보)** 창에서 **이름**, **자르기**, **주소** 를 편집하고 **Farm Boundary(팜 경계)** 필드를 정의할 수 있습니다.

    ![Farm Beats 프로젝트](./media/create-farms-in-azure-farmbeats/edit-farm-1.png)

3. **제출** 을 선택하여 편집한 세부 정보를 저장합니다.

## <a name="delete-farm"></a>팜 삭제

**팜** 페이지에는 만들어진 팜 목록이 표시됩니다. 다음 단계를 사용하여 팜을 삭제합니다.

1.  팜 세부 정보를 삭제하려면 목록에서 팜을 선택합니다.
2.  팜을 삭제하려면 **팜 삭제** 를 선택합니다.

    ![팜 삭제 화면이 표시되고 삭제 단추가 강조 표시된 스크린샷.](./media/create-farms-in-azure-farmbeats/delete-farm-1.png)

    > [!NOTE]
    > 팜을 삭제해도 팜과 연결된 디바이스 및 지도는 삭제되지 않습니다. 디바이스 및 지도와 관련된 모든 팜 정보는 관련이 없습니다. FarmBeats 서비스에서 디바이스, 원격 분석 및 지도를 계속 볼 수 있습니다.


## <a name="next-steps"></a>다음 단계

팜을 만들었으므로 이제 팜으로 들어오는 [센서 데이터를 가져오는](get-sensor-data-from-sensor-partner.md) 방법에 대해 알아봅니다.
