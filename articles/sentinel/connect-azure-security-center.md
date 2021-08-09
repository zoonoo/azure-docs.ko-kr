---
title: Azure Sentinel에 Azure Defender 데이터 연결
description: Azure Security Center에서 Azure Defender 경고를 연결하고 Azure Sentinel로 스트리밍하는 방법을 알아봅니다.
author: yelevin
manager: rkarlin
ms.assetid: d28c2264-2dce-42e1-b096-b5a234ff858a
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: how-to
ms.date: 09/07/2020
ms.author: yelevin
ms.openlocfilehash: bb188aa79015c2123b9d9d8b6baf277dfadf2f9c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98633047"
---
# <a name="connect-azure-defender-alert-data-from-azure-security-center"></a>Azure Security Center에서 Azure Defender 경고 데이터 연결

Azure Defender 경고 커넥터를 사용하여 [Azure Security Center](../security-center/security-center-introduction.md)에서 Azure Defender 경고를 수집하고 Azure Sentinel로 스트리밍합니다. 

## <a name="prerequisites"></a>필수 구성 요소

- 사용자는 스트리밍할 로그의 구독에 보안 읽기 권한자 역할이 있어야 합니다.

- Azure Security Center 내에서 Azure Defender를 사용하도록 설정해야 합니다. (표준 계층은 더 이상 존재하지 않으며 더 이상 라이선스 요구 사항이 아닙니다.)

## <a name="connect-to-azure-defender"></a>Azure Defender에 연결

1. Azure Sentinel의 탐색 메뉴에서 **데이터 커넥터** 를 선택합니다.

1. 데이터 커넥터 갤러리에서 **ASC의 Azure Defender 경고**(계속 Azure Security Center라고 할 수도 있음)를 선택하고 **커넥터 페이지 열기** 단추를 클릭합니다.

1. **구성** 에서 Azure Sentinel로 경고를 스트리밍할 각 구독 옆의 **연결** 을 클릭합니다. 연결 단추는 필요한 권한이 있는 경우에만 사용할 수 있습니다.

1. Azure Defender의 경고가 Azure Sentinel에서 인시던트를 자동으로 생성할지 여부를 선택할 수 있습니다. **인시던트 만들기** 에서 **사용** 을 선택하여 경고에서 인시던트를 자동으로 생성하는 기본 분석 규칙을 설정합니다. 그런 다음, **Analytics** 아래의 **활성 규칙** 에서 이 규칙을 편집할 수 있습니다.

1. Azure Defender 경고에 대한 Log Analytics에서 관련 스키마를 사용하려면 **SecurityAlert** 를 검색합니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure Defender를 Azure Sentinel에 연결하는 방법을 알아봤습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.

- [데이터에 대한 가시성을 얻고 재적 위협을 확인](quickstart-get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](tutorial-detect-threats-built-in.md)을 시작합니다.
