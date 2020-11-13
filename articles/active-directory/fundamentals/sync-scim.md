---
title: Azure Active Directory와 SCIM 동기화
description: Azure Active Directory로 SCIM 동기화를 달성 하는 데 대 한 아키텍처 지침입니다.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/10/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: f957070ec94fc4c61089f31fe91261a2f52c4ee4
ms.sourcegitcommit: 1d6ec4b6f60b7d9759269ce55b00c5ac5fb57d32
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/13/2020
ms.locfileid: "94578861"
---
# <a name="scim-synchronization-with-azure-active-directory"></a>Azure Active Directory와 SCIM 동기화

SCIM (도메인 간 Id 관리)에 대 한 시스템은 id 도메인 및 IT 시스템 간의 사용자 id 정보 교환을 자동화 하기 위한 개방형 표준 프로토콜입니다. SCIM을 사용 하면 HCM (인간 자본 관리) 시스템에 추가 된 직원이 Azure Active Directory (Azure AD) 또는 Windows Server Active Directory에서 만든 계정을 자동으로 갖게 됩니다. 사용자 특성 및 프로필은 두 시스템 간에 동기화 되어 사용자 상태 또는 역할 변경에 따라 사용자 제거를 업데이트 합니다.

SCIM은 두 끝점의 표준화 된 정의 이며,/사용자 ' 끝점과 a/Groups 끝점입니다. 일반적인 REST 동사를 사용 하 여 개체를 만들고, 업데이트 하 고, 삭제 합니다. 또한 그룹 이름, 사용자 이름, 이름, 성, 전자 메일 등의 공통 특성에 대해 미리 정의 된 스키마를 사용 합니다. SCIM 2.0 REST API을 제공 하는 응용 프로그램은 독점적인 사용자 관리 Api 또는 제품을 사용 하는 어려움을 줄이거나 없앨 수 있습니다. 예를 들어 모든 SCIM 규격 클라이언트는/사용자 끝점에 JSON 개체의 HTTP POST를 수행 하 여 새 사용자 항목을 만들 수 있습니다. SCIM 표준을 준수하는 앱은 동일한 기본 작업에 대해 약간 다른 API를 사용하지 않고도 기존 클라이언트, 도구 및 코드를 바로 활용할 수 있습니다. 

## <a name="use-when"></a>사용하는 경우: 

HCM 시스템에서 Azure AD 및 Windows Server Active Directory로 사용자 정보를 자동으로 프로 비전 하 고 필요한 경우 대상 시스템에 자동으로 프로 비전 하려고 합니다. 

![아키텍처 다이어그램](./media/authentication-patterns/scim-auth.png)


## <a name="components-of-system"></a>시스템의 구성 요소 

* **Hcm 시스템** : 직원의 수명 주기 동안 HR 프로세스를 지원 하 고 자동화 하는 사용자 자본 관리 프로세스 및 사례를 지 원하는 응용 프로그램 및 기술입니다. 

* **AZURE AD 프로 비전 서비스** : 자동 프로 비전에 scim 2.0 프로토콜을 사용 합니다. 서비스는 응용 프로그램에 대 한 SCIM 끝점에 연결 하 고 SCIM 사용자 개체 스키마 및 REST Api를 사용 하 여 사용자 및 그룹의 프로 비전 및 프로 비전 해제를 자동화 합니다.  

* **AZURE AD** : id 및 자격 증명의 수명 주기를 관리 하는 데 사용 되는 사용자 리포지토리입니다. 

* **대상 시스템** : scim 끝점을 포함 하며 Azure AD 프로 비전과 연동 하 여 사용자 및 그룹의 자동 프로 비전을 사용 하도록 설정 하는 응용 프로그램 또는 시스템입니다.  

## <a name="implement-scim-with-azure-ad"></a>Azure AD로 SCIM 구현 

* [Azure AD에서 프로 비전이 작동 하는 방식 ](https://docs.microsoft.com/azure/active-directory/app-provisioning/how-provisioning-works)

* [Azure Portal에서 엔터프라이즈 앱에 대 한 사용자 계정 프로 비전 관리 ](https://docs.microsoft.com/azure/active-directory/app-provisioning/configure-automatic-user-provisioning-portal)

* [SCIM 끝점을 빌드하고 Azure AD를 사용 하 여 사용자 프로 비전 구성  ](https://docs.microsoft.com/azure/active-directory/app-provisioning/use-scim-to-provision-users-and-groups)

* [Azure AD 프로 비전 서비스의 SCIM 2.0 프로토콜 준수](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-config-problem-scim-compatibility)

