---
title: 사용자가 Azure AD 갤러리 애플리케이션에 프로비전을 구성하는 동안 관리자 자격 증명을 저장하는 문제 | Microsoft Docs
description: Azure AD 애플리케이션 갤러리에 이미 나열된 애플리케이션에 대한 사용자 프로비전을 구성할 때 발생하는 일반적인 문제를 해결하는 방법
services: active-directory
documentationcenter: ''
author: barbkess
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/21/2018
ms.author: barbkess
ms.reviewer: asmalser
ms.openlocfilehash: 7cf7fb676681099aae92f7ea468eacd6976296d1
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55163741"
---
# <a name="problem-saving-administrator-credentials-while-configuring-user-provisioning-to-an-azure-active-directory-gallery-application"></a>사용자가 Azure Active Directory 갤러리 애플리케이션에 프로비전을 구성하는 동안 관리자 자격 증명을 저장하는 문제 

Azure Portal을 사용하여 엔터프라이즈 애플리케이션에 [자동 사용자 프로비전](user-provisioning.md)을 구성하는 경우 다음과 같은 상황이 발생할 수 있습니다.

* 애플리케이션에 입력한 **관리자 자격 증명**은 유효하고 **테스트 연결** 단추가 작동합니다. 그러나 자격 증명을 저장할 수 없고 Azure Portal이 일반 오류 메시지를 반환합니다.

또한 동일한 애플리케이션에 SAML 기반 Single Sign-On을 구성하는 경우 인증서 및 자격 증명에 대한 Azure AD의 내부 애플리케이션 당 스토리지 용량 한도를 초과했기 때문에 오류가 발생할 가능성이 높습니다.

현재 Azure AD는 애플리케이션의 단일 인스턴스와 연결된 모든 인증서, 비밀 토큰, 자격 증명 및 관련 구성 데이터(Azure AD의 서비스 주체 레코드라도고 함)에 대해 1024바이트의 최대 저장 용량을 제공합니다.

SAML 기반 Single Sign-On을 구성할 때 SAML 토큰에 서명하는 데 사용되는 인증서는 여기에 저장되고 종종 50%를 초과하는 공간을 사용합니다.

사용자 프로비전을 설정하는 동안 입력한 암호 토큰, URI, 알림 이메일 주소, 사용자 이름 및 암호로 인해 저장소 용량 한도를 초과할 수 있습니다.

## <a name="how-to-work-around-this-issue"></a>이 문제를 해결하는 방법 

이 문제를 해결하는 두 가지 방법이 있습니다.

1. **Single Sign-On 및 사용자 프로비전에 하나씩 두 개의 갤러리 애플리케이션 인스턴스를 사용합니다.** - 예를 들어, 애플리케이션 갤러리 [LinkedIn Elevate](../saas-apps/linkedinelevate-tutorial.md)를 사용하여 갤러리에서 LinkedIn Elevate를 추가하고 Single Sign-On에 구성할 수 있습니다. 프로비전을 위해 Azure AD 앱 갤러리에서 LinkedIn Elevate의 다른 인스턴스를 추가하고 이름을 "LinkedIn Elevate(프로비전)"으로 지정합니다. 두 번째 인스턴스의 경우 Single Sign-On이 아닌 [프로비전](../saas-apps/linkedinelevate-provisioning-tutorial.md)을 구성합니다. 이 해결 방법을 사용하는 경우 동일한 사용자 및 그룹을 두 애플리케이션에 [할당](assign-user-or-group-access-portal.md)해야 합니다. 

2. **저장된 구성 데이터의 양을 감소시킵니다.** - 프로비전 탭의 [관리자 자격 증명](user-provisioning.md#how-do-i-set-up-automatic-provisioning-to-an-application) 섹션에서 입력한 모든 데이터는 SAML 인증서와 동일한 위치에 저장됩니다. 이 모든 데이터의 길이를 줄이지 못할 수 있지만 **알림 이메일**과 같은 일부 선택적 구성 필드를 제거할 수 있습니다.

## <a name="next-steps"></a>다음 단계
[SaaS 애플리케이션에 대한 사용자 프로비전 및 프로비전 해제 구성](user-provisioning.md)
