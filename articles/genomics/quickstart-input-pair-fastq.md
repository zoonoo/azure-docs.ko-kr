---
title: FASTQ 파일 입력-Microsoft Genomics를 사용 하 여 워크플로 제출
titleSuffix: Azure
description: 이 문서에서는 msgen 클라이언트가 설치 되어 있고 서비스를 통해 샘플 데이터를 성공적으로 실행 했는지를 가정 합니다.
services: genomics
author: grhuynh
manager: cgronlun
ms.author: grhuynh
ms.service: genomics
ms.topic: conceptual
ms.date: 12/07/2017
ms.openlocfilehash: 2662a8f52c58a39916e5789fa9ed7fadd91216c0
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/06/2019
ms.locfileid: "57438808"
---
# <a name="submit-a-workflow-using-fastq-file-inputs-in-microsoft-genomics"></a>Microsoft Genomics에서 FASTQ 파일 입력을 사용하여 워크플로 제출

이 문서에는 입력된 파일이 하나의 쌍의 FASTQ 파일을 경우 Microsoft Genomics 서비스에 워크플로 제출 하는 방법을 보여 줍니다. 이 항목에서는 사용자가 이미 `msgen` 클라이언트를 설치하여 실행하고 있으며 Azure Storage 사용 방법에 익숙하다고 가정합니다. 제공된 된 샘플 데이터를 사용 하 여 워크플로 성공적으로 제출한 경우이 문서를 진행할 준비가 되었습니다. 

## <a name="set-up-upload-your-fastq-files-to-azure-storage"></a>설정: Azure storage에 업로드 한 FASTQ 파일
두 개의 파일(*reads_1.fq.gz*와 *reads_2.fq.gz*)이 있고, Azure의 개인 저장소 계정인 *myaccount*(**https://<span></span>myaccount.blob.core<span></span>.windows<span></span>.net<span></span>/inputs/reads_1<span></span>.fq<span></span>.gz<span></span>** 및 **https://<span></span>myaccount.blob.core.<span></span>windows<span></span>.net/<span></span>inputs/<span></span>reads_2.fq<span></span>.gz<span></span>**)에 파일을 업로드했다고 가정해보겠습니다. API URL과 액세스 키를 보유하고 있습니다. **https://<span></span>myaccount.blob.core<span></span>.windows<span></span>.net<span></span>/outputs<span></span>** 에 출력하려 합니다.


## <a name="submit-your-job-to-the-msgen-client"></a>`msgen` 클라이언트에게 작업을 제출합니다. 

다음은 `msgen` 클라이언트에게 제공해야 하는 최소한의 인수 집합입니다. 쉽게 알 수 있도록 줄 바꿈이 추가되었습니다.

Windows의 경우:

```
msgen submit ^
  --api-url-base <Genomics API URL> ^
  --access-key <Genomics access key> ^
  --process-args R=b37m1 ^
  --input-storage-account-name myaccount ^
  --input-storage-account-key <storage access key to "myaccount"> ^
  --input-storage-account-container inputs ^
  --input-blob-name-1 reads_1.fq.gz ^
  --input-blob-name-2 reads_2.fq.gz ^
  --output-storage-account-name myaccount ^
  --output-storage-account-key <storage access key to "myaccount"> ^
  --output-storage-account-container outputs
```

Unix의 경우:

```
msgen submit \
  --api-url-base <Genomics API URL> \
  --access-key <Genomics access key> \
  --process-args R=b37m1 \
  --input-storage-account-name myaccount \
  --input-storage-account-key <storage access key to "myaccount"> \
  --input-storage-account-container inputs \
  --input-blob-name-1 reads_1.fq.gz \
  --input-blob-name-2 reads_2.fq.gz \
  --output-storage-account-name myaccount \
  --output-storage-account-key <storage access key to "myaccount"> \
  --output-storage-account-container outputs
```


구성 파일 사용을 선호하는 경우 다음 내용이 포함됩니다.

```
api_url_base:                     <Genomics API URL>
access_key:                       <Genomics access key>
process_args:                     R=b37m1
input_storage_account_name:       myaccount
input_storage_account_key:        <storage access key to "myaccount">
input_storage_account_container:  inputs
input_blob_name_1:                reads_1.fq.gz
input_blob_name_2:                reads_2.fq.gz
output_storage_account_name:      myaccount
output_storage_account_key:       <storage access key to "myaccount">
output_storage_account_container: outputs
```

이 호출로 `config.txt` 파일 제출: `msgen submit -f config.txt`

## <a name="next-steps"></a>다음 단계
이 문서에서는 Azure Storage에 한 쌍의 FASTQ 파일을 업로드했고 `msgen` python 클라이언트를 통해 워크플로를 Microsoft Genomics 서비스에 제출했습니다. 워크플로 제출 및 Microsoft Genomics 서비스를 사용 하 여 사용할 수 있는 다른 명령에 대 한 자세한 내용은를 참조 하세요. 당사의 [FAQ](frequently-asked-questions-genomics.md)합니다. 
