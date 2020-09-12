---
title: Id 용 Microsoft Defender (이전의 Azure ATP) 데이터를 Azure 센티널에 연결 | Microsoft Docs
description: 한 번의 클릭으로 Microsoft Defender for Identity (이전의 Azure Advanced Threat Protection)에서 Azure 센티널로 로그를 스트리밍하는 방법에 대해 알아봅니다.
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
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: 64c1a7155d0cc4e80f97db138a0626d6e9fdc9e9
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/10/2020
ms.locfileid: "89657809"
---
# <a name="connect-data-from-microsoft-defender-for-identity-formerly-azure-advanced-threat-protection"></a>Microsoft Defender에서 Id에 대 한 데이터 연결 (이전의 Azure Advanced Threat Protection)

> [!IMPORTANT]
> Azure 센티널의 Microsoft Defender for Identity 데이터 커넥터는 현재 공개 미리 보기로 제공 됩니다.
> 이 기능은 서비스 수준 계약 없이 제공 되며 프로덕션 워크 로드에는 권장 되지 않습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

한 번의 클릭으로 [Microsoft Defender에서 id에 대 한](https://docs.microsoft.com/azure-advanced-threat-protection/what-is-atp) 로그를 Azure 센티널로 스트리밍할 수 있습니다.

## <a name="prerequisites"></a>전제 조건

- 전역 관리자 또는 보안 관리자 권한이 있는 사용자
- Id에 대 한 microsoft defender의 미리 보기 고객이 고 Id 및 Microsoft Cloud App Security에 대해 Microsoft Defender를 통합할 수 있도록 설정 해야 합니다. 자세한 내용은 [Id 통합을 위한 Microsoft Defender](https://docs.microsoft.com/cloud-app-security/aatp-integration)를 참조 하세요.

## <a name="connect-to-microsoft-defender-for-identity"></a>Id에 대해 Microsoft Defender에 연결

Microsoft Defender for Identity preview 버전이 [네트워크에서 사용 하도록 설정](https://docs.microsoft.com/azure-advanced-threat-protection/install-atp-step1)되어 있는지 확인 합니다.
Microsoft Defender for Identity가 배포 되 고 데이터를 수집 의심 스러운 경고를 Azure 센티널로 쉽게 스트리밍할 수 있습니다. 경고가 Azure 센티널로 스트리밍을 시작 하는 데 최대 24 시간이 걸릴 수 있습니다.


1. Microsoft Defender for Identity를 Azure 센티널에 연결 하려면 먼저 Id 및 Microsoft Cloud App Security에 대해 Microsoft Defender 간 통합을 사용 하도록 설정 해야 합니다. 이 작업을 수행 하는 방법에 대 한 자세한 내용은 [Microsoft Defender For Identity integration](https://docs.microsoft.com/cloud-app-security/aatp-integration)을 참조 하세요.

1. Azure 센티널에서 **데이터 커넥터** 를 선택한 다음 **Id 용 Microsoft Defender (미리 보기)** 타일을 클릭 합니다.

1. Microsoft Defender에서 Id에 대 한 경고를 자동으로 Azure 센티널에서 인시던트를 자동으로 생성할지 여부를 선택할 수 있습니다. **인시던트 만들기**에서 **사용**을 선택하여 연결된 보안 서비스에서 생성된 경고에서 인시던트가 자동으로 생성되는 기본 분석 규칙을 사용하도록 설정합니다. 그런 다음, **Analytics** 및 **활성 규칙**에서 이 규칙을 편집할 수 있습니다.

1. **연결**을 클릭합니다.

1. Id 경고에 대해 Microsoft Defender에 대 한 Log Analytics에서 관련 스키마를 사용 하려면 **Securityalert**를 검색 합니다.

> [!NOTE]
> 경고가 30KB 보다 큰 경우 Azure 센티널은 경고에서 엔터티 필드를 표시 하는 것을 중지 합니다.

## <a name="next-steps"></a>다음 단계
이 문서에서는 Id 용 Microsoft Defender를 Azure 센티널에 연결 하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- [데이터에 대한 가시성을 얻고 재적 위협을 확인](quickstart-get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](tutorial-detect-threats-built-in.md)을 시작합니다.

