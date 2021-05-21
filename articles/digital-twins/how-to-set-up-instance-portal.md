---
title: 인스턴스 및 인증 설정(포털)
titleSuffix: Azure Digital Twins
description: Azure Portal을 사용하여 Azure Digital Twins 서비스 인스턴스를 설정하는 방법을 참조하세요.
author: baanders
ms.author: baanders
ms.date: 7/23/2020
ms.topic: how-to
ms.service: digital-twins
ms.custom: contperf-fy21q2
ms.openlocfilehash: 8263e0805f48976222e66922be8c04e0902101d6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102201841"
---
# <a name="set-up-an-azure-digital-twins-instance-and-authentication-portal"></a>Azure Digital Twins 인스턴스 및 인증 설정(포털)

[!INCLUDE [digital-twins-setup-selector.md](../../includes/digital-twins-setup-selector.md)]

이 문서에서는 인스턴스를 만들고 인증을 설정하는 등 **새 Azure Digital Twins 인스턴스를 설정** 하는 단계에 대해 설명합니다. 이 문서를 완료한 후에는 Azure Digital Twins 인스턴스에 대한 프로그래밍을 시작할 수 있습니다.

이 문서의 버전은 Azure Portal을 사용하여 이러한 단계를 하나씩 수동으로 진행합니다. Azure Portal은 명령줄 도구의 대안을 제공하는 웹 기반의 통합 콘솔입니다.
* CLI를 사용하여 이러한 단계를 수동으로 진행하려면 이 문서의 CLI 버전 [*방법: 인스턴스 및 인증 설정(CLI)* ](how-to-set-up-instance-cli.md)을 참조하세요.
* 배포 스크립트 샘플을 사용하여 자동화된 설정을 실행하려면 이 문서의 스크립팅된 버전 [*방법: 인스턴스 및 인증 설정(스크립팅됨)* ](how-to-set-up-instance-scripted.md)을 참조하세요.

[!INCLUDE [digital-twins-setup-steps.md](../../includes/digital-twins-setup-steps.md)]
[!INCLUDE [digital-twins-setup-permissions.md](../../includes/digital-twins-setup-permissions.md)]

## <a name="create-the-azure-digital-twins-instance"></a>Azure Digital Twins 인스턴스 만들기

이 섹션에서는 [Azure Portal](https://ms.portal.azure.com/)을 사용하여 **Azure Digital Twins의 새 인스턴스를 만듭니다**. 포털로 이동하여 자격 증명으로 로그인합니다.

포털에서 Azure 서비스 홈페이지 메뉴에서 _리소스 만들기_ 를 선택하여 시작합니다.

:::image type="content" source= "media/how-to-set-up-instance/portal/create-resource.png" alt-text="Azure Portal 홈페이지에서 ‘리소스 만들기’를 선택합니다.":::

검색 상자에서 *Azure Digital Twins* 를 검색하고 결과에서 **Azure Digital Twins** 서비스를 선택합니다. _만들기_ 단추를 선택하여 서비스의 새 인스턴스를 만듭니다.

:::image type="content" source= "media/how-to-set-up-instance/portal/create-azure-digital-twins.png" alt-text="Azure Digital Twins 서비스 페이지에서 '만들기' 선택":::

다음 **리소스 만들기** 페이지에서 아래 지정된 값을 입력합니다.
* **구독**: 사용 중인 Azure 구독
  - **리소스 그룹**: 인스턴스를 배포할 리소스 그룹입니다. 기존 리소스 그룹이 아직 없는 경우 *새로 만들기* 링크를 선택하고 새 리소스 그룹의 이름을 입력하여 여기에서 하나를 만들 수 있습니다.
* **위치**: 배포를 위한 Azure Digital twins 지원 지역입니다. 지역별 지원에 대한 자세한 내용은 [*지역별 사용 가능한 Azure 제품(Azure Digital Twins)* ](https://azure.microsoft.com/global-infrastructure/services/?products=digital-twins)을 참조하세요.
* **리소스 이름**: Azure Digital Twins 인스턴스의 이름입니다. 구독에 이미 지정된 이름을 사용하고 있는 지역에 다른 Azure Digital Twins 인스턴스가 있는 경우 다른 이름을 선택하라는 메시지가 표시됩니다.

:::image type="content" source= "media/how-to-set-up-instance/portal/create-azure-digital-twins-2.png" alt-text="설명된 값을 입력하여 Azure Digital Twins 리소스 만들기":::

완료되었을 때 인스턴스에 대한 추가 설정을 구성하지 않으려는 경우 **검토 + 만들기** 를 선택할 수 있습니다. 그렇게 하면 요약 페이지로 이동하여 입력한 인스턴스 세부 정보를 검토하고 **만들기** 로 완료할 수 있습니다. 

인스턴스에 대한 자세한 정보를 구성하려면 다음 섹션에서 나머지 설정 탭을 설명합니다.

### <a name="additional-setup-options"></a>추가 설치 옵션

다음은 설치 중에 **리소스 만들기** 프로세스의 다른 탭을 사용하여 구성할 수 있는 추가 옵션입니다.

* **네트워킹**: 이 탭에서는 [Azure Private Link](../private-link/private-link-overview.md)로 프라이빗 엔드포인트를 사용하도록 설정하여 인스턴스에 대한 공용 네트워크 노출을 제거할 수 있습니다. 자세한 내용은 [*방법: Private Link를 사용하여 프라이빗 액세스 사용(미리 보기)* ](./how-to-enable-private-link-portal.md#add-a-private-endpoint-during-instance-creation)을 참조하세요.
* **고급**: 이 탭에서는 이벤트를 [엔드포인트](concepts-route-events.md)로 전달할 때 사용할 수 있는 인스턴스에 대해 [시스템 관리 ID](../active-directory/managed-identities-azure-resources/overview.md)를 사용하도록 설정할 수 있습니다. 자세한 내용은 [*방법: 라우팅 이벤트에 대해 관리 ID 사용(미리 보기)* ](./how-to-enable-managed-identities-portal.md#add-a-system-managed-identity-during-instance-creation)을 참조하세요.
* **태그**: 이 탭에서는 인스턴스에 태그를 추가하여 Azure 리소스에서 구성하는 데 도움을 줍니다. Azure 리소스 태그에 대한 자세한 내용은 [*논리 조직에 대한 리소스, 리소스 그룹 및 구독 태그*](../azure-resource-manager/management/tag-resources.md)를 참조하세요.

### <a name="verify-success-and-collect-important-values"></a>성공 확인 및 중요한 값 수집

**만들기** 를 선택하여 인스턴스 설치를 완료한 후에는 포털 아이콘 표시줄에 나타나는 Azure 알림에서 인스턴스의 배포 상태를 볼 수 있습니다. 배포에 성공하면 알림이 표시되고, _리소스로 이동_ 단추를 선택하여 생성 인스턴스를 볼 수 있습니다.

:::image type="content" source="media/how-to-set-up-instance/portal/notifications-deployment.png" alt-text="성공적인 배포를 표시하고 '리소스로 이동' 단추를 강조 표시하는 Azure 알림 보기":::

또는 배포에 실패하는 경우 알림이 표시됩니다. 오류 메시지의 조언을 따르고 인스턴스 만들기를 다시 시도합니다.

>[!TIP]
>인스턴스를 만든 후에는 Azure Portal 검색 창에서 인스턴스 이름을 검색하여 언제든지 페이지로 돌아갈 수 있습니다.

인스턴스의 *개요* 페이지에서 *이름*, *리소스 그룹* 및 *호스트 이름* 을 확인합니다. 이 값은 Azure Digital Twins 인스턴스를 계속 사용하는 데 필요할 수 있는 중요한 값입니다. 다른 사용자가 인스턴스에 대한 프로그래밍을 수행하는 경우 다른 사용자와 이 값을 공유해야 합니다.

:::image type="content" source="media/how-to-set-up-instance/portal/instance-important-values.png" alt-text="인스턴스의 개요 페이지에서 중요한 값 강조 표시":::

이제 Azure Digital Twins 인스턴스를 사용할 준비가 되었습니다. 다음으로, 적절한 Azure 사용자에게 관리 권한을 부여합니다.

## <a name="set-up-user-access-permissions"></a>사용자 액세스 권한 설정

[!INCLUDE [digital-twins-setup-role-assignment.md](../../includes/digital-twins-setup-role-assignment.md)]

먼저 Azure Portal에서 Azure Digital Twins 인스턴스의 페이지를 엽니다. 인스턴스의 메뉴에서 *액세스 제어(IAM)* 를 선택합니다. **+ 추가** 단추를 선택하여 새 역할 할당을 추가합니다.

:::image type="content" source="media/how-to-set-up-instance/portal/add-role-assignment-1.png" alt-text="'액세스 제어(IAM)' 페이지에서 역할 할당을 추가하려면 선택":::

다음 *역할 할당 추가* 페이지에서 값을 입력합니다(Azure 구독에서 [충분한 권한](#prerequisites-permission-requirements)이 있는 사용자가 완료해야 함).
* **역할**: 드롭다운 메뉴에서 *Azure Digital Twins 데이터 소유자* 를 선택합니다.
* **액세스 할당**: *사용자, 그룹 또는 서비스 주체* 를 사용합니다.
* **선택**: 할당할 사용자의 이름 또는 이메일 메일 주소를 검색합니다. 결과를 선택하면 사용자가 *선택한 구성원* 섹션에 표시됩니다.

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-set-up-instance/portal/add-role-assignment-2.png" alt-text="‘역할 할당 추가’ 대화 상자에 나열된 필드 입력":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

세부 정보 입력을 마치면 *저장* 단추를 누릅니다.

### <a name="verify-success"></a>성공 확인

*액세스 제어(IAM) > 역할 할당* 에서 설정된 역할 할당을 볼 수 있습니다. 사용자는 *Azure Digital Twins 데이터 소유자* 역할로 목록에 표시되어야 합니다. 

:::image type="content" source="media/how-to-set-up-instance/portal/verify-role-assignment.png" alt-text="Azure Portal에서 Azure Digital Twins 인스턴스에 대한 역할 할당 보기":::

이제 Azure Digital Twins 인스턴스를 사용할 준비가 되고 관리 권한이 할당되었습니다.

## <a name="next-steps"></a>다음 단계

Azure Digital Twins CLI 명령을 사용하여 인스턴스에서 개별 REST API 호출을 테스트합니다. 
* [az dt reference](/cli/azure/ext/azure-iot/dt)
* [*방법: Azure Digital Twins CLI 사용*](how-to-use-cli.md)

또는 인증 코드를 사용하여 인스턴스에 클라이언트 애플리케이션을 연결하는 방법을 참조하세요.
* [*방법: 앱 인증 코드 작성*](how-to-authenticate-client.md)