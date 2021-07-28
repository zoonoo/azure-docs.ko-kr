---
title: Azure Purview Studio 사용
description: 이 문서에서는 Azure Purview Studio를 사용하는 방법을 설명합니다.
author: nayenama
ms.author: nayenama
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 04/02/2021
ms.openlocfilehash: ba22c322d47d8738b1d607597d6f93b8b8456616
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106283872"
---
# <a name="use-purview-studio"></a>Purview Studio 사용

이 문서에서는 Azure Purview의 일부 기본 기능을 간단히 설명합니다.

## <a name="prerequisites"></a>필수 구성 요소

* Azure Purview 계정은 이미 Azure Portal에 만들어져 있고 사용자는 Purview Studio에 액세스할 권한이 있습니다.

## <a name="launch-purview-account"></a>Purview 계정 시작

* Purview 계정을 시작하려면 Azure Portal에서 Purview 계정으로 이동하고, 시작하려는 계정을 선택하고, 계정을 시작합니다.

  :::image type="content" source="./media/use-purview-studio/launch-from-portal.png" alt-text="Azure Purview 계정 카탈로그를 시작하기 위한 선택 항목의 스크린샷":::

* Purview 계정을 시작하는 또 다른 방법은 `https://web.purview.azure.com`으로 이동하고 **Azure Active Directory** 및 계정 이름을 선택하여 계정을 시작하는 것입니다.

## <a name="home-page"></a>홈 페이지

**홈** 은 Azure Purview 클라이언트의 시작 페이지입니다.

:::image type="content" source="./media/use-purview-studio/purview-homepage.png" alt-text="홈페이지의 스크린샷":::

다음 목록에서는 **홈페이지** 의 주요 기능을 요약합니다. 목록의 각 숫자는 이전 스크린샷에서 강조 표시된 숫자에 해당합니다.

1. 카탈로그의 식별 이름입니다. **관리 센터** > **계정 정보** 에서 카탈로그 이름을 설정할 수 있습니다.

2. 카탈로그 분석에는 다음의 수가 표시됩니다.

   * 사용자, 그룹, 애플리케이션
   * 데이터 원본
   * 자산
   * 용어집 용어

3. 검색 상자를 사용하여 데이터 카탈로그에서 데이터 자산을 검색할 수 있습니다.

4. 빠른 실행 단추를 통해 애플리케이션의 자주 사용되는 기능에 액세스할 수 있습니다. 표시되는 단추는 사용자 계정에 할당된 역할에 따라 다릅니다.

   * ‘데이터 큐레이터’에 대한 단추는 **지식 센터**, **자산 찾아보기**, **용어집 관리**, **인사이트 보기** 입니다.
   * ‘데이터 읽기 권한자’에 대한 기능 단추는 **지식 센터**, **자산 찾아보기**, **용어집 보기**, **인사이트 보기** 입니다.
   * ‘데이터 원본 관리자’ + ’데이터 큐레이터’에 대한 기능 단추는 **지식 센터**, **데이터 원본 등록**, **자산 찾아보기**, **용어집 관리** 입니다. 
   * ‘데이터 원본 관리자’ + ’데이터 읽기 권한자’에 대한 기능 단추는 **지식 센터**, **데이터 원본 등록**, **자산 찾아보기**, **용어집 보기** 입니다. 

5. 왼쪽 탐색 모음을 사용하여 애플리케이션의 기본 페이지를 찾을 수 있습니다. 표시되는 단추는 사용자 계정에 할당된 역할에 따라 다릅니다.

   * ‘데이터 큐레이터’에 대한 단추는 **홈**, **용어집**, **인사이트**, **관리 센터** 입니다.
   * ‘데이터 읽기 권한자’에 대한 단추는 **홈**, **용어집**, **인사이트**, **관리 센터** 입니다.
   * ‘데이터 원본 관리자’ 또는 ‘데이터 큐레이터/읽기 권한자’를 위한 단추는 **홈**, **원본**, **용어집**, **인사이트**, **관리 센터** 입니다. 
  
6. **최근에 액세스한 항목** 탭에는 최근에 액세스한 데이터 자산 목록이 표시됩니다. 자산 액세스에 관한 자세한 내용은 [Data Catalog 검색](how-to-search-catalog.md) 및 [자산 유형별 찾아보기](how-to-browse-catalog.md#browse-experience)를 참조하세요.  **내 항목** 탭은 로그온한 사용자가 소유한 데이터 자산 목록입니다.
7. **유용한 링크** 에는 지역 상태, 설명서, 가격 책정, 개요, Purview 상태의 링크가 포함됩니다.
8. 위쪽 탐색 모음에는 릴리스 정보/업데이트, Purview 계정 변경, 알림, 도움말, 피드백 섹션에 관한 정보가 포함됩니다.

## <a name="knowledge-center"></a>지식 센터

지식 센터에서는 Purview와 관련된 모든 비디오 및 자습서를 찾을 수 있습니다.

## <a name="guided-tours"></a>둘러보기

Azure Purview Studio의 각 UX에는 페이지의 개요를 제공하는 둘러보기가 있습니다. 둘러보기를 시작하려면 위쪽 표시줄에서 **도움말** 을 선택하고 **둘러보기** 를 선택합니다.

:::image type="content" source="./media/use-purview-studio/guided-tour.png" alt-text="둘러보기의 스크린샷":::

> [!Important]
> ‘데이터 원본 관리자’ 역할 자체는 Purview Studio에 액세스할 수 없습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [보안 주체 추가](tutorial-scan-data.md)
