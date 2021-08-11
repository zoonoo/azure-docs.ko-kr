---
title: Microsoft Threat Modeling Tool 릴리스 03/22/2020 - Azure
description: Threat Modeling Tool 릴리스 7.3.00316.1에 대한 릴리스 정보 문서화
author: jegeib
ms.author: jegeib
ms.service: security
ms.topic: article
ms.date: 03/22/2020
ms.openlocfilehash: e182d40d20529b5743fa9105c68108a8ae55d943
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94516903"
---
# <a name="threat-modeling-tool-update-release-73003161---03222020"></a>Threat Modeling Tool 업데이트 릴리스 7.3.00316.1 - 03/22/2020

Microsoft TMT(Threat Modeling Tool) 버전 7.3.00316.1은 2020년 3월 22일 출시되었으며 이 버전에 포함된 변경 사항은 다음과 같습니다.

- 내게 필요한 옵션 기능 향
- 버그 수정
- 새 DiagramReader 기능

## <a name="notable-bug-fixes"></a>주목할 만한 버그 수정

### <a name="exporting-the-threat-list-to-csv"></a>위협 목록을 CSV로 내보내기

CSV 함수로 내보내기 기능에서 위협 목록에서 내보낼 필드를 일관되지 않게 선택했습니다. 이제 위협 목록의 모든 필드를 CSV 파일로 내보냅니다. 

### <a name="ux-bugs"></a>UX 버그

- 기본 워크플로의 도움말 메뉴(만들기/열기/분석) 및 템플릿 편집기 환경은 이제 일관된 메뉴 옵션을 제공합니다.
- 이제 스텐실 창의 검색 창에 표준 커서가 있고 적절한 레이블이 추가되었습니다.

## <a name="new-features"></a>새로운 기능

### <a name="diagramreader-feature-has-been-added"></a>DiagramReader 기능이 추가되었습니다

모델이 열려 있는 동안 주 메뉴에 새 DiagramReader 기능이 추가되었습니다. 이 기능은 모델의 그래픽 표현을 텍스트 기반 내러티브로 변환합니다. 

## <a name="system-requirements"></a>시스템 요구 사항

- 지원되는 운영 체제:
  - [Microsoft Windows 10 1주년 업데이트](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97) 이상
- 필요한 .NET 버전:
  - [.NET 4.7.1](https://go.microsoft.com/fwlink/?LinkId=863262) 이상
- 추가 요구 사항:
  - 도구와 템플릿에 대한 업데이트를 수신하려면 인터넷 연결이 필요합니다.

## <a name="documentation-and-feedback"></a>문서 및 피드백

- Threat Modeling Tool에 대한 문서는 [docs.microsoft.com](./threat-modeling-tool.md)에 있으며 [도구 사용](./threat-modeling-tool-getting-started.md)에 대한 정보를 포함합니다.

## <a name="next-steps"></a>다음 단계

[Microsoft Threat Modeling Tool](https://aka.ms/threatmodelingtool)의 최신 버전을 다운로드합니다.