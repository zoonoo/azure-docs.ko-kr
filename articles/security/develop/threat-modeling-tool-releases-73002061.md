---
title: 마이크로소프트 위협 모델링 도구 릴리스 02/11/2020 - Azure
description: Threat Modeling Tool 릴리스 정보 문서화
author: jegeib
ms.author: jegeib
ms.service: security
ms.topic: article
ms.date: 02/25/2020
ms.openlocfilehash: 354707aec90375b4bf25aea6e1baa743d85f20aa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77624840"
---
# <a name="threat-modeling-tool-update-release-73002061---02112020"></a>위협 모델링 도구 업데이트 릴리스 7.3.00206.1 - 02/11/2020

Microsoft 위협 모델링 도구(TMT)의 버전 7.3.00206.1은 2020년 2월 11일에 릴리스되었으며 다음과 같은 변경 사항이 포함되어 있습니다.

- 버그 수정

## <a name="notable-bug-fixes"></a>주목할 만한 버그 수정

### <a name="errors-related-to-priority-values-outside-of-the-expected-ranges"></a>예상 범위를 벗어난 우선 순위 값과 관련된 오류

일부 고객은 "위협 모델링 도구 2016" 또는 사용자 지정 템플릿에서 만든 파일을 열 때 다음과 같은 오류 메시지가 수신되었다고 보고했습니다.

    System.InvalidOperationException: Invalid Priority value. Accepted values are [0..4] and 'High', 'Medium', 'Low' at ThreatModeling.Model.Threat.get_Priority()
    
    System.ArgumentOutOfRangeException: Accepted values are 'High', 'Medium', and 'Low' Parameter name: value Actual value was 5.6. at ThreatModeling.Model.Threat.set_Priority(String value)

이 문제는 이 릴리스에서 해결되었습니다.

## <a name="system-requirements"></a>시스템 요구 사항

- 지원되는 운영 체제
  - [마이크로소프트 윈도 10 주년 기념 업데이트](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97) 이상
- 필요한 .NET 버전
  - [.Net 4.7.1](https://go.microsoft.com/fwlink/?LinkId=863262) 이상
- 추가 요구 사항
  - 도구와 템플릿에 대한 업데이트를 받으려면 인터넷 연결이 필요합니다.

## <a name="documentation-and-feedback"></a>문서 및 피드백

- Threat Modeling Tool에 대한 문서는 [docs.microsoft.com](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool)에 있으며 [도구 사용](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-getting-started)에 대한 정보를 포함합니다.

## <a name="next-steps"></a>다음 단계

[Microsoft Threat Modeling Tool](https://aka.ms/threatmodelingtool)의 최신 버전을 다운로드합니다.
