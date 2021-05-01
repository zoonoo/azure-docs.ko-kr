---
title: Azure Sentinel에 Azure AD Identity Protection 데이터 연결
description: Azure AD Identity Protection에서 Azure Sentinel로 로그 및 경고를 스트림하여 대시보드를 보고, 사용자 지정 경고를 만들고, 조사를 개선하는 방법에 대해 알아봅니다.
author: yelevin
manager: rkarlin
ms.assetid: 91c870e5-2669-437f-9896-ee6c7fe1d51d
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: how-to
ms.date: 06/24/2020
ms.author: yelevin
ms.openlocfilehash: f420e80f10072c440f5401c042d6370a061b1a7f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98632244"
---
# <a name="connect-data-from-azure-active-directory-azure-ad-identity-protection"></a>Azure AD(Azure Active Directory) Identity Protection에서 데이터 연결

[Azure AD Identity Protection](../active-directory/identity-protection/overview-identity-protection.md)에서 Azure Sentinel로 로그 및 경고를 스트림하여 대시보드를 보고, 사용자 지정 경고를 만들고, 조사를 개선할 수 있습니다. Azure Active Directory Identity Protection은 위험에 노출된 사용자, 위험 감지 및 취약성에 대한 통합 보기를 제공하며, 위험을 즉시 수정하고 향후 이벤트를 자동으로 수정하도록 정책을 설정하는 기능이 있습니다. 이 서비스는 고객 ID를 보호하는 Microsoft의 경험을 토대로 구축되었으며 하루에 130억개가 넘는 로그인의 신호를 바탕으로 놀라운 정확성을 자랑합니다. 

## <a name="prerequisites"></a>사전 요구 사항

- [Azure AD Premium P2 구독](https://azure.microsoft.com/pricing/details/active-directory/)이 있어야 합니다.
- 전역 관리자 또는 보안 관리자 권한이 있는 사용자가 있어야 합니다.

## <a name="connect-to-azure-ad-identity-protection"></a>Azure AD Identity Protection 연결

Azure AD Premium P2 구독이 있는 경우 Azure AD Identity Protection이 포함되어 있습니다. [정책을 사용하도록 설정](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)하여 경고를 생성하는 경우 경고 데이터를 쉽게 Azure Sentinel로 스트림할 수 있습니다.

1. Azure Sentinel에서 **데이터 커넥터** 를 선택한 다음 **Azure AD Identity Protection** 타일을 클릭합니다.

1. **연결** 을 클릭하여 Azure Sentinel로 Azure AD Identity Protection 이벤트를 스트림하도록 합니다.

1. Azure AD Identity Protection 경고에 대한 로그 분석에서 관련 스키마를 사용하려면 **SecurityAlert** 를 검색합니다.

커넥터를 테스트하려는 경우, [검색을 시뮬레이션](../active-directory/identity-protection/howto-identity-protection-simulate-risk.md)하여 Azure Sentinel로 스트림되는 샘플 경고를 생성할 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure AD Identity Protection을 Azure Sentinel에 연결하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- [데이터에 대한 가시성을 얻고 재적 위협을 확인](quickstart-get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](tutorial-detect-threats-built-in.md)을 시작합니다.
