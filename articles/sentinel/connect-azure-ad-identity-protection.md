---
title: Azure Sentinel 미리 보기에서 Azure AD Id 보호 데이터 수집 | Microsoft Docs
description: Azure Sentinel에서 Azure AD Id 보호 데이터를 수집 하는 방법에 알아봅니다.
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 91c870e5-2669-437f-9896-ee6c7fe1d51d
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: 609aced38b7e30f78d81934867196c568dcc85ca
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/02/2019
ms.locfileid: "57240001"
---
# <a name="collect-data-from-azure-ad-identity-protection"></a>Azure AD Id 보호에서 데이터를 수집 합니다.

> [!IMPORTANT]
> Azure Sentinel 현재 공개 미리 보기 중입니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

로그를 스트리밍하려면 [Azure AD Id 보호](https://docs.microsoft.com/azure/information-protection/reports-aip) Azure 대시보드 보기, 사용자 지정 경고를 만들고, 조사가 향상 Sentinel로 스트림 경고를 Azure Sentinel에 합니다. Azure Active Directory Id 보호 위험을 즉시 수정 하 고 나중에 이벤트가 자동으로 해결 하는 정책을 설정 하는 기능을 사용 하 여 위험 사용자, 위험 이벤트 및 취약성에 통합된 된 뷰를 제공 합니다. 서비스는 고객 id를 보호 하는 Microsoft의 경험을 토대로 구축 되었으며에서 신호를 하루 13 십억 로그인 바탕으로 해 놀라울 정도의 정확성. 


## <a name="prerequisites"></a>필수 조건

- 있어야는 [Azure Active Directory Premium P1 또는 P2 라이선스](https://azure.microsoft.com/pricing/details/active-directory/)
- 전역 관리자 또는 보안 관리자 권한이 있는 사용자


## <a name="connect-to-azure-ad-identity-protection"></a>Azure AD Id 보호 연결

Azure AD Id 보호에 이미 있는 경우 인지 확인 [네트워크에서 사용 하도록 설정](../active-directory/identity-protection/enable.md)합니다.
Azure AD Identity Protection을 배포 하 고 데이터 가져오기, 경고 데이터를 쉽게 수행할 수 있으면 Azure Sentinel로 스트림할 수 있습니다.


1. Azure Sentinel 선택 **데이터 컬렉션** 클릭 하 고는 **Azure AD Id 보호** 바둑판식으로 배열 합니다.

2. 클릭 **Connect** Azure Sentinel에 Azure AD Id 보호 이벤트를 스트리밍하기 시작 합니다.


6. Log Analytics에서 관련 스키마를 사용 하 여 Azure AD Id 보호 경고, 검색 **IdentityProtectionLogs_CL**합니다.

## <a name="next-steps"></a>다음 단계
이 문서에서는 Azure Sentinel에 Azure AD Id 보호를 연결 하는 방법을 알아보았습니다. Azure에서 감시 하는 방법에 대 한 자세한 내용은 다음 문서를 참조 합니다.
- 에 대해 알아봅니다 하는 방법 [데이터에 잠재적 위협을 파악](quickstart-get-visibility.md)합니다.
- 시작 [사용 하 여 Azure Sentinel 위협을 감지 하도록](tutorial-detect-threats.md)합니다.
