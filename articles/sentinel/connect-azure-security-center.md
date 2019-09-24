---
title: Azure 센티널에 Azure Security Center 데이터 연결 | Microsoft Docs
description: Azure 센티널에 Azure Security Center 데이터를 연결 하는 방법에 대해 알아봅니다.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: d28c2264-2dce-42e1-b096-b5a234ff858a
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: rkarlin
ms.openlocfilehash: a9c210531f2c4cab1c3c023eab795023c3ad9f0c
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2019
ms.locfileid: "71240212"
---
# <a name="connect-data-from-azure-security-center"></a>Azure Security Center에서 데이터 연결





Azure 센티널을 사용 하면 [Azure Security Center](../security-center/security-center-intro.md) 에서 경고를 연결 하 고 azure 센티널로 스트리밍할 수 있습니다. 

## <a name="prerequisites"></a>사전 요구 사항

- Azure Security Center에서 경고를 내보내려면 해당 로그를 스트리밍하는 구독에 대 한 참가자 여야 합니다.

- 구독에서 실행 중인 [Azure Security Center 표준 계층이](../security-center/security-center-pricing.md) 있어야 합니다. 그렇지 않은 경우 [구독을 표준으로 업그레이드](https://azure.microsoft.com/pricing/details/security-center/)합니다.

- 연결 하려는 각 구독에 대해 전역 관리자 또는 보안 관리자 권한이 있는 사용자로 로그인 해야 합니다.


## <a name="connect-to-azure-security-center"></a>Azure Security Center에 연결

1. Azure 센티널에서 **데이터 커넥터** 를 선택한 다음 **Azure Security Center** 타일을 클릭 합니다.

1. 오른쪽에서 경고를 Azure 센티널로 스트리밍할 각 구독 옆의 **연결** 을 클릭 합니다. 각 구독을 Azure Security Center 표준 계층으로 업그레이드 하 여 경고를 Azure 센티널로 스트리밍하려면 확인 하세요.

1. Azure Security Center 경고가 자동으로 Azure 센티널에서 인시던트를 자동으로 생성할지 여부를 선택할 수 있습니다. **인시던트 만들기** 에서 **사용** 을 선택 하 여 연결 된 보안 서비스에 생성 된 경고에서 인시던트를 자동으로 생성 하는 기본 분석 규칙을 사용 하도록 설정 합니다. 그런 다음 **분석** 및 **활성 규칙**에서이 규칙을 편집할 수 있습니다.

3. **연결**을 클릭합니다.

4. Azure Security Center 경고에 대 한 Log Analytics에서 관련 스키마를 사용 하려면 **Securityalert**를 검색 합니다.

## <a name="next-steps"></a>다음 단계
이 문서에서는 Azure Security Center를 Azure 센티널에 연결 하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- [데이터 및 잠재적 위협에 대 한 가시성을 얻는](quickstart-get-visibility.md)방법에 대해 알아봅니다.
- [Azure 센티널로 위협 검색을](tutorial-detect-threats-built-in.md)시작 합니다.
