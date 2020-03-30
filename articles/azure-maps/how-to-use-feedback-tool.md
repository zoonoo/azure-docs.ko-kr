---
title: Azure 지도에 데이터 피드백 제공 | 마이크로소프트 Azure 지도
description: Microsoft Azure 지도 피드백 도구를 사용하여 데이터 피드백을 제공합니다.
author: philmea
ms.author: philmea
ms.date: 08/19/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 92469370f1ea64f5ee1bc2a84e47cc8b294b5375
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335337"
---
# <a name="provide-data-feedback-to-azure-maps"></a>Azure 지도에 데이터 피드백 제공

Azure 지도는 2018년 5월부터 사용할 수 있습니다. Azure Maps는 다양한 종류의 비즈니스 사용 사례를 통해 엔터프라이즈 고객을 지원하기 위해 새로운 지도 데이터, 사용하기 쉬운 REST API 및 강력한 SDK를 제공하고 있습니다. 현실 세계는 매 초마다 변화하고 있으며 고객에게 사실적인 디지털 표현을 제공하는 것이 중요합니다. 시설을 열거나 닫을 계획인 고객은 즉시 업데이트할 지도가 필요합니다. 따라서 적절한 시설에서 납품, 유지 보수 또는 고객 서비스를 효율적으로 계획할 수 있습니다. 고객이 직접 데이터 피드백을 제공할 수 있도록 Azure Maps 데이터 피드백 사이트를 만들었습니다. 고객의 데이터 피드백은 데이터 제공업체와 맵 편집자에게 직접 전달됩니다. 그들은 신속하게 평가하고 우리의 매핑 제품에 피드백을 통합 할 수 있습니다.  

[Azure Maps Data 피드백 사이트는](https://feedback.azuremaps.com) 고객이 특히 관심 지점및 거주지 주소에서 맵 데이터 피드백을 제공할 수 있는 쉬운 방법을 제공합니다. 이 문서에서는 Azure Maps 피드백 사이트를 사용하여 다양한 종류의 피드백을 제공하는 방법을 설명합니다.

## <a name="add-a-business-place-or-a-residential-address"></a>사업장 또는 거주지 주소 추가 

누락된 관심 지점이나 거주지 주소에 대한 피드백을 제공할 수 있습니다. 그렇게 하려면 두 가지 방법이 있습니다. Azure Map 데이터 피드백 사이트를 열고 누락된 위치의 좌표를 검색한 다음 "장소 추가"를 클릭합니다.

  ![누락된 위치 검색](./media/how-to-use-feedback-tool/search-poi.png)

또는 맵과 상호 작용할 수 있습니다. 위치를 클릭하여 좌표에 핀을 놓고 "장소 추가"를 클릭합니다.

  ![핀 추가](./media/how-to-use-feedback-tool/add-poi.png)

클릭하면 양식으로 이동하여 해당 장소에 대한 세부 정보를 제공합니다.

  ![장소 추가](./media/how-to-use-feedback-tool/add-a-place.png)

## <a name="fix-a-business-place-or-a-residential-address"></a>사업장 또는 거주지 주소 수정 

피드백 사이트를 사용하면 사업장이나 주소를 검색하고 찾을 수도 있습니다. 주소 또는 핀 위치가 올바르지 않은 경우 피드백을 제공하여 수정할 수 있습니다. 주소를 수정하기 위한 피드백을 제공하려면 검색 표시줄을 사용하여 사업장 또는 거주지 주소를 검색합니다. 결과 목록에서 관심 있는 위치를 클릭합니다. "이 장소 수정"을 클릭합니다.

  ![수정할 검색 장소](./media/how-to-use-feedback-tool/fix-place.png)

주소를 수정하기 위한 피드백을 제공하려면 "장소 수정" 양식을 작성한 다음 "제출" 버튼을 클릭합니다.

  ![양식 수정](./media/how-to-use-feedback-tool/fix-form.png)

장소의 핀 위치가 잘못된 경우 '핀 위치가 올바르지 않습니다'라는 '장소 수정' 양식의 확인란을 선택합니다. 핀을 올바른 위치로 이동한 다음 "제출" 버튼을 클릭합니다.

  ![핀 위치 이동](./media/how-to-use-feedback-tool/move-pin.png)

## <a name="add-a-comment"></a>댓글 추가 

피드백 도구를 사용하면 위치를 검색할 수 있을 뿐만 아니라 위치와 관련된 세부 정보에 대한 자유 양식 텍스트 주석을 추가할 수도 있습니다. 댓글을 추가하려면 위치를 검색하거나 위치를 클릭합니다. "댓글 추가"를 클릭하고 댓글을 작성한 다음 "제출"을 클릭합니다.

  ![설명 추가](./media/how-to-use-feedback-tool/add-comment.png)

## <a name="track-status"></a>상태 추적 

또한 "상태 추적" 상자를 선택하고 요청을 하는 동안 이메일을 제공하여 요청 상태를 추적할 수도 있습니다. 이메일에 요청의 최신 상태를 제공하는 추적 링크가 제공됩니다. 

  ![피드백 상태](./media/how-to-use-feedback-tool/feedback-status.png)


## <a name="next-steps"></a>다음 단계

Azure 지도와 관련된 기술적인 질문을 게시하려면 다음 웹 방문을 방문하십시오.

* [Azure 맵 스택 오버플로](https://stackoverflow.com/questions/tagged/azure-maps)
* [Azure 지도 피드백 포럼](https://feedback.azure.com/forums/909172-azure-maps)
