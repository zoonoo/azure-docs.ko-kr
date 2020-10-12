---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 06/24/2020
ms.author: alkohli
ms.openlocfilehash: acaebcea59e765f5544f1bfbd692c6508f66a84a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91025154"
---
스토리지 계정에 복사되는 데이터 크기에 대한 제한은 다음과 같습니다. 업로드한 데이터가 이러한 제한을 준수하는지 확인합니다. 이러한 제한에 대 한 최신 정보는 [Blob storage의 확장성 및 성능 목표](../articles/storage/blobs/scalability-targets.md) 및 [Azure Files 확장성 및 성능 목표](../articles/storage/files/storage-files-scale-targets.md)를 참조 하세요.

| Azure Storage 계정에 복사되는 데이터의 크기                      | 기본 제한          |
|---------------------------------------------------------------------|------------------------|
| 블록 Blob 및 페이지 Blob                                            | 최대 제한은 [Azure 구독에 대해 정의 된 저장소 제한과](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#storage-limits) 같으며 Data Box를 포함 하 여 모든 원본의 데이터를 포함 합니다. |
| Azure 파일                                                          | Data Box 순서를 만들기 전에 사용 하도록 설정 하면 Data Box 100TiB (large file 공유)를 지원 합니다. <br> 순서를 만들기 전에 사용 하도록 설정 하지 않은 경우 지원 되는 최대 파일 공유 크기는 5 TiB입니다. <br> 프리미엄 파일 공유는 아직 지원 되지 않습니다.<br> *StorageAccount_AzureFiles*의 모든 폴더는 이 제한을 따라야 합니다. <br> 자세한 내용은 [large file 공유 사용 및 만들기](../articles/storage/files/storage-files-how-to-create-large-file-share.md) 를 참조 하세요.      |
