---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 02/12/2020
ms.author: mimart
ms.openlocfilehash: d43b879057001d62ea72bd2e011ad52957d47470
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78189028"
---
## <a name="sample-templates"></a>샘플 템플릿
UI 사용자 지정을 위한 샘플 템플릿은 다음에서 찾을 수 있습니다.

```bash
git clone https://github.com/Azure-Samples/Azure-AD-B2C-page-templates
```

이 프로젝트에는 다음 템플릿이 포함되어 있습니다.
- [오션 블루](https://github.com/Azure-Samples/Azure-AD-B2C-page-templates/tree/master/ocean_blue)
- [슬레이트 그레이](https://github.com/Azure-Samples/Azure-AD-B2C-page-templates/tree/master/slate_gray)

샘플을 사용하려면 다음을 수행하십시오.

1. 로컬 컴퓨터에서 리포지토리를 복제합니다. 템플릿 폴더 `/ocean_blue` 또는 `/slate_gray`를 선택합니다.
1. 이전 섹션에 설명된 대로 `/assets` 모든 파일을 템플릿 폴더 및 폴더 아래에 있는 Blob 저장소에 업로드합니다.
1. 그런 다음 `\*.html` 상대 URL의 모든 `/ocean_blue` `/slate_gray`인스턴스를 2단계에서 업로드한 css, 이미지 및 글꼴 파일의 URL로 바꾸거나 루트에서 각 파일을 엽니다. 예를 들어:
    ```html
    <link href="./css/assets.css" rel="stylesheet" type="text/css" />
    ```

    수행할 작업
    ```html
    <link href="https://your-storage-account.blob.core.windows.net/your-container/css/assets.css" rel="stylesheet" type="text/css" />
    ```
1. `\*.html` 파일을 저장하고 Blob 저장소에 업로드합니다.
1. 이제 앞에서 설명한 대로 HTML 파일을 가리키는 정책을 수정합니다.
1. 글꼴, 이미지 또는 CSS가 누락된 경우 확장 정책 및 \*.html 파일에서 참조를 확인합니다.
