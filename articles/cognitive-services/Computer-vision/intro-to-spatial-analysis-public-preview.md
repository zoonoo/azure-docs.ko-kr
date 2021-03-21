---
title: 공간 분석 개요
titleSuffix: Azure Cognitive Services
description: 이 문서에서는 Computer Vision 공간 분석 컨테이너의 기본 개념 및 기능을 설명 합니다.
services: cognitive-services
author: nitinme
manager: nitinme
ms.author: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 02/01/2021
ms.openlocfilehash: ad05dd59c925242baf5c2b0e36c1f51bc4fec5d4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100575368"
---
# <a name="overview-of-computer-vision-spatial-analysis"></a>Computer Vision 공간 분석 개요

Computer Vision 공간 분석은 조직에서 지정 된 영역 내에서 사람의 이동과 상태를 파악 하 여 실제 공간의 가치를 극대화 하는 데 도움이 되는 Azure Cognitive Services Computer Vision의 새로운 기능입니다. 이를 통해 CCTV 또는 감시 카메라에서 비디오를 수집 하 고 AI 작업을 실행 하 여 비디오 스트림에서 정보를 추출 하 고 다른 시스템에서 사용할 이벤트를 생성할 수 있습니다. 카메라 스트림의 입력을 통해 AI 작업은 공간을 입력 하는 사용자 수 또는 얼굴 마스크 및 소셜 distancing 지침을 사용 하 여 규정 준수를 측정 하는 것과 같은 작업을 수행할 수 있습니다.

## <a name="the-basics-of-spatial-analysis"></a>공간 분석의 기본 사항

현재 공간 분석의 핵심 작업은 비디오를 수집 하 고, 비디오의 사용자를 검색 하 고, 시간에 따라 이동 하는 사람을 추적 하 고, 사용자가 관심 영역과 상호 작용할 때 이벤트를 생성 하는 파이프라인을 기반으로 합니다.

## <a name="spatial-analysis-terms"></a>공간 분석 용어

| 용어 | 정의 |
|------|------------|
| 사용자 검색 | 이 구성 요소는 "이 이미지의 사람들은 어디에 있습니까?" 라는 질문에 답변 합니다. 이미지에서 사람을 찾고 각 사용자의 위치를 나타내는 경계 상자를 사람 추적 구성 요소에 전달 합니다. |
| 사용자 추적 | 이 구성 요소는 사람들이 카메라 앞에서 이동할 때 시간이 지남에 따라 사용자 검색을 연결 합니다. 사용자가 일반적으로 이동 하는 방법에 대 한 임시 논리를 사용 하 여이 작업을 수행 하는 사람의 전반적인 모습에 대 한 기본 정보입니다. 여러 카메라의 사용자는 추적 하지 않습니다. 사용자가 카메라의 보기 필드를 대략 1 분 이상 표시 한 후 카메라 보기로 다시 들어가면 시스템에서이를 새 사용자로 확인 합니다. 사용자 추적은 카메라 전체의 개인을 고유 하 게 식별 하지 않습니다. 얼굴 인식 또는 gait 추적을 사용 하지 않습니다. |
| 얼굴 마스크 검색 | 이 구성 요소는 카메라의 보기 필드에서 사람의 얼굴 위치를 검색 하 고 얼굴 마스크의 유무를 식별 합니다. 이렇게 하기 위해 AI 작업은 비디오에서 이미지를 검색 합니다. 얼굴이 감지 되 면 서비스에서 얼굴 주위에 경계 상자를 제공 합니다. 개체 검색 기능을 사용 하 여 경계 상자 내에 얼굴 마스크가 있는지를 식별 합니다. 얼굴 마스크 검색은 다른 얼굴에서 얼굴을 구분 하거나 얼굴 특성을 예측 또는 분류 하거나 얼굴 인식 기능을 수행 하는 것을 포함 하지 않습니다. |
| 관심 영역 | 구성의 일부로 입력 비디오에 정의 된 영역 또는 선입니다. 개인이 비디오 영역을 조작 하는 경우 시스템에서 이벤트를 생성 합니다. 예를 들어 PersonCrossingLine 작업의 경우 비디오에서 줄이 정의 됩니다. 사용자가 해당 줄을 교차할 때 이벤트가 생성 됩니다. |
| 이벤트 | 이벤트는 공간 분석의 기본 출력입니다. 각 작업은 주기적으로 특정 이벤트를 내보냅니다 (예: 분당 한 번) 또는 특정 트리거가 발생 하는 경우 이벤트는 입력 비디오에서 발생 한 작업에 대 한 정보를 포함 하지만 이미지 또는 비디오는 포함 하지 않습니다. 예를 들어, PeopleCount 작업은 사용자 수가 변경 될 때마다 (트리거) 또는 1 분 마다 (주기적으로) 업데이트 된 수를 포함 하는 이벤트를 내보낼 수 있습니다. |

## <a name="responsible-use-of-spatial-analysis-technology"></a>공간 분석 기술의 책임 된 사용

공간 분석 기술을 사용 하는 방법에 대 한 자세한 내용은 [투명성 메모](/legal/cognitive-services/computer-vision/transparency-note-spatial-analysis?context=%2fazure%2fcognitive-services%2fComputer-vision%2fcontext%2fcontext)를 참조 하세요. Microsoft의 투명 정보는 AI 기술의 작동 방식을 이해 하는 데 도움을 주기 위해 작성 된 시스템 소유자는 시스템 성능 및 동작에 영향을 줄 수 있으며 기술, 사용자 및 환경을 포함 하 여 전체 시스템에 대해 생각 하는 중요성을 이해 하는 데 도움을 주기 위한 것입니다.

## <a name="spatial-analysis-gating-for-public-preview"></a>공개 미리 보기용 공간 분석 제어

공간 분석이 용으로 설계 된 시나리오에 사용 되도록 하기 위해이 기술은 응용 프로그램 프로세스를 통해 고객에 게 제공 됩니다. 공간 분석에 액세스 하려면 온라인 흡입구 폼을 작성 하 여 시작 해야 합니다. [여기에서 응용 프로그램을 시작](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyQZ7B8Cg2FEjpibPziwPcZUNlQ4SEVORFVLTjlBSzNLRlo0UzRRVVNPVy4u)합니다.

공간 분석 공개 미리 보기에 대 한 액세스는이 제어 된 미리 보기 중에 제한 된 수의 고객을 지 원하는 자격 기준, 심사 프로세스 및 가용성에 따라 Microsoft의 유일한 판단을 받습니다. 공개 미리 보기에서는 Microsoft와 중요 한 관계에 있는 고객을 찾고, 권장 되는 사용 사례에 대 한 작업을 수행 하 고, 책임이 있는 AI 약정을 유지 하는 추가 시나리오에 관심이 있습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [공간 분석 컨테이너 시작](spatial-analysis-container.md)