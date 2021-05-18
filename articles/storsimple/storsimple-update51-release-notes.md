---
title: StorSimple 8000 시리즈 업데이트 5.1 릴리스 정보
description: StorSimple 8000 시리즈 업데이트 5.1의 새로운 기능, 문제 및 해결 방법을 설명합니다.
author: alkohli
ms.assetid: ''
ms.service: storsimple
ms.topic: conceptual
ms.date: 03/18/2021
ms.author: alkohli
ms.openlocfilehash: cdb971851ba678ce18f5a1c7954e5620740f3a4c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104657572"
---
# <a name="storsimple-8000-series-update-51-release-notes"></a>StorSimple 8000 시리즈 업데이트 5.1 릴리스 정보

## <a name="overview"></a>개요

다음 릴리스 정보는 새로운 기능에 대해 설명하고 StorSimple 8000 시리즈 업데이트 5.1에 대한 중요한 미해결 문제를 식별합니다. 또한 이 릴리스에 포함된 StorSimple 소프트웨어 업데이트 목록을 포함합니다.

업데이트 5.1은 업데이트 5를 실행하는 모든 StorSimple 디바이스에 적용할 수 있습니다. 업데이트 5 미만 버전을 사용하는 경우 먼저 업데이트 5를 적용한 다음 5.1을 적용합니다. 업데이트 5.1에 연결된 디바이스 버전은 6.3.9600.17885입니다.

StorSimple 솔루션에 업데이트를 배포하기 전에 릴리스 정보에 포함된 정보를 검토하세요.

> [!IMPORTANT]
>
> * 업데이트 5.1은 필수 업데이트로, 즉시 설치해야 합니다. 자세한 내용은 [업데이트 5.1 적용](storsimple-8000-install-update-51.md) 방법을 참조하세요.
> * 업데이트 5.1에는 보안 업데이트만 있습니다. 이 업데이트를 설치하는 데 30분 정도 걸립니다. 디바이스에서의 작업을 보장하기 위해 업데이트 5.1을 적용하는 것이 좋습니다.
> * 새 릴리스의 경우, 업데이트의 단계적 롤아웃을 수행하기 때문에 즉시 업데이트를 볼 수는 없습니다. 이러한 업데이트가 곧 제공될 예정이니 몇 일 후에 업데이트를 다시 검색하세요.

## <a name="whats-new-in-update-51"></a>업데이트 5.1의 새로운 기능

업데이트 5.1에는 다음과 같은 주요 향상 기능 및 버그 수정이 포함되어 있습니다.

* **TLS 1.2** - 이 StorSimple 업데이트는 모든 클라이언트에 TLS 1.2를 적용합니다. 이는 모든 StorSimple 8000 시리즈 디바이스에 대한 필수 업데이트입니다.

   다음 경고가 표시되는 경우 계속하기 전에 디바이스에서 소프트웨어를 업데이트해야 합니다.

   하나 이상의 StorSimple 디바이스에서 이전 버전의 소프트웨어를 실행하고 있습니다. TLS 1.2에 대해 사용 가능한 최신 업데이트는 필수 업데이트이며, 이러한 디바이스에 즉시 설치되어야 합니다. TLS 1.2는 모든 Azure Portal 통신에 사용되며,이 업데이트 없이 디바이스는 StorSimple 서비스와 통신할 수 없습니다.

## <a name="known-issues-in-update-51-from-previous-releases"></a>업데이트 5.1의 알려진 이전 릴리스 문제

업데이트 5.1에 알려진 새로운 문제는 없습니다. 이전 릴리스에서 업데이트 5.1로 이어지는 문제 목록은 [업데이트 3 릴리스 정보](storsimple-update3-release-notes.md#known-issues-in-update-3)로 이동합니다.

## <a name="storsimple-cloud-appliance-updates-in-update-51"></a>업데이트 5.1의 StorSimple 클라우드 어플라이언스 업데이트

이 업데이트는 StorSimple 클라우드 어플라이언스에 적용할 수 없습니다(가상 디바이스라고도 함). 새 클라우드 어플라이언스는 업데이트 5.1 이미지를 사용하여 만들어야 합니다. StorSimple Cloud Appliance를 만드는 방법에 대한 자세한 내용은 [StorSimple Cloud Appliance 배포 및 관리](storsimple-8000-cloud-appliance-u2.md)로 이동합니다.

## <a name="next-step"></a>다음 단계

StorSimple 디바이스에 [업데이트 5.1을 설치](storsimple-8000-install-update-51.md)하는 방법을 알아봅니다.
