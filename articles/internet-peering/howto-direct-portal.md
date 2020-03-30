---
title: 포털을 사용하여 직접 피어링 만들기 또는 수정
titleSuffix: Azure
description: 포털을 사용하여 직접 피어링 만들기 또는 수정
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 67a4944082d3ebc14fd564eedee0310afe6e3ff5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75775330"
---
# <a name="create-or-modify-a-direct-peering-using-the-portal"></a>포털을 사용하여 직접 피어링 만들기 또는 수정

이 문서에서는 포털을 사용하여 Microsoft Direct 피어링을 만드는 방법에 대해 설명합니다. 이 문서에서는 리소스의 상태를 확인하거나, 업데이트하거나, 삭제하고 프로비저닝해제하는 방법도 보여 주어 있습니다.

원하는 경우 [PowerShell](howto-direct-powershell.md)을 사용하여 이 가이드를 완료할 수 있습니다.

## <a name="before-you-begin"></a>시작하기 전에
* 구성을 시작하기 전에 [필수 구성 조건](prerequisites.md) 및 직접 [피어링 연습을](walkthrough-direct-all.md) 검토합니다.
* Azure 리소스로 변환되지 않은 Microsoft를 이미 직접 피어링하는 경우 [포털을 사용하여 레거시 직접 피어링을 Azure 리소스로 변환하는](howto-legacy-direct-portal.md) 것을 참조하십시오.

## <a name="create-and-provision-a-direct-peering"></a>직접 피어링 만들기 및 프로비전

### <a name="sign-in-to-portal-and-select-your-subscription"></a>포털에 로그인하고 구독을 선택합니다.
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="create-a-direct-peering"></a><a name=create></a>직접 피어링 만들기

피어링 리소스를 사용하여 새 **피어링** 요청을 만들 수 있습니다.

#### <a name="launch-resource-and-configure-basic-settings"></a>리소스 시작 및 기본 설정 구성
[!INCLUDE [direct-peering-basic](./includes/direct-portal-basic.md)]

#### <a name="configure-connections-and-submit"></a>연결 구성 및 제출
[!INCLUDE [direct-peering-configuration](./includes/direct-portal-configuration.md)]

### <a name="verify-direct-peering"></a><a name=get></a>직접 피어링 확인
[!INCLUDE [peering-direct-get-portal](./includes/direct-portal-get.md)]

## <a name="modify-a-direct-peering"></a><a name="modify"></a>직접 피어링 수정
[!INCLUDE [peering-direct-modify-portal](./includes/direct-portal-modify.md)]

## <a name="deprovision-a-direct-peering"></a><a name="delete"></a>직접 피어링 프로비저닝 해제
[!INCLUDE [peering-direct-delete-portal](./includes/delete.md)]

## <a name="next-steps"></a>다음 단계

* [포털을 사용하여 Exchange 피어링을 만들거나 수정합니다.](howto-exchange-portal.md)
* [포털을 사용하여 레거시 Exchange 피어링을 Azure 리소스로 변환합니다.](howto-legacy-exchange-portal.md)

## <a name="additional-resources"></a>추가 리소스

자세한 내용은 [인터넷 피어링 자주 묻는 질문(FAQ)을 방문하십시오.](faqs.md)
