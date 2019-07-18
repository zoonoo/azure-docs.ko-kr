---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 06/18/2019
ms.author: alkohli
ms.openlocfilehash: 0975df8ee9da4a239dbd72e468b967c88ab3feed
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/20/2019
ms.locfileid: "67277361"
---
저장소 계정에 복사되는 데이터 크기에 대한 제한은 다음과 같습니다. 업로드한 데이터가 이러한 제한을 준수하는지 확인합니다. 이러한 제한에 대한 최신 정보는 [Azure Blob Storage 크기 조정 목표](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets#azure-blob-storage-scale-targets) 및 [Azure Files 크기 조정 목표](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets#azure-files-scale-targets)로 이동합니다.

| Azure 저장소 계정에 복사되는 데이터의 크기                      | 기본 제한          |
|---------------------------------------------------------------------|------------------------|
| 블록 Blob 및 페이지 Blob                                            | 2 미국 및 유럽의 경우에 합니다.<br>500TB 그 밖의 국가 대 한 영국을 포함 합니다.  <br> Data Box를 비롯한 모든 원본 데이터를 포함합니다.|
| Azure 파일                                                          | 공유 당 5TB입니다.<br> *StorageAccount_AzureFiles*의 모든 폴더는 이 제한을 따라야 합니다.       |
