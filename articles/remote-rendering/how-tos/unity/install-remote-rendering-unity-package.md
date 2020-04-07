---
title: Unity용 원격 렌더링 패키지 설치
description: Unity용 원격 렌더링 클라이언트 DLL 을 설치하는 방법에 대해 설명합니다.
author: florianborn71
ms.author: flborn
ms.date: 02/26/2020
ms.topic: how-to
ms.openlocfilehash: 86ffe47c009f9e0ae121ffc6ab57bff8fb73210f
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681182"
---
# <a name="install-the-remote-rendering-package-for-unity"></a>Unity용 원격 렌더링 패키지 설치

Azure 원격 렌더링은 Unity 패키지를 사용하여 Unity에 통합을 캡슐화합니다.

## <a name="manage-the-remote-rendering-packages-in-unity"></a>Unity에서 원격 렌더링 패키지 관리

Unity 패키지는 Unity 의 [패키지 관리자를](https://docs.unity3d.com/Packages/com.unity.package-manager-ui@1.8/manual/index.html)통해 관리할 수 있는 컨테이너입니다.
이 패키지에는 전체 C# API와 Unity와 함께 Azure 원격 렌더링을 사용하는 데 필요한 모든 플러그인 바이너리가 포함되어 있습니다.
패키지에 대한 Unity의 명명 체계에 따라 패키지를 **com.microsoft.azure.remote 렌더링이라고**합니다.

패키지는 [ARR 샘플 리포지토리의](https://github.com/Azure/azure-remote-rendering)일부가 아니며 Unity의 내부 패키지 레지스트리에서 사용할 수 없습니다. 프로젝트에 추가하려면 프로젝트의 `manifest.md` 파일을 수동으로 편집하여 다음을 추가해야 합니다.
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
이 추가가 완료되면 Unity 패키지 관리자를 사용하여 최신 버전을 사용할 수 있습니다.
더 포괄적인 지침은 [튜토리얼: 처음부터 Unity 프로젝트 설정에](../../tutorials/unity/project-setup.md)나와 있습니다.

## <a name="unity-render-pipelines"></a>유니티 렌더 파이프라인

원격 렌더링은 **유니버설 렌더 파이프라인과** **표준 렌더 파이프라인**모두에서 작동합니다. 성능상의 이유로 유니버설 렌더 파이프라인을 권장합니다.

**유니버설 렌더 파이프라인을**사용하려면 해당 패키지를 Unity에 설치해야 합니다. 이 작업은 Unity의 패키지 **관리자** UI(패키지 이름 **유니버설 RP,** 버전 7.2.1 이상) 또는 Unity [프로젝트 설정 자습서에](../../tutorials/unity/project-setup.md#configure-the-projects-manifest)설명된 대로 `Packages/manifest.json` 파일을 통해 수행할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [유니티 게임 오브젝트 및 컴포넌트](objects-components.md)
* [자습서: 처음부터 Unity 프로젝트 설정](../../tutorials/unity/project-setup.md)
