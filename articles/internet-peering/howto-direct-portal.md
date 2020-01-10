---
title: 포털을 사용 하 여 직접 피어 링 만들기 또는 수정
titleSuffix: Azure
description: 포털을 사용 하 여 직접 피어 링 만들기 또는 수정
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 67a4944082d3ebc14fd564eedee0310afe6e3ff5
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75775330"
---
# <a name="create-or-modify-a-direct-peering-using-the-portal"></a>포털을 사용 하 여 직접 피어 링 만들기 또는 수정

이 문서에서는 포털을 사용 하 여 Microsoft 직접 피어 링을 만드는 방법을 설명 합니다. 또한이 문서에서는 리소스의 상태를 확인 하거나, 업데이트 하거나, 삭제 하 고 프로 비전을 해제 하는 방법을 보여 줍니다.

원하는 경우 [PowerShell](howto-direct-powershell.md)을 사용 하 여이 가이드를 완료할 수 있습니다.

## <a name="before-you-begin"></a>시작하기 전에
* 구성을 시작 하기 전에 [필수 조건](prerequisites.md) 및 [직접 피어 링 연습](walkthrough-direct-all.md) 을 검토 합니다.
* Azure 리소스로 변환 되지 않은 Microsoft와 직접 피어 링을 이미 사용 하는 경우 [포털을 사용 하 여 레거시 직접 피어 링을 azure 리소스로 변환](howto-legacy-direct-portal.md) 을 참조 하세요.

## <a name="create-and-provision-a-direct-peering"></a>직접 피어 링 만들기 및 프로 비전

### <a name="sign-in-to-portal-and-select-your-subscription"></a>포털에 로그인 하 고 구독을 선택 합니다.
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name=create></a>직접 피어 링 만들기

**피어 링** 리소스를 사용 하 여 새 피어 링 요청을 만들 수 있습니다.

#### <a name="launch-resource-and-configure-basic-settings"></a>리소스 시작 및 기본 설정 구성
[!INCLUDE [direct-peering-basic](./includes/direct-portal-basic.md)]

#### <a name="configure-connections-and-submit"></a>연결 구성 및 제출
[!INCLUDE [direct-peering-configuration](./includes/direct-portal-configuration.md)]

### <a name=get></a>직접 피어 링 확인
[!INCLUDE [peering-direct-get-portal](./includes/direct-portal-get.md)]

## <a name="modify"></a>직접 피어 링 수정
[!INCLUDE [peering-direct-modify-portal](./includes/direct-portal-modify.md)]

## <a name="delete"></a>직접 피어 링 프로 비전 해제
[!INCLUDE [peering-direct-delete-portal](./includes/delete.md)]

## <a name="next-steps"></a>다음 단계

* [포털을 사용 하 여 Exchange 피어 링을 만들거나 수정](howto-exchange-portal.md)합니다.
* [포털을 사용 하 여 레거시 Exchange 피어 링을 Azure 리소스로 변환](howto-legacy-exchange-portal.md)합니다.

## <a name="additional-resources"></a>추가 리소스

자세한 내용은 [인터넷 피어 링 faq](faqs.md) (영문)를 참조 하세요.
