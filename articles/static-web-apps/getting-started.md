---
title: '빠른 시작: Azure Static Web Apps를 사용하여 첫 번째 정적 웹앱 빌드'
description: 선호하는 프런트 엔드 프레임워크를 사용하여 Azure Static Web Apps 인스턴스를 빌드하는 방법을 알아봅니다.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: quickstart
ms.date: 05/08/2020
ms.author: cshoe
ms.openlocfilehash: 72a76fb513dc6eb008fcf1d1e19ffc33e713cfdc
ms.sourcegitcommit: 223cea58a527270fe60f5e2235f4146aea27af32
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/01/2020
ms.locfileid: "84259255"
---
# <a name="quickstart-building-your-first-static-web-app"></a>빠른 시작: 첫 번째 정적 웹앱 빌드

Azure Static Web Apps는 GitHub 리포지토리에서 앱을 빌드하여 프로덕션 환경에 웹 사이트를 게시합니다. 이 빠른 시작에서는 GitHub 리포지토리에서 선호하는 프런트 엔드 프레임워크를 사용하여 웹 애플리케이션을 빌드합니다.

Azure 구독이 아직 없는 경우 [평가판 계정](https://azure.microsoft.com/free)을 만듭니다.

## <a name="prerequisites"></a>필수 구성 요소

- [GitHub](https://github.com) 계정
- [Azure](https://portal.azure.com) 계정

## <a name="create-a-repository"></a>리포지토리 만들기

이 문서에서는 GitHub 템플릿 리포지토리를 사용하여 새 리포지토리를 쉽게 만들 수 있도록 합니다. 이 템플릿에는 다양한 프론트 엔드 프레임워크로 빌드된 스타터 앱이 있습니다.

# <a name="angular"></a>[Angular](#tab/angular)

- GitHub에 로그인했는지 확인하고 다음 위치로 이동하여 새 리포지토리를 만듭니다.
  - https://github.com/staticwebdev/angular-basic/generate
- 리포지토리 이름을 **my-first-static-web-app**으로 지정합니다.

# <a name="react"></a>[React](#tab/react)

- GitHub에 로그인했는지 확인하고 다음 위치로 이동하여 새 리포지토리를 만듭니다.
  - https://github.com/staticwebdev/react-basic/generate
- 리포지토리 이름을 **my-first-static-web-app**으로 지정합니다.

# <a name="vue"></a>[Vue](#tab/vue)

- GitHub에 로그인했는지 확인하고 다음 위치로 이동하여 새 리포지토리를 만듭니다.
  - https://github.com/staticwebdev/vue-basic/generate
- 리포지토리 이름을 **my-first-static-web-app**으로 지정합니다.

# <a name="no-framework"></a>[프레임워크 없음](#tab/vanilla-javascript)

- GitHub에 로그인했는지 확인하고 다음 위치로 이동하여 새 리포지토리를 만듭니다.
  - https://github.com/staticwebdev/vanilla-basic/generate
- 리포지토리 이름을 **my-first-static-web-app**으로 지정합니다.

> [!NOTE]
> Azure Static Web Apps에는 웹앱을 만들기 위한 하나 이상의 HTML 파일이 필요합니다. 이 단계에서 만드는 리포지토리에는 단일 _index.html_ 파일이 포함됩니다.

---

**템플릿에서 리포지토리 만들기** 단추를 클릭합니다.

:::image type="content" source="media/getting-started/create-template.png" alt-text="템플릿에서 리포지토리 만들기":::

## <a name="create-a-static-web-app"></a>정적 웹앱 만들기

이제 리포지토리가 생성되었으므로 Azure Portal에서 정적 웹앱을 만들 수 있습니다.

- [Azure Portal](https://portal.azure.com)로 이동합니다.
- **리소스 만들기**를 클릭합니다.
- **Static Web Apps**를 검색합니다.
- **Static Web Apps(미리 보기)** 를 클릭합니다.
- **만들기**

### <a name="basics"></a>기본 사항

새 앱을 구성하고 GitHub 리포지토리에 연결하여 시작합니다.

:::image type="content" source="media/getting-started/basics-tab.png" alt-text="기본 사항 탭":::

- _Azure 구독_ 선택
- 새 _리소스 그룹_ 선택 또는 만들기
- 앱 이름을 **my-first-static-web-app**으로 지정합니다.
  - 유효한 문자는 `a-z`(대/소문자 구분 안 함), `0-9`및 `-`입니다.
- 가장 가까운 _지역_을 선택합니다.
- **무료** _SKU_를 선택합니다.
- **GitHub로 로그인** 단추를 클릭하고 GitHub로 인증합니다.

GitHub로 로그인한 다음, 리포지토리 정보를 입력합니다.

:::image type="content" source="media/getting-started/repository-details.png" alt-text="리포지토리 세부 정보":::

- 원하는 _조직_을 선택합니다.
- _리포지토리_ 드롭다운에서 **my-first-web-static-app**을 선택합니다.
- _분기_ 드롭다운에서 **마스터**를 선택합니다.
- **다음: 빌드 >** 단추를 클릭하여 빌드 구성을 편집합니다.

:::image type="content" source="media/getting-started/next-build-button.png" alt-text="다음 빌드 단추":::

### <a name="build"></a>빌드

다음으로, 원하는 프런트 엔드 프레임워크에 특정한 구성 세부 정보를 추가합니다.

# <a name="angular"></a>[Angular](#tab/angular)

- _앱 위치_ 상자에 **/** 를 입력합니다.
- _Api 위치_ 상자에서 기본값을 선택 취소합니다.
- _앱 아티팩트 위치_ 상자에 **dist/angular-basic**을 입력합니다.

# <a name="react"></a>[React](#tab/react)

- _앱 위치_ 상자에 **/** 를 입력합니다.
- _Api 위치_ 상자에서 기본값을 선택 취소합니다.
- _앱 아티팩트 위치_ 상자에 **빌드**를 입력합니다.

# <a name="vue"></a>[Vue](#tab/vue)

- _앱 위치_ 상자에 **/** 를 입력합니다.
- _Api 위치_ 상자에서 기본값을 선택 취소합니다.
- _앱 아티팩트 위치_ 상자에 **dist**를 입력합니다.

# <a name="no-framework"></a>[프레임워크 없음](#tab/vanilla-javascript)

- _앱 위치_ 상자에 **/** 를 입력합니다.
- _Api 위치_ 상자에서 기본값을 선택 취소합니다.
- _앱 아티팩트 위치_ 상자에서 기본값을 선택 취소합니다.

---

**검토 + 만들기** 단추를 클릭합니다.

:::image type="content" source="media/getting-started/review-create.png" alt-text="만들기 단추 검토":::

앱을 만든 후 이러한 값을 변경하려면 [워크플로 파일](github-actions-workflow.md)을 편집하면 됩니다.

### <a name="review--create"></a>검토 + 만들기

요청의 유효성을 검사한 후 애플리케이션을 계속 만들 수 있습니다.

**만들기** 단추를 클릭합니다.

:::image type="content" source="media/getting-started/create-button.png" alt-text="만들기 단추":::

리소스가 생성되면 **리소스로 이동** 단추를 클릭합니다.

:::image type="content" source="media/getting-started/resource-button.png" alt-text="리소스로 이동 단추":::

## <a name="view-the-website"></a>웹 사이트 보기

정적 앱을 배포하는 데는 두 가지 측면이 있습니다. 첫 번째는 앱을 구성하는 기본 Azure 리소스를 프로비저닝합니다. 두 번째는 애플리케이션을 빌드하고 게시하는 GitHub Actions 워크플로입니다.

새 정적 사이트로 이동하려면 먼저 배포 빌드가 실행을 완료해야 합니다.

Static Web Apps 개요 창에는 웹앱과 상호 작용하는 데 도움이 되는 일련의 링크가 표시됩니다.

:::image type="content" source="media/getting-started/overview-window.png" alt-text="개요 창":::

1. "GitHub Actions 실행 상태를 확인하려면 여기를 클릭하세요."라는 배너를 클릭하면 리포지토리에 대해 실행 중인 GitHub Actions로 이동됩니다. 배포 작업이 완료되었는지 확인되면 생성된 URL을 통해 웹 사이트로 이동할 수 있습니다.

2. GitHub Actions 워크플로가 완료되면 _URL_ 링크를 클릭하여 새 탭에서 웹 사이트를 열 수 있습니다.

## <a name="clean-up-resources"></a>리소스 정리

이 애플리케이션을 계속 사용하지 않을 경우 다음 단계를 통해 Azure Static Web App 인스턴스를 삭제할 수 있습니다.

1. [Azure Portal](https://portal.azure.com)을 엽니다.
1. 위쪽 검색 창에서 **my-first-web-static-app**을 검색합니다.
1. 앱 이름을 클릭합니다.
1. **삭제** 단추를 클릭합니다.
1. **예**를 클릭하여 작업을 확인합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [API 추가](add-api.md)
