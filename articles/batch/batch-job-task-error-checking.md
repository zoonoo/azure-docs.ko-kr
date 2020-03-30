---
title: 작업 및 작업 오류 확인 - Azure 배치 | 마이크로 소프트 문서
description: 작업 및 작업 문제 해결을 위한 오류
services: batch
author: mscurrell
ms.service: batch
ms.topic: article
ms.date: 03/10/2019
ms.author: markscu
ms.openlocfilehash: 4ace0de6d252680eb64990277b9478adf752f54d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79087013"
---
# <a name="job-and-task-error-checking"></a>작업 및 작업 오류 검사

작업 및 작업을 추가할 때 발생할 수 있는 다양한 오류가 있습니다. 이러한 작업에 대한 오류를 검색하는 것은 API, CLI 또는 UI에 의해 즉시 반환되므로 간단합니다.  그러나 작업 및 작업이 예약되고 실행될 때 나중에 발생할 수 있는 오류가 있습니다.

이 문서에서는 작업 및 작업이 제출된 후 발생할 수 있는 오류를 다룹니다. 검사및처리해야 하는 오류를 나열하고 설명합니다.

## <a name="jobs"></a>작업

작업은 하나 이상의 작업을 그룹화하는 작업으로, 실제로 실행할 명령줄을 지정하는 작업입니다.

작업을 추가할 때 작업이 실패하는 방법에 영향을 미칠 수 있는 다음 매개 변수를 지정할 수 있습니다.

- [작업 제약 조건](https://docs.microsoft.com/rest/api/batchservice/job/add#jobconstraints)
  - 이 `maxWallClockTime` 속성은 선택적으로 작업을 활성 또는 실행 가능한 최대 시간을 설정하도록 지정할 수 있습니다. 초과하면 작업에 대한 `terminateReason` [실행Info에](https://docs.microsoft.com/rest/api/batchservice/job/get#cloudjob) 설정된 속성으로 작업이 종료됩니다.
- [작업 준비 작업](https://docs.microsoft.com/rest/api/batchservice/job/add#jobpreparationtask)
  - 지정된 경우 노드의 작업에 대해 작업을 처음 실행할 때 작업 준비 작업이 실행됩니다. 작업 준비 작업이 실패할 수 있으며, 이로 인해 작업이 실행되지 않고 작업이 완료되지 않습니다.
- [작업 릴리스 작업](https://docs.microsoft.com/rest/api/batchservice/job/add#jobreleasetask)
  - 작업 릴리스 작업은 작업 준비 작업이 구성된 경우에만 지정할 수 있습니다. 작업이 종료될 때 작업 준비 작업이 실행된 각 풀 노드에서 작업 릴리스 작업이 실행됩니다. 작업 릴리스 작업이 실패할 수 있지만 작업은 `completed` 계속 상태로 이동합니다.

### <a name="job-properties"></a>작업 속성

다음 작업 속성에 오류가 있습니까?

- '[실행정보](https://docs.microsoft.com/rest/api/batchservice/job/get#jobexecutioninformation)' :
  - 속성에는 `terminateReason` 작업 제약 조건에 `maxWallClockTime`지정된 이 초과되어 작업이 종료되었음을 나타내는 값이 있을 수 있습니다. 작업 `onTaskFailure` 속성이 적절하게 설정된 경우 작업이 실패했음을 나타내도록 설정할 수도 있습니다.
  - 스케줄링오류가 있는 경우 [schedulingError](https://docs.microsoft.com/rest/api/batchservice/job/get#jobschedulingerror) 속성이 설정됩니다.
 
### <a name="job-preparation-tasks"></a>작업 준비 작업

작업에 대해 작업 준비 작업이 지정되면 해당 작업의 인스턴스가 노드에서 작업에 대한 작업을 처음 실행할 때 실행됩니다. 작업에 구성된 작업 준비 작업은 풀의 노드 수까지 여러 작업 준비 작업 인스턴스가 실행되는 작업 템플릿으로 생각할 수 있습니다.

작업 준비 작업 인스턴스를 확인하여 오류가 있는지 확인해야 합니다.
- 작업 준비 작업이 실행되면 작업 준비 작업을 트리거한 작업이 [다음의](https://docs.microsoft.com/rest/api/batchservice/task/get#taskstate) `preparing`상태로 이동합니다. 작업 준비 작업이 실패하면 트리거 작업이 `active` 상태로 되돌아가고 실행되지 않습니다.  
- 실행된 작업 준비 작업의 모든 인스턴스는 [작업 준비 및 릴리스 작업 상태](https://docs.microsoft.com/rest/api/batchservice/job/listpreparationandreleasetaskstatus) API를 사용하여 작업에서 가져올 수 있습니다. 다른 작업과 마찬가지로 `failureInfo` `exitCode`에서와 같은 속성에서 사용할 `result`수 있는 [실행 정보가](https://docs.microsoft.com/rest/api/batchservice/job/listpreparationandreleasetaskstatus#jobpreparationandreleasetaskexecutioninformation) 있습니다.
- 작업 준비 작업이 실패하면 트리거작업 작업이 실행되지 않고 작업이 완료되지 않고 멈됩니다. 예약할 수 있는 작업이 있는 다른 작업이 없는 경우 풀을 사용하지 않을 수 있습니다.

### <a name="job-release-tasks"></a>작업 릴리스 작업

작업에 대해 작업 릴리스 작업이 지정되면 작업이 종료될 때 작업 준비 작업이 실행된 각 풀 노드에서 작업 릴리스 작업의 인스턴스가 실행됩니다.  작업 릴리스 작업 인스턴스를 검사하여 오류가 있는지 확인해야 합니다.
- 실행 중인 작업 릴리스 작업의 모든 인스턴스는 API [목록 준비 및 릴리스 작업 상태를](https://docs.microsoft.com/rest/api/batchservice/job/listpreparationandreleasetaskstatus)사용하여 작업에서 가져올 수 있습니다. 다른 작업과 마찬가지로 `failureInfo` `exitCode`에서와 같은 속성에서 사용할 `result`수 있는 [실행 정보가](https://docs.microsoft.com/rest/api/batchservice/job/listpreparationandreleasetaskstatus#jobpreparationandreleasetaskexecutioninformation) 있습니다.
- 하나 이상의 작업 릴리스 작업이 실패해도 작업이 종료되고 `completed` 상태로 이동합니다.

## <a name="tasks"></a>작업

작업 작업은 여러 가지 이유로 실패할 수 있습니다.

- 작업 명령줄이 실패하여 0이 아닌 엑시트 코드로 반환됩니다.
- 작업에 `resourceFiles` 대해 지정되어 있지만 하나 이상의 파일이 다운로드되지 않았음을 의미하는 오류가 발생했습니다.
- 작업에 `outputFiles` 대해 지정되어 있지만 하나 이상의 파일이 업로드되지 않았음을 의미하는 오류가 발생했습니다.
- 작업 제약 `maxWallClockTime` [조건의](https://docs.microsoft.com/rest/api/batchservice/task/add#taskconstraints)속성에 의해 지정된 작업의 경과 시간이 초과되었습니다.

모든 경우에 오류 및 오류에 대한 정보를 확인해야합니다.
- 작업 [executionInfo](https://docs.microsoft.com/rest/api/batchservice/task/get#taskexecutioninformation) 속성오류에 대 한 정보를 제공 하는 여러 속성을 포함 합니다. [result는](https://docs.microsoft.com/rest/api/batchservice/task/get#taskexecutionresult) 어떤 이유로든 작업이 실패했는지 를 `exitCode` 나타내며 `failureInfo` 오류에 대한 자세한 정보를 제공합니다.
- 작업은 성공 또는 실패 `completed` 여부에 관계없이 항상 [상태로](https://docs.microsoft.com/rest/api/batchservice/task/get#taskstate)이동합니다.

작업 실패가 작업에 미치는 영향과 작업 종속성을 고려해야 합니다.  [exitConditions](https://docs.microsoft.com/rest/api/batchservice/task/add#exitconditions) 속성은 종속성 및 작업에 대한 작업을 구성하는 작업에 대해 지정할 수 있습니다.
- 종속성의 경우 [종속성 작업은](https://docs.microsoft.com/rest/api/batchservice/task/add#dependencyaction) 실패한 작업에 종속된 작업이 차단되는지 실행되는지 여부를 제어합니다.
- 작업의 경우 [JobAction은](https://docs.microsoft.com/rest/api/batchservice/task/add#jobaction) 실패한 작업이 작업을 사용하지 않도록 설정, 종료 또는 변경되지 않은 상태로 남아 있는지 여부를 제어합니다.

### <a name="task-command-line-failures"></a>작업 명령줄 실패

작업 명령줄이 실행되면 출력이 `stderr.txt` 및 `stdout.txt`에 기록됩니다. 또한 응용 프로그램은 응용 프로그램별 로그 파일에 쓸 수 있습니다.

작업이 실행된 풀 노드가 여전히 있는 경우 로그 파일을 가져오고 볼 수 있습니다. 예를 들어 Azure 포털은 작업 또는 풀 노드에 대한 로그 파일을 나열하고 볼 수 있습니다. 또한 여러 API를 사용하면 작업 에서 [가져옵니다.](https://docs.microsoft.com/rest/api/batchservice/file/getfromtask)

풀 및 풀 노드가 자주 임시로 제공되기 때문에 노드가 지속적으로 추가되고 삭제되기 때문에 로그 파일이 유지되는 것이 좋습니다. [작업 출력 파일은](https://docs.microsoft.com/azure/batch/batch-task-output-files) 로그 파일을 Azure Storage에 저장하는 편리한 방법입니다.

### <a name="output-file-failures"></a>파일 오류 출력
모든 파일 업로드에서 Batch는 두 개의 로그 파일을 `fileuploadout.txt` 및 `fileuploaderr.txt` 컴퓨팅 노드에 씁니다. 이러한 로그 파일을 검사하여 특정 오류에 대해 자세히 확인할 수 있습니다. 예를 들어 작업 자체를 실행할 수 없기 때문에 파일 업로드를 시도하지 않은 경우 이러한 로그 파일이 존재하지 않습니다.  

## <a name="next-steps"></a>다음 단계

응용 프로그램에서 포괄적인 오류 검사를 구현하는지 확인합니다. 문제를 신속하게 감지하고 진단하는 것이 중요할 수 있습니다.
