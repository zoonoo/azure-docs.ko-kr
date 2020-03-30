---
title: 마이크로소프트 디펜더 ATP 데이터를 Azure 센티넬에 연결 | 마이크로 소프트 문서
description: Microsoft Defender 고급 위협 보호 데이터를 Azure Sentinel에 연결하는 방법에 대해 알아봅니다.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/13/2019
ms.author: yelevin
ms.openlocfilehash: ee2c917d02a442ebabc0e1b40bc245982af794f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588215"
---
# <a name="connect-alerts-from-microsoft-defender-advanced-threat-protection"></a>Microsoft Defender 고급 위협 보호의 경고 연결 


> [!IMPORTANT]
> Microsoft Defender 고급 위협 보호 로그의 섭취는 현재 공개 미리 보기상태입니다.
> 이 기능은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 권장되지 않습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.
 

한 번의 클릭으로 [Microsoft Defender 고급 위협 보호에서](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection) Azure Sentinel로 경고를 스트리밍할 수 있습니다. 이 연결을 사용하면 Microsoft Defender 고급 위협 보호에서 Azure Sentinel으로 경고를 스트리밍할 수 있습니다. 

## <a name="prerequisites"></a>사전 요구 사항

- Microsoft Defender 고급 위협 보호에 대한 유효한 라이센스는 [라이선스 프로비저닝 유효성 검사에](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/licensing)설명되어 있으며 Microsoft Defender 고급 위협 보호를 위한 완전 설정에 설명되어 있습니다. 
- Azure Sentinel 테넌트의 관리자 또는 보안 관리자여야 합니다.


## <a name="connect-to-microsoft-defender-advanced-threat-protection"></a>마이크로소프트 디펜더 고급 위협 보호에 연결

Microsoft Defender 고급 위협 보호가 배포되고 데이터를 수집하면 경고를 Azure Sentinel으로 쉽게 스트리밍할 수 있습니다.


1. Azure Sentinel에서 **데이터 커넥터를**선택하고 **Microsoft Defender 고급 위협 보호** 타일을 클릭하고 연결 열기 **페이지를**선택합니다.
1. **연결**을 클릭합니다. 
1. Defender ATP 경고에 대한 로그 분석에서 관련 스키마를 사용하려면 **SecurityAlert** 및 **공급자 이름을** 검색하는 것이 **MDATP입니다.**




## <a name="next-steps"></a>다음 단계
이 문서에서는 Microsoft Defender ATP를 Azure Sentinel에 연결하는 방법을 배웠습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- [데이터에 대한 가시성을 얻고 재적 위협을 확인](quickstart-get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](tutorial-detect-threats.md)을 시작합니다.
