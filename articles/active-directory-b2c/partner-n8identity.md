---
title: Azure Active Directory B2C를 사용 하 여 N8 Identity 구성 자습서
titleSuffix: Azure AD B2C
description: 고객 계정 마이그레이션 및 CSR (고객 서비스 요청) 관리를 처리 하기 위해 Azure Active Directory B2C를 사용 하 여 TheAccessHub 관리 도구를 구성 하는 자습서입니다.
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 10/26/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: a6d6ca825a556ea3c98fb94d4becbb75b8f2a7d7
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2020
ms.locfileid: "93294328"
---
# <a name="tutorial-for-configuring-theaccesshub-admin-tool-with-azure-active-directory-b2c"></a>Azure Active Directory B2C를 사용 하 여 TheAccessHub Admin 도구를 구성 하기 위한 자습서

이 샘플 자습서에서는 N8 Identity에서 AD (Azure Active Directory)와 [TheAccessHub Admin 도구](https://n8id.com/products/theaccesshub-admintool/) 를 통합 하는 방법에 대 한 지침을 제공 합니다. 이 솔루션은 고객 계정 마이그레이션 및 CSR (고객 서비스 요청) 관리를 다룹니다.  

이 솔루션은 다음과 같은 요구 사항이 하나 이상 있는 경우에 적합 합니다.

- 기존 사이트가 있고 Azure AD B2C로 마이그레이션해야 합니다. 그러나 암호를 포함 하 여 고객 계정의 마이그레이션에 어려움이 있습니다.

- Azure AD B2C 계정을 관리 하려면 CSR에 대 한 도구가 필요 합니다.

- Csr에 대해 위임 된 관리를 사용 해야 하는 요구 사항이 있습니다.

- 많은 리포지토리의 데이터를 동기화 하 고 Azure AD B2C로 병합 하려고 합니다.

## <a name="pre-requisites"></a>필수 구성 요소

시작 하려면 다음이 필요 합니다.

- Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.

- [Azure AD B2C 테 넌 트](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-tenant)입니다. 테 넌 트는 Azure 구독에 연결 되어야 합니다.

- TheAccessHub 관리 도구 환경: 새 환경을 프로 비전 하려면 [N8 Identity](https://n8id.com/contact/) 에 문의 하세요.

- 필드 고객 데이터를 마이그레이션하려는 모든 데이터베이스 또는 LDAPs (Lightweight Directory Access protocol)에 대 한 연결 및 자격 증명 정보입니다.

- 필드 TheAccessHub 관리 도구를 등록 정책 흐름에 통합 하려는 경우 [사용자 지정 정책을](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started)사용 하기 위한 Azure AD B2C 환경을 구성 합니다.

## <a name="scenario-description"></a>시나리오 설명

TheAccessHub Admin 도구는 Azure의 다른 응용 프로그램과 마찬가지로 실행 됩니다. N8 Id의 Azure 구독 또는 고객의 구독에서 실행할 수 있습니다. 다음 아키텍처 다이어그램에서는 구현을 보여 줍니다.

![N8identity 아키텍처 다이어그램을 보여 주는 이미지](./media/partner-n8identity/n8identity-architecture-diagram.png)

|단계 | 설명 |
|:-----| :-----------|
| 1. | 사용자가 로그인 페이지에 도착 합니다. 사용자는 등록을 선택 하 여 새 계정을 만들고 페이지에 정보를 입력 합니다. Azure AD B2C 사용자 특성을 수집 합니다.
| 2. | Azure AD B2C TheAccessHub Admin 도구를 호출 하 고 사용자 특성을 전달 합니다.
| 3. | TheAccessHub 관리 도구는 기존 데이터베이스에서 현재 사용자 정보를 확인 합니다.  
| 4. | 사용자 레코드는 데이터베이스에서 TheAccessHub Admin 도구로 동기화 됩니다.
| 5. | TheAccessHub 관리 도구는 위임 된 CSR/기술 지원팀 관리자와 데이터를 공유 합니다.
| 6. | TheAccessHub Admin Tool은 사용자 레코드를 Azure AD B2C와 동기화 합니다.
| 7. |TheAccessHub 관리 도구의 성공/실패 응답에 따라 Azure AD B2C 사용자에 게 사용자 지정 된 환영 전자 메일을 보냅니다.

## <a name="create-a-global-admin-in-your-azure-ad-b2c-tenant"></a>Azure AD B2C 테 넌 트에서 전역 관리자 만들기

TheAccessHub Admin 도구를 사용 하려면 전역 관리자를 대신 하 여 사용자 정보를 읽고 Azure AD B2C 테 넌 트의 변경을 수행 하는 데 필요한 권한이 있어야 합니다. 정규 관리자의 변경 내용 t는 TheAccessHub 관리 도구에서 테 넌 트와 상호 작용 하는 기능에 영향을 줍니다.

전역 관리자를 만들려면 다음 단계를 수행 합니다.

1. Azure Portal에서 관리자 권한으로 Azure AD B2C 테 넌 트에 로그인 합니다. **Azure Active Directory**  >  **사용자** 로 이동 합니다.
2. **새 사용자** 선택
3. **사용자 만들기** 를 선택 하 여 고객이 아닌 일반 디렉터리 사용자를 만듭니다.
4. Id 정보 양식 완료

   a. 사용자 이름 (예: ' theaccesshub ')을 입력 합니다.

   b. ' TheAccessHub Service Account '와 같은 이름을 입력 합니다.

5. **암호 표시** 를 선택 하 고 나중에 사용 하기 위해 초기 암호를 복사 합니다.
6. 전역 관리자 역할 할당

   a. 사용자의 현재 역할 **사용자** 를 선택 하 여 변경

   b. 전역 관리자 레코드 확인

   c. **선택**  >  **만들기**

## <a name="connect-theaccesshub-admin-tool-with-your-azure-ad-b2c-tenant"></a>Azure AD B2C 테 넌 트와 TheAccessHub 관리 도구 연결

TheAccessHub 관리 도구는 Microsoft의 Graph API을 사용 하 여 디렉터리를 읽고 변경 합니다. 테 넌 트에서 전역 관리자 역할을 합니다. TheAccessHub 관리 도구에서 추가 권한을 부여 해야 합니다 .이 도구는 도구 내에서 부여할 수 있습니다.

TheAccessHub 관리자 도구에 디렉터리에 대 한 액세스 권한을 부여 하려면 다음 단계를 수행 합니다.

1. N8 Identity로 제공 된 자격 증명을 사용 하 여 TheAccessHub Admin 도구에 로그인

2. **시스템 관리자**  >  **Azure AD B2C 구성** 으로 이동 합니다.

3. **연결 권한 부여** 선택

4. 새 창에서 전역 관리자 계정으로 로그인 합니다. 새 서비스 계정을 사용 하 여 처음으로 로그인 하는 경우 암호를 다시 설정 하 라는 메시지가 표시 될 수 있습니다.

5. 프롬프트에 따라 **동의 함** 을 선택 하 여 TheAccessHub 관리 도구에 요청 된 사용 권한을 부여 합니다.

## <a name="configure-a-new-csrhelpdesk-user-using-your-enterprise-identity"></a>엔터프라이즈 id를 사용 하 여 새 CSR/기술 지원팀 사용자 구성

기존 엔터프라이즈 Azure Active Directory 자격 증명을 사용 하 여 TheAccessHub Admin 도구에 액세스 하는 CSR/헬프데스크 사용자를 만듭니다.

SSO (Single Sign-on)를 사용 하 여 CSR/헬프데스크 사용자를 구성 하려면 다음 단계를 수행 하는 것이 좋습니다.

1. N8 Identity에서 제공한 자격 증명을 사용 하 여 TheAccessHub Admin 도구에 로그인 합니다.

2. **관리자 도구로** 이동  >  **동료 관리**

3. **동료 추가** 선택

4. **Azure 관리자의 동료 유형** 선택

5. 동료의 프로필 정보 입력

   a. 홈 조직을 선택 하면이 사용자를 관리할 수 있는 권한이 있는 사용자를 제어 합니다.

   b. 로그인 ID/Azure AD 사용자 이름의 경우 사용자의 Azure Active Directory 계정에서 사용자 계정 이름을 제공 합니다.

   c. TheAccessHub 역할 탭에서 관리 되는 역할 기술 지원팀을 선택 합니다. 그러면 사용자가 동료 관리 보기에 액세스할 수 있습니다. 사용자를 그룹에 배치 하거나 조직 소유자에 게 고객에 게 조치를 적용 해야 합니다.

6. **제출** 을 선택합니다.

## <a name="configure-a-new-csrhelpdesk-user-using-a-new-identity"></a>새 id를 사용 하 여 새 CSR/기술 지원팀 사용자 구성

TheAccessHub Admin 도구에 고유한 새 로컬 자격 증명을 사용 하 여 TheAccessHub 관리 도구에 액세스 하는 CSR/헬프데스크 사용자를 만듭니다. 엔터프라이즈에 대해 Azure AD를 사용 하지 않는 조직에서 주로 사용 됩니다.

SSO 없이 [CSR/헬프데스크](https://youtu.be/iOpOI2OpnLI) 사용자를 설정 하려면 다음 단계를 수행 합니다.

1. N8 Identity에서 제공한 자격 증명을 사용 하 여 TheAccessHub Admin 도구에 로그인

2. **관리자 도구로** 이동  >  **동료 관리**

3. **동료 추가** 선택

4. **동료 유형 로컬 관리자** 선택

5. 동료의 프로필 정보 입력

   a. 홈 조직을 선택 하면이 사용자를 관리할 수 있는 권한이 있는 사용자를 제어 합니다.

   b. **TheAccessHub 역할** 탭에서 관리 되는 역할 **기술 지원팀** 을 선택 합니다. 그러면 사용자가 동료 관리 보기에 액세스할 수 있습니다. 사용자를 그룹에 배치 하거나 조직 소유자에 게 고객에 게 조치를 적용 해야 합니다.

6. **로그인 ID/전자 메일** 및 **일회성 암호** 특성을 복사 합니다. 새 사용자에 게 제공 합니다. 이러한 자격 증명을 사용 하 여 TheAccessHub Admin 도구에 로그인 합니다. 사용자에 게 처음 로그인 할 때 새 암호를 입력 하 라는 메시지가 표시 됩니다.

7. **제출** 을 선택합니다.

## <a name="configure-partitioned-csrhelpdesk-administration"></a>분할 된 CSR/기술 지원팀 관리 구성

TheAccessHub 관리 도구에서 고객 및 CSR/기술 지원팀 사용자를 관리할 수 있는 권한은 조직 계층 구조를 사용 하 여 관리 됩니다. 모든 동료와 고객은 거주 하는 홈 조직을 보유 하 고 있습니다. 특정 동료 또는 동료 그룹을 조직의 소유자로 할당할 수 있습니다.  조직 소유자는 자신이 소유한 조직 또는 하위 조직의 동료와 고객을 관리 (변경) 할 수 있습니다. 여러 동료가 사용자 집합을 관리할 수 있도록 하려면 많은 멤버를 사용 하 여 그룹을 만들 수 있습니다. 그런 다음 그룹을 조직 소유자로 할당 하 고 모든 그룹의 구성원이 조직의 동료와 고객을 관리할 수 있습니다.

### <a name="create-a-new-group"></a>새 그룹 만들기

1. N8 Identity로 제공 된 **자격 증명** 을 사용 하 여 TheAccessHub Admin 도구에 로그인

2. **조직 > 그룹 관리** 로 이동 합니다.

3. **그룹 추가** > 선택

4. **그룹 이름** , **그룹 설명** 및 **그룹 소유자** 입력

5. 그룹의 멤버로 지정할 동료의 상자를 검색 하 고 선택한 다음, > **추가** 를 선택 합니다.

6. 페이지 맨 아래에서 그룹의 모든 멤버를 볼 수 있습니다.

7. 필요한 경우 행의 끝에 있는 **x** 를 선택 하 여 멤버를 제거할 수 있습니다.

8. **제출** 을 선택합니다.

### <a name="create-a-new-organization"></a>새 조직 만들기

1. N8 Identity로 제공 된 자격 증명을 사용 하 여 TheAccessHub Admin 도구에 로그인

2. 조직 > 조직 **관리** 로 이동 합니다.

3. > **조직** 선택

4. **조직 이름** , **조직 소유자** 및 **부모 조직을** 제공 합니다.

    a. 조직 이름은 고객 데이터에 해당 하는 값입니다. 동료와 고객 데이터를 로드할 때 부하에서 조직의 이름을 제공 하는 경우 동료가 조직에 자동으로 배치 될 수 있습니다.

    b. 소유자는이 조직의 고객과 동료를 관리 하는 개인 또는 그룹 및 내 모든 suborganization을 나타냅니다.

    c. 부모 조직은 본질적으로이 조직을 담당 하는 다른 조직을 나타냅니다.

5. **제출** 을 선택합니다.

### <a name="modify-the-hierarchy-via-the-tree-view"></a>트리 뷰를 통해 계층 구조 수정

1. N8 Identity로 제공 된 자격 증명을 사용 하 여 TheAccessHub Admin 도구에 로그인

2. **관리자 도구**  >  **트리 뷰로** 이동

3. 이 표현에서는 어떤 동료와 그룹이 어떤 조직을 관리할 수 있는지 시각화할 수 있습니다.

4. 계층은 부모 조직의 상위 조직에서 부모로 사용할 조직을 끌어 수정할 수 있습니다.

5. 계층 변경을 마치면 **저장** 을 선택 합니다.

## <a name="customize-welcome-notification"></a>환영 알림 사용자 지정

TheAccessHub를 사용 하 여 이전 인증 솔루션에서 Azure AD B2C로 사용자를 마이그레이션하는 동안 마이그레이션 중 TheAccessHub에서 사용자에 게 보내는 사용자 시작 알림을 사용자 지정할 수 있습니다. 이 메시지는 일반적으로 사용자가 Azure AD B2C 디렉터리에 새 암호를 설정 하는 링크를 포함 합니다.

알림을 사용자 지정 하려면:

1. N8 Identity로 제공 된 자격 증명을 사용 하 여 TheAccessHub에 로그인 합니다.

2. **시스템 관리자**  >  **알림으로** 이동

3. **동료 만들기 템플릿** 선택

4. **편집** 을 선택합니다.

5. 필요에 따라 메시지 및 템플릿 필드를 변경 합니다. 템플릿 필드는 HTML을 인식 하며 고객 전자 메일에 HTML 형식의 알림을 보낼 수 있습니다.

6. 완료 되 면 **저장** 을 선택 합니다.

## <a name="migrate-data-from-external-data-sources-to-azure-ad-b2c"></a>외부 데이터 원본에서 Azure AD B2C로 데이터 마이그레이션

TheAccessHub Admin 도구를 사용 하 여 다양 한 데이터베이스, LDAPs 및 CSV 파일에서 데이터를 가져온 다음 해당 데이터를 Azure AD B2C 테 넌 트에 푸시할 수 있습니다. TheAccessHub Admin Tool 내에서 Azure AD B2C 사용자 동료 형식으로 데이터를 로드 하 여 수행 합니다.  데이터 원본이 Azure 자체가 아닌 경우 데이터는 TheAccessHub Admin 도구와 Azure AD B2C 모두에 배치 됩니다. 외부 데이터의 원본이 컴퓨터의 단순한 .csv 파일이 아닌 경우 데이터 로드를 수행 하기 전에 데이터 원본을 설정 합니다. 아래 단계에서는 데이터 원본을 만들고 데이터 로드를 수행 하는 방법을 설명 합니다.

### <a name="configure-a-new-data-source"></a>새 데이터 원본 구성

1. N8 Identity로 제공 된 자격 증명을 사용 하 여 TheAccessHub Admin 도구에 로그인

2. **시스템 관리**  >  **데이터 원본** 으로 이동

3. **데이터 원본 추가** 를 선택 합니다.

4. 이 데이터 소스에 대 한 **이름** 및 **형식** 제공

5. 데이터 원본 유형에 따라 양식 데이터를 입력 합니다.

   **데이터베이스의 경우**

   a. **유형** -데이터베이스

   b. **데이터베이스 유형** – 지원 되는 데이터베이스 유형 중 하나에서 데이터베이스를 선택 합니다.

   c. **연결 URL** – 올바른 형식의 JDBC 연결 문자열을 입력 합니다. 예: ``jdbc:postgresql://myhost.com:5432/databasename``

   d. **사용자 이름** – 데이터베이스에 액세스 하기 위한 사용자 이름을 입력 합니다.

   e. **암호** -데이터베이스에 액세스 하기 위한 암호를 입력 합니다.

   f. **쿼리** – 고객 세부 정보를 추출 하는 SQL 쿼리를 입력 합니다. 예: ``SELECT * FROM mytable;``

   g. **연결 테스트** 를 선택 하면 연결이 작동 하는지 확인 하는 데이터 샘플이 표시 됩니다.

   **LDAPs 용**

   a. **유형** – LDAP

   b. **호스트** – LDAP 서버가 실행 되는 컴퓨터의 호스트 이름 또는 IP를 입력 합니다. 예: ``mysite.com``

   c. **포트** – LDAP 서버가 수신 대기 하는 포트 번호를 입력 합니다.

   d. **Ssl** – TheAccessHub 관리 도구가 SSL을 사용 하 여 안전 하 게 LDAP와 통신 해야 하는 경우 확인란을 선택 합니다. SSL을 사용 하는 것이 좋습니다.

   e. **로그인 dn** – 로그인 할 사용자 계정의 DN을 입력 하 고 LDAP 검색을 수행 합니다.

   f. **암호** -사용자에 대 한 암호를 입력 합니다.

   g. **기본 DN** – 검색을 수행 하려는 계층의 맨 위에 있는 DN을 입력 합니다.

   h. **필터** – 고객 레코드를 가져올 LDAP 필터 문자열을 입력 합니다.

   i. **특성** – TheAccessHub Admin 도구에 전달할 고객 레코드에서 쉼표로 구분 된 특성 목록을 입력 합니다.

   j. **연결 테스트** 를 선택 하면 연결이 작동 하는지 확인 하는 데이터 샘플이 표시 됩니다.

   **OneDrive의 경우**

   a. **형식** – 비즈니스용 OneDrive

   b. **연결 권한 부여** 선택

   c. Onedrive 계정에 대 한 읽기 권한이 있는 사용자로 로그인 하 여 **onedrive** 에 로그인 하 라는 메시지가 새 창에 표시 됩니다. TheAccessHub Admin Tool은이 사용자가 CSV 로드 파일을 읽을 수 있도록 합니다.

   d. 프롬프트에 따라 **동의 함** 을 선택 하 여 TheAccessHub 관리 도구에 요청 된 사용 권한을 부여 합니다.

6. 완료 되 면 **저장** 을 선택 합니다.  

### <a name="synchronize-data-from-your-data-source-into-azure-ad-b2c"></a>데이터 원본의 데이터를 Azure AD B2C로 동기화

1. N8 Identity로 제공 된 자격 증명을 사용 하 여 TheAccessHub Admin 도구에 로그인

2. **시스템 관리자**  >  **데이터 동기화** 로 이동 합니다.

3. **새 로드** 선택

4. 사용자 Azure AD B2C **동료 유형** 선택

5. **원본** 을 선택 하 고 팝업 대화 상자에서 데이터 원본을 선택 합니다. OneDrive 데이터 원본을 만든 경우에도 파일을 선택 합니다.

6. 이 로드를 사용 하 여 새 고객 계정을 만들지 않으려면 첫 번째 정책 ( **TheAccessHub에서 동료를 찾을 수 없는 경우** **)을 변경** 합니다.

7. 이 로드를 사용 하 여 기존 고객 계정을 업데이트 하지 않으려면 **원본 및 TheAccessHub 데이터가 일치** 하지 **않는** 경우 두 번째 정책을 변경 합니다.

8. **다음** 을 선택합니다.

9. **검색 매핑 구성** 에서는 TheAccessHub Admin 도구에 이미 로드 된 고객과 부하 레코드를 상호 연결 하는 방법을 확인 합니다. 원본에서 하나 이상의 식별 특성을 선택 합니다. 특성을 동일한 값을 포함 하는 TheAccessHub Admin 도구의 특성과 일치 시킵니다. 일치 하는 항목이 있으면 기존 레코드가 재정의 됩니다. 그렇지 않으면 새 고객이 생성 됩니다. 이러한 검사를 여러 가지 순서로 지정할 수 있습니다. 예를 들어 전자 메일을 먼저 확인 한 다음, 성과 이름을 확인할 수 있습니다.

10. 왼쪽 메뉴에서 **데이터 매핑** 을 선택 합니다.

11. Data-Mapping 구성에서 원본 특성에서 채울 TheAccessHub 관리 도구 특성을 할당 합니다. 모든 특성을 매핑할 필요는 없습니다. 매핑되지 않은 특성은 기존 고객에 대해 변경 되지 않은 상태로 유지 됩니다.

12. 기존 조직의 이름인 값으로 org_name 특성에 매핑하면 새로 만든 고객이 해당 조직에 배치 됩니다.

13. **다음** 을 선택합니다.

14. 이 부하를 반복 해야 하는 경우 일별/주별 또는 월별 일정을 지정할 수 있습니다. 그렇지 않으면 **지금** 기본값을 유지 합니다.

15. **제출** 을 선택합니다.

16. **지금 일정** 을 선택 하면 새 레코드가 데이터 동기화 화면에 즉시 추가 됩니다. 유효성 검사 단계를 100%에 도달한 후에는 **새 레코드** 를 선택 하 여 예상 되는 로드 결과를 확인 합니다. 예약 된 로드의 경우 이러한 레코드는 예약 된 시간 후에만 표시 됩니다.

17. 오류가 없으면 **실행** 을 선택 하 여 변경 내용을 커밋합니다. 그렇지 않은 경우에는 **추가** 메뉴에서 **제거** 를 선택 하 여 로드를 제거 합니다. 그런 다음 원본 데이터를 수정 하거나 매핑을 로드 하 고 다시 시도할 수 있습니다. 대신 오류 수가 작은 경우 수동으로 레코드를 업데이트 하 고 각 레코드에 대해 **업데이트** 를 선택 하 여 수정할 수 있습니다. 마지막으로, 오류를 계속 진행 하 고 나중에 조작을의 TheAccessHub Admin 도구 **지원** 으로 해결할 수 있습니다.

18. 로드 단계에서 **데이터 동기화** 레코드가 100%가 되 면 로드로 인해 발생 하는 모든 변경 내용이 시작 됩니다. 고객은 Azure AD B2C에서 변경 내용을 받거나 받아야 합니다.

## <a name="synchronize-azure-ad-b2c-customer-data-into-theaccesshub-admin-tool"></a>TheAccessHub 관리 도구에 Azure AD B2C 고객 데이터 동기화

일회성 또는 지속적인 작업으로 TheAccessHub Admin 도구는 Azure AD B2C의 모든 고객 정보를 TheAccessHub Admin 도구로 동기화 할 수 있습니다. 이렇게 하면 CSR/기술 지원팀 관리자가 최신 고객 정보를 볼 수 있습니다.

Azure AD B2C에서 TheAccessHub Admin 도구로 데이터를 동기화 하려면:

1. N8 Identity로 제공 된 자격 증명을 사용 하 여 TheAccessHub Admin 도구에 로그인

2. **시스템 관리자**  >  **데이터 동기화** 로 이동 합니다.

3. **새 로드** 선택

4. 사용자 Azure AD B2C **동료 유형** 선택

5. **옵션** 단계에서는 기본값을 그대로 둡니다.

6. **다음** 을 선택합니다.

7. **데이터 매핑 & 검색** 단계에서는 기본값을 그대로 둡니다. 기존 조직의 이름인 값으로 **org_name** 특성에 매핑하는 경우에는 새로 만든 고객이 해당 조직에 배치 됩니다.

8. **다음** 을 선택합니다.

9. 이 부하를 반복 해야 하는 경우 일별/주별 또는 월별 일정을 지정할 수 있습니다. 그렇지 않으면 기본값을 유지 합니다. **Now**. Azure AD B2C에서 정기적으로 동기화 하는 것이 좋습니다.

10. **제출** 을 선택합니다.

11. **지금** 일정을 선택 하면 새 레코드가 데이터 동기화 화면에 즉시 추가 됩니다. 유효성 검사 단계를 100%에 도달한 후에는 새 레코드를 선택 하 여 예상 되는 로드 결과를 확인 합니다. 예약 된 로드의 경우 이러한 레코드는 예약 된 시간 후에만 표시 됩니다.

12. 오류가 없으면 **실행** 을 선택 하 여 변경 내용을 커밋합니다. 그렇지 않은 경우에는 추가 메뉴에서 **제거** 를 선택 하 여 로드를 제거 합니다. 그런 다음 원본 데이터를 수정 하거나 매핑을 로드 하 고 다시 시도할 수 있습니다. 대신 오류 수가 작은 경우 수동으로 레코드를 업데이트 하 고 각 레코드에 대해 **업데이트** 를 선택 하 여 수정할 수 있습니다. 마지막으로, 오류를 계속 진행 하 고 나중에 조작을의 TheAccessHub Admin 도구 지원으로 해결할 수 있습니다.

13. 로드 단계에서 **데이터 동기화** 레코드가 100%가 되 면 로드로 인해 발생 하는 모든 변경 내용이 시작 됩니다.

## <a name="configure-azure-ad-b2c-policies-to-call-theaccesshub-admin-tool"></a>TheAccessHub Admin 도구를 호출 하도록 Azure AD B2C 정책 구성

Azure AD B2C를 사용 하 여 상태를 최신 상태로 유지 하는 기능이 TheAccessHub 관리 도구를 동기화 하는 경우도 있습니다. TheAccessHub 관리 도구의 API 및 Azure AD B2C 정책을 활용 하 여 TheAccessHub 관리 도구를 변경 하는 것을 알릴 수 있습니다. 이 솔루션에는 [Azure AD B2C 사용자 지정 정책](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started#:~:text=%20Get%20started%20with%20custom%20policies%20in%20Azure,Experience%20Framework%20applications.%20Azure%20AD%20B2C...%20More%20)에 대 한 기술 지식이 필요 합니다. 다음 섹션에서는 Sign-Up 사용자 지정 정책에 새 계정에 대 한 TheAccessHub 관리 도구를 알리는 정책 단계 및 보안 인증서 예제를 제공 합니다.

### <a name="create-a-secure-credential-to-invoke-theaccesshub-admin-tools-api"></a>TheAccessHub 관리 도구의 API를 호출 하는 보안 자격 증명 만들기

1. N8 Identity로 제공 된 자격 증명을 사용 하 여 TheAccessHub Admin 도구에 로그인

2. **System Admin**  >  **admin Tools**  >  **API Security** 로 이동 합니다.

3. **생성** 선택

4. **인증서 암호** 복사

5. **다운로드** 를 선택 하 여 클라이언트 인증서를 가져옵니다.

6. 이 [자습서](https://docs.microsoft.com/azure/active-directory-b2c/secure-rest-api#https-client-certificate-authentication ) 에 따라 클라이언트 인증서를 Azure AD B2C에 추가 합니다.

### <a name="retrieve-your-custom-policy-examples"></a>사용자 지정 정책 예제 검색

1. N8 Identity로 제공 된 자격 증명을 사용 하 여 TheAccessHub Admin 도구에 로그인

2. **System Admin**  >  **admin Tools**  >  **Azure B2C 정책** 으로 이동 합니다.

3. Id 경험 프레임 워크 구성에서 Azure AD B2C 테 넌 트 도메인 및 두 개의 Id 경험 프레임 워크 Id를 제공 합니다.

4. **저장** 을 선택합니다.

5. **다운로드** 를 선택 하 여 고객이 등록할 때 TheAccessHub Admin Tool에 고객을 추가 하는 기본 정책으로 zip 파일을 가져옵니다.

6. 이 [자습서](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started) 에 따라 Azure AD B2C에서 사용자 지정 정책 디자인을 시작할 수 있습니다.

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음 문서를 참조 하세요.

- [Azure AD B2C의 사용자 지정 정책](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-overview)

- [Azure AD B2C에서 사용자 지정 정책 시작](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started?tabs=applications)