---
title: "Azure 구독에 로그인할 수 없음 | Microsoft Docs"
description: "몇 가지 일반적인 Azure 구독 로그인 문제를 해결하는 방법에 대해 설명합니다."
services: 
documentationcenter: 
author: genlin
manager: jlian
editor: 
tags: billing
ms.assetid: d1545298-99db-4941-8e97-f24a06bb7cb6
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/2/2016
ms.author: genli
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 030b41eaa405ff70081a4c15cabded08a1b0b07a


---
# <a name="i-cant-sign-in-to-manage-my-azure-subscription"></a>내 Azure 구독 관리를 위해 로그인할 수 없습니다.
이 문서는 로그인 문제를 해결하기 위한 가장 일반적인 방법 중 일부를 안내합니다.

## <a name="page-hangs-in-the-loading-status"></a>페이지가 로딩 상태에서 중지됨
인터넷 브라우저 페이지가 응답하지 않는 경우 [Azure Portal](https://portal.azure.com)에 액세스할 수 있을 때까지 다음 단계를 시도해 보세요.

* 페이지를 새로 고칩니다.
* 다른 인터넷 브라우저를 사용합니다.
* Microsoft Internet Explorer를 사용하는 경우 InPrivate 브라우징 모드를 사용하여 Azure Portal로 이동합니다. 
  
  A.    **도구** ![도구 단추](./media/billing-cannot-login-subscription/Toolsbutton.png) > **안전** > **InPrivate 브라우징**을 클릭합니다.
  
  B.    [Azure Portal](https://portal.azure.com)로 이동한 후 포털에 로그인합니다.

## <a name="error-message-no-subscriptions-found"></a>"구독을 찾을 수 없음" 오류 메시지
계정에 충분한 사용 권한이 없으면 **구독을 찾을 수 없습니다**라는 오류 메시지가 표시될 수 있습니다. 올바른 관리자 권한으로 로그인해야 합니다. 계정 관리자만 [계정 센터](https://account.windowsazure.com/Subscriptions)에 액세스할 수 있습니다. SA(서비스 관리자) 및 CA(공동 관리자)는 [Azure Portal](https://portal.azure.com) 또는 Azure 클래식 포털에 대한 액세스 권한이 있습니다.

**시나리오 1: [Azure Portal](https://portal.azure.com)에서 오류 메시지를 받음**

이 문제를 해결하려면 계정에 대해 [공동 관리자 또는 소유자 역할을 추가](billing-add-change-azure-subscription-administrator.md)합니다.

**시나리오 2: [Azure 계정 센터](https://account.windowsazure.com/Subscriptions)에서 오류 메시지를 받음**

사용한 계정이 계정 관리자인지 확인합니다. 계정 관리자가 누구인지 확인하려면 다음 단계를 따르세요.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 허브 메뉴에서 **구독**을 선택합니다.
3. 확인하려는 구독을 선택한 다음 **설정**을 선택합니다.
4. **속성**을 선택합니다. 구독의 계정 관리자는 **계정 관리자** 상자에 표시됩니다.

## <a name="you-are-automatically-signed-in-as-a-different-user"></a>다른 사용자로 자동으로 로그인됨
인터넷 브라우저에서 둘 이상의 사용자 계정을 사용 중인 경우 이 문제가 발생할 수 있습니다.

이 문제를 해결하려면 다음 방법 중 하나를 사용해 보세요.

* 캐시의 선택을 취소하고 인터넷 쿠키를 삭제합니다. Internet Explorer에서 **도구** ![도구 단추](./media/billing-cannot-login-subscription/Toolsbutton.png) > **인터넷 옵션** > **삭제**를 클릭합니다. 임시 파일, 쿠키, 암호 및 검색 기록에 대한 확인란이 선택되었는지 확인한 후 삭제를 클릭합니다.
* Internet Explorer 설정을 재설정하여 수행한 모든 개인 설정을 되돌립니다. **도구** ![도구 단추](./media/billing-cannot-login-subscription/Toolsbutton.png)> **인터넷 옵션** > **고급** >을 클릭하고 **개인 설정 삭제** 상자 > **재설정**을 선택합니다.
* InPrivate 브라우징 모드에서 Azure Portal로 이동합니다. **도구** ![도구 단추](./media/billing-cannot-login-subscription/Toolsbutton.png) > **안전** > **InPrivate 브라우징**을 클릭합니다.

## <a name="need-help-contact-support"></a>도움이 필요하세요? 지원에 문의하세요.
다른 도움이 필요한 경우 [지원에 문의](http://go.microsoft.com/fwlink/?linkid=544831&clcid=0x409)하여 문제를 신속하게 해결하세요. 




<!--HONumber=Dec16_HO2-->


