---
title: Azure ATP 데이터를 Azure Sentinel에 연결 | 마이크로 소프트 문서
description: Azure ATP 데이터를 Azure Sentinel에 연결하는 방법을 알아봅니다.
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
ms.openlocfilehash: 387d04cbbb125006efcc4efc53a02015fe3f5919
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588589"
---
# <a name="connect-data-from-azure-advanced-threat-protection-atp"></a>AZURE 고급 위협 보호(ATP)의 데이터 연결

> [!IMPORTANT]
> Azure Sentinel의 Azure 고급 위협 보호 데이터 커넥터는 현재 공개 미리 보기 상태입니다.
> 이 기능은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 권장되지 않습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

한 번의 클릭으로 [Azure 고급 위협 보호에서](https://docs.microsoft.com/azure-advanced-threat-protection/what-is-atp) Azure Sentinel로 로그를 스트리밍할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

- 글로벌 관리자 또는 보안 관리자 권한이 있는 사용자
- Azure ATP의 미리 보기 고객이어야 하며 Azure ATP와 Microsoft 클라우드 앱 보안 간의 통합을 활성화해야 합니다. 자세한 내용은 [Azure 고급 보호 통합](https://docs.microsoft.com/cloud-app-security/aatp-integration)을 참조하십시오.

## <a name="connect-to-azure-atp"></a>Azure ATP에 연결

네트워크에서 Azure ATP 미리 보기 버전이 활성화되어 있는지 [확인합니다.](https://docs.microsoft.com/azure-advanced-threat-protection/install-atp-step1)
Azure ATP가 배포되고 데이터를 수집하면 의심스러운 경고를 Azure Sentinel로 쉽게 스트리밍할 수 있습니다. 경고가 Azure Sentinel로 스트리밍을 시작하는 데 최대 24시간이 걸릴 수 있습니다.


1. Azure ATP를 Azure Sentinel에 연결하려면 먼저 Azure ATP와 Microsoft 클라우드 앱 보안 간의 통합을 사용하도록 설정해야 합니다. 이 작업을 수행하는 방법에 대한 자세한 내용은 [Azure 고급 위협 보호 통합을](https://docs.microsoft.com/cloud-app-security/aatp-integration)참조하십시오.

1. Azure Sentinel에서 데이터 커넥터를 선택한 다음 **Azure 고급 위협 보호(미리 보기)** 타일을 **클릭합니다.**

1. Azure ATP의 경고가 Azure Sentinel에서 인시던트를 자동으로 생성할지 여부를 선택할 수 있습니다. **인시던트 만들기**에서 **사용**을 선택하여 연결된 보안 서비스에서 생성된 경고에서 인시던트가 자동으로 생성되는 기본 분석 규칙을 사용하도록 설정합니다. 그런 다음, **Analytics** 및 **활성 규칙**에서 이 규칙을 편집할 수 있습니다.

1. **연결**을 클릭합니다.

1. Azure ATP 경고에 대한 로그 분석에서 관련 스키마를 사용하려면 **SecurityAlert**을 검색합니다.

> [!NOTE]
> 경고가 30KB보다 큰 경우 Azure Sentinel은 경고에 엔터티 필드표시를 중지합니다.

## <a name="next-steps"></a>다음 단계
이 문서에서는 Azure 고급 위협 보호를 Azure Sentinel에 연결하는 방법을 배웠습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- [데이터에 대한 가시성을 얻고 재적 위협을 확인](quickstart-get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](tutorial-detect-threats-built-in.md)을 시작합니다.

