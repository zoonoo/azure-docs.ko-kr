---
title: 인터넷을 통해 LDAPS를 사용하여 관리되는 도메인에 액세스하도록 DNS 구성 | Microsoft Docs
description: 인터넷을 통해 LDAPS를 사용하여 Azure AD Domain Services 관리되는 도메인에 액세스하도록 DNS 구성
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: mtillman
editor: curtand
ms.assetid: a47f0f3e-2578-422a-a421-034f66de38f5
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/01/2018
ms.author: ergreenl
ms.openlocfilehash: f15e2e7d3a9374d29608651fff6b46f7d047c5f9
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/26/2018
ms.locfileid: "50158366"
---
# <a name="configure-dns-to-access-an-azure-ad-domain-services-managed-domain-using-secure-ldap-ldaps"></a>보안 LDAP(LDAPS)를 사용하여 Azure AD Domain Services 관리되는 도메인에 액세스하도록 DNS 구성

## <a name="before-you-begin"></a>시작하기 전에
[작업 3: Azure Portal을 사용하여 관리되는 도메인에 대해 보안 LDAP를 사용하도록 설정](active-directory-ds-admin-guide-configure-secure-ldap-enable-ldaps.md) 완료

## <a name="task-4-configure-dns-to-access-the-managed-domain-from-the-internet"></a>작업 4: 인터넷에서 관리되는 도메인에 액세스하도록 DNS 구성
> [!TIP]
> **선택적 태스크** - 인터넷을 통해 LDAPS를 사용하여 관리되는 도메인에 액세스하지 않으려면 이 구성 태스크를 건너뜁니다.
>
>

이 태스크를 시작하기 전에 [작업 3](active-directory-ds-admin-guide-configure-secure-ldap-enable-ldaps.md)에 나와 있는 단계를 완료합니다.

인터넷을 통한 보안 LDAP 액세스를 사용하도록 설정했으면 클라이언트 컴퓨터가 관리되는 도메인을 찾을 수 있도록 DNS를 업데이트해야 합니다. 외부 IP 주소가 **LDAPS 액세스를 위한 외부 IP 주소**의 **속성** 탭에 표시됩니다.

관리되는 도메인의 DNS 이름(예: 'ldaps.contoso100.com')이 이 외부 IP 주소를 가리키도록 외부 DNS 공급자를 구성합니다. 예를 들어 다음과 같은 DNS 항목을 만듭니다.

    ldaps.contoso100.com  -> 52.165.38.113

이것으로 끝입니다. 이제 인터넷을 통해 보안 LDAP를 사용하여 관리되는 도메인에 연결할 준비가 되었습니다.

> [!WARNING]
> 클라이언트 컴퓨터가 LDAPS를 사용하여 관리되는 도메인에 성공적으로 연결하려면 LDAPS 인증서의 발급자를 신뢰해야 한다는 점에 유의하세요. 공개적으로 신뢰할 수 있는 인증 기관을 사용하는 경우 클라이언트 컴퓨터가 이러한 인증서 발급자를 신뢰하므로 문제가 되지 않습니다. 자체 서명된 인증서를 사용하는 경우 자체 서명된 인증서의 공개 부분을 클라이언트 컴퓨터의 신뢰할 수 있는 인증서 저장소에 설치합니다.
>
>

## <a name="next-step"></a>다음 단계
[작업 5: 관리되는 도메인에 바인딩 및 보안 LDAP 액세스 잠금](active-directory-ds-ldaps-bind-lockdown.md)
