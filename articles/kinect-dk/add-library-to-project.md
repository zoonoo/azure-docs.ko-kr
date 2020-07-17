---
title: Visual Studio 프로젝트에 Azure Kinect library 추가
description: Visual Studio 프로젝트에 Azure Kinect NuGet 패키지를 추가 하는 방법에 대해 알아봅니다.
author: wes-b
ms.author: wesbarc
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: kinect, azure, 센서, sdk, visual studio 2017, visual studio 2019, nuget
ms.openlocfilehash: b0395118481cbaecd5ad0b6a3a6b3e89cc29dfaf
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85276970"
---
# <a name="add-azure-kinect-library-to-your-visual-studio-project"></a>Visual Studio 프로젝트에 Azure Kinect library 추가

이 문서에서는 Visual Studio 프로젝트에 Azure Kinect NuGet 패키지를 추가 하는 과정을 안내 합니다.

## <a name="install-azure-kinect-nuget-package"></a>Azure Kinect NuGet 패키지 설치

Azure Kinect NuGet 패키지를 설치 하려면 다음을 수행 합니다.

1. [빠른 시작: Visual studio에서 패키지 설치 및 사용](https://docs.microsoft.com/nuget/quickstart/install-and-use-a-package-in-visual-studio)의 visual Studio에서 NuGet 패키지를 설치 하는 방법에 대 한 자세한 지침을 찾을 수 있습니다.
2. 패키지를 추가 하려면 참조를 마우스 오른쪽 단추로 클릭 하 고 솔루션 탐색기에서 NuGet 패키지 관리를 선택 하 여 패키지 관리자 UI를 사용할 수 있습니다.
3. 패키지 원본으로 [nuget.org](https://www.nuget.org) 을 선택 하 고 찾아보기 탭을 선택 하 고을 검색 `Microsoft.Azure.Kinect.Sensor` 합니다.
4. 목록에서 해당 패키지를 선택 하 고 설치 합니다.

## <a name="use-azure-kinect-nuget-package"></a>Azure Kinect NuGet 패키지 사용

패키지를 추가한 후에는 다음과 같은 소스 코드에 헤더 파일을 추가 합니다.

- `#include <k4a/k4a.h>`
- `#include <k4arecord/record.h>`
- `#include <k4arecord/playback.h>`

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
>[이제 첫 번째 응용 프로그램을 빌드할 준비가 되었습니다.](build-first-app.md)
