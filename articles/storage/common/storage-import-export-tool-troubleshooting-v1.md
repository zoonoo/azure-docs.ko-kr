---
title: Azure Import/Export 도구 문제 해결 | Microsoft Docs
description: Azure Import/Export 도구를 사용할 때 발생 하는 일반적인 문제와이를 처리 하는 방법에 대해 알아봅니다.
author: twooley
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/15/2017
ms.author: twooley
ms.subservice: common
ms.openlocfilehash: 7e463e1cdd340f852af46e39cca0dd9bfce7b8da
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74978937"
---
# <a name="troubleshooting-the-azure-importexport-tool"></a>Az Azure Import/Export eszköz hibaelhárítása
Microsoft Azure Import/Export 도구는 문제가 발생 하는 경우 오류 메시지를 반환 합니다. 이 항목에서는 사용자가 실행할 수 있는 몇 가지 일반적인 문제를 보여 줍니다.  
  
## <a name="a-copy-session-fails-what-i-should-do"></a>복사 세션이 실패 하 고 어떻게 해야 하나요?  
 복사 세션이 실패 하면 다음과 같은 두 가지 옵션이 있습니다.  
  
 오류를 다시 시도할 수 있는 경우 (예: 네트워크 공유가 짧은 기간 동안 오프 라인 이었던 상태에서 이제 다시 온라인 상태가 된 경우) 복사 세션을 다시 시작할 수 있습니다. 명령줄 매개 변수에서 잘못 된 원본 파일 디렉터리를 지정한 경우와 같이 오류를 다시 시도할 수 없는 경우 복사 세션을 중단 해야 합니다. 복사 세션을 다시 시작 하 고 중단 하는 방법에 대 한 자세한 내용은 [가져오기 작업을 위한 하드 드라이브 준비](../storage-import-export-tool-preparing-hard-drives-import-v1.md) 를 참조 하세요.  
  
## <a name="i-cant-resume-or-abort-a-copy-session"></a>복사 세션을 다시 시작 하거나 중단할 수 없습니다.  
 복사 세션이 드라이브의 첫 번째 복사 세션 인 경우 오류 메시지는 "첫 번째 복사 세션을 다시 시작 하거나 중단할 수 없습니다."입니다. 이 경우 이전 저널 파일을 삭제 하 고 명령을 다시 실행할 수 있습니다.  
  
 복사 세션이 드라이브의 첫 번째 세션이 아닌 경우 항상 다시 시작 하거나 중단할 수 있습니다.  
  
## <a name="i-lost-the-journal-file-can-i-still-create-the-job"></a>저널 파일이 손실 되었습니다. 그래도 작업을 만들 수 있나요?  
 드라이브의 저널 파일에는이 드라이브에 데이터를 복사 하는 방법에 대 한 전체 정보가 포함 되어 있습니다 .이 파일은 드라이브에 파일을 추가 하는 데 필요 하며 가져오기 작업을 만드는 데 사용 됩니다. 저널 파일이 손실 된 경우 드라이브의 모든 복사 세션을 다시 실행 해야 합니다.  
  
## <a name="next-steps"></a>Következő lépések
 
* [Azure import/export 도구 설정](../storage-import-export-tool-setup-v1.md)   
* [Merevlemezek előkészítése importálási feladatokhoz](../storage-import-export-tool-preparing-hard-drives-import-v1.md)   
* [Feladatok állapotának áttekintése a másolási naplófájlok segítségével](../storage-import-export-tool-reviewing-job-status-v1.md)   
* [Importálási feladat javítása](../storage-import-export-tool-repairing-an-import-job-v1.md)   
* [Exportálási feladat javítása](../storage-import-export-tool-repairing-an-export-job-v1.md)
