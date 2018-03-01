---
title: "Azure AD Connect Health 및 일반 데이터 보호 규정 | Microsoft Docs"
description: "이 문서에서는 Azure AD Connect를 사용하여 GDPR을 준수하는 방법에 대해 설명합니다."
services: active-directory
documentationcenter: 
author: billmath
manager: mtillman
editor: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/18/2018
ms.author: billmath
ms.openlocfilehash: b9a0b9027bbead00300040186e453933b3a7f46b
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/21/2018
---
# <a name="gdpr-compliance-and-azure-ad-connect-health"></a>GDPR 규정 준수 및 Azure AD Connect Health 

[GDPR(일반 데이터 보호 규정)](http://ec.europa.eu/justice/data-protection/reform/index_en.htm)은 EU(유럽 연합)의 데이터 보호 및 프라이버시 법률입니다. GDPR은 회사, 정부 기관, 비영리 단체 및 기타 조직에 대해 상품 및 서비스를 제공하거나 EU 거주자와 연결된 데이터를 수집하고 분석하는 새 규칙을 수립합니다. 

오늘날, Microsoft 제품 및 서비스는 GDPR 요구 사항을 충족하는 데 도움이 될 수 있습니다. [보안 센터](https://www.microsoft.com/trustcenter)에서 Microsoft 개인 정보 취급 방침에 대해 자세히 알아보세요.

Azure AD Connect Health는 온 - 프레미스 ID 인프라 및 동기화 서비스를 모니터링합니다. 또한 인사이트 및 표면 경고도 제공합니다. Microsoft는 2018년 5월 GDPR 시행에 맞춰 클라우드 서비스 전체에서 GDPR 규정을 준수하고 계약서에 GDPR 관련 보증을 제공하기 위해 노력하고 있습니다. 

>[!NOTE] 
> 이 문서에서는 Azure AD Connect Health의 GDPR 준수에 대해 간략하게 설명합니다. Azure AD Connect에서 GDPR 준수에 대한 자세한 내용은 [GDPR 준수 및 Azure AD Connect](../../active-directory/connect/active-directory-aadconnect-gdpr.md)를 참조하세요.

## <a name="gdpr-classification"></a>GDPR 분류
Azure AD Connect Health는 GDPR 분류의 **데이터 프로세서** 범주에 해당합니다. 이 서비스는 데이터 프로세서 파이프라인으로, 주요 파트너 및 최종 소비자에게 데이터 처리 서비스를 제공합니다. Azure AD Connect Health는 사용자 데이터를 생성하지 않으며 개인 데이터가 수집되는 방법 및 해당 데이터의 사용 방법을 독립적으로 제어하지 않습니다. Azure AD Connect Health의 데이터 검색, 집계, 분석 및 보고는 기존 온-프레미스 데이터를 기반으로 합니다. 

## <a name="data-retention-policy"></a>데이터 보존 정책
Azure AD Connect Health는 보고서를 생성하거나, 분석을 수행하거나, 30일이 지난 정보를 제공하지 않습니다. 따라서 Azure AD Connect Health는 30일이 지난 데이터를 저장, 처리 또는 유지하지 않습니다. 이러한 설계는 GDPR 규정, Microsoft 개인 정보 보호 준수 규정 및 Azure AD 데이터 보존 정책을 준수합니다. 
 
## <a name="disable-data-collection-and-monitoring-in-azure-ad-connect-health"></a>Azure AD Connect Health에서 데이터 수집 및 모니터링 사용 안 함
Azure AD Connect Health를 사용하면 모니터링되는 개별 서버 또는 모니터링되는 서비스의 인스턴스에 대한 데이터 수집을 중지할 수 있습니다. 예를 들어 Azure AD Connect Health를 사용하여 모니터링되는 개별 ADFS(Active Directory Federation Services) 서버의 데이터 수집을 중지할 수 있습니다. 또한 Azure AD Connect Health를 사용하여 모니터링 중인 전체 ADFS 인스턴스의 데이터 수집을 중지할 수도 있습니다. 중지하도록 선택하면 데이터 수집을 중지한 후 Azure AD Connect Health 포털에서 해당 서버가 삭제됩니다. 

>[!IMPORTANT]
> Azure AD Connect Health에서 모니터링되는 서버를 삭제하려면 RBAC에서 Azure AD 전역 관리자 권한 또는 참가자 역할 중 하나가 필요합니다.
>
> Azure AD Connect Health에서 서버 또는 서비스 인스턴스를 제거하는 작업은 취소할 수 없습니다. 

### <a name="what-to-expect"></a>고려해야 할 사항
모니터링되는 개별 서버 또는 모니터링되는 서비스의 인스턴스에 대한 데이터 수집 및 모니터링을 중지하는 경우, 다음 사항에 주의하세요.

- 모니터링되는 서비스의 인스턴스를 삭제하면 해당 인스턴스가 포털의 Azure AD Connect Health 모니터링 서비스 목록에서 제거됩니다. 
- 모니터링되는 서버 또는 모니터링되는 서비스의 인스턴스를 삭제하면 서버에서 상태 에이전트가 제거되거나 제거되지 않습니다. 상태 에이전트는 Azure AD Connect Health에 데이터를 전송하지 않도록 구성되어 있습니다. 상태 에이전트는 이전에 모니터링한 서버에서 수동으로 제거해야 합니다.
- 이 단계를 수행하기 전에 Health Agent를 제거하지 않은 경우 Health Agent와 관련된 서버에 오류 이벤트가 표시될 수 있습니다.
- 모니터링되는 서비스의 인스턴스에 속한 모든 데이터는 Microsoft Azure 데이터 보존 정책에 따라 삭제됩니다.

### <a name="disable-data-collection-and-monitoring-for-a-monitored-server"></a>모니터링되는 서버에 대한 데이터 수집 및 모니터링을 사용하지 않도록 설정
[Azure AD Connect Health에서 서버를 제거하는 방법](active-directory-aadconnect-health-operations.md#to-delete-a-server-from-the-azure-ad-connect-health-service)을 참조하세요.

### <a name="disable-data-collection-and-monitoring-for-an-instance-of-a-monitored-service"></a>모니터링되는 서비스의 인스턴스에 대한 데이터 수집 및 모니터링을 사용하지 않도록 설정
[Azure AD Connect Health에서 서비스 인스턴스를 제거하는 방법](active-directory-aadconnect-health-operations.md#delete-a-service-instance-from-azure-ad-connect-health-service)을 참조하세요.


## <a name="re-enable-data-collection-and-monitoring-in-azure-ad-connect-health"></a>Azure AD Connect Health에서 데이터 수집 및 모니터링을 다시 사용하도록 설정
Azure AD Connect Health에서 이전에 삭제된 모니터링되는 서비스에 대한 모니터링을 다시 사용하도록 설정하려면 모든 서버에서 [상태 에이전트를 제거한 후 다시 설치](active-directory-aadconnect-health-agent-install.md)해야 합니다.


## <a name="next-steps"></a>다음 단계
* [보안 센터에서 Microsoft 개인 정보 취급 방침을 검토합니다.](https://www.microsoft.com/trustcenter)
* [Azure AD Connect 및 GDPR](../../active-directory/connect/active-directory-aadconnect-gdpr.md)
* [Azure AD Connect Health 작업](active-directory-aadconnect-health-operations.md)
