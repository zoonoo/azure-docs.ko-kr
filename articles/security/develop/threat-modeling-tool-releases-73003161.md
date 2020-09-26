---
title: Microsoft Threat Modeling Tool 릴리스 03/22/2020-Azure
description: 위협 모델링 도구 릴리스 7.3.00316.1에 대 한 릴리스 정보를 문서화 합니다.
author: jegeib
ms.author: jegeib
ms.service: security
ms.topic: article
ms.date: 03/22/2020
ms.openlocfilehash: 39963e9dadca0ee0be7bb8ad0ebf824c287bfee0
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91317908"
---
# <a name="threat-modeling-tool-update-release-73003161---03222020"></a>Threat Modeling Tool 업데이트 릴리스 7.3.00316.1-03/22/2020

TMT (Microsoft Threat Modeling Tool 버전 7.3.00316.1)는 3 월 22 2020 일에 출시 되었으며 다음과 같은 변경 내용이 포함 되어 있습니다.

- 향상된 접근성 기능
- 버그 수정
- New DiagramReader 기능

## <a name="notable-bug-fixes"></a>주목할 만한 버그 수정

### <a name="exporting-the-threat-list-to-csv"></a>CSV로 위협 목록 내보내기

CSV로 내보내기 함수가 일관 되지 않아 위협 목록에서 내보낼 필드를 선택 하지 않았습니다. 이제 위협 목록의 모든 필드를 CSV 파일로 내보냅니다. 

### <a name="ux-bugs"></a>UX 버그

- 기본 워크플로의 도움말 메뉴 (만들기/열기/분석) 및 템플릿 편집기 환경에는 이제 일관 된 메뉴 옵션이 있습니다.
- 이제 스텐실 창의 검색 창에 표준 커서가 있으며 적절 한 레이블이 추가 되었습니다.

## <a name="new-features"></a>새로운 기능

### <a name="diagramreader-feature-has-been-added"></a>DiagramReader 기능이 추가 되었습니다.

모델이 열려 있는 동안에는 주 메뉴에 새 DiagramReader 기능이 추가 되었습니다. 이 기능은 모델의 그래픽 표현을 텍스트 기반 서술로 변환 합니다. 

## <a name="system-requirements"></a>시스템 요구 사항

- 지원되는 운영 체제:
  - [Microsoft Windows 10 기념일 업데이트](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97) 이상
- 필요한 .NET 버전:
  - [.Net 4.7.1](https://go.microsoft.com/fwlink/?LinkId=863262) 이상
- 추가 요구 사항:
  - 도구 및 템플릿에 대 한 업데이트를 수신 하는 인터넷 연결

## <a name="documentation-and-feedback"></a>문서 및 피드백

- Threat Modeling Tool에 대한 문서는 [docs.microsoft.com](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool)에 있으며 [도구 사용](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-getting-started)에 대한 정보를 포함합니다.

## <a name="next-steps"></a>다음 단계

[Microsoft Threat Modeling Tool](https://aka.ms/threatmodelingtool)의 최신 버전을 다운로드합니다.
