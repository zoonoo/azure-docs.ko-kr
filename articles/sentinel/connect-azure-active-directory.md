---
title: Azure 센티널에 Azure AD 데이터 연결 | Microsoft Docs
description: Azure 센티널에 Azure Active Directory 데이터를 연결 하는 방법에 대해 알아봅니다.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 0a8f4a58-e96a-4883-adf3-6b8b49208e6a
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: rkarlin
ms.openlocfilehash: 43405bd3e97dbee325f0a5ed82c5848880775eee
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2019
ms.locfileid: "71240812"
---
# <a name="connect-data-from-azure-active-directory"></a>Azure Active Directory에서 데이터 연결



Azure 센티널을 사용 하면 [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) 에서 데이터를 수집 하 고 azure 센티널로 스트리밍할 수 있습니다. [로그인 로그](../active-directory/reports-monitoring/concept-sign-ins.md) 및 [감사 로그](../active-directory/reports-monitoring/concept-audit-logs.md) 를 스트리밍하기를 선택할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

- Active Directory에서 로그인 데이터를 내보내려면 Azure AD P1 또는 P2 라이선스가 있어야 합니다.

- 로그를 스트리밍할 테 넌 트에 대 한 전역 관리자 또는 보안 관리자 권한이 있는 사용자입니다.

- 연결 상태를 보려면 Azure AD 진단 로그에 액세스할 수 있는 권한이 있어야 합니다. 


## <a name="connect-to-azure-ad"></a>Azure AD에 연결

1. Azure 센티널에서 **데이터 커넥터** 를 선택한 다음 **Azure Active Directory** 타일을 클릭 합니다.

1. Azure 센티널로 스트리밍할 로그 옆에서 **연결**을 클릭 합니다.

1. Azure AD의 경고가 Azure 센티널에서 자동으로 인시던트를 자동으로 생성할지 여부를 선택할 수 있습니다. **인시던트 만들기** 에서 **사용** 을 선택 하 여 연결 된 보안 서비스에 생성 된 경고에서 인시던트를 자동으로 생성 하는 기본 분석 규칙을 사용 하도록 설정 합니다. 그런 다음 **분석** 및 **활성 규칙**에서이 규칙을 편집할 수 있습니다.

1. Azure AD 경고에 대 한 Log Analytics에서 관련 스키마를 사용 하려면 **SigninLogs** 및 **AuditLogs**를 검색 합니다.




## <a name="next-steps"></a>다음 단계
이 문서에서는 azure AD를 Azure 센티널에 연결 하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- [데이터 및 잠재적 위협에 대 한 가시성을 얻는](quickstart-get-visibility.md)방법에 대해 알아봅니다.
- [Azure 센티널로 위협 검색을](tutorial-detect-threats-built-in.md)시작 합니다.
