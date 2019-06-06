---
title: Azure Active Directory B2C에서 리소스 및 데이터에 대한 위협 관리 | Microsoft Docs
description: Azure Active Directory B2C에서 서비스 거부 공격 및 암호 공격에 대한 검색 및 완화 기법을 알아봅니다.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: f961e873bb230e4e6d7e14b03e2664b7f987974e
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66508895"
---
# <a name="manage-threats-to-resources-and-data-in-azure-active-directory-b2c"></a>Azure Active Directory B2C에서 리소스 및 데이터에 대한 위협 관리

Azure AD(Azure Active Directory) B2C에는 리소스 및 데이터에 대한 위협으로부터 보호하는 데 도움이 될 수 있는 기본 제공 기능이 있습니다. 이러한 위협에는 서비스 거부 공격 및 암호 공격이 포함됩니다. 서비스 거부 공격은 의도한 사용자가 리소스를 사용할 수 없게 만듭니다. 암호 공격으로 인해 리소스에 대한 무단 액세스가 발생할 수 있습니다. 

## <a name="denial-of-service-attacks"></a>서비스 거부 공격

Azure AD B2C는 SYN 쿠키를 사용하여 SYN 서비스 장애 공격을 방어합니다. Azure AD B2C는 또한 속도 및 연결 제한을 사용하여 서비스 거부 공격으로부터 보호합니다.

## <a name="password-attacks"></a>암호 공격

사용자가 설정한 암호는 적절한 복잡성을 유지해야 합니다. Azure AD B2C에는 암호 공격에 대한 완화 기술이 포함됩니다. 완화에는 무차별 암호 대입 공격 및 사전 암호 공격이 포함됩니다. 다양한 신호를 사용하여 Azure AD B2C는 요청의 무결성을 분석합니다. Azure AD B2C는 의도한 사용자와 해커 및 봇네트 간을 지능적으로 구분하도록 설계되었습니다. 

Azure AD B2C는 좀 더 복잡한 계정 잠금 전략을 사용합니다. 계정은 요청의 IP 주소와 입력된 암호를 기반으로 잠깁니다. 또한 실패한 시도가 공격일 가능성에 따라 잠금 기간이 늘어납니다. 암호를 10번 잘못 시도하면 1분 동안 잠금이 발생합니다. 계정 잠금이 해제된 후 다음 로그인에 실패하면 다시 1분 동안 잠금이 발생하고 로그인이 실패할 때마다 계속 이렇게 됩니다. 반복적으로 같은 암호를 입력해도 여러 번의 로그인 실패로 계산되지 않습니다. 

처음 10회에 대한 잠금 기간은 1분입니다. 다음 10회 잠금 기간은 약간 더 길고 10회 잠금 기간마다 기간이 늘어납니다. 계정이 잠겨 있지 않은 상태에서 로그인에 성공하면 잠금 카운터가 0으로 다시 설정됩니다. 잠금 기간은 5시간까지 지속될 수 있습니다. 

현재는 다음을 수행할 수 없습니다.

- 로그인 실패가 10회 미만인 경우 잠금 트리거
- 잠긴 계정 목록 검색
- 잠금 정책 구성

자세한 내용은 [Microsoft 보안 센터](https://www.microsoft.com/trustcenter/default.aspx)를 참조하세요.
