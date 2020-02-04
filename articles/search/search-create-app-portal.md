---
title: Azure Portal에서 검색 앱 만들기
titleSuffix: Azure Cognitive Search
description: 앱 만들기(미리 보기) 마법사를 실행하여 운영 웹앱에 대한 HTML 페이지와 스크립트를 생성합니다. 이 페이지에는 검색 창, 결과 영역, 사이드바 및 자동 완성 지원이 포함됩니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 12/05/2019
ms.openlocfilehash: 9ed62ea6fdfc0732128ed340cc0bc48481eb73ec
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76841083"
---
# <a name="quickstart-create-a-search-app-in-the-portal-azure-cognitive-search"></a>빠른 시작: 포털에서 검색 앱 만들기(Azure Cognitive Search)

포털의 **검색 앱 만들기** 마법사를 사용하여 브라우저에서 실행되는 다운로드 가능한 "localhost" 스타일 웹앱을 생성합니다. 구성에 따라 생성된 앱은 처음 사용할 때 원격 인덱스에 대한 라이브 연결을 사용하여 작동합니다. 기본 앱에는 검색 창, 결과 영역, 사이드바 필터 및 자동 완성 지원이 포함될 수 있습니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다. 

## <a name="prerequisites"></a>사전 요구 사항

현재 이 빠른 시작을 위해 [최신 버전의 Microsoft Edge](https://www.microsoft.com/edge)로 업그레이드하거나 Google의 Chrome 브라우저를 사용하는 것이 좋습니다.

[Azure Cognitive Search 서비스를 만들거나](search-create-service-portal.md) 현재 구독에서 [기존 서비스를 찾습니다](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices). 이 빠른 시작에서는 체험 서비스를 사용할 수 있습니다. 

애플리케이션의 기반으로 사용할 [인덱스를 만듭니다](search-create-index-portal.md). 

이 빠른 시작에서는 미리 보기 이미지가 있으므로 기본 제공 부동산 샘플 데이터와 인덱스를 사용합니다. 이 연습에 사용되는 인덱스를 만들려면 **데이터 가져오기** 마법사를 실행하여 *realestate-us-sample* 데이터 원본을 선택합니다.

![샘플 데이터에 대한 데이터 원본 페이지](media/search-create-app-portal/import-data-realestate.png)

인덱스를 사용할 준비가 되면 다음 단계로 이동합니다.

## <a name="start-the-wizard"></a>마법사 시작

1. [Azure Portal](https://portal.azure.com)에 로그인하고 [검색 서비스를 찾습니다](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices).

1. [개요] 페이지의 가운데에 있는 링크에서 **인덱스**를 선택합니다. 

1. 기존 인덱스 목록에서 *realestate-us-sample-index*를 선택합니다.

1. 인덱스 페이지의 위쪽에서 **검색 앱 만들기(미리 보기)** 를 선택하여 마법사를 시작합니다.

1. 첫 번째 마법사 페이지에서 **CORS(원본 간 리소스 공유) 사용**을 선택하여 CORS 지원을 인덱스 정의에 추가합니다. 이 단계는 선택 사항이지만, 이를 수행하지 않으면 로컬 웹앱이 원격 인덱스에 연결되지 않습니다.

## <a name="configure-search-results"></a>검색 결과 구성

마법사는 미리 보기 이미지, 제목 및 설명을 위한 공간이 포함된 기본 레이아웃을 렌더링된 검색 결과에 제공합니다. 이러한 각 요소를 지원하는 것은 데이터를 제공하는 인덱스의 필드입니다. 

1. [미리 보기]의 *realestate-us-sample* 인덱스에서 *thumbnail* 필드를 선택합니다. 이 샘플은 *thumbnail*이라는 필드에 저장된 URL 주소 이미지 형태의 이미지 미리 보기를 포함하고 있습니다. 인덱스에 이미지가 없으면 이 필드를 비워 둡니다.

1. [제목]에서 각 문서의 고유성을 나타내는 필드를 선택합니다. 이 샘플에서는 목록 ID가 적절한 선택입니다.

1. [설명]에서 다른 사용자가 특정 문서를 클릭할지 여부를 결정하는 데 도움이 될 수 있는 세부 정보를 제공하는 필드를 선택합니다.

![샘플 데이터에 대한 데이터 원본 페이지](media/search-create-app-portal/configure-results.png)

## <a name="add-a-sidebar"></a>사이드바 추가

검색 서비스는 사이드바로 자주 렌더링되는 패싯 탐색을 지원합니다. 패싯은 인덱스 스키마에서 표시한 대로 필터링 가능 및 패싯 가능 필드를 기반으로 합니다.

Azure Cognitive Search에서 패싯 탐색은 누적 필터링 환경입니다. 범주 내에서 여러 필터를 선택하면 결과가 확장됩니다(예: 도시 내에서 시애틀 및 벨뷰 선택). 여러 범주에서 여러 필터를 선택하면 결과가 좁혀집니다.

> [!TIP]
> 전체 인덱스 스키마는 포털에서 볼 수 있습니다. 각 인덱스의 개요 페이지에서 **인덱스 정의(JSON)** 링크를 찾습니다. 패싯 탐색에 적합한 필드에는 "filterable: true" 및 "facetable: true" 특성이 있습니다.

현재 선택된 패싯을 그대로 적용하고 다음 페이지로 이동합니다.


## <a name="add-typeahead"></a>자동 완성 추가

자동 완성 및 쿼리 제안 형식으로 미리 구성된 기능을 사용할 수 있습니다. 마법사는 쿼리 제안을 지원합니다. 사용자가 제공하는 키 입력에 따라 검색 서비스에서 입력으로 선택할 수 있는 "완성된" 쿼리 문자열 목록을 반환합니다.

제안은 특정 필드 정의에서 사용하도록 설정됩니다. 마법사는 제안에 포함된 정보의 양을 구성하는 옵션을 제공합니다. 

다음 스크린샷에서는 앱에서 렌더링된 페이지와 함께 마법사의 옵션을 보여 줍니다. 필드 선택이 사용되는 방법 및 "필드 이름 표시"가 제안 내에서 레이블 지정을 포함하거나 제외하는 데 사용되는 방법을 확인할 수 있습니다.

![쿼리 제안 구성](media/search-create-app-portal/suggestions.png)

## <a name="create-download-and-execute"></a>만들기, 다운로드 및 실행

1. **검색 앱 만들기**를 선택하여 HTML 파일을 생성합니다.

1. 메시지가 표시되면 **앱 다운로드**를 선택하여 파일을 다운로드합니다.

1. 파일을 엽니다. 다음 스크린샷과 비슷한 페이지가 표시됩니다. 용어를 입력하고 필터를 사용하여 결과를 좁힙니다. 

기본 인덱스는 문서 간에 중복된 가상의 생성된 데이터로 구성되며, 설명이 이미지와 일치하지 않는 경우도 있습니다. 사용자 고유의 인덱스를 기반으로 하여 앱을 만드는 경우 더 응집력 있는 환경을 사용할 수 있습니다.

![앱 실행](media/search-create-app-portal/run-app.png)


## <a name="clean-up-resources"></a>리소스 정리

본인 소유의 구독으로 이 모듈을 진행하고 있는 경우에는 프로젝트가 끝날 때 여기서 만든 리소스가 계속 필요한지 확인하는 것이 좋습니다. 계속 실행되는 리소스에는 요금이 부과될 수 있습니다. 리소스를 개별적으로 삭제하거나 리소스 그룹을 삭제하여 전체 리소스 세트를 삭제할 수 있습니다.

왼쪽 탐색 창의 **모든 리소스** 또는 **리소스 그룹** 링크를 사용하여 포털에서 리소스를 찾고 관리할 수 있습니다.

무료 서비스를 사용하는 경우 인덱스, 인덱서, 데이터 원본 세 개로 제한됩니다. 포털에서 개별 항목을 삭제하여 제한 이하로 유지할 수 있습니다. 

## <a name="next-steps"></a>다음 단계

기본 앱은 초기 검색 및 작은 작업에 유용하지만, 초기에 API를 검토하면 개념과 워크플로를 더 심층적으로 이해하는 데 도움이 됩니다.

> [!div class="nextstepaction"]
> [.NET SDK를 사용하여 인덱스 만들기](https://docs.microsoft.com/azure/search/search-create-index-dotnet)