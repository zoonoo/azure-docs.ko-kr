---
title: Azure Active Directory B2C를 사용하여 N8 Identity를 구성하기 위한 자습서
titleSuffix: Azure AD B2C
description: 고객 계정 마이그레이션 및 CSR(Customer Service Requests) 관리를 처리하기 위해 Azure Active Directory B2C를 사용하여 TheAccessHub 관리자 도구를 구성하기 위한 자습서입니다.
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 10/26/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 9fee7965e7834f0e05dba91f8652a0e155087b14
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/09/2021
ms.locfileid: "107257877"
---
# <a name="tutorial-for-configuring-theaccesshub-admin-tool-with-azure-active-directory-b2c"></a>Azure Active Directory B2C를 사용하여 TheAccessHub 관리자 도구를 구성하기 위한 자습서

이 샘플 자습서에서는 Azure AD(Azure Active Directory) B2C를 N8 Identity의 [TheAccessHub 관리자 도구](https://n8id.com/products/theaccesshub-admintool/)와 통합하는 방법에 대한 지침을 제공합니다. 이 솔루션은 고객 계정 마이그레이션 및 CSR(Customer Service Requests) 관리를 다룹니다.  

이 솔루션은 다음과 같은 요구 사항이 하나 이상 있는 경우에 적합합니다.

- 기존 사이트가 있고 Azure AD B2C로 마이그레이션하고자 합니다. 그러나 암호를 비롯한 고객 계정의 마이그레이션에 어려움이 있습니다.

- Azure AD B2C 계정을 관리하려면 CSR에 도구가 필요합니다.

- CSR를 위해 위임된 관리를 사용해야 하는 요구 사항이 있습니다.

- 데이터를 여러 리포지토리에서 Azure AD B2C로 동기화하고 병합하고자 합니다.

## <a name="pre-requisites"></a>필수 구성 요소

시작하려면 다음이 필요합니다.

- Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.

- [Azure AD B2C 테넌트](./tutorial-create-tenant.md). 테넌트는 Azure 구독에 연결되어야 합니다.

- TheAccessHub 관리자 도구 환경: 새 환경을 프로비전하려면 [N8 Identity](https://n8id.com/contact/)에 문의하십시오.

- [선택] 마이그레이션하려는 고객 데이터의 모든 데이터베이스 또는 LDAP(Lightweight Directory Access protocol)를 위한 연결 및 자격 증명 정보.

- [선택] 등록 정책 흐름에 TheAccessHub 관리자 도구를 통합하려는 경우 [사용자 지정 정책](./tutorial-create-user-flows.md?pivots=b2c-custom-policy)을 사용하기 위해 구성된 Azure AD B2C 환경.

## <a name="scenario-description"></a>시나리오 설명

TheAccessHub 관리자 도구는 Azure의 다른 애플리케이션들처럼 실행됩니다. N8 Identity의 Azure 구독 또는 고객의 구독에서 실행할 수 있습니다. 다음 아키텍처 다이어그램은 구현된 상황을 보여 줍니다.

![n8identity 아키텍처 다이어그램을 보여 주는 이미지](./media/partner-n8identity/n8identity-architecture-diagram.png)

|단계 | Description |
|:-----| :-----------|
| 1. | 사용자가 로그인 페이지에 들어옵니다. 사용자는 등록을 선택하여 새 계정을 만들고 페이지에 정보를 입력합니다. Azure AD B2C가 사용자 특성을 수집합니다.
| 2. | Azure AD B2C가 TheAccessHub 관리자 도구를 호출하고 사용자 특성을 전달합니다.
| 3. | TheAccessHub 관리자 도구가 기존 데이터베이스에서 현재 사용자 정보를 확인합니다.  
| 4. | 사용자 레코드가 데이터베이스에서 TheAccessHub Admin 관리자 도구로 동기화됩니다.
| 5. | TheAccessHub 관리자 도구가 위임된 CSR/기술 지원팀 관리자와 데이터를 공유합니다.
| 6. | TheAccessHub 관리자 도구가 사용자 레코드를 Azure AD B2C와 동기화합니다.
| 7. |TheAccessHub 관리자 도구의 성공/실패 응답에 따라 Azure AD B2C가 사용자에게 사용자 지정된 환영 이메일을 보냅니다.

## <a name="create-a-global-admin-in-your-azure-ad-b2c-tenant"></a>Azure AD B2C 테넌트에 전역 관리자 만들기

TheAccessHub 관리자 도구가 전역 관리자를 대신하여 사용자 정보를 읽고 Azure AD B2C 테넌트의 변경을 수행하려면 권한이 필요합니다. 일반 관리자에 대한 변경 내용은 TheAccessHub 관리자 도구의 테넌트 상호 작용 기능에 영향을 주지 않습니다.

전역 관리자를 만드는 방법은 다음과 같습니다.

1. Azure Portal에서 Azure AD B2C 테넌트에 관리자로 로그인합니다. **Azure Active Directory** > **사용자** 로 이동합니다.
2. **새 사용자** 를 선택합니다.
3. **사용자 만들기** 를 선택하여 고객이 아닌 일반 디렉터리 사용자를 생성합니다.
4. ID 정보 양식을 완료합니다.

   a. 사용자 이름(예: 'theaccesshub')을 입력합니다.

   b. 이름(예: 'TheAccessHub Service Account')을 입력합니다.

5. **암호 표시** 를 선택하고 추후 사용에 대비해 초기 암호를 복사합니다.
6. 전역 관리자 역할을 할당합니다.

   a. 사용자의 현재 역할 **사용자** 를 선택하여 변경합니다.

   b. 전역 관리자 레코드를 확인합니다.

   다. **선택** > **만들기**

## <a name="connect-theaccesshub-admin-tool-with-your-azure-ad-b2c-tenant"></a>Azure AD B2C 테넌트와 TheAccessHub 관리자 도구 연결

TheAccessHub 관리자 도구는 Microsoft의 Graph API을 사용하여 디렉터리를 읽고 변경합니다. 테넌트에서 전역 관리자 역할을 합니다. TheAccessHub 관리자 도구에는 추가 권한이 필요하며, TheAccessHub 관리자 도구 내에서부터 권한을 부여할 수 있습니다.

TheAccessHub 관리자 도구에 디렉터리에 대한 액세스 권한을 부여하려면 다음 단계를 수행합니다.

1. N8 Identity에서 제공한 자격 증명을 사용하여 TheAccessHub 관리자 도구에 로그인합니다.

2. **시스템 관리자** > **Azure AD B2C 구성** 으로 이동합니다.

3. **연결 권한 부여** 를 선택합니다.

4. 새 창에서 전역 관리자 계정으로 로그인합니다. 새 서비스 계정으로 처음 로그인하는 경우 암호를 다시 설정하라는 메시지가 표시될 수 있습니다.

5. 프롬프트에 따라 **동의함** 을 선택하여 TheAccessHub 관리자 도구에 요청된 사용 권한을 부여합니다.

## <a name="configure-a-new-csr-user-using-your-enterprise-identity"></a>엔터프라이즈 ID를 사용하여 새 CSR 사용자 구성

기존 엔터프라이즈 Azure Active Directory 자격 증명을 사용하여 TheAccessHub 관리자 도구에 액세스하는 CSR/기술 지원팀 사용자를 만듭니다.

SSO(Single Sign-on)를 사용하여 CSR/기술 지원팀 사용자를 구성하는 권장 방법은 다음과 같습니다.

1. N8 Identity에서 제공한 자격 증명을 사용하여 TheAccessHub 관리자 도구에 로그인합니다.

2. **관리자 도구** > **동료 관리** 로 이동합니다.

3. **동료 추가** 를 선택합니다.

4. **동료 유형 Azure 관리자** 를 선택합니다.

5. 동료의 프로필 정보를 입력합니다.

   a. 홈 조직을 선택하면 이 사용자에 대한 관리 권한이 있는 사용자를 제어하게 됩니다.

   b. 로그인 ID/Azure AD 사용자 이름의 경우 사용자의 Azure Active Directory 계정의 사용자 계정 이름을 제공합니다.

   다. TheAccessHub 역할 탭에서 관리되는 역할 기술 지원팀을 확인합니다. 그러면 사용자가 동료 관리 보기에 액세스할 수 있습니다. 여전히 사용자는 그룹에 배치되거나 고객에 조치를 취하는 조직 소유자가 되어야 합니다.

6. **제출** 을 선택합니다.

## <a name="configure-a-new-csr-user-using-a-new-identity"></a>새 ID를 사용하여 새 CSR 사용자 구성

TheAccessHub 관리자 도구 고유의 새 로컬 자격 증명을 사용하여 TheAccessHub 관리자 도구에 액세스하는 CSR/기술 지원팀 사용자를 만듭니다. 엔터프라이즈에 Azure AD를 사용하지 않는 조직에서 주로 사용됩니다.

SSO 없이 [CSR/기술 지원팀 사용자 설정](https://youtu.be/iOpOI2OpnLI)을 하려면 다음 단계를 수행합니다.

1. N8 Identity에서 제공한 자격 증명을 사용하여 TheAccessHub 관리자 도구에 로그인합니다.

2. **관리자 도구** > **동료 관리** 로 이동합니다.

3. **동료 추가** 를 선택합니다.

4. **동료 유형 로컬 관리자** 를 선택합니다.

5. 동료의 프로필 정보를 입력합니다.

   a. 홈 조직을 선택하면 이 사용자에 대한 관리 권한이 있는 사용자를 제어하게 됩니다.

   b. **TheAccessHub 역할** 탭에서 관리되는 역할 **기술 지원팀** 을 선택합니다. 그러면 사용자가 동료 관리 보기에 액세스할 수 있습니다. 여전히 사용자는 그룹에 배치되거나 고객에 작용하는 조직 소유자가 되어야 합니다.

6. **로그인 ID/이메일** 과 **일회성 암호** 특성을 복사합니다. 새 사용자에게 제공합니다. 새 사용자는 이 자격 증명을 사용하여 TheAccessHub 관리자 도구에 로그인합니다. 사용자에게 처음 로그인 할 때 새 암호를 입력하라는 메시지가 표시됩니다.

7. **제출** 을 선택합니다.

## <a name="configure-partitioned-csr-administration"></a>분할된 CSR 관리 구성

TheAccessHub 관리자 도구에서 고객 및 CSR/기술 지원팀 사용자를 관리할 수 있는 권한은 조직 계층 구조를 사용하여 관리됩니다. 모든 동료와 고객에게는 자신이 위치하는 홈 조직이 있습니다. 특정 동료 또는 동료 그룹을 조직의 소유자로 할당할 수 있습니다.  조직 소유자는 자신이 소유한 조직 또는 하위 조직의 동료와 고객을 관리(변경)할 수 있습니다. 여러 동료가 사용자 집합을 관리할 수 있도록 하려면 여러 멤버로 그룹을 만들 수 있습니다. 그런 다음 그룹을 조직 소유자로 할당하면 모든 그룹 구성원이 조직의 동료와 고객을 관리할 수 있습니다.

### <a name="create-a-new-group"></a>새 그룹 만들기

1. N8 Identity에서 제공한 **자격 증명** 을 사용하여 TheAccessHub 관리자 도구에 로그인합니다.

2. **조직 > 그룹 관리** 로 이동합니다.

3. 선택 > **그룹 추가**

4. **그룹 이름**, **그룹 설명**, **그룹 소유자** 를 입력합니다.

5. 그룹의 멤버로 지정할 동료의 확인란을 검색하여 선택한 다음, 선택 >**추가** 를 선택합니다.

6. 페이지 하단에서 그룹의 모든 멤버를 볼 수 있습니다.

7. 필요한 경우 행의 끝에 있는 **x** 를 선택하여 멤버를 제거할 수 있습니다.

8. **제출** 을 선택합니다.

### <a name="create-a-new-organization"></a>새 조직 만들기

1. N8 Identity에서 제공한 자격 증명을 사용하여 TheAccessHub 관리자 도구에 로그인합니다.

2. 조직 > **조직 관리** 로 이동합니다.

3. 선택 > **조직 추가**

4. **조직 이름**, **조직 소유자**, **상위 조직** 을 제공합니다.

    a. 조직 이름은 원칙적으로는 고객 데이터와 일치하는 값입니다. 동료와 고객 데이터를 로드할 때 로드에 있는 조직의 이름을 제공하는 경우 해당 동료가 조직에 자동으로 배치될 수 있습니다.

    b. 소유자는 이 조직과 조직 내 모든 하위 조직의 고객과 동료를 관리하는 개인 또는 그룹을 대표합니다.

    다. 상위 조직은 어떤 다른 조직이 본질적으로 이 조직도 담당하는지 보여 줍니다.

5. **제출** 을 선택합니다.

### <a name="modify-the-hierarchy-via-the-tree-view"></a>트리 뷰를 통한 계층 구조 수정

1. N8 Identity에서 제공한 자격 증명을 사용하여 TheAccessHub 관리자 도구에 로그인합니다.

2. **관리자 도구** > **트리 뷰** 로 이동합니다.

3. 이 표현에서 어떤 동료와 그룹이 어떤 조직을 관리할 수 있는지 시각화할 수 있습니다.

4. 상위 조직으로 지정하고자 하는 상위 조직으로 조직을 끌어오면 계층 구조를 수정할 수 있습니다.

5. 계층 구조 변경을 마치면 **저장** 을 선택합니다.

## <a name="customize-welcome-notification"></a>환영 알림 사용자 지정

TheAccessHub를 사용하여 기존 인증 솔루션에서 Azure AD B2C로 사용자를 마이그레이션하는 동안 마이그레이션 중 TheAccessHub에서 사용자에게 보내는 사용자 환영 알림을 사용자 지정할 수 있습니다. 이 메시지에는 일반적으로 사용자가 Azure AD B2C 디렉터리에 새 암호를 설정하는 링크가 포함됩니다.

알림 사용자 지정 방법

1. N8 Identity에서 제공한 자격 증명을 사용하여 TheAccessHub에 로그인합니다.

2. **시스템 관리자** > **알림** 으로 이동합니다.

3. **동료 만들기 템플릿** 을 선택합니다.

4. **편집** 을 선택합니다.

5. 필요에 따라 메시지 및 템플릿 필드를 변경합니다. 템플릿 필드는 HTML을 인식하며 고객 이메일에 HTML 형식의 알림을 보낼 수 있습니다.

6. 작업을 마쳤으면 **저장** 을 선택합니다.

## <a name="migrate-data-from-external-data-sources-to-azure-ad-b2c"></a>외부 데이터 원본에서 Azure AD B2C로 데이터 마이그레이션

TheAccessHub 관리자 도구를 사용하여 다양한 데이터베이스, LDAP, CSV 파일 등에서 데이터를 가져온 다음 해당 데이터를 Azure AD B2C 테넌트에 푸시할 수 있습니다. TheAccessHub 관리자 도구 내의 Azure AD B2C 사용자 동료 형식으로 데이터를 로드하면 됩니다.  데이터 원본이 Azure 자체가 아닌 경우 데이터는 TheAccessHub 관리자 도구와 Azure AD B2C 모두에 배치됩니다. 외부 데이터의 원본이 컴퓨터의 단순한 .csv 파일이 아닌 경우 데이터 로드를 수행하기 전에 데이터 원본을 설정합니다. 아래는 데이터 원본을 만들고 데이터 로드를 수행하는 단계입니다.

### <a name="configure-a-new-data-source"></a>새 데이터 원본 구성

1. N8 Identity에서 제공한 자격 증명을 사용하여 TheAccessHub 관리자 도구에 로그인합니다.

2. **시스템 관리자**  >  **데이터 원본** 으로 이동합니다.

3. **데이터 원본 추가** 를 선택합니다.

4. 데이터 원본의 **이름** 및 **형식** 을 제공합니다.

5. 데이터 원본 형식에 따라 양식 데이터를 입력합니다.

   **데이터베이스**

   a. **형식** - 데이터베이스

   b. **데이터베이스 형식** – 지원되는 데이터베이스 형식 중 하나에서 데이터베이스를 선택합니다.

   다. **연결 URL** – 올바른 형식의 JDBC 연결 문자열을 입력합니다. 예: ``jdbc:postgresql://myhost.com:5432/databasename``

   d. **사용자 이름** – 데이터베이스에 액세스하기 위한 사용자 이름을 입력합니다.

   e. **암호** – 데이터베이스에 액세스하기 위한 암호를 입력합니다.

   f. **쿼리** – 고객 세부 정보를 추출하는 SQL 쿼리를 입력합니다. 예: ``SELECT * FROM mytable;``

   g. **연결 테스트** 를 선택하면 연결이 작동하는지 확인하는 데이터 샘플이 표시됩니다.

   **LDAP**

   a. **형식** – LDAP

   b. **호스트** – LDAP 서버가 실행되는 컴퓨터의 호스트 이름 또는 IP를 입력합니다. 예: ``mysite.com``

   다. **포트** – LDAP 서버가 수신 대기하는 포트 번호를 입력합니다.

   d. **SSL** – TheAccessHub 관리자 도구가 SSL을 사용하여 안전하게 LDAP와 통신해야 하는 경우 확인란을 선택합니다. SSL 사용을 적극 권장합니다.

   e. **로그인 DN** – 로그인할 사용자 계정의 DN을 입력하고 LDAP 검색을 수행합니다.

   f. **암호** - 사용자 암호를 입력합니다.

   g. **기본 DN** – 검색을 수행하려는 계층 구조의 맨 위에 있는 DN을 입력합니다.

   h. **필터** – 고객 레코드를 가져올 LDAP 필터 문자열을 입력합니다.

   i. **특성** – TheAccessHub 관리자 도구에 전달할 고객 레코드에 있는 쉼표로 구분된 특성 목록을 입력합니다.

   j. **연결 테스트** 를 선택하면 연결이 작동하는지 확인하는 데이터 샘플이 표시됩니다.

   **OneDrive**

   a. **형식** – OneDrive for Business

   b. **연결 권한 부여** 를 선택합니다.

   다. **OneDrive** 에 로그인하라는 메시지가 새 창에 표시됩니다. OneDrive 계정에 대한 읽기 권한이 있는 사용자로 로그인합니다. TheAccessHub 관리자 도구는 사용자가 CSV 로드 파일을 읽을 수 있도록 합니다.

   d. 프롬프트에 따라 **동의함** 을 선택하여 TheAccessHub 관리자 도구에 요청된 사용 권한을 부여합니다.

6. 작업을 마쳤으면 **저장** 을 선택합니다.  

### <a name="synchronize-data-from-your-data-source-into-azure-ad-b2c"></a>데이터 원본의 데이터를 Azure AD B2C로 동기화

1. N8 Identity에서 제공한 자격 증명을 사용하여 TheAccessHub 관리자 도구에 로그인합니다.

2. **시스템 관리자** > **데이터 동기화** 로 이동합니다.

3. **새 로드** 를 선택합니다.

4. **동료 형식** Azure AD B2C 사용자를 선택합니다.

5. **원본** 을 선택하고 팝업 대화 상자에서 데이터 원본을 선택합니다. OneDrive 데이터 원본을 만든 경우에도 해당 파일을 선택합니다.

6. 이 로드를 사용하여 새 고객 계정을 만들지 않으려면 첫 번째 정책(**TheAccessHub에서 동료를 찾을 수 없는 경우**)를 **작업 안 함** 으로 변경합니다.

7. 이 로드를 사용하여 기존 고객 계정을 업데이트하지 않으려면 두 번째 정책 **원본 및 TheAccessHub 데이터가 일치하지 않는 경우** 를 **작업 안 함** 으로 변경합니다.

8. **다음** 을 선택합니다.

9. **검색-매핑 구성** 에서는 TheAccessHub 관리자 도구에 이미 로드된 고객과 로드 레코드를 상호 연결하는 방법을 파악합니다. 원본에서 하나 이상의 식별 특성을 선택합니다. 선택한 특성을 동일한 값을 갖는 TheAccessHub 관리자 도구의 특성과 일치시킵니다. 일치하는 항목이 있으면 기존 레코드가 재정의됩니다. 그렇지 않을 경우 새 고객이 생성됩니다. 이러한 확인 작업이 여럿일 경우 순서를 지정할 수 있습니다. 예를 들어 이메일을 먼저 확인한 다음, 성과 이름을 확인할 수 있습니다.

10. 왼쪽 메뉴에서 **데이터 매핑** 을 선택합니다.

11. 데이터-매핑 구성에서는 원본 특성에서 채워야 하는 TheAccessHub 관리자 도구 특성을 할당합니다. 모든 특성을 매핑할 필요는 없습니다. 매핑되지 않은 특성은 기존 고객에 대해 변경되지 않은 상태를 유지합니다.

12. 기존 조직의 이름인 값으로 org_name 특성에 매핑하면 새로 만든 고객이 해당 조직에 배치됩니다.

13. **다음** 을 선택합니다.

14. 이 로드가 반복 발생해야 하는 경우 일별/주별 또는 월별 일정을 지정할 수 있습니다. 그렇지 않은 경우 기본값 **지금** 을 유지합니다.

15. **제출** 을 선택합니다.

16. **지금 일정** 을 선택하면 새 레코드가 데이터 동기화 화면에 즉시 추가됩니다. 유효성 검사 단계가 100%에 도달하면 **새 레코드** 를 선택하여 예상되는 로드 결과를 확인합니다. 예약된 로드의 경우 이러한 레코드는 예약된 시간 후에만 표시됩니다.

17. 오류가 없으면 **실행** 을 선택하여 변경 내용을 커밋합니다. 그렇지 않은 경우에는 **추가** 메뉴에서 **제거** 를 선택하여 로드를 제거합니다. 그런 다음 원본 데이터나 로드 매핑을 수정하고 다시 시도할 수 있습니다. 대신 오류 수가 작은 경우 수동으로 레코드를 업데이트하고 각 레코드에서 **업데이트** 를 선택하여 수정할 수 있습니다. 마지막으로, 오류가 있는 상태에서 계속 진행한 뒤 나중에 TheAccessHub 관리자 도구에서 **지원 개입** 으로 오류를 해결할 수 있습니다.

18. 로드 단계에서 **데이터 동기화** 레코드가 100%가 되면 로드로 인해 발생하는 모든 변경 내용이 시작됩니다. 고객은 Azure AD B2C에서 표시되거나 변경 내용을 수신하기 시작해야 합니다.

## <a name="synchronize-azure-ad-b2c-customer-data"></a>Azure AD B2C 고객 데이터 동기화 

일회성 또는 지속적인 작업으로서 TheAccessHub 관리자 도구는 Azure AD B2C의 모든 고객 정보를 TheAccessHub 관리자 도구로 동기화할 수 있습니다. 이렇게 하면 CSR/기술 지원팀 관리자가 최신 고객 정보를 볼 수 있습니다.

Azure AD B2C에서 TheAccessHub 관리자 도구로 데이터를 동기화하는 방법

1. N8 Identity에서 제공한 자격 증명을 사용하여 TheAccessHub 관리자 도구에 로그인합니다.

2. **시스템 관리자** > **데이터 동기화** 로 이동합니다.

3. **새 로드** 를 선택합니다.

4. **동료 형식** Azure AD B2C 사용자를 선택합니다.

5. **옵션** 단계에서는 기본값을 그대로 둡니다.

6. **다음** 을 선택합니다.

7. **데이터 매핑 및 검색** 단계에서는 기본값을 그대로 둡니다. 기존 조직의 이름인 값으로 **org_name** 특성에 매핑하는 경우를 제외하면 새로 만든 고객이 해당 조직에 배치됩니다.

8. **다음** 을 선택합니다.

9. 이 로드가 반복 발생해야 하는 경우 일별/주별 또는 월별 일정을 지정할 수 있습니다. 그렇지 않은 경우 기본값 **지금** 을 유지합니다. Azure AD B2C에서 정기적으로 동기화하는 것이 좋습니다.

10. **제출** 을 선택합니다.

11. **지금** 일정을 선택하면 새 레코드가 데이터 동기화 화면에 즉시 추가됩니다. 유효성 검사 단계가 100%에 도달하면 새 레코드를 선택하여 예상되는 로드 결과를 확인합니다. 예약된 로드의 경우 이러한 레코드는 예약된 시간 후에만 표시됩니다.

12. 오류가 없으면 **실행** 을 선택하여 변경 내용을 커밋합니다. 그렇지 않은 경우에는 추가 메뉴에서 **제거** 를 선택하여 로드를 제거합니다. 그런 다음 원본 데이터나 로드 매핑을 수정하고 다시 시도할 수 있습니다. 대신 오류 수가 작은 경우 수동으로 레코드를 업데이트하고 각 레코드에서 **업데이트** 를 선택하여 수정할 수 있습니다. 마지막으로, 오류가 있는 상태에서 계속 진행한 뒤 나중에 TheAccessHub 관리자 도구에서 지원 개입으로 오류를 해결할 수 있습니다.

13. 로드 단계에서 **데이터 동기화** 레코드가 100%가 되면 로드로 인해 발생하는 모든 변경 내용이 시작됩니다.

## <a name="configure-azure-ad-b2c-policies"></a>Azure AD B2C 정책 구성

TheAccessHub 관리자 도구의 동기화는 Azure AD B2C에 대한 최신 상태를 유지하는 기능에 있어서 제한적인 경우가 있습니다. TheAccessHub 관리자 도구의 API 및 Azure AD B2C 정책을 활용하면 변경 사항이 있을 때마다 TheAccessHub 관리자 도구에 이를 알릴 수 있습니다. 이 솔루션에는 [Azure AD B2C 사용자 지정 정책](./user-flow-overview.md)에 대한 기술 지식이 필요합니다. 다음 섹션에서는 등록 사용자 지정 정책에서 TheAccessHub 관리자 도구에 새 계정을 알리는 정책 단계 및 보안 인증서의 예를 제공합니다.

### <a name="create-a-secure-credential-to-invoke-theaccesshub-admin-tools-api"></a>TheAccessHub 관리자 도구의 API를 호출하는 보안 자격 증명 만들기

1. N8 Identity에서 제공한 자격 증명을 사용하여 TheAccessHub 관리자 도구에 로그인합니다.

2. **시스템 관리자** > **관리자 도구** > **API 보안** 으로 이동합니다.

3. **생성** 을 선택합니다.

4. **인증서 암호** 를 복사합니다.

5. **다운로드** 를 선택하여 클라이언트 인증서를 가져옵니다.

6. 이 [자습서](./secure-rest-api.md#https-client-certificate-authentication )에 따라 클라이언트 인증서를 Azure AD B2C에 추가합니다.

### <a name="retrieve-your-custom-policy-examples"></a>사용자 지정 정책 예제 검색

1. N8 Identity에서 제공한 자격 증명을 사용하여 TheAccessHub 관리자 도구에 로그인합니다.

2. **시스템 관리자** > **관리자 도구** > **Azure B2C 정책** 으로 이동합니다.

3. Identity Experience Framework 구성에서 Azure AD B2C 테넌트 도메인과 두 개의 Identity Experience Framework ID를 제공합니다.

4. **저장** 을 선택합니다.

5. **다운로드** 를 선택하여 고객이 등록할 때 TheAccessHub 관리자 도구에 고객을 추가하는 기본 정책으로 zip 파일을 가져옵니다.

6. 이 [자습서](./tutorial-create-user-flows.md?pivots=b2c-custom-policy)에 따라 Azure AD B2C에서 사용자 지정 정책 디자인을 시작합니다.

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음 문서를 참조하세요.

- [Azure AD B2C의 사용자 지정 정책](./custom-policy-overview.md)

- [Azure AD B2C의 사용자 지정 정책 시작하기](tutorial-create-user-flows.md?pivots=b2c-custom-policy)