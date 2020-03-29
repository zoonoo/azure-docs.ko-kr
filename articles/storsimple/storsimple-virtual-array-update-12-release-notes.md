---
title: 마이크로소프트 Azure StorSimple 가상 배열 업데이트 1.2 릴리스 노트 | 마이크로 소프트 문서
description: 업데이트 1.2를 실행하는 StorSimple 가상 배열에 대한 중요한 열린 문제 및 해결 에 대해 설명합니다.
services: storsimple
author: alkohli
ms.service: storsimple
ms.topic: article
ms.date: 05/29/2019
ms.author: alkohli
ms.openlocfilehash: ea7e4801dfaad533403c0f927a03735ae409cc52
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "66420605"
---
# <a name="storsimple-virtual-array-update-12-release-notes"></a>StorSimple 가상 어레이 업데이트 1.2 릴리스 노트

다음 릴리스 정보는 Microsoft Azure StorSimple 가상 배열 업데이트에 대한 중요한 미해결 문제 및 해결된 문제를 식별합니다.

릴리스 노트는 지속적으로 업데이트됩니다. 해결 방법이 필요한 중요한 문제가 발견되면 추가됩니다. StorSimple 가상 배열을 배포하기 전에 릴리스 정보에 포함된 정보를 주의 깊게 검토하세요.

업데이트 1.2는 소프트웨어 버전 **10.0.10311.0에**해당합니다.

> [!IMPORTANT]
> - 업데이트는 작업 중단 업데이트이며 디바이스를 다시 시작합니다. I/O가 진행 중인 경우 디바이스에 가동 중지 시간이 발생합니다. 이 업데이트를 적용하는 데 사용되는 패키지에 대한 자세한 지침은 [업데이트 다운로드 1.2로 이동하십시오.](#download-update-12)
> - 업데이트 1.2는 장치가 업데이트 1.0 또는 1.1을 실행하는 경우에만 Azure 포털을 통해 사용할 수 있습니다.

## <a name="whats-new-in-update-12"></a>업데이트 1.2의 새로운 기능

이 업데이트에는 다음과 같은 버그 수정 사항이 포함되어 있습니다.

- 삭제된 파일을 처리할 때 복원력을 향상시고 있습니다.
- 코드 시작 경로에서 예외 처리가 개선되어 백업, 복원, 클라우드 읽기 및 자동화된 공간 회수에 대한 실패가 줄어듭니다.

## <a name="download-update-12"></a>업데이트 다운로드 1.2

이 업데이트를 다운로드하려면 [Microsoft 업데이트 카탈로그](https://www.catalog.update.microsoft.com/Home.aspx) 서버로 이동하여 KB4502035 패키지를 다운로드합니다. 이 패키지에는 다음 패키지가 포함되어 있습니다.

 - 2012년 R2의 누적 Windows 업데이트가 포함된 **KB4493446은** 2019년 4월까지입니다. 이 롤업에 포함된 내용에 대한 자세한 내용은 [4월 월별 보안 롤업으로](https://support.microsoft.com/help/4493446/windows-8-1-update-kb4493446)이동하십시오.
 - **KB3011067** 이는 마이크로 소프트 업데이트 독립 실행 형 패키지 파일 윈도우TH-KB3011067-x64입니다. 이 파일은 디바이스 소프트웨어를 업데이트하는 데 사용됩니다.

KB4502035를 다운로드하고 다음 지침을 따라 [로컬 웹 UI를 통해 업데이트 적용.](storsimple-virtual-array-install-update-11.md#use-the-local-web-ui)

## <a name="issues-fixed-in-update-12"></a>업데이트 1.2에서 해결된 문제

다음 표에서는 이 릴리스에서 수정된 문제를 간략하게 설명합니다.

| 아니요. | 기능 | 문제 |
| --- | --- | --- |
| 1 |삭제| 이전 버전의 소프트웨어에서는 파일이 삭제된 경우에도 장치 사용이 변경되지 않는 문제가 발생했습니다. 이 문제는 이 버전에서 해결됩니다. 삭제된 파일을 처리할 때 코드 경로 계층화의 복원력이 향상되었습니다.|
| 2 |예외 처리| 이전 버전의 소프트웨어에서는 시스템 재부팅 후 백업, 복원, 클라우드 읽기 및 자동화된 공간 회수로 이어질 수 있는 문제가 발생했습니다. 이 릴리스에는 시작 경로에서 예외가 처리된 방법에 대한 변경 내용이 포함되어 있습니다.|

## <a name="known-issues-in-update-12"></a>업데이트 1.2의 알려진 문제

업데이트 1.2에서는 새로운 문제가 릴리스되지 않았습니다. 릴리스에서 언급한 모든 문제는 이전 릴리스에서 이월됩니다. 이전 릴리스에서 포함된 알려진 문제의 요약을 보려면 [업데이트 1.1의 알려진 문제로](storsimple-virtual-array-update-11-release-notes.md#known-issues-in-update-11)이동하십시오.

## <a name="next-steps"></a>다음 단계

KB4502035를 다운로드하고 [로컬 웹 UI를 통해 업데이트를 적용합니다.](storsimple-virtual-array-install-update-11.md#use-the-local-web-ui)

## <a name="references"></a>참조

이전 릴리스 정보를 찾으시나요? 다음으로 이동합니다.
* [StorSimple 가상 배열 업데이트 1.1 릴리스 노트](storsimple-virtual-array-update-11-release-notes.md)
* [StorSimple 가상 배열 업데이트 1.0 릴리스 정보](storsimple-virtual-array-update-1-release-notes.md)
* [StorSimple 가상 배열 업데이트 0.6 릴리스 노트](storsimple-virtual-array-update-06-release-notes.md)
* [StorSimple 가상 배열 업데이트 0.5 릴리스 정보](storsimple-virtual-array-update-05-release-notes.md)
* [StorSimple 가상 배열 업데이트 0.4 릴리스 노트](storsimple-virtual-array-update-04-release-notes.md)
* [StorSimple 가상 배열 업데이트 0.3 릴리스 노트](storsimple-ova-update-03-release-notes.md)
* [StorSimple 가상 배열 업데이트 0.1 및 0.2 릴리스 정보](storsimple-ova-update-01-release-notes.md)
* [StorSimple 가상 배열 일반 공급 릴리스 정보](storsimple-ova-pp-release-notes.md)
