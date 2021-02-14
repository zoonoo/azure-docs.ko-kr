---
title: 개인 링크를 사용 하 여 개인 액세스 사용 (미리 보기)-포털
titleSuffix: Azure Digital Twins
description: Azure Portal를 사용 하 여 개인 링크를 통해 Azure Digital Twins 솔루션에 대 한 개인 액세스를 사용 하도록 설정 하는 방법을 참조 하세요.
author: baanders
ms.author: baanders
ms.date: 1/25/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 2545915edf9e39b63100a2bb16bd34fa6777675c
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100417834"
---
# <a name="enable-private-access-with-private-link-preview-azure-portal"></a>개인 링크를 사용 하 여 개인 액세스 사용 (미리 보기): Azure Portal

[!INCLUDE [digital-twins-private-link-selector.md](../../includes/digital-twins-private-link-selector.md)]

이 문서에서는 [Azure Digital Twins 인스턴스의 개인 끝점과 개인 링크를 사용 하도록 설정](concepts-security.md#private-network-access-with-azure-private-link-preview) 하는 다양 한 방법에 대해 설명 합니다 (현재 미리 보기 상태). Azure digital twins 인스턴스에 대 한 개인 끝점을 구성 하면 azure digital twins 인스턴스를 보호 하 고, 공개 노출을 제거 하 고, [azure Virtual Network (VNet)](../virtual-network/virtual-networks-overview.md)에서 데이터 반출을 방지할 수 있습니다.

이 문서에서는 [**Azure Portal**](https://portal.azure.com)를 사용 하는 프로세스를 안내 합니다.

이 문서에서 설명 하는 단계는 다음과 같습니다. 
1. 개인 링크를 켜고 Azure Digital Twins 인스턴스에 대 한 개인 끝점을 구성 합니다.
1. 공용 네트워크 액세스 플래그를 사용 하지 않거나 사용 하도록 설정 하 여 전용 링크 연결에 대 한 API 액세스만 제한 합니다.

## <a name="prerequisites"></a>사전 요구 사항

개인 끝점을 설정 하려면 먼저 끝점을 배포할 수 있는 [**Azure Virtual Network (VNet)**](../virtual-network/virtual-networks-overview.md) 가 필요 합니다. VNet이 아직 없는 경우 Azure Virtual Network 빠른 시작 중 하나를 수행 하 여이 [를 설정할](../virtual-network/quick-create-portal.md) 수 있습니다.

## <a name="add-a-private-endpoint-for-an-azure-digital-twins-instance"></a>Azure Digital Twins 인스턴스에 대 한 개인 끝점 추가 

[Azure Portal](https://portal.azure.com)사용 하는 경우 인스턴스의 초기 설정 중에 Azure Digital twins 인스턴스의 개인 끝점을 사용 하 여 개인 링크를 설정 하거나 이미 존재 하는 인스턴스에서는 나중에 사용 하도록 선택할 수 있습니다. 

이러한 생성 방법 중 하나는 인스턴스에 대해 동일한 구성 옵션과 동일한 최종 결과를 제공 합니다. 이 섹션에서는 각 작업을 수행 하는 방법을 설명 합니다.

>[!TIP]
> Azure Digital Twins 인스턴스 대신 개인 링크 서비스를 통해 개인 링크 끝점을 설정할 수도 있습니다. 또한 동일한 구성 옵션 및 동일한 최종 결과를 제공 합니다.
>
> 개인 링크 리소스를 설정 하는 방법에 대 한 자세한 내용은 [Azure Portal](../private-link/create-private-endpoint-portal.md), [Azure CLI](../private-link/create-private-endpoint-cli.md), [ARM](../private-link/create-private-endpoint-template.md)또는 [PowerShell](../private-link/create-private-endpoint-powershell.md)에 대 한 개인 링크 설명서를 참조 하세요.

### <a name="add-a-private-endpoint-during-instance-creation"></a>인스턴스를 만드는 동안 개인 끝점 추가

이 섹션에서는 현재 만들어지는 Azure Digital Twins 인스턴스에서 개인 끝점을 사용 하 여 개인 링크를 사용 하도록 설정 합니다. 이 섹션에서는 만들기 프로세스의 네트워킹 단계를 중점적으로 설명 합니다. 새 Azure Digital Twins 인스턴스를 만드는 전체 연습은 [*방법: 인스턴스 및 인증 설정*](how-to-set-up-instance-portal.md)을 참조 하세요.

개인 링크 옵션은 인스턴스 설정의 **네트워킹** 탭에 있습니다.

이 탭에서 **연결 방법** 에 대 한 **개인 끝점** 옵션을 선택 하 여 개인 끝점을 사용 하도록 설정할 수 있습니다.

그러면 개인 끝점의 세부 정보를 구성할 수 있는 **개인 끝점 연결** 이라는 섹션이 추가 됩니다. **+ 추가** 단추를 선택 하 여 계속 합니다.

:::image type="content" source="media/how-to-enable-private-link/create-instance-networking-1.png" alt-text="Azure Digital Twins에 대 한 리소스 만들기 대화 상자의 네트워킹 탭을 보여 주는 Azure Portal의 스크린샷 탭 이름, 연결 방법에 대 한 개인 끝점 옵션 및 + 추가 단추를 강조 표시 하 여 새 개인 끝점 연결을 만들 수 있습니다." lightbox="media/how-to-enable-private-link/create-instance-networking-1.png":::

그러면 새 개인 끝점의 세부 정보를 입력 하는 페이지가 열립니다.

:::image type="content" source="media/how-to-enable-private-link/create-private-endpoint-full.png" alt-text="개인 끝점 만들기 페이지를 표시 하는 Azure Portal의 스크린샷 여기에는 아래에 설명 된 필드가 포함 됩니다.":::

1. **구독** 및 **리소스 그룹** 에 대 한 선택 항목을 입력 합니다. **위치** 를 사용 하는 VNet과 동일한 위치로 설정 합니다. 끝점의 **이름을** 선택 하 고 **대상 하위 리소스** 에서 *API* 를 선택 합니다.

1. 그런 다음 끝점을 배포 하는 데 사용할 **가상 네트워크** 및 **서브넷** 을 선택 합니다.

1. 마지막으로, **개인 DNS 영역과 통합할** 지 여부를 선택 합니다. 기본값 **예** 또는를 사용할 수 있습니다 .이 옵션에 대 한 도움말을 보려면 포털의 링크를 따라 [개인 DNS 통합에 대해 자세히 알아볼](../private-link/private-endpoint-overview.md#dns-configuration)수 있습니다.

구성 옵션을 입력 한 후에는 **확인** 을 눌러 완료 합니다.

그러면 새 끝점이 * * 개인 끝점 연결 아래에 표시 되는 Azure Digital Twins 인스턴스 설정의 **네트워킹** 탭으로 돌아갑니다.

:::image type="content" source="media/how-to-enable-private-link/create-instance-networking-2.png" alt-text="Azure Digital Twins에 대 한 리소스 만들기 대화 상자의 네트워킹 탭을 보여 주는 Azure Portal의 스크린샷 새 개인 끝점 연결 및 탐색 단추 (검토 + 만들기, 이전, 다음: 고급)에 대해 강조 표시 됩니다." lightbox="media/how-to-enable-private-link/create-instance-networking-2.png":::

그런 다음 아래쪽 탐색 단추를 사용 하 여 나머지 인스턴스 설치를 계속할 수 있습니다.

### <a name="add-a-private-endpoint-to-an-existing-instance"></a>기존 인스턴스에 개인 끝점 추가

이 섹션에서는 이미 존재 하는 Azure Digital Twins 인스턴스에 대해 개인 끝점을 사용 하 여 개인 링크를 사용 하도록 설정 합니다.

1. 먼저 브라우저에서 [Azure Portal](https://portal.azure.com) 로 이동 합니다. 포털 검색 창에서 이름을 검색 하 여 Azure Digital Twins 인스턴스를 가져옵니다.

1. 왼쪽 메뉴에서 **네트워킹 (미리 보기)** 을 선택 합니다.

1. **개인 끝점 연결** 탭으로 전환 합니다.

1. **+ 개인 끝점** 을 선택 하 여 **개인 끝점 만들기** 설정을 엽니다.

    :::image type="content" source="media/how-to-enable-private-link/add-endpoint-digital-twins.png" alt-text="Azure Digital Twins 인스턴스에 대 한 네트워킹 (미리 보기) 페이지를 보여 주는 Azure Portal의 스크린샷 개인 끝점 연결 탭이 강조 표시 되 고 + 개인 끝점 단추도 강조 표시 됩니다." lightbox="media/how-to-enable-private-link/add-endpoint-digital-twins.png":::

1.  **기본 사항**   탭에서 프로젝트의 **구독** 및 **리소스 그룹** 을 입력 하거나 선택 하 고 끝점의 **이름과** **지역을** 선택 합니다. 지역은 사용 중인 VNet의 지역과 동일 해야 합니다.

    :::image type="content" source="media/how-to-enable-private-link/create-private-endpoint-1.png" alt-text="개인 끝점 만들기 대화 상자의 첫 번째 (기본) 탭을 보여 주는 Azure Portal의 스크린샷 여기에는 위에서 설명한 필드가 포함 됩니다.":::

    완료 되 면 **다음: 리소스 >** 단추를 선택 하 여 다음 탭으로 이동 합니다.

1. **리소스** 탭에서 다음 정보를 입력 하거나 선택 합니다. 
    * **연결 방법**: **내 디렉터리에서 Azure 리소스에 연결** 을 선택 하 여 azure Digital twins 인스턴스를 검색 합니다.
    * **구독**: 구독을 입력 합니다.
    * **리소스 종류**: **DigitalTwins/digitalTwinsInstances를 선택 합니다.**
    * **리소스**: Azure Digital twins 인스턴스의 이름을 선택 합니다.
    * **대상 하위 리소스**: **API** 를 선택 합니다.

    :::image type="content" source="media/how-to-enable-private-link/create-private-endpoint-2.png" alt-text="개인 끝점 만들기 대화 상자의 두 번째 (리소스) 탭을 표시 하는 Azure Portal의 스크린샷 여기에는 위에서 설명한 필드가 포함 됩니다.":::

    완료 되 면 **다음: 구성 >** 단추를 선택 하 여 다음 탭으로 이동 합니다.    

1.  **구성** 탭에서 다음 정보를 입력 하거나 선택 합니다.
    * **가상 네트워크**: 가상 네트워크를 선택 합니다.
    * **서브넷**: 가상 네트워크에서 서브넷을 선택 합니다.
    * **개인 dns 영역에 통합**: **개인 dns 영역과 통합할** 지 여부를 선택 합니다. 기본값 **예** 또는를 사용할 수 있습니다 .이 옵션에 대 한 도움말을 보려면 포털의 링크를 따라 [개인 DNS 통합에 대해 자세히 알아볼](../private-link/private-endpoint-overview.md#dns-configuration)수 있습니다.
    **예** 를 선택 하는 경우 기본 구성 정보를 그대로 둘 수 있습니다.

    :::image type="content" source="media/how-to-enable-private-link/create-private-endpoint-3.png" alt-text="개인 끝점 만들기 대화 상자의 세 번째 (구성) 탭을 표시 하는 Azure Portal의 스크린샷 여기에는 위에서 설명한 필드가 포함 됩니다.":::

    완료 되 면 **검토 + 만들기** 단추를 선택 하 여 설치를 완료할 수 있습니다. 

1. **검토 + 만들기** 탭에서 선택 항목을 검토 하 고  **만들기** 단추를 선택 합니다. 

끝점이 배포를 완료 하면 Azure Digital Twins 인스턴스에 대 한 개인 끝점 연결에 표시 되어야 합니다.

>[!TIP]
> Azure Portal의 개인 링크 센터에서 끝점을 볼 수도 있습니다.

## <a name="disable--enable-public-network-access-flags"></a>공용 네트워크 액세스 플래그 사용/사용 안 함

모든 공용 연결을 거부 하 고 개인 끝점을 통한 연결만 허용 하 여 네트워크 보안을 향상 하도록 Azure Digital Twins 인스턴스를 구성할 수 있습니다. 이 작업은 **공용 네트워크 액세스 플래그** 를 사용 하 여 수행 됩니다. 

이 정책을 사용 하면 전용 링크 연결에 대 한 API 액세스만 제한할 수 있습니다. 공용 네트워크 액세스 플래그가 *사용 안 함으로* 설정 되 면 공용 클라우드의 Azure Digital twins 인스턴스 데이터 평면에 대 한 모든 REST API 호출이 반환 됩니다 `403, Unauthorized` . 또는 정책을 *사용 안 함으로* 설정 하 고 개인 끝점을 통해 요청을 수행 하면 API 호출이 성공 합니다.

이 문서에서는 [Azure Portal](https://portal.azure.com)를 사용 하 여 네트워크 플래그의 값을 업데이트 하는 방법을 보여 줍니다. Azure CLI 또는 ARMClient 명령 도구를 사용 하 여이 작업을 수행 하는 방법에 대 한 지침은이 문서의 [CLI 버전](how-to-enable-private-link-cli.md) 을 참조 하세요.

### <a name="use-the-azure-portal"></a>Azure Portal 사용

[Azure Portal](https://portal.azure.com)에서 공용 네트워크 액세스를 사용 하지 않도록 설정 하거나 사용 하도록 설정 하려면 포털을 열고 Azure Digital twins 인스턴스로 이동 합니다.

1. 왼쪽 메뉴에서 **네트워킹 (미리 보기)** 을 선택 합니다.

1. **공용 액세스** 탭에서 **사용 안 함** 또는 **모든 네트워크** **에 대 한 공용 네트워크 액세스 허용을** 설정 합니다.

    :::row:::
        :::column:::
            :::image type="content" source="media/how-to-enable-private-link/network-flag-portal.png" alt-text="Azure Digital Twins 인스턴스에 대 한 네트워킹 (미리 보기) 페이지를 보여 주는 Azure Portal의 스크린샷 공용 액세스 탭이 강조 표시 되 고 공용 네트워크 액세스 허용 여부를 선택 하는 옵션도 강조 표시 됩니다." lightbox="media/how-to-enable-private-link/network-flag-portal.png":::
        :::column-end:::
        :::column:::
        :::column-end:::
    :::row-end:::

    **저장** 을 선택합니다.

## <a name="next-steps"></a>다음 단계

Azure의 개인 링크에 대해 자세히 알아보세요. 
* [*Azure 개인 링크 서비스 란?*](../private-link/private-link-service-overview.md)