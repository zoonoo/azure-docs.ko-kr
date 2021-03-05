---
title: 사설 끝점을 사용 하 여 가상 네트워크와 Azure Functions 통합
description: Azure 가상 네트워크에 함수를 연결 하 고 개인 끝점을 사용 하 여 잠금을 해제 하는 방법을 보여 주는 단계별 자습서입니다.
ms.topic: article
ms.date: 2/22/2021
ms.openlocfilehash: a7bad58167009b4089724165813eb061996f1e6b
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102200209"
---
# <a name="tutorial-integrate-azure-functions-with-an-azure-virtual-network-using-private-endpoints"></a>자습서: 개인 끝점을 사용 하 여 Azure 가상 네트워크와 Azure Functions 통합

이 자습서에서는 Azure Functions를 사용 하 여 개인 끝점으로 Azure 가상 네트워크의 리소스에 연결 하는 방법을 보여 줍니다. Service bus 큐 트리거를 사용 하는 가상 네트워크 뒤에 잠긴 저장소 계정을 사용 하 여 함수를 만듭니다.

> [!div class="checklist"]
> * 프리미엄 계획에서 함수 앱 만들기
> * Azure 리소스 (Service Bus, Storage 계정, Virtual Network) 만들기
> * 개인 끝점 뒤에서 저장소 계정 잠금
> * 개인 끝점 뒤에 Service Bus 잠금
> * Service Bus 및 HTTP 트리거를 모두 사용 하 여 함수 앱을 배포 합니다.
> * 전용 끝점 뒤에서 함수 앱 잠금
> * 함수 앱이 가상 네트워크를 통해 안전 하 게 보호 되는지 테스트 합니다.
> * 리소스 정리

## <a name="create-a-function-app-in-a-premium-plan"></a>프리미엄 계획에서 함수 앱 만들기

먼저이 자습서에서는 c #을 사용 하므로 [프리미엄 계획] 에서 .net 함수 앱을 만듭니다. 다른 언어도 Windows 에서도 지원 됩니다. 이 계획은 가상 네트워크 통합을 지 원하는 동시에 서버 리스 규모를 제공 합니다.

1. Azure Portal 메뉴 또는 **홈** 페이지에서 **리소스 만들기** 를 선택합니다.

1. **새로 만들기** 페이지에서 **컴퓨팅** > **함수 앱** 을 선택합니다.

1. **기본 사항** 페이지에서 함수 앱 설정을 다음 표에서 지정한 대로 사용합니다.

    | 설정      | 제안 값  | 설명 |
    | ------------ | ---------------- | ----------- |
    | **구독** | 사용자의 구독 | 이 새 함수 앱이 만들어질 구독입니다. |
    | **[리소스 그룹](../azure-resource-manager/management/overview.md)** |  *myResourceGroup* | 함수 앱을 만들 새 리소스 그룹의 이름입니다. |
    | **함수 앱 이름** | 전역적으로 고유한 이름 | 새 함수 앱을 식별하는 이름입니다. 유효한 문자는 `a-z`(대/소문자 구분 안 함), `0-9`및 `-`입니다.  |
    |**게시**| 코드 | 코드 파일 또는 Docker 컨테이너를 게시하는 옵션입니다. |
    | **런타임 스택** | .NET | 이 자습서에서는 .NET을 사용 합니다. |
    |**지역**| 기본 지역 | 사용자 근처 또는 함수가 액세스할 기타 서비스에 가까운 [지역](https://azure.microsoft.com/regions/)을 선택합니다. |

1. 완료되면 **다음: 호스팅** 을 선택합니다. **호스팅** 페이지에서 다음 설정을 입력합니다.

    | 설정      | 제안 값  | Description |
    | ------------ | ---------------- | ----------- |
    | **[Storage 계정](../storage/common/storage-account-create.md)** |  전역적으로 고유한 이름 |  함수 앱에서 사용하는 스토리지 계정을 만듭니다. Storage 계정 이름은 3자에서 24자 사이여야 하고 숫자 및 소문자만 포함할 수 있습니다. 기존 계정을 사용할 수도 있습니다. 여기서는 [스토리지 계정 요구 사항](./storage-considerations.md#storage-account-requirements)을 충족해야 합니다. |
    |**운영 체제**| Windows | 이 자습서에서는 Windows |
    | **[계획](./functions-scale.md)** | Premium | 함수 앱에 리소스가 할당되는 방법을 정의하는 호스팅 계획입니다. **프리미엄** 을 선택합니다. 기본적으로 새 App Service 요금제가 만들어집니다. 기본 **SKU 및 크기** 는 **EP1** 입니다. 여기서 EP는 _탄력적 프리미엄_ 을 의미합니다. 자세한 내용은 [프리미엄 SKU 목록](./functions-premium-plan.md#available-instance-skus)을 참조하세요.<br/>프리미엄 계획에서 JavaScript 함수를 실행 중인 경우 vCPU 수가 더 작은 인스턴스를 선택해야 합니다. 자세한 내용은 [단일 코어 프리미엄 계획 선택](./functions-reference-node.md#considerations-for-javascript-functions)을 참조하세요.  |

1. 완료되면 **다음: 모니터링** 을 선택합니다. **모니터링** 페이지에서 다음 설정을 입력합니다.

    | 설정      | 제안 값  | Description |
    | ------------ | ---------------- | ----------- |
    | **[Application Insights](./functions-monitoring.md)** | 기본값 | 가장 가까운 지원 영역에 동일한 *앱 이름* 의 Application Insight 리소스를 만듭니다. 이 설정을 확장하면 **새 리소스 이름** 을 변경하거나 데이터를 저장하려는 [Azure 지리적 위치](https://azure.microsoft.com/global-infrastructure/geographies/)에서 다른 **위치** 를 선택합니다. |

1. **검토 + 만들기** 를 선택하여 앱 구성 선택을 검토합니다.

1. **검토 + 만들기** 페이지에서 설정을 검토한 다음, **만들기** 를 선택하여 함수 앱을 프로비저닝하고 배포합니다.

1. 포털의 오른쪽 위 모서리에 있는 **알림** 아이콘을 선택하고 **배포 성공** 메시지를 확인합니다.

1. **리소스로 이동** 을 선택하여 함수 앱을 봅니다. **대시보드에 고정** 을 선택할 수도 있습니다. 고정하면 대시보드에서 이 함수 앱 리소스로 쉽게 돌아올 수 있습니다.

1. 축하합니다! Premium 함수 앱을 만들었습니다.

## <a name="create-azure-resources"></a>Azure 리소스 만들기

### <a name="create-a-storage-account"></a>스토리지 계정 만들기

가상 네트워크에는 함수 앱을 처음 만들 때 만든 것과는 별도의 저장소 계정이 필요 합니다.

1. Azure Portal 메뉴 또는 **홈** 페이지에서 **리소스 만들기** 를 선택합니다.

1. 새로 만들기 페이지에서 **저장소 계정** 을 검색 하 고 **만들기** 를 선택 합니다.

1. **기본 사항** 탭에서 아래 표에 지정 된 대로 설정을 설정 합니다. 나머지는 기본값으로 남겨둘 수 있습니다.

    | 설정      | 제안 값  | 설명      |
    | ------------ | ---------------- | ---------------- |
    | **구독** | 사용자의 구독 | 리소스가 만들어지는 구독입니다. | 
    | **[리소스 그룹](../azure-resource-manager/management/overview.md)**  | myResourceGroup | 함수 앱을 사용 하 여 만든 리소스 그룹을 선택 합니다. |
    | **이름** | mysecurestorage| 개인 끝점이 적용 될 저장소 계정의 이름입니다. |
    | **[지역](https://azure.microsoft.com/regions/)** | myFunctionRegion | 함수 앱을 만든 지역을 선택 합니다. |

1. **검토 + 만들기** 를 선택합니다. 유효성 검사가 완료되면 **만들기** 를 선택합니다.

### <a name="create-a-service-bus"></a>Service Bus 만들기

1. Azure Portal 메뉴 또는 **홈** 페이지에서 **리소스 만들기** 를 선택합니다.

1. 새로 만들기 페이지에서 **Service Bus** 를 검색 하 고 **만들기** 를 선택 합니다.

1. **기본 사항** 탭에서 아래 표에 지정 된 대로 설정을 설정 합니다. 나머지는 기본값으로 남겨둘 수 있습니다.

    | 설정      | 제안 값  | 설명      |
    | ------------ | ---------------- | ---------------- |
    | **구독** | 사용자의 구독 | 리소스가 만들어지는 구독입니다. |
    | **[리소스 그룹](../azure-resource-manager/management/overview.md)**  | myResourceGroup | 함수 앱을 사용 하 여 만든 리소스 그룹을 선택 합니다. |
    | **이름** | myServiceBus| 개인 끝점이 적용 될 service bus의 이름입니다. |
    | **[지역](https://azure.microsoft.com/regions/)** | myFunctionRegion | 함수 앱을 만든 지역을 선택 합니다. |
    | **가격 책정 계층** | Premium | Service Bus에서 개인 끝점을 사용 하려면이 계층을 선택 합니다. |

1. **검토 + 만들기** 를 선택합니다. 유효성 검사가 완료되면 **만들기** 를 선택합니다.

### <a name="create-a-virtual-network"></a>가상 네트워크 만들기

이 자습서의 Azure 리소스는와 통합 되거나 가상 네트워크 내에 배치 됩니다. 가상 네트워크에 포함 된 네트워크 트래픽을 유지 하려면 개인 끝점을 사용 합니다.

이 자습서에서는 두 개의 서브넷을 만듭니다.
- **기본값**: 개인 끝점의 서브넷입니다. 개인 IP 주소는이 서브넷에서 제공 됩니다.
- **함수**: Azure Functions 가상 네트워크 통합에 대 한 서브넷입니다. 이 서브넷은 함수 앱에 위임 됩니다.

이제 함수 앱이 통합 되는 가상 네트워크를 만듭니다.

1. Azure Portal 메뉴 또는 홈 페이지에서 **리소스 만들기** 를 선택합니다.

1. 새로 만들기 페이지에서 **Virtual Network** 를 검색 하 고 **만들기** 를 선택 합니다.

1. **기본 사항** 탭에서 아래 지정 된 대로 가상 네트워크 설정을 사용 합니다.

    | 설정      | 제안 값  | 설명      |
    | ------------ | ---------------- | ---------------- |
    | **구독** | 사용자의 구독 | 리소스가 만들어지는 구독입니다. | 
    | **[리소스 그룹](../azure-resource-manager/management/overview.md)**  | myResourceGroup | 함수 앱을 사용 하 여 만든 리소스 그룹을 선택 합니다. |
    | **이름** | myVirtualNet| 함수 앱이 연결 되는 가상 네트워크의 이름입니다. |
    | **[지역](https://azure.microsoft.com/regions/)** | myFunctionRegion | 함수 앱을 만든 지역을 선택 합니다. |

1. **IP 주소** 탭에서 **서브넷 추가** 를 선택 합니다. 서브넷을 추가할 때 아래에 지정 된 대로 설정을 사용 합니다.

    :::image type="content" source="./media/functions-create-vnet/1-create-vnet-ip-address.png" alt-text="가상 네트워크 구성 보기 만들기의 스크린샷":::

    | 설정      | 제안 값  | 설명      |
    | ------------ | ---------------- | ---------------- |
    | **서브넷 이름** | functions | 함수 앱이 연결 될 서브넷의 이름입니다. | 
    | **서브넷 주소 범위** | 10.0.1.0/24 | 위의 이미지에서 IPv4 주소 공간은 10.0.0.0/16입니다. 위의 작업이 10.1.0.0/16 인 경우 권장 *서브넷 주소 범위* 는 10.1.1.0/24입니다. |

1. **검토 + 만들기** 를 선택합니다. 유효성 검사가 완료되면 **만들기** 를 선택합니다.

## <a name="lock-down-your-storage-account-with-private-endpoints"></a>개인 끝점을 사용 하 여 저장소 계정 잠금

Azure 개인 끝점은 개인 IP 주소를 사용 하 여 특정 Azure 리소스에 연결 하는 데 사용 됩니다. 이 연결을 통해 네트워크 트래픽이 선택한 가상 네트워크 내에 유지 되 고 특정 리소스에 대해서만 액세스를 사용할 수 있습니다. 이제 저장소 계정을 사용 하 여 Azure File storage 및 Azure Blob 저장소에 대 한 개인 끝점을 만듭니다.

1. 새 저장소 계정의 왼쪽 메뉴에서 **네트워킹** 을 선택 합니다.

1. **개인 끝점 연결** 탭을 선택 하 고 **개인 끝점** 을 선택 합니다.

    :::image type="content" source="./media/functions-create-vnet/2-navigate-private-endpoint-store.png" alt-text="저장소 계정에 대 한 개인 끝점을 만들기 위해 탐색 하는 방법의 스크린샷":::

1. **기본** 탭에서 아래 지정 된 대로 개인 끝점 설정을 사용 합니다.

    | 설정      | 제안 값  | 설명      |
    | ------------ | ---------------- | ---------------- |
    | **구독** | 사용자의 구독 | 리소스가 만들어지는 구독입니다. | 
    | **[리소스 그룹](../azure-resource-manager/management/overview.md)**  | myResourceGroup | 함수 앱을 사용 하 여 만든 리소스 그룹을 선택 합니다. | |
    | **이름** | 파일-끝점 | 저장소 계정의 파일에 대 한 개인 끝점의 이름입니다. |
    | **[지역](https://azure.microsoft.com/regions/)** | myFunctionRegion | 저장소 계정을 만든 지역을 선택 합니다. |

1. **리소스** 탭에서 아래 지정 된 대로 개인 끝점 설정을 사용 합니다.

    | 설정      | 제안 값  | 설명      |
    | ------------ | ---------------- | ---------------- |
    | **구독** | 사용자의 구독 | 리소스가 만들어지는 구독입니다. | 
    | **리소스 종류**  | Microsoft.Storage/storageAccounts | 저장소 계정에 대 한 리소스 유형입니다. |
    | **리소스** | mysecurestorage | 방금 만든 저장소 계정 |
    | **대상 하위 리소스** | 파일 | 이 개인 끝점은 저장소 계정의 파일에 사용 됩니다. |

1. **구성** 탭에서 서브넷 설정에 대해 **기본값** 을 선택 합니다.

1. **검토 + 만들기** 를 선택합니다. 유효성 검사가 완료되면 **만들기** 를 선택합니다. 가상 네트워크의 리소스는 이제 저장소 파일과 통신할 수 있습니다.

1. Blob에 대 한 다른 개인 끝점을 만듭니다. **리소스** 탭에서 아래 설정을 사용 합니다. 다른 모든 설정의 경우 방금 수행한 파일 개인 끝점 만들기 단계에서와 동일한 설정을 사용 합니다.

    | 설정      | 제안 값  | 설명      |
    | ------------ | ---------------- | ---------------- |
    | **구독** | 사용자의 구독 | 리소스가 만들어지는 구독입니다. | 
    | **리소스 종류**  | Microsoft.Storage/storageAccounts | 저장소 계정에 대 한 리소스 유형입니다. |
    | **리소스** | mysecurestorage | 방금 만든 저장소 계정 |
    | **대상 하위 리소스** | blob | 이 개인 끝점은 저장소 계정의 blob에 사용 됩니다. |

## <a name="lock-down-your-service-bus-with-a-private-endpoint"></a>개인 끝점을 사용 하 여 서비스 버스 잠그기

이제 Azure Service Bus에 대 한 개인 끝점을 만듭니다.

1. 새 service bus의 왼쪽 메뉴에서 **네트워킹** 을 선택 합니다.

1. **개인 끝점 연결** 탭을 선택 하 고 **개인 끝점** 을 선택 합니다.

    :::image type="content" source="./media/functions-create-vnet/3-navigate-private-endpoint-service-bus.png" alt-text="Service bus에 대 한 개인 끝점으로 이동 하는 방법의 스크린샷":::

1. **기본** 탭에서 아래 지정 된 대로 개인 끝점 설정을 사용 합니다.

    | 설정      | 제안 값  | 설명      |
    | ------------ | ---------------- | ---------------- |
    | **구독** | 사용자의 구독 | 리소스가 만들어지는 구독입니다. | 
    | **[리소스 그룹](../azure-resource-manager/management/overview.md)**  | myResourceGroup | 함수 앱을 사용 하 여 만든 리소스 그룹을 선택 합니다. |
    | **이름** | sb-끝점 | 저장소 계정의 파일에 대 한 개인 끝점의 이름입니다. |
    | **[지역](https://azure.microsoft.com/regions/)** | myFunctionRegion | 저장소 계정을 만든 지역을 선택 합니다. |

1. **리소스** 탭에서 아래 지정 된 대로 개인 끝점 설정을 사용 합니다.

    | 설정      | 제안 값  | 설명      |
    | ------------ | ---------------- | ---------------- |
    | **구독** | 사용자의 구독 | 리소스가 만들어지는 구독입니다. | 
    | **리소스 종류**  | Microsoft.ServiceBus/namespaces | Service Bus에 대 한 리소스 형식입니다. |
    | **리소스** | myServiceBus | 자습서의 앞부분에서 만든 Service Bus입니다. |
    | **대상 하위 리소스** | 네임스페이스 | 이 개인 끝점은 service bus의 네임 스페이스에 사용 됩니다. |

1. **구성** 탭에서 서브넷 설정에 대해 **기본값** 을 선택 합니다.

1. **검토 + 만들기** 를 선택합니다. 유효성 검사가 완료되면 **만들기** 를 선택합니다. 가상 네트워크의 리소스는 이제 service bus와 통신할 수 있습니다.

## <a name="create-a-file-share"></a>파일 공유 만들기

1. 만든 저장소 계정에서 왼쪽 메뉴에 있는 **파일 공유** 를 선택 합니다.

1. **+ 파일 공유** 를 선택 합니다. 이 자습서의 목적을 위해 파일을 파일 공유의 이름 **으로 제공 합니다** .

    :::image type="content" source="./media/functions-create-vnet/4-create-file-share.png" alt-text="저장소 계정에서 파일 공유를 만드는 방법의 스크린샷":::

## <a name="get-storage-account-connection-string"></a>저장소 계정 연결 문자열 가져오기

1. 만든 저장소 계정에서 왼쪽 메뉴의 **액세스 키** 를 선택 합니다.

1. **키 표시** 를 선택 합니다. Key1의 연결 문자열을 복사 하 고 저장 합니다. 앱 설정을 구성 하는 경우 나중에이 연결 문자열이 필요 합니다.

    :::image type="content" source="./media/functions-create-vnet/5-get-store-connection-string.png" alt-text="저장소 계정 연결 문자열을 가져오는 방법의 스크린샷":::

## <a name="create-a-queue"></a>큐 만들기

이는 Azure Functions Service Bus 트리거가 이벤트를 가져올 큐입니다.

1. Service bus의 왼쪽 메뉴에서 **큐** 를 선택 합니다.

1. **공유 액세스 정책** 을 선택합니다. 이 자습서의 목적에 따라 큐를 큐의 이름 **으로 제공 합니다** .

    :::image type="content" source="./media/functions-create-vnet/6-create-queue.png" alt-text="Service bus 큐를 만드는 방법의 스크린샷":::

## <a name="get-service-bus-connection-string"></a>Service bus 연결 문자열 가져오기

1. Service bus의 왼쪽 메뉴에서 **공유 액세스 정책** 을 선택 합니다.

1. **RootManageSharedAccessKey** 를 선택합니다. **기본 연결 문자열** 을 복사 하 고 저장 합니다. 앱 설정을 구성 하는 경우 나중에이 연결 문자열이 필요 합니다.

    :::image type="content" source="./media/functions-create-vnet/7-get-service-bus-connection-string.png" alt-text="Service bus 연결 문자열을 가져오는 방법의 스크린샷":::

## <a name="integrate-function-app-with-your-virtual-network"></a>가상 네트워크에 함수 앱 통합

가상 네트워크에서 함수 앱을 사용 하려면 서브넷에 조인 해야 합니다. 이 자습서에서 만든 다른 모든 개인 끝점에 대 한 Azure Functions 가상 네트워크 통합 및 기본 하위 네트워크에 대 한 특정 서브넷을 사용 합니다.

1. 함수 앱의 왼쪽 메뉴에서 **네트워킹** 을 선택 합니다.

1. VNet 통합 아래에서 **구성 하려면 여기를 클릭** 하세요 .를 선택 합니다.

    :::image type="content" source="./media/functions-create-vnet/8-connect-app-vnet.png" alt-text="가상 네트워크 통합을 탐색 하는 방법의 스크린샷":::

1. **VNet 추가** 를 선택 합니다.

1. **Virtual Network** 에서 열리는 블레이드에서 이전에 만든 가상 네트워크를 선택 합니다.

1. 이전에 호출한 **함수** 에서 만든 **서브넷** 을 선택 합니다. 이제 함수 앱이 가상 네트워크와 통합 되었습니다.

    :::image type="content" source="./media/functions-create-vnet/9-connect-app-subnet.png" alt-text="서브넷에 함수 앱을 연결 하는 방법의 스크린샷":::

## <a name="configure-your-function-app-settings-for-private-endpoints"></a>전용 끝점에 대 한 함수 앱 설정 구성

1. 함수 앱의 왼쪽 메뉴에서 **구성** 을 선택 합니다.

1. 가상 네트워크에서 함수 앱을 사용 하려면 다음 앱 설정을 업데이트 해야 합니다. 적절 한 앱 설정 테이블의 맨 오른쪽 열에서 **+ 새 응용 프로그램 설정** 또는 연필 **편집** 을 선택 합니다. 완료되면 **저장** 을 선택합니다.

    :::image type="content" source="./media/functions-create-vnet/10-configure-app-settings.png" alt-text="전용 끝점에 대 한 함수 앱 설정을 구성 하는 방법의 스크린샷":::

    | 설정      | 제안 값  | 설명      |
    | ------------ | ---------------- | ---------------- |
    | **AzureWebJobsStorage** | mysecurestorageConnectionString | 만든 저장소 계정의 연결 문자열입니다. [저장소 계정 연결 문자열 가져오기](#get-storage-account-connection-string)의 저장소 연결 문자열입니다. 이 설정을 변경 하면 함수 앱은 런타임에 일반 작업에 보안 저장소 계정을 사용 합니다. | 
    | **WEBSITE_CONTENTAZUREFILECONNECTIONSTRING**  | mysecurestorageConnectionString | 만든 저장소 계정의 연결 문자열입니다. 이 설정을 변경 하면 함수 앱은를 배포할 때 사용 되는 Azure Files에 대 한 보안 저장소 계정을 사용 합니다. |
    | **WEBSITE_CONTENTSHARE** | 파일 | 저장소 계정에서 만든 파일 공유의 이름입니다. 이 앱 설정은 WEBSITE_CONTENTAZUREFILECONNECTIONSTRING와 함께 사용 됩니다. |
    | **SERVICEBUS_CONNECTION** | myServiceBusConnectionString | Service bus의 연결 문자열에 대 한 앱 설정을 만듭니다. [Get service bus 연결 문자열](#get-service-bus-connection-string)의 저장소 연결 문자열입니다.|
    | **WEBSITE_CONTENTOVERVNET** | 1 | 이 앱 설정을 만듭니다. 값 1은 저장소 계정이 가상 네트워크로 제한 된 경우 함수 앱의 크기를 조정할 수 있도록 합니다. 저장소 계정을 가상 네트워크로 제한할 때이 설정을 사용 하도록 설정 해야 합니다. |
    | **WEBSITE_DNS_SERVER** | 168.63.129.16 | 이 앱 설정을 만듭니다. 앱이 가상 네트워크와 통합 되 면 가상 네트워크와 동일한 DNS 서버를 사용 하 게 됩니다. 이는 함수 앱이 Azure DNS 전용 영역에서 작동 하 고 개인 끝점을 사용 하는 데 필요한 두 설정 중 하나입니다. 이러한 설정은 앱의 모든 아웃 바운드 호출을 가상 네트워크로 보냅니다. |
    | **WEBSITE_VNET_ROUTE_ALL** | 1 | 이 앱 설정을 만듭니다. 앱이 가상 네트워크와 통합 되 면 가상 네트워크와 동일한 DNS 서버를 사용 하 게 됩니다. 이는 함수 앱이 Azure DNS 전용 영역에서 작동 하 고 개인 끝점을 사용 하는 데 필요한 두 설정 중 하나입니다. 이러한 설정은 앱의 모든 아웃 바운드 호출을 가상 네트워크로 보냅니다. |

1. **구성** 뷰를 그대로 유지 하 고 **함수 런타임 설정** 탭을 선택 합니다.

1. **런타임 규모 모니터링** **을 설정으로 설정** 하 고 **저장** 을 선택 합니다. 런타임 기반 크기 조정을 사용 하면 HTTP가 아닌 트리거 함수를 가상 네트워크 내에서 실행 되는 서비스에 연결할 수 있습니다.

    :::image type="content" source="./media/functions-create-vnet/11-enable-runtime-scaling.png" alt-text="Azure Functions에 대 한 런타임 기반 크기 조정을 사용 하도록 설정 하는 방법의 스크린샷":::

## <a name="deploy-a-service-bus-trigger-and-http-trigger-to-your-function-app"></a>Service bus 트리거 및 http 트리거를 함수 앱에 배포

1. GitHub에서 두 개의 함수, 즉 HTTP 트리거와 Service Bus 큐 트리거를 사용 하는 함수 앱을 포함 하는 다음 샘플 리포지토리로 이동 합니다.

    <https://github.com/Azure-Samples/functions-vnet-tutorial>

1. 페이지 위쪽에서 **포크** 단추를 선택 하 여 사용자 고유의 GitHub 계정 또는 조직에서이 리포지토리의 포크를 만듭니다.

1. 함수 앱의 왼쪽 메뉴에서 **Deployment Center** 를 선택 합니다. 그런 다음 **설정** 을 선택 합니다.

1. **설정** 탭에서 아래 지정 된 대로 배포 설정을 사용 합니다.

    | 설정      | 제안 값  | 설명      |
    | ------------ | ---------------- | ---------------- |
    | **원본** | GitHub | 2 단계에서 샘플 코드를 사용 하 여 GitHub 리포지토리를 만들어야 합니다. | 
    | **조직**  | myOrganization | 리포지토리를 체크 인 하는 조직입니다. 일반적으로 사용자의 계정입니다. |
    | **리포지토리** | myRepo | 예제 코드를 사용 하 여 만든 리포지토리입니다. |
    | **분기** | main | 이 리포지토리는 방금 만든 리포지토리입니다. 따라서 main 분기를 사용 합니다. |
    | **런타임 스택** | .NET | 샘플 코드는 c #으로 되어 있습니다. |

1. **저장** 을 선택합니다. 

    :::image type="content" source="./media/functions-create-vnet/12-deploy-portal.png" alt-text="포털을 통해 Azure Functions 코드를 배포 하는 방법의 스크린샷":::

1. 초기 배포에는 몇 분 정도 걸릴 수 있습니다. 앱이 성공적으로 배포 되 면 **로그** 탭에 **성공 (활성)** 상태 메시지가 표시 됩니다. 필요한 경우 페이지를 새로 고칩니다. 

1. 축하합니다! 샘플 함수 앱을 성공적으로 배포 했습니다.

## <a name="lock-down-your-function-app-with-a-private-endpoint"></a>전용 끝점을 사용 하 여 함수 앱 잠금

이제 함수 앱에 대 한 개인 끝점을 만듭니다. 이 개인 끝점은 개인 IP 주소를 사용 하 여 가상 네트워크에 안전 하 게 안전 하 게 함수 앱을 연결 합니다. 전용 끝점에 대 한 자세한 내용은 [개인 끝점 설명서](https://docs.microsoft.com/azure/private-link/private-endpoint-overview)를 참조 하세요.

1. 함수 앱의 왼쪽 메뉴에서 **네트워킹** 을 선택 합니다.

1. 개인 끝점 연결 아래에서 **구성 하려면 여기를 클릭** 하세요 .를 선택 합니다.

    :::image type="content" source="./media/functions-create-vnet/14-navigate-app-private-endpoint.png" alt-text="함수 앱 개인 끝점으로 이동 하는 방법의 스크린샷":::

1. **추가** 를 선택합니다.

1. 열리는 메뉴에서 아래 지정 된 대로 개인 끝점 설정을 사용 합니다.

    :::image type="content" source="./media/functions-create-vnet/15-create-app-private-endpoint.png" alt-text="함수 앱 개인 끝점을 만드는 방법의 스크린샷":::

1. **확인** 을 선택 하 여 개인 끝점을 추가 합니다. 축하합니다! 전용 끝점을 사용 하 여 함수 앱, service bus 및 storage 계정을 성공적으로 보호 했습니다.

### <a name="test-your-locked-down-function-app"></a>잠긴 함수 앱 테스트

1. 함수 앱의 왼쪽 메뉴에서 **함수** 를 선택 합니다.

1. **ServiceBusQueueTrigger** 를 선택 합니다.

1. 왼쪽 메뉴에서 **모니터** 를 선택 합니다. 앱을 모니터링할 수 없습니다. 이는 브라우저가 가상 네트워크에 액세스할 수 없으므로 가상 네트워크 내의 리소스에 직접 액세스할 수 없기 때문입니다. 이제 함수를 Application Insights 모니터링할 수 있는 다른 방법을 보여 드리겠습니다.

1. 함수 앱의 왼쪽 메뉴에서 **Application Insights** 를 선택 하 고 **Application Insights 데이터 보기** 를 선택 합니다.

    :::image type="content" source="./media/functions-create-vnet/16-app-insights.png" alt-text="함수 앱에 대 한 application insights를 보는 방법의 스크린샷":::

1. 왼쪽 메뉴에서 **라이브 메트릭** 을 선택 합니다.

1. 새 탭을 엽니다. Service Bus의 왼쪽 메뉴에서 **큐** 를 선택 합니다.

1. 큐를 선택 합니다.

1. 왼쪽 메뉴에서 **Service Bus 탐색기** 를 선택 합니다. **보내기** 아래에서 **내용 유형** 으로 **텍스트/일반** 을 선택 하 고 메시지를 입력 합니다. 

1. **보내기** 를 선택 하 여 메시지를 보냅니다.

    :::image type="content" source="./media/functions-create-vnet/17-send-service-bus-message.png" alt-text="포털을 사용 하 여 Service Bus 메시지를 보내는 방법의 스크린샷":::

1. **라이브 메트릭이** 있는 탭에서 Service Bus 큐 트리거가 트리거됨을 확인 해야 합니다. 그렇지 않은 경우 **Service Bus 탐색기** 에서 메시지를 다시 보냅니다.

    :::image type="content" source="./media/functions-create-vnet/18-hello-world.png" alt-text="함수 앱에 대 한 라이브 메트릭을 사용 하 여 메시지를 보는 방법의 스크린샷":::

1. 축하합니다! 전용 끝점을 사용 하 여 함수 앱 설정을 테스트 했습니다.

### <a name="private-dns-zones"></a>프라이빗 DNS 영역
개인 끝점을 사용 하 여 Azure 리소스에 연결 하는 것은 공용 끝점 대신 개인 IP 주소에 연결 하는 것을 의미 합니다. 기존 Azure 서비스는 기존 DNS를 사용 하 여 공용 끝점에 연결 하도록 구성 됩니다. DNS 구성을 재정의 하 여 개인 끝점에 연결 해야 합니다.

개인 끝점으로 구성 된 각 Azure 리소스에 대해 개인 DNS 영역을 만들었습니다. DNS A 레코드는 개인 끝점과 연결 된 각 개인 IP 주소에 대해 만들어집니다.

이 자습서에서는 다음 DNS 영역을 만들었습니다.

- privatelink.file.core.windows.net
- privatelink.blob.core.windows.net
- privatelink.servicebus.windows.net
- privatelink.azurewebsites.net

[!INCLUDE [clean-up-section-portal](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>다음 단계

이 자습서에서는 프리미엄 함수 앱, 저장소 계정 및 Service Bus을 만들었으며 개인 끝점 뒤에 모두 보안을 설정 했습니다. 아래에서 제공 하는 다양 한 네트워킹 기능에 대해 자세히 알아보세요.

> [!div class="nextstepaction"]
> [Functions 네트워킹 옵션에 대한 자세한 정보](./functions-networking-options.md)

[프리미엄 계획]: functions-premium-plan.md
