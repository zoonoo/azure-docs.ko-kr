---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 02/12/2020
ms.author: mimart
ms.openlocfilehash: d43b879057001d62ea72bd2e011ad52957d47470
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/29/2020
ms.locfileid: "78189028"
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

1. 로컬 컴퓨터에서 리포지토리를 복제 합니다. `/ocean_blue` 또는 `/slate_gray`템플릿 폴더를 선택 합니다.
1. 이전 섹션에 설명 된 대로 템플릿 폴더 및 `/assets` 폴더 아래의 모든 파일을 Blob 저장소에 업로드 합니다.
1. 다음으로 `/ocean_blue` 또는 `/slate_gray`의 루트에 있는 각 `\*.html` 파일을 열고 상대 Url의 모든 인스턴스를 2 단계에서 업로드 한 css, 이미지 및 글꼴 파일의 Url로 바꿉니다. 예를 들면 다음과 같습니다.
    ```html
    <link href="./css/assets.css" rel="stylesheet" type="text/css" />
    ```

    수행 작업
    ```html
    <link href="https://your-storage-account.blob.core.windows.net/your-container/css/assets.css" rel="stylesheet" type="text/css" />
    ```
1. `\*.html` 파일을 저장 하 고 Blob 저장소에 업로드 합니다.
1. 이제 앞에서 설명한 것 처럼 HTML 파일을 가리키는 정책을 수정 합니다.
1. 누락 된 글꼴, 이미지 또는 CSS가 표시 되 면 확장 정책 및 \*파일에서 참조를 확인 합니다.
