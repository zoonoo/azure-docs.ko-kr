---
title: 농장 관리
description: 팜 관리 방법에 대해 설명합니다.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 09144c4c35ab911b60931849807123608f2c3cdd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79271721"
---
# <a name="manage-farms"></a>팜 관리

Azure FarmBeats에서 팜을 관리할 수 있습니다. 이 문서에서는 농장을 만드는 방법에 대한 정보를 제공하며, 팜을 관리하는 데 도움이 되는 장치, 센서 및 드론을 설치합니다.

## <a name="create-farms"></a>팜 만들기

다음 단계를 사용합니다.

1. 농장 액셀러레이터에 로그인하면 **농장** 페이지가 표시됩니다.
    **팜** 페이지에는 팜이 구독에서 이미 생성된 경우팜 목록이 표시됩니다.

    다음은 샘플 이미지입니다.

    ![Farm Beats 프로젝트](./media/create-farms-in-azure-farmbeats/create-farm-main-page-1.png)


2. **팜 만들기를** 선택하고 **이름,** **자르기** 및 **주소를**제공합니다.
3. 팜 **경계 정의에서**(필수 필드)는 **맵에서 마크를** 선택하거나 **GeoJSON 코드를**붙여넣습니다.

팜 경계를 정의하는 두 가지 방법은 다음과 같습니다.

1. **지도에 표시**: 지도 제어 도구를 사용하여 팜의 경계를 그리고 표시합니다. 경계를 표시하기 ![위해 프로젝트](./media/create-farms-in-azure-farmbeats/pencil-icon-1.png) 팜 비트와 정확한 경계를 표시합니다.

    ![Farm Beats 프로젝트](./media/create-farms-in-azure-farmbeats/create-farm-mark-on-map-1.png)

2. **붙여 넣기 GeoJson 코드**: GeoJSON은 자바 스크립트 개체 표기법 (JSON)을 사용하여 지리적 데이터 구조를 인코딩하기위한 형식입니다. 이 옵션은 Farm 경계를 표시하기 위해 GeoJSON 문자열을 입력할 수 있는 텍스트 상자를 표시합니다. GeoJSON.io GeoJSON 코드를 만들 수도 있습니다.
도구 설명에서 정보를 입력할 수 있습니다.

    ![Farm Beats 프로젝트](./media/create-farms-in-azure-farmbeats/create-new-farm-1.png)

3.  팜을 만들려면 **제출을** 선택합니다. 새 팜이 만들어지고 **팜** 페이지에 표시됩니다.

## <a name="view-farm"></a>팜 보기

팜 목록 페이지에는 생성된 팜 목록이 표시됩니다. 다음 목록을 보려면 팜을 선택합니다.

 - **장치 수** — 팜 내에 배포된 장치의 수와 상태를 표시합니다.
 - **지도** — 팜에 배포된 장치가 있는 팜의 지도입니다.
 - **원격 분석** — 팜에 배포된 센서의 원격 분석을 표시합니다.
 - **최신 정밀도 -** 최신 위성 지수지도 (EVI, NDWI), 토양 수분 히트맵 및 센서 배치지도를 표시합니다.

## <a name="edit-farm"></a>팜 편집

**팜** 페이지에는 생성된 팜 목록이 표시됩니다.

1.  팜을 보고 편집할 팜을 선택합니다.
2.  **팜 편집을** 선택하여 팜 정보를 편집합니다. 팜 **세부 정보** 창에서 **이름,** **자르기,** **주소**및 **팜 경계** 필드를 정의할 수 있습니다.

    ![Farm Beats 프로젝트](./media/create-farms-in-azure-farmbeats/edit-farm-1.png)

3. 편집된 세부 정보를 저장하려면 **제출을** 선택합니다.

## <a name="delete-farm"></a>팜 삭제

**팜** 페이지에는 생성된 팜 목록이 표시됩니다. 다음 단계를 사용하여 팜을 삭제합니다.

1.  목록에서 팜을 선택하여 팜 세부 정보를 삭제합니다.
2.  **팜 삭제를** 선택하여 팜을 삭제합니다.

    ![Farm Beats 프로젝트](./media/create-farms-in-azure-farmbeats/delete-farm-1.png)

    > [!NOTE]
    > 팜을 삭제하면 팜과 연결된 장치 및 맵이 삭제되지 않습니다. 장치 및 맵과 연결된 팜 정보는 관련이 없습니다. FarmBeats 서비스에서 장치, 원격 분석 및 맵을 계속 볼 수 있습니다.


## <a name="next-steps"></a>다음 단계

이제 팜을 만들었으니 [팜으로 센서 데이터를](get-sensor-data-from-sensor-partner.md) 유입하는 방법을 알아봅니다.
