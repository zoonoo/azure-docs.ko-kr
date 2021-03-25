---
title: Unity용 Remote Rendering 패키지 설치
description: Unity 용 원격 렌더링 클라이언트 Dll을 설치 하는 방법에 대해 설명 합니다.
author: florianborn71
ms.author: flborn
ms.date: 02/26/2020
ms.topic: how-to
ms.openlocfilehash: bfb383a7079e98db1db1f9b5077558c187bcea96
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/24/2021
ms.locfileid: "105047731"
---
# <a name="install-the-remote-rendering-package-for-unity"></a>Unity용 Remote Rendering 패키지 설치

Azure 원격 렌더링은 unity 패키지를 사용 하 여 통합을 Unity로 캡슐화 합니다.
이 패키지에는 전체 c # API 뿐만 아니라 Unity와 함께 Azure 원격 렌더링을 사용 하는 데 필요한 모든 플러그 인 이진 파일이 포함 되어 있습니다.
패키지에 대 한 Unity 이름 지정 체계를 따라 패키지를 **com.**

다음 옵션 중 하나를 선택 하 여 Unity 패키지를 설치할 수 있습니다.

## <a name="install-remote-rendering-package-using-the-mixed-reality-feature-tool"></a>혼합 현실 기능 도구를 사용 하 여 원격 렌더링 패키지 설치

[혼합 현실 기능 도구](/windows/mixed-reality/develop/unity/welcome-to-mr-feature-tool) ([다운로드](https://aka.ms/mrfeaturetool))는 혼합 현실 기능 패키지를 Unity 프로젝트에 통합 하는 데 사용 되는 도구입니다. 패키지는 [ARR 샘플 리포지토리의](https://github.com/Azure/azure-remote-rendering)일부가 아니므로 Unity의 내부 패키지 레지스트리에서 사용할 수 없습니다.

프로젝트에 패키지를 추가 하려면 다음을 수행 해야 합니다.
1. [Mixed Reality 기능 도구 다운로드](https://aka.ms/mrfeaturetool)
1. 도구를 사용 하는 방법에 대 한 [전체 지침](/windows/mixed-reality/develop/unity/welcome-to-mr-feature-tool) 을 따르세요.
1. **검색 기능** 페이지의 **Microsoft Azure 원격 렌더링** 패키지 상자에서 프로젝트에 추가 하려는 패키지의 버전을 선택 합니다.

![Mixed_Reality_feature_tool_package](media/mixed-reality-feature-tool-package.png)

로컬 패키지를 업데이트 하려면 혼합 현실 기능 도구에서 최신 버전을 선택 하 고 설치 하기만 하면 됩니다. 패키지를 업데이트하면 경우에 따라 콘솔 오류가 발생할 수 있습니다. 이 경우 프로젝트를 닫고 다시 열어 보세요.

## <a name="install-remote-rendering-package-manually"></a>수동으로 원격 렌더링 패키지 설치

원격 렌더링 패키지를 수동으로 설치 하려면 다음을 수행 해야 합니다.

1. 에서 NPM의 혼합 현실 패키지에서 패키지를 다운로드 `https://pkgs.dev.azure.com/aipmr/MixedReality-Unity-Packages/_packaging/Unity-packages/npm/registry` 합니다.
    * [NPM](https://www.npmjs.com/get-npm) 를 사용 하 고 다음 명령을 실행 하 여 현재 폴더에 패키지를 다운로드할 수 있습니다.
      ```
      npm pack com.microsoft.azure.remote-rendering --registry https://pkgs.dev.azure.com/aipmr/MixedReality-Unity-Packages/_packaging/Unity-packages/npm/registry
      ```

    * 또는 `Scripts/DownloadUnityPackages.ps1` [azure 원격 렌더링 GitHub 리포지토리에서](https://github.com/Azure/azure-remote-rendering)에서 PowerShell 스크립트를 사용할 수 있습니다.
        * `Scripts/unity_sample_dependencies.json`의 내용을 편집 합니다.
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

        * PowerShell에서 다음 명령을 실행 하 여 제공 된 대상 디렉터리에 패키지를 다운로드 합니다.
          ```
          DownloadUnityPackages.ps1 -DownloadDestDir <destination directory>
          ```

1. Unity의 패키지 관리자를 사용 하 여 [다운로드 한 패키지를 설치](https://docs.unity3d.com/Manual/upm-ui-tarball.html) 합니다.

로컬 패키지를 업데이트 하려면 사용한 각 명령을 다시 실행 하 고 패키지를 다시 가져와야 합니다. 패키지를 업데이트하면 경우에 따라 콘솔 오류가 발생할 수 있습니다. 이 경우 프로젝트를 닫고 다시 열어 보세요.

## <a name="unity-render-pipelines"></a>Unity 렌더링 파이프라인

원격 렌더링은 및 둘 다에서 작동 **:::no-loc text="Universal render pipeline":::** **:::no-loc text="Standard render pipeline":::** 합니다. 성능상의 이유로 유니버설 렌더링 파이프라인을 권장 합니다.

를 사용 하려면 **:::no-loc text="Universal render pipeline":::** 해당 패키지를 Unity에 설치 해야 합니다. Unity   `Packages/manifest.json` [프로젝트 설정 자습서](../../tutorials/unity/view-remote-models/view-remote-models.md#include-the-azure-remote-rendering-package)에 설명 된 대로 unity의 패키지 관리자 UI (패키지 이름 유니버설 RP, 버전 7.3.1 이상) 또는 파일을 통해이 작업을 수행할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [Unity 게임 개체 및 구성 요소](objects-components.md)
* [자습서: 원격 모델 보기](../../tutorials/unity/view-remote-models/view-remote-models.md)