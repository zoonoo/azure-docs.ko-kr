---
title: Microsoft Azure StorSimple 가상 배열 업데이트 1.3 릴리스 정보| Microsoft Docs
description: 업데이트 1.3을 실행하는 StorSimple 가상 배열에 대한 중대한 미해결 문제 및 해결 방법을 설명합니다.
ms.service: storsimple
author: v-dalc
ms.topic: article
ms.date: 04/13/2021
ms.author: alkohli
ms.openlocfilehash: 498e3d11d8188850a918c67a9a88643d15c134c5
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107389522"
---
# <a name="storsimple-virtual-array-update-13-release-notes"></a>StorSimple Virtual Array Update 1.3 릴리스 정보

다음 릴리스 정보는 Microsoft Azure StorSimple 가상 배열 업데이트에 대한 중요한 미해결 문제 및 해결된 문제를 식별합니다.

릴리스 정보는 지속적으로 업데이트됩니다. 해결 방법이 필요한 중요한 문제가 발견되면 추가됩니다. StorSimple 가상 배열을 배포하기 전에 릴리스 정보에 포함된 정보를 주의 깊게 검토하세요.

업데이트 1.3은 소프트웨어 버전 10.0.10319.0에 해당합니다.

> [!IMPORTANT]
> - 업데이트 1.3은 중요한 업데이트입니다. 가능한 한 빨리 설치하는 것이 좋습니다.
> - 업데이트 1.2를 실행하는 디바이스에만 업데이트 1.3을 설치할 수 있습니다.
> - 업데이트는 작업 중단 업데이트이며 디바이스를 다시 시작합니다. I/O가 진행 중인 경우 디바이스에 가동 중지 시간이 발생합니다. 이 업데이트를 적용하는 데 사용되는 패키지에 대한 자세한 지침을 보려면 [업데이트 1.3 다운로드](#download-update-13)로 이동하세요.

## <a name="whats-new-in-update-13"></a>업데이트 1.3의 새로운 기능

이 업데이트에는 다음과 같은 향상된 기능이 포함되어 있습니다: KB4540725

- 전송 계층 보안(TLS) 1.2는 필수 업데이트이며 반드시 설치해야 합니다. 이 릴리스에서는 TLS 1.2가 모든 Azure Portal 통신에 대한 표준 프로토콜이 됩니다.
  
   다음 경고가 표시되는 경우, 계속하기 전에 디바이스에서 소프트웨어를 업데이트해야 합니다.

   하나 이상의 StorSimple 디바이스에서 이전 버전의 소프트웨어를 실행하고 있습니다. TLS 1.2에 대해 사용 가능한 최신 업데이트는 필수 업데이트이며, 이러한 디바이스에 즉시 설치되어야 합니다. TLS 1.2는 모든 Azure Portal 통신에 사용되며,이 업데이트 없이 디바이스는 StorSimple 서비스와 통신할 수 없습니다.

- 가비지 수집 버그 수정은 디바이스와 저장 계정이 서로 먼 두 지역에 있을 때 가비지 수집 주기의 성능을 향상시킵니다.
- Blob 시간 제한으로 인한 백업 실패를 수정합니다.
- 업데이트된 OS/.NET framework 보안 패치:
  - [KB4540725](https://support.microsoft.com/topic/servicing-stack-update-for-windows-8-1-rt-8-1-and-server-2012-r2-march-10-2020-cfa082a3-0b58-a8a3-7dc7-ab424de91b86): 3월 2020 SSU (서비스 스택 업데이트)
  - [KB4565541](https://support.microsoft.com/topic/july-14-2020-kb4565541-monthly-rollup-fed6b2b1-3d23-5981-34df-9215a8d8ce01): 7월 2020 롤업
  - [KB4565622](https://support.microsoft.com/topic/security-and-quality-rollup-for-net-framework-4-6-4-6-1-4-6-2-4-7-4-7-1-4-7-2-for-windows-8-1-rt-8-1-and-windows-server-2012-r2-kb4565622-b7320848-1889-a624-da01-719f55ee8a00): 7월 2020 .NET Framework 업데이트

## <a name="download-update-13"></a>업데이트 다운로드 1.3

이 업데이트를 다운로드하려면 [Microsoft 업데이트 카탈로그](https://www.catalog.update.microsoft.com/Home.aspx) 서버로 이동하여 KB4575898 패키지를 다운로드합니다. 이 패키지에는 다음 패키지가 포함되어 있습니다. 다음 순서에 따라 패키지를 설치합니다.

1. 2020년 3월까지 2012 R2용 누적 Windows 업데이트를 포함하는 **KB4540725**. 이 롤업에 포함된 내용에 대한 자세한 내용은 [3월 월간 보안 롤업](https://support.microsoft.com/help/4540725)으로 이동하세요.
1. 2020년 7월까지 2012 R2에 대한 누적 Windows Update를 포함하는 **KB4565541**. 이 롤업에 포함된 내용에 대한 자세한 내용은 [7월 월간 보안 롤업](https://support.microsoft.com/help/4565541)으로 이동하세요.
1. 2020년 7월까지 2012 R2에 대한 누적 NET Framework Update를 포함하는 **KB4565622**. 이 롤업에 포함된 내용에 대한 자세한 내용은 [KB4565622](https://support.microsoft.com/help/4565622)로 이동하세요.<!--The Help link opens the KB. I can't find a monthly rollup. I updated the link text to accurately describe what opens.-->
1. 디바이스 소프트웨어 업데이트를 포함하는 **KB3011067**.

KB4575898를 다운로드하고, 다음 지침에 따라 [로컬 웹 UI를 통해 업데이트를 적용](./storsimple-virtual-array-install-update-11.md#use-the-local-web-ui)합니다.

## <a name="known-issues-in-update-13"></a>업데이트 1.3의 알려진 문제
업데이트 1.3에서 발표된 새로운 문제가 없습니다. 발표된 모든 문제는 이전 릴리스에서 넘어온 것입니다. 이전 릴리스에 포함된 알려진 문제에 대한 요약을 보려면 [업데이트 1.2의 알려진 문제](./storsimple-virtual-array-update-12-release-notes.md#known-issues-in-update-12)로 이동하세요.

## <a name="next-steps"></a>다음 단계
KB4575898를 다운로드하고 [로컬 웹 UI를 통해 업데이트를 적용](./storsimple-virtual-array-install-update-1.md#use-the-local-web-ui)합니다.

## <a name="references"></a>참조
이전 릴리스 정보를 찾으시나요? 다음으로 이동합니다.

- [StorSimple 가상 배열 업데이트 1.2 릴리스 정보](./storsimple-virtual-array-update-12-release-notes.md)
- [StorSimple 가상 배열 업데이트 1.0 릴리스 정보](./storsimple-virtual-array-update-1-release-notes.md)
- [StorSimple 가상 배열 업데이트 0.6 릴리스 정보](./storsimple-virtual-array-update-06-release-notes.md)
- [StorSimple 가상 배열 업데이트 0.5 릴리스 정보](./storsimple-virtual-array-update-05-release-notes.md)
- [StorSimple 가상 배열 업데이트 0.4 릴리스 정보](./storsimple-virtual-array-update-04-release-notes.md)
- [StorSimple 가상 배열 업데이트 0.3 릴리스 정보](./storsimple-ova-update-03-release-notes.md)
- [StorSimple 가상 배열 업데이트 0.1 및 0.2 릴리스 정보](./storsimple-ova-update-01-release-notes.md)
- [StorSimple 가상 배열 일반 공급 릴리스 정보](./storsimple-virtual-array-update-06-release-notes.md)