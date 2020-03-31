---
title: 마이크로소프트 위협 모델링 도구 릴리스 03/22/2020 - Azure
description: Threat Modeling Tool 릴리스 정보 문서화
author: jegeib
ms.author: jegeib
ms.service: security
ms.topic: article
ms.date: 03/22/2020
ms.openlocfilehash: 6fea4d48b62d6ea429d37924ffd15855db6294cc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80146863"
---
# <a name="threat-modeling-tool-update-release-73003161---03222020"></a>위협 모델링 도구 업데이트 릴리스 7.3.00316.1 - 03/22/2020

Microsoft 위협 모델링 도구(TMT)의 버전 7.3.00316.1은 2020년 3월 22일에 릴리스되었으며 다음과 같은 변경 사항이 포함되어 있습니다.

- 내게 필요한 옵션 기능 향
- 버그 수정
- 새로운 다이어그램리더 기능

## <a name="notable-bug-fixes"></a>주목할 만한 버그 수정

### <a name="exporting-the-threat-list-to-csv"></a>위협 목록을 CSV로 내보내기

CSV 함수로의 내보내기는 위협 목록에서 내보낼 필드를 일관되게 선택했습니다. 이제 위협 목록의 모든 필드가 CSV 파일로 내보내집니다. 

### <a name="ux-bugs"></a>UX 버그

- 기본 워크플로의 도움말 메뉴(만들기/열기/분석) 및 템플릿 편집기 환경의 일관된 메뉴 옵션이 있습니다.
- 이제 스텐실 창의 검색 창에 표준 커서가 있고 적절한 레이블이 추가되었습니다.

## <a name="new-features"></a>새로운 기능

### <a name="diagramreader-feature-has-been-added"></a>다이어그램리더 기능이 추가되었습니다.

모델이 열려 있는 동안 주 메뉴에 새로운 DiagramReader 기능이 추가되었습니다. 이 기능은 모델의 그래픽 표현을 텍스트 기반 내러티브로 변환합니다. 

## <a name="system-requirements"></a>시스템 요구 사항

- 지원되는 운영 체제:
  - [마이크로소프트 윈도 10 주년 기념 업데이트](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97) 이상
- .NET 버전이 필요합니다.
  - [.NET 4.7.1](https://go.microsoft.com/fwlink/?LinkId=863262) 이상
- 추가 요구 사항:
  - 템플릿뿐만 아니라 도구에 대한 업데이트를받을 수있는 인터넷 연결

## <a name="documentation-and-feedback"></a>문서 및 피드백

- Threat Modeling Tool에 대한 문서는 [docs.microsoft.com](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool)에 있으며 [도구 사용](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-getting-started)에 대한 정보를 포함합니다.

## <a name="next-steps"></a>다음 단계

[Microsoft Threat Modeling Tool](https://aka.ms/threatmodelingtool)의 최신 버전을 다운로드합니다.
