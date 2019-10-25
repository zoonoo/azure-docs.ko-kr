---
title: Azure 센티널에 Azure AD ID 보호 데이터 연결 | Microsoft Docs
description: Azure 센티널에 Azure AD ID 보호 데이터를 연결 하는 방법에 대해 알아봅니다.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 91c870e5-2669-437f-9896-ee6c7fe1d51d
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: rkarlin
ms.openlocfilehash: 5dae8c4da46c750f69057f33d593f5bb7396a99e
ms.sourcegitcommit: 7efb2a638153c22c93a5053c3c6db8b15d072949
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/24/2019
ms.locfileid: "72882248"
---
# <a name="connect-data-from-azure-ad-identity-protection"></a>Azure AD ID 보호에서 데이터 연결



[Azure AD ID 보호](https://docs.microsoft.com/azure/information-protection/reports-aip) 에서 azure 센티널로 로그를 스트리밍하 여 대시보드를 보고, 사용자 지정 경고를 만들고, 조사를 개선할 수 있습니다. Azure Active Directory Identity Protection 위험 사용자, 위험 검색 및 취약성에 대 한 통합 보기를 제공 하며, 위험을 즉시 수정 하 고 향후 이벤트를 자동 수정 하도록 정책을 설정할 수 있습니다. 이 서비스는 고객 id를 보호 하는 Microsoft의 경험을 바탕으로 구축 되었으며 하루에 130억 대 이상의 로그에서 발생 하는 신호의 정확성을 향상 시킵니다. 


## <a name="prerequisites"></a>전제 조건

- [Azure Active Directory Premium P1 또는 P2 라이선스가](https://azure.microsoft.com/pricing/details/active-directory/) 있어야 합니다.
- 전역 관리자 또는 보안 관리자 권한이 있는 사용자


## <a name="connect-to-azure-ad-identity-protection"></a>Azure AD ID 보호에 연결

Azure AD ID 보호 이미 있는 경우 [네트워크에서 사용 하도록 설정](../active-directory/identity-protection/overview-identity-protection.md)되었는지 확인 합니다.
Azure AD ID 보호 배포 되 고 데이터를 가져오는 경우 경고 데이터를 쉽게 Azure 센티널로 스트리밍할 수 있습니다.


1. Azure 센티널에서 **데이터 커넥터** 를 선택한 다음 **Azure AD ID 보호** 타일을 클릭 합니다.

2. **연결** 을 클릭 하 여 Azure 센티널로 Azure AD ID 보호 이벤트 스트리밍을 시작 합니다.


6. Azure AD ID 보호 경고에 대 한 Log Analytics에서 관련 스키마를 사용 하려면 **IdentityProtectionLogs_CL**를 검색 합니다.

## <a name="next-steps"></a>다음 단계
이 문서에서는 Azure AD ID 보호를 Azure 센티널에 연결 하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- [데이터 및 잠재적 위협에 대 한 가시성을 얻는](quickstart-get-visibility.md)방법에 대해 알아봅니다.
- [Azure 센티널로 위협 검색을](tutorial-detect-threats-built-in.md)시작 합니다.
