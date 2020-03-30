---
title: Azure 보안 센터 데이터를 Azure Sentinel에 연결
description: Azure 보안 센터 데이터를 Azure Sentinel에 연결하는 방법을 알아봅니다.
author: yelevin
manager: rkarlin
ms.assetid: d28c2264-2dce-42e1-b096-b5a234ff858a
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: yelevin
ms.openlocfilehash: e6e24a97a8b98bdd5447295880811914100563fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588538"
---
# <a name="connect-data-from-azure-security-center"></a>Azure 보안 센터의 데이터 연결





Azure Sentinel을 사용하면 Azure [보안 센터에서](../security-center/security-center-intro.md) 경고를 연결하고 Azure Sentinel으로 스트리밍할 수 있습니다. 

## <a name="prerequisites"></a>사전 요구 사항

- Azure Security Center에서 경고를 내보내려면 스트리밍하는 로그의 구독에 보안 판독기 역할이 있어야 합니다.

- 구독에서 실행 중인 [Azure 보안 센터 표준 계층이](../security-center/security-center-pricing.md) 있어야 합니다. 그렇지 않은 경우 구독을 표준 으로 [업그레이드합니다.](https://azure.microsoft.com/pricing/details/security-center/)



## <a name="connect-to-azure-security-center"></a>Azure 보안 센터에 연결

1. Azure Sentinel에서 데이터 커넥터를 선택한 다음 **Azure 보안 센터** 타일을 **클릭합니다.**

1. 오른쪽에서 Azure Sentinel로 스트리밍하려는 경고를 각 구독 옆에 **연결합니다.** Azure 보안 센터 표준 계층으로 각 구독을 업그레이드하여 Azure Sentinel으로 경고를 스트리밍해야 합니다.

1. Azure 보안 센터의 경고가 Azure Sentinel에서 인시던트를 자동으로 생성할지 여부를 선택할 수 있습니다. **인시던트 만들기**에서 **사용**을 선택하여 연결된 보안 서비스에서 생성된 경고에서 인시던트가 자동으로 생성되는 기본 분석 규칙을 사용하도록 설정합니다. 그런 다음, **Analytics** 및 **활성 규칙**에서 이 규칙을 편집할 수 있습니다.

3. **연결**을 클릭합니다.

4. Azure 보안 센터 경고에 대한 로그 분석에서 관련 스키마를 사용하려면 **SecurityAlert**를 검색합니다.

## <a name="next-steps"></a>다음 단계
이 문서에서는 Azure 보안 센터를 Azure Sentinel에 연결하는 방법을 배웠습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- [데이터에 대한 가시성을 얻고 재적 위협을 확인](quickstart-get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](tutorial-detect-threats-built-in.md)을 시작합니다.
