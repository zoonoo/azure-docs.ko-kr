---
author: msftradford
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 2/3/21
ms.author: parkerra
ms.openlocfilehash: f6c2780ccbb914228a9870cb3b5fe4b0e3d0b214
ms.sourcegitcommit: f82e290076298b25a85e979a101753f9f16b720c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/04/2021
ms.locfileid: "99569511"
---
필요한 패키지를 다운로드하려면 <a href="https://www.npmjs.com/get-npm" target="_blank">NPM</a>이 설치되어 있어야 합니다.

다음 명령을 실행하여 `<version_number>`를 현재 폴더에 다운로드할 Azure Spatial Anchors 버전으로 바꿉니다.

```bash
npm pack com.microsoft.azure.spatial-anchors-sdk.core@<version_number> --registry https://api.bintray.com/npm/microsoft/AzureMixedReality-NPM
```

> [!NOTE]
> 사용 가능한 Azure Spatial Anchors 패키지 버전을 나열하려면 다음을 실행합니다.
>
> ```bash
> npm view com.microsoft.azure.spatial-anchors-sdk.core --registry https://api.bintray.com/npm/microsoft/AzureMixedReality-NPM versions
> ```

Azure Spatial Anchors 코어 패키지는 명령을 실행한 폴더에 다운로드됩니다.

이 단계를 반복하여 프로젝트에서 지원하려는 각 플랫폼(Android/iOS/HoloLens)의 패키지를 다운로드합니다.

| 플랫폼 | 패키지 이름                                    |
|----------|-------------------------------------------------|
| Android  | com.microsoft.azure.spatial-anchors-sdk.android@<version_number> |
| iOS      | com.microsoft.azure.spatial-anchors-sdk.ios@<version_number>     |
| HoloLens | com.microsoft.azure.spatial-anchors-sdk.windows@<version_number> |