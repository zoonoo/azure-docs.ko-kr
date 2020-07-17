---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 11/05/2019
ms.author: alkohli
ms.openlocfilehash: 16647b6a13e64073ab570d36a8a380d0e36bd855
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "73799974"
---
- 사전 생성 된 공유에 직접 파일을 복사 하지 않습니다. 공유 아래에 폴더를 만든 후 해당 폴더에 파일을 복사 해야 합니다.
- *StorageAccount_BlockBlob* 및 *StorageAccount_PageBlob*의 폴더는 컨테이너입니다. 예를 들어 컨테이너는 *StorageAccount_BlockBlob/container* 및 *StorageAccount_PageBlob/container*로 만들어집니다.
- *StorageAccount_AzureFiles*에서 직접 만들어진 각각의 폴더는 Azure 파일 공유로 변환됩니다.
- 복사되는 개체와 동일한 이름을 사용하는 기존 Azure 개체(Blob 또는 파일)가 클라우드에 있는 경우, Data Box가 클라우드의 파일을 덮어씁니다.
- *StorageAccount_BlockBlob* 및 *StorageAccount_PageBlob* 공유에 기록된 모든 파일은 각각 블록 Blob 및 페이지 Blob으로 업로드됩니다.
- Azure blob storage는 디렉터리를 지원 하지 않습니다. *StorageAccount_BlockBlob* 폴더에 폴더를 만든 경우, 가상 폴더가 Blob 이름으로 생성됩니다. Azure Files을 위한 실제 디렉터리 구조가 유지됩니다.
- *StorageAccount_BlockBlob* 및 *StorageAccount_PageBlob* 폴더 아래에 생성 된 모든 빈 디렉터리 계층 구조 (파일 없음)는 업로드 되지 않습니다.
- 데이터를 Azure에 업로드할 때 오류가 발생하는 경우 오류 로그는 대상 스토리지 계정에서 만들어집니다. 업로드가 완료되면 오류 로그 경로를 사용할 수 있으며, 로그를 검토하여 정정 작업을 수행할 수 있습니다. 업로드 된 데이터를 확인 하지 않고 원본에서 데이터를 삭제 하지 않습니다.
- 데이터를 Azure Files에 업로드할 때 파일 메타 데이터 및 NTFS 권한은 유지 되지 않습니다. 예를 들어 데이터를 복사할 때 파일의 *마지막 수정 된 특성* 은 유지 되지 않습니다.
