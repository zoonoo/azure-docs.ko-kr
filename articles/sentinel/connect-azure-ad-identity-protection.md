---
title: Azure AD ID 보호 데이터를 Azure Sentinel에 연결
description: Azure AD ID 보호 데이터를 Azure Sentinel에 연결하는 방법을 알아봅니다.
author: yelevin
manager: rkarlin
ms.assetid: 91c870e5-2669-437f-9896-ee6c7fe1d51d
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.date: 11/17/2019
ms.author: yelevin
ms.openlocfilehash: 7d42ff28ddd2d883feb25139096d781efe64d50f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588572"
---
# <a name="connect-data-from-azure-ad-identity-protection"></a>Azure AD ID 보호의 데이터 연결



[Azure AD ID 보호에서](https://docs.microsoft.com/azure/information-protection/reports-aip) Azure Sentinel로 로그를 스트리밍하여 Azure Sentinel로 경고를 스트리밍하여 대시보드를 보고, 사용자 지정 경고를 만들고, 조사를 개선할 수 있습니다. Azure Active Directory ID 보호는 위험 사용자, 위험 검색 및 취약점에 대한 통합 보기를 제공하며, 위험을 즉시 수정하고 향후 이벤트를 자동으로 수정하도록 정책을 설정할 수 있습니다. 이 서비스는 소비자 신원을 보호하는 Microsoft의 경험을 바탕으로 구축되었으며 하루에 130억 개 이상의 로그인으로 신호로부터 엄청난 정확도를 얻을 수 있습니다. 


## <a name="prerequisites"></a>사전 요구 사항

- Azure Active [Directory 프리미엄 P1 또는 P2 라이선스가](https://azure.microsoft.com/pricing/details/active-directory/) 있어야 합니다.
- 글로벌 관리자 또는 보안 관리자 권한이 있는 사용자


## <a name="connect-to-azure-ad-identity-protection"></a>Azure AD ID 보호에 연결

Azure AD ID 보호기능이 이미 있는 경우 네트워크에서 활성화되어 있는지 [확인합니다.](../active-directory/identity-protection/overview-identity-protection.md)
Azure AD ID 보호가 배포되고 데이터를 가져오는 경우 경고 데이터를 Azure Sentinel으로 쉽게 스트리밍할 수 있습니다.


1. Azure Sentinel에서 **데이터 커넥터를** 선택한 다음 **Azure AD ID 보호** 타일을 클릭합니다.

2. **연결을** 클릭하여 Azure AD ID 보호 이벤트를 Azure Sentinel로 스트리밍을 시작합니다.


6. Azure AD ID 보호 경고에 대한 로그 분석에서 관련 스키마를 사용하려면 **SecurityAlert**를 검색합니다.

## <a name="next-steps"></a>다음 단계
이 문서에서는 Azure AD ID 보호를 Azure Sentinel에 연결하는 방법을 배웠습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- [데이터에 대한 가시성을 얻고 재적 위협을 확인](quickstart-get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](tutorial-detect-threats-built-in.md)을 시작합니다.
