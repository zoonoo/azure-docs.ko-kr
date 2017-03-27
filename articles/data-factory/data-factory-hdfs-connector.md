---
title: "온-프레미스 HDFS에서 데이터 이동 | Microsoft Docs"
description: "Azure 데이터 팩터리를 사용하여 온-프레미스 HDFS에서 데이터를 이동하는 방법에 대해 알아봅니다."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.assetid: 3215b82d-291a-46db-8478-eac1a3219614
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/13/2017
ms.author: jingwang
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: 8a6050fc52407ab6b974a9698d970248062665c1
ms.lasthandoff: 03/15/2017


---
# <a name="move-data-from-on-premises-hdfs-using-azure-data-factory"></a>Azure 데이터 팩터리를 사용하여 온-프레미스 HDFS에서 데이터 이동
이 문서에서는 Azure 데이터 팩터리에서 복사 작업을 사용하여 온-프레미스 HDFS에서 다른 데이터 저장소로 데이터를 이동하는 방법에 대해 간략하게 설명합니다. 이 문서는 복사 작업 및 지원되는 데이터 저장소 조합을 사용하여 데이터 이동의 일반적인 개요를 보여주는 [데이터 이동 활동](data-factory-data-movement-activities.md) 문서를 작성합니다.

현재 데이터 팩터리는 다른 데이터 저장소에서 온-프레미스 HDFS로 데이터 이동이 아닌 온-프레미스 HDFS에서 다른 데이터 저장소로 데이터 이동만을 지원합니다.

## <a name="enabling-connectivity"></a>연결 사용
데이터 팩터리 서비스는 데이터 관리 게이트웨이를 사용하여 온-프레미스 HDFS에 연결을 지원합니다. 데이터 관리 게이트웨이 및 게이트웨이 설정에 대한 단계별 지침을 알아보려면 [온-프레미스 위치 및 클라우드 간 데이터 이동](data-factory-move-data-between-onprem-and-cloud.md) 문서를 참조하세요. Azure IaaS VM에서 호스팅되는 경우 HDFS에 연결하려면 게이트웨이를 사용합니다.

> [!NOTE]
> 데이터 관리 게이트웨이에서 Hadoop 클러스터의 **모든** [이름 노드 서버]:[이름 노드 포트] 및 [데이터 노드 서버]:[데이터 노드 포트]에 액세스할 수 있는지 확인합니다. 기본 [이름 노드 포트]는 50070이며 기본 [데이터 노드 포트]는 50075입니다.
>

동일한 온-프레미스 컴퓨터 또는 HDFS로 Azure VM에 게이트웨이 설치할 수 있지만 별도 컴퓨터/Azure IaaS VM에 게이트웨이를 설치하는 것이 좋습니다. 별도의 컴퓨터에 게이트웨이가 있으면 리소스 경합이 줄어들고 성능이 향상됩니다. 별도 컴퓨터에 게이트웨이를 설치하는 경우 컴퓨터는 HDFS를 사용하여 컴퓨터에 액세스할 수 있어야 합니다.

## <a name="copy-data-wizard"></a>데이터 복사 마법사
온-프레미스 HDFS에서 데이터를 복사하는 파이프라인을 만드는 가장 쉬운 방법은 데이터 복사 마법사를 사용하는 것입니다. 데이터 복사 마법사를 사용하여 파이프라인을 만드는 방법에 대한 빠른 연습은 [자습서: 복사 마법사를 사용하여 파이프라인 만들기](data-factory-copy-data-wizard-tutorial.md) 를 참조하세요.

다음 예에서는 [Azure 포털](data-factory-copy-activity-tutorial-using-azure-portal.md), [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) 또는 [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)을 사용하여 파이프라인을 만드는 데 사용할 수 있는 샘플 JSON 정의를 제공합니다. 이 샘플은 온-프레미스 HDFS에서 Azure Blob 저장소로 데이터를 복사하는 방법을 보여 줍니다. 그러나 Azure Data Factory의 복사 작업을 사용하여 [여기](data-factory-data-movement-activities.md#supported-data-stores-and-formats)에 설명한 싱크로 데이터를 복사할 수 있습니다.

## <a name="sample-copy-data-from-on-premises-hdfs-to-azure-blob"></a>샘플: 온-프레미스 HDFS에서 Azure Blob으로 데이터 복사
이 샘플은 온-프레미스 HDFS에서 Azure Blob 저장소로 데이터를 복사하는 방법을 보여 줍니다. 그러나 Azure Data Factory의 복사 작업을 사용하여 **여기**에 설명한 싱크로 [직접](data-factory-data-movement-activities.md#supported-data-stores-and-formats) 데이터를 복사할 수 있습니다.  

이 샘플에는 다음 데이터 팩터리 엔터티가 있습니다.

1. [OnPremisesHdfs](#hdfs-linked-service-properties) 형식의 연결된 서비스입니다.
2. [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service) 형식의 연결된 서비스
3. [FileShare](#hdfs-dataset-type-properties) 형식의 입력 [데이터 집합](data-factory-create-datasets.md)
4. [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties) 형식의 출력 [데이터 집합](data-factory-create-datasets.md)
5. [FileSystemSource](#hdfs-copy-activity-type-properties) 및 [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties)를 사용하는 복사 작업의 [파이프라인](data-factory-create-pipelines.md)입니다.

샘플은 온-프레미스 HDFS에서 Azure Blob으로 매시간 데이터를 복사합니다. 이 샘플에 사용된 JSON 속성은 샘플 다음에 나오는 섹션에서 설명합니다.

첫 번째 단계로 데이터 관리 게이트웨이를 설정합니다. 해당 지침은 [온-프레미스 위치와 클라우드 간에 데이터 이동](data-factory-move-data-between-onprem-and-cloud.md) 문서에 나와 있습니다.

**HDFS 연결된 서비스** 이 예제에서는 Windows 인증을 사용합니다. 사용할 수 있는 다른 유형의 인증은 [HDFS 연결된 서비스](#hdfs-linked-service-properties) 섹션을 참조하세요.

```JSON
{
    "name": "HDFSLinkedService",
    "properties":
    {
        "type": "Hdfs",
        "typeProperties":
        {
            "authenticationType": "Windows",
            "userName": "Administrator",
            "password": "password",
            "url" : "http://<machine>:50070/webhdfs/v1/",
            "gatewayName": "mygateway"
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

**HDFS 입력 데이터 집합** 이 데이터 집합은 HDFS 폴더 DataTransfer/UnitTest/를 가리킵니다. 파이프라인은 이 폴더의 모든 파일을 대상에 복사합니다.

"external": "true"로 설정하면 데이터 집합이 Data Factory의 외부에 있으며 Data Factory의 활동에 의해 생성되지 않는다는 사실이 Data Factory 서비스에 전달됩니다.

```JSON
{
    "name": "InputDataset",
    "properties": {
        "type": "FileShare",
        "linkedServiceName": "HDFSLinkedService",
        "typeProperties": {
            "folderPath": "DataTransfer/UnitTest/"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval":  1
        }
    }
}
```

**Azure Blob 출력 데이터 집합**

데이터는 매시간 새 blob에 기록됩니다(frequency: hour, interval: 1). Blob에 대한 폴더 경로는 처리 중인 조각의 시작 시간에 기반하여 동적으로 평가됩니다. 폴더 경로는 시작 시간에서 연도, 월, 일 및 시간 부분을 사용합니다.

```JSON
{
    "name": "OutputDataset",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/hdfs/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

파이프라인은 이러한 입력 및 출력 데이터 집합을 사용하도록 구성되며 매시간 실행되도록 예약되는 복사 활동을 포함합니다. 파이프라인 JSON 정의에서 **source** 형식은 **FileSystemSource**로 설정되고 **sink** 형식은 **BlobSink**로 설정됩니다. **query** 속성에 지정된 SQL 쿼리는 과거 한 시간에서 복사할 데이터를 선택합니다.

```JSON
{
    "name": "pipeline",
    "properties":
    {
        "activities":
        [
            {
                "name": "HdfsToBlobCopy",
                "inputs": [ {"name": "InputDataset"} ],
                "outputs": [ {"name": "OutputDataset"} ],
                "type": "Copy",
                "typeProperties":
                {
                    "source":
                    {
                        "type": "FileSystemSource"
                    },
                    "sink":
                    {
                        "type": "BlobSink"
                    }
                },
                "policy":
                {
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1,
                    "timeout": "00:05:00"
                }
            }
        ],
        "start": "2014-06-01T18:00:00Z",
        "end": "2014-06-01T19:00:00Z"
    }
}
```

## <a name="hdfs-linked-service-properties"></a>HDFS 연결된 서비스 속성
다음 표는 HDFS 연결된 서비스에 특정된 JSON 요소에 대한 설명을 제공합니다.

| 속성 | 설명 | 필수 |
| --- | --- | --- |
| type |형식 속성은 다음으로 설정해야 함: **Hdfs** |예 |
| Url |HDFS에 대한 URL |예 |
| authenticationType |익명 또는 Windows입니다. <br><br> HDFS 커넥터에 **Kerberos 인증**을 사용하려면 [이 섹션](#use-kerberos-authentication-for-hdfs-connector)을 참조하여 온-프레미스 환경을 적절히 설정합니다. |예 |
| userName |Windows 인증에 대한 사용자 이름. |예(Windows 인증에 대한) |
| password |Windows 인증에 대한 암호. |예(Windows 인증에 대한) |
| gatewayName |데이터 팩터리 서비스가 HDFS에 연결하는 데 사용해야 하는 게이트웨이의 이름. |예 |
| encryptedCredential |[New-AzureRMDataFactoryEncryptValue](https://msdn.microsoft.com/library/mt603802.aspx) 출력. |아니요 |

온-프레미스 HDFS의 자격 증명을 설정하는 방법에 대한 자세한 내용은 [데이터 관리 게이트웨이를 사용하여 온-프레미스 원본과 클라우드 간 데이터 이동](data-factory-move-data-between-onprem-and-cloud.md)을 참조하세요.

### <a name="using-anonymous-authentication"></a>익명 인증 사용

```JSON
{
    "name": "hdfs",
    "properties":
    {
        "type": "Hdfs",
        "typeProperties":
        {
            "authenticationType": "Anonymous",
            "userName": "hadoop",
            "url" : "http://<machine>:50070/webhdfs/v1/",
            "gatewayName": "mygateway"
        }
    }
}
```

### <a name="using-windows-authentication"></a>Windows 인증 사용

```JSON
{
    "name": "hdfs",
    "properties":
    {
        "type": "Hdfs",
        "typeProperties":
        {
            "authenticationType": "Windows",
            "userName": "Administrator",
            "password": "password",
            "url" : "http://<machine>:50070/webhdfs/v1/",
            "gatewayName": "mygateway"
        }
    }
}
```

## <a name="use-kerberos-authentication-for-hdfs-connector"></a>HDFS 커넥터에 Kerberos 인증 사용
HDFS 커넥터에 Kerberos 인증을 사용하도록 온-프레미스 환경을 설정하는 옵션은 두 가지가 있습니다. 이 중 더 잘 맞는 옵션을 선택할 수 있습니다.
* 옵션 1: [게이트웨이 컴퓨터가 Kerberos 영역에 가입하도록 함](#kerberos-join-realm)
* 옵션 2: [Windows 도메인과 Kerberos 영역 사이에 상호 트러스트를 사용하도록 설정](#kerberos-mutual-trust)

### <a name="kerberos-join-realm"></a>옵션 1: 게이트웨이 컴퓨터가 Kerberos 영역에 가입하도록 함

#### <a name="requirement"></a>요구 사항:

* 게이트웨이 컴퓨터가 Kerberos 영역에 가입해야 하고 Windows 도메인에는 가입할 수 없습니다.

#### <a name="how-to-configure"></a>구성 방법:

**게이트웨이 컴퓨터에서:**

1.    **Ksetup** 유틸리티를 실행하여 Kerberos KDC 서버 및 영역을 구성합니다.

    Kerberos 영역은 Windows 도메인과 다르기 때문에 컴퓨터를 작업 그룹의 구성원으로 구성해야 합니다. 그러려면 다음과 같이 Kerberos 영역을 설정하고 KDC 서버를 추가합니다. *REALM.COM*은 고유한 각 영역으로 필요에 맞게 바꿉니다.

            C:> Ksetup /setdomain REALM.COM
            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>

    이러한 2개 명령을 실행한 후 컴퓨터를 **다시 시작**합니다.

2.    **Ksetup** 명령을 사용하여 구성을 확인합니다. 아래와 같이 출력되어야 합니다.

            C:> Ksetup
            default realm = REALM.COM (external)
            REALM.com:
                kdc = <your_kdc_server_address>

**Azure Data Factory에서:**

* HDFS 데이터 원본에 연결하는 Kerberos 주체 이름 및 암호와 **Windows 인증**을 함께 사용하여 HDFS 커넥터를 구성합니다. 구성 세부 정보에서 [HDFS 연결된 서비스 속성](#hdfs-linked-service-properties)을 확인합니다.

### <a name="kerberos-mutual-trust"></a>옵션 2: Windows 도메인과 Kerberos 영역 사이에 상호 트러스트를 사용하도록 설정

#### <a name="requirement"></a>요구 사항:
*    게이트웨이 컴퓨터가 Windows 도메인에 가입해야 합니다.
*    도메인 컨트롤러의 설정을 업데이트하는 권한이 있어야 합니다.

#### <a name="how-to-configure"></a>구성 방법:

> [!NOTE]
> 아래 자습서에서 REALM.COM 및 AD.COM을 고유한 각 영역과 도메인 컨트롤러로 필요에 맞게 바꿉니다.

**KDC 서버에서:**

1.    아래의 구성 템플릿을 참조하여 **krb5.conf** 파일에서 KDC가 Windows 도메인을 신뢰하도록 KDC 구성을 편집합니다. 기본적으로 이 구성은 **/etc/krb5.conf**에 있습니다.

            [logging]
             default = FILE:/var/log/krb5libs.log
             kdc = FILE:/var/log/krb5kdc.log
             admin_server = FILE:/var/log/kadmind.log

            [libdefaults]
             default_realm = REALM.COM
             dns_lookup_realm = false
             dns_lookup_kdc = false
             ticket_lifetime = 24h
             renew_lifetime = 7d
             forwardable = true

            [realms]
             REALM.COM = {
              kdc = node.REALM.COM
              admin_server = node.REALM.COM
             }
            AD.COM = {
             kdc = windc.ad.com
             admin_server = windc.ad.com
            }

            [domain_realm]
             .REALM.COM = REALM.COM
             REALM.COM = REALM.COM
             .ad.com = AD.COM
             ad.com = AD.COM

            [capaths]
             AD.COM = {
              REALM.COM = .
             }

        구성 후 KDC 서비스를 **다시 시작**합니다.

2.    다음 명령을 사용하여 KDC 서버의 **krbtgt/REALM.COM@AD.COM**이라는 주체를 준비합니다.

            Kadmin> addprinc krbtgt/REALM.COM@AD.COM

3.    **hadoop.security.auth_to_local** HDFS 서비스 구성 파일에서 `RULE:[1:$1@$0](.*@AD.COM)s/@.*//`를 추가합니다.

**도메인 컨트롤러에서:**

1.    아래의 **Ksetup** 명령을 실행하여 영역 항목을 추가합니다.

            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
            C:> ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM

2.    Windows 도메인에서 Kerberos 영역으로의 트러스트를 설정합니다. [password]는 **krbtgt/REALM.COM@AD.COM** 주체의 암호입니다.

            C:> netdom trust REALM.COM /Domain: AD.COM /add /realm /passwordt:[password]

3.    Kerberos에서 사용되는 암호화 알고리즘을 선택합니다.

    1. 서버 관리자 > 그룹 정책 관리 > 도메인 > 그룹 정책 개체 > 기본 또는 활성 도메인 정책으로 이동하고 편집을 클릭합니다.

    2. **그룹 정책 관리 편집기** 팝업 창에서 컴퓨터 구성 > 정책 > Windows 설정 > 보안 설정 > 로컬 정책 > 보안 옵션으로 이동하고 **네트워크 보안: Kerberos에 허용된 암호화 유형 구성**을 구성합니다.

    3. KDC에 연결할 때 사용할 암호화 알고리즘을 선택합니다. 일반적으로 간단히 모든 옵션을 선택할 수 있습니다.

        ![Kerberos의 암호화 유형 구성](media/data-factory-hdfs-connector/config-encryption-types-for-kerberos.png)

    4. **Ksetup** 명령을 사용하여 특정 영역에서 사용할 암호화 알고리즘을 지정합니다.

                C:> ksetup /SetEncTypeAttr REALM.COM DES-CBC-CRC DES-CBC-MD5 RC4-HMAC-MD5 AES128-CTS-HMAC-SHA1-96 AES256-CTS-HMAC-SHA1-96

4.    Windows 도메인에서 Kerberos 주체를 사용하려면 도메인 계정 및 Kerberos 주체 간의 매핑을 만듭니다.

    1. 관리 도구 > **Active Directory 사용자 및 컴퓨터**를 시작합니다.

    2. **보기** > **고급 기능**을 클릭하여 고급 기능을 구성합니다.

    3. 매핑을 만들려는 계정을 찾고 마우스 오른쪽 단추를 클릭하여 **이름 매핑**을 본 후 **Kerberos 이름** 탭을 클릭합니다.

    4. 영역의 주체를 추가합니다.

        ![보안 ID 매핑](media/data-factory-hdfs-connector/map-security-identity.png)

**게이트웨이 컴퓨터에서:**

* 아래의 **Ksetup** 명령을 실행하여 영역 항목을 추가합니다.

            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
            C:> ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM

**Azure Data Factory에서:**

* HDFS 데이터 원본에 연결하는 도메인 계정 또는 Kerberos 주체와 **Windows 인증**을 함께 사용하여 HDFS 커넥터를 구성합니다. 구성 세부 정보에서 [HDFS 연결된 서비스 속성](#hdfs-linked-service-properties)을 확인합니다.


## <a name="hdfs-dataset-type-properties"></a>HDFS 데이터 집합 형식 속성
데이터 집합 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [데이터 집합 만들기](data-factory-create-datasets.md) 문서를 참조하세요. 구조, 가용성 및 JSON 데이터 집합의 정책과 같은 섹션이 모든 데이터 집합 형식에 대해 유사합니다(Azure SQL, Azure blob, Azure 테이블 등).

**typeProperties** 섹션은 데이터 집합의 각 형식에 따라 다르며 데이터 저장소에 있는 데이터의 위치에 대한 정보를 제공합니다. **FileShare** (HDFS 데이터 집합 포함) 데이터 집합 형식의 데이터 집합에 대한 typeProperties 섹션에는 다음 속성이 있습니다.

| 속성 | 설명 | 필수 |
| --- | --- | --- |
| folderPath |파일의 경로입니다. 예: `myfolder`<br/><br/>문자열의 특수 문자에 이스케이프 문자 '\'를 사용합니다. 예: 폴더\하위 폴더의 경우 폴더\\\\하위 폴더를 지정하고 d:\samplefolder의 경우 d:\\\\samplefolder를 지정합니다.<br/><br/>이 속성을 **partitionBy**와 결합하여 조각 시작/종료 날짜/시간을 기준으로 폴더 경로를 지정할 수 있습니다. |예 |
| fileName |폴더에서 특정 파일을 참조하기 위해 테이블을 사용하려는 경우 **folderPath** 에 있는 파일의 이름을 지정합니다. 이 속성에 값을 지정하지 않으면 테이블은 폴더에 있는 모든 파일을 가리킵니다.<br/><br/>출력 데이터 집합에 대한 fileName이 지정되는 경우 생성되는 파일의 이름 형식은 다음과 같습니다. <br/><br/>Data<Guid>.txt(예: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt) |아니요 |
| partitionedBy |동적 folderPath, 시계열 데이터에 대한 filename을 지정하는 데 partitionedBy를 사용할 수 있습니다. 예를 들어 folderPath는 매시간 데이터에 대해 매개 변수화됩니다. |아니요 |
| format | **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**과 같은 서식 유형이 지원됩니다. 이 값 중 하나로 서식에서 **type** 속성을 설정합니다. 자세한 내용은 [텍스트 형식](#specifying-textformat), [Json 형식](#specifying-jsonformat), [Avro 형식](#specifying-avroformat), [Orc 형식](#specifying-orcformat) 및 [Parquet 형식](#specifying-parquetformat) 섹션을 참조하세요. <br><br> 파일 기반 저장소(이진 복사) 간에 **파일을 있는 그대로 복사**하려는 경우 입력 및 출력 데이터 집합 정의 둘 다에서 형식 섹션을 건너뜁니다. |아니요 |
| 압축 | 데이터에 대한 압축 유형 및 수준을 지정합니다. 지원되는 형식은 **GZip**, **Deflate**, **BZip2** 및 **ZipDeflate**이고 지원되는 수준은 **최적** 및 **가장 빠름**입니다. 자세한 내용은 [압축 지정](#specifying-compression) 섹션을 참조하세요. |아니요 |

> [!NOTE]
> filename 및 fileFilter는 동시에 사용할 수 없습니다.
>
>

### <a name="using-partionedby-property"></a>partionedBy 속성 사용
이전 섹션에서 설명했듯이 동적 folderPath, partitionedBy된 시계열 데이터에 대한 filename을 지정할 수 있습니다. 지정된 데이터 조각에 대한 논리적 기간을 나타내는 데이터 팩터리 매크로 및 시스템 변수 SliceStart, SliceEnd를 사용하여 이 작업을 수행할 수 있습니다.

시계열 데이터 집합, 예약 및 조각에 대한 자세한 내용은 [데이터 집합 만들기](data-factory-create-datasets.md), [일정 예약 및 실행](data-factory-scheduling-and-execution.md) 및 [파이프라인 만들기](data-factory-create-pipelines.md) 문서를 참조하세요.

#### <a name="sample-1"></a>샘플 1:

```JSON
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```
이 예제에서 {Slice}는 지정된 형식(YYYYMMDDHH)의 Data Factory 시스템 변수 SliceStart 값으로 대체됩니다. SliceStart는 조각의 시작 시간을 가리킵니다. folderPath는 각 조각에 따라 다릅니다. 예를 들어 wikidatagateway/wikisampledataout/2014100103 또는 wikidatagateway/wikisampledataout/2014100104입니다.

#### <a name="sample-2"></a>샘플 2:

```JSON
"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
"fileName": "{Hour}.csv",
"partitionedBy":
 [
    { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
    { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
    { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
    { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } }
],
```
이 예제에서 SliceStart의 연도, 월, 일 및 시간은 folderPath 및 fileName 속성에서 사용하는 별도 변수로 추출됩니다.

[!INCLUDE [data-factory-file-format](../../includes/data-factory-file-format.md)]

[!INCLUDE [data-factory-compression](../../includes/data-factory-compression.md)]

## <a name="hdfs-copy-activity-type-properties"></a>HDFS 복사 활동 형식 속성
활동 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인 만들기](data-factory-create-pipelines.md) 문서를 참조하세요. 이름, 설명, 입력/출력 테이블, 정책 등의 속성은 모든 형식의 활동에 사용할 수 있습니다.

반면 활동의 typeProperties 섹션에서 사용할 수 있는 속성은 각 활동 형식에 따라 다릅니다. 복사 활동의 경우 이러한 속성은 소스 및 싱크의 형식에 따라 달라집니다.

원본이 **FileSystemSource** 형식인 복사 작업의 경우 typeProperties 섹션에서 다음과 같은 속성을 사용할 수 있습니다.

**FileSystemSource**는 다음 속성을 지원합니다.

| 속성 | 설명 | 허용되는 값 | 필수 |
| --- | --- | --- | --- |
| recursive |하위 폴더에서 또는 지정된 폴더에서만 데이터를 재귀적으로 읽을지 여부를 나타냅니다. |True, False(기본값) |아니요 |

[!INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

[!INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

## <a name="performance-and-tuning"></a>성능 및 튜닝
Azure Data Factory의 데이터 이동(복사 작업) 성능에 영향을 주는 주요 요소 및 최적화하는 다양한 방법에 대해 알아보려면 [복사 작업 성능 및 조정 가이드](data-factory-copy-activity-performance.md)를 참조하세요.

