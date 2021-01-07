---
title: Azure Batch의 오류 처리 및 검색
description: 개발 관점에서 Batch 서비스 워크플로의 오류 처리에 대해 알아봅니다.
ms.topic: article
ms.date: 05/15/2020
ms.openlocfilehash: 3bd460598dae08fa18415e1c9865249f3ca4c9c2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "85964280"
---
# <a name="error-handling-and-detection-in-azure-batch"></a>Azure Batch의 오류 처리 및 검색

때때로 Batch 솔루션 내에서 태스크 오류와 애플리케이션 오류를 모두 처리해야 할 수 있습니다. 이 문서에서는 오류 유형과 해결 방법을 설명합니다.

## <a name="error-codes"></a>오류 코드

일반적인 오류 유형:

- Batch에 도달하지 못한 요청이나, Batch 응답이 시간 안에 클라이언트에 도달하지 못한 경우의 네트워킹 실패
- 내부 서버 오류(표준 5xx 상태 코드 HTTP 응답)
- Retry-after 헤더를 포함한 429 또는 503 상태 코드 HTTP 응답과 같은 네트워크 대역폭 제한 관련 오류
- AlreadyExists 및 InvalidOperation과 같은 4xx 오류. 리소스가 상태 전환에 올바른 상태가 아님을 의미합니다.

REST API, Batch 서비스 및 작업 태스크/예약에 대한 오류 코드를 포함하여 특정 오류 코드에 대한 자세한 내용은 [Batch 상태 및 오류 코드](/rest/api/batchservice/batch-status-and-error-codes)를 참조하세요.

## <a name="application-failures"></a>애플리케이션 오류

실행하는 동안 애플리케이션에서 문제를 해결하는 데 사용할 수 있는 진단 출력을 생성할 수 있습니다. [파일 및 디렉터리](files-and-directories.md) 섹션에서 설명한 것처럼 Batch 서비스는 컴퓨팅 노드의 태스크 디렉터리에 있는 `stdout.txt` 및 `stderr.txt` 파일에 표준 출력 및 표준 오류 출력을 작성합니다.

Azure Portal 또는 Batch SDK 중 하나를 사용하여 이러한 파일을 다운로드할 수 있습니다. 예를 들어, Batch .NET 라이브러리에서 [ComputeNode.GetNodeFile](/dotnet/api/microsoft.azure.batch.computenode) 및 [CloudTask.GetNodeFile](/dotnet/api/microsoft.azure.batch.cloudtask)을 사용하여 문제 해결을 위해 이러한 파일 및 다른 파일을 검색할 수 있습니다.

## <a name="task-errors"></a>태스크 오류

태스크 오류는 몇 가지 범주로 나뉩니다.

### <a name="pre-processing-errors"></a>전처리 오류

작업 시작에 실패하면 작업에 대해 사전 처리 오류가 설정됩니다.  

태스크의 리소스 파일이 이동되었거나, 스토리지 계정을 더 이상 사용할 수 없거나, 노드에 파일을 복사하지 못하도록 하는 다른 문제가 발생하는 경우 사전 처리 오류가 발생할 수 있습니다.

### <a name="file-upload-errors"></a>파일 업로드 오류

태스크에 대해 지정된 파일이 어떤 이유로 업로드에 실패한 경우 해당 태스크에 대해 파일 업로드 오류가 설정됩니다.

Azure Storage 액세스를 위해 제공된 SAS가 유효하지 않거나 쓰기 권한이 제공되지 않는 경우, 스토리지 계정을 더 이상 사용할 수 없거나, 노드에서 파일의 복사를 방지하여 다른 문제가 발생한 경우 파일 업로드 오류가 발생할 수 있습니다.

### <a name="application-errors"></a>애플리케이션 오류

태스크의 명령줄에서 지정된 프로세스도 실패할 수 있습니다. 태스크에 의해 실행된 프로세스에서 0이 아닌 종료 코드가 반환되면 프로세스가 실패한 것으로 간주됩니다(다음 섹션에서 *태스크 종료 코드* 참조).

애플리케이션 오류의 경우 지정된 횟수까지 자동으로 태스크를 다시 시도하도록 Batch를 구성할 수 있습니다.

### <a name="constraint-errors"></a>제약 조건 오류

작업 또는 태스크의 최대 실행 기간을 지정하는 제약 조건( *maxWallClockTime*)을 지정할 수 있습니다. 이 기능은 작동되지 않는 태스크를 종료하는 데 유용할 수 있습니다.

최대 기간을 초과하면 태스크가 *완료됨*으로 표시되지만 종료 코드가 `0xC000013A`로 설정되고 *schedulingError* 필드가 `{ category:"ServerError", code="TaskEnded"}`로 표시됩니다.

## <a name="task-exit-codes"></a>태스크 종료 코드

앞서 설명했듯이 태스크에서 실행하는 프로세스가 0이 아닌 종료 코드를 반환하는 경우 태스크는 Batch 서비스에서 실패했다고 표시됩니다. 태스크가 프로세스를 실행하는 경우 Batch는 프로세스의 반환 코드를 사용하여 태스크의 종료 코드 속성을 채웁니다.

태스크의 종료 코드는 Batch 서비스에 의해 결정되지 않는다는 점에 주의해야 합니다. 태스크의 종료 코드는 프로세스 자체 또는 프로세스가 실행되는 운영 체제에 의해 결정됩니다.

## <a name="task-failures-or-interruptions"></a>태스크 실패 또는 중단

간혹 태스크가 실패하거나 중단될 수 있습니다. 태스크 애플리케이션 자체가 실패하거나, 태스크를 실행 중인 노드가 다시 부팅되거나, 풀의 할당 취소 정책이 태스크가 완료되기를 기다리지 않고 즉시 노드를 제거하도록 설정된 상태에서 크기 조정 작업 중에 풀에서 노드가 제거되었을 수 있습니다. 어떤 경우든지 태스크는 다른 노드에서 실행되도록 Batch에 의해 자동으로 큐에 다시 대기할 수 있습니다.

일시적인 문제로 인해 태스크가 응답을 멈추거나 실행하는 데 너무 오래 걸릴 수도 있습니다. 태스크에 대한 최대 실행 간격을 설정할 수 있습니다. 최대 실행 간격을 초과하면 Batch 서비스가 태스크 애플리케이션을 중단합니다.

## <a name="connect-to-compute-nodes"></a>컴퓨팅 노드 연결

컴퓨팅 노드에 원격으로 로그인하여 추가 디버깅 및 문제 해결을 수행할 수 있습니다. Azure Portal을 사용하여 Windows 노드에 RDP(원격 데스크톱 프로토콜) 파일을 다운로드하고 Linux 노드에 SSH(Secure Shell) 연결 정보를 가져올 수 있습니다. 또한 [Batch .NET](/dotnet/api/microsoft.azure.batch.computenode) 또는 [Batch Python](batch-linux-nodes.md#connect-to-linux-nodes-using-ssh)과 같은 Batch API를 사용하여 수행할 수 있습니다.

> [!IMPORTANT]
> RDP 또는 SSH를 통해 노드에 연결하려면 먼저 해당 노드에서 사용자를 만들어야 합니다. 이렇게 하려면 Azure Portal을 사용할 수 있습니다. Batch REST API를 사용하여 [노드에 사용자 계정을 추가](/rest/api/batchservice/computenode/adduser)하거나 Batch .NET에서 [ComputeNode.CreateComputeNodeUser](/dotnet/api/microsoft.azure.batch.computenode) 메서드를 호출하거나 Batch Python 모듈에서 [add_user](batch-linux-nodes.md#connect-to-linux-nodes-using-ssh) 메서드를 호출합니다.

컴퓨팅 노드에 대한 RDP 또는 SSH 액세스를 제한하거나 사용하지 않도록 설정해야 하는 경우 [Azure Batch 풀의 컴퓨팅 노드에 대한 원격 액세스를 구성하거나 사용하지 않도록 설정](pool-endpoint-configuration.md)을 참조하세요.

## <a name="troubleshoot-problem-nodes"></a>문제 노드 문제 해결

태스크가 실패한 경우 Batch 클라이언트 애플리케이션 또는 서비스는 실패한 작업의 메타데이터를 검사하여 오동작 노드를 식별할 수 있습니다. 풀의 각 노드에는 고유 ID가 지정되며, 태스크가 실행되는 노드는 태스크 메타데이터에 포함됩니다. 문제 노드를 식별하면 다음과 같은 몇 가지 작업을 수행할 수 있습니다.

- **노드 다시 시작**([REST](/rest/api/batchservice/computenode/reboot) | [.NET](/dotnet/api/microsoft.azure.batch.computenode.reboot)))

    노드를 다시 시작하면 경우에 따라 충돌 또는 중단 프로세스와 같은 잠재적인 문제를 해소할 수 있습니다. 풀에서 시작 태스크를 사용하거나 작업에서 준비 태스크를 사용하는 경우 노드가 다시 시작될 때 실행됩니다.
- **노드 이미지로 다시 설치**([REST](/rest/api/batchservice/computenode/reimage) | [.NET](/dotnet/api/microsoft.azure.batch.computenode.reimage))

    노드에서 운영 체제를 다시 설치합니다. 노드를 다시 시작할 때와 마찬가지로 태스크를 시작하고 노드가 이미지로 다시 설치된 후에 작업 준비 태스크를 다시 실행합니다.
- **풀에서 노드 제거**([REST](/rest/api/batchservice/pool/removenodes) | [.NET](/dotnet/api/microsoft.azure.batch.pooloperations))

    경우에 따라 풀에서 노드를 완전히 제거해야 합니다.
- **노드에서 태스크 예약 사용 안 함**([REST](/rest/api/batchservice/computenode/disablescheduling) | [.NET](/dotnet/api/microsoft.azure.batch.computenode.disablescheduling))

    사실상 노드를 오프라인으로 만들기 때문에 해당 노드에 더 이상 작업이 할당되지 않지만 노드가 풀에서 실행되도록 유지할 수 있습니다. 그러면 실패한 태스크에 데이터 손실이나 노드에 추가 작업 오류를 발생시키지 않고 실패의 원인을 조사할 수 있습니다. 예를 들어 노드에서 태스크 예약을 사용하지 않도록 설정한 다음, 원격으로 로그인하여 노드의 이벤트 로그를 검사하거나 다른 문제를 해결할 수 있습니다. 조사를 완료하면 태스크 예약([REST](/rest/api/batchservice/computenode/enablescheduling) | [.NET](/dotnet/api/microsoft.azure.batch.computenode.enablescheduling))을 사용하여 노드를 다시 온라인 상태로 되돌리거나 앞서 설명한 다른 작업 중 하나를 수행할 수 있습니다.

> [!IMPORTANT]
> 위에서 설명한 작업을 사용하여 현재 노드에서 실행되는 태스크가 작업 완료 시 처리되는 방법을 지정할 수 있습니다. 예를 들어 Batch .NET 클라이언트 라이브러리를 사용하여 노드에서 태스크 예약을 사용하지 않으면 [DisableComputeNodeSchedulingOption](/dotnet/api/microsoft.azure.batch.common.disablecomputenodeschedulingoption) 열거형 값을 지정하여 실행 중인 태스크를 **종료**하거나, 다른 노드에서 예약을 **다시 대기**하거나 또는 작업(**TaskCompletion**)을 수행하기 전에 완료할 태스크 실행을 허용할지를 지정할 수 있습니다.

## <a name="retry-after-errors"></a>오류 후 다시 시도

Batch API는 오류가 발생하면 사용자에게 알립니다. 모두 다시 시도할 수 있고, 이를 위해 모두 전역 다시 시도 처리기를 포함합니다. 이 기본 제공 메커니즘을 사용하는 것이 가장 좋습니다.

오류가 발생한 후 다시 시도하기 전에 잠시(다시 시도 사이의 몇 초) 기다려야 합니다. 너무 자주 또는 너무 빨리 다시 시도하는 경우 다시 시도 처리기가 제한됩니다.

## <a name="next-steps"></a>다음 단계

- [풀 및 노드 오류 확인](batch-pool-node-error-checking.md) 방법을 알아봅니다.
- [작업 및 태스크 오류 확인](batch-job-task-error-checking.md) 방법을 알아봅니다.
- [배치 상태 및 오류 코드](/rest/api/batchservice/batch-status-and-error-codes) 목록을 검토합니다.
