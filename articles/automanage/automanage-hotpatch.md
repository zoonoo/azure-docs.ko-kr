---
title: Windows Server Azure Edition 용 Hotpatch (미리 보기)
description: Windows Server Azure Edition의 Hotpatch 작동 방식 및 사용 방법에 대해 알아봅니다.
author: ju-shim
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: jushiman
ms.openlocfilehash: 710e6902be6ebe28caaf40fb446e4ee7cd2bf4dc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101687569"
---
# <a name="hotpatch-for-new-virtual-machines-preview"></a>새 가상 컴퓨터의 Hotpatch (미리 보기)

핫 패치는 설치 후 다시 부팅 하지 않아도 되는 새 Windows Server Azure Edition Vm (가상 머신)에 업데이트를 설치 하는 새로운 방법입니다. 이 문서에서는 다음과 같은 이점을 제공 하는 Windows Server Azure Edition Vm에 대 한 Hotpatch에 대 한 정보를 다룹니다.
* 낮은 다시 부팅으로 인 한 워크 로드 효과
* 패키지가 더 작고, 더 빠르게 설치 되며, Azure 업데이트 관리자를 사용 하 여 더 쉽게 패치를 배포할 수 있는 업데이트를 빠르게 배포할 수 있습니다.
* Hotpatch 업데이트 패키지의 범위가 다시 부팅 없이 빠르게 설치 되는 Windows 보안 업데이트로 범위가 향상 됨에 따라 보호 기능이 향상 되었습니다.

## <a name="how-hotpatch-works"></a>Hotpatch 작동 방식

Hotpatch은 먼저 Windows 업데이트 최신 누적 업데이트를 사용 하 여 기준선을 설정 합니다. Hotpatches는 해당 기준선을 기반으로 하는 해당 월의 두 번째 화요일에 정기적으로 릴리스됩니다. Hotpatches에는 다시 부팅 하지 않아도 되는 업데이트가 포함 됩니다. 정기적으로 (3 개월 마다 시작) 새로운 최신 누적 업데이트를 사용 하 여 기준이 새로 고쳐집니다.

:::image type="content" source="media\automanage-hotpatch\hotpatch-sample-schedule.png" alt-text="Hotpatch 샘플 일정.":::

**계획** 된 기준과 계획 되지 **않은 기준** 이라는 두 가지 기준 유형이 있습니다.
*  **계획 된 기준은** hotpatch 릴리스가 포함 된 일반 흐름에 릴리스됩니다.  계획 된 기준에는 해당 월에 대 한 유사 _최신 누적 업데이트_ 의 모든 업데이트가 포함 되며 다시 부팅 해야 합니다.
    * 위의 샘플 일정은 달력 연도 (다이어그램의 총 5 개)와 8 개의 hotpatch 릴리스에 대 한 계획 된 기본 릴리스 4 개를 보여 줍니다.
* **계획** 되지 않은 기준은 중요 한 업데이트 (예: 0 일 수정)가 출시 되 고 특정 업데이트를 Hotpatch 해제할 수 없을 때 릴리스됩니다.  계획 되지 않은 기준선이 릴리스되는 경우 hotpatch 릴리스가 해당 월에 계획 되지 않은 기준으로 교체 됩니다.  계획 되지 않은 기준에는 해당 월에 대 한 유사 _최신 누적 업데이트_ 의 모든 업데이트가 포함 되며 재부팅도 필요 합니다.
    * 위의 샘플 일정은 해당 월에 대 한 hotpatch 릴리스를 대체 하는 계획 되지 않은 두 개의 기준선을 보여 줍니다. 1 년에 계획 되지 않은 기준선의 실제 수는 미리 알려지지 않습니다.

## <a name="regional-availability"></a>지역별 가용성
Hotpatch는 미리 보기의 모든 글로벌 Azure 지역에서 사용할 수 있습니다. Azure Government 지역은 미리 보기에서 지원 되지 않습니다.

## <a name="how-to-get-started"></a>시작하는 방법

> [!NOTE]
> 미리 보기 단계 중에는 [이 링크](https://aka.ms/AzureAutomanageHotPatch)를 사용 하 여 Azure Portal를 시작할 수 있습니다.

새 VM에서 Hotpatch 사용을 시작 하려면 다음 단계를 수행 합니다.
1.  미리 보기 액세스 사용
    * 구독 당 일회성 미리 보기 액세스를 사용 해야 합니다.
    * 미리 보기 액세스는 다음 섹션에 설명 된 대로 API, PowerShell 또는 CLI를 통해 사용 하도록 설정할 수 있습니다.
1.  Azure Portal에서 VM 만들기
    * 미리 보기 중에는 [이 링크](https://aka.ms/AzureAutomanageHotPatch)를 사용 하 여 시작 해야 합니다.
1.  VM 세부 정보 제공
    * 이미지 드롭다운에서 _Windows Server 2019 Datacenter: Azure Edition_ 이 선택 되어 있는지 확인 합니다.
    * 관리 탭 단계에서 ' 게스트 OS 업데이트 ' 섹션까지 아래로 스크롤합니다. 핫패칭을 On으로 설정 하 고 패치 설치를 Azure-오케스트레이션 Patch로 설정 하는 것을 볼 수 있습니다.
    * VM 자동 관리 모범 사례는 기본적으로 사용 하도록 설정 됩니다.
1. 새 VM 만들기

## <a name="enabling-preview-access"></a>미리 보기 액세스 사용

### <a name="rest-api"></a>REST API

다음 예제에서는 구독에 대해 미리 보기를 사용 하도록 설정 하는 방법을 설명 합니다.

```
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/ InGuestHotPatchVMPreview/register?api-version=2015-12-01`
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestAutoPatchVMPreview/register?api-version=2015-12-01`
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestPatchVMPreview/register?api-version=2015-12-01`
```

기능 등록에는 최대 15 분이 걸릴 수 있습니다. 등록 상태를 확인하는 방법은 다음과 같습니다.

```
GET on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestHotPatchVMPreview?api-version=2015-12-01`
GET on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestAutoPatchVMPreview?api-version=2015-12-01`
GET on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestPatchVMPreview?api-version=2015-12-01`
```

구독에 대 한 기능을 등록 한 후에는 계산 리소스 공급자에 변경 내용을 전파 하 여 옵트인 프로세스를 완료 합니다.

```
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Compute/register?api-version=2019-12-01`
```

### <a name="azure-powershell"></a>Azure PowerShell

Cmdlet을 사용 ```Register-AzProviderFeature``` 하 여 구독에 대 한 미리 보기를 사용 하도록 설정 합니다.

``` PowerShell
Register-AzProviderFeature -FeatureName InGuestHotPatchVMPreview -ProviderNamespace Microsoft.Compute
Register-AzProviderFeature -FeatureName InGuestAutoPatchVMPreview -ProviderNamespace Microsoft.Compute
Register-AzProviderFeature -FeatureName InGuestPatchVMPreview -ProviderNamespace Microsoft.Compute
```

기능 등록에는 최대 15 분이 걸릴 수 있습니다. 등록 상태를 확인하는 방법은 다음과 같습니다.

``` PowerShell
Get-AzProviderFeature -FeatureName InGuestHotPatchVMPreview -ProviderNamespace Microsoft.Compute
Get-AzProviderFeature -FeatureName InGuestAutoPatchVMPreview -ProviderNamespace Microsoft.Compute
Get-AzProviderFeature -FeatureName InGuestPatchVMPreview -ProviderNamespace Microsoft.Compute
```

구독에 대 한 기능을 등록 한 후에는 계산 리소스 공급자에 변경 내용을 전파 하 여 옵트인 프로세스를 완료 합니다.

``` PowerShell
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
```

### <a name="azure-cli"></a>Azure CLI

```az feature register```를 사용 하 여 구독에 대 한 미리 보기를 사용 하도록 설정 합니다.

```
az feature register --namespace Microsoft.Compute --name InGuestHotPatchVMPreview
az feature register --namespace Microsoft.Compute --name InGuestAutoPatchVMPreview
az feature register --namespace Microsoft.Compute --name InGuestPatchVMPreview
```

기능 등록에는 최대 15 분이 걸릴 수 있습니다. 등록 상태를 확인하는 방법은 다음과 같습니다.
```
az feature show --namespace Microsoft.Compute --name InGuestHotPatchVMPreview
az feature show --namespace Microsoft.Compute --name InGuestAutoPatchVMPreview
az feature show --namespace Microsoft.Compute --name InGuestPatchVMPreview
```

구독에 대 한 기능을 등록 한 후에는 계산 리소스 공급자에 변경 내용을 전파 하 여 옵트인 프로세스를 완료 합니다.

```
az provider register --namespace Microsoft.Compute
```

## <a name="patch-installation"></a>패치 설치

미리 보기 중에는 _Windows Server 2019 Datacenter: Azure Edition_ 을 사용 하 여 만든 모든 vm에 대해 [자동 VM 게스트 패치가](https://docs.microsoft.com/azure/virtual-machines/automatic-vm-guest-patching) 자동으로 설정 됩니다. 자동 VM 게스트 패치를 사용 하는 경우:
* 중요 또는 보안으로 분류 된 패치는 자동으로 다운로드 되어 VM에 적용 됩니다.
* 패치는 VM의 표준 시간대에서 사용량이 적은 시간에 적용 됩니다.
* 패치 오케스트레이션은 Azure에 의해 관리 되며, 패치는 [가용성 우선 원칙](https://docs.microsoft.com/azure/virtual-machines/automatic-vm-guest-patching#availability-first-patching)에 따라 적용 됩니다.
* 플랫폼 상태 신호를 통해 결정 되는 가상 머신 상태는 패치 실패를 감지 하기 위해 모니터링 됩니다.

### <a name="how-does-automatic-vm-guest-patching-work"></a>자동 VM 게스트 패치는 어떻게 작동 하나요?

VM에서 [자동 Vm 게스트 패치](https://docs.microsoft.com/azure/virtual-machines/automatic-vm-guest-patching) 를 사용 하도록 설정 하면 사용 가능한 중요 및 보안 패치가 자동으로 다운로드 되 고 적용 됩니다. 이 프로세스는 새 패치가 출시 될 때마다 매월 자동으로 시작 됩니다. 패치 평가 및 설치는 자동으로 수행 되며, 필요에 따라이 프로세스에는 VM을 다시 부팅 하는 작업이 포함 됩니다.

_Windows Server 2019 Datacenter_ 에서 Hotpatch을 사용 하는 경우: Azure 버전 vm, 대부분의 월간 보안 업데이트는 다시 부팅이 필요 하지 않은 hotpatches로 제공 됩니다. 계획 되거나 계획 되지 않은 기준 월에 전송 된 최신 누적 업데이트는 VM을 다시 부팅 해야 합니다. VM을 다시 부팅 해야 할 수도 있는 추가 중요 또는 보안 패치를 정기적으로 사용할 수도 있습니다.

VM은 해당 VM에 대해 적용 가능한 패치를 결정 하기 위해 며칠 마다 자동으로 평가 되 고 30 일 동안 여러 번 평가 됩니다. 이러한 자동 평가를 통해 누락 된 패치가 가능한 가장 빠른 기회에서 검색 됩니다.

패치는 [가용성 우선 원칙](https://docs.microsoft.com/azure/virtual-machines/automatic-vm-guest-patching#availability-first-patching)에 따라 월별 패치 릴리스의 30 일 이내에 설치 됩니다. 패치는 vm의 표준 시간대에 따라 VM에 대 한 사용률이 낮은 시간에만 설치 됩니다. 패치를 자동으로 설치 하는 데 사용량이 많은 시간 동안 VM이 실행 중 이어야 합니다. 정기적으로 평가 하는 동안 VM의 전원이 꺼진 경우 vm이 평가 되 고 해당 패치가 켜 졌을 때 다음 주기적 평가 중에 적용 가능한 패치가 자동으로 설치 됩니다. 다음 주기적 평가는 일반적으로 며칠 이내에 발생 합니다.

중요 또는 보안으로 분류 되지 않은 정의 업데이트 및 기타 패치는 자동 VM 게스트 패치를 통해 설치 되지 않습니다.

## <a name="understanding-the-patch-status-for-your-vm"></a>VM에 대 한 패치 상태 이해

VM에 대 한 패치 상태를 보려면 Azure Portal에서 VM에 대 한 **게스트 + 호스트 업데이트** 섹션으로 이동 합니다. **게스트 OS 업데이트** 섹션에서 ' Hotpatch (미리 보기)로 이동 '을 클릭 하 여 VM에 대 한 최신 패치 상태를 확인 합니다.

이 화면에 VM에 대 한 Hotpatch 상태가 표시 됩니다. 또한 설치 되지 않은 VM에 대 한 사용 가능한 패치가 있는지 검토할 수 있습니다. 위의 ' 패치 설치 ' 섹션에 설명 된 대로 모든 보안 및 중요 업데이트는 [자동 Vm 게스트 패치](https://docs.microsoft.com/azure/virtual-machines/automatic-vm-guest-patching) 를 사용 하 여 vm에 자동으로 설치 되며 추가 작업이 필요 하지 않습니다. 다른 업데이트 분류가 포함 된 패치는 자동으로 설치 되지 않습니다. 대신 ' 호환성 업데이트 ' 탭의 사용 가능한 패치 목록에서 볼 수 있습니다. ' 업데이트 기록 '을 통해 VM에서 업데이트 배포 기록을 볼 수도 있습니다. 최근 30 일 동안의 업데이트 기록이 패치 설치 세부 정보와 함께 표시 됩니다.


:::image type="content" source="media\automanage-hotpatch\hotpatch-management-ui.png" alt-text="Hotpatch 관리.":::

자동 VM 게스트 패치를 사용 하면 VM이 주기적으로 사용 가능한 업데이트를 자동으로 평가 합니다. 이러한 정기 평가는 사용 가능한 패치가 검색 되는지 확인 합니다. 위의 업데이트 화면에서 마지막 평가 시간을 포함 하 여 평가 결과를 볼 수 있습니다. ' 지금 평가 ' 옵션을 사용 하 여 언제 든 지 VM에 대 한 주문형 패치 평가를 트리거하고 평가를 완료 한 후에 결과를 검토 하도록 선택할 수도 있습니다.

주문형 평가와 마찬가지로 ' 지금 업데이트 설치 ' 옵션을 사용 하 여 VM에 대 한 주문형 패치를 설치할 수도 있습니다. 여기서 특정 패치 분류의 모든 업데이트를 설치 하도록 선택할 수 있습니다. 개별 기술 자료 문서 목록을 제공 하 여 포함 하거나 제외할 업데이트를 지정할 수도 있습니다. 요청 시에 설치 된 패치는 가용성 우선 원칙을 사용 하 여 설치 되지 않으며 업데이트 설치를 위한 더 많은 재부팅 및 VM 가동 중지 시간이 필요할 수 있습니다.

## <a name="supported-updates"></a>지원 되는 업데이트

Hotpatch는 Windows 보안 업데이트를 다루며, 일반 (비-Hotpatch) Windows 업데이트 채널에서에 발급 된 보안 업데이트의 콘텐츠로 패리티를 유지 관리 합니다.

Hotpatch가 사용 하도록 설정 된 Windows Server Azure edition VM을 실행 하려면 몇 가지 중요 한 사항을 고려해 야 합니다. Hotpatch 프로그램에 포함 되지 않은 업데이트를 설치 하려면 다시 부팅 해야 합니다. 새 기준을 설치한 후에도 다시 부팅이 정기적으로 필요 합니다. 이러한 재부팅을 통해 VM은 최신 누적 업데이트에 포함 된 비보안 패치와 동기화 된 상태로 유지 됩니다.
* 현재 Hotpatch 프로그램에 포함 되지 않은 패치에는 Windows 용으로 릴리스된 비보안 업데이트 및 비 Windows 업데이트 (예: .NET 패치)가 포함 됩니다.  이러한 유형의 패치는 기준 월에 설치 해야 하며 다시 부팅 해야 합니다.

## <a name="frequently-asked-questions"></a>질문과 대답

### <a name="what-is-hotpatching"></a>핫패칭 이란?

* 핫패칭은 Windows Server 2019 Datacenter: azure에서 설치 후 다시 부팅할 필요가 없는 Azure Edition VM에 업데이트를 설치 하는 새로운 방법입니다. 프로세스를 다시 시작할 필요 없이 실행 중인 프로세스의 메모리 내 코드를 패치 하는 방식으로 작동 합니다.

### <a name="how-does-hotpatching-work"></a>핫패칭을 어떻게 작동 하나요?

* 핫 패치는 Windows 업데이트 최신 누적 업데이트를 사용 하 여 기준선을 설정 하 고 나 서 다시 부팅이 필요 하지 않은 업데이트를 사용 하 여 해당 초기 계획을 작성 하는 방식으로 작동 합니다.  기준은 새 누적 업데이트를 사용 하 여 주기적으로 업데이트 됩니다. 누적 업데이트에는 모든 보안 및 품질 업데이트가 포함 되며, 다시 부팅 해야 합니다.

### <a name="why-should-i-use-hotpatch"></a>Hotpatch를 사용 해야 하는 이유는 무엇 인가요?

* Windows Server 2019 Datacenter: Azure Edition에서 Hotpatch를 사용 하는 경우 VM은 더 높은 가용성 (더 적은 수의 재부팅)을 제공 하 고 더 빠른 업데이트 (프로세스를 다시 시작 하지 않고 더 빠르게 설치 되는 더 작은 패키지)를 제공 합니다. 이 프로세스는 항상 최신 상태로 유지 되는 VM을 생성 합니다.

### <a name="what-types-of-updates-are-covered-by-hotpatch"></a>Hotpatch에서 적용 되는 업데이트 유형은 무엇 인가요?

* Hotpatch에는 현재 Windows 보안 업데이트가 포함 되어 있습니다.

### <a name="when-will-i-receive-the-first-hotpatch-update"></a>첫 번째 Hotpatch 업데이트는 언제 받게 되나요?

* Hotpatch 업데이트는 일반적으로 매월 두 번째 화요일에 릴리스됩니다. 자세한 내용은 아래를 참조하세요.

### <a name="what-will-the-hotpatch-schedule-look-like"></a>Hotpatch 일정은 어떤 모양 인가요?

* 핫 패치는 Windows 업데이트 최신 누적 업데이트를 사용 하 여 기준선을 설정한 다음 매월 릴리스된 Hotpatch 업데이트를 사용 하 여 해당 기준선을 기반으로 합니다.  미리 보기 중에는 3 개월 마다 시작 하는 기준이 출시 됩니다. 연간 3 개월 일정 (0 일 수정으로 인해 계획 되지 않은 기준 예제 포함)의 예는 아래 이미지를 참조 하세요.

    :::image type="content" source="media\automanage-hotpatch\hotpatch-sample-schedule.png" alt-text="Hotpatch 샘플 일정.":::

### <a name="are-reboots-still-needed-for-a-vm-enrolled-in-hotpatch"></a>Hotpatch에 등록 된 VM에 대해서도 다시 부팅이 필요 한가요?

* Hotpatch 프로그램에 포함 되지 않은 업데이트를 설치 하는 경우에도 다시 부팅이 필요 하며, 기준 (최신 누적 업데이트 Windows 업데이트)이 설치 된 후 주기적으로 필요 합니다. 이렇게 다시 부팅 하면 VM이 누적 업데이트에 포함 된 모든 패치와 동기화 된 상태로 유지 됩니다. 다시 부팅 해야 하는 기준은 3 개월 주기로 시작 되며 시간이 지남에 따라 증가 합니다.

### <a name="are-my-applications-affected-when-a-hotpatch-update-is-installed"></a>Hotpatch 업데이트가 설치 될 때 내 응용 프로그램이 영향을 받습니까?

* Hotpatch는 프로세스를 다시 시작할 필요 없이 실행 중인 프로세스의 메모리 내 코드를 패치 하므로 응용 프로그램은 패치 프로세스의 영향을 받지 않습니다. 이는 패치 자체의 잠재적인 성능 및 기능 영향과 구분 됩니다.

### <a name="can-i-turn-off-hotpatch-on-my-vm"></a>내 VM에서 Hotpatch를 끌 수 있나요?

* Azure Portal를 통해 VM에서 Hotpatch를 해제할 수 있습니다.  Hotpatch을 해제 하면 Hotpatch에서 VM의 등록을 취소 하 여 Windows Server에 대 한 일반적인 업데이트 동작으로 VM을 되돌립니다.  VM에서 Hotpatch의 등록을 취소 한 후에는 다음 Hotpatch 기준이 출시 될 때 해당 VM을 다시 등록할 수 있습니다.

### <a name="can-i-upgrade-from-my-existing-windows-server-os"></a>기존 Windows Server OS에서 업그레이드할 수 있나요?

* 기존 버전의 Windows Server에서 업그레이드 하는 경우 (즉, Windows Server 2016 또는 2019 비 Azure 버전) 현재 지원 되지 않습니다. 이후 버전의 Windows Server Azure 버전으로 업그레이드 하는 것이 지원 됩니다.

### <a name="can-i-use-hotpatch-for-production-workloads-during-the-preview"></a>미리 보기 중에 프로덕션 작업에 Hotpatch를 사용할 수 있나요?

* 미리 보기는 테스트 목적 으로만 제공 되며 프로덕션 환경에서 사용 하기 위한 것이 아닙니다.

### <a name="will-i-be-charged-during-the-preview"></a>미리 보기 기간 중에 요금이 청구 되나요?

* Windows Server Azure 버전에 대 한 라이선스는 미리 보기 기간 동안 무료입니다. 그러나 VM에 설정 된 기본 인프라 (저장소, 계산, 네트워킹 등)의 비용은 여전히 구독에 청구 됩니다.

### <a name="how-can-i-get-troubleshooting-support-for-hotpatching"></a>핫패칭에 대 한 문제 해결 지원을 받으려면 어떻게 해야 하나요?

* [기술 지원 사례 티켓](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)을 입력할 수 있습니다. 서비스 옵션에 대해 계산에서 Windows를 실행 하는 **가상 컴퓨터** 를 검색 하 고 선택 합니다. 문제 유형에 대해 **Azure 기능** 을 선택 하 고 문제 하위 유형에 대해 **자동 VM 게스트 패치** 를 선택 합니다.

## <a name="next-steps"></a>다음 단계

* Azure 업데이트 관리에 대 한 자세한 내용은 [여기](https://docs.microsoft.com/azure/automation/update-management/overview)를 참조 하세요.
* [여기](https://docs.microsoft.com/azure/virtual-machines/automatic-vm-guest-patching) 에서 자동 VM 게스트 패치에 대해 자세히 알아보세요.