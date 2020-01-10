---
title: 포털을 사용 하 여 레거시 직접 피어 링을 Azure 리소스로 변환
titleSuffix: Azure
description: 포털을 사용 하 여 레거시 직접 피어 링을 Azure 리소스로 변환
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: a40c7bbc9f37135814b7bba3396d368faf97a166
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75775200"
---
# <a name="convert-a-legacy-exchange-peering-to-azure-resource-using-the-portal"></a>포털을 사용 하 여 레거시 Exchange 피어 링을 Azure 리소스로 변환

이 문서에서는 포털을 사용 하 여 기존 레거시 Exchange 피어 링을 Azure 리소스로 변환 하는 방법을 설명 합니다.

원하는 경우 [PowerShell](howto-legacy-exchange-powershell.md)을 사용 하 여이 가이드를 완료할 수 있습니다.

## <a name="before-you-begin"></a>시작하기 전에
* 구성을 시작 하기 전에 [필수 구성 요소](prerequisites.md) 및 [Exchange 피어 링 연습](walkthrough-exchange-all.md) 을 검토 하십시오.

## <a name="convert-a-legacy-exchange-peering-to-azure-resource"></a>레거시 Exchange 피어 링을 Azure 리소스로 변환

### <a name="sign-in-to-portal-and-select-your-subscription"></a>포털에 로그인 하 고 구독을 선택 합니다.
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name=create></a>레거시 교환 피어 링 변환

**피어 링** 리소스를 사용 하 여 레거시 피어 링 연결을 변환할 수 있습니다.

#### <a name="launch-resource-and-configure-basic-settings"></a>리소스 시작 및 기본 설정 구성
[!INCLUDE [direct-peering-basic](./includes/direct-portal-basic.md)]

#### <a name="configure-connections-and-submit"></a>연결 구성 및 제출
[!INCLUDE [exchange-peering-configuration](./includes/exchange-portal-configuration-legacy.md)]

### <a name=get></a>Exchange 피어 링 확인
[!INCLUDE [peering-exchange-get-portal](./includes/exchange-portal-get.md)]

## <a name="additional-resources"></a>추가 리소스

자세한 내용은 [인터넷 피어 링 faq](faqs.md) (영문)를 참조 하세요.

## <a name="next-steps"></a>다음 단계

* [포털을 사용 하 여 Exchange 피어 링 만들기 또는 수정](howto-exchange-portal.md)
