---
title: Azure Active Directory B2C에서 리소스 및 데이터에 대한 위협 관리
description: Azure Active Directory B2C에서 서비스 거부 공격 및 암호 공격에 대한 검색 및 완화 기법을 알아봅니다.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 7232917df6018c9c8afc7e7edd3730a277b193f4
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67798245"
---
# <a name="manage-threats-to-resources-and-data-in-azure-active-directory-b2c"></a>Azure Active Directory B2C에서 리소스 및 데이터에 대한 위협 관리

Azure AD(Azure Active Directory) B2C에는 리소스 및 데이터에 대한 위협으로부터 보호하는 데 도움이 될 수 있는 기본 제공 기능이 있습니다. 이러한 위협에는 서비스 거부 공격 및 암호 공격이 포함됩니다. 서비스 거부 공격은 의도한 사용자가 리소스를 사용할 수 없게 만듭니다. 암호 공격으로 인해 리소스에 대한 무단 액세스가 발생할 수 있습니다.

## <a name="denial-of-service-attacks"></a>서비스 거부 공격

Azure AD B2C는 SYN 쿠키를 사용하여 SYN 서비스 장애 공격을 방어합니다. Azure AD B2C는 또한 속도 및 연결 제한을 사용하여 서비스 거부 공격으로부터 보호합니다.

## <a name="password-attacks"></a>암호 공격

사용자가 설정한 암호는 적절한 복잡성을 유지해야 합니다. Azure AD B2C에는 암호 공격에 대한 완화 기술이 포함됩니다. 완화는 무차별 암호 대입 공격 및 사전 암호 공격이 검색을 포함합니다. 다양한 신호를 사용하여 Azure AD B2C는 요청의 무결성을 분석합니다. Azure AD B2C는 의도한 사용자와 해커 및 봇네트 간을 지능적으로 구분하도록 설계되었습니다.

Azure AD B2C는 좀 더 복잡한 계정 잠금 전략을 사용합니다. 계정은 요청의 IP 주소와 입력된 암호를 기반으로 잠깁니다. 또한 실패한 시도가 공격일 가능성에 따라 잠금 기간이 늘어납니다. 암호는 10 번 시도 했으나 실패 한 후 (기본 시도 임계값) 1 분 잠금이 발생 합니다. 다음에 계정 로그인이 성공한 후를 잠금 해제 (즉, 계정 된 자동으로 잠금 해제 되어 있는 서비스에서 잠금 기간이 만료 되 면), 다른 1 분 잠금 발생 하 고 실패 한 각 로그인에 대해 계속 합니다. 반복적으로 같은 암호를 입력해도 여러 번의 로그인 실패로 계산되지 않습니다.

처음 10회에 대한 잠금 기간은 1분입니다. 다음 10회 잠금 기간은 약간 더 길고 10회 잠금 기간마다 기간이 늘어납니다. 계정이 잠겨 있지 않은 상태에서 로그인에 성공하면 잠금 카운터가 0으로 다시 설정됩니다. 잠금 기간은 5시간까지 지속될 수 있습니다.

## <a name="manage-password-protection-settings"></a>암호 보호 설정 관리

잠금 임계값을 포함 하 여 암호 보호 설정을 관리 합니다.

1. [Azure Portal](https://portal.azure.com)로 이동합니다.
1. 선택 된 **디렉터리 + 구독** 포털의 오른쪽 위 메뉴에서 필터링 한 다음 Azure AD B2C 테 넌 트를 선택 합니다.
1. 선택 **Azure Active Directory** 왼쪽 메뉴에서 (누르거나 **서비스를 모두** 포털의 왼쪽 위 섹션에서 다음 검색 하 고 선택 *Azure Active Directory*).
1. 아래 **보안**를 선택 **인증 방법**을 선택한 후 **암호 보호**합니다.
1. 원하는 암호 보호 설정을 입력 하 고 선택 **저장할**합니다.

    ![Azure AD 설정에서 azure portal 암호 보호 페이지](media/active-directory-b2c-reference-threat-management/portal-02-password-protection.png)
    <br />*잠금 임계값에서 5로 설정 **암호 보호** 설정을*합니다.

## <a name="view-locked-out-accounts"></a>잠긴 계정 보기

잠긴 계정에 대 한 정보를 얻으려면 Active Directory를 확인할 수 있습니다 [로그인 활동 보고서](../active-directory/reports-monitoring/reference-sign-ins-error-codes.md)합니다. 아래 **상태**를 선택 **오류**합니다. 사용 하 여 로그인 시도 실패를 **로그인 오류 코드** 의 `50053` 잠긴된 계정을 나타냅니다.

![Azure AD 로그인 보고서의 잠긴 계정을 보여 주는 섹션](media/active-directory-b2c-reference-threat-management/portal-01-locked-account.png)

Azure Active Directory에서의 로그인 활동 보고서를 보는 방법에 알아봅니다, 참조 [로그인 활동 보고서 오류 코드](../active-directory/reports-monitoring/reference-sign-ins-error-codes.md)합니다.
