---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 02/12/2020
ms.author: mimart
ms.openlocfilehash: d43b879057001d62ea72bd2e011ad52957d47470
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/25/2020
ms.locfileid: "95990907"
---
## <a name="sample-templates"></a>샘플 템플릿
UI 사용자 지정을 위한 샘플 템플릿은 다음에서 찾을 수 있습니다.

```bash
git clone https://github.com/Azure-Samples/Azure-AD-B2C-page-templates
```

이 프로젝트에는 다음 템플릿이 포함 되어 있습니다.
- [바다색](https://github.com/Azure-Samples/Azure-AD-B2C-page-templates/tree/master/ocean_blue)
- [슬레이트 회색](https://github.com/Azure-Samples/Azure-AD-B2C-page-templates/tree/master/slate_gray)

샘플을 사용 하려면 다음을 수행 합니다.

1. 로컬 컴퓨터에서 리포지토리를 복제 합니다. 템플릿 폴더 또는를 `/ocean_blue` 선택 `/slate_gray` 합니다.
1. `/assets`이전 섹션에 설명 된 대로 템플릿 폴더 및 폴더 아래의 모든 파일을 Blob 저장소에 업로드 합니다.
1. 그런 다음 `\*.html` 또는의 루트에 있는 각 파일 `/ocean_blue` 을 열고 `/slate_gray` 상대 url의 모든 인스턴스를 2 단계에서 업로드 한 css, 이미지 및 글꼴 파일의 url로 바꿉니다. 예들 들어 다음과 같습니다.
    ```html
    <link href="./css/assets.css" rel="stylesheet" type="text/css" />
    ```

    대상
    ```html
    <link href="https://your-storage-account.blob.core.windows.net/your-container/css/assets.css" rel="stylesheet" type="text/css" />
    ```
1. 파일을 저장 하 `\*.html` 고 Blob 저장소에 업로드 합니다.
1. 이제 앞에서 설명한 것 처럼 HTML 파일을 가리키는 정책을 수정 합니다.
1. 누락 된 글꼴, 이미지 또는 CSS가 표시 되 면 확장 정책 및 .html 파일의 참조를 확인 합니다. \*
