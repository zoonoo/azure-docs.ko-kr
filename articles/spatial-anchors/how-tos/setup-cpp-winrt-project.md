---
title: C++/WinRT HoloLens 애플리케이션용 Azure Spatial Anchors 설치
description: Azure Spatial Anchors를 사용하도록 C++/WinRT HoloLens 프로젝트 구성
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 11/20/2020
ms.topic: how-to
ms.service: azure-spatial-anchors
ms.openlocfilehash: 69134ef87d90fe69de2d9e4e9222e90f65edc785
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "95507009"
---
# <a name="configuring-azure-spatial-anchors-in-a-cwinrt-hololens-project"></a>C++/WinRT HoloLens 프로젝트에서 Azure Spatial Anchors 구성

## <a name="requirements"></a>요구 사항

* **유니버설 Windows 플랫폼 개발** 워크로드 및 **Windows 10 SDK(10.0.18362.0 이상)** 구성 요소로 설치된 [Visual Studio 2019](https://www.visualstudio.com/downloads/)

## <a name="configuring-a-project"></a>프로젝트 구성

HoloLens 및 C++/WinRT용 Azure Spatial Anchors는 [Microsoft.Azure.SpatialAnchors.WinRT](https://www.nuget.org/packages/Microsoft.Azure.SpatialAnchors.WinRT/) NuGet 패키지를 사용하여 배포됩니다.

Visual Studio의 NuGet 패키지 관리자를 사용하여 [Microsoft.Azure.SpatialAnchors.WinRT](https://www.nuget.org/packages/Microsoft.Azure.SpatialAnchors.WinRT/) NuGet 패키지를 프로젝트에 설치하려면 [여기](/nuget/consume-packages/install-use-packages-visual-studio)의 지침을 따릅니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [방법: C++/WinRT에서 앵커 만들기 및 찾기](./create-locate-anchors-cpp-winrt.md)