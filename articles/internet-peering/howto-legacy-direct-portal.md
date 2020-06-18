---
title: Azure Portal을 사용하여 레거시 직접 피어링을 Azure 리소스로 변환
titleSuffix: Azure
description: Azure Portal을 사용하여 레거시 직접 피어링을 Azure 리소스로 변환
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 159e15a6be59991fba574e72dcaf2ed740fb10c2
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/26/2020
ms.locfileid: "83846170"
---
# <a name="convert-a-legacy-direct-peering-to-an-azure-resource-by-using-the-azure-portal"></a>Azure Portal을 사용하여 레거시 직접 피어링을 Azure 리소스로 변환

이 문서에서는 Azure Portal을 사용하여 기존 레거시 직접 피어 링을 Azure 리소스로 변환하는 방법을 설명합니다.

원하는 경우 [PowerShell](howto-legacy-direct-powershell.md)을 사용하여 이 가이드를 완료할 수 있습니다.

## <a name="before-you-begin"></a>시작하기 전에
* 구성을 시작하기 전에 [필수 구성 요소](prerequisites.md) 및 [직접 피어링 연습](walkthrough-direct-all.md)을 검토합니다.


## <a name="convert-a-legacy-direct-peering-to-an-azure-resource"></a>레거시 직접 피어링을 Azure 리소스로 변환

### <a name="sign-in-to-the-portal-and-select-your-subscription"></a>포털에 로그인하고 구독 선택
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="convert-a-legacy-direct-peering"></a><a name=create></a>레거시 직접 피어링 변환

인터넷 서비스 공급자는 [피어링 만들기]( https://go.microsoft.com/fwlink/?linkid=2129593)를 사용하여 레거시 직접 피어링 연결을 변환할 수 있습니다.

1. **피어링 만들기** 페이지의 **기본 사항** 탭에서 다음과 같이 입력란을 채웁니다.

    > [!div class="mx-imgBorder"] 
    > ![Peering Service 등록](./media/setup-basics-tab.png)

*    Azure 구독을 선택합니다.

* 리소스 그룹의 경우 드롭다운 목록에서 기존 리소스 그룹을 선택하거나 새로 만들기를 선택하여 새 그룹을 만들 수 있습니다. 이 예제에서는 새 리소스 그룹을 만듭니다.

* 이름은 리소스 이름에 해당하며, 원하는 어떤 이름도 사용 가능합니다.

* 기존 리소스 그룹을 선택한 경우 지역이 자동으로 선택됩니다. 새 리소스 그룹을 만들도록 선택한 경우에는 리소스를 저장할 Azure 지역도 선택해야 합니다.

>[!NOTE]
>리소스 그룹이 있는 지역은 Microsoft와 피어링을 만들려는 위치와 별개입니다. 하지만 가장 가까운 Azure 지역에 있는 리소스 그룹 내에 피어링 리소스를 구성하는 것이 좋습니다. 예를 들어, 애슈번에서 피어링이 필요한 경우 미국 동부 또는 미국 동부 2에 리소스 그룹을 만들 수 있습니다.

* **PeerASN** 상자에서 ASN을 선택합니다.

>[!IMPORTANT] 
>피어링 요청을 제출하기 전에 ValidationState가 승인된 ASN만 선택할 수 있습니다. 방금 PeerAsn 요청을 제출한 경우 ASN 연결이 승인될 때까지 12시간 정도 기다립니다. 선택한 ASN의 유효성 검사가 보류 중인 경우 오류 메시지가 표시됩니다. 선택해야 하는 ASN이 표시되지 않는 경우 올바른 구독을 선택했는지 확인합니다. 그렇다면 **[피어 ASN을 Azure 구독에 연결](https://go.microsoft.com/fwlink/?linkid=2129592)** 을 사용하여 PeerAsn을 이미 만들었는지 확인합니다.

#### <a name="launch-the-resource-and-configure-basic-settings"></a>리소스 시작 및 기본 설정 구성
[!INCLUDE [direct-peering-basic](./includes/direct-portal-basic.md)]

#### <a name="configure-connections-and-submit"></a>연결 구성 및 제출
[!INCLUDE [direct-peering-configuration](./includes/direct-portal-configuration-legacy.md)]

### <a name="verify-direct-peering"></a><a name=get></a>직접 피어링 확인
[!INCLUDE [peering-direct-get-portal](./includes/direct-portal-get.md)]

## <a name="additional-resources"></a>추가 리소스

자세한 내용은 [인터넷 피어링 FAQ](faqs.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

* [포털을 사용하여 직접 피어링 만들기 또는 수정](howto-direct-portal.md)
