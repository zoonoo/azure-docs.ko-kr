---
title: Azure 자식 DNS 영역 만들기
titleSuffix: Azure DNS
description: Azure Portal에서 자식 DNS 영역을 만드는 방법에 대한 자습서입니다.
author: jonbeck
ms.assetid: be4580d7-aa1b-4b6b-89a3-0991c0cda897
ms.service: dns
ms.topic: tutorial
ms.custom: ''
ms.workload: infrastructure-services
ms.date: 7/16/2020
ms.author: jonbeck
ms.openlocfilehash: 3f35d39634470ccacffa4d35c272a82725e9001c
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89081373"
---
# <a name="tutorial-creating-a-new-child-dns-zone"></a>자습서: 새 자식 DNS 영역 만들기

이 자습서에서는 다음 작업 방법을 알아봅니다. 

> [!div class="checklist"]
> * Azure Portal에 로그인.
> * 새 DNS 영역을 통해 자식 DNS 영역 만들기.
> * 부모 DNS 영역을 통해 자식 DNS 영역 만들기.
> * 새 자식 DNS 영역에 대한 NS 위임 확인.



## <a name="prerequisites"></a>필수 구성 요소

* 활성 구독이 있는 Azure 계정.  계정이 없는 경우 [체험 계정을 만들 수 있습니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* 기존 부모 Azure DNS 영역.  

이 자습서에서는 contoso.com을 부모 영역으로 사용하고 subdomain.contoso.com을 자식 도메인 이름으로 사용합니다.  *contoso.com*을 부모 도메인 이름으로, *subdomain*을 자식 도메인으로 바꿉니다.  부모 DNS 영역을 만들지 않은 경우 [Azure Portal을 사용하여 DNS 영역 만들기](https://docs.microsoft.com/azure/dns/dns-getstarted-portal#create-a-dns-zone) 단계를 참조하세요. 


## <a name="sign-in-to-azure-portal"></a>Azure Portal에 로그인

Azure 계정을 사용하여 [Azure Portal](https://portal.azure.com/) 에 로그인합니다.
Azure 구독이 없는 경우 시작하기 전에 체험 계정을 만듭니다.

자식 DNS 영역을 만드는 방법에는 두 가지가 있습니다.
1.  “DNS 영역 만들기” 포털 페이지를 통해.
1.  부모 DNS 영역의 구성 페이지를 통해.


## <a name="create-child-dns-zone-via-create-dns-zone"></a>DNS 영역 만들기를 통해 자식 DNS 영역 만들기

이 단계에서는 이름이 **subdomain.contoso.com**인 새 자식 DNS 영역을 만들고 기존 부모 DNS 영역 **contoso.com**에 위임합니다. **DNS 영역 만들기** 페이지의 탭을 사용하여 DNS 영역을 만듭니다.
1.  Azure Portal 메뉴 또는 **홈**페이지에서 **리소스 만들기**를 선택합니다. **새로 만들기** 창이 나타납니다.
1.  **네트워킹**을 선택하고 **DNS 영역**을 선택한 다음, **추가** 단추를 선택합니다.

1.  **서브넷** 탭에서 다음 값을 입력하거나 선택합니다.
    * **구독**: 영역을 만들 구독을 선택합니다.
    * **리소스 그룹**: 기존 리소스 그룹을 입력합니다. 또는 **새로 만들기**를 선택하고 *MyResourceGroup*을 입력한 후 **확인**을 선택하여 새로 만들 수 있습니다. 리소스 그룹 이름은 Azure 구독 내에서 고유해야 합니다.
    * 이 확인란을 선택합니다. **이 영역은 Azure DNS에 이미 호스트된 기존 영역의 자식입니다.**
    * **부모 영역 구독**: 이 드롭다운에서 부모 DNS 영역 *contoso.com*이 생성된 구독 이름을 검색하여 선택합니다.
    * **부모 영역**: 검색 창에서 *contoso.com*을 입력하여 드롭다운 목록에 로드합니다. 로드된 후 드롭다운 목록에서 *contoso.com*을 선택합니다.
    * **이름:** 이 자습서 예제에 대한 *subdomain*을 입력합니다. 위의 단계에서 부모 영역을 선택하는 경우 부모 DNS 영역 이름 *contoso.com*이 이름에 접미사로 자동 추가됩니다.

1. 완료되면 **다음: 리뷰 + 만들기**를 클릭합니다.
1. **검토 + 만들기** 탭에서 요약을 검토하고, 유효성 검사 오류를 수정한 다음, **만들기**를 선택합니다.
영역을 만드는 데 몇 분이 걸릴 수 있습니다.

 
    :::image type="content" source="./media/dns-delegate-domain-azure-dns/create-dns-zone-inline.png" alt-text="DNS 영역 만들기 페이지의 스크린샷." lightbox="./media/dns-delegate-domain-azure-dns/create-dns-zone-expanded.png":::

## <a name="create-child-dns-zone-via-parent-dns-zone-overview-page"></a>부모 DNS 영역을 통해 자식 DNS 영역 만들기 페이지
부모 영역 개요 페이지의 **자식 영역** 단추를 사용하여 새 자식 DNS 영역을 만들고 부모 DNS 영역에 위임할 수도 있습니다. 이 단추를 사용하면 자식 영역에 대한 부모 매개 변수가 자동으로 미리 채워집니다. 

1.  Azure Portal의 **모든 리소스**에서 **MyResourceGroup** 리소스 그룹의 *contoso.com* DNS 영역을 엽니다. 더 쉽게 찾기 위해 **이름으로 필터링** 상자에 *contoso.com*을 입력할 수 있습니다.
1.  DNS 영역 개요 페이지에서 **+자식 영역** 단추를 선택합니다.

      :::image type="content" source="./media/dns-delegate-domain-azure-dns/create-child-zone-inline.png" alt-text="자식 영역 단추의 스크린샷." border="true" lightbox="./media/dns-delegate-domain-azure-dns/create-child-zone-expanded.png":::

1.  그러면 DNS 영역 만들기 페이지가 열립니다. 자식 영역 옵션이 이미 선택되어 있으며 이 페이지에서 부모 영역 구독과 부모 영역이 이미 채워져 있습니다.
1.  이 자습서 예제의 이름을 *child*라고 입력합니다. 부모 DNS 영역 이름 contoso.com은 이름에 접두사로 자동 추가됩니다.
1.  완료되면 **다음: 태그**를 선택하고 **다음: 리뷰 + 만들기**를 클릭합니다.
1.  **검토 + 만들기** 탭에서 요약을 검토하고, 유효성 검사 오류를 수정한 다음, **만들기**를 선택합니다.

    :::image type="content" source="./media/dns-delegate-domain-azure-dns/create-dns-zone-child-inline.png" alt-text="선택한 자식 영역 스크린샷" border="true"  lightbox="./media/dns-delegate-domain-azure-dns/create-dns-zone-child-expanded.png":::
## <a name="verify-child-dns-zone"></a>자식 DNS 영역 확인
이제 새 자식 DNS 영역 *subdomain.contoso.com*이 생성되었습니다. 위임이 올바르게 이루어졌는지 확인하려면 아래에 설명된 대로 자식 영역에 대한 nameserver(NS) 레코드가 부모 영역에 있는지 확인하는 것이 좋습니다.  

**자식 DNS 영역의 이름 서버 검색:**

1.  Azure Portal의 **모든 리소스**에서 **MyResourceGroup** 리소스 그룹의 *subdomain.contoso.com* DNS 영역을 엽니다. 더 쉽게 찾기 위해 **이름으로 필터링** 상자에 *subdomain.contoso.com*을 입력할 수 있습니다.
1.  DNS 영역 개요 페이지에서 이름 서버를 검색합니다. 이 예제에서는 contoso.com 영역에 이름 서버 *ns1-08.azure-dns.com, ns2-08.azure-dns.net, ns3-08.azure-dns.org* 및 *ns4-08.azure-dns.info*가 할당되었습니다.

      :::image type="content" source="./media/dns-delegate-domain-azure-dns/create-child-zone-ns-inline.png" alt-text="자식 영역 이름 서버 스크린샷" border="true" lightbox="./media/dns-delegate-domain-azure-dns/create-child-zone-ns-expanded.png":::
**부모 DNS 영역에서 NS 레코드 확인:**

이제 이 단계에서 부모 DNS 영역 *contoso.com*으로 이동하고 자식 영역 이름 서버의 NS 레코드 세트 항목이 만들어졌는지 확인합니다.

1. Azure Portal의 **모든 리소스**에서 **MyResourceGroup** 리소스 그룹의 contoso.com DNS 영역을 엽니다. 더 쉽게 찾기 위해 **이름으로 필터링** 상자에 contoso.com을 입력할 수 있습니다.
1.  *contoso.com* DNS 영역 개요 페이지에서 레코드 세트를 확인합니다.
1.  NS 및 이름 하위 도메인 형식의 레코드 집합이 이미 부모 DNS 영역에 생성된 것을 확인할 수 있습니다. 이 레코드 세트에 대한 값을 확인합니다. 이는 위의 단계에서 자식 DNS 영역에서 검색한 이름 서버 목록과 유사합니다.

     :::image type="content" source="./media/dns-delegate-domain-azure-dns/create-child-zone-ns-validate-inline.png" alt-text="자식 영역 이름 서버 확인의 스크린샷" border="true" lightbox="./media/dns-delegate-domain-azure-dns/create-child-zone-ns-validate-expanded.png":::
## <a name="clean-up-resources"></a>리소스 정리
이 자습서에서 만든 리소스가 더 이상 필요하지 않으면 **MyResourceGroup** 리소스 그룹을 삭제하여 제거합니다. **MyResourceGroup** 리소스 그룹을 열고 **리소스 그룹 삭제**를 선택합니다.



## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure DNS Private Zones 시나리오](private-dns-scenarios.md)
