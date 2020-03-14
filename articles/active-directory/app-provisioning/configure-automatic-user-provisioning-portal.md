---
title: Azure AD에서 엔터프라이즈 앱에 대 한 사용자 프로 비전 관리
description: Azure Active Directory를 사용하여 엔터프라이즈 앱에 대한 사용자 계정 프로비전을 관리하는 방법에 대해 알아봅니다.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/25/2019
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 54f9bd1afeebedf4cbf37d75d9c57f3d8be0f288
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/13/2020
ms.locfileid: "79264129"
---
# <a name="managing-user-account-provisioning-for-enterprise-apps-in-the-azure-portal"></a>Azure Portal에서 엔터프라이즈 앱에 대한 사용자 계정 프로비전 관리

이 문서에서는이를 지 원하는 응용 프로그램에 대 한 자동 사용자 계정 프로 비전 및 프로 비전 해제를 관리 하는 일반적인 단계에 대해 설명 합니다. *사용자 계정 프로비전*은 애플리케이션의 로컬 사용자 프로필 저장소에 사용자 계정 레코드를 생성, 업데이트 및/또는 비활성화하는 작업입니다. 대부분의 클라우드 및 SaaS 응용 프로그램은 사용자의 로컬 사용자 프로필 저장소에 사용자 역할 및 사용 권한을 저장 하며, Single Sign-On 하 고 작업에 액세스 하려면 사용자의 로컬 저장소에 해당 사용자 레코드가 *있어야* 합니다. 자동 사용자 계정 프로 비전에 대해 자세히 알아보려면 [Azure Active Directory를 사용 하 여 SaaS 응용 프로그램에 대 한 사용자 프로 비전 및 프로 비전](user-provisioning.md)해제를 참조 하세요.

> [!IMPORTANT]
> Azure ad (Azure Active Directory)에는 Azure AD를 사용 하 여 자동으로 프로 비전 할 수 있는 수천 개의 사전 통합 응용 프로그램이 포함 된 갤러리가 있습니다. [SaaS 앱을 Azure Active Directory와 통합 하는 방법에 대 한 자습서 목록](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/)에서 응용 프로그램과 관련 된 프로 비전 설정 자습서를 먼저 찾아야 합니다. 프로 비전 연결을 만들기 위해 앱과 Azure AD를 구성 하는 방법에 대 한 단계별 지침을 찾을 수 있습니다.

## <a name="finding-your-apps-in-the-portal"></a>포털에서 앱 찾기

Azure Active Directory 포털을 사용 하 여 디렉터리에서 Single Sign-On 대해 구성 된 모든 응용 프로그램을 보고 관리할 수 있습니다. 엔터프라이즈 앱은 조직 내에서 배포 및 사용되는 앱입니다. 엔터프라이즈 응용 프로그램을 확인 하 고 관리 하려면 다음 단계를 따르세요.

1. [Azure Active Directory 포털](https://aad.portal.azure.com)을 엽니다.
1. 왼쪽 창에서 **엔터프라이즈 응용 프로그램** 을 선택 합니다. 갤러리에서 추가 된 앱을 포함 하 여 구성 된 모든 앱의 목록이 표시 됩니다.
1. 앱을 선택 하 여 리소스 창을 로드 하면 보고서를 보고 앱 설정을 관리할 수 있습니다.
1. **프로 비전** 을 선택 하 여 선택한 앱에 대 한 사용자 계정 프로 비전 설정을 관리 합니다.

   ![사용자 계정 프로 비전 설정을 관리 하기 위한 프로 비전 화면](./media/configure-automatic-user-provisioning-portal/enterprise-apps-provisioning.png)

## <a name="provisioning-modes"></a>프로비전 모드

**프로 비전** 창은 엔터프라이즈 응용 프로그램에 대해 지원 되는 프로 비전 모드를 표시 하는 **모드** 메뉴로 시작 하 고 구성할 수 있습니다. 사용 가능한 옵션은 다음과 같습니다.

* **자동** -이 옵션은 Azure AD가이 응용 프로그램에 대 한 사용자 계정의 자동 API 기반 프로 비전 또는 프로 비전 해제를 지 원하는 경우에 표시 됩니다. 관리자에 게 도움이 되는 인터페이스를 표시 하려면이 모드를 선택 합니다.

  * 응용 프로그램의 사용자 관리 API에 연결 하도록 Azure AD 구성
  * Azure AD와 앱 간에 사용자 계정 데이터가 전달 되는 방법을 정의 하는 계정 매핑 및 워크플로 만들기
  * Azure AD 프로 비전 서비스 관리

* **수동** -Azure AD가이 응용 프로그램에 대 한 사용자 계정의 자동 프로 비전을 지원 하지 않는 경우이 옵션이 표시 됩니다. 이 경우 응용 프로그램에 저장 된 사용자 계정 레코드는 해당 응용 프로그램에서 제공 하는 사용자 관리 및 프로비저닝 기능 (SAML Just-in-time 프로 비전 포함 가능)에 따라 외부 프로세스를 사용 하 여 관리 해야 합니다.

## <a name="configuring-automatic-user-account-provisioning"></a>자동 사용자 계정 프로비전 구성

**자동** 옵션을 선택 하 여 관리자 자격 증명, 매핑, 시작 및 중지 및 동기화에 대 한 설정을 지정할 수 있습니다.

### <a name="admin-credentials"></a>관리자 자격 증명

**관리자 자격 증명** 을 확장 하 여 Azure AD에 응용 프로그램의 사용자 관리 API에 연결 하는 데 필요한 자격 증명을 입력 합니다. 필요한 입력은 애플리케이션에 따라 다릅니다. 특정 애플리케이션에 대한 자격 증명 형식 및 요구 사항에 대해 알아보려면 [특정 애플리케이션에 대한 구성 자습서](user-provisioning.md)를 참조하세요.

제공 된 자격 증명을 사용 하 여 Azure AD에서 앱의 프로 비전 앱에 연결을 시도 하 여 자격 증명을 테스트 하려면 **연결 테스트** 를 선택 합니다.

### <a name="mappings"></a>매핑

**매핑** 을 확장 하 여 사용자 계정이 프로 비전 되거나 업데이트 될 때 Azure AD와 대상 응용 프로그램 간에 흐르는 사용자 특성을 보고 편집 합니다.

Azure AD 사용자 개체와 각 SaaS 앱의 사용자 개체 사이에 미리 구성 된 매핑 집합이 있습니다. 일부 앱은 그룹이나 연락처 같은 다른 유형의 개체를 관리합니다. 테이블에서 매핑을 선택 하 여 오른쪽에 있는 매핑 편집기를 열고 사용자가이를 보고 사용자 지정할 수 있습니다.

![특성 매핑 화면을 표시 합니다.](./media/configure-automatic-user-provisioning-portal/enterprise-apps-provisioning-mapping.png)

지원되는 사용자 지정은 다음과 같습니다.

* Azure AD 사용자 개체를 SaaS 앱의 사용자 개체로 매핑하는 것과 같이 특정 개체에 대한 매핑을 사용하거나 사용하지 않도록 설정합니다.
* Azure AD 사용자 개체에서 앱의 사용자 개체로 흐르는 특성을 편집합니다. 특성 매핑에 대한 자세한 내용은 [특성 매핑 유형 이해하기](customize-application-attributes.md#understanding-attribute-mapping-types)를 참조하세요.
* 대상 응용 프로그램에서 Azure AD가 실행 하는 프로 비전 작업을 필터링 합니다. Azure AD에서 개체를 완전히 동기화 하는 대신 실행 작업을 제한할 수 있습니다.

  예를 들어, 선택 **업데이트** 및 Azure AD만 응용 프로그램에서 기존 사용자 계정을 업데이트 하지만 새로 만들지는 않습니다. **만들기** 만 선택 하 고 Azure는 새 사용자 계정만 만들지만 기존 계정을 업데이트 하지는 않습니다. 이 기능을 통해 관리자는 계정을 만들고 워크플로를 업데이트 하는 다른 매핑을 만들 수 있습니다.

* 새 특성 매핑 추가 **특성 매핑** 창의 맨 아래에 있는 **새 매핑 추가** 를 선택 합니다. **특성 편집** 양식을 작성 하 고 **확인** 을 선택 하 여 목록에 새 매핑을 추가 합니다.

### <a name="settings"></a>설정

**프로 비전** 화면의 **설정** 영역에서 선택한 응용 프로그램에 대 한 Azure AD 프로 비전 서비스를 시작 하 고 중지할 수 있습니다. 프로 비전 캐시를 지우고 서비스를 다시 시작 하도록 선택할 수도 있습니다.

프로비전을 애플리케이션에 대해 처음으로 사용하는 경우 **프로비전 상태**를 **켜기**로 변경하여 서비스를 활성화합니다. 이러한 변경으로 인해 Azure AD 프로 비전 서비스가 초기 주기를 실행 합니다. **사용자 및 그룹** 섹션에 할당 된 사용자를 읽고 대상 응용 프로그램을 쿼리 한 다음 Azure AD **매핑** 섹션에 정의 된 프로 비전 작업을 실행 합니다. 이 과정에서 프로 비전 서비스는 자신이 관리 하는 사용자 계정에 대해 캐시 된 데이터를 저장 하므로 할당 범위 내에 있지 않은 대상 응용 프로그램 내의 관리 되지 않는 계정은 프로 비전 해제 작업의 영향을 받지 않습니다. 초기 주기 후 프로 비전 서비스는 40 분 간격으로 사용자 및 그룹 개체를 자동으로 동기화 합니다.

프로 비전 서비스를 일시 중지 하려면 **프로 비전 상태** 를 **Off** 로 변경 합니다. 이 상태에서 Azure는 앱의 사용자 또는 그룹 개체를 만들거나, 업데이트 하거나, 제거 하지 않습니다. 상태를 다시 **설정** 으로 변경 하면 서비스가 중단 된 위치에서 선택 됩니다.

**현재 상태를 지우고 동기화를 다시 시작 하** 여 초기 주기를 트리거합니다. 그러면 서비스는 원본 시스템의 모든 사용자를 다시 평가 하 고 프로 비전 범위에 있는지 확인 합니다. 이는 응용 프로그램이 현재 격리 되 고 있거나 특성 매핑을 변경 해야 하는 경우에 유용할 수 있습니다. 계산 해야 하는 개체의 수로 인해 초기 주기는 일반적인 증분 주기 보다 완료 하는 데 더 오래 걸립니다. [여기](application-provisioning-when-will-provisioning-finish-specific-user.md)에서 초기 및 증분 주기의 성능에 대해 자세히 알아볼 수 있습니다. 
