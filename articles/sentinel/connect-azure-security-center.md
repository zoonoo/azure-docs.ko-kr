---
title: Azure 센티널에 azure Defender 데이터 연결
description: Azure Security Center에서 Azure Defender 경고를 연결 하 고 Azure 센티널로 스트리밍하는 방법에 대해 알아봅니다.
author: yelevin
manager: rkarlin
ms.assetid: d28c2264-2dce-42e1-b096-b5a234ff858a
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: how-to
ms.date: 09/07/2020
ms.author: yelevin
ms.openlocfilehash: b1188e533039b0137cebb22652d9921418c41deb
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/10/2020
ms.locfileid: "89659647"
---
# <a name="connect-azure-defender-alert-data-from-azure-security-center"></a>Azure Security Center에서 Azure Defender 경고 데이터 연결

Azure Defender 경고 커넥터를 사용 하 여 [Azure Security Center](../security-center/security-center-intro.md) 에서 azure defender 경고를 수집 하 고 azure 센티널로 스트리밍합니다. 

## <a name="prerequisites"></a>전제 조건

- 사용자는 스트리밍할 로그의 구독에 보안 읽기 권한자 역할이 있어야 합니다.

- Azure Security Center 내에서 Azure Defender를 사용 하도록 설정 해야 합니다. (표준 계층은 더 이상 존재 하지 않으며 더 이상 라이선스 요구 사항이 아닙니다.)

## <a name="connect-to-azure-defender"></a>Azure Defender에 연결

1. Azure 센티널의 탐색 메뉴에서 **데이터 커넥터** 를 선택 합니다.

1. 데이터 커넥터 갤러리에서 **ASC의 Azure Defender 경고** (계속 Azure Security Center 호출 될 수 있음)를 선택 하 고 **커넥터 페이지 열기** 단추를 클릭 합니다.

1. **구성**아래에서 Azure 센티널로 스트리밍할 경고를 포함 하는 각 구독 옆에 있는 **연결** 을 클릭 합니다. 연결 단추는 필요한 권한이 있는 경우에만 사용할 수 있습니다.

1. Azure Defender의 경고가 Azure 센티널에서 인시던트를 자동으로 생성할지 여부를 선택할 수 있습니다. **인시던트 만들기**에서 **사용** 을 선택 하 여 경고에서 인시던트를 자동으로 생성 하는 기본 분석 규칙을 설정 합니다. 그런 다음 **활성 규칙** 탭의 **분석**에서이 규칙을 편집할 수 있습니다.

1. Azure Defender 알림에 대 한 Log Analytics에서 관련 스키마를 사용 하려면 **Securityalert**를 검색 합니다.

## <a name="next-steps"></a>다음 단계
이 문서에서는 azure 사용자를 Azure 센티널에 연결 하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- [데이터에 대한 가시성을 얻고 재적 위협을 확인](quickstart-get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](tutorial-detect-threats-built-in.md)을 시작합니다.
