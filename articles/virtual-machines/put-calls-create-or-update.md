---
title: 만들기 또는 업데이트 작업에 대한 PUT 호출
description: 계산 리소스에 대 한 만들기 또는 업데이트 작업에 대 한 호출을 추가 합니다.
author: mimckitt
ms.author: mimckitt
ms.reviewer: cynthn
ms.topic: conceptual
ms.service: virtual-machines
ms.date: 08/4/2020
ms.custom: avverma
ms.openlocfilehash: c57025346a9f623e3fe5536b36820ea62f355cc0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91347496"
---
# <a name="put-calls-for-creation-or-updates-on-compute-resources"></a>계산 리소스에 대 한 만들기 또는 업데이트에 대 한 호출을 추가 합니다.

`Microsoft.Compute` 리소스는 *HTTP PUT* 의미 체계의 기본 정의를 지원 하지 않습니다. 대신 이러한 리소스는 PUT 및 PATCH 동사 모두에 대해 패치 의미 체계를 사용 합니다.

작업 **만들기** 는 해당 하는 경우 기본값을 적용 합니다. 그러나 PUT 또는 PATCH를 통해 수행 된 리소스 **업데이트** 는 기본 속성을 적용 하지 않습니다. **업데이트** 작업은 엄격한 패치 의미 체계 적용을 적용 합니다.

예를 들어 `caching` 가상 컴퓨터의 disk 속성은 `ReadWrite` 리소스가 OS 디스크인 경우 기본적으로로 설정 됩니다.

```json
    "storageProfile": {
      "osDisk": {
        "name": "myVMosdisk",
        "image": {
          "uri": "http://{existing-storage-account-name}.blob.core.windows.net/{existing-container-name}/{existing-generalized-os-image-blob-name}.vhd"
        },
        "osType": "Windows",
        "createOption": "FromImage",
        "caching": "ReadWrite",
        "vhd": {
          "uri": "http://{existing-storage-account-name}.blob.core.windows.net/{existing-container-name}/myDisk.vhd"
        }
      }
    },
```

그러나 속성을 유지 하거나 *null* 값을 전달 하는 경우 **업데이트** 작업의 경우에는 변경 되지 않은 상태로 유지 되며 기본값은 없습니다.

이는 연결 제거의 목적으로 업데이트 작업을 리소스로 보내는 경우에 중요 합니다. 리소스가 `Microsoft.Compute` 리소스인 경우 제거할 해당 속성을 명시적으로 호출 하 고 값을 할당 해야 합니다. 이를 위해 사용자는 **""** 와 같은 빈 문자열을 전달할 수 있습니다. 이렇게 하면 해당 연결을 제거 하도록 플랫폼에 지시할 수 있습니다.

> [!IMPORTANT]
> 배열 요소에 "패치"를 지원 하지 않습니다. 대신 클라이언트는 업데이트 된 배열의 전체 콘텐츠를 사용 하 여 PUT 또는 PATCH 요청을 수행 해야 합니다. 예를 들어 VM에서 데이터 디스크를 분리 하려면 GET 요청을 수행 하 여 현재 VM 모델을 가져오고, 분리할 디스크를 제거 하 `properties.storageProfile.dataDisks` 고, 업데이트 된 VM 엔터티를 사용 하 여 PUT 요청을 수행 합니다.

## <a name="examples"></a>예제

### <a name="correct-payload-to-remove-a-proximity-placement-groups-association"></a>근접 배치 그룹 연결을 제거 하려면 페이로드를 수정 하십시오.

`
{ "location": "westus", "properties": { "platformFaultDomainCount": 2, "platformUpdateDomainCount": 20, "proximityPlacementGroup": "" } }
`

### <a name="incorrect-payloads-to-remove-a-proximity-placement-groups-association"></a>근접 배치 그룹 연결을 제거 하는 잘못 된 페이로드

`
{ "location": "westus", "properties": { "platformFaultDomainCount": 2, "platformUpdateDomainCount": 20, "proximityPlacementGroup": null } }
`

`
{ "location": "westus", "properties": { "platformFaultDomainCount": 2, "platformUpdateDomainCount": 20 } }
`

## <a name="next-steps"></a>다음 단계
[Virtual Machines](https://docs.microsoft.com/rest/api/compute/virtualmachines/createorupdate) 및 [Virtual Machine Scale Sets](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate) 에 대 한 호출을 만들거나 업데이트 하는 방법에 대해 자세히 알아보세요.

