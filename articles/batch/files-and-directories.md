---
title: Azure Batch의 파일 및 디렉터리
description: 파일 및 디렉터리에 대해 살펴보고 개발 관점에서 Azure Batch 워크플로에서 이들을 사용하는 방법을 알아봅니다.
ms.topic: conceptual
ms.date: 08/03/2020
ms.openlocfilehash: eafea6c234c3b261521f8a791b7a03e25388f02a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87552642"
---
# <a name="files-and-directories-in-azure-batch"></a>Azure Batch의 파일 및 디렉터리

Azure Batch 각 태스크에는 파일 및 디렉터리를 만들 수 있는 작업 디렉터리가 있습니다. 태스크가 실행하는 프로그램, 처리하는 데이터 및 수행하는 처리의 출력을 저장하는 데 작업 디렉터리를 사용할 수 있습니다. 태스크의 모든 파일 및 디렉터리는 태스크 사용자가 소유합니다.

Batch 서비스는 노드의 파일 시스템 일부를 *루트 디렉터리*로 노출합니다. 이 루트 디렉터리는 OS 드라이브에서 직접이 아니라 VM의 임시 저장소 드라이브에 있습니다.

태스크는 `AZ_BATCH_NODE_ROOT_DIR` 환경 변수를 참조하여 루트 디렉터리에 액세스할 수 있습니다. 환경 변수 사용에 대한 자세한 내용은 [태스크에 대한 환경 설정](jobs-and-tasks.md#environment-settings-for-tasks)을 참조하세요.

## <a name="root-directory-structure"></a>루트 디렉터리 구조

루트 디렉터리는 다음과 같은 디렉터리 구조를 포함합니다.

![계산 노드 디렉터리 구조의 스크린샷](media\files-and-directories\node-folder-structure.png)

- **애플리케이션**: 컴퓨팅 노드에 설치된 애플리케이션 패키지의 세부 사항에 대한 정보를 포함합니다. 태스크는 `AZ_BATCH_APP_PACKAGE` 환경 변수를 참조하여 이 디렉터리에 액세스할 수 있습니다.

- **fsmounts**: 이 디렉터리에는 컴퓨팅 노드에 탑재된 모든 파일 시스템이 포함됩니다. 태스크는 `AZ_BATCH_NODE_MOUNTS_DIR` 환경 변수를 참조하여 이 디렉터리에 액세스할 수 있습니다. 자세한 내용은 [Batch 풀에 가상 파일 시스템 탑재](virtual-file-mount.md)를 참조하세요.

- **shared**: 이 디렉터리는 노드에서 실행되는 *모든* 태스크에 대한 읽기/쓰기 액세스를 제공합니다. 노드에서 실행되는 태스크는 이 디렉터리에서 파일을 만들고, 읽고, 업데이트하고, 삭제할 수 있습니다. 태스크는 `AZ_BATCH_NODE_SHARED_DIR` 환경 변수를 참조하여 이 디렉터리에 액세스할 수 있습니다.

- **startup**: 이 디렉터리는 시작 태스크에서 작업 디렉터리로 사용됩니다. 시작 태스크에서 노드에 다운로드한 모든 파일은 여기에 저장됩니다. 시작 태스크는 이 디렉터리 아래에서 파일을 만들고, 읽고, 업데이트하고, 삭제할 수 있습니다. 태스크는 `AZ_BATCH_NODE_STARTUP_DIR` 환경 변수를 참조하여 이 디렉터리에 액세스할 수 있습니다.

- **volatile**: 이 디렉터리는 내부용입니다. 이 디렉터리에 있는 파일이나 디렉터리 자체가 나중에도 사용될 것이라고 보장할 수 없습니다.

- **workitems**: 이 디렉터리에는 컴퓨팅 노드의 작업 및 해당 태스크에 대한 디렉터리가 포함되어 있습니다.

    **workitems** 디렉터리 내에서는 노드에서 실행되는 각 태스크에 대해 **Tasks** 디렉터리가 만들어집니다. 이 디렉터리는 `AZ_BATCH_TASK_DIR` 환경 변수를 참조하여 액세스할 수 있습니다.

    각 **Tasks** 디렉터리 내에서 Batch 서비스는 `AZ_BATCH_TASK_WORKING_DIR` 환경 변수에 의해 고유 경로가 지정되는 작업 디렉터리(`wd`)를 만듭니다. 이 디렉터리는 태스크에 대한 읽기/쓰기 액세스를 제공합니다. 태스크는 이 디렉터리 아래에서 파일을 만들고, 읽고, 업데이트하고, 삭제할 수 있습니다. 이 디렉터리는 태스크에 대해 지정된 *RetentionTime* 제약 조건에 따라 유지됩니다.

    `stdout.txt` 및 `stderr.txt` 파일은 태스크를 실행하는 동안 **Tasks** 폴더에 기록됩니다.

> [!IMPORTANT]
> 풀에서 노드가 제거되면 노드에 저장된 모든 파일이 제거됩니다.

## <a name="next-steps"></a>다음 단계

- Azure Batch의 [오류 처리 및 검색](error-handling.md)에 대해 알아봅니다.