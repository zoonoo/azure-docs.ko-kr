---
title: "Azure B2C의 위협 관리 | Microsoft Docs"
description: "Azure B2C의 DOS 공격/암호 공격 감지 및 완화 기법"
services: active-directory-b2c
documentationcenter: 
author: vigunase
manager: Ajith Alexander
editor: 
ms.assetid: 6df79878-65cb-4dfc-98bb-2b328055bc2e
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2016
ms.author: 
translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: 5cca5fcd445e70b4eab9197258bb783e20016f76
ms.lasthandoff: 03/31/2017


---
# <a name="azure-ad-b2c-threat-management"></a>Azure AD B2C: 위협 관리
위협 관리에는 시스템 및 네트워크에 대한 공격으로부터 보호하는 기능이 포함됩니다. DOS(서비스 거부)는 가용성에 영향을 줄 수 있으며 의도한 사용자가 리소스를 사용하지 못하게 할 수 있습니다. 암호 공격으로 인해 리소스에 대한 무단 액세스가 발생할 수 있습니다. Microsoft Azure Active Directory B2C에는 여러 가지 방법으로 이러한 위협으로부터 데이터를 보호하는 기본 제공 기능이 있습니다. 

## <a name="denial-of-service-attack"></a>서비스 거부 공격

Azure AD B2C는 SYN 쿠키, 속도 및 연결 제한과 같은 검색 및 완화 기술을 사용하여 이러한 공격으로부터 기본 리소스를 보호합니다.  

## <a name="password-attacks"></a>암호 공격

또한 Azure AD B2C에는 암호 공격을 위한 완화 방법도 제공되어 있습니다.  이 기술에는 무작위 암호 공격 및 사전 암호 공격이 모두 포함됩니다.  사용자가 설정하는 암호는 적절한 복잡성을 유지해야 합니다.  Azure AD B2C는 요청 무결성을 분석하여 다양한 신호를 통해 의도한 사용자와 해커 및 봇네트 간을 지능적으로 구분합니다. B2C는 공격 가능성이 있을 때 입력된 암호에 따라 계정을 잠그는 정교한 전략을 제공합니다.

[Microsoft의 위협 관리에 대한 자세한 내용](https://www.microsoft.com/trustcenter/security/threatmanagement)
