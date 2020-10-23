---
title: Azure Active Directory를 사용한 SSH 인증
description: Azure Active Directory와의 SSH 통합 구현에 대 한 아키텍처 지침
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/19/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: eb766150339820f9356fe94311cd1ff33dda5480
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/23/2020
ms.locfileid: "92462915"
---
# <a name="ssh"></a>SSH  

SSH (Secure Shell)는 보안 되지 않은 네트워크를 통해 안전 하 게 운영 네트워크 서비스에 대 한 암호화를 제공 하는 네트워크 프로토콜입니다. 또한 SSH는 명령줄 로그인을 제공 하 고, 원격 명령을 실행 하 고, 파일을 안전 하 게 전송 합니다. Linux®와 같은 UNIX 기반 시스템에서 일반적으로 사용 됩니다. SSH는 보안 되지 않은 네트워크에서 암호화를 제공 하지 않는 텔넷 프로토콜을 대체 합니다. 

Azure AD (Azure Active Directory)는 Azure에서 실행 되는 Linux® 기반 시스템용 VM (가상 컴퓨터) 확장을 제공 합니다. 

## <a name="use-when"></a>적용 가능한 상황 

* 원격 로그인이 필요한 Linux® 기반 Vm 작업

* Linux® 기반 시스템에서 원격 명령 실행

* 안전 하지 않은 네트워크에서 파일을 안전 하 게 전송

![SSH 프로토콜을 사용 하는 Azure AD 다이어그램](./media/authentication-patterns/ssh-auth.png)

Azure AD를 사용 하는 SSH

## <a name="components-of-system"></a>시스템의 구성 요소 

* **사용자**: Linux® vm과의 연결을 설정 하 고 인증을 위한 자격 증명을 제공 하기 위해 SSH 클라이언트를 시작 합니다.

* **웹 브라우저**: 사용자가 상호 작용 하는 구성 요소입니다. 사용자를 안전 하 게 인증 하 고 권한을 부여 하기 위해 Id 공급자 (Azure AD)와 통신 합니다.

* **SSH 클라이언트**: 연결 설정 프로세스를 구동 합니다.

* **AZURE AD**: 장치 흐름을 사용 하 여 사용자의 id를 인증 하 고 Linux vm에 토큰을 발급 합니다.

* **LINUX VM**: 토큰을 수락 하 고 성공적인 연결을 제공 합니다.

## <a name="implement-ssh-with-azure-ad"></a>Azure AD를 사용 하 여 SSH 구현 

* [Azure Active Directory 자격 증명을 사용 하 여 Linux® VM에 로그인-Azure Virtual Machines ](https://docs.microsoft.com/azure/virtual-machines/linux/login-using-aad) 

* [OAuth 2.0 장치 코드 흐름-Microsoft identity platform ](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-device-code)

* [Azure Active Directory와 통합 (akamai.com)](https://learn.akamai.com/webhelp/enterprise-application-access/enterprise-application-access/GUID-6B16172C-86CC-48E8-B30D-8E678BF3325F.html)

 
