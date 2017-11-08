---
title: "Azure AD 셀프 서비스 암호 재설정 개요 | Microsoft Docs"
description: "Azure AD 셀프 서비스 암호 재설정으로 조직에서 어떤 작업을 수행할 수 있나요?"
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
ms.date: 10/24/2017
ms.author: joflore
ms.custom: it-pro
ms.openlocfilehash: f577ee95c34f9bc3065c026bc943d30622b9d658
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/31/2017
---
# <a name="azure-ad-self-service-password-reset-for-the-it-professional"></a>IT 전문가를 위한 Azure AD 셀프 서비스 암호 재설정

Azure AD(Azure Active Directory) SSPR(셀프 서비스 암호 재설정)을 사용하면 사용자가 필요할 때 언제 어디서나 자신의 암호를 재설정할 수 있으며 관리자는 암호 재설정 방법을 제어할 수 있습니다. 암호를 재설정하기 위해 사용자가 기술 지원팀에 더 이상 전화할 필요가 없습니다.

* **셀프 서비스 암호 변경** – 사용자가 암호를 알고 있지만 새로운 암호로 변경하려고 합니다.
* **셀프 서비스 암호 재설정** – 사용자가 로그인할 수 없으며 다음 검증된 인증 방법 중 한 가지 이상을 사용하여 자신의 암호를 재설정하려고 합니다.
   * 검증된 휴대폰으로 문자 메시지 전송
   * 검증된 휴대폰 또는 사무실 전화로 전화
   * 검증된 보조 전자 메일 계정으로 메일 보내기
   * 보안 질문에 대한 답변
* **셀프 서비스 계정 잠금 해제** – 사용자가 자신의 잠긴 암호로 로그인할 수 없는 상태에서 인증 방법을 사용하여 관리자 개입 없이 계정 잠금을 해제하려고 합니다.

## <a name="why-choose-azure-ad-self-service-password-reset"></a>Azure AD 셀프 서비스 암호 재설정을 선택하는 이유

* **비용 절감** 기술 지원팀이 지원하는 암호 재설정은 일반적으로 IT 조직 지원 요청의 20%를 차지합니다. 
* **최종 사용자 환경 개선** 및 **기술 지원팀 볼륨 감소** 기술 지원팀에 연락하거나 지원을 요청하지 않고 한 번에 고유한 암호 문제를 해결하기 위해 최종 사용자에게 권한을 부여합니다.
* **이동성 공급** - 사용자가 어디에서든 암호를 재설정할 수 있습니다
* 보안 정책에 대한 **제어를 유지**합니다. 관리자가 현재 가지고 있는 정책을 지속적으로 시행할 수 있습니다.

준비를 마쳤다면 [빠른 시작 설명서](active-directory-passwords-getting-started.md)를 사용하여 Azure AD SSPR을 시작하고 사용자가 자신의 고유한 암호를 재설정할 수 있습니다.

## <a name="azure-ad-self-service-password-reset-availability"></a>Azure AD 셀프 서비스 암호 재설정 가용성

Azure AD 셀프 서비스 암호 재설정은 구독에 따라 세 가지 계층으로 제공됩니다.

* **Azure AD Free** - 클라우드 전용 관리자가 자신의 암호를 다시 설정할 수 있습니다.
* **Azure AD Basic** 또는 **유료 Office 365 구독** - 클라우드 전용 사용자는 자신의 암호를 재설정할 수 있습니다.
* **Azure AD Premium** - 사용자 또는 관리자는 클라우드 전용, 페더레이션된 또는 암호 동기화된 사용자를 포함하여 해당하는 고유한 암호를 재설정할 수 있습니다. 온-프레미스 암호는 비밀번호 쓰기 저장을 사용하도록 설정해야 합니다.

## <a name="azure-ad-pricing-sla-updates-and-roadmap"></a>Azure AD 가격 책정, SLA, 업데이트 및 로드맵

라이선스, 가격 책정 및 향후 계획에 대한 자세한 내용은 다음 사이트를 참조하세요.

* [**Azure AD 가격 책정 정보**](https://azure.microsoft.com/pricing/details/active-directory/)
* [**Office 365 가격 책정**](https://products.office.com/compare-all-microsoft-office-products?tab=2)
* [**Azure Service Level Agreements(서비스 수준 약정)**](https://azure.microsoft.com/support/legal/sla/)
* [**Microsoft 온라인 서비스에 대한 Service Level Agreements(서비스 수준 약정)**](http://go.microsoft.com/fwlink/?LinkID=272026&clcid=0x409)
* [**Azure 업데이트**](https://azure.microsoft.com/updates/)
* [**Azure 로드맵**](https://www.microsoft.com/cloud-platform/roadmap-recently-available)

## <a name="next-steps"></a>다음 단계

* SSPR을 시작할 준비가 되었나요? [Azure AD 셀프 서비스 암호 재설정 설정](active-directory-passwords-getting-started.md).
* [**출시 가이드**](active-directory-passwords-best-practices.md)의 지침을 사용하여 사용자에 대한 성공적인 SSPR 배포를 계획하세요.
* [암호 재설정 또는 변경](active-directory-passwords-update-your-own-password.md)
* [셀프 서비스 암호 재설정 등록](active-directory-passwords-reset-register.md)