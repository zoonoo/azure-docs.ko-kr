---
title: 'Bing Custom Search: Custom Autosuggest 제안 정의 | Microsoft Docs'
description: 사용자 지정 제안으로 Custom Autosuggest를 구성하는 방법을 설명합니다.
services: cognitive-services
author: brapel
manager: ehansen
ms.service: cognitive-services
ms.technology: bing-custom-search
ms.topic: article
ms.date: 09/28/2017
ms.author: v-brapel
ms.openlocfilehash: e7a62a79bdc2e486fb6bfca34eb4addeba2bde0e
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/07/2018
ms.locfileid: "44158316"
---
# <a name="configure-your-custom-autosuggest-experience"></a>Custom Autosuggest 환경 구성
적절한 수준에서 Custom Search를 구독한 경우([가격 책정 페이지](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/) 참조) Custom Search 환경의 검색 제안을 사용자 지정할 수 있습니다. Custom Autosuggest는 사용자가 제공하는 부분 쿼리 문자열을 기준으로 제안된 쿼리 목록을 반환합니다. Custom Autosuggest를 사용하는 경우 검색 환경과 관련된 사용자 지정 검색 제안을 제공합니다. 사용자 지정 제안만 반환할지 또는 Bing 제안도 포함할지를 지정합니다. Bing 제안을 포함하는 경우 사용자 지정 제안이 Bing 제안보다 먼저 표시됩니다. Bing 제안은 Custom Search 인스턴스의 컨텍스트로 제한됩니다.

## <a name="configure-custom-autosuggest"></a>Custom Autosuggest 구성
Custom Search 인스턴스에 대해 Custom Autosuggest를 구성하려면 다음 지침에 따르세요.

1.  [Custom Search](https://customsearch.ai)에 로그인합니다.
2.  Custom Search 인스턴스를 클릭합니다. 인스턴스를 만들려면 [처음으로 Bing Custom Search 인스턴스 만들기](quick-start.md)를 참조하세요.
3.  **자동 제안** 탭을 클릭합니다.

## <a name="enable-bing-suggestions"></a>Bing 제안 사용
Bing 제안을 사용하려면 **자동 Bing 제안** 슬라이더를 켬 위치로 전환합니다. 슬라이더가 파란색이 됩니다.

## <a name="add-suggestions"></a>제안 추가
제안을 추가하려면 텍스트 상자에 입력합니다. Enter 키를 누르거나 **+** 아이콘을 클릭합니다. 사용자 지정 제안은 임의 언어일 수 있으며 Bing 제안보다 먼저 표시됩니다.

## <a name="upload-suggestions"></a>제안 업로드
파일에서 제안 목록을 업로드할 수 있습니다. 각 제안을 새 줄에 배치합니다. 업로드 아이콘을 클릭하고 파일을 선택합니다.

## <a name="remove-suggestions"></a>제안 제거
제안을 제거하려면 제거할 제안 옆에 있는 제거 아이콘을 클릭합니다.

[!INCLUDE [publish or revert](./includes/publish-revert.md)]

  >[!NOTE]  
  >Custom Autosuggest 구성 변경 내용을 적용하는 데 최대 24시간이 걸릴 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [사용자 지정 제안 가져오기](./get-custom-suggestions.md)
- [사용자 지정 인스턴스 검색](./search-your-custom-view.md)
- [사용자 지정 호스트된 UI 구성 및 사용](./hosted-ui.md)