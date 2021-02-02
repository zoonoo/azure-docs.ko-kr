---
title: Azure 센티널에 Azure Active Directory 데이터 연결 | Microsoft Docs
description: Azure Active Directory에서 데이터를 수집 하 고 azure AD 로그인, 감사 및 프로 비전 로그를 Azure 센티널로 스트리밍하는 방법에 대해 알아봅니다.
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
ms.openlocfilehash: f8931fedb380cf81d72b7b5280a5795498daaa57
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/02/2021
ms.locfileid: "99251984"
---
# <a name="connect-azure-active-directory-azure-ad-data-to-azure-sentinel"></a>Azure 센티널에 azure AD (Azure Active Directory) 데이터 연결

Azure 센티널의 기본 제공 커넥터를 사용 하 여 [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) 에서 데이터를 수집 하 고 azure 센티널로 스트리밍할 수 있습니다. 커넥터를 사용 하 여 다음 로그 유형을 스트리밍할 수 있습니다.

- [**로그인 로그**](../active-directory/reports-monitoring/concept-all-sign-ins.md)-사용자가 인증 요소를 제공 하는 [대화형 사용자 로그인](../active-directory/reports-monitoring/concept-all-sign-ins.md#user-sign-ins) 에 대 한 정보가 포함 됩니다.

    이제 Azure AD 커넥터에는 다음과 같은 세 가지 추가 범주의 로그인 로그가 포함 되어 있습니다. 모두 현재 **미리 보기** 상태입니다.
    
    - [**비 대화형 사용자 로그인 로그**](../active-directory/reports-monitoring/concept-all-sign-ins.md#non-interactive-user-sign-ins)-사용자를 대신 하 여 클라이언트에서 수행 하는 로그인에 대 한 정보를 포함 하는 사용자의 상호 작용 또는 인증 요소
    
    - 사용자를 포함 하지 않는 앱 및 서비스 사용자의 로그인에 대 한 정보가 포함 된 [**서비스 사용자 로그인 로그**](../active-directory/reports-monitoring/concept-all-sign-ins.md#service-principal-sign-ins)입니다. 이러한 로그인에서 앱 또는 서비스는 리소스를 인증 하거나 액세스 하기 위해 자체 자격 증명을 제공 합니다.
    
    - Azure에서 관리 하는 암호를 포함 하는 Azure 리소스의 로그인에 대 한 정보를 포함 하는 [**관리 되는 id 로그인 로그**](../active-directory/reports-monitoring/concept-all-sign-ins.md#managed-identity-for-azure-resources-sign-ins). 자세한 내용은 [Azure 리소스에 대 한 관리 되는 Id 란?](../active-directory/managed-identities-azure-resources/overview.md) 을 참조 하세요.

- [**감사 로그**](../active-directory/reports-monitoring/concept-audit-logs.md)-사용자 및 그룹 관리, 관리 되는 응용 프로그램 및 디렉터리 작업과 관련 된 시스템 작업에 대 한 정보를 포함 합니다.

- Azure AD 프로 비전 서비스에서 프로 비전 한 사용자, 그룹 및 역할에 대 한 시스템 활동 정보를 포함 하는 [**로그 프로 비전**](../active-directory/reports-monitoring/concept-provisioning-logs.md) ( **미리 보기** 에서도). 

> [!IMPORTANT]
> 위에서 설명한 것 처럼 일부 사용 가능한 로그 유형은 현재 **미리 보기** 상태입니다. 베타, 미리 보기 또는 아직 일반 공급으로 출시 되지 않은 Azure 기능에 적용 되는 추가 약관은 [Microsoft Azure 미리 보기에](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) 대 한 추가 사용 약관을 참조 하세요.
## <a name="prerequisites"></a>필수 구성 요소

- Azure AD 라이선스 (Free/O365/P1/P2)는 모두 Azure 센티널로 로그인 로그를 수집 하는 데 충분 합니다. Azure Monitor (Log Analytics) 및 Azure 센티널에 대해 추가 기가바이트 요금이 적용 될 수 있습니다.

- 작업 영역에서 사용자에 게 Azure 센티널 참가자 역할을 할당 해야 합니다.

- 사용자에 게 로그를 스트리밍할 테 넌 트의 전역 관리자 또는 보안 관리자 역할이 할당 되어야 합니다.

- 연결 상태를 볼 수 있으려면 사용자에 게 Azure AD 진단 설정에 대 한 읽기 및 쓰기 권한이 있어야 합니다. 

## <a name="connect-to-azure-active-directory"></a>Azure Active Directory에 연결

1. Azure 센티널의 탐색 메뉴에서 **데이터 커넥터** 를 선택 합니다.

1. 데이터 커넥터 갤러리에서 **Azure Active Directory** 을 선택한 다음 **커넥터 페이지 열기** 를 선택 합니다.

1. Azure 센티널 (위 참조)로 스트리밍할 로그 유형 옆에 있는 확인란을 표시 하 고 **연결** 을 클릭 합니다.

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
- [데이터 및 잠재적 위협에 대 한 가시성을 확보](quickstart-get-visibility.md)하는 방법을 알아보세요.
- [Azure Sentinel을 사용하여 위협 검색](tutorial-detect-threats-built-in.md)을 시작합니다.
