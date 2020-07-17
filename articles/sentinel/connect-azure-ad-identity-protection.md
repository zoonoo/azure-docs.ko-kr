---
title: Azure 센티널에 Azure AD ID 보호 데이터 연결
description: Azure AD ID 보호에서 Azure 센티널로 로그 및 경고를 스트리밍하 여 대시보드를 보고, 사용자 지정 경고를 만들고, 조사를 개선 하는 방법에 대해 알아봅니다.
author: yelevin
manager: rkarlin
ms.assetid: 91c870e5-2669-437f-9896-ee6c7fe1d51d
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: how-to
ms.date: 06/24/2020
ms.author: yelevin
ms.openlocfilehash: 69ab76bf213653ea10db8dfd181b615a7e0f47b5
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85564473"
---
# <a name="connect-data-from-azure-active-directory-azure-ad-identity-protection"></a>Azure Active Directory (Azure AD) Id 보호의 데이터 연결

[Azure AD ID 보호](../active-directory/identity-protection/overview-identity-protection.md) 에서 azure 센티널로 로그를 스트리밍하 여 대시보드를 보고, 사용자 지정 경고를 만들고, 조사를 개선할 수 있습니다. Azure Active Directory Identity Protection 위험 사용자, 위험 검색 및 취약성에 대 한 통합 보기를 제공 하며, 위험을 즉시 수정 하 고 향후 이벤트를 자동 수정 하도록 정책을 설정할 수 있습니다. 이 서비스는 고객 id를 보호 하는 Microsoft의 경험을 바탕으로 구축 되었으며 하루에 130억 대 이상의 로그에서 발생 하는 신호의 정확성을 향상 시킵니다. 

## <a name="prerequisites"></a>사전 요구 사항

- [Azure AD Premium P2 구독이](https://azure.microsoft.com/pricing/details/active-directory/)있어야 합니다.
- 전역 관리자 또는 보안 관리자 권한이 있는 사용자가 있어야 합니다.


## <a name="connect-to-azure-ad-identity-protection"></a>Azure AD ID 보호에 연결

Azure AD Premium P2 구독이 있는 경우 Azure AD ID 보호 포함 됩니다. [정책을 사용 하도록 설정](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md) 하 고 경고를 생성 하는 경우 경고 데이터를 쉽게 Azure 센티널로 스트리밍할 수 있습니다.

1. Azure 센티널에서 **데이터 커넥터** 를 선택한 다음 **Azure AD ID 보호** 타일을 클릭 합니다.

1. **연결** 을 클릭 하 여 Azure 센티널로 Azure AD ID 보호 이벤트 스트리밍을 시작 합니다.

1. Azure AD ID 보호 경고에 대 한 Log Analytics에서 관련 스키마를 사용 하려면 **Securityalert**를 검색 합니다.

커넥터를 테스트 하려는 경우 검색을 [시뮬레이트하여](../active-directory/identity-protection/howto-identity-protection-simulate-risk.md) Azure 센티널로 스트리밍되는 샘플 경고를 생성할 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure AD ID 보호를 Azure 센티널에 연결 하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- [데이터에 대한 가시성을 얻고 재적 위협을 확인](quickstart-get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](tutorial-detect-threats-built-in.md)을 시작합니다.
