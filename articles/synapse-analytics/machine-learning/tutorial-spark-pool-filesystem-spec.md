---
title: '자습서: FSSPEC를 사용하여 Synapse Analytics Spark 풀에서 ADLS 데이터 읽기/쓰기'
description: 기계 학습 워크로드에 FSSPEC를 사용하여 전용 Spark 풀에서 ADLS 데이터를 읽고 쓰는 방법에 대한 자습서입니다.
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: tutorial
ms.reviewer: jrasnick, garye
ms.date: 06/07/2021
author: AjAgr
ms.author: ajagarw
ms.openlocfilehash: ecd0b923939ab1da724b46272dbaf60c44389d3d
ms.sourcegitcommit: f9e368733d7fca2877d9013ae73a8a63911cb88f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111904315"
---
# <a name="tutorial-use-fsspec-to-readwrite-adls-data-in-synapse-analytics-spark-pool"></a>자습서: FSSPEC를 사용하여 Synapse Analytics Spark 풀에서 ADLS 데이터 읽기/쓰기

FSSPEC(파일 시스템 사양)를 통해 Azure Synapse Analytics의 전용 Spark 풀에 연결된 서비스를 사용하여 ADLS(Azure Data Lake Storage)에 데이터를 읽고 쓰는 방법을 알아봅니다.

이 자습서에서는 다음과 같은 작업을 수행하는 방법을 알아봅니다.

> [!div class="checklist"]
> - 전용 Spark 세션에서 ADLS 데이터를 읽고 씁니다.

Azure 구독이 없는 경우 [시작하기 전에 체험 계정을 만듭니다](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>사전 요구 사항

- [Azure Synapse Analytics 작업 영역](../get-started-create-workspace.md)(기본 스토리지로 구성된 Azure Data Lake Storage Gen2 스토리지 계정이 있음). 사용하는 Data Lake Storage Gen2 파일 시스템의 *Storage Blob 데이터 기여자* 여야 합니다.
- Azure Synapse Analytics 작업 영역의 Spark 풀. 자세한 내용은 [Azure Synapse에서 Spark 풀 만들기](../get-started-analyze-spark.md)를 참조하세요.

## <a name="sign-in-to-the-azure-portal"></a>Azure Portal에 로그인

[Azure Portal](https://portal.azure.com/)에 로그인합니다.

## <a name="create-linked-services"></a>연결된 서비스 만들기

Azure Synapse Analytics에서 연결된 서비스는 다른 서비스에 대한 연결 정보를 정의합니다. 본 섹션에서는 Azure Synapse Analytics와 Azure Data Lake Storage Gen2 연결 서비스를 추가합니다.

1. Azure Synapse Studio를 열고 **관리** 탭을 선택합니다.
1. **외부 연결** 에서 **연결된 서비스** 를 선택합니다.
1. 연결된 서비스를 추가하려면 **새로 만들기** 를 선택합니다.
1. 목록에서 Azure Data Lake Storage Gen2 타일을 선택하고 **계속** 을 선택합니다.
1. 인증 자격 증명을 입력합니다. 계정 키는 현재 지원되는 인증 유형입니다. **연결 테스트** 를 선택하여 자격 증명이 올바른지 확인합니다. **만들기** 를 선택합니다.

   :::image type="content" source="media/tutorial-spark-pool-filesystem-spec/create-adls-linked-service.png" alt-text="ADLS Gen2 Storage 액세스 키를 사용하여 연결된 서비스를 만듭니다.":::


## <a name="readwrite-data-using-storage-account-name-and-key"></a>스토리지 계정 이름 및 키를 사용하여 데이터 읽기/쓰기

FSSPEC는 스토리지 계정 이름 및 키를 직접 지정하여 ADLS 데이터를 읽고 쓸 수 있습니다.

1. Synapse 스튜디오에서 **데이터** > **연결** > **Azure Data Lake Storage Gen2** 를 엽니다. 기본 스토리지 계정에 데이터를 업로드합니다.

1. 다음 코드를 실행합니다.

   > [!NOTE]
   > 이 스크립트를 실행하기 전에 파일 URL, ADLS Gen2 스토리지 이름 및 키를 업데이트합니다.

   ```PYSPARK
   # To read data
   import fsspec
   import pandas

   adls_account_name = '' #Provide exact ADLS account name
   adls_account_key = '' #Provide exact ADLS account key

   fsspec_handle = fsspec.open('abfs://<container>/<path-to-file>', account_name=adls_account_name, account_key=adls_account_key)

   with fsspec_handle.open() as f:
       df = pandas.read_csv(f)

   # To write data
   import fsspec
   import pandas

   adls_account_name = '' #Provide exact ADLS account name 
   adls_account_key = '' #Provide exact ADLS account key 
   
   data = pandas.DataFrame({'Name':['Tom', 'nick', 'krish', 'jack'], 'Age':[20, 21, 19, 18]})
   
   fsspec_handle = fsspec.open('abfs://<container>/<path-to-file>', account_name=adls_account_name,    account_key=adls_account_key, mode="wt")
   
   with fsspec_handle.open() as f:
    data.to_csv(f)
   ```

## <a name="readwrite-data-using-linked-service"></a>연결된 서비스를 사용하여 데이터 읽기/쓰기

FSSPEC는 연결된 서비스 이름을 지정하여 ADLS 데이터를 읽고 쓸 수 있습니다.


1. Synapse 스튜디오에서 **데이터** > **연결** > **Azure Data Lake Storage Gen2** 를 엽니다. 기본 스토리지 계정에 데이터를 업로드합니다.

1. 다음 코드를 실행합니다.

   > [!NOTE]
   > 실행하기 전에 이 스크립트에서 파일 URL, 연결된 서비스 이름 및 ADLS Gen2 스토리지 이름을 업데이트합니다.

   ```PYSPARK
   # To read data
   import fsspec
   import pandas
   
   adls_account_name = '' #Provide exact ADLS account name
   sas_key = TokenLibrary.getConnectionString(<LinkedServiceName>)
   
   fsspec_handle = fsspec.open('abfs://<container>/<path-to-file>', account_name =    adls_account_name, sas_token=sas_key)
   
   with fsspec_handle.open() as f:
       df = pandas.read_csv(f)

   # To write data
   import fsspec
   import pandas
   
   adls_account_name = '' #Provide exact ADLS account name
   
   data = pandas.DataFrame({'Name':['Tom', 'nick', 'krish', 'jack'], 'Age':[20, 21, 19, 18]})
   sas_key = TokenLibrary.getConnectionString(<LinkedServiceName>) 
   
   fsspec_handle = fsspec.open('abfs://<container>/<path-to-file>', account_name =    adls_account_name, sas_token=sas_key, mode="wt") 
   
   with fsspec_handle.open() as f:
       data.to_csv(f) 
   ```

## <a name="next-steps"></a>다음 단계

- [Azure Synapse Analytics의 Machine Learning 기능](what-is-machine-learning.md)