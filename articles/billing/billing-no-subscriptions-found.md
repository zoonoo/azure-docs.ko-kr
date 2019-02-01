---
title: 구독을 찾을 수 없음 오류 - Azure Portal 로그인 | Microsoft Docs
description: Azure Portal 또는 Azure 계정 센터에 로그인할 때 구독을 찾을 수 없음 오류가 발생하는 문제에 대한 해결 방법을 제공합니다.
services: ''
documentationcenter: ''
author: genlin
manager: adpick
editor: ''
tags: billing
ms.assetid: d1545298-99db-4941-8e97-f24a06bb7cb6
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 05/11/2018
ms.author: banders
ms.custom: seodec18
ms.openlocfilehash: c6a2f14900d3a0d6f9e16e9b0c6d0bdfff97d6b5
ms.sourcegitcommit: 644de9305293600faf9c7dad951bfeee334f0ba3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/25/2019
ms.locfileid: "54903839"
---
# <a name="no-subscriptions-found-sign-in-error-for-azure-portal-or-azure-account-center"></a>Azure Portal 또는 Azure 계정 센터에 대한 구독을 찾을 수 없음 로그인 오류

[Azure Portal](https://portal.azure.com/) 또는 [Azure 계정 센터](https://account.windowsazure.com/Subscriptions)에 로그인을 시도할 때 “구독을 찾을 수 없음” 오류 메시지가 표시될 수 있습니다. 본 문서는 이 문제에 대한 해결 방법을 제공합니다.

## <a name="symptom"></a>증상

[Azure Portal](https://portal.azure.com/) 또는 [Azure 계정 센터](https://account.windowsazure.com/Subscriptions)에 로그인을 시도할 때 다음의 오류 메시지가 표시됩니다. "구독을 찾을 수 없음"

## <a name="cause"></a>원인

잘못된 디렉터리에서 선택했거나 사용자 계정에 충분한 권한이 없는 경우 이 문제가 발생합니다. 

## <a name="solution"></a>해결 방법

### <a name="scenario-1-error-message-is-received-in-the-azure-portalhttpsportalazurecom"></a>시나리오 1: [Azure Portal](https://portal.azure.com)에서 오류 메시지가 표시됨

이 문제를 해결하려면

* 오른쪽 위에 있는 계정을 클릭하여 올바른 Azure 디렉터리를 선택했는지 확인합니다.

  ![Azure Portal 오른쪽 위에 있는 디렉터리를 선택합니다.](./media/billing-no-subscriptions-found/directory-switch.png)
* 올바른 Azure 디렉터리를 선택했으나 여전히 오류 메시지가 표시되면 [계정에 소유자 역할을 할당](../role-based-access-control/role-assignments-portal.md)합니다.

### <a name="scenario-2-error-message-is-received-in-the-azure-account-centerhttpsaccountwindowsazurecomsubscriptions"></a>시나리오 2: [Azure 계정 센터](https://account.windowsazure.com/Subscriptions)에서 오류 메시지가 표시됨

사용한 계정이 계정 관리자인지 확인합니다. 계정 관리자가 누구인지 확인하려면 다음 단계를 따르세요.

1. [Azure Portal의 구독 보기](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)에 로그인합니다.
1. 확인하려는 구독을 선택한 다음 **설정**에서 확인합니다.
1. **속성**을 선택합니다. 구독의 계정 관리자는 **계정 관리자** 상자에 표시됩니다.  

## <a name="need-help-contact-us"></a>도움 필요 시 문의하세요.

질문이 있거나 도움이 필요한 경우 [지원 요청을 만드세요](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).
