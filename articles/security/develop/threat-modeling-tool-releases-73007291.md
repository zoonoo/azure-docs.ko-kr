---
title: Microsoft Threat Modeling Tool 릴리스 07/29/2020-Azure
description: 위협 모델링 도구 릴리스 7.3.00729.1에 대 한 릴리스 정보를 문서화 합니다.
author: jegeib
ms.author: jegeib
ms.service: security
ms.topic: article
ms.date: 07/29/2020
ms.openlocfilehash: fc343f292fa32ed2db67dd74aba7a66dbc00d6ab
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91317840"
---
# <a name="threat-modeling-tool-update-release-73007291---07292020"></a>Threat Modeling Tool 업데이트 릴리스 7.3.00729.1-07/29/2020

TMT (Microsoft Threat Modeling Tool 버전 7.3.00729.1)는 7 월 29 2020에 출시 되었으며 다음과 같은 변경 내용이 포함 되어 있습니다.

- 버그 수정
 
## <a name="known-issues"></a>알려진 문제

### <a name="errors-related-to-tmt7application-file-deserialization"></a>TMT7와 관련 된 오류입니다. 응용 프로그램 파일 deserialization

#### <a name="issue"></a>문제

일부 고객은 Threat Modeling Tool를 다운로드할 때 다음 오류 메시지를 보고 했습니다.

```
The threat model file '$PATH\TMT7.application' could not be deserialized. File is not an actual threat model or the threat model may be corrupted.
```

이 오류는 일부 브라우저에서 ClickOnce 설치를 기본적으로 지원 하지 않기 때문에 발생 합니다. 이러한 경우 ClickOnce 응용 프로그램 파일이 사용자의 하드 드라이브에 다운로드 됩니다.

#### <a name="workaround"></a>해결 방법

TMT7 파일을 두 번 클릭 하 여 Threat Modeling Tool를 시작 하면이 오류가 계속 표시 됩니다. 그러나이 오류를 무시 한 후 도구는 정상적으로 작동 합니다. TMT7 파일을 두 번 클릭 하 여 Threat Modeling Tool를 시작 하는 대신 사용자는 설치 중에 Windows 메뉴에서 만든 바로 가기를 사용 하 여 Threat Modeling Tool를 시작 해야 합니다.

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
