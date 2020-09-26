---
title: Microsoft Defender for Office 365 (이전의 Office 365 ATP) 데이터를 Azure 센티널에 연결 | Microsoft Docs
description: Microsoft Defender for Office 365 데이터를 Azure 센티널로 수집 하 여 가시성을 얻고 자동화 된 응답 시나리오를 빌드합니다.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/06/2020
ms.author: yelevin
ms.openlocfilehash: 5ac72c9861e06773ffab87dcbf4e730562b798fe
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91346596"
---
# <a name="connect-alerts-from-microsoft-defender-for-office-365"></a>Microsoft Defender for Office 365에서 경고 연결 

> [!IMPORTANT]
>
> - **Microsoft Defender For office 365** 은 이전에 **office 365 ATP (Advanced Threat Protection)** 로 알려져 있었습니다.
>
>     제품에서 아직 사용 중인 이전 이름 (Azure 센티널의 데이터 커넥터 포함)을 일정 기간 동안 볼 수 있습니다.
>
> - Microsoft Defender for Office 365 경고는 현재 공개 미리 보기로 제공 됩니다. 이 기능은 서비스 수준 계약 없이 제공 되며 프로덕션 워크 로드에는 권장 되지 않습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.
 
[Microsoft Defender For Office 365](https://docs.microsoft.com/office365/servicedescriptions/office-365-advanced-threat-protection-service-description) 는 전자 메일 메시지, 악성 URL 링크 및 공동 작업 도구에서 알 수 없는 맬웨어가 야기 하는 0 일 및 기타 고급 위협 으로부터 조직을 보호 합니다. Azure 센티널에 Microsoft Defender for Office 365 경고를 수집 하 여 보안 작업에서 전자 메일, 파일 공유 및 URL 기반 위협에 대 한 정보를 활용할 수 있습니다. 그런 다음 조직 전체에서 보안 이벤트를 더욱 포괄적으로 분석 하 고, 유효 하 고 즉각적인 응답을 위해 플레이 북을 빌드할 수 있습니다.

커넥터는 다음과 같은 경고를 가져옵니다.

- 잠재적으로 악성 URL 클릭이 검색 되었습니다. 

- 배달 후 제거 된 맬웨어가 포함 된 전자 메일 메시지

- 배달 후 제거 된 phish Url을 포함 하는 전자 메일 메시지 

- 사용자가 맬웨어 또는 phish으로 보고 한 전자 메일 

- 의심 스러운 전자 메일 전송 패턴이 검색 됨 

- 사용자가 전자 메일을 보내는 것을 제한 

이러한 경고는 office **보안 및 규정 준수 센터**의 office 고객이 볼 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

- 커넥터를 사용 하도록 설정 하는 경우 Azure 센티널 작업 영역에 대 한 읽기 및 쓰기 권한이 있어야 합니다.

- Azure 센티널 작업 영역 테 넌 트의 전역 관리자 또는 보안 관리자 여야 합니다.

- Office 365 E5, Office 365 A5 및 Microsoft 365 E5 라이선스에 포함 되 고 별도로 구매할 수 있는 [office 365 ATP 요금제 2](https://docs.microsoft.com/microsoft-365/security/office-365-security/office-365-atp#office-365-atp-plan-1-and-plan-2) 에 대 한 유효한 라이선스가 있어야 합니다. 

## <a name="connect-to-microsoft-defender-for-office-365"></a>Microsoft Defender for Office 365에 연결

Microsoft Defender for Office 365가 배포 되 고 정책이 구성 된 경우 경고를 Azure 센티널로 쉽게 수집 수 있습니다.

1. Azure 센티널의 탐색 메뉴에서 **데이터 커넥터** 를 선택 합니다.

1. 커넥터 갤러리에서 **Microsoft Defender For office 365** (여전히 *Office 365 Advanced Threat Protection*이라고 함)를 선택 하 고 **커넥터 페이지 열기**를 선택 합니다.

1. **구성** 섹션에서 **연결**을 클릭 합니다. 

1. **인시던트 만들기** 섹션에서 **사용**을 클릭 합니다.

1. 관련 스키마를 사용 하 여 Office 365 ATP 경고를 쿼리하려면 **Securityalert** 를 검색 하 고 **공급자 이름을** **oatp**로 지정 합니다.

1. Microsoft Defender for Office 365 connector와 함께 제공 되는 쿼리 예제 및 분석 규칙 템플릿을 확인 하 고 사용 하려면 **다음 단계** 탭을 선택 합니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 Office 365 용 Microsoft Defender를 Azure 센티널에 연결 하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- [데이터에 대한 가시성을 얻고 재적 위협을 확인](quickstart-get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](tutorial-detect-threats.md)을 시작합니다.
