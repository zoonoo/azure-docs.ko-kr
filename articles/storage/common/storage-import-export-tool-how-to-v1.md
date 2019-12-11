---
title: Azure Import/Export 도구 사용-v1 | Microsoft Docs
description: 가져오기/내보내기 도구를 사용 하 여 가져오기 작업을 위해 하드 드라이브를 준비 하거나, 가져오기 작업을 복구 하거나, 내보내기 작업을 복구 하는 방법에 대해 알아봅니다.
author: twooley
services: storage
ms.service: storage
ms.topic: article
ms.date: 1/15/2017
ms.author: twooley
ms.subservice: common
ms.openlocfilehash: 4cd5fde2f47cca1b03efbc217d7764ac429d15fb
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74973937"
---
# <a name="using-the-azure-importexport-tool-classic-deployment-model"></a>Azure Import/Export 도구 사용 (클래식 배포 모델)

Azure Import/Export 도구 (WAImportExport .exe)는 Azure Import/Export 서비스에 대 한 작업을 만들고 관리 하는 데 사용 되므로 많은 양의 데이터를 Azure Blob Storage으로 전송 하거나 외부로 전송할 수 있습니다.

이 설명서는 Azure Import/Export 도구의 클래식 배포 모델에 대 한 것입니다. 최신 버전의 도구를 사용 하는 방법에 대 한 자세한 내용은 [Azure Import/Export 도구 사용](../storage-import-export-tool-how-to.md)을 참조 하세요.

다음 문서에서는 다음을 수행 하는 방법을 보여 줍니다.

- 가져오기/내보내기 도구를 설치 하 고 설정 합니다.
- 드라이브에서 데이터를 가져올 수 있도록 하드 드라이브를 준비 하는 작업을 Azure Blob Storage 합니다.
- 로그 파일 복사를 사용 하 여 작업의 상태를 검토 합니다. 
- 가져오기 작업을 복구 합니다. 
- 내보내기 작업을 복구 합니다. 
- 프로세스 중에 문제가 발생 한 경우 Azure Import/Export 도구 문제를 해결 합니다. 

## <a name="next-steps"></a>Következő lépések

* [WAImportExport 도구 설정](../storage-import-export-tool-how-to.md)
