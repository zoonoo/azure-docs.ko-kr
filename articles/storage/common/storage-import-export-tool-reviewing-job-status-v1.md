---
title: Azure Import/Export 작업 상태 검토 - v1 | Microsoft Docs
description: Import/Export 작업의 상태를 보기 위해 가져오기 또는 내보내기 작업을 실행할 때 생성된 로그 파일을 사용하는 방법에 알아봅니다.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/26/2017
ms.author: muralikk
ms.component: common
ms.openlocfilehash: c5b9d1993c9e90411c7b05d9874721a159275f22
ms.sourcegitcommit: d211f1d24c669b459a3910761b5cacb4b4f46ac9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/06/2018
ms.locfileid: "44021831"
---
# <a name="reviewing-azure-importexport-job-status-with-copy-log-files"></a>복사 로그 파일로 Azure Import/Export 작업 상태 검토 | Microsoft Docs
Microsoft Azure Import/Export 서비스가 가져오기 또는 내보내기 작업과 연결된 드라이브를 처리하는 경우 blob을 가져오는 곳에서 또는 내보내는 곳으로 저장소 계정에 복사 로그 파일을 씁니다. 로그 파일에는 가져오거나 내보낸 각 파일에 대한 자세한 상태가 포함되어 있습니다. 완료된 작업의 상태를 쿼리할 때 각 복사 로그 파일에 대한 URL이 반환됩니다. 자세한 내용은 [작업 가져오기](https://docs.microsoft.com/rest/api/storageimportexport/Jobs/Get)를 참조하세요.  

## <a name="example-urls"></a>예제 URL

다음 예제는 두 드라이브를 사용하는 가져오기 작업에 대한 복사 로그 파일 URL입니다.  
  
 `http://myaccount.blob.core.windows.net/ImportExportStatesPath/waies/myjob_9WM35C2V_20130921-034307-902_error.xml`  
  
 `http://myaccount.blob.core.windows.net/ImportExportStatesPath/waies/myjob_9WM45A6Q_20130921-042122-021_error.xml`  
  
 상태 코드의 전체 목록 및 복사 로그 형식은 [Import/Export 서비스 로그 파일 형식](../storage-import-export-file-format-log.md)을 참조하세요.  
  
## <a name="next-steps"></a>다음 단계
 
 * [Azure Import/Export 도구 설정](storage-import-export-tool-setup-v1.md)   
 * [가져오기 작업을 위한 하드 드라이브 준비](../storage-import-export-tool-preparing-hard-drives-import-v1.md)   
 * [가져오기 작업 복구](../storage-import-export-tool-repairing-an-import-job-v1.md)   
 * [내보내기 작업 복구](../storage-import-export-tool-repairing-an-export-job-v1.md)   
 * [Azure Import/Export 도구 문제 해결](storage-import-export-tool-troubleshooting-v1.md)
