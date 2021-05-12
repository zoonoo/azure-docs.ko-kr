---
title: Azure에서 BareMetal 인스턴스 단위 연결
description: Azure Portal 또는 Azure CLI BareMetal 인스턴스 단위를 식별하고 이들과 상호 작용하는 방법에 대해 알아봅니다.
ms.topic: how-to
ms.date: 03/19/2021
ms.openlocfilehash: 42de1618813ba33934e3f8eeeee8dc7ac27d9824
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104951660"
---
# <a name="connect-baremetal-instance-units-in-azure"></a>Azure에서 BareMetal 인스턴스 단위 연결
 
이 문서에서는 [Azure Portal](https://portal.azure.com/)에 [BareMetal 인스턴스](concepts-baremetal-infrastructure-overview.md)를 표시하는 방법을 보여 줍니다. 또한 이 문서에서는 배포된 BareMetal 인스턴스 단위를 사용하여 Azure Portal에서 수행할 수 있는 작업을 보여 줍니다. 
 
## <a name="register-the-resource-provider"></a>리소스 공급자 등록
BareMetal 인스턴스에 대한 Azure 리소스 공급자는 현재 퍼블릭 미리 보기로 제공되는 Azure Portal의 인스턴스를 표시합니다. 기본적으로 BareMetal 인스턴스 배포에 사용하는 Azure 구독에서는 *BareMetalInfrastructure* 리소스 공급자를 등록합니다. 배포된 BareMetal 인스턴스 단위가 표시되지 않는 경우 구독에 리소스 공급자를 등록해야 합니다. 

Azure Portal 또는 Azure CLI를 사용하여 BareMetal 인스턴스 리소스 공급자를 등록할 수 있습니다.

### <a name="portal"></a>[포털](#tab/azure-portal)
 
Azure Portal에서 구독을 나열하고 BareMetal 인스턴스 단위를 배포하는 데 사용되는 구독을 두 번 클릭해야 합니다.
 
1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. Azure Portal 메뉴에서 **모든 서비스** 를 선택합니다.

1. **모든 서비스** 상자에서 **구독** 을 입력한 다음, **구독** 을 선택합니다.

1. 구독 목록에서 보려는 구독을 선택합니다.

1. **리소스 공급자** 를 선택하고 검색에 **BareMetalInfrastructure** 를 입력합니다. 이미지에 표시된 대로 리소스 공급자를 **등록** 해야 합니다.
 
>[!NOTE]
>리소스 공급자가 등록되지 않은 경우 **등록** 을 선택합니다.
 
:::image type="content" source="media/baremetal-infrastructure-portal/register-resource-provider-azure-portal.png" alt-text="등록된 BareMetal 인스턴스 단위를 보여 주는 스크린샷":::

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI 사용을 시작하려면 다음을 수행합니다.

[!INCLUDE [azure-cli-prepare-your-environment-no-header](../../includes/azure-cli-prepare-your-environment-no-header.md)]

Azure CLI를 통해 BareMetal 인스턴스 배포에 사용하는 Azure 구독에 로그인합니다. [az provider register](/cli/azure/provider#az_provider_register) 명령으로 `BareMetalInfrastructure` 리소스 제공자를 등록합니다.

```azurecli
az provider register --namespace Microsoft.BareMetalInfrastructure
```

[az provider list](/cli/azure/provider#az_provider_list) 명령을 사용하여 사용 가능한 모든 공급자를 볼 수 있습니다.

---

리소스 공급자에 관한 자세한 내용은 [Azure 리소스 공급자 및 유형](../azure-resource-manager/management/resource-providers-and-types.md)을 참조하세요.  

## <a name="baremetal-instance-units-in-the-azure-portal"></a>Azure Portal의 BareMetal 인스턴스 단위
 
BareMetal 인스턴스 배포 요청을 제출하는 경우 BareMetal 인스턴스에 연결할 Azure 구독을 지정합니다. BareMetal 인스턴스 단위에 대해 작동하는 애플리케이션 계층을 배포하는 데 사용하는 것과 동일한 구독을 사용합니다.
 
BareMetal 인스턴스를 배포하는 동안 배포 요청에 사용한 Azure 구독에 새 [Azure 리소스 그룹](../azure-resource-manager/management/manage-resources-portal.md)이 생성됩니다. 이 새 리소스 그룹에는 특정 구독에 배포한 모든 BareMetal 인스턴스 단위가 나열됩니다.

### <a name="portal"></a>[포털](#tab/azure-portal)

1. BareMetal 구독의 Azure Portal에서 **리소스 그룹** 을 선택합니다.
 
   :::image type="content" source="media/baremetal-infrastructure-portal/view-baremetal-instance-units-azure-portal.png" alt-text="리소스 그룹 목록을 보여 주는 스크린샷":::

1. 목록에서 새 리소스 그룹을 찾습니다.
 
   :::image type="content" source="media/baremetal-infrastructure-portal/filter-resource-groups.png" alt-text="필터링된 리소스 그룹 목록에서 BareMetal 인스턴스 단위를 보여 주는 스크린샷" lightbox="media/baremetal-infrastructure-portal/filter-resource-groups.png":::
   
   >[!TIP]
   >BareMetal 인스턴스를 배포하는 데 사용한 구독을 필터링할 수 있습니다. 적절한 구독으로 필터링한 후에도 리소스 그룹 목록이 길 수 있습니다. 뒤에 **-Txxx** 가 붙은 리소스 그룹을 찾습니다. 여기서 xxx는 **-T250** 과 같은 3자리 숫자입니다.

1. 새 리소스 그룹을 선택하여 세부 정보를 표시합니다. 이미지는 배포된 BareMetal 인스턴스 단위 하나를 보여 줍니다.
   
   >[!NOTE]
   >동일한 Azure 구독에서 여러 BareMetal Instance 테넌트를 배포한 경우 여러 Azure 리소스 그룹이 표시됩니다.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

모든 BareMetal 인스턴스를 보려면 리소스 그룹에 대해 [az baremetalinstance list](/cli/azure/ext/baremetal-infrastructure/baremetalinstance#ext_baremetal_infrastructure_az_baremetalinstance_list) 명령을 실행합니다.

```azurecli
az baremetalinstance list --resource-group DSM05A-T550 –output table
```

> [!TIP]
> `--output` 매개 변수는 모든 명령에 사용할 수 있는 글로벌 매개 변수입니다. **table** 값은 출력을 친숙한 형식으로 표시합니다. 자세한 내용은 [Azure CLI 명령의 출력 형식](/cli/azure/format-output-azure-cli)을 참조하세요.

---

## <a name="view-the-attributes-of-a-single-instance"></a>단일 인스턴스의 특성 보기

단일 단위의 세부 정보를 볼 수 있습니다.

### <a name="portal"></a>[포털](#tab/azure-portal)

BareMetal 인스턴스 목록에서 보려는 단일 인스턴스를 선택합니다.
 
:::image type="content" source="media/baremetal-infrastructure-portal/view-attributes-single-baremetal-instance.png" alt-text="단일 인스턴스의 BareMetal 인스턴스 단위 특성을 보여 주는 스크린샷" lightbox="media/baremetal-infrastructure-portal/view-attributes-single-baremetal-instance.png":::
 
이미지에 나오는 특성은 Azure VM(가상 머신) 특성과 크게 달라 보이지 않습니다. 왼쪽에는 리소스 그룹, Azure 지역, 구독 이름, ID가 있습니다. 태그를 할당한 경우 태그도 여기에 표시됩니다. 기본적으로 BareMetal 인스턴스 단위에는 할당된 태그가 없습니다.
 
오른쪽에는 단위 이름, OS(운영 체제), IP 주소, 그리고 CPU 스레드 수와 메모리 수를 보여 주는 SKU가 있습니다. 또한 전원 상태와 하드웨어 버전(BareMetal 인스턴스 스탬프의 수정 버전)이 표시됩니다. 전원 상태는 하드웨어 장치의 전원이 켜져 있는지 또는 꺼져 있는지를 나타냅니다. 그러나 운영 체제 세부 정보는 실행 여부를 나타내지 않습니다.
 
가능한 하드웨어 수정 버전은 다음과 같습니다.

* 수정 버전 3(Rev 3)

* 수정 버전 4(Rev 4)
 
* 수정 버전 4.2(Rev 4.2)
 
>[!NOTE]
>Rev 4.2는 기존 Rev 4 아키텍처를 사용하는 완전히 새로워진 최신 BareMetal 인프라입니다. Rev 4는 Azure VM(가상 머신) 호스트에 더 가까운 근접성을 제공합니다. Rev 4 스탬프 또는 행에 배포된 Azure VM과 BareMetal 인스턴스 단위 간의 네트워크 대기 시간이 대폭 단축되었습니다. Azure Portal을 통해 BareMetal 인스턴스에 액세스하고 이를 관리할 수 있습니다. 자세한 내용은 [Azure 기반 BareMetal 인프라](concepts-baremetal-infrastructure-overview.md)를 참조하세요.
 
또한 오른쪽에는 배포된 각 BareMetal 인스턴스 단위에 대해 자동으로 생성되는 [Azure 근접 배치 그룹](../virtual-machines/co-location.md)의 이름이 있습니다. 애플리케이션 계층을 호스트하는 Azure VM을 배포할 때 근접 배치 그룹을 참조합니다. BareMetal 인스턴스 단위와 연결된 근접 배치 그룹을 사용하는 경우 Azure VM이 BareMetal 인스턴스 단위에 가깝게 배포되도록 합니다.
 
>[!TIP]
>수정 버전 4.x와 동일한 Azure 데이터 센터에서 애플리케이션 계층을 찾으려면 [최적의 네트워크 대기 시간을 위한 Azure 근접 배치 그룹](/azure/virtual-machines/workloads/sap/sap-proximity-placement-scenarios)을 참조하세요.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

BareMetal 인스턴스에 대한 세부 정보를 보려면 [az baremetalinstance show](/cli/azure/ext/baremetal-infrastructure/baremetalinstance#ext_baremetal_infrastructure_az_baremetalinstance_show) 명령을 실행합니다.

```azurecli
az baremetalinstance show --resource-group DSM05A-T550 --instance-name orcllabdsm01
```

인스턴스 이름을 잘 모르는 경우 위에 설명된 `az baremetalinstance list` 명령을 실행합니다.

---
 
## <a name="check-activities-of-a-single-instance"></a>단일 인스턴스 활동 검사
 
단일 단위의 활동을 검사할 수 있습니다. 기록되는 주요 활동 중 하나는 단위 재시작입니다. 나열된 데이터에는 활동의 상태, 작업이 트리거된 타임스탬프, 구독 ID, 활동을 트리거한 Azure 사용자가 포함됩니다.
 
:::image type="content" source="media/baremetal-infrastructure-portal/check-activities-single-baremetal-instance.png" alt-text="BareMetal 인스턴스 단위 활동을 보여 주는 스크린샷" lightbox="media/baremetal-infrastructure-portal/check-activities-single-baremetal-instance.png":::
 
Azure의 단위 메타데이터에 대한 변경 내용도 활동 로그에 기록됩니다. 다시 시작하는 것 외에도 **BareMetallnstances 쓰기** 활동을 볼 수 있습니다. 이 활동은 BareMetal 인스턴스 단위 자체를 변경하지 않고 Azure의 단위 메타데이터에 대한 변경 내용을 문서화합니다.
 
[태그](../azure-resource-manager/management/tag-resources.md)를 인스턴스에 추가하거나 삭제하는 경우에도 활동이 기록됩니다.
 
## <a name="add-and-delete-an-azure-tag-to-an-instance"></a>Azure 태그를 인스턴스에 추가 및 삭제

### <a name="portal"></a>[포털](#tab/azure-portal)
 
Azure 태그를 BareMetal 인스턴스 단위에 추가하거나 삭제할 수 있습니다. 태그를 할당하는 방법은 VM에 태그를 할당하는 것과는 다릅니다. VM의 경우와 마찬가지로, 태그가 Azure 메타데이터에 존재하며, BareMetal 인스턴스의 경우 VM에 대한 태그와 동일한 제한이 적용됩니다.
 
태그 삭제는 VM과 동일한 방식으로 이루어집니다. 태그를 적용하고 삭제하는 작업이 BareMetal 인스턴스 단위의 활동 로그에 나열됩니다.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

BareMetal 인스턴스에 태그를 할당하는 작업은 가상 머신의 경우와 동일하게 이루어집니다. 태그가 Azure 메타데이터에 존재하며, BareMetal 인스턴스의 경우 VM에 대한 태그와 동일한 제한이 적용됩니다.

BareMetal 인스턴스 단위에 태그를 추가하려면 [az baremetalinstance update](/cli/azure/ext/baremetal-infrastructure/baremetalinstance#ext_baremetal_infrastructure_az_baremetalinstance_update) 명령을 실행합니다.

```azurecli
az baremetalinstance update --resource-group DSM05a-T550 --instance-name orcllabdsm01 --set tags.Dept=Finance tags.Status=Normal
```

동일한 명령을 사용하여 태그를 제거할 수 있습니다.

```azurecli
az baremetalinstance update --resource-group DSM05a-T550 --instance-name orcllabdsm01 --remove tags.Dept
```

---

## <a name="check-properties-of-an-instance"></a>인스턴스의 속성 확인
 
인스턴스를 획득할 때 속성 섹션으로 이동하여 인스턴스에 대해 수집된 데이터를 볼 수 있습니다. 수집된 데이터에는 Azure 연결, 스토리지 백 엔드, ExpressRoute 회로 ID, 고유 리소스 ID, 구독 ID가 포함됩니다. 이 정보는 지원 요청에 사용하거나 스토리지 스냅샷 구성을 설정할 때 사용합니다.
 
표시되는 또 다른 중요한 부분은 스토리지 NFS IP 주소입니다. 이는 BareMetal 인스턴스 스택에서 스토리지를 **테넌트** 로 격리합니다. [스토리지 스냅샷 백업을 위한 구성 파일](../virtual-machines/workloads/sap/hana-backup-restore.md#set-up-storage-snapshots)을 편집할 때 이 IP 주소를 사용합니다.
 
:::image type="content" source="media/baremetal-infrastructure-portal/baremetal-instance-properties.png" alt-text="BareMetal Instance 속성 설정을 보여 주는 스크린샷" lightbox="media/baremetal-infrastructure-portal/baremetal-instance-properties.png":::
 
## <a name="restart-a-unit-through-the-azure-portal"></a>Azure Portal을 통해 단위 다시 시작

OS가 다시 시작을 완료하지 못하는 여러 가지 상황이 있으며, 이 경우 BareMetal 인스턴스 단위를 다시 시작해야 합니다.

### <a name="portal"></a>[포털](#tab/azure-portal)

Azure Portal에서 직접 단위의 전원을 다시 시작할 수 있습니다.
 
**다시 시작** 을 선택하고 **예** 를 선택하여 단위 재시작을 확인합니다.
 
:::image type="content" source="media/baremetal-infrastructure-portal/baremetal-instance-restart.png" alt-text="BareMetal 인스턴스 단위를 다시 시작하는 방법을 보여 주는 스크린샷":::
 
BareMetal 인스턴스 단위를 다시 시작하면 지연이 발생합니다. 이 지연 시간 동안 전원 상태는 **시작 중** 에서 **시작됨** 으로 전환됩니다. 이는 OS가 완전히 시작되었음을 의미합니다. 결과적으로, 다시 시작한 후 상태가 **시작됨** 으로 전환되는 즉시 단위에 로그인할 수는 없습니다.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

BareMetal 인스턴스 단위를 다시 시작하려면 [az baremetalinstance restart](/cli/azure/ext/baremetal-infrastructure/baremetalinstance#ext_baremetal_infrastructure_az_baremetalinstance_restart) 명령을 사용합니다.

```azurecli
az baremetalinstance restart --resource-group DSM05a-T550 --instance-name orcllabdsm01
```

---

>[!IMPORTANT]
>BareMetal 인스턴스 단위의 메모리 양에 따라 다시 시작하고 하드웨어 및 운영 체제를 다시 부팅하는 데 최대 1시간이 걸릴 수 있습니다.
 
## <a name="open-a-support-request-for-baremetal-instances"></a>BareMetal 인스턴스에 대한 지원 요청 열기
 
BareMetal 인스턴스 단위에 대해 특별히 지원 요청을 제출할 수 있습니다.
1. Azure Portal의 **도움말 + 지원** 에서 **[새 지원 요청](https://rc.portal.azure.com/#create/Microsoft.Support)** 을 작성하고, 티켓에 대한 다음 정보를 제공합니다.
 
   - **문제 유형:** 문제 유형 선택
 
   - **구독:** 해당 구독 선택
 
   - **서비스:** BareMetal 인프라
 
   - **리소스:** 인스턴스의 이름 제공
 
   - **요약:** 요청 요약 제공
 
   - **문제 유형:** 문제 유형 선택
 
   - **문제 하위 유형:** 문제 하위 유형 선택

1. **해결 방법** 탭을 선택하여 문제에 대한 해결 방법을 찾습니다. 해결 방법을 찾을 수 없는 경우 다음 단계로 이동합니다.

1. **세부 정보** 탭을 선택하고 VM 또는 BareMetal 인스턴스 단위에 문제가 있는지를 선택합니다. 이 정보를 통해 적합한 전문가에게 지원 요청을 보낼 수 있습니다.

1. 문제가 시작된 시간을 알려 주고 인스턴스 지역을 선택합니다.

1. 요청에 대한 자세한 정보를 제공하고 필요한 경우 파일을 업로드합니다.

1. **검토 + 만들기** 를 선택하여 요청을 제출합니다.
 
지원 담당자가 요청을 확인하는 데 최대 5영업일이 소요됩니다.

## <a name="next-steps"></a>다음 단계

워크로드에 대해 자세히 알아보려면 [BareMetal 워크로드 유형](../virtual-machines/workloads/sap/get-started.md)을 참조하세요.