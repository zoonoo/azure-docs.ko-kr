---
title: 배치 풀 및 노드 오류 확인
description: 풀과 노드를 만들 때 확인해야 하는 오류와 오류를 방지하는 방법에 대해 알아봅니다.
services: batch
author: mscurrell
ms.author: markscu
ms.date: 9/25/2018
ms.topic: conceptual
ms.openlocfilehash: bc09089fa9984e9042166938da19499afca21509
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/28/2018
ms.locfileid: "47435831"
---
# <a name="checking-for-pool-and-node-errors"></a>풀 및 노드 오류 확인

배치 풀을 만들고 관리할 때는 즉시 실행되는 작업도 있고, 즉시 실행되지 않는 비동기 작업도 있습니다. 이러한 비동기 작업은 백그라운드에서 실행되며 완료하려면 몇 분 정도 걸릴 수 있습니다.

즉시 실행되는 작업에서는 오류를 쉽게 검색할 수 있습니다. API, CLI 또는 UI에서 오류가 즉시 반환되기 때문입니다.

이 문서에서는 풀 및 풀 노드에 대해 수행될 수 있는 백그라운드 작업과 오류를 검색 및 방지할 수 있는 방법을 설명합니다.

## <a name="pool-errors"></a>풀 오류

### <a name="resize-timeout-or-failure"></a>크기 조정 시간 제한 또는 오류

새 풀을 만들거나 기존 풀의 크기를 조정할 때는 노드의 대상 수를 지정합니다.  해당 작업은 즉시 완료되지만, 새 노드의 실제 할당이나 기존 노드의 제거는 백그라운드에서 수행되며 몇 분 정도 걸릴 수 있습니다.  크기 조정 시간 제한은 [create](https://docs.microsoft.com/rest/api/batchservice/pool/add) 또는 [resize](https://docs.microsoft.com/rest/api/batchservice/pool/resize) API에서 지정합니다. 크기 조정 시간 제한 동안 대상 수에 해당하는 노드를 가져올 수 없으면 작업은 중지되고 풀은 안정 상태가 되며 크기 조정 오류가 발생합니다.

[ResizeError](https://docs.microsoft.com/rest/api/batchservice/pool/get#resizeerror) 속성이 마지막 평가의 크기 조정 시간 제한을 보고합니다. 이 속성에는 발생한 오류가 하나 이상 나열됩니다.

크기 조정 시간 제한 관련 오류가 발생하는 일반적인 원인은 다음과 같습니다.
- 크기 조정 시간 제한이 너무 짧음
  - 기본 시간 제한인 15분은 대개 풀 노드를 할당하거나 제거하기에 충분한 시간입니다.
  - 하지만 Marketplace 이미지에서 1,000개가 넘는 노드를 할당하거나 사용자 지정 이미지에서 300개가 넘는 노드를 할당하는 등 많은 수의 노드를 할당할 때는 풀 만들기 또는 크기 조정 중에 시간 제한을 30분으로 설정하는 것이 좋습니다.
- 코어 할당량 부족
  - 배치 계정에는 모든 풀에 할당할 수 있는 코어 수에 대한 할당량이 있습니다.  해당 할당량에 도달하면 배치에서 노드가 할당되지 않습니다.  코어 할당량을 [늘리면](https://docs.microsoft.com/azure/batch/batch-quota-limit) 노드를 더 할당할 수 있습니다.
- [풀이 가상 네트워크에 있을 때](https://docs.microsoft.com/azure/batch/batch-virtual-network) 서브넷 IP가 부족함
  - 가상 네트워크 서브넷에는 요청 중인 모든 풀 노드에 할당하기에 충분한 미할당 IP 주소가 있어야 합니다. 그렇지 않으면 노드를 만들 수 없습니다.
- [풀이 가상 네트워크에 있을 때](https://docs.microsoft.com/azure/batch/batch-virtual-network) 리소스가 부족함
  - 배치 계정을 만드는 데 사용되는 구독에서는 부하 분산 장치, 공용 IP, NSG 등의 리소스가 생성됩니다.  이러한 리소스에 대한 구독 할당량이 충분해야 합니다.
- 큰 풀에 사용자 지정 VM 이미지 사용
  - 큰 풀이 사용자 지정 이미지를 사용하는 경우 할당 시간이 더 오래 걸릴 수 있으며 크기 조정 오류가 발생할 수 있습니다.  제한 및 구성 권장 사항은 [이 문서](https://docs.microsoft.com/azure/batch/batch-custom-images)에서 제공됩니다. 

### <a name="auto-scale-failures"></a>자동 크기 조정 오류

풀 만들기 또는 크기 조정에서 풀의 대상 노드 수를 명시적으로 설정하는 대신 풀의 노드 수를 자동으로 조정할 수 있습니다.  이렇게 하려면 [풀에 대해 자동 크기 조정 수식을 작성](https://docs.microsoft.com/azure/batch/batch-automatic-scaling)할 수 있습니다. 그러면 구성 가능한 규칙적인 간격으로 이 수식을 평가하여 풀의 대상 노드 수를 설정합니다.  이 과정에서 발생 가능하며 검색되는 문제는 다음과 같습니다.

- 자동 크기 조정 평가가 실패할 수 있습니다.
- 평가 결과에 따른 크기 조정 작업이 실패하고 시간이 초과될 수 있습니다.
- 자동 크기 조정 수식에 문제가 있어 노드 대상 값이 잘못 계산될 수 있으며, 그러면 크기 조정이 작동하지 않거나 시간이 초과됩니다.

[autoScaleRun](https://docs.microsoft.com/rest/api/batchservice/pool/get#autoscalerun) 속성을 사용하여 마지막 자동 크기 조정 평가에 대한 정보를 가져올 수 있습니다. 이 속성은 평가 시간, 평가 값/결과 및 평가 수행 시에 발생한 오류를 보고합니다.

모든 평가에 대한 정보는 [풀 크기 조정 완료 이벤트](https://docs.microsoft.com/azure/batch/batch-pool-resize-complete-event)를 통해 자동으로 캡처됩니다.

### <a name="delete"></a>삭제

풀에 노드가 있다고 가정할 때 풀 삭제 작업을 수행하면 노드가 먼저 삭제된 다음 풀 개체 자체가 삭제됩니다.  풀 노드가 삭제되려면 몇 분 정도 걸릴 수 있습니다.

삭제 프로세스 중에는 [풀 상태](https://docs.microsoft.com/rest/api/batchservice/pool/get#poolstate)가 ‘deleting’으로 설정됩니다.  호출 응용 프로그램은 ‘state’ 및 ‘stateTransitionTime’ 속성을 사용하여 풀 삭제가 너무 오래 걸리는지를 검색할 수 있습니다.

## <a name="pool-compute-node-errors"></a>풀 계산 노드 오류

노드를 풀에 정상적으로 할당할 수는 있는데 여러 가지 문제로 인해 노드가 비정상 상태가 되어 사용하지 못하게 될 수도 있습니다.  풀에 할당된 노드에는 요금이 부과되므로, 사용할 수 없는 노드에 대해 비용을 지불하는 일이 없도록 문제를 검색해야 합니다.

### <a name="start-task-failure"></a>시작 작업 실패

풀에 대해 선택적인 [시작 작업](https://docs.microsoft.com/rest/api/batchservice/pool/add#starttask)을 지정할 수 있으며,  일반적인 작업과 마찬가지로 저장소에서 다운로드할 리소스 파일과 명령줄을 지정할 수 있습니다.  시작 작업은 풀에 대해 지정되지만 각 노드에서 실행됩니다. 각 노드를 시작하고 나면 시작 작업이 실행됩니다.  [시작 작업](https://docs.microsoft.com/rest/api/batchservice/pool/add#starttask)의 추가 속성인 'waitForSuccess'는 배치가 노드에 대한 작업을 예약하기 전에 시작 작업이 정상 완료될 때까지 기다려야 하는지 여부를 지정합니다.

시작 작업이 정상 완료될 때까지 기다리도록 시작 작업 구성을 지정했는데 시작 작업이 실패하면 해당 노드를 사용할 수 없게 됩니다. 하지만 노드 요금은 계속 청구됩니다.

최상위 [startTaskInfo](https://docs.microsoft.com/rest/api/batchservice/computenode/get#starttaskinformation) 노드 속성의 [result](https://docs.microsoft.com/rest/api/batchservice/computenode/get#taskexecutionresult) 및 [failureInfo](https://docs.microsoft.com/rest/api/batchservice/computenode/get#taskfailureinformation) 속성을 사용하여 시작 작업 실패를 검색할 수 있습니다.

'waitForSuccess'가 'true'로 설정된 경우에 한해 시작 작업이 실패하면 노드 [상태](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodestate)도 'starttaskfailed'로 설정됩니다.

일반적인 작업과 마찬가지로 시작 작업 역시 여러 가지 원인으로 인해 실패할 수 있습니다.  문제를 해결하려면 stdout, stderr 및 추가 작업 관련 로그 파일을 확인해야 합니다.

### <a name="application-package-download-failure"></a>응용 프로그램 패키지 다운로드 실패

필요에 따라 풀용으로 응용 프로그램 패키지를 하나 이상 지정할 수 있습니다. 지정한 패키지는 노드가 시작된 후 작업을 예약하기 전에 각 노드에 다운로드되어 압축이 해제됩니다.  일반적으로는 응용 프로그램 패키지와 함께 시작 작업 명령줄을 사용하여 다른 위치에 파일을 복사하거나 설치를 실행하는 등의 방식을 사용합니다.

노드 [errors](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodeerror) 속성이 응용 프로그램 패키지 다운로드 및 압축 풀기 실패를 보고합니다.  이 경우 노드 상태는 'unusable'로 설정됩니다.

### <a name="node-in-unusable-state"></a>unusable 상태의 노드

[노드 상태](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodestate)는 여러 가지 이유로 인해 ‘unusable’로 설정될 수 있습니다.  ‘unusable’ 상태인 노드에는 작업을 예약할 수 없지만, 해당 노드에 대해 요금은 계속 부과됩니다.

배치는 항상 사용할 수 없는 노드의 복구를 시도하지만 복구 가능 여부는 원인에 따라 달라집니다.

확인 가능한 원인은 노드 [error](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodeerror) 속성을 통해 보고됩니다.

그 외에 노드가 ‘unusable’ 상태로 설정되는 원인의 몇 가지 예는 다음과 같습니다.

- 잘못된 사용자 지정 이미지(예: 사용자 지정 이미지가 올바르게 준비되지 않음)
- 인프라 오류 또는 하위 수준 업그레이드로 인해 기본 VM이 이동됨(배치에서 해당 노드를 복구함)

### <a name="node-agent-log-files"></a>노드 에이전트 로그 파일

풀 노드 문제 관련 지원을 문의해야 하는 경우에는 각 풀 노드에서 실행되는 배치 에이전트 프로세스의 로그 파일을 가져올 수 있습니다.  Azure Portal, Batch Explorer 또는 [API](https://docs.microsoft.com/rest/api/batchservice/computenode/uploadbatchservicelogs)를 통해 노드의 로그 파일을 업로드할 수 있습니다.  실행 중인 노드 비용을 절약하기 위해 노드나 풀이 삭제될 수 있으므로, 로그 파일을 업로드 및 저장하면 매우 유용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

응용 프로그램이 문제를 빠르게 검색하고 진단할 수 있도록 특히 비동기 작업에 대해 포괄적인 오류 검사를 구현했는지 확인합니다.
