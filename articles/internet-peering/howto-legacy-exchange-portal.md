---
title: Azure Portal를 사용 하 여 레거시 Exchange 피어 링을 Azure 리소스로 변환
titleSuffix: Azure
description: Azure Portal를 사용 하 여 레거시 Exchange 피어 링을 Azure 리소스로 변환
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 87a7a6bca608f1748d3b659eabdc3e941b537377
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81678582"
---
# <a name="convert-a-legacy-exchange-peering-to-an-azure-resource-by-using-the-azure-portal"></a>Azure Portal를 사용 하 여 레거시 Exchange 피어 링을 Azure 리소스로 변환

이 문서에서는 Azure Portal를 사용 하 여 기존 레거시 Exchange 피어 링을 Azure 리소스로 변환 하는 방법을 설명 합니다.

원하는 경우 [PowerShell](howto-legacy-exchange-powershell.md)을 사용 하 여이 가이드를 완료할 수 있습니다.

## <a name="before-you-begin"></a>시작하기 전에
* 구성을 시작 하기 전에 [필수 구성 요소](prerequisites.md) 및 [Exchange 피어 링 연습](walkthrough-exchange-all.md) 을 검토 하십시오.

## <a name="convert-a-legacy-exchange-peering-to-an-azure-resource"></a>레거시 Exchange 피어 링을 Azure 리소스로 변환

### <a name="sign-in-to-the-portal-and-select-your-subscription"></a>포털에 로그인 하 고 구독을 선택 합니다.
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="convert-legacy-exchange-peering"></a><a name=create></a>레거시 교환 피어 링 변환

**피어 링** 리소스를 사용 하 여 레거시 피어 링 연결을 변환할 수 있습니다.

#### <a name="launch-the-resource-and-configure-basic-settings"></a>리소스를 시작 하 고 기본 설정을 구성 합니다.
[!INCLUDE [direct-peering-basic](./includes/direct-portal-basic.md)]

#### <a name="configure-connections-and-submit"></a>연결 구성 및 제출
[!INCLUDE [exchange-peering-configuration](./includes/exchange-portal-configuration-legacy.md)]

### <a name="verify-exchange-peering"></a><a name=get></a>Exchange 피어 링 확인
[!INCLUDE [peering-exchange-get-portal](./includes/exchange-portal-get.md)]

## <a name="additional-resources"></a>추가 리소스

자세한 내용은 [인터넷 피어 링 faq](faqs.md)를 참조 하십시오.

## <a name="next-steps"></a>다음 단계

* [포털을 사용 하 여 Exchange 피어 링 만들기 또는 수정](howto-exchange-portal.md)
