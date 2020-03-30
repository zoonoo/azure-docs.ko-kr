---
title: Azure AD 데이터를 Azure 센티넬에 연결 | 마이크로 소프트 문서
description: Azure Active Directory 데이터를 Azure Sentinel에 연결하는 방법을 알아봅니다.
services: sentinel
documentationcenter: na
author: yelevin
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
ms.author: yelevin
ms.openlocfilehash: be9241a6156621d3f90dbab2da5bebeb463b4232
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588623"
---
# <a name="connect-data-from-azure-active-directory"></a>Azure Active 디렉터리에서 데이터를 연결



Azure Sentinel을 사용하면 [Azure Active Directory에서](../active-directory/fundamentals/active-directory-whatis.md) 데이터를 수집하고 Azure Sentinel로 스트리밍할 수 있습니다. [로그인 로그](../active-directory/reports-monitoring/concept-sign-ins.md) 및 감사 로그를 스트리밍하도록 선택할 수 [있습니다.](../active-directory/reports-monitoring/concept-audit-logs.md)

## <a name="prerequisites"></a>사전 요구 사항

- Active Directory에서 로그인 데이터를 내보내려면 Azure AD P1 또는 P2 라이선스가 있어야 합니다.

- 로그를 스트리밍하려는 테넌트에 대한 글로벌 관리자 또는 보안 관리자 권한이 있는 사용자입니다.

- 연결 상태를 보려면 Azure AD 진단 로그에 액세스할 수 있는 권한이 있어야 합니다. 


## <a name="connect-to-azure-ad"></a>Azure에 연결

1. Azure Sentinel에서 **데이터 커넥터를** 선택한 다음 **Azure Active Directory** 타일을 클릭합니다.

1. Azure Sentinel로 스트리밍하려는 로그 옆에서 **연결을**클릭합니다.

1. Azure AD의 경고를 Azure Sentinel에서 인시던트를 자동으로 생성할지 여부를 선택할 수 있습니다. **인시던트 만들기**에서 **사용**을 선택하여 연결된 보안 서비스에서 생성된 경고에서 인시던트가 자동으로 생성되는 기본 분석 규칙을 사용하도록 설정합니다. 그런 다음, **Analytics** 및 **활성 규칙**에서 이 규칙을 편집할 수 있습니다.

1. Azure AD 경고에 대한 로그 분석에서 관련 스키마를 사용하려면 **SigninLog및** **AuditLogs를 검색합니다.**




## <a name="next-steps"></a>다음 단계
이 문서에서는 Azure AD를 Azure Sentinel에 연결하는 방법을 배웠습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- [데이터에 대한 가시성을 얻고 재적 위협을 확인](quickstart-get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](tutorial-detect-threats-built-in.md)을 시작합니다.
