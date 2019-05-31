---
title: 풀 및 노드 오류 확인 - Azure Batch
description: 풀과 노드를 만들 때 확인해야 하는 오류와 오류를 방지하는 방법에 대해 알아봅니다.
services: batch
ms.service: batch
author: mscurrell
ms.author: markscu
ms.date: 05/28/2019
ms.topic: conceptual
ms.openlocfilehash: b0a9d04fccce7ccbacb700f7af5126c6ae05140a
ms.sourcegitcommit: 8e76be591034b618f5c11f4e66668f48c090ddfd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/29/2019
ms.locfileid: "66357771"
---
# <a name="check-for-pool-and-node-errors"></a>풀 및 노드 오류 확인

Azure Batch 풀을 만들고 관리할 때 일부 작업은 즉시 수행됩니다. 그러나 일부 작업은 비동기적으로 백그라운드에서 실행됩니다. 이러한 작업은 완료하는 데 몇 분 정도 걸릴 수 있습니다.

즉시 실행되는 작업에서는 오류를 쉽게 검색할 수 있습니다. API, CLI 또는 UI에서 오류가 즉시 반환되기 때문입니다.

이 문서에서는 풀 및 풀 노드에 대해 발생할 수 있는 백그라운드 작업을 설명합니다. 또한 오류를 감지하고 피하는 방법을 지정합니다.

## <a name="pool-errors"></a>풀 오류

### <a name="resize-timeout-or-failure"></a>크기 조정 시간 제한 또는 오류

새 풀을 만들거나 기존 풀의 크기를 조정할 때는 노드의 대상 수를 지정합니다.  작업은 즉시 완료되지만 새 노드의 실제 할당 또는 기존 노드의 제거에는 몇 분 정도 걸릴 수 있습니다.  [create](https://docs.microsoft.com/rest/api/batchservice/pool/add) 또는 [resize](https://docs.microsoft.com/rest/api/batchservice/pool/resize) API에서 크기 조정 시간 제한을 지정합니다. Batch는 크기 조정 시간 제한 기간 동안 목표 노드 수를 획득할 수 없으면 작업을 중지합니다. 풀은 안정적인 상태가 되고 크기 조정 오류를 보고합니다.

가장 최근 평가의 [ResizeError](https://docs.microsoft.com/rest/api/batchservice/pool/get#resizeerror) 속성은 크기 조정 시간 제한을 보고하고 발생한 오류를 나열합니다.

크기 조정 시간 제한 관련 오류가 발생하는 일반적인 원인은 다음과 같습니다.

- 크기 조정 시간 제한이 너무 짧음
  - 대부분의 경우 기본 시간 제한인 15분은 풀 노드를 할당하거나 제거할 수 있도록 충분히 깁니다.
  - 많은 수의 노드를 할당하는 경우에는 크기 조정 시간 제한을 30분으로 설정하는 것이 좋습니다. 예를 들어, 1개의 Azure Marketplace 이미지에서 1,000개가 넘는 노드로 크기를 조정하거나 1개의 사용자 지정 VM 이미지에서 300개가 넘는 노드로 크기를 조정할 수 있습니다.
- 코어 할당량 부족
  - Batch 계정은 모든 풀에서 할당할 수 있는 코어 수가 제한됩니다. 해당 할당량에 도달하면 Batch의 노드 할당이 중지됩니다. Batch에서 더 많은 노드를 할당할 수 있도록 코어 할당량을 [늘릴 수 있습니다](https://docs.microsoft.com/azure/batch/batch-quota-limit).
- [풀이 가상 네트워크에 있을 때](https://docs.microsoft.com/azure/batch/batch-virtual-network) 서브넷 IP가 부족함
  - 가상 네트워크 서브넷은 요청한 모든 풀 노드에 할당할 수 있는 충분한 미할당 IP 주소가 있어야 합니다. 그렇지 않으면 노드를 만들 수 없습니다.
- [풀이 가상 네트워크에 있을 때](https://docs.microsoft.com/azure/batch/batch-virtual-network) 리소스가 부족함
  - Batch 계정과 동일한 구독에 부하 분산 장치, 공용 IP 및 네트워크 보안 그룹과 같은 리소스를 만들 수 있습니다. 구독 할당량이 이러한 리소스에 대해 충분한지 확인합니다.
- 사용자 지정 VM 이미지를 사용하는 큰 풀
  - 사용자 지정 VM 이미지를 사용하는 큰 풀은 할당 시간이 더 오래 걸릴 수 있으며 크기 조정 오류가 발생할 수 있습니다.  제한 및 구성에 대한 권장 사항을 보려면 [사용자 지정 이미지를 사용하여 가상 머신 풀 만들기](https://docs.microsoft.com/azure/batch/batch-custom-images)를 참조하세요.

### <a name="automatic-scaling-failures"></a>자동 크기 조정 실패

풀의 노드 수를 자동으로 조정하도록 Azure Batch를 설정할 수도 있습니다. [풀의 자동 크기 조정 수식](https://docs.microsoft.com/azure/batch/batch-automatic-scaling)에 대한 매개 변수를 정의합니다. Batch 서비스는 이 수식을 사용하여 풀의 노드 수를 정기적으로 평가하고 새 목표 개수를 설정합니다. 다음과 같은 유형의 문제가 발생할 수 있습니다.

- 자동 크기 조정 평가가 실패합니다.
- 평가 결과에 따른 크기 조정 작업이 실패하고 시간이 초과됩니다.
- 자동 크기 조정 수식에 문제가 있으면 노드 대상 값이 잘못됩니다. 크기 조정이 작동하거나 시간이 초과됩니다.

[autoScaleRun](https://docs.microsoft.com/rest/api/batchservice/pool/get#autoscalerun) 속성을 사용하여 마지막 자동 크기 조정 평가에 대한 정보를 얻을 수 있습니다. 이 속성은 평가 시간, 값과 결과, 성능 오류를 보고합니다.

[풀 크기 조정 완료 이벤트](https://docs.microsoft.com/azure/batch/batch-pool-resize-complete-event)는 모든 평가 정보를 캡처합니다.

### <a name="delete"></a>삭제

노드를 포함하는 풀을 삭제하면 Batch는 먼저 노드를 삭제합니다. 그런 다음, 풀 개체 자체를 삭제합니다. 풀 노드가 삭제되려면 몇 분 정도 걸릴 수 있습니다.

삭제 프로세스 중에 Batch는 [풀 상태](https://docs.microsoft.com/rest/api/batchservice/pool/get#poolstate)를 **deleting**으로 설정합니다. 호출 애플리케이션은 **state** 및 **stateTransitionTime** 속성을 사용하여 풀 삭제가 너무 오래 걸리는지를 검색할 수 있습니다.

## <a name="pool-compute-node-errors"></a>풀 계산 노드 오류

Batch가 풀의 노드를 성공적으로 할당하더라도 다양한 문제로 인해 일부 노드가 비정상 상태가 되어 사용하지 못할 수 있습니다. 이러한 노드에는 요금이 발생합니다. 사용할 수 없는 노드에 비용이 청구되지 않도록 문제를 감지하는 것이 중요합니다.

### <a name="start-task-failure"></a>시작 작업 실패

풀에 대해 선택적 [시작 작업](https://docs.microsoft.com/rest/api/batchservice/pool/add#starttask)을 지정할 수 있습니다. 일반적인 작업과 마찬가지로 스토리지에서 다운로드할 명령줄과 리소스 파일을 지정할 수 있습니다. 시작 작업은 시작된 후 각 노드에 대해 실행됩니다. **waitForSuccess** 속성은 Batch가 노드에 대한 작업을 예약하기 전에 시작 작업이 성공적으로 완료될 때까지 대기하는지 여부를 지정합니다.

시작 작업이 성공적으로 완료될 때까지 대기하도록 노드를 구성했으나 시작 작업이 실패하면 어떻게 될까요? 이 경우 노드를 사용할 수 없어도 요금은 계속 부과됩니다.

최상위 [startTaskInfo](https://docs.microsoft.com/rest/api/batchservice/computenode/get#starttaskinformation) 노드 속성의 [result](https://docs.microsoft.com/rest/api/batchservice/computenode/get#taskexecutionresult) 및 [failureInfo](https://docs.microsoft.com/rest/api/batchservice/computenode/get#taskfailureinformation) 속성을 사용하여 시작 작업 실패를 감지할 수 있습니다.

시작 작업이 실패하면 사용자가 **waitForSuccess**를 **true**로 설정한 경우에도 Batch는 노드 [state](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodestate)를 **starttaskfailed**로 설정합니다.

일반적인 작업과 마찬가지로 시작 작업 역시 여러 가지 원인으로 인해 실패할 수 있습니다.  문제를 해결하려면 stdout, stderr 및 추가 작업 관련 로그 파일을 확인합니다.

### <a name="application-package-download-failure"></a>애플리케이션 패키지 다운로드 실패

하나의 풀에 대해 하나 이상의 애플리케이션 패키지를 지정할 수 있습니다. Batch는 지정된 패키지 파일을 각 노드에 다운로드하고 노드가 시작된 후, 작업이 예약되기 전에 파일의 압축을 풉니다. 애플리케이션 패키지와 함께 시작 작업 명령줄을 사용하는 것이 일반적입니다. 예를 들어, 파일을 다른 위치로 복사하거나 설치 프로그램을 실행합니다.

노드 [errors](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodeerror) 속성은 애플리케이션 패키지의 다운로드 및 압축 풀기 실패를 보고합니다. Batch는 노드 상태를 **unusable**로 설정합니다.

### <a name="container-download-failure"></a>컨테이너 다운로드 실패

풀에서 하나 이상의 컨테이너 참조를 지정할 수 있습니다. 각 노드에 지정된 된 컨테이너를 다운로드 하는 일괄 처리 합니다. 노드 [오류](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodeerror) 컨테이너를 다운로드 하지 못함을 보고 하 고 노드 상태를 설정 하는 속성 **사용할 수 없는**합니다.

### <a name="node-in-unusable-state"></a>unusable 상태의 노드

Azure Batch에서는 여러 가지 이유로 [노드 상태](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodestate)를 **unusable**로 설정할 수 있습니다. 노드 상태를 **unusable**로 설정하면 작업을 노드로 예약할 수 없지만 요금은 여전히 부과됩니다.

노드를 **unsuable**, 없이 [오류](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodeerror) 상태 일괄 처리는 VM과 통신할 수 없는 것을 의미 합니다. 이 경우 일괄 처리는 항상 VM 복구를 시도 합니다. 일괄 처리는 해당 상태 이더라도 응용 프로그램 패키지 또는 컨테이너를 설치 하지 못한 Vm 복구을 자동으로 시도 하지 것입니다 **사용할 수 없는**합니다.

Batch가 원인을 확인할 수 있으면 노드 [errors](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodeerror) 속성이 그 원인을 보고합니다.

노드가 **unusable** 상태로 설정되는 원인의 추가 예제는 다음과 같습니다.

- 사용자 지정 VM 이미지를 올바르지 않습니다. 예를 들어, 제대로 준비되지 않은 이미지입니다.

- VM이 인프라 오류 또는 하위 수준 업그레이드로 인해 이동됩니다. Batch가 노드를 복구합니다.

- VM 이미지를 지원 하지 않는 하드웨어에 배포 되었습니다. 예를 들어 "HPC" VM 이미지로 HPC 이외의 하드웨어에서 실행 합니다. 예를 들어 CentOS HPC 이미지를 실행 하는 동안에 [Standard_D1_v2](../virtual-machines/linux/sizes-general.md#dv2-series) VM.

- Vm에는 [Azure 가상 네트워크](batch-virtual-network.md), 키 포트로 트래픽이 차단 되었습니다.

### <a name="node-agent-log-files"></a>노드 에이전트 로그 파일

각 풀 노드에서 실행되는 Batch 에이전트 프로세스는 풀 노드 문제로 인해 지원 서비스에 문의해야 할 때 유용한 로그 파일을 제공할 수 있습니다. Azure Portal, Batch Explorer 또는 [API](https://docs.microsoft.com/rest/api/batchservice/computenode/uploadbatchservicelogs)를 통해 노드의 로그 파일을 업로드할 수 있습니다. 로그 파일을 업로드하고 저장하면 유용합니다. 나중에 노드 또는 풀을 삭제하여 실행 중인 노드 비용을 절감할 수 있습니다.

## <a name="next-steps"></a>다음 단계

특히 비동기 작업에 대해 포괄적인 오류 검사를 구현하도록 애플리케이션을 설정했는지 확인합니다. 즉시 문제를 검색하고 진단하는 것이 중요할 수 있습니다.
