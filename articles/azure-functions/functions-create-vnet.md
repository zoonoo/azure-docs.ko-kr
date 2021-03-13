---
title: 사설 끝점을 사용 하 여 가상 네트워크와 Azure Functions 통합
description: 이 자습서에서는 Azure 가상 네트워크에 함수를 연결 하 고 개인 끝점을 사용 하 여 잠그는 방법을 보여 줍니다.
ms.topic: article
ms.date: 2/22/2021
ms.openlocfilehash: 3dd5e700b3081f1c1ef8e4601385c707a5738321
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/11/2021
ms.locfileid: "102630472"
---
# <a name="tutorial-integrate-azure-functions-with-an-azure-virtual-network-by-using-private-endpoints"></a>자습서: 개인 끝점을 사용 하 여 Azure 가상 네트워크와 Azure Functions 통합

이 자습서에서는 Azure Functions를 사용 하 여 개인 끝점을 사용 하 여 Azure 가상 네트워크의 리소스에 연결 하는 방법을 보여 줍니다. 가상 네트워크 뒤에 잠긴 저장소 계정을 사용 하 여 함수를 만듭니다. 가상 네트워크에서 service bus 큐 트리거를 사용 합니다.

이 자습서에서는 다음을 수행합니다.

> [!div class="checklist"]
> * 프리미엄 계획에서 함수 앱을 만듭니다.
> * Service bus, 저장소 계정 및 가상 네트워크와 같은 Azure 리소스를 만듭니다.
> * 개인 끝점 뒤에서 저장소 계정을 잠급니다.
> * 개인 끝점 뒤에서 service bus를 잠급니다.
> * Service bus 및 HTTP 트리거를 모두 사용 하는 함수 앱을 배포 합니다.
> * 전용 끝점 뒤에서 함수 앱을 잠급니다.
> * 함수 앱이 가상 네트워크 내에서 안전 하 게 보호 되는지 테스트 합니다.
> * 리소스를 정리합니다.

## <a name="create-a-function-app-in-a-premium-plan"></a>프리미엄 계획에서 함수 앱 만들기

이 자습서에서는 c #을 사용 하기 때문에 프리미엄 계획에서 .NET 함수 앱을 만듭니다. 다른 언어도 Windows 에서도 지원 됩니다. 프리미엄 요금제는 가상 네트워크 통합을 지 원하는 동시에 서버를 사용 하지 않는 규모를 제공 합니다.

1. Azure Portal 메뉴나 **홈** 페이지에서 **리소스 만들기** 를 선택 합니다.

1. **새로 만들기** 페이지에서 **Compute**  >  **함수 앱** 를 선택 합니다.

1. **기본 사항** 페이지에서 다음 표를 사용 하 여 함수 앱 설정을 구성 합니다.

    | 설정      | 제안 값  | 설명 |
    | ------------ | ---------------- | ----------- |
    | **구독** | 사용자의 구독 | 이 새 함수 앱이 생성 되는 구독입니다. |
    | **[리소스 그룹](../azure-resource-manager/management/overview.md)** |  myResourceGroup | 함수 앱을 만들 새 리소스 그룹의 이름입니다. |
    | **함수 앱 이름** | 전역적으로 고유한 이름 | 새 함수 앱을 식별하는 이름입니다. 유효한 문자는 `a-z`(대/소문자 구분 안 함), `0-9`및 `-`입니다.  |
    |**게시**| 코드 | 코드 파일 또는 Docker 컨테이너를 게시 하도록 선택 합니다. |
    | **런타임 스택** | .NET | 이 자습서에서는 .NET을 사용 합니다. |
    |**지역**| 기본 지역 | 사용자 또는 함수에서 액세스 하는 다른 서비스 근처의 [지역을](https://azure.microsoft.com/regions/) 선택 합니다. |

1. 완료되면 **다음: 호스팅** 을 선택합니다. **호스팅** 페이지에서 다음 설정을 입력합니다.

    | 설정      | 제안 값  | Description |
    | ------------ | ---------------- | ----------- |
    | **[Storage 계정](../storage/common/storage-account-create.md)** |  전역적으로 고유한 이름 |  함수 앱에서 사용하는 스토리지 계정을 만듭니다. Storage 계정 이름은 3 자에서 24 자 사이 여야 합니다. 숫자 및 소문자만 포함할 수 있습니다. 기존 계정을 사용할 수도 있습니다. 여기서는 [스토리지 계정 요구 사항](./storage-considerations.md#storage-account-requirements)을 충족해야 합니다. |
    |**운영 체제**| Windows | 이 자습서에서는 Windows를 사용 합니다. |
    | **[계획](./functions-scale.md)** | Premium | 함수 앱에 리소스가 할당되는 방법을 정의하는 호스팅 계획입니다. 기본적으로 **Premium** 을 선택 하면 새 App Service 계획이 만들어집니다. 기본 **Sku와 크기** 는 **EP1** 입니다. 여기서 *EP* 는 _탄력적 프리미엄_ 을 의미 합니다. 자세한 내용은 [프리미엄 sku](./functions-premium-plan.md#available-instance-skus)목록을 참조 하세요.<br/><br/>프리미엄 계획에서 JavaScript 함수를 실행 하는 경우 vCPUs 수가 작은 인스턴스를 선택 합니다. 자세한 내용은 [단일 코어 프리미엄 계획 선택](./functions-reference-node.md#considerations-for-javascript-functions)을 참조하세요.  |

1. 완료되면 **다음: 모니터링** 을 선택합니다. **모니터링** 페이지에서 다음 설정을 입력합니다.

    | 설정      | 제안 값  | Description |
    | ------------ | ---------------- | ----------- |
    | **[Application Insights](./functions-monitoring.md)** | 기본값 | 지원 되는 가장 가까운 지역에 동일한 앱 이름의 Application Insights 리소스를 만듭니다. **새 리소스 이름을** 변경 하거나 [Azure Geography](https://azure.microsoft.com/global-infrastructure/geographies/)의 다른 **위치** 에 데이터를 저장 해야 하는 경우이 설정을 확장 합니다. |

1. **검토 + 만들기** 를 선택하여 앱 구성 선택을 검토합니다.

1. **검토 + 만들기** 페이지에서 설정을 검토 합니다. 그런 다음 **만들기** 를 선택 하 여 함수 앱을 프로 비전 하 고 배포 합니다.

1. 포털의 오른쪽 위 모서리에서 **알림** 아이콘을 선택 하 고 **배포 성공** 메시지를 감시 합니다.

1. **리소스로 이동** 을 선택하여 함수 앱을 봅니다. **대시보드에 고정** 을 선택할 수도 있습니다. 고정하면 대시보드에서 이 함수 앱 리소스로 쉽게 돌아올 수 있습니다.

축하합니다! 프리미엄 함수 앱을 만들었습니다.

## <a name="create-azure-resources"></a>Azure 리소스 만들기

그런 다음 저장소 계정, service bus 및 가상 네트워크를 만듭니다. 
### <a name="create-a-storage-account"></a>스토리지 계정 만들기

가상 네트워크에는 함수 앱을 사용 하 여 만든 것과는 다른 저장소 계정이 필요 합니다.

1. Azure Portal 메뉴나 **홈** 페이지에서 **리소스 만들기** 를 선택 합니다.

1. **새로 만들기** 페이지에서 *저장소 계정* 을 검색 합니다. 그런 다음 **만들기** 를 선택합니다.

1. **기본 사항** 탭에서 다음 표를 사용 하 여 저장소 계정 설정을 구성 합니다. 다른 모든 설정에는 기본값을 사용할 수 있습니다.

    | 설정      | 제안 값  | 설명      |
    | ------------ | ---------------- | ---------------- |
    | **구독** | 사용자의 구독 | 리소스가 만들어지는 구독입니다. | 
    | **[리소스 그룹](../azure-resource-manager/management/overview.md)**  | myResourceGroup | 함수 앱을 사용 하 여 만든 리소스 그룹입니다. |
    | **이름** | mysecurestorage| 개인 끝점이 적용 될 저장소 계정의 이름입니다. |
    | **[지역](https://azure.microsoft.com/regions/)** | myFunctionRegion | 함수 앱을 만든 지역입니다. |

1. **검토 + 만들기** 를 선택합니다. 유효성 검사를 완료 한 후 **만들기** 를 선택 합니다.

### <a name="create-a-service-bus"></a>Service bus 만들기

1. Azure Portal 메뉴나 **홈** 페이지에서 **리소스 만들기** 를 선택 합니다.

1. **새로 만들기** 페이지에서 *service bus* 를 검색 합니다. 그런 다음 **만들기** 를 선택합니다.

1. **기본 정보** 탭에서 다음 표를 사용 하 여 service bus 설정을 구성 합니다. 다른 모든 설정에는 기본값을 사용할 수 있습니다.

    | 설정      | 제안 값  | 설명      |
    | ------------ | ---------------- | ---------------- |
    | **구독** | 사용자의 구독 | 리소스가 만들어지는 구독입니다. |
    | **[리소스 그룹](../azure-resource-manager/management/overview.md)**  | myResourceGroup | 함수 앱을 사용 하 여 만든 리소스 그룹입니다. |
    | **이름** | myServiceBus| 개인 끝점이 적용 될 service bus의 이름입니다. |
    | **[지역](https://azure.microsoft.com/regions/)** | myFunctionRegion | 함수 앱을 만든 지역입니다. |
    | **가격 책정 계층** | Premium | Azure Service Bus에서 개인 끝점을 사용 하려면이 계층을 선택 합니다. |

1. **검토 + 만들기** 를 선택합니다. 유효성 검사를 완료 한 후 **만들기** 를 선택 합니다.

### <a name="create-a-virtual-network"></a>가상 네트워크 만들기

이 자습서의 Azure 리소스는와 통합 되거나 가상 네트워크 내에 배치 됩니다. 가상 네트워크 내에 네트워크 트래픽을 포함 하기 위해 개인 끝점을 사용 합니다.

이 자습서에서는 두 개의 서브넷을 만듭니다.
- **기본값**: 개인 끝점의 서브넷입니다. 개인 IP 주소는이 서브넷에서 제공 됩니다.
- **함수**: Azure Functions 가상 네트워크 통합에 대 한 서브넷입니다. 이 서브넷은 함수 앱에 위임 됩니다.

함수 앱이 통합 되는 가상 네트워크를 만듭니다.

1. Azure Portal 메뉴나 **홈** 페이지에서 **리소스 만들기** 를 선택 합니다.

1. **새로 만들기** 페이지에서 *가상 네트워크* 를 검색 합니다. 그런 다음 **만들기** 를 선택합니다.

1. **기본 사항** 탭에서 다음 표를 사용 하 여 가상 네트워크 설정을 구성 합니다.

    | 설정      | 제안 값  | 설명      |
    | ------------ | ---------------- | ---------------- |
    | **구독** | 사용자의 구독 | 리소스가 만들어지는 구독입니다. | 
    | **[리소스 그룹](../azure-resource-manager/management/overview.md)**  | myResourceGroup | 함수 앱을 사용 하 여 만든 리소스 그룹입니다. |
    | **이름** | myVirtualNet| 함수 앱이 연결 되는 가상 네트워크의 이름입니다. |
    | **[지역](https://azure.microsoft.com/regions/)** | myFunctionRegion | 함수 앱을 만든 지역입니다. |

1. **IP 주소** 탭에서 **서브넷 추가** 를 선택 합니다. 다음 표를 사용 하 여 서브넷 설정을 구성 합니다.

    :::image type="content" source="./media/functions-create-vnet/1-create-vnet-ip-address.png" alt-text="가상 네트워크 구성 보기 만들기의 스크린샷":::

    | 설정      | 제안 값  | 설명      |
    | ------------ | ---------------- | ---------------- |
    | **서브넷 이름** | functions | 함수 앱이 연결 될 서브넷의 이름입니다. | 
    | **서브넷 주소 범위** | 10.0.1.0/24 | 서브넷 주소 범위입니다. 위의 그림에서 IPv4 주소 공간은 10.0.0.0/16입니다. 값이 10.1.0.0/16 인 경우 권장 서브넷 주소 범위는 10.1.1.0/24입니다. |

1. **검토 + 만들기** 를 선택합니다. 유효성 검사를 완료 한 후 **만들기** 를 선택 합니다.

## <a name="lock-down-your-storage-account"></a>저장소 계정 잠금

Azure 개인 끝점은 개인 IP 주소를 사용 하 여 특정 Azure 리소스에 연결 하는 데 사용 됩니다. 이렇게 연결 하면 선택한 가상 네트워크 내에서 네트워크 트래픽이 유지 되 고 특정 리소스에 대해서만 액세스를 사용할 수 있습니다. 

저장소 계정을 사용 하 여 Azure Files 저장소 및 Azure Blob Storage에 대 한 개인 끝점을 만듭니다.

1. 새 저장소 계정의 왼쪽에 있는 메뉴에서 **네트워킹** 을 선택 합니다.

1. **개인 끝점 연결** 탭에서 **개인 끝점** 을 선택 합니다.

    :::image type="content" source="./media/functions-create-vnet/2-navigate-private-endpoint-store.png" alt-text="저장소 계정에 대 한 개인 끝점을 만드는 방법의 스크린샷":::

1. **기본 사항** 탭에서 다음 표에 나와 있는 개인 끝점 설정을 사용 합니다.

    | 설정      | 제안 값  | 설명      |
    | ------------ | ---------------- | ---------------- |
    | **구독** | 사용자의 구독 | 리소스가 만들어지는 구독입니다. | 
    | **[리소스 그룹](../azure-resource-manager/management/overview.md)**  | myResourceGroup | 함수 앱을 사용 하 여 만든 리소스 그룹을 선택 합니다. | |
    | **이름** | 파일-끝점 | 저장소 계정의 파일에 대 한 개인 끝점의 이름입니다. |
    | **[지역](https://azure.microsoft.com/regions/)** | myFunctionRegion | 저장소 계정을 만든 지역을 선택 합니다. |

1. **리소스** 탭에서 다음 표에 나와 있는 개인 끝점 설정을 사용 합니다.

    | 설정      | 제안 값  | 설명      |
    | ------------ | ---------------- | ---------------- |
    | **구독** | 사용자의 구독 | 리소스가 만들어지는 구독입니다. | 
    | **리소스 종류**  | Microsoft.Storage/storageAccounts | 저장소 계정에 대 한 리소스 유형입니다. |
    | **리소스** | mysecurestorage | 만든 저장소 계정입니다. |
    | **대상 하위 리소스** | 파일 | 저장소 계정의 파일에 사용 될 개인 끝점입니다. |

1. **구성** 탭의 **서브넷** 설정에서 **기본값** 을 선택 합니다.

1. **검토 + 만들기** 를 선택합니다. 유효성 검사를 완료 한 후 **만들기** 를 선택 합니다. 가상 네트워크의 리소스는 이제 저장소 파일과 통신할 수 있습니다.

1. Blob에 대 한 다른 개인 끝점을 만듭니다. **리소스** 탭에서 다음 표에 나와 있는 설정을 사용 합니다. 다른 모든 설정의 경우에는 파일에 대 한 개인 끝점을 만드는 데 사용한 것과 동일한 값을 사용 합니다.

    | 설정      | 제안 값  | 설명      |
    | ------------ | ---------------- | ---------------- |
    | **구독** | 사용자의 구독 | 리소스가 만들어지는 구독입니다. | 
    | **리소스 종류**  | Microsoft.Storage/storageAccounts | 저장소 계정에 대 한 리소스 유형입니다. |
    | **리소스** | mysecurestorage | 만든 저장소 계정입니다. |
    | **대상 하위 리소스** | blob | 저장소 계정의 blob에 사용 될 개인 끝점입니다. |

## <a name="lock-down-your-service-bus"></a>Service bus 잠금

Service bus를 잠글 개인 끝점을 만듭니다.

1. 새 service bus의 왼쪽에 있는 메뉴에서 **네트워킹** 을 선택 합니다.

1. **개인 끝점 연결** 탭에서 **개인 끝점** 을 선택 합니다.

    :::image type="content" source="./media/functions-create-vnet/3-navigate-private-endpoint-service-bus.png" alt-text="Service bus에 대 한 개인 끝점으로 이동 하는 방법의 스크린샷":::

1. **기본 사항** 탭에서 다음 표에 나와 있는 개인 끝점 설정을 사용 합니다.

    | 설정      | 제안 값  | 설명      |
    | ------------ | ---------------- | ---------------- |
    | **구독** | 사용자의 구독 | 리소스가 만들어지는 구독입니다. | 
    | **[리소스 그룹](../azure-resource-manager/management/overview.md)**  | myResourceGroup | 함수 앱을 사용 하 여 만든 리소스 그룹입니다. |
    | **이름** | sb-끝점 | 저장소 계정의 파일에 대 한 개인 끝점의 이름입니다. |
    | **[지역](https://azure.microsoft.com/regions/)** | myFunctionRegion | 저장소 계정을 만든 지역입니다. |

1. **리소스** 탭에서 다음 표에 나와 있는 개인 끝점 설정을 사용 합니다.

    | 설정      | 제안 값  | 설명      |
    | ------------ | ---------------- | ---------------- |
    | **구독** | 사용자의 구독 | 리소스가 만들어지는 구독입니다. | 
    | **리소스 종류**  | Microsoft.ServiceBus/namespaces | 서비스 버스에 대 한 리소스 형식입니다. |
    | **리소스** | myServiceBus | 자습서의 앞부분에서 만든 service bus입니다. |
    | **대상 하위 리소스** | namespace | Service bus의 네임 스페이스에 사용 될 개인 끝점입니다. |

1. **구성** 탭의 **서브넷** 설정에서 **기본값** 을 선택 합니다.

1. **검토 + 만들기** 를 선택합니다. 유효성 검사를 완료 한 후 **만들기** 를 선택 합니다. 

가상 네트워크의 리소스는 이제 service bus와 통신할 수 있습니다.

## <a name="create-a-file-share"></a>파일 공유 만들기

1. 만든 저장소 계정 왼쪽의 메뉴에서 **파일 공유** 를 선택 합니다.

1. **+ 파일 공유** 를 선택 합니다. 이 자습서의 목적을 위해 파일 이름을 공유 *파일* 로 합니다.

    :::image type="content" source="./media/functions-create-vnet/4-create-file-share.png" alt-text="저장소 계정에서 파일 공유를 만드는 방법의 스크린샷":::

1. **만들기** 를 선택합니다.

## <a name="get-the-storage-account-connection-string"></a>저장소 계정 연결 문자열을 가져옵니다.

1. 만든 저장소 계정 왼쪽의 메뉴에서 **액세스 키** 를 선택 합니다.

1. **키 표시** 를 선택 합니다. **Key1** 의 연결 문자열을 복사 하 여 저장 합니다. 앱 설정을 구성할 때이 연결 문자열이 필요 합니다.

    :::image type="content" source="./media/functions-create-vnet/5-get-store-connection-string.png" alt-text="저장소 계정 연결 문자열을 가져오는 방법의 스크린샷":::

## <a name="create-a-queue"></a>큐 만들기

Azure Functions service bus 트리거에서 이벤트를 가져오는 큐를 만듭니다.

1. Service bus의 왼쪽에 있는 메뉴에서 **큐** 를 선택 합니다.

1. **공유 액세스 정책** 을 선택합니다. 이 자습서에서는 목록 *큐* 의 이름을로 표시 합니다.

    :::image type="content" source="./media/functions-create-vnet/6-create-queue.png" alt-text="Service bus 큐를 만드는 방법의 스크린샷":::

1. **만들기** 를 선택합니다.

## <a name="get-a-service-bus-connection-string"></a>Service bus 연결 문자열 가져오기

1. Service bus의 왼쪽에 있는 메뉴에서 **공유 액세스 정책** 을 선택 합니다.

1. **RootManageSharedAccessKey** 를 선택합니다. **기본 연결 문자열** 을 복사 하 여 저장 합니다. 앱 설정을 구성할 때이 연결 문자열이 필요 합니다.

    :::image type="content" source="./media/functions-create-vnet/7-get-service-bus-connection-string.png" alt-text="Service bus 연결 문자열을 가져오는 방법의 스크린샷":::

## <a name="integrate-the-function-app"></a>함수 앱 통합

가상 네트워크에서 함수 앱을 사용 하려면 서브넷에 조인 해야 합니다. Azure Functions 가상 네트워크 통합에 대해 특정 서브넷을 사용 합니다. 이 자습서에서 만든 다른 개인 끝점에 대해서는 기본 서브넷을 사용 합니다.

1. 함수 앱의 왼쪽에 있는 메뉴에서 **네트워킹** 을 선택 합니다.

1. **VNet 통합** 에서 **구성하려면 클릭** 을 선택하세요.

    :::image type="content" source="./media/functions-create-vnet/8-connect-app-vnet.png" alt-text="가상 네트워크 통합으로 이동 하는 방법의 스크린샷":::

1. **VNet 추가** 를 선택합니다.

1. **Virtual Network** 에서 이전에 만든 가상 네트워크를 선택 합니다.

1. 앞에서 만든 **함수** 서브넷을 선택 합니다. 이제 함수 앱이 가상 네트워크와 통합 되었습니다.

    :::image type="content" source="./media/functions-create-vnet/9-connect-app-subnet.png" alt-text="서브넷에 함수 앱을 연결 하는 방법의 스크린샷":::

## <a name="configure-your-function-app-settings"></a>함수 앱 설정 구성

1. 함수 앱의 왼쪽에 있는 메뉴에서 **구성** 을 선택 합니다.

1. 가상 네트워크에서 함수 앱을 사용 하려면 다음 표에 표시 된 앱 설정을 업데이트 합니다. 설정을 추가 하거나 편집 하려면 앱 설정 테이블의 맨 오른쪽 열에서 **+ 새 응용 프로그램 설정** 또는 **편집** 아이콘을 선택 합니다. 완료되면 **저장** 을 선택합니다.

    :::image type="content" source="./media/functions-create-vnet/10-configure-app-settings.png" alt-text="전용 끝점에 대 한 함수 앱 설정을 구성 하는 방법의 스크린샷":::

    | 설정      | 제안 값  | 설명      |
    | ------------ | ---------------- | ---------------- |
    | **AzureWebJobsStorage** | mysecurestorageConnectionString | 만든 저장소 계정의 연결 문자열입니다. 이 저장소 연결 문자열은 [저장소 계정 연결 문자열 가져오기](#get-the-storage-account-connection-string) 섹션에서 가져온 것입니다. 이 설정을 통해 함수 앱은 런타임에 정상 작업에 보안 저장소 계정을 사용할 수 있습니다. | 
    | **WEBSITE_CONTENTAZUREFILECONNECTIONSTRING**  | mysecurestorageConnectionString | 만든 저장소 계정의 연결 문자열입니다. 이 설정을 사용 하면 함수 앱이 배포 중에 사용 되는 Azure Files에 대 한 보안 저장소 계정을 사용할 수 있습니다. |
    | **WEBSITE_CONTENTSHARE** | 파일 | 저장소 계정에서 만든 파일 공유의 이름입니다. 이 설정은 WEBSITE_CONTENTAZUREFILECONNECTIONSTRING와 함께 사용 합니다. |
    | **SERVICEBUS_CONNECTION** | myServiceBusConnectionString | Service bus의 연결 문자열에 대해이 앱 설정을 만듭니다. 이 저장소 연결 문자열은 [service bus 연결 문자열 가져오기](#get-a-service-bus-connection-string) 섹션에서 가져온 것입니다.|
    | **WEBSITE_CONTENTOVERVNET** | 1 | 이 앱 설정을 만듭니다. 값 1은 저장소 계정이 가상 네트워크로 제한 될 때 함수 앱의 크기를 조정할 수 있도록 합니다. |
    | **WEBSITE_DNS_SERVER** | 168.63.129.16 | 이 앱 설정을 만듭니다. 앱이 가상 네트워크와 통합 되는 경우 가상 네트워크와 동일한 DNS 서버를 사용 합니다. Azure DNS 전용 영역에서 사용할 수 있도록 함수 앱에이 설정이 필요 합니다. 개인 끝점을 사용 하는 경우 필요 합니다. 이 설정 및 WEBSITE_VNET_ROUTE_ALL는 앱의 모든 아웃 바운드 호출을 가상 네트워크로 보냅니다. |
    | **WEBSITE_VNET_ROUTE_ALL** | 1 | 이 앱 설정을 만듭니다. 앱이 가상 네트워크와 통합 되 면 가상 네트워크와 동일한 DNS 서버를 사용 합니다. Azure DNS 전용 영역에서 사용할 수 있도록 함수 앱에이 설정이 필요 합니다. 개인 끝점을 사용 하는 경우 필요 합니다. 이 설정 및 WEBSITE_DNS_SERVER는 앱의 모든 아웃 바운드 호출을 가상 네트워크로 보냅니다. |

1. **구성** 뷰에서 **함수 런타임 설정** 탭을 선택 합니다.

1. **런타임 규모 모니터링** 을 **On** 으로 설정 합니다. 그런 다음 **저장** 을 선택합니다. 런타임 기반 크기 조정을 사용 하면 가상 네트워크 내에서 실행 되는 서비스에 HTTP가 아닌 트리거 함수를 연결할 수 있습니다.

    :::image type="content" source="./media/functions-create-vnet/11-enable-runtime-scaling.png" alt-text="Azure Functions에 대 한 런타임 기반 크기 조정을 사용 하도록 설정 하는 방법의 스크린샷":::

## <a name="deploy-a-service-bus-trigger-and-http-trigger"></a>Service bus 트리거 및 HTTP 트리거 배포

1. GitHub에서 다음 샘플 리포지토리로 이동 합니다. 이 클래스에는 함수 앱과 두 개의 함수, 즉 HTTP 트리거와 service bus 큐 트리거가 포함 되어 있습니다.

    <https://github.com/Azure-Samples/functions-vnet-tutorial>

1. 페이지 맨 위에서 **포크** 를 선택 하 여 고유한 GitHub 계정 또는 조직에서이 리포지토리의 포크를 만듭니다.

1. 함수 앱의 왼쪽에 있는 메뉴에서 **Deployment Center** 를 선택 합니다. 그런 다음 **설정** 을 선택 합니다.

1. **설정** 탭에서 다음 표에 나와 있는 배포 설정을 사용 합니다.

    | 설정      | 제안 값  | 설명      |
    | ------------ | ---------------- | ---------------- |
    | **원본** | GitHub | 2 단계에서 샘플 코드에 대 한 GitHub 리포지토리를 만들어야 합니다. | 
    | **조직**  | myOrganization | 리포지토리가 체크 인 된 조직입니다. 일반적으로 사용자의 계정입니다. |
    | **리포지토리** | myRepo | 샘플 코드에 대해 만든 리포지토리입니다. |
    | **분기** | main | 만든 리포지토리의 주 분기입니다. |
    | **런타임 스택** | .NET | 샘플 코드는 c #으로 되어 있습니다. |

1. **저장** 을 선택합니다. 

    :::image type="content" source="./media/functions-create-vnet/12-deploy-portal.png" alt-text="포털을 통해 Azure Functions 코드를 배포 하는 방법의 스크린샷":::

1. 초기 배포에는 몇 분 정도 걸릴 수 있습니다. 앱이 성공적으로 배포 되 면 **로그** 탭에 **성공 (활성)** 상태 메시지가 표시 됩니다. 필요한 경우 페이지를 새로 고칩니다.

축하합니다! 샘플 함수 앱을 성공적으로 배포 했습니다.

## <a name="lock-down-your-function-app"></a>함수 앱 잠금

이제 함수 앱을 잠글 개인 끝점을 만듭니다. 이 개인 끝점은 개인 IP 주소를 사용 하 여 전용 및 안전 하 게 가상 네트워크에 함수 앱을 연결 합니다. 

자세한 내용은 [개인 끝점 설명서](https://docs.microsoft.com/azure/private-link/private-endpoint-overview)를 참조 하세요.

1. 함수 앱의 왼쪽에 있는 메뉴에서 **네트워킹** 을 선택 합니다.

1. **개인 끝점 연결** 에서 **개인 끝점 연결 구성** 을 선택 합니다.

    :::image type="content" source="./media/functions-create-vnet/14-navigate-app-private-endpoint.png" alt-text="함수 앱 전용 끝점으로 이동 하는 방법의 스크린샷":::

1. **추가** 를 선택합니다.

1. 열리는 창에서 다음 개인 끝점 설정을 사용 합니다.

    :::image type="content" source="./media/functions-create-vnet/15-create-app-private-endpoint.png" alt-text="함수 앱 전용 끝점을 만드는 방법의 스크린샷 Functionapp 끝점 이름입니다. 구독은 ' Private Test Sub CACHHAI '입니다. 가상 네트워크는 MyVirtualNet-자습서입니다. 서브넷은 기본값입니다.":::

1. **확인** 을 선택 하 여 개인 끝점을 추가 합니다. 
 
축하합니다! 전용 끝점을 추가 하 여 함수 앱, service bus 및 저장소 계정에 대 한 보안을 설정 했습니다.

### <a name="test-your-locked-down-function-app"></a>잠긴 함수 앱 테스트

1. 함수 앱의 왼쪽에 있는 메뉴에서 **함수** 를 선택 합니다.

1. **ServiceBusQueueTrigger** 를 선택 합니다.

1. 왼쪽 메뉴에서 **모니터** 를 선택합니다. 
 
앱을 모니터링할 수 없습니다. 브라우저는 가상 네트워크에 액세스할 수 없으므로 가상 네트워크 내의 리소스에 직접 액세스할 수 없습니다. 
 
Application Insights를 사용 하 여 함수를 모니터링 하는 다른 방법은 다음과 같습니다.

1. 함수 앱의 왼쪽에 있는 메뉴에서 **Application Insights** 를 선택 합니다. 그런 다음 **데이터 보기 Application Insights** 선택 합니다.

    :::image type="content" source="./media/functions-create-vnet/16-app-insights.png" alt-text="함수 앱에 대 한 application insights를 보는 방법의 스크린샷":::

1. 왼쪽 메뉴에서 **라이브 메트릭** 을 선택 합니다.

1. 새 탭을 엽니다. Service bus의 왼쪽에 있는 메뉴에서 **큐** 를 선택 합니다.

1. 큐를 선택 합니다.

1. 왼쪽 메뉴에서 **Service Bus 탐색기** 를 선택 합니다. **보내기** 에서 **콘텐츠 형식** 으로 **텍스트/일반** 을 선택 합니다. 그런 다음 메시지를 입력 합니다. 

1. **보내기** 를 선택 하 여 메시지를 보냅니다.

    :::image type="content" source="./media/functions-create-vnet/17-send-service-bus-message.png" alt-text="포털을 사용 하 여 service bus 메시지를 보내는 방법의 스크린샷":::

1. **라이브 메트릭** 탭에서 service bus 큐 트리거가 발생 한 것을 확인할 수 있습니다. 그렇지 않은 경우 **Service Bus 탐색기** 에서 메시지를 다시 보냅니다.

    :::image type="content" source="./media/functions-create-vnet/18-hello-world.png" alt-text="함수 앱에 대 한 라이브 메트릭을 사용 하 여 메시지를 보는 방법의 스크린샷":::

축하합니다! 전용 끝점을 사용 하 여 함수 앱 설정을 테스트 했습니다.

## <a name="understand-private-dns-zones"></a>개인 DNS 영역 이해
개인 끝점을 사용 하 여 Azure 리소스에 연결 했습니다. 공용 끝점 대신 개인 IP 주소에 연결 하 고 있습니다. 기존 Azure 서비스는 기존 DNS를 사용 하 여 공용 끝점에 연결 하도록 구성 됩니다. 개인 끝점에 연결 하려면 DNS 구성을 재정의 해야 합니다.

개인 끝점으로 구성 된 각 Azure 리소스에 대해 개인 DNS 영역이 생성 됩니다. 개인 끝점과 연결 된 각 개인 IP 주소에 대 한 DNS 레코드가 만들어집니다.

이 자습서에서는 다음 DNS 영역을 만들었습니다.

- privatelink.file.core.windows.net
- privatelink.blob.core.windows.net
- privatelink.servicebus.windows.net
- privatelink.azurewebsites.net

[!INCLUDE [clean-up-section-portal](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>다음 단계

이 자습서에서는 프리미엄 함수 앱, 저장소 계정 및 service bus를 만들었습니다. 이러한 리소스는 모두 개인 끝점 뒤에 보안을 설정 했습니다. 

다음 링크를 사용 하 여 사용 가능한 네트워킹 기능에 대해 자세히 알아보세요.

> [!div class="nextstepaction"]
> [Azure Functions 네트워킹 옵션](./functions-networking-options.md)


> [!div class="nextstepaction"]
> [Azure Functions 프리미엄 계획](./functions-premium-plan.md)
