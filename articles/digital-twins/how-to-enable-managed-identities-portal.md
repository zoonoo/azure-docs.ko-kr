---
title: 라우팅 이벤트에 대해 관리 id 사용 (미리 보기)-포털
titleSuffix: Azure Digital Twins
description: Azure Portal를 사용 하 여 Azure Digital Twins에 대해 시스템에서 할당 한 id를 사용 하 고 이벤트를 전달 하는 데 사용 하는 방법을 참조 하세요.
author: baanders
ms.author: baanders
ms.date: 1/21/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 1d54ecdac739bbc5a072426f5bf73f71ab394159
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100417676"
---
# <a name="enable-a-managed-identity-for-routing-azure-digital-twins-events-preview-azure-portal"></a>Azure 디지털 쌍 이벤트 라우팅 (미리 보기)에 대 한 관리 id 사용: Azure Portal

[!INCLUDE [digital-twins-managed-service-identity-selector.md](../../includes/digital-twins-managed-service-identity-selector.md)]

이 문서에서는 [Azure Digital Twins 인스턴스에 대해 시스템에 할당 된 id](concepts-security.md#managed-identity-for-accessing-other-resources-preview) 를 사용 하도록 설정 하 고 (현재 미리 보기 상태) [이벤트 허브](../event-hubs/event-hubs-about.md), [Service Bus](../service-bus-messaging/service-bus-messaging-overview.md)   대상 및 [Azure Storage 컨테이너](../storage/blobs/storage-blobs-introduction.md)와 같은 지원 되는 대상으로 이벤트를 전달할 때 id를 사용 하는 방법을 설명 합니다.

이 문서에서는 [**Azure Portal**](https://portal.azure.com)를 사용 하는 프로세스를 안내 합니다.

이 문서에서 설명 하는 단계는 다음과 같습니다. 

1. 시스템이 할당 한 id를 사용 하 여 Azure 디지털 Twins 인스턴스를 만들거나 기존 Azure Digital Twins 인스턴스에서 시스템 할당 id를 사용 하도록 설정 합니다. 
1. Id에 적절 한 역할 또는 역할을 추가 합니다. 예를 들어 끝점이 이벤트 허브가 면 **Azure 이벤트 허브 데이터 발신자** 역할을 id에 할당 하 고, 끝점이 Service Bus 되는 경우 **데이터 발신자 역할을 Azure Service Bus** 합니다.
1. 인증에 시스템 할당 id를 사용할 수 있는 Azure Digital Twins에서 끝점을 만듭니다.

## <a name="enable-system-managed-identities-for-an-instance"></a>인스턴스에 대해 시스템 관리 id 사용 

Azure Digital Twins 인스턴스에서 시스템 할당 id를 사용 하도록 설정 하면 azure는 [Azure Active Directory (AZURE AD)](../active-directory/fundamentals/active-directory-whatis.md)에서 해당 id에 대 한 id를 자동으로 만듭니다. 그런 다음 해당 id를 사용 하 여 이벤트 전달을 위한 Azure Digital Twins 끝점을 인증할 수 있습니다.

**인스턴스의 초기 설정 중** 에 Azure Digital twins 인스턴스에 대해 시스템 관리 id를 사용 하도록 설정 하거나 **이미 존재 하는 인스턴스에서는 나중에 사용 하도록** 설정할 수 있습니다.

이러한 생성 방법 중 하나는 인스턴스에 대해 동일한 구성 옵션과 동일한 최종 결과를 제공 합니다. 이 섹션에서는 두 작업을 수행 하는 방법을 설명 합니다.

### <a name="add-a-system-managed-identity-during-instance-creation"></a>인스턴스를 만드는 동안 시스템 관리 id 추가

이 섹션에서는 현재 만들어지는 Azure Digital Twins 인스턴스에서 시스템 관리 id를 사용 하도록 설정 하는 방법에 대해 알아봅니다. 이 섹션에서는 만들기 프로세스의 관리 되는 id 단계에 대해 집중적으로 설명 합니다. 새 Azure Digital Twins 인스턴스를 만드는 전체 연습은 [*방법: 인스턴스 및 인증 설정*](how-to-set-up-instance-portal.md)을 참조 하세요.

시스템 관리 id 옵션은 인스턴스 설치의 **고급** 탭에 있습니다.

이 탭에서 **시스템 관리 id** 에 대 한 **설정 옵션을 선택 하 여이** 기능을 설정 합니다.

:::image type="content" source="media/how-to-enable-managed-identities/create-instance-advanced.png" alt-text="Azure Digital Twins에 대 한 리소스 만들기 대화 상자의 고급 탭을 보여 주는 Azure Portal의 스크린샷 탭 이름, 시스템 관리 id에 대 한 켜기 옵션 및 탐색 단추 (검토 + 만들기, 이전, 다음: 고급)가 강조 표시 됩니다.":::

그런 다음 아래쪽 탐색 단추를 사용 하 여 나머지 인스턴스 설치를 계속할 수 있습니다.

### <a name="add-a-system-managed-identity-to-an-existing-instance"></a>기존 인스턴스에 시스템 관리 id 추가

이 섹션에서는 이미 존재 하는 Azure Digital Twins 인스턴스에 시스템 관리 id를 추가 합니다.

1. 먼저 브라우저에서 [Azure Portal](https://portal.azure.com) 로 이동 합니다.

1. 포털 검색 표시줄에서 인스턴스 이름을 검색 하 고 선택 하 여 세부 정보를 확인 합니다.

1. 왼쪽 메뉴에서 **id (미리 보기)** 를 선택 합니다.

1. 이 페이지에서이 기능을 설정 하 **는 옵션을 선택 합니다.**

1. **저장** 단추를 누르고 **예** 를 클릭 하 여 확인 합니다.

    :::image type="content" source="media/how-to-enable-managed-identities/identity-digital-twins.png" alt-text="Azure Digital Twins 인스턴스의 Id (미리 보기) 페이지를 보여 주는 Azure Portal의 스크린샷 Azure 디지털 Twins 인스턴스 메뉴에서 페이지 이름, 상태에 대 한 옵션, 저장 단추 및 예 확인 단추를 강조 표시 합니다.":::

변경 내용이 저장 되 면 새 id의 **개체 ID** 및 **사용 권한에** 대 한 추가 필드가이 페이지에 표시 됩니다.

필요한 경우 여기에서 **개체 id** 를 복사 하 고, **사용 권한** 단추를 사용 하 여 Id에 할당 된 Azure 역할을 볼 수 있습니다. 일부 역할을 설정 하려면 다음 섹션을 계속 진행 합니다.

## <a name="assign-azure-roles-to-the-identity"></a>Id에 Azure 역할 할당 

Azure Digital Twins 인스턴스에 대해 시스템 할당 id를 만든 후에는 지원 되는 대상에 이벤트를 전달 하기 위해 서로 다른 유형의 [끝점](concepts-route-events.md) 을 사용 하 여 인증할 수 있도록 적절 한 역할을 할당 해야 합니다. 이 섹션에서는 역할 옵션과 시스템 할당 id에 역할을 할당 하는 방법에 대해 설명 합니다.

>[!NOTE]
> 이는 중요 한 단계입니다 .이 단계를 사용 하지 않으면 id가 끝점에 액세스할 수 없고 이벤트가 전달 되지 않습니다.

### <a name="supported-destinations-and-azure-roles"></a>지원 되는 대상 및 Azure 역할 

대상 유형에 따라 id에서 끝점에 액세스 하는 데 필요한 최소 역할은 다음과 같습니다. 더 높은 사용 권한이 있는 역할 (예: 데이터 소유자 역할)도 작동 합니다.

| 대상 | Azure 역할 |
| --- | --- |
| Azure Event Hubs | Azure Event Hubs 데이터 보내는 사람 |
| Azure Service Bus | Azure Service Bus 데이터 보내는 사람 |
| Azure 스토리지 컨테이너 | Storage Blob 데이터 기여자 |

Azure Digital Twins에서 라우팅에 대해 지원 되는 끝점, 경로 및 대상 유형에 대 한 자세한 내용은 [*개념: 이벤트 경로*](concepts-route-events.md)를 참조 하세요.

### <a name="assign-the-role"></a>역할 할당

>[!NOTE]
> 이 섹션은 azure 리소스에 대 한 사용자 액세스를 관리할 수 있는 권한이 있는 Azure 사용자 (권한 부여 및 위임 포함)에서 완료 해야 합니다. 이 요구 사항을 충족 하는 일반적인 역할은 *소유자*, *계정 관리자* 또는 *사용자 액세스 관리자* 와 *참가자* 의 조합입니다. Azure Digital Twins 역할의 권한 요구 사항에 대 한 자세한 내용은 [*방법: 인스턴스 및 인증 설정*](how-to-set-up-instance-portal.md#prerequisites-permission-requirements)을 참조 하세요.

Id에 역할을 할당 하려면 먼저 [Azure Portal](https://portal.azure.com)를 열어 시작 합니다.

1. 포털 검색 표시줄에서 해당 이름을 검색 하 여 끝점 리소스 (이벤트 허브, Service Bus 토픽 또는 저장소 컨테이너)로 이동 합니다. 
1. 왼쪽 메뉴에서 **액세스 제어 (IAM)** 를 선택 합니다.
1. **+ 추가** 단추를 선택 하 여 새 역할 할당을 추가 합니다.

    :::image type="content" source="media/how-to-enable-managed-identities/add-role-assignment-1.png" alt-text="이벤트 허브에 대 한 액세스 제어 (IAM) 페이지를 표시 하는 Azure Portal의 스크린샷 + 추가 단추가 강조 표시 됩니다." lightbox="media/how-to-enable-managed-identities/add-role-assignment-1.png":::

1. 다음 **역할 할당 추가** 페이지에서 값을 입력 합니다.
    * **역할**: 드롭다운 메뉴에서 원하는 역할을 선택 합니다.
    * **액세스 권한 할당**: **사용자, 그룹 또는 서비스 주체** 선택
    * **Select**: 여기에서 역할에 할당 되는 Azure Digital twins 인스턴스의 관리 id를 선택 합니다. 관리 id의 이름은 인스턴스의 이름과 일치 하므로 Azure Digital Twins 인스턴스의 이름을 검색 합니다. 결과를 선택 하면 인스턴스의 id가 **선택한 구성원** 섹션에 표시 됩니다.

    :::row:::
        :::column:::
            :::image type="content" source="media/how-to-enable-managed-identities/add-role-assignment-2.png" alt-text="' 역할 할당 추가 ' 대화 상자에 나열 된 필드를 채웁니다.":::
        :::column-end:::
        :::column:::
        :::column-end:::
    :::row-end:::

세부 정보를 입력 하는 작업이 끝나면 **저장** 을 선택 합니다.

## <a name="create-an-endpoint-with-identity-based-authorization"></a>Id 기반 권한 부여를 사용 하 여 끝점 만들기

Azure Digital Twins 인스턴스에 대해 시스템 관리 id를 설정 하 고 적절 한 역할을 할당 한 후에는 인증에 id를 사용할 수 있는 Azure Digital Twins [끝점](how-to-manage-routes-portal.md#create-an-endpoint-for-azure-digital-twins) 을 만들 수 있습니다. 이 옵션은 이벤트 허브 및 Service Bus 형식 끝점에 대해서만 사용할 수 있습니다 (Event Grid에는 지원 되지 않음).

>[!NOTE]
> 키 기반 id로 이미 만들어진 끝점을 편집 하 여 id 기반 인증으로 변경할 수는 없습니다. 끝점을 처음 만들 때 인증 유형을 선택 해야 합니다.

지침에 따라 [Azure Digital Twins 끝점을 만듭니다](how-to-manage-routes-portal.md#create-an-endpoint-for-azure-digital-twins).

끝점 유형에 필요한 세부 정보를 완료 하는 단계를 수행 하려면 인증 유형에 대해 **id 기반** 을 선택 해야 합니다.

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-manage-routes-portal/create-endpoint-event-hub-authentication.png" alt-text="Event Hub 형식의 끝점을 만드는 스크린샷" lightbox="media/how-to-manage-routes-portal/create-endpoint-event-hub-authentication.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

끝점 설정을 완료 하 고 **저장** 을 선택 합니다.

## <a name="considerations-for-disabling-system-managed-identities"></a>시스템 관리 id를 사용 하지 않도록 설정 시 고려 사항

Id는이를 사용 하는 끝점과 별도로 관리 되기 때문에 id 또는 해당 역할에 대 한 변경 사항이 Azure Digital Twins 인스턴스의 끝점에 포함 될 수 있는 영향을 고려 하는 것이 중요 합니다. Id를 사용 하지 않도록 설정 하거나 끝점에 대해 필요한 역할을 제거 하는 경우 끝점에 액세스할 수 없게 되 고 이벤트 흐름이 중단 됩니다.

이제 사용 하지 않도록 설정 된 관리 id로 설정 된 끝점을 계속 사용 하려면 끝점을 삭제 하 고 다른 인증 유형을 사용 하 여 [다시 만들어야](how-to-manage-routes-portal.md#create-an-endpoint-for-azure-digital-twins) 합니다. 이 변경 후에는 이벤트가 끝점에 대 한 배달을 다시 시작 하는 데 최대 1 시간이 걸릴 수 있습니다.

## <a name="next-steps"></a>다음 단계

Azure AD에서 관리 되는 id에 대해 자세히 알아보세요. 
* [*Azure 리소스에 대한 관리 ID*](../active-directory/managed-identities-azure-resources/overview.md)