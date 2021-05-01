---
title: Azure Maps에 대한 데이터 피드백 제공 | Microsoft Azure Maps
description: Microsoft Azure Maps 피드백 도구를 사용하여 데이터 피드백을 제공합니다.
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 4be775d8a6a8c476bcc659a902c7f43c12a6d6d0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96905335"
---
# <a name="provide-data-feedback-to-azure-maps"></a>Azure Maps에 대한 데이터 피드백 제공

Azure Maps는 2018년 5월부터 사용할 수 있습니다. Azure Maps는 새로운 지도 데이터, 사용하기 쉬운 REST API 및 강력한 SDK를 제공하여 다양한 종류의 비즈니스 사용 사례로 당사 엔터프라이즈 고객을 지원합니다. 현실 세계는 매초 바뀌고 있으며 고객에게 사실적인 디지털 표현을 제공하는 것이 매우 중요합니다. 시설을 열거나 닫을 계획인 고객은 지도를 즉시 업데이트해야 합니다. 따라서 올바른 시설에서 배달, 유지 관리 또는 고객 서비스를 효율적으로 계획할 수 있습니다. 고객에게 직접 데이터 피드백을 제공할 수 있도록 하는 Azure Maps 데이터 피드백 사이트를 만들었습니다. 고객의 데이터 피드백은 데이터 공급자 및 해당 맵 편집기로 직접 이동합니다. 이를 통해 피드백을 신속하게 평가하고 매핑 제품에 통합할 수 있습니다.  

[Azure Maps 데이터(미리 보기) 피드백 사이트](https://feedback.azuremaps.com)를 통해 맵 데이터 피드백, 특히 비즈니스적 관심 지점 및 거주지 주소를 고객에게 쉽게 제공할 수 있습니다. 이 문서에서는 Azure Maps 피드백 사이트를 사용하여 다양한 종류의 피드백을 제공하는 방법을 안내합니다.

## <a name="add-a-business-place-or-a-residential-address"></a>비즈니스 위치 또는 거주지 주소 추가 

누락된 관심 지점 도는 거주지 주소에 대한 피드백을 제공할 수 있습니다. 그렇게 하려면 두 가지 방법이 있습니다. Azure Map 데이터 피드백 사이트를 열고 누락된 위치 좌표를 검색한 다음 "위치 추가"를 클릭합니다.

  ![누락된 위치 검색](./media/how-to-use-feedback-tool/search-poi.png)

또는 지도와 상호 작용할 수 있습니다. 좌표에 핀을 놓을 위치를 클릭하고 "위치 추가"를 클릭합니다.

  ![pin 추가](./media/how-to-use-feedback-tool/add-poi.png)

클릭하면 위치에 해당하는 세부 정보를 제공하는 약식으로 이동합니다.

  ![위치 추가](./media/how-to-use-feedback-tool/add-a-place.png)

## <a name="fix-a-business-place-or-a-residential-address"></a>비즈니스 위치 또는 거주지 주소 수정 

피드백 사이트를 사용하여 비즈니스 장소 또는 주소를 검색하고 찾을 수도 있습니다. 주소나 핀 위치가 올바르지 않은 경우 이를 수정하도록 피드백을 제공할 수 있습니다. 주소 수정에 대한 피드백을 제공하려면 검색 창을 사용하여 비즈니스 위치 또는 거주자 주소를 검색하세요. 결과 목록에서 관심 있는 위치를 클릭합니다. "이 장소 수정"을 클릭합니다.

  ![수정할 장소 검색](./media/how-to-use-feedback-tool/fix-place.png)

주소 수정 피드백을 제공하려면 "장소 수정" 양식을 작성하여 "제출" 단추를 클릭합니다.

  ![수정 양식](./media/how-to-use-feedback-tool/fix-form.png)

위치에 대한 핀 위치가 잘못된 경우 "장소 수정" 양식에서 "핀 위치가 잘못되었습니다"라는 확인란을 선택합니다. 올바른 위치로 핀을 옮긴 다음 "제출" 단추를 클릭합니다.

  ![핀 위치 이동](./media/how-to-use-feedback-tool/move-pin.png)

## <a name="add-a-comment"></a>댓글 추가 

피드백 도구를 사용하면 위치를 검색할 수 있을 뿐 아니라 해당 위치와 관련된 세부 사항에 대해 자유 형식 텍스트를 설명으로 추가할 수도 있습니다. 설명을 추가하려면 위치에 대해 검색하거나 해당 위치를 클릭합니다. "설명 추가"를 클릭하고 설명을 작성한 다음 "제출"을 클릭합니다.

  ![설명 추가](./media/how-to-use-feedback-tool/add-comment.png)

## <a name="track-status"></a>상태 추적 

"상태를 추적하고 싶습니다" 확인란을 선택하고 요청을 작성하면서 이메일을 입력하면 요청 상태를 추적할 수도 있습니다. 요청에 대한 최신 상태를 알려주는 이메일의 추적 링크를 받게 됩니다. 

  ![피드백 상태](./media/how-to-use-feedback-tool/feedback-status.png)


## <a name="next-steps"></a>다음 단계

Azure Maps와 관련된 기술적 질문을 게시하려면 다음을 방문하세요.

* [Microsoft Q & A](/answers/topics/azure-maps.html)