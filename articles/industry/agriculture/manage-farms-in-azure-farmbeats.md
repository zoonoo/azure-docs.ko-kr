---
title: 팜 관리
description: 팜을 관리 하는 방법을 설명 합니다.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 09144c4c35ab911b60931849807123608f2c3cdd
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/05/2020
ms.locfileid: "78385203"
---
# <a name="manage-farms"></a>팜 관리

Azure FarmBeats에서 팜을 관리할 수 있습니다. 이 문서에서는 팜을 만드는 방법, 팜을 관리 하는 데 도움이 되는 장치, 센서 및 드 론을 만드는 방법에 대 한 정보를 제공 합니다.

## <a name="create-farms"></a>팜 만들기

다음 단계를 사용합니다.

1. 팜 가속기에 **로그인 하면 팜 페이지가 표시** 됩니다.
    **팜** 페이지는 구독에서 이미 만들어져 있는 경우의 팜 목록을 표시 합니다.

    샘플 이미지는 다음과 같습니다.

    ![Farm Beats 프로젝트](./media/create-farms-in-azure-farmbeats/create-farm-main-page-1.png)


2. **팜 만들기** 를 선택 하 **고 이름**, **자르기** 및 **주소**를 제공 합니다.
3. **팜 경계 정의**(필수 필드)에서 **지도에 표시** 또는 **붙여넣기 GeoJSON 코드**를 선택 합니다.

팜 경계를 정의 하는 두 가지 방법은 다음과 같습니다.

1. **지도에 표시**: 지도 컨트롤 도구를 사용 하 여 팜의 경계를 그리거나 표시 합니다. 경계를 표시 하기 위해 ![프로젝트 팜](./media/create-farms-in-azure-farmbeats/pencil-icon-1.png)를 보다 정확 하 게 표시 하 고 정확한 경계를 표시 합니다.

    ![Farm Beats 프로젝트](./media/create-farms-in-azure-farmbeats/create-farm-mark-on-map-1.png)

2. **GeoJson 코드 붙여넣기**: GeoJson는 JAVASCRIPT OBJECT NOTATION (JSON)를 사용 하 여 지리적 데이터 구조 인코딩에 사용 되는 형식입니다. 이 옵션은 팜 경계를 표시 하기 위해 GeoJSON 문자열을 입력할 수 있는 텍스트 상자를 표시 합니다. GeoJSON.io에서 GeoJSON 코드를 만들 수도 있습니다.
도구 설명을 사용 하 여 정보를 입력 합니다.

    ![Farm Beats 프로젝트](./media/create-farms-in-azure-farmbeats/create-new-farm-1.png)

3.  **제출** 을 선택 하 여 팜을 만듭니다. 새 팜이 만들어지고 **팜** 페이지에 표시 됩니다.

## <a name="view-farm"></a>팜 보기

팜 목록 페이지에는 생성 된 팜 목록이 표시 됩니다. 목록을 볼 팜을 선택 합니다.

 - **장치 수** -팜 내에 배포 된 장치의 수와 상태를 표시 합니다.
 - **맵** -팜에 배포 된 장치와 팜의 맵입니다.
 - **원격 분석** -팜에 배포 된 센서의 원격 분석을 표시 합니다.
 - **최신 전체 자릿수 맵** -최신 위성 인덱스 맵 (EVI, ndwi), 토양 습기 열 지도 및 센서 배치 맵을 표시 합니다.

## <a name="edit-farm"></a>팜 편집

**팜** 페이지에는 생성 된 팜 목록이 표시 됩니다.

1.  팜을 보고 편집할 팜을 선택 합니다.
2.  팜 **편집** 을 선택 하 여 팜 정보를 편집 합니다. **팜 세부 정보** 창에서 **팜 경계** 필드의 **이름**, **자르기**, **주소**및 정의를 편집할 수 있습니다.

    ![Farm Beats 프로젝트](./media/create-farms-in-azure-farmbeats/edit-farm-1.png)

3. **제출** 을 선택 하 여 편집 된 세부 정보를 저장 합니다.

## <a name="delete-farm"></a>팜 삭제

**팜** 페이지에 생성 된 팜 목록이 표시 됩니다. 팜을 삭제 하려면 다음 단계를 사용 합니다.

1.  팜 세부 정보를 삭제 하려면 목록에서 팜을 선택 합니다.
2.  팜 **삭제** 를 선택 하 여 팜을 삭제 합니다.

    ![Farm Beats 프로젝트](./media/create-farms-in-azure-farmbeats/delete-farm-1.png)

    > [!NOTE]
    > 팜을 삭제 하면 팜에 연결 된 장치 및 맵이 삭제 되지 않습니다. 장치와 연결 된 팜 정보는 관련이 없습니다. FarmBeats 서비스에서 장치, 원격 분석 및 맵을 계속 볼 수 있습니다.


## <a name="next-steps"></a>다음 단계

팜을 만들었으므로 이제 팜으로 들어오는 [센서 데이터를 가져오는](get-sensor-data-from-sensor-partner.md) 방법에 대해 알아보세요.
