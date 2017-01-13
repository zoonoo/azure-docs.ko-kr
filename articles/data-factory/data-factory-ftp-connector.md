---
title: "FTP 서버에서 데이터 이동 | Microsoft Docs"
description: "Azure Data Factory를 사용하여 FTP 서버에서 데이터를 이동하는 방법에 대해 알아봅니다."
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.assetid: eea3bab0-a6e4-4045-ad44-9ce06229c718
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/12/2017
ms.author: spelluru
translationtype: Human Translation
ms.sourcegitcommit: 6ec8ac288a4daf6fddd6d135655e62fad7ae17c2
ms.openlocfilehash: f27d978eab3aba52ee9a51a5f284b592e3edd6af


---
# <a name="move-data-from-an-ftp-server-using-azure-data-factory"></a>Azure Data Factory를 사용하여 FTP 서버에서 데이터 이동
이 문서에서는 Azure Data Factory의 복사 작업을 사용하여 FTP 서버의 데이터를 지원되는 싱크 데이터 저장소로 이동하는 방법에 대해 간략하게 설명합니다. 이 문서는 복사 작업 및 지원되는 데이터 저장소를 원본/싱크로 사용한 데이터 이동의 일반적인 개요를 보여주는 [데이터 이동 활동](data-factory-data-movement-activities.md) 문서를 작성합니다.

현재 데이터 팩터리는 FTP 서버에서 다른 데이터 저장소로의 데이터 이동만 지원하며, 다른 데이터 저장소에서 FTP 서버로의 데이터 이동은 지원하지 않습니다. 온-프레미스 및 클라우드 FTP 서버를 지원합니다.

**온-프레미스** FTP 서버의 데이터를 클라우드 데이터 저장소(예: Azure Blob Storage)로 이동하는 경우 데이터 관리 게이트웨이를 설치하여 사용합니다. 데이터 관리 게이트웨이는 온-프레미스 컴퓨터에 설치되는 클라이언트 컴퓨터로, 클라우드 서비스가 온-프레미스 리소스에 연결할 수 있게 합니다. 게이트웨이에 대한 자세한 내용은 [데이터 관리 게이트웨이](data-factory-data-management-gateway.md)를 참조하세요. 게이트웨이 설정 및 사용에 대한 단계별 지침은 [온-프레미스 위치 및 클라우드 간 데이터 이동](data-factory-move-data-between-onprem-and-cloud.md) 문서를 참조하세요. 서버가 Azure IaaS 가상 컴퓨터(VM)인 경우에도 게이트웨이를 사용하여 FTP 서버를 연결합니다.

동일한 온-프레미스 컴퓨터나 Azure IaaS VM에 FTP 서버로 게이트웨이를 설치할 수 있습니다. 그러나 리소스 경합을 방지하고 성능 향상을 꾀하려면 게이트웨이를 별도 컴퓨터나 별도의 Azure IaaS VM에 설치하는 것이 좋습니다. 별도 컴퓨터에 게이트웨이를 설치하는 경우 컴퓨터가 FTP 서버에 액세스할 수 있어야 합니다.

## <a name="copy-data-wizard"></a>데이터 복사 마법사
FTP 서버에서 데이터를 복사하는 파이프라인을 만드는 가장 쉬운 방법은 데이터 복사 마법사를 사용하는 것입니다. 데이터 복사 마법사를 사용하여 파이프라인을 만드는 방법에 대한 빠른 연습은 [자습서: 복사 마법사를 사용하여 파이프라인 만들기](data-factory-copy-data-wizard-tutorial.md) 를 참조하세요.

다음 예에서는 [Azure 포털](data-factory-copy-activity-tutorial-using-azure-portal.md), [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) 또는 [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)을 사용하여 파이프라인을 만드는 데 사용할 수 있는 샘플 JSON 정의를 제공합니다.

## <a name="sample-copy-data-from-ftp-server-to-azure-blob"></a>샘플: FTP 서버에서 Azure Blob로 데이터 복사
이 샘플은 FTP 서버에서 Azure Blob Storage로 데이터를 복사하는 방법을 보여줍니다. 그러나 Azure Data Factory의 복사 작업을 사용하여 **여기** 에 설명한 싱크로 [직접](data-factory-data-movement-activities.md#supported-data-stores-and-formats) 데이터를 복사할 수 있습니다.  

이 샘플에는 다음 데이터 팩터리 엔터티가 있습니다.

* [FtpServer](#ftp-linked-service-properties) 형식의 연결된 서비스입니다.
* [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service)형식의 연결된 서비스
* [FileShare](#fileshare-dataset-type-properties) 형식의 입력 [데이터 집합](data-factory-create-datasets.md)
* [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties) 형식의 출력 [데이터 집합](data-factory-create-datasets.md)
* [FileSystemSource](#ftp-copy-activity-type-properties) 및 [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties)를 사용하는 복사 작업의 [파이프라인](data-factory-create-pipelines.md)입니다.

샘플은 1시간마다 FTP 서버의 데이터를 Azure Blob으로 복사합니다. 이 샘플에 사용된 JSON 속성은 샘플 다음에 나오는 섹션에서 설명합니다.

**FTP 연결 서비스** 이 예제에서는 일반 텍스트의 사용자 이름과 암호를 기본 인증으로 사용합니다. 다음 방법 중 하나를 사용할 수도 있습니다.

* 익명 인증 
* 암호화된 자격 증명으로 기본 인증 
* SSL/TLS 경우 FTP(FTPS)

사용할 수 있는 다른 유형의 인증은 [FTP 연결 서비스](#ftp-linked-service-properties) 섹션을 참조하세요.

```JSON
{
    "name": "FTPLinkedService",
    "properties": {
    "type": "FtpServer",
    "typeProperties": {
        "host": "myftpserver.com",           
        "authenticationType": "Basic",
        "username": "Admin",
        "password": "123456"
    }
  }
}
```
**Azure 저장소 연결된 서비스**

```JSON
{
  "name": "AzureStorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
**FTP 입력 데이터 집합** 이 데이터 집합은 FTP 폴더 `mysharedfolder`와 `test.csv` 파일을 참조합니다. 파이프라인은 파일을 대상에 복사합니다.

"external": "true" 설정은 데이터 집합이 Data Factory의 외부에 있으며 Data Factory의 활동에 의해 생성되지 않는다는 사실을 Data Factory 서비스에 전달합니다.

```JSON
{
  "name": "FTPFileInput",
  "properties": {
    "type": "FileShare",
    "linkedServiceName": "FTPLinkedService",
    "typeProperties": {
      "folderPath": "mysharedfolder",
      "fileName": "test.csv",
      "useBinaryTransfer": true
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```

**Azure Blob 출력 데이터 집합**

데이터는 매시간 새 blob에 기록됩니다(frequency: hour, interval: 1). Blob에 대한 폴더 경로는 처리 중인 조각의 시작 시간에 기반하여 동적으로 평가됩니다. 폴더 경로는 시작 시간에서 연도, 월, 일 및 시간 부분을 사용합니다.

```JSON
{
    "name": "AzureBlobOutput",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/ftp/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
            "format": {
                "type": "TextFormat",
                "rowDelimiter": "\n",
                "columnDelimiter": "\t"
            },
            "partitionedBy": [
                {
                    "name": "Year",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "yyyy"
                    }
                },
                {
                    "name": "Month",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "MM"
                    }
                },
                {
                    "name": "Day",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "dd"
                    }
                },
                {
                    "name": "Hour",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "HH"
                    }
                }
            ]
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```


**복사 작업을 포함하는 파이프라인**

파이프라인은 입력 및 출력 데이터 집합을 사용하도록 구성된 복사 작업을 포함하고 매시간 실행하도록 예약됩니다. 파이프라인 JSON 정의에서 **source** 형식은 **FileSystemSource**로 설정되고 **sink** 형식은 **BlobSink**로 설정됩니다.

```JSON
{
    "name": "pipeline",
    "properties": {
        "activities": [{
            "name": "FTPToBlobCopy",
            "inputs": [{
                "name": "FtpFileInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "FileSystemSource"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "NewestFirst",
                "retry": 1,
                "timeout": "00:05:00"
            }
        }],
        "start": "2016-08-24T18:00:00Z",
        "end": "2016-08-24T19:00:00Z"
    }
}
```

## <a name="ftp-linked-service-properties"></a>FTp 연결 서비스 속성
다음 테이블은 FTP 연결 서비스에 특정한 JSON 요소에 대한 설명을 제공합니다.

| 속성 | 설명 | 필수 | 기본값 |
| --- | --- | --- | --- |
| type |형식 속성은 FtpServer로 설정해야 합니다. |예 |&nbsp; |
| host |FTP 서버의 이름 또는 IP 주소 |예 |&nbsp; |
| authenticationType |인증 유형 지정 |예 |기본, 익명 |
| username |FTP 서버에 액세스하는 사용자 |아니요 |&nbsp; |
| password |사용자(사용자 이름) 암호 |아니요 |&nbsp; |
| encryptedCredential |FTP 서버 액세스를 위한 암호화된 자격 증명 |아니요 |&nbsp; |
| gatewayName |온-프레미스 FTP 서버에 연결하기 위한 데이터 관리 게이트웨이 이름 |아니요 |&nbsp; |
| 포트 |FTP 서버가 수신 대기하는 포트 |아니요 |21 |
| enableSsl |SSL/TLS 채널을 통해 FTP를 사용할지 여부 |아니요 |true |
| enableServerCertificateValidation |SSL/TLS 채널을 통해 FTP를 사용할 때 서버 SSL 인증서 유효성 검사를 사용할지 지정 |아니요 |true |

### <a name="using-anonymous-authentication"></a>익명 인증 사용

```JSON
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
        "typeProperties": {        
            "authenticationType": "Anonymous",
              "host": "myftpserver.com"
        }
    }
}
```

### <a name="using-username-and-password-in-plain-text-for-basic-authentication"></a>일반 텍스트에 사용자 이름 및 암호를 기본 인증에 사용

```JSON
{
    "name": "FTPLinkedService",
      "properties": {
    "type": "FtpServer",
        "typeProperties": {
            "host": "myftpserver.com",
            "authenticationType": "Basic",
            "username": "Admin",
            "password": "123456"
        }
      }
}
```

### <a name="using-port-enablessl-enableservercertificatevalidation"></a>포트, enableSsl, enableServerCertificateValidation 사용

```JSON
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
        "typeProperties": {
            "host": "myftpserver.com",
            "authenticationType": "Basic",    
            "username": "Admin",
            "password": "123456",
            "port": "21",
            "enableSsl": true,
            "enableServerCertificateValidation": true
        }
    }
}
```

### <a name="using-encryptedcredential-for-authentication-and-gateway"></a>인증 및 게이트웨이에 encryptedCredential 사용

```JSON
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
        "typeProperties": {
            "host": "myftpserver.com",
            "authenticationType": "Basic",
            "encryptedCredential": "xxxxxxxxxxxxxxxxx",
            "gatewayName": "mygateway"
        }
      }
}
```

온-프레미스 FTP 데이터 원본의 자격 증명을 설정하는 방법에 대한 자세한 내용은 [데이터 관리 게이트웨이를 사용하여 온-프레미스 원본과 클라우드 간 데이터 이동](data-factory-move-data-between-onprem-and-cloud.md)을 참조하세요.

[!INCLUDE [data-factory-file-share-dataset](../../includes/data-factory-file-share-dataset.md)]

[!INCLUDE [data-factory-file-format](../../includes/data-factory-file-format.md)]

[!INCLUDE [data-factory-compression](../../includes/data-factory-compression.md)]

## <a name="ftp-copy-activity-type-properties"></a>FTP 복사 활동 형식 속성
활동 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인 만들기](data-factory-create-pipelines.md) 문서를 참조하세요. 이름, 설명, 입력/출력 테이블, 정책 등의 속성은 모든 형식의 활동에 사용할 수 있습니다.

반면 활동의 typeProperties 섹션에서 사용할 수 있는 속성은 각 활동 형식에 따라 다릅니다. 복사 활동의 경우 유형은 소스 및 싱크의 형식에 따라 달라집니다.

[!INCLUDE [data-factory-file-system-source](../../includes/data-factory-file-system-source.md)]

[!INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

[!INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

## <a name="performance-and-tuning"></a>성능 및 튜닝
Azure Data Factory의 데이터 이동(복사 작업) 성능에 영향을 주는 주요 요소 및 최적화하는 다양한 방법에 대해 알아보려면 [복사 작업 성능 및 조정 가이드](data-factory-copy-activity-performance.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계
다음 문서를 참조하세요.

* [복사 작업 자습서](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) .



<!--HONumber=Nov16_HO3-->


