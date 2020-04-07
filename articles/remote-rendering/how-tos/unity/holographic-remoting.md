---
title: 유니티에서 홀로그램 원격 및 원격 렌더링 사용
description: 홀로그램 원격 미리 보기를 Azure 원격 렌더링과 함께 사용할 수 있는 방법
author: christophermanthei
ms.author: chmant
ms.date: 03/23/2020
ms.topic: how-to
ms.openlocfilehash: ac47a2922e92233f0acabf75817a712671306bc1
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681208"
---
# <a name="use-holographic-remoting-and-remote-rendering-in-unity"></a>유니티에서 홀로그램 원격 및 원격 렌더링 사용

[홀로그램 원격 및](https://docs.microsoft.com/windows/mixed-reality/holographic-remoting-player) Azure 원격 렌더링은 하나의 응용 프로그램 내에서 상호 배타적입니다. 따라서 [Unity 플레이 모드도](https://docs.microsoft.com/windows/mixed-reality/unity-play-mode) 사용할 수 없습니다.

Unity 에디터를 실행할 때마다 둘 중 하나만 사용할 수 있습니다. 다른 하나를 사용하려면 먼저 Unity를 다시 시작합니다.

## <a name="use-unity-play-mode-to-preview-on-hololens-2"></a>유니티 플레이 모드를 사용하여 홀로렌즈 2에서 미리 보기

 Unity 재생 모드는 응용 프로그램의 UI를 테스트하는 등 계속 사용할 수 있습니다. 그러나 ARR이 초기화되지 않는 것이 중요합니다. 그렇지 않으면 충돌합니다.

## <a name="use-a-wmr-vr-headset-to-preview-on-desktop"></a>WMR VR 헤드셋을 사용하여 데스크톱에서 미리 보기

Windows 혼합 현실 VR 헤드셋이 있는 경우 Unity 내부에서 미리 볼 수 있습니다. 이 경우 ARR을 초기화하는 것은 좋지만 WMR 헤드셋을 사용하는 동안세션에 연결할 수 없습니다.
