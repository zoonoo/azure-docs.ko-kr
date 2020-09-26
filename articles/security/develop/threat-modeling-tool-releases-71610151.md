---
title: Microsoft Threat Modeling Tool 릴리스 10/16/2019-Azure
description: 위협 모델링 도구 릴리스 7.1.61015.1에 대 한 릴리스 정보를 문서화 합니다.
author: jegeib
ms.author: jegeib
ms.service: security
ms.topic: article
ms.date: 10/16/2019
ms.openlocfilehash: e45d1347993611130d1bb5036ad4638210c15517
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91317942"
---
# <a name="threat-modeling-tool-update-release-71610151---10162019"></a>Threat Modeling Tool 업데이트 릴리스 7.1.61015.1 - 2019/10/16

TMT (Microsoft Threat Modeling Tool 버전 7.1.61015.1)는 10 월 16 2019 일에 출시 되었으며 다음과 같은 변경 내용이 포함 되어 있습니다.

- 향상된 접근성 기능
- 버그 수정
- Azure Logic Apps 및 Azure 데이터 탐색기에 대 한 새 스텐실

## <a name="notable-bug-fixes"></a>주목할 만한 버그 수정

### <a name="improved-backward-compatibility-with-files-created-in-threat-modeling-tool-2016"></a>"Threat Modeling Tool 2016"에서 만든 파일과의 이전 버전과의 호환성이 향상 되었습니다.

"Threat Modeling Tool 2016"에서 만든 위협 모델 파일의 열거나 표시와 관련 된 몇 가지 버그가 수정 되었습니다.

## <a name="feature-enhancements"></a>향상 된 기능

### <a name="new-stencils-for-azure-logic-apps-and-azure-data-explorer"></a>Azure Logic Apps 및 Azure 데이터 탐색기에 대 한 새 스텐실

Azure Logic Apps 및 Azure 데이터 탐색기에 대 한 새 스텐실이 관련 위협과 완화와 함께 Azure 스텐실에 추가 되었습니다.

![Azure Logic Apps 및 Azure 데이터 탐색기 스텐실](./media/threat-modeling-tool-releases-71610151/tmt-logic-apps.png)

## <a name="known-issues"></a>알려진 문제

### <a name="errors-related-to-priority-values-outside-of-the-expected-ranges"></a>예상 범위를 벗어난 우선 순위 값과 관련 된 오류

일부 고객은 "Threat Modeling Tool 2016" 또는 사용자 지정 템플릿에서 생성 된 파일을 열 때 다음 오류 메시지를 보고 했습니다.

```output
System.InvalidOperationException: Invalid Priority value. Accepted values are [0..4] and 'High', 'Medium', 'Low' at ThreatModeling.Model.Threat.get_Priority()

System.ArgumentOutOfRangeException: Accepted values are 'High', 'Medium', and 'Low' Parameter name: value Actual value was 5.6. at ThreatModeling.Model.Threat.set_Priority(String value)
```

이 문제는 조사 중입니다.

## <a name="system-requirements"></a>시스템 요구 사항

- 지원되는 운영 체제
  - [Microsoft Windows 10 기념일 업데이트](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97) 이상
- 필요한 .NET 버전
  - [.Net 4.7.1](https://go.microsoft.com/fwlink/?LinkId=863262) 이상
- 추가 요구 사항
  - 도구와 템플릿에 대한 업데이트를 받으려면 인터넷 연결이 필요합니다.

## <a name="documentation-and-feedback"></a>문서 및 피드백

- Threat Modeling Tool에 대한 문서는 [docs.microsoft.com](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool)에 있으며 [도구 사용](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-getting-started)에 대한 정보를 포함합니다.

## <a name="next-steps"></a>다음 단계

[Microsoft Threat Modeling Tool](https://aka.ms/threatmodelingtool)의 최신 버전을 다운로드합니다.
