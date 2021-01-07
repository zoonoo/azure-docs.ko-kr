---
title: Microsoft Threat Modeling Tool 릴리스 02/11/2020-Azure
description: 위협 모델링 도구 릴리스 7.3.00206.1에 대 한 릴리스 정보를 문서화 합니다.
author: jegeib
ms.author: jegeib
ms.service: security
ms.topic: article
ms.date: 02/25/2020
ms.openlocfilehash: 9f9b162460cd2e7a624c1ad3f992011487d1b795
ms.sourcegitcommit: 5831eebdecaa68c3e006069b3a00f724bea0875a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2020
ms.locfileid: "94516937"
---
# <a name="threat-modeling-tool-update-release-73002061---02112020"></a>Threat Modeling Tool 업데이트 릴리스 7.3.00206.1-02/11/2020

TMT (Microsoft Threat Modeling Tool 버전 7.3.00206.1)는 2 월 11 2020 일에 출시 되었으며 다음과 같은 변경 내용이 포함 되어 있습니다.

- 버그 수정

## <a name="notable-bug-fixes"></a>주목할 만한 버그 수정

### <a name="errors-related-to-priority-values-outside-of-the-expected-ranges"></a>예상 범위를 벗어난 우선 순위 값과 관련 된 오류

"Threat Modeling Tool 2016" 또는 사용자 지정 템플릿에서 생성 된 파일을 열 때 일부 고객이 다음 오류 메시지를 보고 했습니다.

```output
System.InvalidOperationException: Invalid Priority value. Accepted values are [0..4] and 'High', 'Medium', 'Low' at ThreatModeling.Model.Threat.get_Priority()

System.ArgumentOutOfRangeException: Accepted values are 'High', 'Medium', and 'Low' Parameter name: value Actual value was 5.6. at ThreatModeling.Model.Threat.set_Priority(String value)
```

이 문제는이 릴리스에서 해결 되었습니다.

## <a name="system-requirements"></a>시스템 요구 사항

- 지원되는 운영 체제
  - [Microsoft Windows 10 기념일 업데이트](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97) 이상
- 필요한 .NET 버전
  - [.Net 4.7.1](https://go.microsoft.com/fwlink/?LinkId=863262) 이상
- 추가 요구 사항
  - 도구와 템플릿에 대한 업데이트를 받으려면 인터넷 연결이 필요합니다.

## <a name="documentation-and-feedback"></a>문서 및 피드백

- Threat Modeling Tool에 대한 문서는 [docs.microsoft.com](./threat-modeling-tool.md)에 있으며 [도구 사용](./threat-modeling-tool-getting-started.md)에 대한 정보를 포함합니다.

## <a name="next-steps"></a>다음 단계

[Microsoft Threat Modeling Tool](https://aka.ms/threatmodelingtool)의 최신 버전을 다운로드합니다.