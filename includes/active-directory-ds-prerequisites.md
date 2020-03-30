---
title: 포함 파일
description: 필수 조건이 있는 포함 파일
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 66b5f8e2-e08d-43c8-b460-6204aecda8f7
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.custom: include file
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2018
ms.author: maheshu
ms.openlocfilehash: f7b1b294e9500ef9e0aadd24cfe3cd4e61fddda1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "68426923"
---
> [!IMPORTANT]
> **이 문서의 작업을 완료하기 전에 Azure AD Domain Services에 대한 암호 해시 동기화를 사용하도록 설정합니다.**
>
> Azure AD 디렉터리의 사용자 유형에 따라 아래 지침을 따릅니다. Azure AD 디렉터리에 클라우드 전용 사용자 계정과 동기화된 사용자 계정이 혼합되어 있는 경우, 두 가지 지침을 모두 완료합니다. B2B Guest 계정(예: 당사가 허용하는 다른 ID 제공업체의 Gmail 또는 MSA)을 사용하려는 경우, 관리되는 도메인에 동기화된 사용자에 대한 암호가 없기 때문에 다음과 같은 작업을 수행하지 못할 수 있습니다. 디렉터리에서 게스트 계정을 연결합니다. 암호를 포함한 이러한 계정에 대한 전체 정보는 Azure AD 외부에 있으며 이 정보가 Azure AD에 없으므로 관리되는 도메인에 동기화되지 않습니다. 
> - [클라우드 전용 사용자 계정에 대한 지침](../articles/active-directory-domain-services/active-directory-ds-getting-started-password-sync.md)
> - [온-프레미스 디렉터리에서 동기화된 사용자 계정에 대한 지침](../articles/active-directory-domain-services/active-directory-ds-getting-started-password-sync-synced-tenant.md)
