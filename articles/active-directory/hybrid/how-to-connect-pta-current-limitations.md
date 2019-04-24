---
title: 'Azure AD Connect: 통과 인증 - 현재 제한 사항 | Microsoft Docs'
description: 이 문서에서는 Azure AD(Azure Active Directory) 통과 인증 문제의 현재 제한 사항에 대해 설명합니다.
services: active-directory
keywords: Azure AD Connect 통과 인증, Active Directory 설치, Azure AD에 대한 필수 구성 요소, SSO, Single Sign-on
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/04/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 97dc67d46b08bf5765c59806b45edd82f38720cd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60347749"
---
# <a name="azure-active-directory-pass-through-authentication-current-limitations"></a>Azure Active Directory 통과 인증: 현재 제한 사항

>[!IMPORTANT]
>Azure AD(Azure Active Directory) 통과 인증은 무료 기능이며, 이 기능을 사용하기 위해 Azure AD 유료 버전이 필요하지 않습니다. 통과 인증은 Azure AD의 전 세계 인스턴스에서만 사용할 수 있고, [Microsoft Azure 독일 클라우드](https://www.microsoft.de/cloud-deutschland) 또는 [Microsoft Azure Government 클라우드](https://azure.microsoft.com/features/gov/)에서는 사용할 수 없습니다.

## <a name="supported-scenarios"></a>지원되는 시나리오

다음 시나리오가 지원됩니다.

- 사용자가 웹 브라우저 기반 애플리케이션에 로그인합니다.
- 사용자가 로그인 Exchange ActiveSync, EAS, SMTP, POP 및 IMAP 등의 레거시 프로토콜을 사용하여 Outlook 클라이언트에 로그인합니다.
- 사용자가 [최신 인증](https://www.microsoft.com/en-us/microsoft-365/blog/2015/11/19/updated-office-365-modern-authentication-public-preview): Office 2013 및 2016 버전입니다.
- 사용자가 PowerShell 버전 1.0 등과 같은 레거시 프로토콜 애플리케이션에 로그인합니다.
- Azure AD가 Windows 10 디바이스에 조인합니다.
- Multi-Factor Authentication에 사용할 앱 암호.

## <a name="unsupported-scenarios"></a>지원되지 않는 시나리오

다음 시나리오는 지원되지 _않습니다_.

- [자격 증명이 손실된](../reports-monitoring/concept-risk-events.md#leaked-credentials) 사용자 검색
- Azure AD Domain Services를 사용하려면 테넌트에서 암호 해시 동기화를 사용하도록 설정해야 합니다. 따라서 통과 인증_만_ 사용하는 테넌트는 Azure AD Domain Services가 필요한 시나리오에서 사용할 수 없습니다.
- 통과 인증은 [Azure AD Connect Health](whatis-hybrid-identity-health.md)와 통합되지 않습니다.

> [!IMPORTANT]
> (Azure AD Connect Health 통합을 제외한) 지원되지 않는 시나리오에_만_ 해당하는 해결 방법으로, Azure AD Connect 마법사의 [선택적 기능](how-to-connect-install-custom.md#optional-features) 페이지에서 암호 해시 동기화를 사용하도록 설정합니다.
> 
> [!NOTE]
> 암호 해시 동기화를 사용하면 온-프레미스 인프라가 손상된 경우 인증을 장애 조치(Failover)할 수 있는 옵션이 제공됩니다. 통과 인증에서 암호 해시 동기화로 장애 조치(failover)는 자동으로 수행되지 않습니다. Azure AD Connect를 사용하여 수동으로 로그인 방법을 전환해야 합니다. Azure AD Connect를 실행하는 서버가 다운되면 Microsoft 지원 서비스의 도움을 받아 통과 인증을 해제해야 합니다.

## <a name="next-steps"></a>다음 단계
- [빠른 시작](how-to-connect-pta-quick-start.md): Azure AD 통과 인증을 준비하고 실행합니다.
- [AD FS에서 통과 인증으로 마이그레이션](https://aka.ms/ADFSTOPTADPDownload) - AD FS(또는 기타 페더레이션 기술)에서 통과 인증으로 마이그레이션하는 방법에 대한 자세한 가이드입니다.
- [스마트 잠금](../authentication/howto-password-smart-lockout.md): 테넌트에서 스마트 잠금 기능을 구성하여 사용자 계정을 보호하는 방법을 알아봅니다.
- [기술 심층 분석](how-to-connect-pta-how-it-works.md): 통과 인증 기능이 작동하는 원리를 이해합니다.
- [질문과 대답](how-to-connect-pta-faq.md): 통과 인증 기능에 대한 질문과 대답을 살펴봅니다.
- [문제 해결](tshoot-connect-pass-through-authentication.md): 통과 인증 기능의 일반적인 문제를 해결하는 방법을 알아봅니다.
- [보안 심층 분석](how-to-connect-pta-security-deep-dive.md): 통과 인증 기능에 대한 심층 기술 정보를 가져옵니다.
- [Azure AD Seamless SSO](how-to-connect-sso.md): 이 보완 기능을 자세히 알아봅니다.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): Azure Active Directory 포럼을 사용하여 새 기능 요청을 제출합니다.
