---
title: Azure AD 데이터를 Azure Sentinel 미리 보기에 연결 | Microsoft Docs
description: Azure Active Directory 데이터를 Azure Sentinel 연결 하는 방법에 알아봅니다.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 0a8f4a58-e96a-4883-adf3-6b8b49208e6a
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/17/2019
ms.author: rkarlin
ms.openlocfilehash: ce4a57b8c266fe474fc2e6dd8f811fc7440e7ac6
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67190450"
---
# <a name="connect-data-from-azure-active-directory"></a>Azure Active Directory에서 데이터 연결

> [!IMPORTANT]
> Azure Sentinel은 현재 공개 미리 보기로 제공됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

Azure Sentinel를 사용 하면 데이터를 수집할 [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) 하 고 Azure Sentinel에 스트림 합니다. 선택할 수 있습니다 스트림에 [로그인 로그가](../active-directory/reports-monitoring/concept-sign-ins.md) 하 고 [감사 로그](../active-directory/reports-monitoring/concept-audit-logs.md) 합니다.

## <a name="prerequisites"></a>필수 조건

- Active Directory에서 로그인 데이터를 내보낼 하려는 경우에 Azure AD P1 또는 P2 라이선스가 있어야 합니다.

- 로그를 스트리밍 하려는 테 넌 트에서 전역 관리자 또는 보안 관리자 권한이 있는 사용자입니다.

- 연결 상태를 볼 수, Azure AD에 대 한 진단 정보를 액세스할 수 있는 권한이 있어야 합니다. 


## <a name="connect-to-azure-ad"></a>Azure AD에 연결

1. Azure Sentinel 선택 **데이터 커넥터** 클릭 하 고는 **Azure Active Directory** 바둑판식으로 배열 합니다.

2. Azure Sentinel를 스트리밍 하려는 로그 옆에 있는 **Connect**합니다.

6. Log Analytics에서 관련 스키마를 사용 하 여 Azure AD 경고에 대 한를 검색 **SigninLogs** 하 고 **AuditLogs**합니다.




## <a name="next-steps"></a>다음 단계
이 문서에서는 Azure Sentinel를 Azure AD에 연결 하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- 에 대해 알아봅니다 하는 방법 [데이터에 잠재적 위협을 파악](quickstart-get-visibility.md)합니다.
- 시작 [사용 하 여 Azure Sentinel 위협을 감지 하도록](tutorial-detect-threats.md)합니다.
