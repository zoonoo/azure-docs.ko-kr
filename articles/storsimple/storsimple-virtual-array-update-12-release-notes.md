---
title: Microsoft Azure StorSimple 가상 배열 업데이트 1.2 릴리스 정보 | Microsoft Docs
description: 업데이트 1.2를 실행 하는 StorSimple 가상 배열에 대 한 중요 한 미해결 문제 및 해결책을 설명 합니다.
services: storsimple
author: alkohli
ms.service: storsimple
ms.topic: article
ms.date: 05/29/2019
ms.author: alkohli
ms.openlocfilehash: ea7e4801dfaad533403c0f927a03735ae409cc52
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66420605"
---
# <a name="storsimple-virtual-array-update-12-release-notes"></a>StorSimple 가상 배열 업데이트 1.2 릴리스 정보

다음 릴리스 정보는 Microsoft Azure StorSimple 가상 배열 업데이트에 대한 중요한 미해결 문제 및 해결된 문제를 식별합니다.

릴리스 정보를 지속적으로 업데이트 됩니다. 해결 방법이 필요한 중요 한 문제가 발견 되 면 추가 됩니다. StorSimple 가상 배열을 배포하기 전에 릴리스 정보에 포함된 정보를 주의 깊게 검토하세요.

소프트웨어 버전에 해당 하는 업데이트 1.2 **10.0.10311.0**합니다.

> [!IMPORTANT]
> - 업데이트는 작업 중단 업데이트이며 디바이스를 다시 시작합니다. I/O가 진행 중인 경우 디바이스에 가동 중지 시간이 발생합니다. 이 업데이트를 적용 하는 데 사용 되는 패키지에 대 한 자세한 내용은 이동 [업데이트 1.2 다운로드](#download-update-12)합니다.
> - 업데이트 1.0 또는 1.1 장치가 실행 하는 경우에 업데이트 1.2를 Azure portal을 통해 사용할 수 있습니다.

## <a name="whats-new-in-update-12"></a>업데이트 1.2의 새로운 기능

이 업데이트는 다음 버그 수정을 포함합니다.

- 향상 된 복원 력 처리할 때 파일을 삭제 합니다.
- 이어지는 코드 시작 경로에서 향상 된 처리 예외 백업, 복원, 클라우드 읽기 오류 줄어들고 공간 확보를 자동화 합니다.

## <a name="download-update-12"></a>업데이트 1.2를 다운로드 합니다.

이 업데이트를 다운로드 하려면로 이동 합니다 [Microsoft Update 카탈로그](https://www.catalog.update.microsoft.com/Home.aspx) 서버 및 다운로드는 KB4502035 패키지 있습니다. 이 패키지를 다음 패키지가 포함 되어 있습니다.

 - **KB4493446** 2019 년 4 월까지 2012 R2에 대 한 누적 Windows 업데이트를 포함 하는 합니다. 이 롤업에 포함 된 내용에 대 한 자세한 내용은 이동 [월 월간 보안 롤업](https://support.microsoft.com/help/4493446/windows-8-1-update-kb4493446)합니다.
 - **KB3011067** 파일인 Microsoft 업데이트 독립 실행형 패키지 WindowsTH-KB3011067-x64. 이 파일은 디바이스 소프트웨어를 업데이트하는 데 사용됩니다.

KB4502035를 다운로드 하 고 다음이 지침에 따라 [로컬 웹 UI 통해 업데이트를 적용](storsimple-virtual-array-install-update-11.md#use-the-local-web-ui)합니다.

## <a name="issues-fixed-in-update-12"></a>업데이트 1.2에서 해결된 문제

다음 표에서는 이 릴리스에서 수정된 문제를 간략하게 설명합니다.

| 아니요. | 기능 | 문제 |
| --- | --- | --- |
| 1 |삭제| 이전 버전의 소프트웨어에서 문제가 발생 했습니다 장치의 사용 파일 삭제 된 경우에 변경 되지 않았습니다. 이 문제는이 버전에서 해결 됩니다. 삭제 된 파일을 처리할 때 계층화 코드 경로 복원 력이 향상 했습니다.|
| 2 |예외 처리| 소프트웨어의 이전 버전에서는 백업, 복원, 클라우드에서 읽기 실패를 초래할 수와 공간 확보를 자동화 하는 시스템 재부팅 후 문제가 발생 했습니다. 이 릴리스 시작 경로에서 예외를 처리 하는 방법에 대 한 변경 내용을 포함 합니다.|

## <a name="known-issues-in-update-12"></a>업데이트 1.2의 알려진 문제

새로운 문제가 업데이트 1.2의 릴리스 언급 했습니다. 모든 릴리스 언급 된 문제는 이전 릴리스에서 전달 됩니다. 이전 릴리스에서 포함 하는 알려진 문제의 요약을 확인 하려면로 이동 [업데이트 1.1의 알려진 문제](storsimple-virtual-array-update-11-release-notes.md#known-issues-in-update-11)합니다.

## <a name="next-steps"></a>다음 단계

KB4502035를 다운로드 하 고 [로컬 웹 UI 통해 업데이트를 적용](storsimple-virtual-array-install-update-11.md#use-the-local-web-ui)합니다.

## <a name="references"></a>참조

이전 릴리스 정보를 찾으시나요? 다음으로 이동합니다.
* [StorSimple 가상 배열 업데이트 1.1 릴리스 정보](storsimple-virtual-array-update-11-release-notes.md)
* [StorSimple 가상 배열 업데이트 1.0 릴리스 정보](storsimple-virtual-array-update-1-release-notes.md)
* [StorSimple 가상 배열 업데이트 0.6 릴리스 정보](storsimple-virtual-array-update-06-release-notes.md)
* [StorSimple 가상 배열 업데이트 0.5 릴리스 정보](storsimple-virtual-array-update-05-release-notes.md)
* [StorSimple 가상 배열 업데이트 0.4 릴리스 정보](storsimple-virtual-array-update-04-release-notes.md)
* [StorSimple 가상 배열 업데이트 0.3 릴리스 정보](storsimple-ova-update-03-release-notes.md)
* [StorSimple 가상 배열 업데이트 0.1 및 0.2 릴리스 정보](storsimple-ova-update-01-release-notes.md)
* [StorSimple 가상 배열 일반 공급 릴리스 정보](storsimple-ova-pp-release-notes.md)
