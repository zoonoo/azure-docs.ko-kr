---
title: C + +/WinRT HoloLens 응용 프로그램에 대 한 Azure 공간 앵커 설치
description: Azure 공간 앵커를 사용 하도록 c + +/WinRT HoloLens 프로젝트 구성
author: craigktreasure
manager: vriveras
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: how-to
ms.service: azure-spatial-anchors
ms.openlocfilehash: 43d5c1ae03c359dcbef21f8e7ba3205bc6ab0004
ms.sourcegitcommit: 93329b2fcdb9b4091dbd632ee031801f74beb05b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2020
ms.locfileid: "92096116"
---
# <a name="configuring-azure-spatial-anchors-in-a-cwinrt-hololens-project"></a>C + +/WinRT HoloLens 프로젝트에서 Azure 공간 앵커 구성

## <a name="requirements"></a>요구 사항

* [Visual Studio 2019](https://www.visualstudio.com/downloads/) 은 **유니버설 Windows 플랫폼 개발** 워크 로드 및 **Windows 10 SDK (10.0.18362.0 이상)** 구성 요소와 함께 설치 됩니다.

## <a name="configuring-a-project"></a>프로젝트 구성

HoloLens 및 c + +/WinRT 용 Azure 공간 앵커는 [SpatialAnchors](https://www.nuget.org/packages/Microsoft.Azure.SpatialAnchors.WinRT/) NuGet 패키지를 사용 하 여 배포 됩니다.

Visual Studio의 NuGet 패키지 관리자를 사용 하 여 [SpatialAnchors](https://www.nuget.org/packages/Microsoft.Azure.SpatialAnchors.WinRT/) NuGet 패키지를 프로젝트에 설치 하려면 [여기](/nuget/consume-packages/install-use-packages-visual-studio) 의 지침을 따르세요.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [방법: c + +/WinRT에서 앵커 만들기 및 찾기](./create-locate-anchors-cpp-winrt.md)