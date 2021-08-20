---
title: Azure AD ECMA 커넥터 호스트 일반 SQL 커넥터 자습서
description: 이 자습서에서는 온-프레미스 애플리케이션 프로비전 일반 SQL 커넥터를 사용하는 방법을 설명합니다.
services: active-directory
author: billmath
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: tutorial
ms.date: 07/13/2021
ms.subservice: hybrid
ms.author: billmath
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: a8ef75392676b2c766064eefb3c8113734bfa622
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114437540"
---
# <a name="azure-ad-ecma-connector-host-generic-sql-connector-tutorial"></a>Azure AD ECMA 커넥터 호스트 일반 SQL 커넥터 자습서

>[!IMPORTANT]
> 온-프레미스 프로비저닝 미리 보기는 현재 초대 전용 미리 보기로 제공됩니다. 기능에 대한 액세스를 요청하려면 [액세스 요청 양식](https://aka.ms/onpremprovisioningpublicpreviewaccess)을 사용하세요. 일반 공급을 준비하면서 향후 몇 개월 동안 더 많은 고객 및 커넥터에 대한 미리 보기를 공개할 예정입니다.

이 자습서에서는 사용자를 Azure AD(Azure Active Directory)에서 SQL 데이터베이스로 자동으로 프로비전 및 프로비전 해제하기 위해 수행해야 하는 단계에 대해 설명합니다. 이 서비스의 기능, 작동 방법 및 질문과 대답에 대한 중요한 내용은 [Azure Active Directory를 사용하여 SaaS 애플리케이션의 사용자를 자동으로 프로비저닝 및 프로비저닝 해제](../app-provisioning/user-provisioning.md)를 참조하세요.

이 자습서에서는 Azure AD ECMA 커넥터 호스트를 사용하여 일반 SQL 커넥터를 설정하고 사용하는 방법을 설명합니다. 

## <a name="prepare-the-sample-database"></a>샘플 데이터베이스 준비
SQL Server를 실행하는 서버에서 [부록 A](#appendix-a)에 있는 SQL 스크립트를 실행합니다. 이 스크립트는 CONTOSO라는 샘플 데이터베이스를 만듭니다. 이 데이터베이스는 사용자를 프로비전할 데이터베이스입니다.


## <a name="create-the-dsn-connection-file"></a>DSN 연결 파일 만들기
일반 SQL 커넥터는 SQL 서버에 연결하기 위한 DSN 파일입니다. 먼저 ODBC 연결 정보가 포함된 파일을 만들어야 합니다.

1. 서버에서 ODBC 관리 유틸리티를 시작합니다.
  
     ![ODBC 관리를 보여 주는 스크린샷](./media/tutorial-ecma-sql-connector/odbc.png)
1. **파일 DSN** 탭, **추가** 를 차례로 선택합니다. 
 
     ![파일 DSN 탭을 보여 주는 스크린샷](./media/tutorial-ecma-sql-connector/dsn-2.png)
1. **SQL Server Native Client 11.0**, **다음** 을 차례로 선택합니다. 
 
     ![Native Client 선택을 보여 주는 스크린샷](./media/tutorial-ecma-sql-connector/dsn-3.png)
1. **GenericSQL** 과 같은 파일 이름을 지정하고, **다음** 을 선택합니다. 
 
     ![커넥터 이름 지정을 보여 주는 스크린샷](./media/tutorial-ecma-sql-connector/dsn-4.png)
1. **마침** 을 선택합니다. 
 
     ![마침을 보여 주는 스크린샷](./media/tutorial-ecma-sql-connector/dsn-5.png)
1. 이제 연결을 구성합니다. 서버 이름에 대해 **APP1** 을 입력하고, **다음** 을 선택합니다.

     ![서버 이름 입력을 보여 주는 스크린샷](./media/tutorial-ecma-sql-connector/dsn-6.png)
1. Windows 인증을 유지하고, **다음** 을 선택합니다.

     ![Windows 인증을 보여 주는 스크린샷](./media/tutorial-ecma-sql-connector/dsn-7.png)
1. 샘플 데이터베이스의 이름(**CONTOSO**)을 입력합니다.

     ![데이터베이스 이름 입력을 보여 주는 스크린샷](./media/tutorial-ecma-sql-connector/dsn-8.png)
1. 이 화면에서 모든 항목을 기본값으로 유지하고, **마침** 을 선택합니다.

     ![마침 선택을 보여 주는 스크린샷](./media/tutorial-ecma-sql-connector/dsn-9.png)
1. 모든 항목이 예상대로 작동하는지 확인하려면 **데이터 원본 테스트** 를 선택합니다. 
 
     ![데이터 원본 테스트를 보여 주는 스크린샷](./media/tutorial-ecma-sql-connector/dsn-10.png)
1. 테스트에 성공했는지 확인합니다.

     ![성공을 보여 주는 스크린샷](./media/tutorial-ecma-sql-connector/dsn-11.png)
1. **확인** 을 두 번 선택합니다. ODBC 데이터 원본 관리자를 닫습니다.

## <a name="download-and-install-the-azure-ad-connect-provisioning-agent-package"></a>Azure AD Connect 프로비저닝 에이전트 패키지 다운로드 및 설치

 1. 사용할 서버에 엔터프라이즈 관리자 권한으로 로그인합니다.
 1. Azure Portal에 로그인한 다음 **Azure Active Directory** 로 이동합니다.
 1. 왼쪽 메뉴에서 **Azure AD Connect** 를 선택합니다.
 1. **클라우드 동기화 관리** > **모든 에이전트 검토** 를 선택합니다.
 1. Azure Portal에서 Azure AD Connect 프로비전 에이전트를 다운로드합니다.
 1. 사용 약관에 동의하고, **다운로드** 를 선택합니다.
 1. Azure AD Connect 프로비전 설치 프로그램 AADConnectProvisioningAgentSetup.msi를 실행합니다.
 1. **Microsoft Azure AD Connect 프로비전 에이전트 패키지** 화면에서 **설치** 를 선택합니다.
 
    ![Microsoft Azure AD Connect 프로비전 에이전트 패키지 화면을 보여 주는 스크린샷](media/on-premises-ecma-install/install-1.png)</br>
 1. 이 작업이 완료되면 구성 마법사가 시작됩니다. **다음** 을 선택합니다.
 
    ![시작 화면을 보여 주는 스크린샷](media/on-premises-ecma-install/install-2.png)</br>
 1. **확장 선택** 화면에서 **온-프레미스 애플리케이션 프로비전(Azure AD에서 애플리케이션으로)** , **다음** 을 차례로 선택합니다. 
 
    ![확장 선택 화면을 보여 주는 스크린샷](media/on-premises-ecma-install/install-3.png)</br>
 1. 전역 관리자 계정을 사용하여 Azure AD에 로그인합니다.
 
     ![Azure 로그인 화면을 보여 주는 스크린샷](media/on-premises-ecma-install/install-4.png)</br>
 1. **에이전트 구성** 화면에서 **확인** 을 선택합니다.
 
     ![설치 확인을 보여 주는 스크린샷](media/on-premises-ecma-install/install-5.png)</br>
 1. 설치가 완료되면 마법사 아래쪽에 메시지가 표시됩니다. **끝내기** 를 선택합니다.
 
     ![끝내기 단추를 보여 주는 스크린샷](media/on-premises-ecma-install/install-6.png)</br>
 
## <a name="configure-the-azure-ad-ecma-connector-host"></a>Azure AD ECMA 커넥터 호스트 구성
1. 바탕 화면에서 ECMA 바로 가기를 선택합니다.
1. ECMA 커넥터 호스트 구성이 시작되면 기본 **8585** 포트를 그대로 두고, **생성** 을 선택하여 인증서를 생성합니다. 자동 생성된 인증서는 신뢰할 수 있는 루트의 일부로 자체 서명됩니다. SAN은 호스트 이름과 일치합니다.

     ![설정 구성을 보여 주는 스크린샷](.\media\on-premises-ecma-configure\configure-1.png)
1. **저장** 을 선택합니다.

## <a name="create-a-generic-sql-connector"></a>일반 SQL 커넥터 만들기
 1. 바탕 화면에서 ECMA 커넥터 호스트 바로 가기를 선택합니다.
 1. **새 커넥터** 를 선택합니다.
 
     ![새 커넥터 선택을 보여 주는 스크린샷](.\media\on-premises-sql-connector-configure\sql-1.png)

 1. **속성** 페이지에서 상자를 이미지 뒤에 나오는 표에 지정된 값으로 채우고, **다음** 을 선택합니다.
 
     ![속성 입력을 보여 주는 스크린샷](.\media\tutorial-ecma-sql-connector\conn-1.png)

     |속성|값|
     |-----|-----|
     |속성|SQL|
     |자동 동기화 타이머(분)|120|
     |비밀 토큰|여기에 사용자 고유의 키를 입력합니다. 12자 이상이어야 합니다.|
       |확장 DLL|일반 SQL 커넥터의 경우 **Microsoft.IAM.Connector.GenericSql.dll** 을 선택합니다.|
 1. **연결** 페이지에서 상자를 이미지 뒤에 나오는 표에 지정된 값으로 채우고, **다음** 을 선택합니다.
 
     ![연결 페이지를 보여 주는 스크린샷](.\media\tutorial-ecma-sql-connector\conn-2.png)

     |속성|값|
     |-----|-----|
     |DSN 파일|"DSN 연결 파일 만들기" 자습서의 시작 부분에서 만든 파일로 이동합니다.|
     |사용자 이름|contoso\administrator|
     |암호|관리자의 암호를 입력합니다.|
 1. **스키마 1** 페이지에서 상자를 이미지 뒤에 나오는 표에 지정된 값으로 채우고, **다음** 을 선택합니다.
 
     ![스키마 1 페이지를 보여 주는 스크린샷](.\media\tutorial-ecma-sql-connector\conn-3.png)

     |속성|값|
     |-----|-----|
     |개체 형식 검색 방법|고정 값|
     |고정 값 목록/테이블/보기/SP|사용자|
 1. **스키마 2** 페이지에서 상자를 이미지 뒤에 나오는 표에 지정된 값으로 채우고, **다음** 을 선택합니다.
 
     ![스키마 2 페이지를 보여 주는 스크린샷](.\media\tutorial-ecma-sql-connector\conn-4.png)

     |속성|값|
     |-----|-----|
     |사용자:특성 검색|테이블|
     |사용자:테이블/보기/SP|Employees|
 1. **스키마 3** 페이지에서 상자를 이미지 뒤에 나오는 표에 지정된 값으로 채우고, **다음** 을 선택합니다.
 
     ![스키마 3 페이지를 보여 주는 스크린샷](.\media\tutorial-ecma-sql-connector\conn-5.png)

     |속성|Description|
     |-----|-----|
     |사용자에 대한 앵커 선택|User:ContosoLogin|
     |사용자에 대한 DN 특성 선택|AzureID|
 1. **스키마 4** 페이지에서 기본값을 그대로 두고, **다음** 을 선택합니다.
 
     ![스키마 4 페이지를 보여 주는 스크린샷](.\media\tutorial-ecma-sql-connector\conn-6.png)
 1. **전체** 페이지에서 상자를 채우고, **다음** 을 선택합니다. 개별 상자에 대한 지침은 이미지 뒤에 나오는 표를 사용합니다.
 
     ![전체 페이지를 보여 주는 스크린샷](.\media\tutorial-ecma-sql-connector\conn-7.png)

     |속성|Description|
     |-----|-----|
     |데이터 원본 날짜 시간 형식|yyyy-MM-dd HH:mm:ss|
 1. **파티션** 페이지에서 **다음** 을 선택합니다.
 
     ![파티션 페이지를 보여 주는 스크린샷](.\media\tutorial-ecma-sql-connector\conn-8.png)

 1. **실행 프로필** 페이지에서 **내보내기** 확인란을 선택한 상태로 유지합니다. **전체 가져오기** 확인란을 선택하고, **다음** 을 선택합니다.
 
     ![실행 프로필 페이지를 보여 주는 스크린샷](.\media\tutorial-ecma-sql-connector\conn-9.png)

 1. **내보내기** 페이지에서 상자를 채우고, **다음** 을 선택합니다. 개별 상자에 대한 지침은 이미지 뒤에 나오는 표를 사용합니다. 
 
     ![내보내기 페이지를 보여 주는 스크린샷](.\media\tutorial-ecma-sql-connector\conn-10.png)

     |속성|Description|
     |-----|-----|
     |작업 방법|테이블|
     |테이블/보기/SP|Employees|
 
 1. **전체 가져오기** 페이지에서 상자를 채우고, **다음** 을 선택합니다. 개별 상자에 대한 지침은 이미지 뒤에 나오는 표를 사용합니다. 
 
     ![전체 가져오기 페이지를 보여 주는 스크린샷](.\media\tutorial-ecma-sql-connector\conn-11.png)

     |속성|Description|
     |-----|-----|
     |작업 방법|테이블|
     |테이블/보기/SP|Employees|
 
 1. **개체 형식** 페이지에서 상자를 채우고, **다음** 을 선택합니다. 개별 상자에 대한 지침은 이미지 뒤에 나오는 표를 사용합니다.

      - **앵커**: 이 특성은 대상 시스템에서 고유해야 합니다. Azure AD 프로비전 서비스는 초기 주기 후에 이 특성을 사용하여 ECMA 호스트를 쿼리합니다. 이 앵커 값은 스키마 3의 앵커 값과 동일해야 합니다.
      - **쿼리 특성**: ECMA 호스트에서 메모리 내 캐시를 쿼리하는 데 사용됩니다. 이 특성은 고유해야 합니다.
      - **DN**: 대부분의 경우 **자동 생성됨** 옵션을 선택해야 합니다. 선택되지 않은 경우 DN 특성이 DN을 CN = anchorValue, Object = objectType 형식으로 저장하는 Azure AD의 특성에 매핑되었는지 확인합니다.

        ![개체 형식 페이지를 보여 주는 스크린샷](.\media\tutorial-ecma-sql-connector\conn-12.png)

      |속성|Description|
      |-----|-----|
      |대상 개체|사용자|
      |앵커|ContosoLogin|
      |쿼리 특성|AzureID|
      |DN|AzureID|
      |Autogenerated|선택됨|
      

 1. **특성 선택** 페이지의 드롭다운 목록에 있는 모든 특성을 추가하고, **다음** 을 선택합니다. 
 
     ![특성 선택 페이지를 보여 주는 스크린샷](.\media\tutorial-ecma-sql-connector\conn-13.png)

      **특성** 드롭다운 목록에는 대상 시스템에서 검색되었지만 이전 **특성 선택** 페이지에서 선택하지 *않은* 속성이 표시됩니다. 
 1. **프로비전 해제** 페이지의 **흐름 사용 안 함** 아래에서 **삭제** 를 선택합니다. **마침** 을 선택합니다.
 
     ![프로비전 해제 페이지를 보여 주는 스크린샷](.\media\tutorial-ecma-sql-connector\conn-14.png)

## <a name="ensure-ecma2host-service-is-running"></a>ECMA2Host 서비스가 실행되고 있는지 확인
1. Azure AD ECMA 커넥터 호스트를 실행하는 서버에서 **시작** 을 선택합니다.
1. 상자에서 **run**, **services.msc** 를 차례로 입력합니다.
1. **서비스** 목록에서 **Microsoft ECMA2Host** 가 있고 실행 중인지 확인합니다. 그렇지 않은 경우 **시작** 을 선택합니다.

   ![서비스가 실행 중임을 보여 주는 스크린샷](.\media\on-premises-ecma-configure\configure-2.png)

## <a name="add-an-enterprise-application"></a>엔터프라이즈 애플리케이션 추가
1. 애플리케이션 관리자 권한으로 Azure Portal에 로그인합니다.
1. 포털에서 **Azure Active Directory** > **엔터프라이즈 애플리케이션** 으로 차례로 이동합니다.
1. **새 애플리케이션** 을 선택합니다.

   ![새 애플리케이션 추가를 보여 주는 스크린샷](.\media\on-premises-ecma-configure\configure-4.png)
1. 갤러리에서 **온-프레미스 ECMA 앱** 을 검색하고, **만들기** 를 선택합니다.

## <a name="configure-the-application-and-test"></a>애플리케이션 구성 및 테스트
1. 만들어지면 **프로비전** 페이지를 선택합니다.
1. **시작** 을 선택합니다.

     ![시작을 보여 주는 스크린샷](.\media\on-premises-ecma-configure\configure-1.png)
1. **프로비전** 페이지에서 모드를 **자동** 으로 변경합니다.

     ![모드를 자동으로 변경하는 방법을 보여 주는 스크린샷](.\media\on-premises-ecma-configure\configure-7.png)
1. **온-프레미스 연결** 섹션에서 방금 배포한 에이전트를 선택하고, **에이전트 할당** 을 선택합니다.
     >[!NOTE]
     >에이전트를 추가한 후 등록이 완료될 때까지 10분 동안 기다립니다. 연결 테스트는 등록이 완료될 때까지 작동하지 않습니다.
     >
     >또는 서버에서 프로비저닝 에이전트를 다시 시작하여 에이전트 등록을 강제로 완료할 수 있습니다. 서버로 이동하고, Windows 검색 창에서 **서비스** 를 검색하고, **Azure AD Connect 프로비전 에이전트 서비스** 를 식별하고, 마우스 오른쪽 단추로 해당 서비스를 클릭하고, 다시 시작합니다.
   
     ![에이전트 다시 시작을 보여 주는 스크린샷](.\media\on-premises-ecma-configure\configure-8.png)
1.  10분 후 **관리자 자격 증명** 섹션에서 다음 URL을 입력합니다. `connectorName` 부분을 ECMA 호스트의 커넥터 이름으로 바꿉니다. 또한 `localhost`를 호스트 이름으로 바꿀 수도 있습니다.

     |속성|값|
     |-----|-----|
     |테넌트 URL|https://localhost:8585/ecma2host_connectorName/scim|

1. 커넥터를 만들 때 정의한 **비밀 토큰** 값을 입력합니다.
1. **연결 테스트** 를 선택하고, 1분 동안 기다립니다.

     ![에이전트 할당을 보여 주는 스크린샷](.\media\on-premises-ecma-configure\configure-5.png)
1. 연결 테스트가 성공하면 **저장** 을 선택합니다.</br>

     ![에이전트 테스트를 보여 주는 스크린샷](.\media\on-premises-ecma-configure\configure-9.png)

## <a name="assign-users-to-an-application"></a>애플리케이션에 사용자 할당
이제 Azure AD와 통신하는 Azure AD ECMA 커넥터 호스트가 있으므로 프로비전 범위에 있는 사용자를 구성하기 위해 이동할 수 있습니다. 

1. Azure Portal에서 **엔터프라이즈 애플리케이션** 을 선택합니다.
1. **온-프레미스 프로비전** 애플리케이션을 선택합니다.
1. 왼쪽의 **관리** 아래에서 **사용자 및 그룹** 을 선택합니다.
1. **사용자/그룹 추가** 를 선택합니다.

     ![사용자 추가를 보여 주는 스크린샷](.\media\tutorial-ecma-sql-connector\app-2.png)
1. **사용자** 아래에서 **선택된 항목 없음** 을 선택합니다.

     ![선택된 항목 없음을 보여 주는 스크린샷](.\media\tutorial-ecma-sql-connector\app-3.png)
1. 오른쪽에서 사용자를 선택하고, **선택** 단추를 선택합니다.</br>

     ![사용자 선택을 보여 주는 스크린샷](.\media\tutorial-ecma-sql-connector\app-4.png)
1. 이제 **할당** 을 선택합니다.

     ![사용자 할당을 보여 주는 스크린샷](.\media\tutorial-ecma-sql-connector\app-5.png)

## <a name="configure-attribute-mappings"></a>특성 매핑 구성
이제 온-프레미스 애플리케이션과 SQL 서버 간에 특성을 매핑해야 합니다.

#### <a name="configure-attribute-mapping"></a>특성 매핑 구성
 1. Azure AD 포털의 **엔터프라이즈 애플리케이션** 에서 **프로비전** 페이지를 선택합니다.
 1. **시작** 을 선택합니다.
 1. **매핑** 을 펼치고, **Azure Active Directory 사용자 프로비전** 을 선택합니다.
 
    ![사용자 프로비저닝을 보여 주는 스크린샷입니다.](.\media\on-premises-ecma-configure\configure-10.png)
 1. **새 매핑 추가** 를 선택합니다.
 
     ![새 매핑 추가를 보여 주는 스크린샷](.\media\on-premises-ecma-configure\configure-11.png)
 1. 원본 및 대상 특성을 지정하고, 다음 표에 있는 모든 매핑을 추가합니다.

      |매핑 유형|원본 특성|대상 특성|
      |-----|-----|-----|
      |직접|userPrincipalName|urn:ietf:params:scim:schemas:extension:ECMA2Host:2.0:User:ContosoLogin|
      |직접|objectID|urn:ietf:params:scim:schemas:extension:ECMA2Host:2.0:User:AzureID|
      |직접|mail|urn:ietf:params:scim:schemas:extension:ECMA2Host:2.0:User:Email|
      |직접|givenName|urn:ietf:params:scim:schemas:extension:ECMA2Host:2.0:User:FirstName|
      |직접|surName|urn:ietf:params:scim:schemas:extension:ECMA2Host:2.0:User:LastName|
      |직접|mailNickname|urn:ietf:params:scim:schemas:extension:ECMA2Host:2.0:User:textID|

 1. **저장** 을 선택합니다.
 
     ![매핑 저장을 보여 주는 스크린샷](.\media\tutorial-ecma-sql-connector\app-6.png)

## <a name="test-provisioning"></a>프로비저닝 테스트
이제 특성이 매핑되었으므로 사용자 중 한 명과 주문형 프로비전을 테스트할 수 있습니다.
 
 1. Azure Portal에서 **엔터프라이즈 애플리케이션** 을 선택합니다.
 1. **온-프레미스 프로비전** 애플리케이션을 선택합니다.
 1. 왼쪽에서 **프로비전** 을 선택합니다.
 1. **주문형 프로비전** 을 선택합니다.
 1. 테스트 사용자 중 한 명을 검색하고, **프로비전** 을 선택합니다.
 
     ![프로비전 테스트를 보여 주는 스크린샷](.\media\on-premises-ecma-configure\configure-13.png)

## <a name="start-provisioning-users"></a>사용자 프로비저닝 시작
 1. 주문형 프로비전이 성공하면 프로비전 구성 페이지로 다시 변경합니다. 범위가 할당된 사용자 및 그룹으로만 설정되어 있는지 확인하고, 프로비전을 **켜기** 로 설정하고, **저장** 을 선택합니다.
 
    ![프로비전 시작을 보여주는 스크린샷](.\media\on-premises-ecma-configure\configure-14.png)
 1. 프로비전이 시작될 때까지 몇 분 정도 기다립니다. 최대 40분이 걸릴 수 있습니다. 프로비전 작업이 완료되면 다음 섹션에서 설명한 대로 프로비전 상태를 **끄기** 로 변경하고, **저장** 을 선택할 수 있습니다. 이 작업은 나중에 프로비전 서비스의 실행을 중지합니다.

## <a name="check-that-users-were-successfully-provisioned"></a>사용자가 성공적으로 프로비전되었는지 확인
기다린 후에 SQL 데이터베이스를 확인하여 사용자가 프로비전되었는지 확인합니다.

 ![사용자가 프로비전 확인을 보여주는 스크린샷](.\media\on-premises-ecma-configure\configure-15.png)

## <a name="appendix-a"></a>부록 A
다음 SQL 스크립트를 사용하여 샘플 데이터베이스를 만듭니다.

```SQL
---Creating the Database---------
Create Database CONTOSO
Go
-------Using the Database-----------
Use [CONTOSO]
Go
-------------------------------------

/****** Object:  Table [dbo].[Employees]    Script Date: 1/6/2020 7:18:19 PM ******/
SET ANSI_NULLS ON
GO

SET QUOTED_IDENTIFIER ON
GO

CREATE TABLE [dbo].[Employees](
    [ContosoLogin] [nvarchar](128) NULL,
    [FirstName] [nvarchar](50) NOT NULL,
    [LastName] [nvarchar](50) NOT NULL,
    [Email] [nvarchar](128) NULL,
    [InternalGUID] [uniqueidentifier] NULL,
    [AzureID] [uniqueidentifier] NULL,
    [textID] [nvarchar](128) NULL
) ON [PRIMARY]
GO

ALTER TABLE [dbo].[Employees] ADD  CONSTRAINT [DF_Employees_InternalGUID]  DEFAULT (newid()) FOR [InternalGUID]
GO

```




## <a name="next-steps"></a>다음 단계

- [온-프레미스 애플리케이션 프로비전 문제 해결](on-premises-ecma-troubleshoot.md)
- [알려진 제한 사항 검토](known-issues.md)
- [온-프레미스 프로비전 필수 구성 요소](on-premises-ecma-prerequisites.md)
- [온-프레미스 프로비전에 대한 필수 구성 요소 검토](on-premises-ecma-prerequisites.md)
