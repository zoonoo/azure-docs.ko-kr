---
title: SCIM 지원 앱으로의 Azure AD 온-프레미스 앱 프로비저닝
description: 이 문서에서는 SCIM 지원 앱에 온-프레미스 앱을 프로비저닝하는 방법을 설명합니다.
services: active-directory
author: billmath
manager: mtillman
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: conceptual
ms.workload: identity
ms.date: 05/28/2021
ms.author: billmath
ms.reviewer: arvinh
ms.openlocfilehash: bf0b13bf1e2de31d15599bff2aa8750396f021ac
ms.sourcegitcommit: ff1aa951f5d81381811246ac2380bcddc7e0c2b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/07/2021
ms.locfileid: "111570041"
---
# <a name="azure-ad-on-premises-application-provisioning-to-scim-enabled-apps"></a>SCIM 지원 앱으로의 Azure AD 온-프레미스 애플리케이션 프로비저닝

>[!IMPORTANT]
> 온-프레미스 프로비저닝 미리 보기는 현재 초대 전용 미리 보기로 제공됩니다. [여기](https://aka.ms/onpremprovisioningpublicpreviewaccess)에서 기능에 대한 액세스를 요청할 수 있습니다. 일반 공급을 준비 중이므로 향후 몇 개월 동안 더 많은 고객 및 커넥터에 대한 미리 보기를 공개할 예정입니다.

Azure AD 프로비저닝 서비스는 사용자를 클라우드 또는 온-프레미스 애플리케이션에 자동으로 프로비저닝하는 데 사용할 수 있는 [SCIM 2.0](https://techcommunity.microsoft.com/t5/identity-standards-blog/provisioning-with-scim-getting-started/ba-p/880010) 클라이언트를 지원합니다. 이 문서에서는 Azure AD 프로비저닝 서비스를 사용하여 SCIM이 지원되는 온-프레미스 애플리케이션에 사용자를 프로비저닝하는 방법을 간략하게 설명합니다. 비 AD LDAP 디렉터리 또는 SQL DB와 같은 비 SCIM 온-프레미스 애플리케이션에 사용자를 프로비저닝하려는 경우 여기(만들어야 하는 새 문서에 대한 링크)를 참조하세요. DropBox, Atlassian 등과 같은 클라우드 앱에 사용자를 프로비저닝하려는 경우 앱별 [자습서](../../active-directory/saas-apps/tutorial-list.md)를 검토합니다. 

![아키텍처](./media/on-premises-scim-provisioning/scim-4.png)


## <a name="pre-requisites"></a>필수 구성 요소
- Azure AD Premium P1 또는 Premium P2(또는 EMS E3 또는 E5)를 사용하는 Azure AD 테넌트 
    [!INCLUDE [active-directory-p1-license.md](../../../includes/active-directory-p1-license.md)]
- 에이전트를 설치하기 위한 관리자 역할.  이것은 일회성 작업이며 하이브리드 관리자 또는 전역 관리자인 Azure 계정이어야 합니다. 
- 클라우드에서 애플리케이션을 구성하기 위한 관리자 역할(애플리케이션 관리자, 클라우드 애플리케이션 관리자, 전역 관리자, 권한이 있는 사용자 지정 역할)

## <a name="steps-for-on-premises-app-provisioning-to-scim-enabled-apps"></a>SCIM 지원 앱으로의 Azure AD 온-프레미스 앱 프로비저닝 단계
아래 단계를 사용하여 SCIM 지원 앱에 프로비저닝합니다. 

 1. [갤러리](../../active-directory/manage-apps/add-application-portal.md)에서 "에이전트 기반 SCIM 프로비저닝" 앱을 추가합니다.
 2. 앱 > 프로비저닝으로 이동한 후 프로비저닝 에이전트를 다운로드합니다.
 3. 온-프레미스 연결을 클릭하고 프로비저닝 에이전트를 다운로드합니다.
 4. SCIM 엔드포인트가 호스트되는 가상 머신 또는 서버에 에이전트를 복사합니다.
 5. 프로비저닝 에이전트 설치 관리자를 열고 서비스 약관에 동의한 다음, 설치를 클릭합니다.
 6. 프로비저닝 에이전트 마법사를 열고 사용하도록 설정할 확장을 묻는 메시지가 표시되면 온-프레미스 프로비저닝을 선택합니다.
 7. 권한을 부여하라는 메시지가 표시되면 Azure AD 관리자에 대한 자격 증명을 제공합니다(하이브리드 관리자 또는 전역 관리자 필요).
 8. 확인을 클릭하여 설치에 성공했음을 확인합니다.
 9. 애플리케이션 > 온-프레미스 연결로 다시 이동합니다.
 10. 설치한 에이전트를 드롭다운 목록에서 선택하고 에이전트 할당을 클릭합니다.
 11. 10분 정도 기다리거나 서버/VM에서 Azure AD Connect 프로비저닝 에이전트 서비스를 다시 시작합니다.
 12. 테넌트 URL 필드에서 SCIM 엔드포인트의 URL을 제공합니다(예: Https://localhost:8585/scim)).
     ![에이전트 할당](./media/on-premises-scim-provisioning/scim-2.png)
 13. 연결 테스트를 클릭하고 자격 증명을 저장합니다.
 14. 애플리케이션에 필요한 [특성 매핑](customize-application-attributes.md) 또는 [범위 지정](define-conditional-rules-for-provisioning-user-accounts.md) 규칙을 구성합니다.  
 15. 애플리케이션에 [사용자 및 그룹을 할당](../../active-directory/manage-apps/add-application-portal-assign-users.md)하여 범위에 사용자를 추가합니다.
 16. 몇 명의 사용자를 대상으로 [주문형](provision-on-demand.md) 프로비저닝을 테스트합니다.
 17. 애플리케이션에 사용자를 할당하여 범위에 사용자를 더 추가합니다.
 18. 프로비저닝 블레이드로 이동하고 프로비저닝 시작을 누릅니다.
 19. [프로비저닝 로그](../../active-directory/reports-monitoring/concept-provisioning-logs.md)를 사용하여 모니터링합니다.
 

## <a name="things-to-be-aware-of"></a>알아두어야 할 사항
* [SCIM](https://techcommunity.microsoft.com/t5/identity-standards-blog/provisioning-with-scim-getting-started/ba-p/880010) 구현이 Azure [AD SCIM 요구 사항](use-scim-to-provision-users-and-groups.md)을 충족하는지 확인합니다.
  * Azure AD는 개발자가 SCIM 구현을 부트스트랩하는 데 사용할 수 있는 오픈 소스 [참조 코드](https://github.com/AzureAD/SCIMReferenceCode/wiki)를 제공합니다(코드는 있는 그대로 사용함).
* /schemaDiscovery 엔드포인트를 지원하여 Azure Portal에 필요한 구성을 줄입니다. 

## <a name="next-steps"></a>다음 단계

- [앱 프로비저닝](user-provisioning.md)
- [Azure AD ECMA 커넥터 호스트 설치](on-premises-ecma-install.md)
- [Azure AD ECMA 커넥터 호스트 구성](on-premises-ecma-configure.md)
- [일반 SQL 커넥터](on-premises-sql-connector-configure.md)
- [자습서: ECMA 커넥터 호스트 일반 SQL 커넥터](tutorial-ecma-sql-connector.md)
