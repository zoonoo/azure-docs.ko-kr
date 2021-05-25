---
title: Unity용 Remote Rendering 패키지 설치
description: Unity용 Remote Rendering 클라이언트 DLL을 설치하는 방법에 대해 설명합니다.
author: florianborn71
ms.author: flborn
ms.date: 02/26/2020
ms.topic: how-to
ms.openlocfilehash: bfb383a7079e98db1db1f9b5077558c187bcea96
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105047731"
---
# <a name="install-the-remote-rendering-package-for-unity"></a>Unity용 Remote Rendering 패키지 설치

Azure Remote Rendering은 Unity 패키지를 사용하여 통합을 Unity로 캡슐화합니다.
이 패키지에는 전체 C# API뿐만 아니라 Unity와 함께 Azure Remote Rendering을 사용하는 데 필요한 모든 플러그 인 이진 파일이 포함되어 있습니다.
패키지에 대한 Unity 이름 지정 체계에 따라 패키지를 **com.microsoft.azure.remote-rendering** 이라고 합니다.

다음 옵션 중 하나를 선택하여 Unity 패키지를 설치할 수 있습니다.

## <a name="install-remote-rendering-package-using-the-mixed-reality-feature-tool"></a>Mixed Reality 기능 도구를 사용하여 Remote Rendering 패키지 설치

[Mixed Reality 기능 도구](/windows/mixed-reality/develop/unity/welcome-to-mr-feature-tool)([다운로드](https://aka.ms/mrfeaturetool))는 Mixed Reality 기능 패키지를 Unity 프로젝트에 통합하는 데 사용되는 도구입니다. 패키지는 [ARR 샘플 리포지토리](https://github.com/Azure/azure-remote-rendering)에 속하지 않으며 Unity의 내부 패키지 레지스트리에서 사용할 수 없습니다.

프로젝트에 패키지를 추가하려면 다음을 수행해야 합니다.
1. [Mixed Reality 기능 도구 다운로드](https://aka.ms/mrfeaturetool)
1. 도구를 사용하는 방법에 대한 [전체 지침](/windows/mixed-reality/develop/unity/welcome-to-mr-feature-tool)을 따르세요.
1. **기능 검색** 페이지에서 **Microsoft Azure Remote Rendering** 패키지의 상자를 선택하고 프로젝트에 추가할 패키지의 버전을 선택합니다.

![Mixed_Reality_feature_tool_package](media/mixed-reality-feature-tool-package.png)

로컬 패키지를 업데이트하려면 Mixed Reality 기능 도구에서 최신 버전을 선택하고 설치하기만 하면 됩니다. 패키지를 업데이트하면 경우에 따라 콘솔 오류가 발생할 수 있습니다. 이 경우 프로젝트를 닫고 다시 열어 보세요.

## <a name="install-remote-rendering-package-manually"></a>수동으로 Remote Rendering 패키지 설치

Remote Rendering 패키지를 수동으로 설치하려면 다음을 수행해야 합니다.

1. `https://pkgs.dev.azure.com/aipmr/MixedReality-Unity-Packages/_packaging/Unity-packages/npm/registry`에 있는 Mixed Reality 패키지 NPM 피드에서 패키지를 다운로드합니다.
    * [NPM](https://www.npmjs.com/get-npm)을 사용해 다음 명령을 실행하여 패키지를 현재 폴더에 다운로드할 수 있습니다.
      ```
      npm pack com.microsoft.azure.remote-rendering --registry https://pkgs.dev.azure.com/aipmr/MixedReality-Unity-Packages/_packaging/Unity-packages/npm/registry
      ```

    * 또는 [azure-remote-rendering GitHub 리포지토리](https://github.com/Azure/azure-remote-rendering)에서 `Scripts/DownloadUnityPackages.ps1`에 있는 PowerShell 스크립트를 사용할 수 있습니다.
        * `Scripts/unity_sample_dependencies.json`의 콘텐츠 편집
          ```json
          {
            "packages": [
              {
                "name": "com.microsoft.azure.remote-rendering", 
                "version": "latest", 
                "registry": "https://pkgs.dev.azure.com/aipmr/MixedReality-Unity-Packages/_packaging/Unity-packages/npm/registry"
              }
            ]
          }
          ```

        * PowerShell에서 다음 명령을 실행하여 제공된 대상 디렉터리에 패키지를 다운로드합니다.
          ```
          DownloadUnityPackages.ps1 -DownloadDestDir <destination directory>
          ```

1. Unity의 패키지 관리자를 사용하여 [다운로드한 패키지를 설치](https://docs.unity3d.com/Manual/upm-ui-tarball.html)합니다.

로컬 패키지를 업데이트하려면 사용한 각 명령을 다시 실행하고 패키지를 다시 가져오면 됩니다. 패키지를 업데이트하면 경우에 따라 콘솔 오류가 발생할 수 있습니다. 이 경우 프로젝트를 닫고 다시 열어 보세요.

## <a name="unity-render-pipelines"></a>Unity 렌더링 파이프라인

Remote Rendering은 **:::no-loc text="Universal render pipeline":::** 및 **:::no-loc text="Standard render pipeline":::** 모두에서 사용할 수 있습니다. 성능상의 이유로 유니버설 렌더링 파이프라인이 권장됩니다.

**:::no-loc text="Universal render pipeline":::** 를 사용하려면 해당 패키지를 Unity에 설치해야 합니다. 이 작업은 Unity의 **패키지 관리자** UI(패키지 이름 **유니버설 RP**, 버전 7.3.1 이상)에서 수행하거나 [Unity 프로젝트 설정 자습서](../../tutorials/unity/view-remote-models/view-remote-models.md#include-the-azure-remote-rendering-package)에 설명된 대로 `Packages/manifest.json` 파일을 통해 수행할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [Unity 게임 개체 및 구성 요소](objects-components.md)
* [자습서: 원격 모델 보기](../../tutorials/unity/view-remote-models/view-remote-models.md)