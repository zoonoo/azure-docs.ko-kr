---
title: Azure Active Directory와 응용 프로그램 관리 | Microsoft Docs
description: 이 문서는 온-프레미스, 클라우드 및 SaaS 응용 프로그램을 사용하여 Azure Active Directory를 통합하는 이점을 얻을 수 있습니다.
services: active-directory
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.topic: overview
ms.workload: identity
ms.date: 09/11/2018
ms.author: barbkess
ms.reviewer: arvinh
ms.openlocfilehash: 820c6a5cb0bf5fb2d98d27c171c1276866082700
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/12/2018
ms.locfileid: "44716188"
---
# <a name="application-management-with-azure-active-directory"></a>Azure Active Directory의 응용 프로그램 관리

Azure AD(Azure Active Directory)는 클라우드 및 온-프레미스 응용 프로그램에 안전하고 원활한 액세스를 제공합니다. 사용자는 한 번 로그인하여 Microsoft의 Office 365 및 기타 비즈니스 응용 프로그램, 수천 개의 SaaS(Software as a Service) 응용 프로그램, 온-프레미스 응용 프로그램 및 LOB(사업 부문) 앱에 액세스할 수 있습니다. 사용자 프로비저닝을 자동화하여 관리 비용을 절감합니다. 다단계 인증 및 조건부 액세스 정책을 사용하여 보안 응용 프로그램 액세스를 제공합니다.

![Azure AD를 통해 페더레이션된 응용 프로그램](media/migrate-adfs-apps-to-azure/migrate2.png)

## <a name="why-manage-applications-with-a-cloud-solution"></a>클라우드 솔루션을 사용하여 응용 프로그램을 관리하는 이유는 무엇인가요?

조직에는 사용자가 작업하는 데 사용해야 하는 수백 개의 응용 프로그램이 있을 수 있습니다. 사용자는 많은 장치 및 위치에서 이러한 응용 프로그램에 액세스합니다. 매일 새 응용 프로그램이 추가, 개발 및 중단됩니다. 너무 많은 응용 프로그램과 액세스 지점이 있는 경우 클라우드 기반 솔루션을 사용하여 모든 응용 프로그램에 대한 사용자 액세스를 관리하는 것이 그 어느 때보다 더 중요합니다.

## <a name="manage-risk-with-conditional-access-policies"></a>조건부 액세스 정책을 사용하여 위험 관리
Azure AD SSO(Single Sign-On)를 조건부 액세스 정책과 연결하면 응용 프로그램 액세스에 대한 높은 수준의 보안이 제공됩니다. 보안 기능에는 클라우드 규모 ID 보호, 위험 기반 액세스 제어, 기본 다단계 인증 및 조건부 액세스 정책이 포함됩니다. 이러한 기능을 사용하면 응용 프로그램 또는 높은 수준의 보안이 필요한 그룹을 기반으로 제어 정책을 세분화할 수 있습니다.

## <a name="improve-productivity-with-single-sign-on"></a>Single Sign-On으로 생산성 개선
응용 프로그램 및 Office 365에서 SSO(Single Sign-On)를 사용하도록 설정하면 로그인 프롬프트가 감소하거나 제거되어 기존 사용자를 위한 우수한 환경이 제공됩니다. 사용자 환경은 더 응집력이 있는 것으로 느껴지고 여러 프롬프트가 없거나 여러 암호를 관리할 필요가 없어 덜 산만합니다. 비즈니스 그룹은 셀프 서비스 및 동적 멤버 자격을 통해 액세스를 관리하고 승인할 수 있습니다. 비즈니스에서 적합한 사용자가 응용 프로그램에 대한 액세스를 관리하도록 하면 ID 시스템의 보안이 향상됩니다.

SSO는 보안을 개선합니다. ‘Single Sign-On을 사용하지 않으면’ 관리자가 각 개별 응용 프로그램의 사용자 계정을 만들고 업데이트해야 하며 이 작업에는 시간이 걸립니다. 또한, 사용자는 응용 프로그램에 액세스하기 위해 여러 자격 증명을 추적해야 합니다. 결과적으로 사용자는 암호를 적어 두거나 다른 암호 관리 솔루션을 사용하기 쉬우므로 데이터 보안 위험이 도입될 수 있습니다. 

## <a name="address-governance-and-compliance"></a>거버넌스 및 규정 준수 처리
Azure AD를 사용하면 SIEM(보안 인시던트 및 이벤트 관리)을 사용하는 보고서를 통해 응용 프로그램 로그인을 모니터링할 수 있습니다. 포털 또는 API에서 보고서에 액세스할 수 있습니다. 응용 프로그램에 액세스할 수 있는 사용자를 프로그래밍 방식으로 감사하고 액세스 검토를 통해 비활성 사용자에 대한 액세스를 제거합니다.

## <a name="manage-costs"></a>비용 관리
Azure AD로 마이그레이션하면 비용을 절약하고 온-프레미스 인프라를 관리하는 번거로움을 제거할 수 있습니다. 또한 Azure AD는 응용 프로그램에 대한 셀프 서비스 액세스를 제공하므로 관리자와 사용자의 시간이 절약됩니다. Single Sign-On을 사용하면 응용 프로그램별 암호를 사용할 필요가 없어 응용 프로그램의 암호 재설정 및 암호 검색 시 손실된 생산성에 관련된 비용이 절약됩니다.

