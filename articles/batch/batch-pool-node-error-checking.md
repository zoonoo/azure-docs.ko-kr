---
title: 풀 및 노드 오류 확인
description: 이 문서에서는 풀 및 노드를 만들 때 발생 가능한 백그라운드 작업과 확인해야 할 오류, 이를 방지하는 방법을 설명합니다.
author: mscurrell
ms.author: markscu
ms.date: 08/23/2019
ms.topic: how-to
ms.openlocfilehash: 519b357e4e5fde30221f7dc804bb848ecec9704c
ms.sourcegitcommit: 93462ccb4dd178ec81115f50455fbad2fa1d79ce
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/06/2020
ms.locfileid: "85979920"
---
# <a name="check-for-pool-and-node-errors"></a>풀 및 노드 오류 확인

Azure Batch 풀을 만들고 관리할 때 일부 작업은 즉시 수행됩니다. 그러나 일부 작업은 비동기적으로 백그라운드에서 실행되어 완료되는 데 몇 분 정도 걸릴 수 있습니다.

즉시 실행되는 작업에서는 오류를 쉽게 검색할 수 있습니다. API, CLI 또는 UI에서 오류가 즉시 반환되기 때문입니다.

이 문서에서는 풀 및 풀 노드에 대해 발생할 수 있는 백그라운드 작업을 설명합니다. 또한 오류를 감지하고 피하는 방법을 지정합니다.

## <a name="pool-errors"></a>풀 오류

### <a name="resize-timeout-or-failure"></a>크기 조정 시간 제한 또는 오류

새 풀을 만들거나 기존 풀의 크기를 조정할 때는 노드의 대상 수를 지정합니다.  작업은 즉시 완료되지만 새 노드의 실제 할당 또는 기존 노드의 제거에는 몇 분 정도 걸릴 수 있습니다.  [create](/rest/api/batchservice/pool/add) 또는 [resize](/rest/api/batchservice/pool/resize) API에서 크기 조정 시간 제한을 지정합니다. 크기 조정 제한 시간 동안 Batch에서 대상 노드 수를 가져올 수 없는 경우 풀이 안정된 상태가 되고 크기 조정 오류가 보고됩니다.

가장 최근 평가의 [ResizeError](/rest/api/batchservice/pool/get#resizeerror) 속성은 발생한 오류를 나열합니다.

크기 조정 오류가 발생하는 일반적인 원인은 다음과 같습니다.

- 크기 조정 시간 제한이 너무 짧음
  - 대부분의 경우 기본 시간 제한인 15분은 풀 노드를 할당하거나 제거할 수 있도록 충분히 깁니다.
  - 많은 수의 노드를 할당하는 경우에는 크기 조정 시간 제한을 30분으로 설정하는 것이 좋습니다. 예를 들어, 1개의 Azure Marketplace 이미지에서 1,000개가 넘는 노드로 크기를 조정하거나 1개의 사용자 지정 VM 이미지에서 300개가 넘는 노드로 크기를 조정할 수 있습니다.
- 코어 할당량 부족
  - Batch 계정은 모든 풀에서 할당할 수 있는 코어 수가 제한됩니다. 해당 할당량에 도달하면 Batch의 노드 할당이 중지됩니다. Batch에서 더 많은 노드를 할당할 수 있도록 코어 할당량을 [늘릴 수 있습니다](./batch-quota-limit.md).
- [풀이 가상 네트워크에 있을 때](./batch-virtual-network.md) 서브넷 IP가 부족함
  - 가상 네트워크 서브넷은 요청한 모든 풀 노드에 할당할 수 있는 충분한 미할당 IP 주소가 있어야 합니다. 그렇지 않으면 노드를 만들 수 없습니다.
- [풀이 가상 네트워크에 있을 때](./batch-virtual-network.md) 리소스가 부족함
  - Batch 계정과 동일한 구독에 부하 분산 장치, 공용 IP 및 네트워크 보안 그룹과 같은 리소스를 만들 수 있습니다. 구독 할당량이 이러한 리소스에 대해 충분한지 확인합니다.
- 사용자 지정 VM 이미지를 사용하는 큰 풀
  - 사용자 지정 VM 이미지를 사용하는 큰 풀은 할당 시간이 더 오래 걸릴 수 있으며 크기 조정 오류가 발생할 수 있습니다.  제한 및 구성에 대한 권장 사항은 [공유 이미지 갤러리를 사용하여 풀 만들기](batch-sig-images.md)를 참조하세요.

### <a name="automatic-scaling-failures"></a>자동 크기 조정 실패

풀의 노드 수를 자동으로 조정하도록 Azure Batch를 설정할 수도 있습니다. [풀의 자동 크기 조정 수식](./batch-automatic-scaling.md)에 대한 매개 변수를 정의합니다. Batch 서비스는 이 수식을 사용하여 풀의 노드 수를 정기적으로 평가하고 새 목표 개수를 설정합니다. 다음과 같은 유형의 문제가 발생할 수 있습니다.

- 자동 크기 조정 평가가 실패합니다.
- 평가 결과에 따른 크기 조정 작업이 실패하고 시간이 초과됩니다.
- 자동 크기 조정 수식에 문제가 있으면 노드 대상 값이 잘못됩니다. 크기 조정이 작동하거나 시간이 초과됩니다.

[autoScaleRun](/rest/api/batchservice/pool/get#autoscalerun) 속성을 사용하여 마지막 자동 크기 조정 평가에 대한 정보를 얻을 수 있습니다. 이 속성은 평가 시간, 값과 결과, 성능 오류를 보고합니다.

[풀 크기 조정 완료 이벤트](./batch-pool-resize-complete-event.md)는 모든 평가 정보를 캡처합니다.

### <a name="delete"></a>DELETE

노드를 포함하는 풀을 삭제하면 Batch는 먼저 노드를 삭제합니다. 그런 다음, 풀 개체 자체를 삭제합니다. 풀 노드가 삭제되려면 몇 분 정도 걸릴 수 있습니다.

삭제 프로세스 중에 Batch는 [풀 상태](/rest/api/batchservice/pool/get#poolstate)를 **deleting**으로 설정합니다. 호출 애플리케이션은 **state** 및 **stateTransitionTime** 속성을 사용하여 풀 삭제가 너무 오래 걸리는지를 검색할 수 있습니다.

## <a name="pool-compute-node-errors"></a>풀 컴퓨팅 노드 오류

Batch가 풀의 노드를 성공적으로 할당하더라도 다양한 문제로 인해 일부 노드가 비정상 상태가 되어 태스크를 실행하지 못할 수 있습니다. 이러한 노드에도 요금이 계속 부과되므로 사용할 수 없는 노드에 대한 요금을 지불하지 않도록 문제를 검색하는 것이 중요합니다. 일반적인 노드 오류 외에도 현재 [작업 상태](/rest/api/batchservice/job/get#jobstate)를 파악하는 것이 문제를 해결하는 데 유용합니다.

### <a name="start-task-failures"></a>시작 태스크 실패

풀에 대해 선택적 [시작 작업](/rest/api/batchservice/pool/add#starttask)을 지정할 수 있습니다. 일반적인 작업과 마찬가지로 스토리지에서 다운로드할 명령줄과 리소스 파일을 지정할 수 있습니다. 시작 작업은 시작된 후 각 노드에 대해 실행됩니다. **waitForSuccess** 속성은 Batch가 노드에 대한 작업을 예약하기 전에 시작 작업이 성공적으로 완료될 때까지 대기하는지 여부를 지정합니다.

시작 작업이 성공적으로 완료될 때까지 대기하도록 노드를 구성했으나 시작 작업이 실패하면 어떻게 될까요? 이 경우 노드는 사용할 수 없지만 요금은 계속 청구됩니다.

최상위 [startTaskInfo](/rest/api/batchservice/computenode/get#starttaskinformation) 노드 속성의 [result](/rest/api/batchservice/computenode/get#taskexecutionresult) 및 [failureInfo](/rest/api/batchservice/computenode/get#taskfailureinformation) 속성을 사용하여 시작 작업 실패를 감지할 수 있습니다.

시작 작업이 실패하면 **waitForSuccess**가 **true**로 설정된 경우에도 Batch는 노드 [state](/rest/api/batchservice/computenode/get#computenodestate)를 **starttaskfailed**로 설정합니다.

일반적인 작업과 마찬가지로 시작 작업 역시 여러 가지 원인으로 인해 실패할 수 있습니다.  문제를 해결하려면 stdout, stderr 및 추가 작업 관련 로그 파일을 확인합니다.

시작 태스크는 동일한 노드에서 여러 번 실행될 수 있으므로 재진입성이 있어야 합니다. 노드가 이미지로 다시 설치되거나 다시 부팅되면 시작 태스크가 실행됩니다. 드물지만 이벤트에 의해 노드가 다시 부팅되었는데 운영 체제 또는 임시 디스크 중 하나가 이미지로 다시 설치되었지만 다른 하나는 삭제되지 않은 경우 시작 태스크가 실행됩니다. Batch 시작 태스크(예: 모든 Batch 태스크)는 임시 디스크에서 실행되므로 일반적으로 문제가 되지 않지만, 시작 태스크가 운영 체제 디스크에 애플리케이션을 설치하고 다른 데이터를 임시 디스크에 유지하는 경우에는 데이터가 동기화되지 않기 때문에 문제가 발생할 수 있습니다. 두 디스크를 모두 사용하는 경우 애플리케이션을 적절히 보호하세요.

### <a name="application-package-download-failure"></a>애플리케이션 패키지 다운로드 실패

하나의 풀에 대해 하나 이상의 애플리케이션 패키지를 지정할 수 있습니다. Batch는 지정된 패키지 파일을 각 노드에 다운로드하고 노드가 시작된 후, 작업이 예약되기 전에 파일의 압축을 풉니다. 애플리케이션 패키지와 함께 시작 작업 명령줄을 사용하는 것이 일반적입니다. 예를 들어, 파일을 다른 위치로 복사하거나 설치 프로그램을 실행합니다.

노드 [errors](/rest/api/batchservice/computenode/get#computenodeerror) 속성은 애플리케이션 패키지의 다운로드 및 압축 풀기 실패를 보고합니다. 노드 상태는 **unusable**로 설정됩니다.

### <a name="container-download-failure"></a>컨테이너 다운로드 실패

풀에 하나 이상의 컨테이너 참조를 지정할 수 있습니다. Batch는 지정된 컨테이너를 각 노드에 다운로드합니다. 노드 [errors](/rest/api/batchservice/computenode/get#computenodeerror) 속성은 컨테이너 다운로드 실패를 보고하고 노드 상태를 **unusable**로 설정합니다.

### <a name="node-in-unusable-state"></a>unusable 상태의 노드

Azure Batch에서는 여러 가지 이유로 [노드 상태](/rest/api/batchservice/computenode/get#computenodestate)를 **unusable**로 설정할 수 있습니다. 노드 상태를 **unusable**로 설정하면 작업을 노드로 예약할 수 없지만 요금은 여전히 부과됩니다.

**unusable** 상태이지만 [errors](/rest/api/batchservice/computenode/get#computenodeerror)가 없는 노드는 Batch가 VM과 통신할 수 없음을 의미합니다. 이 경우 Batch는 항상 VM 복구를 시도합니다. Batch는 애플리케이션 패키지 또는 컨테이너를 설치하지 못한 VM의 상태가 **unusable**이라도 자동으로 복구를 시도하지 않습니다.

Batch가 원인을 확인할 수 있으면 노드 [errors](/rest/api/batchservice/computenode/get#computenodeerror) 속성이 그 원인을 보고합니다.

노드가 **unusable** 상태로 설정되는 원인의 추가 예제는 다음과 같습니다.

- 사용자 지정 VM 이미지를 올바르지 않습니다. 예를 들어, 제대로 준비되지 않은 이미지입니다.

- VM이 인프라 오류 또는 하위 수준 업그레이드로 인해 이동됩니다. Batch가 노드를 복구합니다.

- VM 이미지가 해당 이미지를 지원하지 않는 하드웨어에 배포되었습니다. 예를 들어 [Standard_D1_v2](../virtual-machines/dv2-dsv2-series.md) VM에서 CentOS HPC 이미지를 실행하려는 경우가 있습니다.

- VM이 [Azure 가상 네트워크](batch-virtual-network.md)에 있으며 트래픽이 키 포트로 차단되었습니다.

- VM이 가상 네트워크에 있지만 Azure Storage에 대한 아웃바운드 트래픽이 차단되었습니다.

- VM이 고객 DNS 구성을 사용하는 가상 네트워크에 있으며 DNS 서버가 Azure Storage를 확인할 수 없습니다.

### <a name="node-agent-log-files"></a>노드 에이전트 로그 파일

각 풀 노드에서 실행되는 Batch 에이전트 프로세스는 풀 노드 문제로 인해 지원 서비스에 문의해야 할 때 유용한 로그 파일을 제공할 수 있습니다. Azure Portal, Batch Explorer 또는 [API](/rest/api/batchservice/computenode/uploadbatchservicelogs)를 통해 노드의 로그 파일을 업로드할 수 있습니다. 로그 파일을 업로드하고 저장하면 유용합니다. 나중에 노드 또는 풀을 삭제하여 실행 중인 노드 비용을 절감할 수 있습니다.

### <a name="node-disk-full"></a>노드 디스크가 가득 참

Batch는 풀 노드 VM의 임시 드라이브를 작업 파일, 태스크 파일 및 공유 파일용으로 사용합니다.

- 애플리케이션 패키지 파일
- 태스크 리소스 파일
- Batch 폴더 중 하나로 다운로드되는 애플리케이션 관련 파일
- 각 태스크 애플리케이션 실행에 대한 stdout 및 stderr 파일
- 애플리케이션 관련 출력 파일

이러한 파일 중 일부는 풀 애플리케이션 패키지 또는 풀 시작 태스크 리소스 파일 등의 풀 노드를 만들 때 한 번만 기록됩니다. 노드가 생성될 때 한 번만 기록된 경우에도 이러한 파일이 너무 크면 임시 드라이브가 가득 찰 수 있습니다.

다른 파일은 stdout, stderr 등의 노드에서 실행되는 각 태스크에 대해 기록됩니다. 동일한 노드에서 많은 작업을 실행하거나 태스크 파일이 너무 클 경우 임시 드라이브가 가득 찰 수 있습니다.

임시 드라이브의 크기는 VM 크기에 따라 달라집니다. VM 크기를 선택할 때는 임시 드라이브에 충분한 공간이 있는지 확인해야 합니다.

- Azure Portal에서 풀을 추가하면 VM 크기의 전체 목록이 표시되는데, 여기에는 '리소스 디스크 크기' 열이 있습니다.
- 모든 VM 크기를 설명하는 문서에는 '임시 스토리지' 열이 있는 테이블이 있습니다(예: [컴퓨팅 최적화 VM 크기](../virtual-machines/sizes-compute.md)).

각 태스크에 의해 기록된 파일의 경우 태스크 파일이 자동으로 정리되기 전에 유지되는 기간을 결정하는 보존 기간을 각 태스크별로 지정할 수 있습니다. 보존 기간을 축소하면 스토리지 요구량을 줄일 수 있습니다.


임시 디스크의 공간이 부족하거나 공간이 얼마 남지 않은 경우 노드가 [Unusable](/rest/api/batchservice/computenode/get#computenodestate) 상태로 전환되고 디스크가 가득 찼다는 노드 오류가 보고됩니다.

### <a name="what-to-do-when-a-disk-is-full"></a>디스크가 가득 찬 경우에 수행할 작업

디스크가 가득 찬 이유를 확인합니다. 노드의 공간이 가득 찬 이유를 모르겠다면 노드에 원격으로 액세스하여 여유 공간이 없는 위치를 수동으로 조사하는 것이 좋습니다. 또한 [Batch List Files API](/rest/api/batchservice/file/listfromcomputenode)를 사용하여 Batch 관리형 폴더의 파일(예: 태스크 출력)을 살펴볼 수도 있습니다. 이 API는 Batch 관리형 디렉터리의 파일만 나열하며, 태스크가 다른 위치에 파일을 만든 경우 해당 파일을 볼 수 없습니다.

필요한 모든 데이터가 노드에서 검색되었거나 영구 저장소로 업로드되었는지 확인합니다. 디스크 용량 부족 문제를 완화하려면 항상 데이터를 삭제하여 공간을 확보해야 합니다.

### <a name="recovering-the-node"></a>노드 복구

1. 풀이 [C.loudServiceConfiguration](/rest/api/batchservice/pool/add#cloudserviceconfiguration) 풀인 경우 [Batch 이미지로 다시 설치 API](/rest/api/batchservice/computenode/reimage)를 통해 노드를 이미지로 다시 설치할 수 있습니다. 그러면 전체 디스크가 삭제됩니다. [VirtualMachineConfiguration](/rest/api/batchservice/pool/add#virtualmachineconfiguration) 풀은 현재 이미지로 다시 설치할 수 없습니다.

2. 풀이 [VirtualMachineConfiguration](/rest/api/batchservice/pool/add#virtualmachineconfiguration)인 경우 [노드 제거 API](/rest/api/batchservice/pool/removenodes)를 사용하여 풀에서 노드를 제거할 수 있습니다. 그런 다음, 풀을 다시 확장하여 잘못된 노드를 새 노드로 바꿀 수 있습니다.

3.  이전에 완료된 작업 또는 이전에 완료된 태스크 중 태스크 데이터가 아직 노드에 남아 있는 항목을 삭제합니다. 노드에 있는 작업/태스크 데이터는 노드의 [RecentTasks 컬렉션](/rest/api/batchservice/computenode/get#taskinformation) 또는 [노드의 파일](/rest/api/batchservice/file/listfromcomputenode)에서 확인할 수 있습니다. 작업을 삭제하면 작업의 모든 태스크가 삭제되고, 작업의 태스크를 삭제하면 삭제할 노드의 태스크 디렉터리에 있는 데이터가 트리거되어 공간이 확보됩니다. 충분한 공간을 확보한 후에는 노드를 다시 부팅하세요. 그러면 "Unusable" 상태에서 "Idle" 상태로 다시 전환됩니다.

## <a name="next-steps"></a>다음 단계

특히 비동기 작업에 대해 포괄적인 오류 검사를 구현하도록 애플리케이션을 설정했는지 확인합니다. 즉시 문제를 검색하고 진단하는 것이 중요할 수 있습니다.
