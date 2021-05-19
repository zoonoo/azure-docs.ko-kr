---
title: Unity에서 Holographic 원격 및 Remote Rendering 사용
description: Holographic Remoting 미리 보기를 Azure Remote Rendering과 함께 사용할 수 있는 방법
author: christophermanthei
ms.author: chmant
ms.date: 03/23/2020
ms.topic: how-to
ms.openlocfilehash: bd69710b6e4404a76d3cca385b6c07ea7c1f3f5c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "92201820"
---
# <a name="use-holographic-remoting-and-remote-rendering-in-unity"></a>Unity에서 Holographic 원격 및 Remote Rendering 사용

[Holographic Remoting](/windows/mixed-reality/holographic-remoting-player) 및 Azure Remote Rendering은 하나의 애플리케이션 내에서 상호 배타적입니다. 따라서 [Unity 재생 모드](/windows/mixed-reality/unity-play-mode)도 사용할 수 없습니다.

Unity 편집기를 실행할 때마다 둘 중 하나만 사용할 수 있습니다. 다른 항목을 사용하려면 먼저 Unity를 다시 시작합니다.

## <a name="use-unity-play-mode-to-preview-on-hololens-2"></a>Unity 재생 모드를 사용하여 HoloLens 2에서 미리 보기

 애플리케이션의 UI를 테스트하는 등의 방법으로 Unity 재생 모드를 계속 사용할 수 있습니다. 그러나 ARR을 초기화하지 않는 것이 중요합니다. 그렇지 않으면 충돌합니다.

## <a name="use-a-wmr-vr-headset-to-preview-on-desktop"></a>WMR VR 헤드셋을 사용하여 데스크톱에서 미리 보기

Windows Mixed Reality VR 헤드셋이 있는 경우 이를 사용하여 Unity 내에서 미리 볼 수 있습니다. 이 경우 ARR을 초기화해도 괜찮지만 WMR 헤드셋이 사용되는 동안에는 세션에 연결할 수 없습니다.