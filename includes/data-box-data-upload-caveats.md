---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 09/30/2020
ms.author: alkohli
ms.openlocfilehash: 80e34c117c02c2126b46297c4219effa85e9caa6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98792389"
---
- 사전 생성된 공유에 파일을 직접 복사하지 마세요. 공유 아래에 폴더를 만든 다음, 해당 폴더에 파일을 복사해야 합니다.
- *StorageAccount_BlockBlob* 및 *StorageAccount_PageBlob* 의 폴더는 컨테이너입니다. 예를 들어 컨테이너는 *StorageAccount_BlockBlob/container* 및 *StorageAccount_PageBlob/container* 로 만들어집니다.
- *StorageAccount_AzureFiles* 에서 직접 만들어진 각각의 폴더는 Azure 파일 공유로 변환됩니다.
- 복사되는 개체가 이미 클라우드에 있는 Azure 개체(예: Blob 또는 파일)와 이름이 같으면 Data Box가 클라우드의 파일을 덮어씁니다.
- *StorageAccount_BlockBlob* 및 *StorageAccount_PageBlob* 공유에 기록된 모든 파일은 각각 블록 Blob 및 페이지 Blob으로 업로드됩니다.
- Azure Blob Storage는 디렉터리를 지원하지 않습니다. *StorageAccount_BlockBlob* 폴더에 폴더를 만든 경우, 가상 폴더가 Blob 이름으로 생성됩니다. Azure Files을 위한 실제 디렉터리 구조가 유지됩니다.
- *StorageAccount_BlockBlob* 및 *StorageAccount_PageBlob* 폴더 아래에 만들어진 빈 디렉터리 계층 구조(어떤 파일도 없음)는 업로드되지 않습니다.
- 데이터를 Azure에 업로드할 때 오류가 발생하는 경우 오류 로그는 대상 스토리지 계정에서 만들어집니다. 업로드가 완료되면 이 오류 로그 경로를 사용할 수 있으며, 로그를 검토하여 수정 작업을 수행할 수 있습니다. 업로드된 데이터를 확인하지 않고 원본에서 데이터를 삭제하지 마세요.
- [Azure Data Box를 통해 파일 ACL, 특성 및 타임스탬프 유지](../articles/databox/data-box-file-acls-preservation.md)의 지침을 사용하여 Azure Files에 데이터를 업로드하면 파일 메타데이터 및 NTFS 권한을 보존할 수 있습니다.
