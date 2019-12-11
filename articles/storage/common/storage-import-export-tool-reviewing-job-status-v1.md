---
title: Azure Import/Export 작업 상태 검토-v1 | Microsoft Docs
description: 가져오기/내보내기 작업의 상태를 보기 위해 가져오기 또는 내보내기 작업을 실행할 때 생성 된 로그 파일을 사용 하는 방법에 대해 알아봅니다.
author: twooley
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/26/2017
ms.author: twooley
ms.subservice: common
ms.openlocfilehash: 075af43796d3ca3dfef4b48f8f98f20903af3308
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74978971"
---
# <a name="reviewing-azure-importexport-job-status-with-copy-log-files"></a>복사 로그 파일을 사용 하 여 Azure Import/Export 작업 상태 검토
Microsoft Azure Import/Export 서비스는 가져오기 또는 내보내기 작업과 연결 된 드라이브를 처리할 때 blob을 가져오거나 내보낼 저장소 계정에 복사 로그 파일을 기록 합니다. 로그 파일에는 가져오거나 내보낸 각 파일에 대 한 자세한 상태가 포함 됩니다. 완료 된 작업의 상태를 쿼리할 때 각 복사 로그 파일에 대 한 URL이 반환 됩니다. 자세한 내용은 [작업 가져오기](https://docs.microsoft.com/rest/api/storageimportexport/Jobs/Get) 를 참조 하세요.  

## <a name="example-urls"></a>Url 예

다음은 두 개의 드라이브가 있는 가져오기 작업의 복사 로그 파일에 대 한 Url의 예입니다.  
  
 `http://myaccount.blob.core.windows.net/ImportExportStatesPath/waies/myjob_9WM35C2V_20130921-034307-902_error.xml`  
  
 `http://myaccount.blob.core.windows.net/ImportExportStatesPath/waies/myjob_9WM45A6Q_20130921-042122-021_error.xml`  
  
 복사 로그 형식과 상태 코드의 전체 목록은 [Import/Export 서비스 로그 파일 형식](../storage-import-export-file-format-log.md) 을 참조 하세요.  
  
## <a name="next-steps"></a>Következő lépések
 
 * [Azure Import/Export 도구 설정](storage-import-export-tool-setup-v1.md)   
 * [Merevlemezek előkészítése importálási feladatokhoz](../storage-import-export-tool-preparing-hard-drives-import-v1.md)   
 * [Importálási feladat javítása](../storage-import-export-tool-repairing-an-import-job-v1.md)   
 * [Exportálási feladat javítása](../storage-import-export-tool-repairing-an-export-job-v1.md)   
 * [Az Azure Import/Export eszköz hibaelhárítása](storage-import-export-tool-troubleshooting-v1.md)
