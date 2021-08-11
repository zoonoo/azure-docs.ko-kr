---
title: Cloud App Security 데이터를 Azure Sentinel에 연결 | Microsoft Docs
description: MCAS(Microsoft Cloud App Security) 커넥터를 사용하여 MCAS에서 Azure Sentinel로 경고와 Cloud Discovery 로그를 스트림하는 방법을 알아봅니다.
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
ms.date: 03/24/2020
ms.author: yelevin
ms.openlocfilehash: 3312eed71865508e5e83d37c7ced8cf220f13ca9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97835111"
---
# <a name="connect-data-from-microsoft-cloud-app-security"></a>Microsoft Cloud App Security에서 데이터 연결 

MCAS([Microsoft Cloud App Security](/cloud-app-security/what-is-cloud-app-security)) 커넥터를 사용하여 MCAS에서 Azure Sentinel로 경고와 [Cloud Discovery 로그](/cloud-app-security/tutorial-shadow-it)를 스트림할 수 있습니다. 이를 통해 클라우드 앱을 표시하고, 사이버 위협을 식별하고 대응하기 위한 정교한 분석을 가져오고, 데이터가 이동하는 방식을 제어할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

- 사용자에게 작업 영역에 대한 읽기 및 쓰기 권한이 있어야 합니다.
- 사용자에게 작업 영역의 테넌트에 대한 전역 관리자 또는 보안 관리자 권한이 있어야 합니다.
- Cloud Discovery 로그를 Azure Sentinel로 스트림하려면 [Microsoft Cloud App Security에서 Azure Sentinel을 SIEM으로 사용](/cloud-app-security/siem-sentinel)하도록 설정합니다.

> [!IMPORTANT]
> Cloud Discovery 로그 수집은 현재 퍼블릭 미리 보기 상태입니다.
> 해당 기능은 별도의 서비스 수준 계약 없이 제공되며, 프로덕션 작업에는 사용하지 않는 것이 좋습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.
 
## <a name="connect-to-cloud-app-security"></a>Cloud App Security에 연결

Cloud App Security가 이미 있는 경우 [네트워크에서 사용하도록 설정](/cloud-app-security/getting-started-with-cloud-app-security)되어 있는지 확인합니다.
Cloud App Security가 배포되고 데이터를 수집하는 경우 경고 데이터를 Azure Sentinel로 쉽게 스트림할 수 있습니다.


1. Azure Sentinel 탐색 메뉴에서 **데이터 커넥터** 를 선택합니다. 커넥터 목록에서 **Microsoft Cloud App Security** 타일을 클릭한 다음, 오른쪽 아래에 있는 **커넥터 페이지 열기** 단추를 클릭합니다.

1. Azure Sentinel로 스트림할 로그를 선택합니다. **경고** 및  **Cloud Discovery 로그**(미리 보기)를 선택할 수 있습니다. 

1. **변경 내용 적용** 을 클릭합니다.

1. Cloud App Security의 경고가 Azure Sentinel에서 인시던트를 자동으로 생성할지 여부를 선택할 수 있습니다. **인시던트 만들기 - 권장** 에서 **사용** 을 선택하여 경고에서 인시던트를 자동으로 생성하는 기본 분석 규칙을 켭니다. 그런 다음, **활성 규칙** 탭의 **분석** 에서 이 규칙을 편집할 수 있습니다.

1. Cloud App Security 경고에 대해 Log Analytics의 관련 스키마를 사용하려면 쿼리 창에 `SecurityAlert`를 입력합니다. Cloud Discovery 로그 스키마에 대해 `McasShadowItReporting`을 입력합니다.

> [!NOTE]
> Cloud Discovery를 사용하면 클라우드 앱에 대한 사용자 연결에 관한 데이터를 집계하여 추세를 검색하고 식별할 수 있습니다.
>
> Cloud Discovery 데이터는 매일 집계되므로 최대 24시간 동안의 최신 데이터가 Azure Sentinel에 반영되지 않는다는 점에 유의하세요. 낮은 수준 조사에 더 즉각적인 데이터가 필요한 경우 원시 데이터가 있는 원본 어플라이언스나 서비스에서 직접 조사를 수행해야 합니다.

## <a name="next-steps"></a>다음 단계
이 문서에서는 Microsoft Cloud App Security를 Azure Sentinel에 연결하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- [데이터에 대한 가시성을 얻고 재적 위협을 확인](quickstart-get-visibility.md)하는 방법을 알아봅니다.
- [기본 제공](./tutorial-detect-threats-built-in.md) 또는 [사용자 지정](tutorial-detect-threats-custom.md) 규칙을 사용하여 Azure Sentinel에서 위협 검색 시작하기.