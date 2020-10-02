---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 09/30/2020
ms.author: alkohli
ms.openlocfilehash: 80411ac191d0385ddafd4a3cc490d322ce039689
ms.sourcegitcommit: 4bebbf664e69361f13cfe83020b2e87ed4dc8fa2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2020
ms.locfileid: "91644160"
---
- 사전 생성 된 공유에 직접 파일을 복사 하지 않습니다. 공유 아래에 폴더를 만든 후 해당 폴더에 파일을 복사 해야 합니다.
- *StorageAccount_BlockBlob* 및 *StorageAccount_PageBlob*의 폴더는 컨테이너입니다. 예를 들어 컨테이너는 *StorageAccount_BlockBlob/container* 및 *StorageAccount_PageBlob/container*로 만들어집니다.
- *StorageAccount_AzureFiles*에서 직접 만들어진 각각의 폴더는 Azure 파일 공유로 변환됩니다.
- 복사할 개체가 이미 클라우드에 있는 blob 또는 파일과 같은 Azure 개체와 동일한 이름을 가지는 경우 Data Box는 클라우드의 파일을 덮어씁니다 (예:).
- *StorageAccount_BlockBlob* 및 *StorageAccount_PageBlob* 공유에 기록된 모든 파일은 각각 블록 Blob 및 페이지 Blob으로 업로드됩니다.
- Azure blob storage는 디렉터리를 지원 하지 않습니다. *StorageAccount_BlockBlob* 폴더에 폴더를 만든 경우, 가상 폴더가 Blob 이름으로 생성됩니다. Azure Files을 위한 실제 디렉터리 구조가 유지됩니다.
- *StorageAccount_BlockBlob* 및 *StorageAccount_PageBlob* 폴더 아래에 생성 된 모든 빈 디렉터리 계층 구조 (파일 없음)는 업로드 되지 않습니다.
- 데이터를 Azure에 업로드할 때 오류가 발생하는 경우 오류 로그는 대상 스토리지 계정에서 만들어집니다. 업로드가 완료 되 면이 오류 로그의 경로를 사용할 수 있으며, 로그를 검토 하 여 정정 작업을 수행할 수 있습니다. 업로드 된 데이터를 확인 하지 않고 원본에서 데이터를 삭제 하지 않습니다.
- [Azure Data Box를 사용 하 여 파일 acl, 특성 및 타임 스탬프를 유지](../articles/databox/data-box-file-acls-preservation.md)하는 지침을 사용 하 여 Azure Files에 데이터를 업로드 하면 파일 메타 데이터 및 NTFS 권한을 보존할 수 있습니다.