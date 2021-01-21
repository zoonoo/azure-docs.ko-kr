---
title: Azure 센티널에 Azure Active Directory 데이터 연결 | Microsoft Docs
description: Azure Active Directory에서 데이터를 수집 하 고 Azure AD 로그인 로그 및 감사 로그를 Azure 센티널로 스트리밍하는 방법에 대해 알아봅니다.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 0a8f4a58-e96a-4883-adf3-6b8b49208e6a
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/20/2021
ms.author: yelevin
ms.openlocfilehash: e84484990725b0c39b132aead51e9b01dbb7e7ef
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/21/2021
ms.locfileid: "98632294"
---
# <a name="connect-data-from-azure-active-directory-azure-ad"></a>Azure Active Directory에서 데이터 연결 (Azure AD)

Azure 센티널의 기본 제공 커넥터를 사용 하 여 [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) 에서 데이터를 수집 하 고 azure 센티널로 스트리밍할 수 있습니다. 커넥터를 사용 하 여 [로그인 로그](../active-directory/reports-monitoring/concept-sign-ins.md) 및 [감사 로그](../active-directory/reports-monitoring/concept-audit-logs.md)를 스트리밍할 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

- Azure AD 라이선스 (Free/O365/P1/P2)는 모두 Azure 센티널로 로그인 로그를 수집 하는 데 충분 합니다. Azure Monitor (Log Analytics) 및 Azure 센티널에 대해 추가 기가바이트 요금이 적용 될 수 있습니다.

- 작업 영역에서 사용자에 게 Azure 센티널 참가자 역할을 할당 해야 합니다.

- 사용자에 게 로그를 스트리밍할 테 넌 트의 전역 관리자 또는 보안 관리자 역할이 할당 되어야 합니다.

- 연결 상태를 볼 수 있으려면 사용자에 게 Azure AD 진단 설정에 대 한 읽기 및 쓰기 권한이 있어야 합니다. 

## <a name="connect-to-azure-active-directory"></a>Azure Active Directory에 연결

1. Azure 센티널의 탐색 메뉴에서 **데이터 커넥터** 를 선택 합니다.

1. 데이터 커넥터 갤러리에서 **Azure Active Directory** 을 선택한 다음 **커넥터 페이지 열기** 를 선택 합니다.

1. Azure 센티널로 스트리밍할 로그 유형 옆의 확인란을 선택 하 고 **연결** 을 클릭 합니다. 선택할 수 있는 로그 유형은 다음과 같습니다.

    - 로그인 로그
    - 감사 로그
    - 비 대화형 사용자 로그인 로그
    - 서비스 사용자 로그인 로그
    - 관리 Id 로그인 로그
    - 프로비저닝 로그

## <a name="find-your-data"></a>데이터 찾기

연결이 설정 되 면 데이터는 **로그** 의 **logmanagement** 섹션 아래에 있는 다음 테이블에 표시 됩니다.

- `SigninLogs`
- `AuditLogs`
- `AADNonInteractiveUserSignInLogs`
- `AADServicePrincipalSignInLogs`
- `AADManagedIdentitySignInLogs`
- `AADProvisioningLogs`

Azure AD 로그를 쿼리하려면 쿼리 창의 맨 위에 관련 테이블 이름을 입력 합니다.

## <a name="next-steps"></a>다음 단계
이 문서에서는 Azure Active Directory를 Azure 센티널에 연결 하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- [데이터에 대한 가시성을 얻고 재적 위협을 확인](quickstart-get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](tutorial-detect-threats-built-in.md)을 시작합니다.
