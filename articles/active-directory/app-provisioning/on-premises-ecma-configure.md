---
title: Azure AD ECMA 커넥터 호스트 구성
description: 이 문서에서는 Azure AD ECMA 커넥터 호스트를 구성하는 방법을 설명합니다.
services: active-directory
author: billmath
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 06/04/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: cad38ff8c0dc18d363773783e95e544d67f55193
ms.sourcegitcommit: ff1aa951f5d81381811246ac2380bcddc7e0c2b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/07/2021
ms.locfileid: "111570451"
---
# <a name="configure-the-azure-ad-ecma-connector-host-and-the-provisioning-agent"></a>Azure AD ECMA 커넥터 호스트 및 프로비저닝 에이전트를 구성합니다.

>[!IMPORTANT]
> 온-프레미스 프로비저닝 미리 보기는 현재 초대 전용 미리 보기로 제공됩니다. [여기](https://aka.ms/onpremprovisioningpublicpreviewaccess)에서 기능에 대한 액세스를 요청할 수 있습니다. 일반 공급을 준비 중이므로 향후 몇 개월 동안 더 많은 고객 및 커넥터에 대한 미리 보기를 공개할 예정입니다.

이 문서에서는 Azure AD ECMA 커넥터 호스트와 프로비저닝 에이전트를 성공적으로 설치한 후 구성하는 방법에 대한 지침을 제공합니다.

Azure AD ECMA 커넥터 호스트 설치 및 구성은 프로세스입니다.  다음 흐름을 사용하여 프로세스를 안내할 수 있습니다.

 ![설치 흐름](./media/on-premises-ecma-configure/flow-1.png)  

설치 및 구성에 대한 자세한 내용은 다음을 참조하세요.
   - [Azure AD ECMA 커넥터 호스트의 필수 구성 요소](on-premises-ecma-prerequisites.md)
   - [Azure AD ECMA 커넥터 호스트 설치](on-premises-ecma-install.md)
    - [Azure AD ECMA 커넥터 호스트 일반 SQL 커넥터 구성](on-premises-sql-connector-configure.md)
## <a name="configure-the-azure-ad-ecma-connector-host"></a>Azure AD ECMA 커넥터 호스트 구성
Azure AD ECMA 커넥터 호스트 구성은 두 부분으로 진행됩니다.
    
   - **설정 구성** - Azure AD ECMA 커넥터 호스트에서 사용할 포트 및 인증서를 구성합니다.  이 작업은 ECMA 커넥터 호스트가 처음 시작될 때만 수행됩니다.
   - **커넥터 만들기** - 커넥터(예: SQL 또는 LDAP)를 만들어 Azure AD ECMA 커넥터 호스트가 데이터 원본으로 데이터를 내보내거나 가져올 수 있도록 합니다.

### <a name="configure-the-settings"></a>설정 구성
Azure AD ECMA 커넥터 호스트를 처음 시작할 때 기본 8585를 사용하여 이미 필터링된 포트 번호가 표시됩니다.  

 ![설정 구성](.\media\on-premises-ecma-configure\configure-1.png)

미리 보기의 경우에는 자체 서명된 새 인증서를 생성해야 합니다.

 >[!NOTE]
 >이 미리 보기는 기간이 제한된 인증서를 사용합니다. 자동 생성된 인증서는 자체 서명되고, 신뢰할 수 있는 루트의 일부이며, SAN은 호스트 이름과 일치합니다.


### <a name="create-a-connector"></a>커넥터 만들기
이제 Azure AD ECMA 커넥터 호스트에서 사용할 커넥터를 만들어야 합니다.  이 커넥터를 사용하면 ECMA 커넥터 호스트에서 사용자가 만든 커넥터에 대한 데이터 원본으로 데이터를 내보내고 원할 경우 가져올 수 있습니다.  

각 개별 커넥터에 대한 구성 단계는 더 길어지며 자체 문서에 제공됩니다.

아래 링크 중 하나를 사용하여 커넥터를 만들고 구성합니다.

- [일반 SQL 커넥터](on-premises-sql-connector-configure.md) - Microsoft SQL 또는 MySQL과 같은 SQL 데이터베이스에 작용하는 커넥터입니다.


## <a name="establish-connectivity-between-azure-ad-and-the-azure-ad-ecma-connector-host"></a>Azure AD와 Azure AD ECMA 커넥터 호스트 간 연결 설정
다음 섹션에서는 온-프레미스 Azure AD ECMA 커넥터 호스트 및 Azure AD와의 연결을 설정하는 과정을 안내합니다.

#### <a name="ensure-ecma2host-service-is-running"></a>ECMA2Host 서비스가 실행되고 있는지 확인
1.  Azure AD ECMA 커넥터 호스트를 실행하는 서버에서 시작을 클릭합니다.
2. 실행을 입력하고 상자에 services.msc를 입력합니다.
3. 서비스에서 **Microsoft ECMA2Host** 가 있고 실행 중인지 확인합니다.  그렇지 않은 경우 **시작** 을 클릭합니다.
 ![서비스가 실행 중입니다.](.\media\on-premises-ecma-configure\configure-2.png)

#### <a name="add-enterprise-application"></a>엔터프라이즈 애플리케이션 추가
1.  애플리케이션 관리자 권한으로 Azure Portal에 로그인합니다.
2. 포털에서 Azure Active Directory, **엔터프라이즈 애플리케이션** 으로 이동합니다.
3. **새 애플리케이션** 을 클릭합니다.
 ![새 애플리케이션 추가](.\media\on-premises-ecma-configure\configure-4.png)
4. 갤러리에서 "온-프레미스 프로비저닝" 애플리케이션을 찾고 **만들기** 를 클릭합니다.

### <a name="configure-the-application-and-test"></a>애플리케이션 구성 및 테스트
  1. 만든 후에는 해당 **프로비저닝 페이지** 를 클릭합니다.
  2. **시작** 을 클릭합니다.
 ![시작](.\media\on-premises-ecma-configure\configure-6.png)
  3. **프로비저닝 페이지** 에서 모드를 **자동**
   ![변경 모드](.\media\on-premises-ecma-configure\configure-7.png)로 변경합니다.
  4. 온-프레미스 연결 섹션에서 방금 배포한 에이전트를 선택하고 에이전트 할당을 클릭합니다.
     ![에이전트 할당](.\media\on-premises-ecma-configure\configure-8.png)</br>

  >[!NOTE]
  >에이전트를 추가한 후 등록이 완료되기까지 10~20분 정도 기다려야 합니다.  연결 테스트는 등록이 완료될 때까지 작동하지 않습니다.
  >
  >또는 서버에서 프로비저닝 에이전트를 다시 시작하여 에이전트 등록을 강제로 완료할 수 있습니다. 서버로 이동한 후 Windows 검색 표시줄에서 서비스를 검색하고 Azure AD Connect 프로비저닝 에이전트 서비스를 확인한 후 서비스를 마우스 오른쪽 단추로 클릭하고 다시 시작을 클릭합니다.
  

  5.  10분 후에 **관리자 자격 증명** 섹션에서 다음 URL을 입력하고 "connectorName" 부분을 ECMA 호스트의 커넥터 이름으로 바꿉니다.

      |속성|값|
      |-----|-----|
      |테넌트 URL|https://localhost:8585/ecma2host_connectorName/scim|

  6. 커넥터를 만들 때 정의한 비밀 토큰 값을 입력합니다.
  7. 연결 테스트를 클릭하고 1분 동안 기다립니다.
     ![연결 테스트](.\media\on-premises-ecma-configure\configure-5.png)

     >[!NOTE]
     >연결을 테스트하려면 에이전트를 할당한 후 10-20분 정도 기다려야 합니다.  등록이 완료되지 않은 경우에는 연결이 실패합니다.
  8. 연결 테스트가 완료되면 **저장** 을 클릭합니다.</br>
   ![성공한 테스트](.\media\on-premises-ecma-configure\configure-9.png)

## <a name="configure-who-is-in-scope-for-provisioning"></a>프로비저닝 범위에 있는 사용자 구성
Azure AD ECMA 커넥터 호스트가 Azure AD와 통신하고 있으므로 계속해서 프로비저닝 범위에 있는 사용자를 구성할 수 있습니다.  다음 섹션에서는 사용자의 범위를 지정하는 방법에 대한 정보를 제공합니다.

### <a name="assign-users-to-your-application"></a>애플리케이션에 사용자 할당
Azure AD를 사용하면 애플리케이션에 대한 할당을 기준으로 또는 특정 특성에 대해 필터링하여 프로비저닝해야 하는 사용자의 범위를 지정할 수 있습니다. 프로비저닝 범위에 있는 사용자를 결정하고 필요에 따라 범위 지정 규칙을 정의합니다.  자세한 내용은 [Azure Active Directory에서 앱에 대한 사용자 할당 관리](../../active-directory/manage-apps/assign-user-or-group-access-portal.md)를 참조하세요.

### <a name="configure-your-attribute-mappings"></a>특성 매핑 구성
Azure AD의 사용자 특성을 대상 애플리케이션의 특성에 매핑해야 합니다. Azure AD 프로비저닝 서비스는 프로비저닝을 위해 SCIM 표준을 사용하므로 표시되는 특성에는 SCIM 이름 공간이 있습니다. 아래 예제에서는 Azure AD의 mail 및 objectId 특성을 애플리케이션의 Email 및 InternalGUID 특성에 매핑하는 방법을 보여 줍니다. 

>[!NOTE]
>기본 매핑에는 특성 이름 PLACEHOLDER에 대한 userPrincipalName이 포함됩니다. PLACEHOLDER 특성을 애플리케이션에 있는 특성으로 변경해야 합니다. 자세한 내용은 [원본 및 대상 시스템의 사용자 일치](customize-application-attributes.md#matching-users-in-the-source-and-target--systems)를 참조하세요.

|Azure AD의 특성 이름|SCIM의 특성 이름|대상 애플리케이션의 특성 이름|
|-----|-----|-----|
|mail|urn:ietf:params:scim:schemas:extension:ECMA2Host:2.0:User:Email|Email|
|objectId|urn:ietf:params:scim:schemas:extension:ECMA2Host:2.0:User:InternalGUID|InternalGUID|

#### <a name="configure-attribute-mapping"></a>특성 매핑 구성
 1. Azure AD 포털의 **엔터프라이즈 애플리케이션** 에서 **프로비저닝 페이지** 를 클릭합니다.
 2. **시작** 을 클릭합니다.
 3. **매핑** 을 확장하고 **Azure Active Directory 사용자 프로비저닝**
   ![사용자 프로비저닝](.\media\on-premises-ecma-configure\configure-10.png)을 클릭합니다.
 4. **새 매핑 추가**
   ![매핑 추가](.\media\on-premises-ecma-configure\configure-11.png)를 클릭합니다.
 5. 원본 및 대상 특성을 지정하고 **확인** 을 클릭합니다.</br>
   ![특성 편집](.\media\on-premises-ecma-configure\configure-12.png)


애플리케이션에서 Azure AD로 사용자 특성을 매핑하는 방법에 대한 자세한 내용은 [자습서 - Azure Active Directory에서 SaaS 애플리케이션에 대한 사용자 프로비저닝 특성 매핑 사용자 지정](customize-application-attributes.md)을 참조하세요.

### <a name="test-your-configuration-by-provisioning-users-on-demand"></a>사용자에 대해 주문형 프로비저닝을 수행하여 구성 테스트
구성을 테스트하기 위해 사용자의 주문형 프로비저닝을 사용할 수 있습니다.  주문형 사용자 프로비저닝에 대한 자세한 내용은 [주문형 프로비저닝](provision-on-demand.md)을 참조하세요.

 1. Single Sign-On 블레이드로 이동한 다음, 프로비저닝 블레이드로 돌아갑니다. 새 프로비저닝 개요 블레이드에서 주문형을 클릭합니다.
 2. [여기](provision-on-demand.md)에 설명된 대로 몇 명의 사용자에 대해 주문형 프로비저닝을 테스트합니다.
   ![프로비저닝 테스트](.\media\on-premises-ecma-configure\configure-13.png)

### <a name="start-provisioning-users"></a>사용자 프로비저닝 시작
 1. 주문형 프로비저닝이 성공하면 프로비저닝 구성 페이지로 다시 변경합니다. 범위가 할당된 사용자 및 그룹으로만 설정되어 있는지 확인하고 **프로비저닝을 켜기** 로 전환한 후 **저장** 을 클릭합니다.
   ![프로비저닝 시작](.\media\on-premises-ecma-configure\configure-14.png)
  2.  프로비저닝이 시작될 때까지 몇 분 정도 기다립니다(최대 40분이 걸릴 수 있음). 여기서 프로비저닝 서비스 성능에 대해 자세히 알아볼 수 있습니다. 프로비저닝 작업이 완료된 후 다음 섹션에 설명된 대로 프로비저닝 상태를 끄기로 변경하고 저장을 클릭합니다. 이렇게 하면 프로비저닝 서비스가 나중에 실행될 수 없습니다.

### <a name="verify-users-have-been-successfully-provisioned"></a>사용자가 성공적으로 프로비저닝되었는지 확인
대기한 후 데이터 원본을 확인하여 새 사용자가 프로비저닝되고 있는지 확인합니다.
 ![사용자가 프로비저닝되었는지 확인](.\media\on-premises-ecma-configure\configure-15.png)

## <a name="monitor-your-deployment"></a>배포 모니터링

1. 프로비저닝 로그를 사용하여 어떤 사용자가 성공적으로 프로비저닝되었는지 여부를 확인합니다.
2. Azure Monitor 통합을 사용하여 사용자 지정 경고, 대시보드 및 쿼리를 빌드합니다.
3. 프로비저닝 구성이 비정상 상태로 보이면 애플리케이션이 격리됩니다. 격리 상태에 대한 자세한 내용은 여기를 참조하세요.

## <a name="next-steps"></a>다음 단계

- [Azure AD ECMA 커넥터 호스트 필수 조건](on-premises-ecma-prerequisites.md)
- [Azure AD ECMA 커넥터 호스트 설치](on-premises-ecma-install.md)
- [일반 SQL 커넥터](on-premises-sql-connector-configure.md)