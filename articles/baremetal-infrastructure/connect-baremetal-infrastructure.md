---
title: Azure에서 BareMetal 인스턴스 단위 연결
description: Azure Portal 또는 Azure CLI BareMetal 인스턴스 단위를 식별 하 고 상호 작용 하는 방법에 대해 알아봅니다.
ms.topic: how-to
ms.subservice: workloads
ms.date: 03/19/2021
ms.openlocfilehash: 60a36deb00ab6c2399d25a880bce8fe624d9609f
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104776255"
---
# <a name="connect-baremetal-instance-units-in-azure"></a>Azure에서 BareMetal 인스턴스 단위 연결
 
이 문서에서는 [Azure Portal](https://portal.azure.com/) [BareMetal 인스턴스](concepts-baremetal-infrastructure-overview.md)를 표시 하는 방법을 보여 줍니다. 또한이 문서에서는 배포 된 BareMetal 인스턴스 단위를 사용 하 여 Azure Portal에서 수행할 수 있는 작업을 보여 줍니다. 
 
## <a name="register-the-resource-provider"></a>리소스 공급자 등록
BareMetal 인스턴스에 대 한 Azure 리소스 공급자는 현재 공개 미리 보기로 제공 되는 Azure Portal의 인스턴스를 표시 합니다. 기본적으로 BareMetal 인스턴스 배포에 사용 하는 Azure 구독은 *BareMetalInfrastructure* 리소스 공급자를 등록 합니다. 배포 된 BareMetal 인스턴스 단위가 표시 되지 않는 경우 구독에 리소스 공급자를 등록 해야 합니다. 

Azure Portal 또는 Azure CLI를 사용 하 여 BareMetal Instance 리소스 공급자를 등록할 수 있습니다.

### <a name="portal"></a>[포털](#tab/azure-portal)
 
Azure Portal에서 구독을 나열 하 고 BareMetal 인스턴스 단위를 배포 하는 데 사용 되는 구독을 두 번 클릭 해야 합니다.
 
1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. Azure Portal 메뉴에서 **모든 서비스** 를 선택합니다.

1. **모든 서비스** 상자에서 **구독** 을 입력한 다음, **구독** 을 선택합니다.

1. 구독 목록에서 보려는 구독을 선택합니다.

1. **리소스 공급자** 를 선택 하 고 검색에 **BareMetalInfrastructure** 를 입력 합니다. 이미지에 표시 된 대로 리소스 공급자를 **등록** 해야 합니다.
 
>[!NOTE]
>리소스 공급자가 등록되지 않은 경우 **등록** 을 선택합니다.
 
:::image type="content" source="media/baremetal-infrastructure-portal/register-resource-provider-azure-portal.png" alt-text="등록 된 BareMetal 인스턴스 단위를 보여 주는 스크린샷":::

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI 사용을 시작하려면 다음을 수행합니다.

[!INCLUDE [azure-cli-prepare-your-environment-no-header](../../includes/azure-cli-prepare-your-environment-no-header.md)]

Azure CLI를 통해 BareMetal 인스턴스 배포에 사용 하는 Azure 구독에 로그인 합니다. [az provider register](/cli/azure/provider#az_provider_register) 명령으로 `BareMetalInfrastructure` 리소스 제공자를 등록합니다.

```azurecli
az provider register --namespace Microsoft.BareMetalInfrastructure
```

[az provider list](/cli/azure/provider#az_provider_list) 명령을 사용하여 사용 가능한 모든 공급자를 볼 수 있습니다.

---

리소스 공급자에 관한 자세한 내용은 [Azure 리소스 공급자 및 유형](../azure-resource-manager/management/resource-providers-and-types.md)을 참조하세요.  

## <a name="baremetal-instance-units-in-the-azure-portal"></a>Azure Portal BareMetal Instance unit
 
BareMetal 인스턴스 배포 요청을 제출 하는 경우 BareMetal 인스턴스에 연결 하는 Azure 구독을 지정 합니다. BareMetal 인스턴스 단위에 대해 작동 하는 응용 프로그램 계층을 배포 하는 데 사용 하는 것과 동일한 구독을 사용 합니다.
 
BareMetal 인스턴스를 배포 하는 동안 배포 요청에서 사용한 Azure 구독에서 새 [azure 리소스 그룹이](../azure-resource-manager/management/manage-resources-portal.md) 생성 됩니다. 이 새 리소스 그룹에는 특정 구독에 배포한 모든 BareMetal 인스턴스 단위가 나열 됩니다.

### <a name="portal"></a>[포털](#tab/azure-portal)

1. BareMetal 구독의 Azure Portal에서 **리소스 그룹** 을 선택 합니다.
 
   :::image type="content" source="media/baremetal-infrastructure-portal/view-baremetal-instance-units-azure-portal.png" alt-text="리소스 그룹 목록을 보여 주는 스크린샷":::

1. 목록에서 새 리소스 그룹을 찾습니다.
 
   :::image type="content" source="media/baremetal-infrastructure-portal/filter-resource-groups.png" alt-text="필터링 된 리소스 그룹 목록에서 BareMetal 인스턴스 단위를 보여 주는 스크린샷" lightbox="media/baremetal-infrastructure-portal/filter-resource-groups.png":::
   
   >[!TIP]
   >BareMetal 인스턴스를 배포 하는 데 사용한 구독을 필터링 할 수 있습니다. 적절 한 구독으로 필터링 한 후에는 리소스 그룹의 긴 목록이 있을 수 있습니다. **-Txxx** 에서 xxx는- **T250** 와 같은 3 자리 숫자를 사용 하 여 찾습니다.

1. 새 리소스 그룹을 선택 하 여 세부 정보를 표시 합니다. 이미지에는 배포 된 하나의 BareMetal 인스턴스 단위가 표시 됩니다.
   
   >[!NOTE]
   >동일한 Azure 구독에서 여러 BareMetal Instance 테 넌 트를 배포한 경우 여러 Azure 리소스 그룹이 표시 됩니다.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

모든 BareMetal 인스턴스를 보려면 리소스 그룹에 대해 [az baremetalinstance list](/cli/azure/ext/baremetal-infrastructure/baremetalinstance#ext_baremetal_infrastructure_az_baremetalinstance_list) 명령을 실행 합니다.

```azurecli
az baremetalinstance list --resource-group DSM05A-T550 –output table
```

> [!TIP]
> `--output` 매개 변수는 모든 명령에 사용할 수 있는 글로벌 매개 변수입니다. **table** 값은 출력을 친숙한 형식으로 표시합니다. 자세한 내용은 [Azure CLI 명령의 출력 형식](/cli/azure/format-output-azure-cli)을 참조하세요.

---

## <a name="view-the-attributes-of-a-single-instance"></a>단일 인스턴스의 특성 보기

단일 단위의 세부 정보를 볼 수 있습니다.

### <a name="portal"></a>[포털](#tab/azure-portal)

BareMetal 인스턴스 목록에서 보려는 단일 인스턴스를 선택 합니다.
 
:::image type="content" source="media/baremetal-infrastructure-portal/view-attributes-single-baremetal-instance.png" alt-text="단일 인스턴스의 BareMetal Instance unit 특성을 보여 주는 스크린샷" lightbox="media/baremetal-infrastructure-portal/view-attributes-single-baremetal-instance.png":::
 
이미지의 특성은 Azure VM (가상 머신) 특성과 크게 다르지 않습니다. 왼쪽에 리소스 그룹, Azure 지역, 구독 이름 및 ID가 표시 됩니다. 태그를 할당 한 경우 여기에도 표시 됩니다. 기본적으로 BareMetal 인스턴스 단위에는 할당 된 태그가 없습니다.
 
오른쪽에는 CPU 스레드 및 메모리의 수를 표시 하는 단위 이름, OS (운영 체제), IP 주소 및 SKU가 표시 됩니다. 또한 전원 상태와 하드웨어 버전 (BareMetal 인스턴스 스탬프의 수정 버전)이 표시 됩니다. 전원 상태는 하드웨어 장치의 전원이 켜 졌는 지 여부를 나타냅니다. 그러나 운영 체제 세부 정보는 실행 여부를 나타내지 않습니다.
 
가능한 하드웨어 수정 사항은 다음과 같습니다.

* 수정 버전 3 (Rev 3)

* 수정 버전 4 (Rev 4)
 
* 수정 버전 4.2 (Rev 4.2)
 
>[!NOTE]
>Rev 4.2은 기존 수정 버전 4 아키텍처를 사용 하는 최신 리브랜딩 BareMetal 인프라입니다. Rev 4는 Azure VM (가상 컴퓨터) 호스트에 더 근접 하 게 됩니다. Rev 4 스탬프 또는 행에 배포 된 Azure Vm과 BareMetal instance 단위 간의 네트워크 대기 시간이 크게 향상 되었습니다. Azure Portal를 통해 BareMetal 인스턴스에 액세스 하 고 관리할 수 있습니다. 자세한 내용은 [BareMetal Infrastructure In Azure](concepts-baremetal-infrastructure-overview.md)항목을 참조 하세요.
 
또한 오른쪽에는 배포 된 각 BareMetal 인스턴스 단위에 대해 자동으로 생성 되는 [Azure 근접 배치 그룹](../virtual-machines/co-location.md) 이름이 있습니다. 응용 프로그램 계층을 호스트 하는 Azure Vm을 배포할 때 근접 배치 그룹을 참조 합니다. BareMetal 인스턴스 단위와 연결 된 근접 배치 그룹을 사용 하는 경우 Azure Vm이 BareMetal 인스턴스 단위에 가깝게 배포 되도록 합니다.
 
>[!TIP]
>수정 버전 4.x와 동일한 Azure 데이터 센터에서 응용 프로그램 계층을 찾으려면 [최적의 네트워크 대기 시간에 대 한 azure 근접 배치 그룹](/azure/virtual-machines/workloads/sap/sap-proximity-placement-scenarios)을 참조 하세요.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

BareMetal 인스턴스에 대 한 세부 정보를 보려면 [az baremetalinstance show](/cli/azure/ext/baremetal-infrastructure/baremetalinstance#ext_baremetal_infrastructure_az_baremetalinstance_show) 명령을 실행 합니다.

```azurecli
az baremetalinstance show --resource-group DSM05A-T550 --instance-name orcllabdsm01
```

인스턴스 이름을 잘 모르는 경우 `az baremetalinstance list` 위에 설명 된 명령을 실행 합니다.

---
 
## <a name="check-activities-of-a-single-instance"></a>단일 인스턴스의 활동 확인
 
단일 단위의 활동을 확인할 수 있습니다. 기록 된 주요 활동 중 하나가 단위 다시 시작입니다. 나열 된 데이터에는 활동의 상태, 작업을 트리거한 타임 스탬프, 구독 ID 및 활동을 트리거한 Azure 사용자가 포함 됩니다.
 
:::image type="content" source="media/baremetal-infrastructure-portal/check-activities-single-baremetal-instance.png" alt-text="BareMetal Instance unit 활동을 보여 주는 스크린샷" lightbox="media/baremetal-infrastructure-portal/check-activities-single-baremetal-instance.png":::
 
Azure의 단위 메타 데이터에 대 한 변경 내용도 활동 로그에 기록 됩니다. 다시 시작을 시작 하는 것 외에도 **Write BareMetallnstances** 의 작업을 볼 수 있습니다. 이 활동은 BareMetal 인스턴스 단위 자체를 변경 하지 않고 Azure의 단위 메타 데이터에 대 한 변경 내용을 문서화 합니다.
 
인스턴스에 [태그](../azure-resource-manager/management/tag-resources.md) 를 추가 하거나 삭제 하는 경우 기록 되는 다른 작업이 있습니다.
 
## <a name="add-and-delete-an-azure-tag-to-an-instance"></a>Azure 태그를 인스턴스에 추가 하 고 삭제 합니다.

### <a name="portal"></a>[포털](#tab/azure-portal)
 
BareMetal 인스턴스 단위에 Azure 태그를 추가 하거나 삭제할 수 있습니다. 태그를 할당 하는 방법은 Vm에 태그를 할당 하는 것과 다릅니다. Vm의 경우와 마찬가지로 태그가 Azure 메타 데이터에 존재 하 고 BareMetal 인스턴스의 경우 Vm에 대 한 태그와 동일한 제한이 적용 됩니다.
 
태그 삭제는 Vm과 동일한 방식으로 작동 합니다. 태그를 적용 하 고 삭제 하는 작업은 BareMetal 인스턴스 단위의 활동 로그에 나열 됩니다.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

BareMetal 인스턴스에 태그를 할당 하는 작업은 가상 컴퓨터의 경우와 동일 하 게 작동 합니다. 태그는 Azure 메타 데이터에 존재 하 고 BareMetal 인스턴스의 경우 Vm에 대 한 태그와 동일한 제한 사항이 있습니다.

BareMetal 인스턴스 단위에 태그를 추가 하려면 [az baremetalinstance update](/cli/azure/ext/baremetal-infrastructure/baremetalinstance#ext_baremetal_infrastructure_az_baremetalinstance_update) 명령을 실행 합니다.

```azurecli
az baremetalinstance update --resource-group DSM05a-T550 --instance-name orcllabdsm01 --set tags.Dept=Finance tags.Status=Normal
```

동일한 명령을 사용 하 여 태그를 제거 합니다.

```azurecli
az baremetalinstance update --resource-group DSM05a-T550 --instance-name orcllabdsm01 --remove tags.Dept
```

---

## <a name="check-properties-of-an-instance"></a>인스턴스의 속성 확인
 
인스턴스를 획득할 때 속성 섹션으로 이동 하 여 인스턴스에 대해 수집 된 데이터를 볼 수 있습니다. 수집 된 데이터에는 Azure 연결, 저장소 백 엔드, Express 경로 회로 ID, 고유 리소스 ID 및 구독 ID가 포함 됩니다. 이 정보는 지원 요청에 사용 하거나 저장소 스냅숏 구성을 설정할 때 사용 합니다.
 
표시 되는 또 다른 중요 한 부분은 저장소 NFS IP 주소입니다. BareMetal 인스턴스 스택에서 저장소를 **테 넌 트** 로 격리 합니다. [저장소 스냅숏 백업에 대 한 구성 파일](../virtual-machines/workloads/sap/hana-backup-restore.md#set-up-storage-snapshots)을 편집할 때이 IP 주소를 사용 합니다.
 
:::image type="content" source="media/baremetal-infrastructure-portal/baremetal-instance-properties.png" alt-text="BareMetal Instance 속성 설정을 보여 주는 스크린샷" lightbox="media/baremetal-infrastructure-portal/baremetal-instance-properties.png":::
 
## <a name="restart-a-unit-through-the-azure-portal"></a>Azure Portal를 통해 단위 다시 시작

OS가 다시 시작을 완료 하지 못하는 여러 가지 상황이 있으며,이 경우 BareMetal 인스턴스 단위를 다시 시작 해야 합니다.

### <a name="portal"></a>[포털](#tab/azure-portal)

Azure Portal에서 직접 단위의 전원을 다시 시작할 수 있습니다.
 
**다시 시작** 을 선택 하 고 **예** 를 선택 하 여 단위 다시 시작을 확인 합니다.
 
:::image type="content" source="media/baremetal-infrastructure-portal/baremetal-instance-restart.png" alt-text="BareMetal 인스턴스 단위를 다시 시작 하는 방법을 보여 주는 스크린샷":::
 
BareMetal 인스턴스 단위를 다시 시작 하면 지연이 발생 합니다. 이 지연 시간 동안 전원 상태 **는 시작부터** **시작** 까지 이동 합니다. 즉, OS가 완전히 시작 되었습니다. 결과적으로, 다시 시작한 후 상태가 **시작 됨** 으로 전환 되는 즉시 단위에 로그인 할 수 없습니다.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

BareMetal 인스턴스 단위를 다시 시작 하려면 [az baremetalinstance restart](/cli/azure/ext/baremetal-infrastructure/baremetalinstance#ext_baremetal_infrastructure_az_baremetalinstance_restart) 명령을 사용 합니다.

```azurecli
az baremetalinstance restart --resource-group DSM05a-T550 --instance-name orcllabdsm01
```

---

>[!IMPORTANT]
>BareMetal 인스턴스 단위의 메모리 양에 따라 다시 시작 하 고 하드웨어 및 운영 체제를 다시 부팅 하는 데 최대 1 시간이 걸릴 수 있습니다.
 
## <a name="open-a-support-request-for-baremetal-instances"></a>BareMetal 인스턴스에 대 한 지원 요청 열기
 
BareMetal 인스턴스 단위에 대해 특별히 지원 요청을 제출할 수 있습니다.
1. Azure Portal의 **도움말 + 지원** 에서 **[새 지원 요청](https://rc.portal.azure.com/#create/Microsoft.Support)** 을 만들고 티켓에 대 한 다음 정보를 제공 합니다.
 
   - **문제 유형:** 문제 유형 선택
 
   - **구독:** 구독 선택
 
   - **서비스:** BareMetal 인프라
 
   - **리소스:** 인스턴스의 이름을 제공 합니다.
 
   - **요약:** 요청에 대 한 요약 제공
 
   - **문제 유형:** 문제 유형 선택
 
   - **문제 하위 유형:** 문제에 대 한 하위 유형 선택

1. **솔루션 탭을** 선택 하 여 문제에 대 한 해결 방법을 찾습니다. 솔루션을 찾을 수 없는 경우 다음 단계로 이동 합니다.

1. **세부 정보** 탭을 선택 하 고 Vm 또는 BareMetal 인스턴스 단위에 문제가 있는지 여부를 선택 합니다. 이 정보를 통해 올바른 전문가에 게 지원 요청을 보낼 수 있습니다.

1. 문제가 시작 된 시간을 나타내고 인스턴스 영역을 선택 합니다.

1. 요청에 대 한 자세한 정보를 제공 하 고 필요한 경우 파일을 업로드 합니다.

1. **검토 + 만들기** 를 선택 하 여 요청을 제출 합니다.
 
지원 담당자가 요청을 확인 하는 데 최대 5 영업일이 소요 됩니다.

## <a name="next-steps"></a>다음 단계

워크 로드에 대해 자세히 알아보려면 [BareMetal 워크 로드 유형](../virtual-machines/workloads/sap/get-started.md)을 참조 하세요.