---
title: 기존 가상 네트워크에 배포
description: 관리 되는 응용 프로그램의 사용자가 기존 가상 네트워크를 선택할 수 있도록 하는 방법을 설명 합니다. 가상 네트워크는 관리 되는 응용 프로그램 외부에 있을 수 있습니다.
author: tfitzmac
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: tomfitz
ms.openlocfilehash: fa5e59b96aada06c2dd486094d9be6a52c79e43e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84260686"
---
# <a name="use-existing-virtual-network-with-azure-managed-applications"></a>Azure Managed Applications에서 기존 가상 네트워크 사용

이 문서에서는 소비자 구독의 기존 가상 네트워크와 통합 되는 Azure 관리 되는 응용 프로그램을 정의 하는 방법을 보여 줍니다. 관리 되는 응용 프로그램을 통해 소비자는 새 가상 네트워크를 만들지 아니면 기존 네트워크를 사용할지를 결정할 수 있습니다. 기존 가상 네트워크는 관리 되는 리소스 그룹의 외부에 있을 수 있습니다.

## <a name="main-template"></a>기본 템플릿

먼저 파일 **의mainTemplate.js** 을 살펴보겠습니다. 가상 컴퓨터 및 관련 리소스를 배포 하기 위한 전체 템플릿은 다음과 같습니다. 나중에 기존 가상 네트워크를 사용 하는 것과 관련 된 템플릿의 일부를 자세히 살펴보겠습니다.

:::code language="json" source="~/resourcemanager-templates/managed-app-existing-vnet/mainTemplate.json":::

가상 네트워크는 [조건적으로 배포](../templates/conditional-resource-deployment.md)됩니다. 소비자는 새를 만들지 아니면 기존 가상 네트워크를 사용할지를 나타내는 매개 변수 값을 전달 합니다. 소비자가 새 가상 네트워크를 선택 하면 리소스가 배포 됩니다. 그렇지 않으면 배포 중에 리소스를 건너뜁니다.

:::code language="json" source="~/resourcemanager-templates/managed-app-existing-vnet/mainTemplate.json" range="111-132" highlight="2":::

가상 네트워크 ID에 대 한 변수에는 두 개의 속성이 있습니다. 새 가상 네트워크를 배포 하는 경우 한 가지 속성은 리소스 ID를 반환 합니다. 다른 속성은 기존 가상 네트워크를 사용 하는 경우 리소스 ID를 반환 합니다. 기존 가상 네트워크의 리소스 ID에는 가상 네트워크를 포함 하는 리소스 그룹의 이름이 포함 됩니다.

서브넷 ID는 가상 네트워크 ID에 대 한 값에서 생성 됩니다. 소비자 선택 항목과 일치 하는 값을 사용 합니다.

:::code language="json" source="~/resourcemanager-templates/managed-app-existing-vnet/mainTemplate.json" range="98-109" highlight="6-10":::

네트워크 인터페이스가 서브넷 ID 변수로 설정 됩니다.

:::code language="json" source="~/resourcemanager-templates/managed-app-existing-vnet/mainTemplate.json" range="142-163" highlight="16":::

## <a name="ui-definition"></a>UI 정의

이제 파일 **에createUiDefinition.js** 을 살펴보겠습니다. 전체 파일은 다음과 같습니다.

:::code language="json" source="~/resourcemanager-templates/managed-app-existing-vnet/createUiDefinition.json":::

이 파일에는 가상 네트워크 요소가 포함 됩니다.

:::code language="json" source="~/resourcemanager-templates/managed-app-existing-vnet/createUiDefinition.json" range="53-81":::

이 요소를 통해 소비자는 새 가상 네트워크 또는 기존 가상 네트워크를 선택할 수 있습니다.

:::image type="content" source="./media/existing-vnet-integration/new-or-existing-vnet.png" alt-text="새 가상 네트워크 또는 기존 가상 네트워크":::

출력에 소비자가 새 가상 네트워크 또는 기존 가상 네트워크를 선택 했는지 여부를 나타내는 값을 포함 합니다. 관리 되는 id 값도 있습니다.

> [!NOTE]
> 관리 id의 출력 값은 **microsoft.managedidentity**로 지정 해야 합니다.

:::code language="json" source="~/resourcemanager-templates/managed-app-existing-vnet/createUiDefinition.json" range="136-148" highlight="6,12":::

## <a name="next-steps"></a>다음 단계

UI 정의 파일을 만드는 방법에 대 한 자세한 내용은 [Azure 관리 되는 응용 프로그램의 만들기 환경을 위한CreateUiDefinition.js](create-uidefinition-overview.md)를 참조 하세요.
