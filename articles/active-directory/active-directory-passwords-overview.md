---
title: "Azure AD 셀프 서비스 암호 재설정 개요 | Microsoft Docs"
description: "Azure AD의 셀프 서비스 암호 재설정이 조직에서 어떤 작업을 수행할 수 있나요?"
services: active-directory
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.reviewer: sahenry
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: joflore
ms.custom: it-pro
ms.openlocfilehash: de2d41d8c2787ffdc80daddf959487f6031da87d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-ad-self-service-password-reset-for-the-it-professional"></a>IT 전문가를 위한 Azure AD 셀프 서비스 암호 재설정

"셀프 서비스"는 전 세계의 많은 IT 부서 내부에서 throw되는 다양한 의미를 가진 용어입니다. 시장은 클라우드 또는 온-프레미스에서 온-프레미스 그룹, 암호 또는 사용자 프로필을 관리할 수 있는 제품으로 넘쳐납니다.

Azure Active Directory(Azure AD) SSPR(셀프 서비스 암호 재설정)은 사용 및 배포 편의로부터 분리됩니다. Azure AD 셀프 서비스 암호 재설정은 다음과 같은 일련의 기능을 결합합니다.

* 사용자가 자신의 암호를
  * 어떤 장치든지
  * 어디에서든지
  * 언제든지
* 관리자 권한으로 정의한 정책을 준수하여 관리할 수 있게 됩니다.

준비를 마쳤다면 [빠른 시작 설명서](active-directory-passwords-getting-started.md)를 사용하여 Azure AD SSPR을 시작하고 사용자가 자신의 고유한 암호를 재설정할 수 있습니다.

## <a name="what-is-possible"></a>가능한 기능

* **셀프 서비스 암호 변경**을 사용하면 최종 사용자나 관리자가 관리자의 도움 없이 자신의 암호를 변경할 수 있습니다.
* **셀프 서비스 계정 잠금 해제**를 사용하면 최종 사용자가 관리자의 도움 없이 자신의 계정의 잠금을 해제할 수 있습니다.
* **셀프 서비스 암호 재설정**을 사용하면 최종 사용자나 관리자는 관리자의 도움 없이 자신의 암호를 자동으로 재설정할 수 있습니다. 셀프 서비스 암호 재설정 기능을 사용하려면 Azure AD Premium 또는 Basic - [Azure Active Directory 버전](active-directory-editions.md)이 필요합니다.
* **관리자가 시작한 암호 재설정** 기능을 사용하여 관리자는 [Azure Portal](https://docs.microsoft.com/azure/azure-portal-overview) 내에서 최종 사용자나 다른 관리자의 암호를 재설정할 수 있습니다.
* **암호 관리 작업 보고서**는 조직에서 발생하는 암호 재설정 및 등록 작업에서의 관리자 이해도를 높여줍니다. [관리 보고서](active-directory-passwords-reporting.md)
* **비밀번호 쓰기 저장**을 사용하면 클라우드에서 온-프레미스 암호를 관리할 수 있으므로 이전의 모든 시나리오에서 수행되거나 페더레이션 및 암호가 동기화된 사용자가 대신 수행할 수 있습니다. 비밀번호 쓰기 저장을 사용하려면 [Azure AD Premium](active-directory-get-started-premium.md)이 필요합니다.

## <a name="why-choose-azure-ad-self-service-password-reset"></a>Azure AD 셀프 서비스 암호 재설정을 선택하는 이유

* **비용 절감** 기술 지원팀의 지원 기반 암호 재설정은 일반적으로 IT 조직 지출의 20%입니다.
* **최종 사용자 환경 개선** 및 **기술 지원팀 볼륨 감소** 기술 지원팀에 연락하거나 지원을 요청하지 않고 한 번에 고유한 암호 문제를 해결하기 위해 최종 사용자에게 권한을 부여합니다.
* **이동성 공급** - 사용자가 어디에서든 암호를 재설정할 수 있습니다

## <a name="azure-ad-self-service-password-reset-availability"></a>Azure AD 셀프 서비스 암호 재설정 가용성

Azure AD 셀프 서비스 암호 재설정은 구독에 따라 세 가지 계층으로 제공됩니다.

* **Azure AD Free** - 클라우드 전용 관리자가 자신의 암호를 다시 설정할 수 있습니다.
* **Azure AD Basic** 또는 **유료 Office 365 구독** - 클라우드 전용 사용자가 자신의 암호를 변경할 수 있고, 클라우드 전용 관리자가 자신의 암호를 다시 설정할 수 있습니다.
* **Azure AD Premium** - 사용자 또는 관리자는 클라우드 전용, 페더레이션된 또는 암호 동기화된 사용자를 포함하여 해당하는 고유한 암호를 재설정할 수 있습니다. 온-프레미스 암호는 비밀번호 쓰기 저장을 사용하도록 설정해야 합니다.

## <a name="azure-ad-self-service-password-reset-a-sum-of-the-parts"></a>Azure AD 셀프 서비스 암호 재설정, 부분의 합

Azure AD의 셀프 서비스 암호 재설정은 다음 구성 요소로 구성됩니다.

* **암호 관리 구성 포털** Azure Portal을 통해 테넌트에서 암호를 관리하는 방법에 대한 옵션을 제어할 수 있습니다
* **암호 재설정 등록 포털** 사용자가 암호 재설정을 자동 등록할 수 있습니다.
* **암호 재설정 포털** 관리자가 정의한 과제 및 사용자가 제공한 답변을 사용하여 사용자는 자신의 암호를 재설정할 수 있습니다.
* **사용자 암호 변경 포털** 사용자는 이전 암호를 입력하고 새 암호를 제공하여 자신의 암호를 변경할 수 있습니다.
* **암호 관리 보고서** 관리자는 Azure Portal에서 해당 테넌트에 대한 암호 작업 보고서를 보고 분석할 수 있습니다.
* **Azure AD Connect를 사용하여 온-프레미스에 비밀번호 쓰기 저장**을 사용하면 클라우드에서 온-프레미스, 페더레이션 또는 암호 동기화 사용자를 관리할 수 있습니다

## <a name="azure-ad-pricing-sla-updates-and-roadmap"></a>Azure AD 가격 책정, SLA, 업데이트 및 로드맵

다음 페이지에서 이러한 항목에 대한 자세한 정보를 찾을 수 있습니다.

* [**Azure AD 가격 책정 정보**](https://azure.microsoft.com/pricing/details/active-directory/)
* [**Office 365 가격 책정**](https://products.office.com/compare-all-microsoft-office-products?tab=2)
* [**Azure Service Level Agreements(서비스 수준 약정)**](https://azure.microsoft.com/support/legal/sla/)
* [**Microsoft 온라인 서비스에 대한 Service Level Agreements(서비스 수준 약정)**](http://go.microsoft.com/fwlink/?LinkID=272026&clcid=0x409)
* [**Azure 업데이트**](https://azure.microsoft.com/updates/)
* [**Azure 로드맵**](https://www.microsoft.com/cloud-platform/roadmap-recently-available)

## <a name="next-steps"></a>다음 단계

다음 링크는 Azure AD를 사용한 암호 재설정에 대한 추가 정보를 제공합니다.

* [**빠른 시작**](active-directory-passwords-getting-started.md) - Azure AD 셀프 서비스 암호 관리를 사용하여 운영 시작 
* [**라이선스**](active-directory-passwords-licensing.md) - Azure AD 라이선스 구성
* [**데이터**](active-directory-passwords-data.md) - 암호 관리에 필요한 데이터 및 사용 방식을 이해
* [**롤아웃**](active-directory-passwords-best-practices.md) - 여기서 제공하는 지침을 사용하여 배포 계획을 세우고 사용자에게 SSPR 배포
* [**사용자 지정**](active-directory-passwords-customize.md) - 회사 SSPR 경험의 모양과 느낌을 사용자 지정.
* [**보고**](active-directory-passwords-reporting.md) - 사용자가 SSPR 기능에 액세스하는 조건, 시간 및 위치 탐색
* [**기술 심층 분석**](active-directory-passwords-how-it-works.md) - 작동 방식을 이해하기 위해 심층 분석
* [**질문과 대답**](active-directory-passwords-faq.md) - 어떤 방식으로? 그 이유는 무엇을? 어디서? 누가? 언제? - 많은 분들이 항상 묻는 질문에 대한 답변입니다.
* [**문제 해결**](active-directory-passwords-troubleshoot.md) - SSPR의 일반적인 문제 해결 방법 알아보기

