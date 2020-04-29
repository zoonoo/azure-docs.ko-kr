---
title: Unity용 Remote Rendering 패키지 설치
description: Unity 용 원격 렌더링 클라이언트 Dll을 설치 하는 방법에 대해 설명 합니다.
author: florianborn71
ms.author: flborn
ms.date: 02/26/2020
ms.topic: how-to
ms.openlocfilehash: 86ffe47c009f9e0ae121ffc6ab57bff8fb73210f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80681182"
---
# <a name="install-the-remote-rendering-package-for-unity"></a>Unity용 Remote Rendering 패키지 설치

Azure 원격 렌더링은 unity 패키지를 사용 하 여 통합을 Unity로 캡슐화 합니다.

## <a name="manage-the-remote-rendering-packages-in-unity"></a>Unity에서 원격 렌더링 패키지 관리

Unity 패키지는 Unity의 [패키지 관리자](https://docs.unity3d.com/Packages/com.unity.package-manager-ui@1.8/manual/index.html)를 통해 관리할 수 있는 컨테이너입니다.
이 패키지에는 전체 c # API 뿐만 아니라 Unity와 함께 Azure 원격 렌더링을 사용 하는 데 필요한 모든 플러그 인 이진 파일이 포함 되어 있습니다.
패키지에 대 한 Unity 이름 지정 체계를 따라 패키지를 **com.**

패키지는 [ARR 샘플 리포지토리의](https://github.com/Azure/azure-remote-rendering)일부가 아니므로 Unity의 내부 패키지 레지스트리에서 사용할 수 없습니다. 프로젝트에 추가 하려면 프로젝트의 `manifest.md` 파일을 수동으로 편집 하 여 다음을 추가 해야 합니다.
```json
{
  "scopedRegistries": [
    {
      "name": "Azure Mixed Reality Services",
      "url": "https://api.bintray.com/npm/microsoft/AzureMixedReality-NPM/",
      "scopes": ["com.microsoft.azure"]
    }
   ],
  "dependencies": {
    "com.microsoft.azure.remote-rendering": "0.1.11",
    ...existing dependencies...
  }
}
```
이를 추가한 후에는 Unity 패키지 관리자를 사용 하 여 최신 버전이 있는지 확인할 수 있습니다.
[자습서: Unity 프로젝트를 처음부터 설정](../../tutorials/unity/project-setup.md)하는 방법에 대 한 더 포괄적인 지침이 제공 됩니다.

## <a name="unity-render-pipelines"></a>Unity 렌더링 파이프라인

원격 렌더링은 **유니버설 렌더링 파이프라인과** **표준 렌더링 파이프라인**모두에서 작동 합니다. 성능상의 이유로 유니버설 렌더링 파이프라인을 권장 합니다.

**유니버설 렌더링 파이프라인**을 사용 하려면 해당 패키지를 Unity에 설치 해야 합니다. Unity [프로젝트 설정 자습서](../../tutorials/unity/project-setup.md#configure-the-projects-manifest)에 설명 된 대로 Unity의 **패키지 관리자** UI (패키지 이름 **유니버설 RP**, 버전 7.2.1 이상) 또는 `Packages/manifest.json` 파일을 통해이 작업을 수행할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [Unity 게임 개체 및 구성 요소](objects-components.md)
* [자습서: Unity 프로젝트를 처음부터 설정](../../tutorials/unity/project-setup.md)
