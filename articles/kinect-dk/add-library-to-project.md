---
title: Visual Studio 프로젝트에 Azure Kinect 라이브러리 추가
description: Visual Studio 프로젝트에 Azure Kinect NuGet 패키지를 추가하는 방법을 알아봅니다.
author: wes-b
ms.author: wesbarc
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: kinect, azure, 센서, sdk, visual studio 2017, visual studio 2019, nuget
ms.openlocfilehash: fd71f0d327b8c828cc9ddac5810757cccdffbcea
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "94359615"
---
# <a name="add-azure-kinect-library-to-your-visual-studio-project"></a>Visual Studio 프로젝트에 Azure Kinect 라이브러리 추가

이 문서에서는 Visual Studio 프로젝트에 Azure Kinect NuGet 패키지를 추가하는 프로세스를 안내합니다.

## <a name="install-azure-kinect-nuget-package"></a>Azure Kinect NuGet 패키지 설치

Azure Kinect NuGet 패키지를 설치하려면:

1. [빠른 시작: Visual Studio에서 패키지 설치 및 사용](/nuget/quickstart/install-and-use-a-package-in-visual-studio)에서는 Visual Studio에서 NuGet 패키지를 설치하는 방법에 대한 자세한 지침을 찾을 수 있습니다.
2. 패키지를 추가하려면 참조를 마우스 오른쪽 단추로 클릭하고 솔루션 탐색기에서 NuGet 패키지 관리를 선택하여 패키지 관리자 UI를 사용하면 됩니다.
3. 패키지 원본으로 [nuget.org](https://www.nuget.org)를 선택하고 찾아보기 탭을 선택하여 `Microsoft.Azure.Kinect.Sensor`를 검색합니다.
4. 목록에서 해당 패키지를 선택하고 설치합니다.

## <a name="use-azure-kinect-nuget-package"></a>Azure Kinect NuGet 패키지 사용

패키지를 추가한 후 다음과 같은 헤더 파일 include를 소스 코드에 추가합니다.

- `#include <k4a/k4a.h>`
- `#include <k4arecord/record.h>`
- `#include <k4arecord/playback.h>`

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
>[이제 첫 번째 애플리케이션을 빌드할 준비가 되었습니다.](build-first-app.md)