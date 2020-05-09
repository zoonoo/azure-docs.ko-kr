---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 06/18/2019
ms.author: alkohli
ms.openlocfilehash: 3e1f9c225a57e7d41f85c2a92dac989453057c51
ms.sourcegitcommit: 4499035f03e7a8fb40f5cff616eb01753b986278
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/03/2020
ms.locfileid: "82736980"
---
스토리지 계정에 복사되는 데이터 크기에 대한 제한은 다음과 같습니다. 업로드한 데이터가 이러한 제한을 준수하는지 확인합니다. 이러한 제한에 대 한 최신 정보는 [Blob storage의 확장성 및 성능 목표](../articles/storage/blobs/scalability-targets.md) 및 [Azure Files 확장성 및 성능 목표](../articles/storage/files/storage-files-scale-targets.md)를 참조 하세요.

| Azure Storage 계정에 복사되는 데이터의 크기                      | 기본 제한          |
|---------------------------------------------------------------------|------------------------|
| 블록 Blob 및 페이지 Blob                                            | 미국 및 유럽의 경우 2 PB<br>영국을 포함 하는 다른 모든 지역에 대해 500 TB  <br> Data Box를 비롯한 모든 원본 데이터를 포함합니다.|
| Azure 파일                                                          | 표준 파일 공유의 최대 크기 100TiB *, 5TB, 공유 당 100TiB 프리미엄 파일 공유<br> *StorageAccount_AzureFiles*의 모든 폴더는 이 제한을 따라야 합니다.       |
