---
title: SaaS 응용 프로그램에 자동 사용자 계정 프로비전 보고
description: 자동 사용자 계정 프로비전 작업의 상태를 확인하는 방법과 개별 사용자의 프로비전 문제를 해결하는 방법에 대해 알아봅니다.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: app-provisioning
ms.devlang: na
ms.topic: conceptual
ms.date: 09/09/2018
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 19d76f69669ffa13d1d55ffa807e6c4818b8840c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80282191"
---
# <a name="tutorial-reporting-on-automatic-user-account-provisioning"></a>자습서: 자동 사용자 계정 프로비전에 대한 보고

Azure Active Directory(Azure AD)에는 종단 간 ID 수명 주기 관리를 위해 SaaS 앱 및 기타 시스템에서 사용자 계정의 프로비저닝 해제를 자동화하는 데 도움이 되는 사용자 [계정 프로비저닝 서비스가](user-provisioning.md) 포함되어 있습니다. Azure AD는 [여기에](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)사용자 프로비전 자습서를 통해 모든 응용 프로그램 및 시스템에 대해 사전 통합된 사용자 프로비저닝 커넥터를 지원합니다.

이 문서에서는 프로비전 작업을 설정한 후 해당 상태를 확인하는 방법과 개별 사용자 및 그룹의 프로비전 문제를 해결하는 방법에 대해 설명합니다.

## <a name="overview"></a>개요

프로비전 커넥터는 지원되는 애플리케이션에 [제공된 설명서](../saas-apps/tutorial-list.md)에 따라 [Azure Portal](https://portal.azure.com)을 사용하여 설정하고 구성합니다. 일단 구성되고 실행된 후에는 다음 두 가지 방법 중 하나를 사용하여 프로비전 작업을 보고할 수 있습니다.

* **Azure 포털** - 이 문서에서는 주로 지정된 응용 프로그램에 대한 프로비저닝 요약 보고서와 자세한 프로비저닝 감사 로그를 제공하는 [Azure Portal에서](https://portal.azure.com)보고서 정보 검색을 설명합니다.
* **감사 API** - Azure Active Directory는 자세한 프로비저닝 감사 로그를 프로그래밍 방식으로 검색할 수 있게 해주는 감사 API도 제공합니다. 이 API의 사용과 관련하여 [Azure Active Directory 감사 API 참조](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit) 문서를 참조하세요. 이 문서에서는 API를 사용하는 방법을 구체적으로 다루지 않지만, 감사 로그에 기록되는 프로비전 이벤트 유형에 대해서는 자세히 설명합니다.

### <a name="definitions"></a>정의

이 문서에서 사용하는 용어는 다음과 같이 정의됩니다.

* **원본 시스템** - Azure AD 프로비전 서비스에서 동기화하기 위해 원본이 되는 사용자의 리포지토리입니다. Azure Active Directory는 대부분의 사전 통합된 프로비전 커넥터를 위한 원본 시스템이지만, Workday 인바운드 동기화와 같이 일부 예외가 있습니다.
* **대상 시스템** - Azure AD 프로비전 서비스에서 동기화하기 위해 대상이 되는 사용자의 리포지토리입니다. 일반적으로 SaaS 응용 프로그램(예: Salesforce, ServiceNow, G Suite, 비즈니스용 Dropbox)이지만 경우에 따라 Active Directory(예: Active Directory에 대한 근무일 인바운드 동기화)와 같은 온-프레미스 시스템이 될 수 있습니다.

## <a name="getting-provisioning-reports-from-the-azure-portal"></a>Azure 포털에서 프로비저닝 보고서 받기

지정된 응용 프로그램에 대한 프로비저닝 보고서 정보를 얻으려면 **활동** 섹션에서 [Azure 포털](https://portal.azure.com) 및 Azure **Active Directory** &gt; **엔터프라이즈 앱** &gt; **프로비저닝 로그(미리 보기)를** 시작하여 시작합니다. 프로비저닝이 구성된 엔터프라이즈 응용 프로그램으로 검색할 수도 있습니다. 예를 들어 사용자를 LinkedIn Elevate로 프로비전하는 경우 애플리케이션 세부 정보의 탐색 경로는 다음과 같습니다.

**Azure Active Directory &gt; 엔터프라이즈 애플리케이션 &gt; 모든 애플리케이션 &gt; LinkedIn Elevate**

여기에서 아래에 설명된 프로비저닝 진행률 표시줄과 프로비저닝 로그에 모두 액세스할 수 있습니다.

## <a name="provisioning-progress-bar"></a>프로비저닝 진행률 표시줄

[프로비저닝 진행률 표시줄은](application-provisioning-when-will-provisioning-finish-specific-user.md#view-the-provisioning-progress-bar) 지정된 응용 프로그램의 **프로비저닝** 탭에 표시됩니다. **설정**아래의 **현재 상태** 섹션에 있으며 현재 초기 또는 증분 주기의 상태를 표시합니다. 이 섹션에서는 다음을 보여 주기도 합니다.

* 동기화되어 현재의 원본 시스템과 대상 시스템 간 프로비전에 해당하는 범위에 포함된 총 사용자 및/또는 그룹 수
* 동기화가 마지막으로 실행된 시간 - 동기화는 일반적으로 [초기 주기가](../app-provisioning/how-provisioning-works.md#provisioning-cycles-initial-and-incremental) 완료된 후 20-40분마다 발생합니다.
* [초기 주기가](../app-provisioning/how-provisioning-works.md#provisioning-cycles-initial-and-incremental) 완료되었는지 여부입니다.
* 프로비저닝 프로세스의 격리 여부 및 격리 상태에 대한 이유(예: 잘못된 관리자 자격 증명으로 인해 대상 시스템과 통신하지 못하는 경우).

**현재 상태는** 관리자가 프로비저닝 작업의 운영 상태를 확인하는 첫 번째 장소여야 합니다.

 ![요약 보고서](./media/check-status-user-account-provisioning/provisioning-progress-bar-section.png)

## <a name="provisioning-logs-preview"></a>프로비저닝 로그(미리 보기)

프로비저닝 서비스에서 수행하는 모든 활동은 Azure AD [프로비저닝 로그에 기록됩니다.](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) **활동** 섹션에서 **Azure Active Directory** &gt; **엔터프라이즈 앱** &gt; **프로비저닝 로그(미리 보기)를** 선택하여 Azure 포털의 프로비저닝 로그에 액세스할 수 있습니다. 소스 시스템 또는 대상 시스템에서 사용자 이름 또는 식별자의 이름을 기반으로 프로비저닝 데이터를 검색할 수 있습니다. 자세한 내용은 [프로비저닝 로그(미리 보기)를](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context)참조하십시오. 기록되는 활동 이벤트 유형은 다음과 같습니다.

## <a name="troubleshooting"></a>문제 해결

프로비저닝 요약 보고서 및 프로비저닝 로그는 관리자가 다양한 사용자 계정 프로비저닝 문제를 해결하는 데 중요한 역할을 합니다.

자동 사용자 프로비전 문제를 해결하는 방법에 대한 시나리오 기반 지침은 [애플리케이션에 사용자를 구성 및 프로비전하는 문제](../app-provisioning/application-provisioning-config-problem.md)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

* [엔터프라이즈 앱용 사용자 계정 프로비저닝 관리](configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory의 애플리케이션 액세스 및 Single Sign-On이란 무엇입니까?](../manage-apps/what-is-single-sign-on.md)
