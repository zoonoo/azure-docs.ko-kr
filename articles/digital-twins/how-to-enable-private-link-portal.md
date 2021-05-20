---
title: Private Link(미리 보기)로 프라이빗 액세스 사용 - 포털
titleSuffix: Azure Digital Twins
description: Azure Portal을 사용해서 Private Link로 Azure Digital Twins 솔루션에 대해 프라이빗 액세스를 사용하도록 설정하는 방법을 참조하세요.
author: baanders
ms.author: baanders
ms.date: 1/25/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 2545915edf9e39b63100a2bb16bd34fa6777675c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "100417834"
---
# <a name="enable-private-access-with-private-link-preview-azure-portal"></a>Private Link(미리 보기)로 프라이빗 액세스 사용: Azure Portal

[!INCLUDE [digital-twins-private-link-selector.md](../../includes/digital-twins-private-link-selector.md)]

이 문서에서는 [Azure Digital Twins 인스턴스에 대해 프라이빗 엔드포인트로 Private Link를 사용 설정](concepts-security.md#private-network-access-with-azure-private-link-preview)하는 여러 방법을 설명합니다(현재 미리 보기). Azure Digital Twins 인스턴스에 대해 프라이빗 엔드포인트를 구성하면 [Azure Virtual Network(VNet)](../virtual-network/virtual-networks-overview.md)에서 데이터 반출을 방지할 뿐만 아니라 Azure Digital Twins 인스턴스를 보호하고 공개 노출을 없앨 수 있습니다.

이 문서에서는 [**Azure Portal**](https://portal.azure.com)을 사용하여 프로세스를 안내합니다.

이 문서에서 설명하는 단계는 다음과 같습니다. 
1. Private Link를 켜고 Azure Digital Twins 인스턴스에 대한 프라이빗 엔드포인트를 구성합니다.
1. Private Link 연결만으로 API 액세스를 제한하기 위해 공용 네트워크 액세스 플래그를 사용하지 않거나 사용하도록 설정합니다.

## <a name="prerequisites"></a>필수 조건

프라이빗 엔드포인트를 설정하려면 먼저 엔드포인트를 배포할 수 있는 [**Azure Virtual Network(VNet)** ](../virtual-network/virtual-networks-overview.md)가 필요합니다. VNet이 아직 없으면 Azure Virtual Network [빠른 시작](../virtual-network/quick-create-portal.md) 중 하나에 따라 이를 설정할 수 있습니다.

## <a name="add-a-private-endpoint-for-an-azure-digital-twins-instance"></a>Azure Digital Twins 인스턴스에 대한 프라이빗 엔드포인트 추가 

[Azure Portal](https://portal.azure.com)을 사용할 때 인스턴스의 초기 설정 중 Azure Digital Twins 인스턴스에 대해 프라이빗 엔드포인트로 Private Link를 사용하도록 설정하거나 이미 존재하는 인스턴스에서 나중에 사용하도록 설정할 수 있습니다. 

이러한 생성 방법은 해당 인스턴스에 대해 동일한 구성 옵션 및 동일한 최종 결과를 제공합니다. 이 섹션에서는 각 작업을 수행하는 방법을 설명합니다.

>[!TIP]
> 또한 Azure Digital Twins 인스턴스 대신 Private Link 서비스를 통해 Private Link 엔드포인트를 설정할 수 있습니다. 이 방법도 동일한 구성 옵션 및 동일한 최종 결과를 제공합니다.
>
> Private Link 리소스 설정에 대한 자세한 내용은 [Azure Portal](../private-link/create-private-endpoint-portal.md), [Azure CLI](../private-link/create-private-endpoint-cli.md), [ARM](../private-link/create-private-endpoint-template.md) 또는 [PowerShell](../private-link/create-private-endpoint-powershell.md)에 대한 Private Link 설명서를 참조하세요.

### <a name="add-a-private-endpoint-during-instance-creation"></a>인스턴스를 만드는 동안 프라이빗 엔드포인트 추가

이 섹션에서는 현재 생성 중인 Azure Digital Twins 인스턴스에서 프라이빗 엔드포인트로 Private Link를 사용하도록 설정합니다. 이 섹션에서는 만들기 프로세스의 네트워킹 단계를 자세히 설명합니다. 새 Azure Digital Twins 인스턴스 만들기에 대한 전체 연습은 [*방법: 인스턴스 및 인증 설정*](how-to-set-up-instance-portal.md)을 참조하세요.

Private Link 옵션은 인스턴스 설정의 **네트워킹** 탭에 있습니다.

이 탭에서는 **연결 방법** 에 대해 **프라이빗 엔드포인트** 옵션을 선택하여 프라이빗 엔드포인트를 사용하도록 설정할 수 있습니다.

그러면 프라이빗 엔드포인트 세부 정보를 구성할 수 있는 **프라이빗 엔드포인트 연결** 이라는 섹션이 추가됩니다. 계속해서 **+ 추가** 단추를 선택합니다.

:::image type="content" source="media/how-to-enable-private-link/create-instance-networking-1.png" alt-text="Azure Digital Twins에 대해 리소스 만들기 대화 상자의 네트워킹 탭을 보여 주는 Azure Portal의 스크린샷입니다. 탭 이름, 연결 방법에 대한 프라이빗 엔드포인트, 새 프라이빗 엔드포인트 연결을 만들기 위한 +추가 단추가 강조 표시되어 있습니다." lightbox="media/how-to-enable-private-link/create-instance-networking-1.png":::

그러면 새 프라이빗 엔드포인트의 세부 정보를 입력할 수 있는 페이지가 열립니다.

:::image type="content" source="media/how-to-enable-private-link/create-private-endpoint-full.png" alt-text="프라이빗 엔드포인트 만들기 페이지를 보여 주는 Azure Portal의 스크린샷입니다. 아래에서 설명된 필드가 포함되어 있습니다.":::

1. **구독** 및 **리소스 그룹** 에 선택 항목을 입력합니다. **위치** 를 사용할 VNet과 동일한 위치로 설정합니다. 엔드포인트 **이름** 을 선택하고 **대상 하위 리소스** 로 *API* 를 선택합니다.

1. 그런 후 엔드포인트 배포를 위해 사용할 **가상 네트워크** 및 **서브넷** 을 선택합니다.

1. 마지막으로 **비공개 DNS 영역과 통합** 할지 여부를 선택합니다. 기본값 **예** 를 사용하거나 이 옵션에 대한 도움말이 필요하면 포털 링크에 따라 [프라이빗 DNS 통합에 대해 자세히 알아볼 수 있습니다](../private-link/private-endpoint-overview.md#dns-configuration).

구성 옵션을 입력한 후 **확인** 을 눌러 완료합니다.

그러면 **프라이빗 엔드포인트 연결에서 새 엔드포인트를 확인할 수 있는 Azure Digital Twins 인스턴스 설정의 **네트워킹** 탭이 다시 표시됩니다.

:::image type="content" source="media/how-to-enable-private-link/create-instance-networking-2.png" alt-text="Azure Digital Twins에 대해 리소스 만들기 대화 상자의 네트워킹 탭을 보여 주는 Azure Portal의 스크린샷입니다. 새 프라이빗 엔드포인트 연결, 탐색 단추(검토 + 만들기, 이전, 다음: 고급)가 강조 표시되어 있습니다." lightbox="media/how-to-enable-private-link/create-instance-networking-2.png":::

그런 후 아래의 탐색 단추를 사용하여 남은 인스턴스 설정을 계속할 수 있습니다.

### <a name="add-a-private-endpoint-to-an-existing-instance"></a>기존 인스턴스에 프라이빗 엔드포인트 추가

이 섹션에서는 이미 존재하는 Azure Digital Twins 인스턴스에 대해 프라이빗 엔드포인트로 Private Link를 사용 설정합니다.

1. 먼저 브라우저에서 [Azure Portal](https://portal.azure.com)로 이동합니다. 포털 검색 창에서 이름을 검색하여 Azure Digital Twins 인스턴스를 표시합니다.

1. 왼쪽 메뉴에서 **네트워킹(미리 보기)** 를 선택합니다.

1. **프라이빗 엔드포인트 연결** 탭으로 전환합니다.

1. **+ 프라이빗 엔드포인트** 를 선택하여 **프라이빗 엔드포인트 만들기** 설정을 엽니다.

    :::image type="content" source="media/how-to-enable-private-link/add-endpoint-digital-twins.png" alt-text="Azure Digital Twins 인스턴스에 대한 네트워킹(미리 보기) 페이지를 보여 주는 Azure Portal의 스크린샷입니다. 프라이빗 엔드포인트 연결 탭과 + 프라이빗 엔드포인트 단추가 강조 표시되어 있습니다." lightbox="media/how-to-enable-private-link/add-endpoint-digital-twins.png":::

1.  **기본**  탭에서 **구독** 및 프로젝트의 **리소스 그룹** 을 선택하고 엔드포인트의 **이름** 및 **지역** 을 선택합니다. 지역은 사용 중인 VNet의 지역과 동일해야 합니다.

    :::image type="content" source="media/how-to-enable-private-link/create-private-endpoint-1.png" alt-text="프라이빗 엔드포인트 만들기 대화 상자의 첫 번째(기본) 탭을 보여 주는 Azure Portal의 스크린샷입니다. 위에서 설명된 필드가 포함되어 있습니다.":::

    완료되었으면 **다음: 리소스 >** 단추를 선택하여 다음 탭으로 이동합니다.

1. **리소스** 탭에서 이 정보를 입력하거나 선택합니다. 
    * **연결 방법**: **내 디렉터리의 Azure 리소스에 연결** 을 선택하여 Azure Digital Twins 인스턴스를 검색합니다.
    * **구독**: 구독을 입력합니다.
    * **리소스 종류**: **Microsoft.DigitalTwins/digitalTwinsInstances** 를 선택합니다.
    * **리소스**: Azure Digital Twins 인스턴스의 이름을 선택합니다.
    * **대상 하위 리소스**: **API** 를 선택합니다.

    :::image type="content" source="media/how-to-enable-private-link/create-private-endpoint-2.png" alt-text="프라이빗 엔드포인트 만들기 대화 상자의 두 번째(리소스) 탭을 보여 주는 Azure Portal의 스크린샷입니다. 위에서 설명된 필드가 포함되어 있습니다.":::

    완료되었으면 **다음: 연결 >** 단추를 선택하여 다음 탭으로 이동합니다.    

1.  **구성** 탭에서 다음 정보를 입력하거나 선택합니다.
    * **가상 네트워크**: 가상 네트워크를 선택합니다.
    * **서브넷**: 가상 네트워크에서 서브넷을 선택합니다.
    * **프라이빗 DNS 영역과 통합**: **프라이빗 DNS 영역과 통합** 여부를 선택합니다. 기본값 **예** 를 사용하거나 이 옵션에 대한 도움말이 필요하면 포털 링크에 따라 [프라이빗 DNS 통합에 대해 자세히 알아볼 수 있습니다](../private-link/private-endpoint-overview.md#dns-configuration).
    **예** 를 선택하면 기본 구성 정보를 그대로 둘 수 있습니다.

    :::image type="content" source="media/how-to-enable-private-link/create-private-endpoint-3.png" alt-text="프라이빗 엔드포인트 만들기 대화 상자의 세 번째(구성) 탭을 보여 주는 Azure Portal의 스크린샷입니다. 위에서 설명된 필드가 포함되어 있습니다.":::

    완료되었으면 **검토 + 만들기** 단추를 선택하여 설정을 완료할 수 있습니다. 

1. **검토 + 만들기** 탭에서 선택 항목을 검토한 후  **만들기** 단추를 선택합니다. 

엔드포인트 배포가 완료되었으면 Azure Digital Twins 인스턴스에 대한 프라이빗 엔드포인트 연결이 표시됩니다.

>[!TIP]
> Azure Portal의 Private Link 센터에서도 엔드포인트를 확인할 수 있습니다.

## <a name="disable--enable-public-network-access-flags"></a>공용 네트워크 액세스 플래그 사용 안 함/사용

네트워크 보안 향상을 위해 모든 공용 연결을 거부하고 프라이빗 엔드포인트를 통한 연결만 허용하도록 Azure Digital Twins 인스턴스를 구성할 수 있습니다. 이 작업은 **공용 네트워크 액세스 플래그** 를 사용하여 수행됩니다. 

이 정책을 사용하면 API 액세스를 Private Link 연결만으로 제한할 수 있습니다. 공용 네트워크 액세스 플래그가 *사용 안 함* 으로 설정되었으면 퍼블릭 클라우드에서 Azure Digital Twins 인스턴스 데이터 영역으로의 모든 REST API 호출이 `403, Unauthorized`를 반환합니다. 또는 정책이 *사용 안 함* 으로 설정되었고 프라이빗 엔드포인트를 통해 요청을 수행하면 API 호출이 성공합니다.

이 문서에서는 [Azure Portal](https://portal.azure.com)을 사용하여 네트워크 플래그의 값을 업데이트하는 방법을 보여줍니다. Azure CLI 또는 ARMClient 명령 도구를 사용하여 이를 수행하는 방법은 이 문서의 [CLI 버전](how-to-enable-private-link-cli.md)을 참조하세요.

### <a name="use-the-azure-portal"></a>Azure Portal 사용

[Azure Portal](https://portal.azure.com)에서 공용 네트워크 액세스를 사용 안 함 또는 사용하도록 설정하려면 포털을 열고 Azure Digital Twins 인스턴스로 이동합니다.

1. 왼쪽 메뉴에서 **네트워킹(미리 보기)** 를 선택합니다.

1. **공용 액세스** 탭에서 **공용 네트워크 액세스 허용** 을 **사용 안 함** 또는 **모든 네트워크** 로 설정합니다.

    :::row:::
        :::column:::
            :::image type="content" source="media/how-to-enable-private-link/network-flag-portal.png" alt-text="Azure Digital Twins 인스턴스의 네트워킹(미리 보기) 페이지를 보여주는 Azure Portal의 스크린샷입니다. 공용 액세스 탭과 공용 네트워크 액세스를 허용할지 여부를 선택할 수 있는 옵션이 강조 표시되어 있습니다." lightbox="media/how-to-enable-private-link/network-flag-portal.png":::
        :::column-end:::
        :::column:::
        :::column-end:::
    :::row-end:::

    **저장** 을 선택합니다.

## <a name="next-steps"></a>다음 단계

Azure Private Link에 대해 자세히 알아보기: 
* [*Azure Private Link 서비스는 무엇입니까?* ](../private-link/private-link-service-overview.md)