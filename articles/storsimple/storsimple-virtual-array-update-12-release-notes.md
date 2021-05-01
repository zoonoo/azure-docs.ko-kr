---
title: Microsoft Azure StorSimple 가상 배열 업데이트 1.2 릴리스 정보| Microsoft Docs
description: 업데이트 1.2를 실행하는 StorSimple 가상 배열에 대한 중대한 미해결 문제 및 해결 방법을 설명합니다.
services: storsimple
author: alkohli
ms.service: storsimple
ms.topic: article
ms.date: 05/29/2019
ms.author: alkohli
ms.openlocfilehash: 1118dfcec67a49365f1f6e5e522e98b97694d052
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94960194"
---
# <a name="storsimple-virtual-array-update-12-release-notes"></a>StorSimple 가상 배열 업데이트 1.2 릴리스 정보

다음 릴리스 정보는 Microsoft Azure StorSimple 가상 배열 업데이트에 대한 중요한 미해결 문제 및 해결된 문제를 식별합니다.

릴리스 정보는 지속적으로 업데이트됩니다. 해결 방법이 필요한 중요한 문제가 발견되면 추가됩니다. StorSimple 가상 배열을 배포하기 전에 릴리스 정보에 포함된 정보를 주의 깊게 검토하세요.

업데이트 1.2는 소프트웨어 버전 **10.0.10311.0** 에 해당합니다.

> [!IMPORTANT]
> - 업데이트는 작업 중단 업데이트이며 디바이스를 다시 시작합니다. I/O가 진행 중인 경우 디바이스에 가동 중지 시간이 발생합니다. 이 업데이트를 적용하는 데 사용되는 패키지에 대한 자세한 지침을 보려면 [업데이트 1.2 다운로드](#download-update-12)로 이동하세요.
> - 업데이트 1.2는 디바이스에서 업데이트 1.0 또는 1.1을 실행하는 경우에만 Azure Portal을 통해 사용할 수 있습니다.

## <a name="whats-new-in-update-12"></a>업데이트 1.2의 새로운 기능

이 업데이트는 다음 버그 수정을 포함합니다.

- 삭제된 파일을 처리할 때 복원력이 향상되었습니다.
- 코드 시작 경로의 처리 기능이 향상되어 백업, 복원, 클라우드 읽기 및 자동화된 공간 재사용에 대한 오류를 줄일 수 있습니다.

## <a name="download-update-12"></a>업데이트 다운로드 1.2

이 업데이트를 다운로드하려면 [Microsoft 업데이트 카탈로그](https://www.catalog.update.microsoft.com/Home.aspx) 서버로 이동하여 KB4502035 패키지를 다운로드합니다. 이 패키지에는 다음 패키지가 포함됩니다.

 - 2019년 4월까지 2012 R2용 누적 Windows 업데이트를 포함하는 **KB4493446** 입니다. 이 롤업에 포함된 내용에 대한 자세한 내용은 [4월 월간 보안 롤업](https://support.microsoft.com/help/4493446/windows-8-1-update-kb4493446)으로 이동하세요.
 - Microsoft 업데이트 독립 실행형 패키지 파일인 WindowsTH-KB3011067-x64인 **KB3011067** 입니다. 이 파일은 디바이스 소프트웨어를 업데이트하는 데 사용됩니다.

KB4502035를 다운로드하고 다음 지침에 따라 [로컬 웹 UI를 통해 업데이트를 적용](storsimple-virtual-array-install-update-11.md#use-the-local-web-ui)합니다.

## <a name="issues-fixed-in-update-12"></a>업데이트 1.2에서 해결된 문제

다음 표에서는 이 릴리스에서 수정된 문제를 간략하게 설명합니다.

| 아니요. | 기능 | 문제 |
| --- | --- | --- |
| 1 |삭제| 이전 버전의 소프트웨어에서는 파일이 삭제된 경우에도 디바이스의 사용이 변경되지 않은 경우 문제가 발생했습니다. 이 문제는 이 버전에서 해결되었습니다. 삭제된 파일을 처리할 때 계층화 코드 경로를 더 탄력적으로 만들었습니다.|
| 2 |예외 처리| 이전 버전의 소프트웨어에서는 시스템을 다시 부팅한 후 백업, 복원, 클라우드에서 읽기 및 자동화된 공간 재사용으로 인해 발생할 수 있는 문제가 발생했습니다. 이 릴리스에는 시작 경로에서 예외가 처리된 방법에 대한 변경 내용이 포함되어 있습니다.|

## <a name="known-issues-in-update-12"></a>업데이트 1.2의 알려진 문제

업데이트 1.2에서 발표된 새로운 문제가 없습니다. 발표된 모든 문제는 이전 릴리스에서 넘어온 것입니다. 이전 릴리스에 포함된 알려진 문제에 대한 요약을 보려면 [업데이트 1.1의 알려진 문제](storsimple-virtual-array-update-11-release-notes.md#known-issues-in-update-11)로 이동하세요.

## <a name="next-steps"></a>다음 단계

KB4502035를 다운로드하고 [로컬 웹 UI를 통해 업데이트를 적용](storsimple-virtual-array-install-update-11.md#use-the-local-web-ui)합니다.

## <a name="references"></a>참조

이전 릴리스 정보를 찾으시나요? 다음으로 이동합니다.
* [StorSimple 가상 배열 업데이트 1.1 릴리스 정보](storsimple-virtual-array-update-11-release-notes.md)
* [StorSimple 가상 배열 업데이트 1.0 릴리스 정보](storsimple-virtual-array-update-1-release-notes.md)
* [StorSimple 가상 배열 업데이트 0.6 릴리스 정보](storsimple-virtual-array-update-06-release-notes.md)
* [StorSimple 가상 배열 업데이트 0.5 릴리스 정보](storsimple-virtual-array-update-05-release-notes.md)
* [StorSimple 가상 배열 업데이트 0.4 릴리스 정보](storsimple-virtual-array-update-04-release-notes.md)
* [StorSimple 가상 배열 업데이트 0.3 릴리스 정보](storsimple-ova-update-03-release-notes.md)
* [StorSimple 가상 배열 업데이트 0.1 및 0.2 릴리스 정보](storsimple-ova-update-01-release-notes.md)
* [StorSimple 가상 배열 일반 공급 릴리스 정보](./storsimple-virtual-array-update-06-release-notes.md)