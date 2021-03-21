---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 01/12/2021
ms.author: alkohli
ms.openlocfilehash: 6cabac4c59b09d146c1e42762402043622edb60e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98225168"
---
저장소 계정에 복사 되는 데이터의 크기에 대 한 제한은 다음과 같습니다. 업로드 하는 데이터가 이러한 제한을 준수 하는지 확인 합니다. 이러한 제한에 대 한 최신 정보는 [Blob storage의 확장성 및 성능 목표](../articles/storage/blobs/scalability-targets.md) 및 [Azure Files 확장성 및 성능 목표](../articles/storage/files/storage-files-scale-targets.md)를 참조 하세요.

| Azure Storage 계정에 복사되는 데이터의 크기                      | 기본 제한          |
|---------------------------------------------------------------------|------------------------|
| 블록 Blob 및 페이지 Blob                                            | 최대 제한은 [Azure 구독에 대해 정의 된 저장소 제한과](../articles/azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits) 같으며 Data Box를 포함 하 여 모든 원본의 데이터를 포함 합니다.   |
| Azure 파일                                                          | Data Box 순서를 만들기 전에 사용 하도록 설정 된 경우 Data Box는 대량 파일 공유 (100 TiB)를 지원 합니다. <br> 순서를 만들기 전에 사용 하도록 설정 하지 않은 경우 지원 되는 최대 파일 공유 크기는 5 TiB입니다. <br> Data Box는 저장소 계정의 모든 공유에 대해 총 100 TiB을 허용 하는 Azure Premium 파일 공유를 지원 합니다. <br> 복사 로그 및 감사 로그에 사용 되는 공간 때문에 사용 가능한 최대 용량이 약간 줄어듭니다. 최소 100 GiB은 복사 로그 및 감사 로그를 위해 예약 됩니다. 자세한 내용은 [Azure Data Box, Azure Data Box Heavy에 대 한 감사 로그](../articles/databox/data-box-audit-logs.md)를 참조 하세요. <br> *StorageAccount_AzureFiles* 의 모든 폴더는 이 제한을 따라야 합니다. <br> 자세한 내용은 [large file 공유 사용 및 만들기](../articles/storage/files/storage-files-how-to-create-large-file-share.md) 를 참조 하세요.      |
