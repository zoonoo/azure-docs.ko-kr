---
title: Azure 센티널에 Cloud App Security 데이터 연결 | Microsoft Docs
description: MCAS (Microsoft Cloud App Security) 커넥터를 사용 하 여 MCAS에서 Azure 센티널로 경고 및 Cloud Discovery 로그를 스트리밍하는 방법을 알아봅니다. 
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
ms.openlocfilehash: 5c67f517ffd3704644fbe8041f3ac1e7b0d1f09c
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90904560"
---
# <a name="connect-data-from-microsoft-cloud-app-security"></a>Microsoft Cloud App Security에서 데이터 연결 

Mcas ( [Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) ) 커넥터를 사용 하면 mcas의 경고 및 [Cloud Discovery 로그](https://docs.microsoft.com/cloud-app-security/tutorial-shadow-it) 를 Azure 센티널로 스트리밍할 수 있습니다. 이렇게 하면 클라우드 앱에 대 한 가시성을 확보 하 고 정교한 분석을 통해 사이버 위협 대를 식별 및 공격 하 고 데이터가 이동 하는 방식을 제어할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

- 사용자에 게 작업 영역에 대 한 읽기 및 쓰기 권한이 있어야 합니다.
- 사용자에 게 작업 영역의 테 넌 트에 대 한 전역 관리자 또는 보안 관리자 권한이 있어야 합니다.
- Cloud Discovery 로그를 Azure 센티널로 스트리밍하려면 [Microsoft Cloud App Security에서 SIEM으로 Azure 센티널을 사용 하도록 설정](https://aka.ms/AzureSentinelMCAS)합니다.

> [!IMPORTANT]
> Cloud Discovery 로그 수집은 현재 공개 미리 보기로 제공 됩니다.
> 이 기능은 서비스 수준 계약 없이 제공 되며 프로덕션 워크 로드에는 권장 되지 않습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.
 
## <a name="connect-to-cloud-app-security"></a>Cloud App Security에 연결

Cloud App Security 이미 있는 경우 [네트워크에서 사용 하도록 설정](https://docs.microsoft.com/cloud-app-security/getting-started-with-cloud-app-security)되었는지 확인 합니다.
Cloud App Security 배포 하 고 데이터를 수집 경고 데이터를 Azure 센티널로 쉽게 스트리밍할 수 있습니다.


1. Azure 센티널 탐색 메뉴에서 **데이터 커넥터**를 선택 합니다. 커넥터 목록에서 **Microsoft Cloud App Security** 타일을 클릭 한 다음 오른쪽 아래에 있는 **커넥터 페이지 열기** 단추를 클릭 합니다.

1. Azure 센티널로 스트리밍할 로그를 선택 합니다. **경고** 를 선택 하 고 **로그** (미리 보기)를 Cloud Discovery 수 있습니다. 

1. **변경 내용 적용**을 클릭합니다.

1. Azure Defender 경고가 Azure Security Center에서 Azure 센티널에 인시던트를 자동으로 생성할지 여부를 선택할 수 있습니다. **인시던트 만들기**에서 **사용** 을 선택 하 여 경고에서 인시던트를 자동으로 생성 하는 기본 분석 규칙을 설정 합니다. 그런 다음 **활성 규칙** 탭의 **분석**에서이 규칙을 편집할 수 있습니다.

1. Cloud App Security 경고에 대 한 Log Analytics에서 관련 스키마를 사용 하려면 `SecurityAlert` 쿼리 창에를 입력 합니다. Cloud Discovery logs 스키마의 경우를 입력 `McasShadowItReporting` 합니다.

> [!NOTE]
> Cloud Discovery는 클라우드 앱에 대 한 데이터 기본 사용자의 연결을 집계 하 여 추세를 검색 하 고 식별 하는 데 도움이 됩니다.
>
> Cloud Discovery 데이터는 날짜별로 집계 되므로 최신 데이터의 최대 24 시간이 Azure 센티널에 반영 되지 않을 수 있습니다. 하위 수준 조사에서 더 많은 즉각적인 데이터를 요구 하는 경우 원시 데이터가 있는 원본 어플라이언스 또는 서비스에서 직접 수행 해야 합니다.

## <a name="next-steps"></a>다음 단계
이 문서에서는 Microsoft Cloud App Security를 Azure 센티널에 연결 하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- [데이터에 대한 가시성을 얻고 재적 위협을 확인](quickstart-get-visibility.md)하는 방법을 알아봅니다.
- [기본 제공](tutorial-detect-threats.md) 또는 [사용자 지정](tutorial-detect-threats-custom.md) 규칙을 사용 하 여 Azure 센티널에서 위협을 검색 하기 시작 합니다.
