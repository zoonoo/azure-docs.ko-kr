---
title: Azure Import/Export의 가져오기 및 내보내기 문제 해결 | Microsoft Docs
description: Azure Import/Export를 사용할 때 일반적인 문제를 처리 하는 방법을 알아봅니다.
author: v-dalc
services: storage
ms.service: storage
ms.topic: troubleshooting
ms.date: 01/19/2021
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: 555529b52d586078ba7e1832373ec126ba545c11
ms.sourcegitcommit: 75041f1bce98b1d20cd93945a7b3bd875e6999d0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/22/2021
ms.locfileid: "98706428"
---
# <a name="troubleshoot-issues-in-azure-importexport"></a>Azure Import/Export 문제 해결
이 문서에서는 Azure Import/Export에서 데이터를 가져오고 내보낼 때 일반적인 문제를 해결 하는 방법을 설명 합니다.

## <a name="a-copy-session-failed-what-i-should-do"></a>복사 세션이 실패 했습니다. 어떻게 해야 하나요?  

복사 세션이 실패 하면 다음과 같은 두 가지 옵션을 사용할 수 있습니다.  
* 오류를 다시 시도할 수 있는 경우 (예: 네트워크 공유가 짧은 기간 동안 오프 라인 상태이 고 이제 다시 온라인 상태가 된 경우) 복사 세션을 다시 시작할 수 있습니다.
* 오류를 다시 시도할 수 없는 경우 (예: 명령줄 매개 변수에서 잘못 된 원본 파일 디렉터리를 지정한 경우) 복사 세션을 중단 해야 합니다.
 
<!--For information about resuming and aborting copy sessions, see [Preparing Hard Drives for an Import Job](../storage-import-export-tool-preparing-hard-drives-import-v1.md  - Article we removed from TOC. File remains.-->

## <a name="i-cant-resume-or-abort-a-copy-session"></a>복사 세션을 중단하거나 다시 시작할 수 없습니다.

복사 세션이 드라이브에 대한 첫 번째 복사 세션인 경우 "첫 번째 복사 세션은 다시 시작하거나 중단할 수 없습니다."라는 오류 메시지가 표시됩니다. 이 경우 이전 저널 파일을 삭제하고 명령을 다시 실행하면 됩니다.  

복사 세션이 드라이브의 첫 번째 세션이 아닌 경우에는 항상 세션을 다시 시작 하거나 중단할 수 있습니다.  

## <a name="i-lost-the-journal-file-can-i-still-create-the-job"></a>저널 파일이 손실 되었습니다. 그래도 작업을 만들 수 있나요?

드라이브의 저널 파일에는 데이터 복사본의 전체 세션 정보가 포함 되어 있습니다. 드라이브에 파일을 추가 하는 경우 저널 파일을 사용 하 여 가져오기 작업을 만듭니다. 저널 파일이 손실 된 경우 드라이브의 모든 복사 세션을 다시 실행 해야 합니다.

## <a name="next-steps"></a>다음 단계

* [Azure Import/Export 도구 설정](storage-import-export-tool-setup-v1.md)
* [가져오기 작업을 위한 하드 드라이브 준비](storage-import-export-data-to-blobs.md#step-1-prepare-the-drives)
* [복사 로그 파일을 사용 하 여 작업 상태 검토](storage-import-export-tool-reviewing-job-status-v1.md)
* [가져오기 작업 복구](storage-import-export-tool-repairing-an-import-job-v1.md)
* [내보내기 작업 복구](storage-import-export-tool-repairing-an-export-job-v1.md)