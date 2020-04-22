---
title: Azure 포털을 사용하여 Exchange 피어링 만들기 또는 수정
titleSuffix: Azure
description: Azure 포털을 사용하여 Exchange 피어링 만들기 또는 수정
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: e4f2ee72cbe17c094567aab5c7cc4720b02cde68
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81680930"
---
# <a name="create-or-modify-an-exchange-peering-by-using-the-azure-portal"></a>Azure 포털을 사용하여 Exchange 피어링 만들기 또는 수정

이 문서에서는 Azure 포털을 사용하여 Microsoft Exchange 피어링을 만드는 방법에 대해 설명합니다. 이 문서에서는 리소스의 상태를 확인하거나, 업데이트하거나, 삭제하고 프로비저닝해제하는 방법도 보여 주어 있습니다.

원하는 경우 [PowerShell](howto-exchange-powershell.md)을 사용하여 이 가이드를 완료할 수 있습니다.

## <a name="before-you-begin"></a>시작하기 전에
* 구성을 시작하기 전에 [필수 구성 조건](prerequisites.md) 및 Exchange [피어링 연습을](walkthrough-exchange-all.md) 검토합니다.
* Azure 리소스로 변환되지 않은 Microsoft와의 Exchange 피어링이 이미 있는 경우 [포털을 사용하여 레거시 Exchange 피어링을 Azure 리소스로 변환참조를](howto-legacy-exchange-portal.md)참조하세요.

## <a name="create-and-provision-an-exchange-peering"></a>Exchange 피어링 만들기 및 프로비전

### <a name="sign-in-to-the-portal-and-select-your-subscription"></a>포털에 로그인하고 구독을 선택합니다.
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="create-an-exchange-peering"></a><a name=create></a>Exchange 피어링 만들기

피어링 리소스를 사용하여 새 **피어링** 요청을 만들 수 있습니다.

#### <a name="launch-the-resource-and-configure-basic-settings"></a>리소스를 실행하고 기본 설정을 구성합니다.
[!INCLUDE [direct-peering-basic](./includes/direct-portal-basic.md)]

#### <a name="configure-connections-and-submit"></a>연결 구성 및 제출
[!INCLUDE [exchange-peering-configuration](./includes/exchange-portal-configuration.md)]

### <a name="verify-an-exchange-peering"></a><a name=get></a>Exchange 피어링 확인
[!INCLUDE [peering-exchange-get-portal](./includes/exchange-portal-get.md)]

## <a name="modify-an-exchange-peering"></a><a name="modify"></a>Exchange 피어링 수정
[!INCLUDE [peering-exchange-modify-portal](./includes/exchange-portal-modify.md)]

## <a name="deprovision-an-exchange-peering"></a><a name="delete"></a>Exchange 피어링 프로비저닝 해제
[!INCLUDE [peering-exchange-delete-portal](./includes/delete.md)]

## <a name="next-steps"></a>다음 단계

* [포털을 사용하여 직접 피어링 만들기 또는 수정](howto-direct-portal.md)
* [포털을 사용하여 레거시 직접 피어링을 Azure 리소스로 변환](howto-legacy-direct-portal.md)

## <a name="additional-resources"></a>추가 리소스

자세한 내용은 [인터넷 피어링 FAQ를](faqs.md)참조하십시오.
