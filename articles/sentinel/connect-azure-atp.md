---
title: Microsoft Defender for Identity(이전의 Azure ATP) 데이터를 Azure Sentinel에 연결 | Microsoft Docs
description: 한 번의 클릭으로 Microsoft Defender for Identity(이전의 Azure ATP(Advanced Threat Protection))에서 Azure Sentinel로 로그를 스트리밍하는 방법에 대해 알아봅니다.
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
ms.openlocfilehash: 7a26091d4985b6fdb17120c6fd70476a750c94a9
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107714126"
---
# <a name="connect-data-from-microsoft-defender-for-identity-formerly-azure-advanced-threat-protection"></a>Microsoft Defender for Identity에서 데이터 연결(이전의 Azure Advanced Threat Protection)

> [!IMPORTANT]
> Azure Sentinel의 Microsoft Defender for Identity 데이터 커넥터는 현재 공개 미리 보기로 제공됩니다.
> 해당 기능은 별도의 Service Level Agreement(서비스 수준 규약) 없이 제공되며, 프로덕션 작업에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

이 문서에서는 [Microsoft Defender for Identity](/azure-advanced-threat-protection/what-is-atp)에서 Azure Sentinel로 보안 경고를 스트리밍하는 방법을 설명합니다. 

보안 경고 외에도 상태 경고를 전달하려면 Microsoft Defender for Identity를 Syslog 서버와 통합합니다. 자세한 내용은 [Microsoft Defender for Identity 설명서](/defender-for-identity/setting-syslog)를 참조하세요. 

## <a name="prerequisites"></a>필수 구성 요소

- 전역 관리자 또는 보안 관리자 권한이 있는 사용자
- Microsoft Defender for Identity의 미리 보기 고객이고 Microsoft Defender for Identity 및 Microsoft Cloud App Security를 통합할 수 있도록 설정해야 합니다. 자세한 내용은 [Microsoft Defender for Identity 통합](/cloud-app-security/mdi-integration)을 참조하세요.

## <a name="connect-to-microsoft-defender-for-identity"></a>Microsoft Defender for Identity에 연결

Microsoft Defender for Identity 미리 보기 버전이 [네트워크에서 사용하도록 설정](/azure-advanced-threat-protection/install-atp-step1)되어 있는지 확인합니다.
Microsoft Defender for Identity가 배포되고 데이터를 수집하는 경우 경고를 Azure Sentinel로 쉽게 스트리밍할 수 있습니다. 경고가 Azure Sentinel로 스트리밍을 시작하는 데 최대 24시간이 걸릴 수 있습니다.


1. Microsoft Defender for Identity을 Azure Sentinel에 연결하려면 Microsoft Defender for Identity 및 Microsoft Cloud App Security를 통합할 수 있도록 설정해야 합니다. 이 작업을 수행하는 방법에 대한 자세한 내용은 [Microsoft Defender For Identity 통합](/cloud-app-security/mdi-integration)을 참조하세요.

1. Azure Sentinel에서 **데이터 커넥터** 를 선택한 다음 **Microsoft Defender for Identity(미리 보기)** 타일을 클릭합니다.

1. Microsoft Defender for Identity의 경고가 Azure Sentinel에서 인시던트를 자동으로 생성하도록 설정할지 여부를 선택할 수 있습니다. **인시던트 만들기** 에서 **사용** 을 선택하여 연결된 보안 서비스에서 생성된 경고에서 인시던트가 자동으로 생성되는 기본 분석 규칙을 사용하도록 설정합니다. 그런 다음, **Analytics** 및 **활성 규칙** 에서 이 규칙을 편집할 수 있습니다.

1. **연결** 을 클릭합니다.

1. Microsoft Defender for Identity 경고에 대한 Log Analytics에서 관련 스키마를 사용하려면 **SecurityAlert** 를 검색합니다.

> [!NOTE]
> 경고가 30KB보다 큰 경우 Azure Sentinel은 경고에서 엔터티 필드를 표시하는 것을 중지합니다.

## <a name="next-steps"></a>다음 단계
이 문서에서는 Microsoft Defender for Identity를 Azure Sentinel에 연결하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- [데이터에 대한 가시성을 얻고 잠재적 위협을 확인](quickstart-get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](tutorial-detect-threats-built-in.md)을 시작합니다.
