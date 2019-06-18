---
title: Azure Active Directory에서 엔터프라이즈 앱에 대한 사용자 프로비전 관리 | Microsoft Docs
description: Azure Active Directory를 사용하여 엔터프라이즈 앱에 대한 사용자 계정 프로비전을 관리하는 방법에 대해 알아봅니다.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/01/2019
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6bf265f51f2fea16f90dd0bcf2891bd9bed5cef8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65963579"
---
# <a name="managing-user-account-provisioning-for-enterprise-apps-in-the-azure-portal"></a>Azure Portal에서 엔터프라이즈 앱에 대한 사용자 계정 프로비전 관리

이 문서에서는 [Azure Portal](https://portal.azure.com)을 사용하여 자동 사용자 계정 프로비전 및 지원되는 애플리케이션에 대한 프로비전 해제를 관리하는 방법을 설명합니다. 자동 사용자 계정 프로비전 및 작동 방식에 대한 자세한 내용은 [Azure Active Directory를 사용하여 SaaS 애플리케이션에 사용자 프로비전 및 프로비전 해제 자동화](user-provisioning.md)를 참조하세요.

## <a name="finding-your-apps-in-the-portal"></a>포털에서 앱 찾기

Azure Active Directory 포털을 사용 하 여 보기 및 디렉터리에서에서 single sign-on에 대해 구성 된 모든 응용 프로그램을 관리 합니다. 엔터프라이즈 앱은 조직 내에서 배포 및 사용되는 앱입니다. 보기 및 엔터프라이즈 응용 프로그램을 관리 하려면 다음이 단계를 수행 합니다.

1. 엽니다는 [Azure Active Directory 포털](https://aad.portal.azure.com)합니다.

1. 선택 **엔터프라이즈 응용 프로그램** 왼쪽된 창에서. 갤러리에서 추가 된 앱을 비롯 한 모든 구성 된 앱의 목록이 표시 됩니다.

1. 보고서를 확인 하 고 앱 설정을 관리할 수 있는 해당 리소스 창 로드 앱을 선택 합니다.

1. 선택 **프로 비전** 선택한 앱에 대 한 설정을 프로 비전 하는 사용자 계정을 관리 합니다.

   ![애플리케이션 리소스 창](./media/configure-automatic-user-provisioning-portal/enterprise-apps-provisioning.png)

## <a name="provisioning-modes"></a>프로비전 모드

합니다 **프로 비전** 창 시작을 **모드** 메뉴에 엔터프라이즈 응용 프로그램에 대 한 지원 되는 프로 비전 모드를 표시 하 고 구성할 수 있습니다. 사용 가능한 옵션은 다음과 같습니다.

* **자동** -자동 API 기반 프로 비전 또는이 응용 프로그램에 사용자 계정을 프로 비전 해제 Azure AD에서 지 원하는 경우이 옵션이 표시 됩니다. 관리자는 데 도움이 되는 인터페이스를 표시 하기 위해이 모드를 선택 합니다.

  * 응용 프로그램의 사용자 관리 API에 연결 하려면 Azure AD를 구성 합니다.
  * 계정 매핑 및 Azure AD 간에 사용자 계정 데이터 흐르는 방식을 정의 하는 워크플로 만들기 및 앱
  * Azure AD 프로 비전 서비스를 관리 합니다.

* **수동** -Azure AD 사용자 계정을이 응용 프로그램의 자동 프로 비전 지원 하지 않습니다 하는 경우이 옵션이 표시 됩니다. 이 경우 해당 응용 프로그램 (SAML Just-In-Time 프로 비전을 포함할 수 있습니다)를 제공한 사용자 관리 및 프로 비전 기능을 기반으로 외부 프로세스를 사용 하 여 응용 프로그램에서 레코드를 저장 하는 사용자 계정에 관리 되어야 합니다.

## <a name="configuring-automatic-user-account-provisioning"></a>자동 사용자 계정 프로비전 구성

선택 된 **자동** 관리자 자격 증명, 시작 및 중지, 매핑 및 동기화에 대 한 설정을 지정 하는 옵션입니다.

### <a name="admin-credentials"></a>관리자 자격 증명

확장 **관리자 자격 증명** Azure AD 응용 프로그램의 사용자 관리 API에 연결 하는 데 필요한 자격 증명을 입력 합니다. 필요한 입력은 애플리케이션에 따라 다릅니다. 특정 애플리케이션에 대한 자격 증명 형식 및 요구 사항에 대해 알아보려면 [특정 애플리케이션에 대한 구성 자습서](user-provisioning.md)를 참조하세요.

선택 **연결 테스트** AD 앱에 연결 하려고의 제공된 된 자격 증명을 사용 하 여 앱을 프로비저닝 하는 데 Azure 함으로써 자격 증명을 테스트 합니다.

### <a name="mappings"></a>매핑

확장 **매핑을** Azure AD 사이 흐르는 사용자 특성 보기 및 편집 하 고 사용자 계정을 프로 비전 되거나 업데이트 되 면 대상 응용 프로그램.

미리 구성 된 Azure AD 사용자 개체와 각 SaaS 앱의 사용자 개체 간의 매핑 집합이 있습니다. 일부 앱은 그룹이나 연락처 같은 다른 유형의 개체를 관리합니다. 오른쪽으로 확인 하 고 사용자 지정할 수 있는 매핑 편집기를 열고 테이블의 매핑을 선택 합니다.

![애플리케이션 리소스 창](./media/configure-automatic-user-provisioning-portal/enterprise-apps-provisioning-mapping.png)

지원되는 사용자 지정은 다음과 같습니다.

* Azure AD 사용자 개체를 SaaS 앱의 사용자 개체로 매핑하는 것과 같이 특정 개체에 대한 매핑을 사용하거나 사용하지 않도록 설정합니다.
* Azure AD 사용자 개체에서 앱의 사용자 개체로 흐르는 특성을 편집합니다. 특성 매핑에 대한 자세한 내용은 [특성 매핑 유형 이해하기](customize-application-attributes.md#understanding-attribute-mapping-types)를 참조하세요.
* 대상된 응용 프로그램에서 실행 되는 Azure AD는 프로 비전 작업을 필터링 합니다. 완벽 하 게 개체를 동기화 하는 Azure AD 대신 실행할 작업을 제한할 수 있습니다. 

  예를 들어 선택할 **업데이트** 및 Azure AD만 업데이트 기존 사용자 응용 프로그램의 계정도 지원 하지만 새로 만들지 않습니다. 선택할 **만들기** 및 Azure만 새 사용자 계정을 만들지만 기존 업데이트 되지 않습니다. 이 기능에는 관리자를 계정 만들기 위해 여러 매핑을 만들고 워크플로 업데이트할 수 있습니다.

* 새 특성 매핑을 추가합니다. 선택 **새 매핑 추가** 맨 아래에 **특성 매핑** 창입니다. 입력 합니다 **특성 편집** 형성 하 고 선택 **확인** 목록에 새 매핑을 추가 하려면. 

### <a name="settings"></a>설정

시작 하 고 프로 비전 서비스에서 선택한 응용 프로그램에 대 한 Azure AD를 중지할 수 있습니다 합니다 **설정을** 영역의 합니다 **프로 비전** 화면. 프로 비전 캐시를 지우고 서비스를 다시 시작을 수도 있습니다.

프로비전을 애플리케이션에 대해 처음으로 사용하는 경우 **프로비전 상태**를 **켜기**로 변경하여 서비스를 활성화합니다. 이 변경으로 인해 초기 동기화를 실행 하려면 Azure AD 서비스를 프로 비전 합니다. 할당 된 사용자를 읽고 합니다 **사용자 및 그룹** 섹션에서 해당 대상 응용 프로그램을 쿼리하고 다음 Azure AD에서 정의 된 프로 비전 작업을 실행 **매핑** 섹션입니다. 이 과정에서 프로 비전 서비스 되지 할당에 대 한 범위에에서 있던 대상 응용 프로그램 내에서 관리 되지 않는 계정 프로 비전 해제 작업에서 영향을 받지 않습니다 있도록을 관리 하는 사용자 계정에 대 한 캐시 된 데이터를 저장 합니다. 초기 동기화 후 프로비전 서비스는 10분 간격으로 사용자 및 그룹 개체를 자동으로 동기화합니다.

변경 된 **프로 비전 상태** 에 **해제** 프로 비전 서비스를 일시 중지 합니다. 이 상태에서는 Azure 하지 만들기, 업데이트 또는 앱에서 사용자 또는 그룹 개체를 제거 합니다. 상태를 변경 **에서** 서비스가 중단 된 위치를 선택 합니다.

선택 합니다 **현재 상태를 지우고 동기화를 다시 시작** 확인란을 선택 하 고 **저장** 에:

* 프로 비전 서비스를 중지 합니다.
* Azure AD에서 관리 하는 계정에 대해 캐시 된 데이터를 덤프 합니다.
* 서비스를 다시 시작 하 고 초기 동기화를 다시 실행

이 옵션을 사용 하면 관리자를 다시 프로 비전 배포 프로세스를 시작 합니다.

### <a name="synchronization-details"></a>동기화 세부 정보

이 섹션에서는 응용 프로그램 및 관리 얼마나 많은 사용자 및 그룹 개체에 대해 프로 비전 서비스를 실행 하는 첫 번째 및 마지막 시간을 포함 하 여 프로 비전 서비스의 작업에 대 한 추가 정보를 제공 합니다.

에 링크가 제공 됩니다는 **프로 비전 활동 보고서**, 모든 사용자의 로그를 제공 하며 생성, 업데이트 및 제거 간의 Azure AD 그룹 및 대상 응용 프로그램입니다. 링크에도 제공 됩니다는 **프로 비전 오류 보고서**, 업데이트 또는 제거를 제공 하는 자세한 오류 메시지 사용자 및 그룹 개체를 생성, 읽기, 실패 합니다.
