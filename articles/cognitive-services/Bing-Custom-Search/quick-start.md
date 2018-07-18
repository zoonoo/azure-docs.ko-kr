---
title: 처음으로 Bing Custom Search 인스턴스 만들기 - Microsoft Cognitive Services
description: Bing Custom Search를 사용하려면 웹 보기 또는 조각을 정의하는 사용자 지정 검색 인스턴스를 만들어야 합니다. 인스턴스에는 Bing에서 검색하려는 공용 도메인, 하위 사이트 및 웹 페이지를 지정하는 설정과 모든 순위 조정이 포함됩니다.
services: cognitive-services
author: brapel
manager: ehansen
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: conceptual
ms.date: 05/07/2017
ms.author: v-brapel
ms.openlocfilehash: 35f0bca01de1c2087f6ae30949cca9b03192b838
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35374742"
---
# <a name="create-your-first-bing-custom-search-instance"></a>처음으로 Bing Custom Search 인스턴스 만들기
Bing Custom Search를 사용하려면 웹 보기 또는 조각을 정의하는 사용자 지정 검색 인스턴스를 만들어야 합니다. 인스턴스에는 Bing에서 검색하려는 공용 도메인, 웹 사이트 및 웹 페이지를 지정하는 설정과 모든 순위 조정이 포함됩니다. 인스턴스를 만들려면 Bing Custom Search [포털](https://customsearch.ai)을 사용합니다. 

## <a name="create-a-custom-search-instance"></a>사용자 지정 검색 인스턴스 만들기

Bing Custom Search 인스턴스를 만들려면

1.  Custom Search API에 대한 키를 받습니다. [Cognitive Services 체험하기](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search)를 참조하세요.
2.  MSA(Microsoft 계정)를 사용하여 포털에 로그인합니다. **로그인** 단추를 클릭합니다. 포털을 처음 사용하는 경우 아래의 추가 단계를 따르세요. 처음 사용하는 경우가 아니면 3단계에서 계속 진행합니다.
    - MSA가 없는 경우 **Microsoft 계정 만들기**를 클릭합니다. 포털에서 데이터에 액세스할 수 있는 권한을 요청합니다. **예**를 클릭합니다.
    - Cognitive Services 사용 약관에 동의합니다. **동의함**을 선택하고 **동의**를 클릭합니다.  
3.  로그인한 후 **새 인스턴스**를 클릭하고 인스턴스의 이름을 지정합니다. 의미 있는 이름을 사용하고 검색에서 반환하는 콘텐츠 형식을 설명합니다. 언제든지 이름을 변경할 수 있습니다. 
4.  **검색 환경**의 **활성** 탭에서 검색에 포함할 하나 이상 사이트의 URL을 입력합니다.
5.  인스턴스가 결과를 반환하는지 확인하려면 오른쪽 미리 보기 창에 쿼리를 입력합니다. 결과가 없으면 새 사이트를 지정합니다. Bing은 인덱싱된 공용 사이트에 대해서만 결과를 반환합니다.
6.  **게시**를 클릭하여 구성 변경 내용을 프로덕션에 게시합니다. 메시지가 표시되면 **게시**를 클릭하여 확인합니다.
7.  **프로덕션** > **끝점**을 클릭하고 **사용자 지정 구성 ID**를 복사합니다. Custom Search API를 호출하려면 이 ID가 필요합니다.

## <a name="next-steps"></a>다음 단계

다음 방법 가이드의 지침에 따라 방금 만든 사용자 지정 검색 인스턴스로 작업을 계속합니다.

- [사용자 지정 검색 환경 구성](./define-your-custom-view.md)
- [사용자 지정 검색 호출](./search-your-custom-view.md)
- [사용자 지정 검색 공유](./share-your-custom-search.md)
- [호스트된 UI 환경 구성](./hosted-ui.md)
- [장식 표식을 사용하여 텍스트를 강조 표시](./hit-highlighting.md)
- [웹 페이지 페이징](./page-webpages.md)
