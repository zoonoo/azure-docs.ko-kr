---
title: 데이터를 마이그레이션할 Azure Data Box를 사용 하 여 온-프레미스 HDFS를 Azure Storage에 저장
description: 온-프레미스 HDFS 저장소에서 데이터를 Azure Storage로 마이그레이션
services: storage
author: normesta
ms.service: storage
ms.date: 06/11/2019
ms.author: normesta
ms.topic: article
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: 4445a8566c04d30cfb8743cbd33623f2e23f0dde
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67595391"
---
# <a name="use-azure-data-box-to-migrate-data-from-an-on-premises-hdfs-store-to-azure-storage"></a>Azure Data Box를 사용 하 여 Azure Storage에 온-프레미스 HDFS 저장소에서 데이터를 마이그레이션하려면

Data Box 장치를 사용 하 여 Hadoop 클러스터를 Azure Storage (blob storage 또는 Data Lake 저장소 Gen2)로 온-프레미스 HDFS 저장소에서 데이터를 마이그레이션할 수 있습니다. 80 TB 데이터 상자나 770 TB 데이터 상자 무에서 선택할 수 있습니다.

이 문서에서는 이러한 작업을 완료할 수 있습니다.

> [!div class="checklist"]
> * 데이터를 마이그레이션하기 위해 준비 합니다.
> * Data Box 또는 상자에 과도 한 데이터를 장치에 데이터를 복사 합니다.
> * Microsoft로 장치를 제공 합니다.
> * Data Lake 저장소 Gen2 데이터를 이동 합니다.

## <a name="prerequisites"></a>필수 구성 요소

마이그레이션을 완료 하려면 이러한 항목이 필요 합니다.

* 두 개의 저장소 계정을; 계층적 네임 스페이스를 사용 하는 하는 하지 않습니다.

* 원본 데이터를 포함 하는 온-프레미스 Hadoop 클러스터입니다.

* [Azure Data Box 장치](https://azure.microsoft.com/services/storage/databox/)합니다.

  * [프로그램 Data Box 주문](https://docs.microsoft.com/azure/databox/data-box-deploy-ordered) 나 [Data Box 많은](https://docs.microsoft.com/azure/databox/data-box-heavy-deploy-ordered)합니다. 장치의 순서 지정 하는 동안 선택 해야 하는 저장소 계정 **하지** 계층적 네임 스페이스를 사용 하도록 설정 했습니다. 즉, Data Box 장치에 Azure Data Lake 저장소 Gen2 직접 수집을 아직 지원 하지 않습니다. Storage 계정으로 복사 하 고 다음 Gen2 ADLS 계정에 두 번째 복사본을 수행 해야 합니다. 이 대 한 지침은 아래 단계에 제공 됩니다.

  * 케이블 연결 하 고 연결 프로그램 [Data Box](https://docs.microsoft.com/azure/databox/data-box-deploy-set-up) 또는 [상자에 과도 한 데이터](https://docs.microsoft.com/azure/databox/data-box-heavy-deploy-set-up) 온-프레미스 네트워크에 있습니다.

준비 인 경우 시작 해 보겠습니다.

## <a name="copy-your-data-to-a-data-box-device"></a>Data Box 장치에 데이터 복사

데이터에는 하나의 Data Box 장치에 적합 한 경우 Data Box 장치에 데이터를 복사할 수 있습니다. 

Data Box 장치 용량을 초과 하는 데이터 크기를 사용 하 여 합니다 [여러 Data Box 장치에서 데이터를 분할 하는 선택적 절차](#appendix-split-data-across-multiple-data-box-devices) 다음이 단계를 수행 합니다. 

Data Box 장치에 온-프레미스 HDFS 저장소에서 데이터를 복사, 몇 가지 항목을 설정 및 사용 하 여 수를 [DistCp](https://hadoop.apache.org/docs/stable/hadoop-distcp/DistCp.html) 도구입니다.

Data Box 장치에 REST Api의 Blob/개체 저장소를 통해 데이터를 복사 하려면 다음이 단계를 수행 합니다. REST API 인터페이스를 표시 하 여 클러스터에 HDFS 저장소로 장치를 확인 합니다.

1. REST 통해 데이터를 복사 하기 전에 Data Box 또는 상자에 과도 한 데이터는 REST 인터페이스에 연결할 기본 보안 및 연결 형식을 식별 합니다. 로컬 웹 UI의 Data Box에 로그인 하 고 이동할 **연결 및 복사** 페이지입니다. Azure storage에 대 한 계정에 장치에 대 한 아래 **설정에 액세스**를 찾아 선택 **REST**합니다.

    !["연결 및 복사" 페이지](media/data-lake-storage-migrate-on-premises-HDFS-cluster/data-box-connect-rest.png)

2. 액세스 저장소 계정에 업로드 데이터 대화 상자에서 복사 하 고는 **Blob service 끝점** 하며 **저장소 계정 키**합니다. Blob service 끝점에서 생략 된 `https://` 및 후행 슬래시입니다.

    이 경우에 끝점은: `https://mystorageaccount.blob.mydataboxno.microsoftdatabox.com/`합니다. 사용 하는 URI의 호스트 부분은: `mystorageaccount.blob.mydataboxno.microsoftdatabox.com`합니다. 예를 들어 참조 하는 방법 [http 통한 REST에 연결](/azure/databox/data-box-deploy-copy-data-via-rest)합니다. 

     !["저장소 계정에 액세스 및 데이터 업로드" 대화 상자](media/data-lake-storage-migrate-on-premises-HDFS-cluster/data-box-connection-string-http.png)

3. 끝점 및 Data Box 또는 상자에 과도 한 데이터 노드 IP 주소를 추가 `/etc/hosts` 각 노드에서 합니다.

    ```    
    10.128.5.42  mystorageaccount.blob.mydataboxno.microsoftdatabox.com
    ```

    DNS에 대 한 일부 다른 메커니즘을 사용 해야 하는 Data Box 끝점을 해결할 수 있습니다.

4. 셸 변수를 설정 `azjars` 의 위치를 `hadoop-azure` 및 `azure-storage` jar 파일입니다. Hadoop 설치 디렉터리에서 이러한 파일을 찾을 수 있습니다.

    이러한 파일이 존재 하는 경우를 확인 하려면 다음 명령을 사용 하 여: `ls -l $<hadoop_install_dir>/share/hadoop/tools/lib/ | grep azure`합니다. 대체는 `<hadoop_install_dir>` 자리 표시자를 Hadoop을 설치한 디렉터리의 경로입니다. 정규화 된 경로 사용 해야 합니다.

    예를 들면 다음과 같습니다.

    `azjars=$hadoop_install_dir/share/hadoop/tools/lib/hadoop-azure-2.6.0-cdh5.14.0.jar` `azjars=$azjars,$hadoop_install_dir/share/hadoop/tools/lib/microsoft-windowsazure-storage-sdk-0.6.0.jar`

5. 데이터 복사에 사용 하려는 저장소 컨테이너를 만듭니다. 이 명령의 일부로 대상 디렉터리도 지정 해야 합니다. 이 시점에서 더미 대상 디렉터리를 수 있습니다.

    ```
    hadoop fs -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.<blob_service_endpoint>=<account_key> \
    -mkdir -p  wasb://<container_name>@<blob_service_endpoint>/<destination_directory>
    ```

    * 대체는 `<blob_service_endpoint>` 자리 표시자를 blob service 끝점의 이름입니다.

    * 대체는 `<account_key>` 계정의 액세스 키를 사용 하 여 자리 표시자입니다.

    * 대체는 `<container-name>` 자리 표시자를 컨테이너의 이름입니다.

    * 대체는 `<destination_directory>` 자리 표시자 데이터를 복사 하려는 디렉터리의 이름입니다.

6. 컨테이너 및 디렉터리에 만들어졌는지 확인 하려면 목록 명령을 실행 합니다.

    ```
    hadoop fs -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.<blob_service_endpoint>=<account_key> \
    -ls -R  wasb://<container_name>@<blob_service_endpoint>/
    ```

   * 대체는 `<blob_service_endpoint>` 자리 표시자를 blob service 끝점의 이름입니다.

   * 대체는 `<account_key>` 계정의 액세스 키를 사용 하 여 자리 표시자입니다.

   * 대체는 `<container-name>` 자리 표시자를 컨테이너의 이름입니다.

7. 이전에 만든 컨테이너에 데이터 상자 Blob storage로 Hadoop HDFS에서 데이터를 복사 합니다. 디렉터리에 복사 하는 항목이 없을 경우 명령이 자동으로 만듭니다.

    ```
    hadoop distcp \
    -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.<blob_service_endpoint<>=<account_key> \
    -filters <exclusion_filelist_file> \
    [-f filelist_file | /<source_directory> \
           wasb://<container_name>@<blob_service_endpoint>/<destination_directory>
    ```

    * 대체는 `<blob_service_endpoint>` 자리 표시자를 blob service 끝점의 이름입니다.

    * 대체는 `<account_key>` 계정의 액세스 키를 사용 하 여 자리 표시자입니다.

    * 대체는 `<container-name>` 자리 표시자를 컨테이너의 이름입니다.

    * 대체는 `<exlusion_filelist_file>` 자리 표시자는 제외 된 파일의 목록이 포함 된 파일의 이름입니다.

    * 대체는 `<source_directory>` 자리 표시자를 복사 하려면 데이터를 포함 하는 디렉터리의 이름입니다.

    * 대체는 `<destination_directory>` 자리 표시자 데이터를 복사 하려는 디렉터리의 이름입니다.

    `-libjars` 되도록 옵션을 사용 합니다 `hadoop-azure*.jar` 및 종속 `azure-storage*.jar` 파일을 사용할 수 `distcp`입니다. 이 일부 클러스터에 대 한 이미 발생할 수 있습니다.

    다음 예제와 방법을 `distcp` 명령은 데이터를 복사 하는 데 사용 됩니다.

    ```
     hadoop distcp \
    -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.mystorageaccount.blob.mydataboxno.microsoftdatabox.com=myaccountkey \
    -filter ./exclusions.lst -f /tmp/copylist1 -m 4 \
    /data/testfiles \
    wasb://hdfscontainer@mystorageaccount.blob.mydataboxno.microsoftdatabox.com/data
    ```
  
    속도를 향상 복사 합니다.

    * 매퍼 수를 변경해 보세요. (위의 예제에서는 `m` 4 매퍼 =.)

    * 여러 실행 `distcp` 병렬로 합니다.

    * 큰 파일을 작은 파일 보다 더 잘 수행 해야 합니다.

## <a name="ship-the-data-box-to-microsoft"></a>Microsoft Data Box를 배송 합니다.

Microsoft Data Box 장치 배송 준비 하려면 다음이 단계를 따릅니다.

1. 먼저 [Data Box 또는 상자에 과도 한 데이터에서 배송 준비](https://docs.microsoft.com/azure/databox/data-box-deploy-copy-data-via-rest)합니다.

2. 장치 준비를 완료 한 후 BOM 파일을 다운로드 합니다. 이러한 BOM을 사용 하거나 나중에 Azure에 데이터 업로드를 확인 하려면 파일 매니페스트.

3. 장치를 종료 하 고 케이블을 제거 합니다.

4. UPS를 사용하여 픽업을 예약합니다.

    * Data Box 장치에 대 한 참조 [여 Data Box 배송](https://docs.microsoft.com/azure/databox/data-box-deploy-picked-up)합니다.

    * 데이터 상자 많은 장치에 대 한 참조 [배송 데이터 상자 많은](https://docs.microsoft.com/azure/databox/data-box-heavy-deploy-picked-up)합니다.

5. Microsoft가 장치를 수신, 데이터 센터 네트워크에 연결 되어 및 데이터 (계층적 네임 스페이스를 사용 하지 않도록 설정)으로 지정한 저장소 계정에 업로드 한 후 장치 순서를 배치한 경우입니다. 모든 데이터를 Azure에 업로드 되는 BOM 파일에 대해 확인 합니다. 이제 Data Lake 저장소 Gen2 저장소 계정에이 데이터를 이동할 수 있습니다.

## <a name="move-the-data-into-azure-data-lake-storage-gen2"></a>Azure Data Lake 저장소 Gen2로 데이터 이동

Azure Storage 계정에는 이미 데이터. 이제 Azure Data Lake 저장소 계정에 데이터를 복사 하 파일 및 디렉터리에 대 한 액세스 권한을 적용 합니다.

> [!NOTE]
> 이 단계는 데이터 저장소로 Azure Data Lake 저장소 Gen2를 사용 하는 경우에 필요 합니다. 방금 계층적 네임 스페이스가 없는 blob 저장소 계정을 데이터 저장소로을 사용 하는 경우에이 섹션을 건너뛸 수 있습니다.

### <a name="copy-data-to-the-azure-data-lake-storage-gen-2-account"></a>Azure Data Lake 저장소 Gen 2 계정으로 데이터 복사

Azure Data Factory를 사용 하 여 또는 Azure 기반 Hadoop 클러스터를 사용 하 여 데이터를 복사할 수 있습니다.

* Azure Data Factory를 사용 하려면 참조 [ADLS Gen2로 데이터를 이동 하려면 Azure Data Factory](https://docs.microsoft.com/azure/data-factory/load-azure-data-lake-storage-gen2)합니다. 지정 해야 합니다 **Azure Blob Storage** 원본으로 합니다.

* Azure 기반 Hadoop 클러스터를 사용 하려면이 DistCp 명령을 실행 합니다.

    ```bash
    hadoop distcp -Dfs.azure.account.key.<source_account>.dfs.windows.net=<source_account_key> abfs://<source_container> @<source_account>.dfs.windows.net/<source_path> abfs://<dest_container>@<dest_account>.dfs.windows.net/<dest_path>
    ```

    * 대체는 `<source_account>` 고 `<dest_account>` 원본 및 대상 저장소 계정 이름의 자리 표시자입니다.

    * 대체는 `<source_container>` 및 `<dest_container>` 원본 및 대상 컨테이너의 이름은 자리 표시자입니다.

    * 대체는 `<source_path>` 고 `<dest_path>` 원본 및 대상 디렉터리 경로 사용 하 여 자리 표시자입니다.

    * 대체는 `<source_account_key>` 데이터가 포함 된 저장소 계정의 액세스 키를 사용 하 여 자리 표시자입니다.

    이 명령은 Data Lake 저장소 Gen2 저장소 계정의 저장소 계정에서 데이터와 메타 데이터를 복사합니다.

### <a name="create-a-service-principal-for-your-azure-data-lake-storage-gen2-account"></a>Azure Data Lake 저장소 Gen2 계정에 대 한 서비스 주체 만들기

서비스 주체를 만들려면 참조 [방법: 포털을 사용하여 리소스에 액세스할 수 있는 Azure AD 애플리케이션 및 서비스 주체 만들기](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)

* 문서의 [애플리케이션을 역할에 할당](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#assign-the-application-to-a-role) 섹션에 있는 단계를 수행할 때 **Storage Blob 데이터 참가자** 역할을 서비스 주체에 할당해야 합니다.

* 단계를 수행 하는 경우는 [로그인에 대 한 값을 얻으려면](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) 문서, 응용 프로그램 ID 및 클라이언트 암호 값을 텍스트 파일로 섹션입니다. 곧 이 값들이 필요합니다.

### <a name="generate-a-list-of-copied-files-with-their-permissions"></a>함께 해당 사용 권한 복사 된 파일의 목록을 생성 합니다.

온-프레미스 Hadoop 클러스터에서이 명령을 실행 합니다.

```bash

sudo -u hdfs ./copy-acls.sh -s /{hdfs_path} > ./filelist.json
```

이 명령은 함께 해당 사용 권한 복사 된 파일의 목록을 생성합니다.

> [!NOTE]
> HDFS의 파일 수에 따라이 명령을 실행 하려면 시간이 오래 걸릴 수 있습니다.

### <a name="generate-a-list-of-identities-and-map-them-to-azure-active-directory-add-identities"></a>Id의 목록을 생성 하 고 Azure Active Directory (추가) id에 매핑할 수

1. 다운로드는 `copy-acls.py` 스크립트입니다. 참조 된 [도우미 스크립트를 다운로드 및 실행 하 여에 지 노드 설정](#download-helper-scripts) 이 문서의 섹션입니다.

2. 고유한 id의 목록을 생성 하려면이 명령을 실행 합니다.

   ```bash
   
   ./copy-acls.py -s ./filelist.json -i ./id_map.json -g
   ```

   이 스크립트 파일을 생성 `id_map.json` 추가 기반 id에 매핑해야 하는 id를 포함 하는 합니다.

3. 텍스트 편집기에서 `id_map.json` 파일을 엽니다.

4. 파일에 표시 되는 각 JSON 개체에 대 한 업데이트는 `target` 는 AAD 사용자 이름 (UPN) 또는 ObjectId (OID), 적절 한 특성 매핑 identity입니다. 완료 되 면 파일을 저장 합니다. 다음 단계에서이 파일을 해야 합니다.

### <a name="apply-permissions-to-copied-files-and-apply-identity-mappings"></a>복사 된 파일에 권한을 적용합니다 하 고 id 매핑이 적용

Data Lake 저장소 Gen2 계정에 복사 하는 데이터에 권한을 적용 하려면이 명령을 실행 합니다.

```bash
./copy-acls.py -s ./filelist.json -i ./id_map.json  -A <storage-account-name> -C <container-name> --dest-spn-id <application-id>  --dest-spn-secret <client-secret>
```

* `<storage-account-name>` 자리 표시자를 스토리지 계정 이름으로 바꿉니다.

* 대체는 `<container-name>` 자리 표시자를 컨테이너의 이름입니다.

* 대체는 `<application-id>` 고 `<client-secret>` 서비스 주체를 만들 때 수집 된 응용 프로그램 ID 및 클라이언트 암호를 사용 하 여 자리 표시자입니다.

## <a name="appendix-split-data-across-multiple-data-box-devices"></a>부록: 여러 Data Box 장치에서 데이터 분할

Data Box 장치에 데이터를 이동 하기 전에 몇 가지 도우미 스크립트를 다운로드 하려면 데이터 Data Box 장치에 적합 하 고 불필요 한 파일을 제외 하려면 구성 되어 있는지 확인 해야 합니다.

<a id="download-helper-scripts" />

### <a name="download-helper-scripts-and-set-up-your-edge-node-to-run-them"></a>도우미 스크립트를 다운로드 및 실행 하 여에 지 노드 설정

1. Edge 또는 온-프레미스 Hadoop 클러스터의 헤드에서이 명령을 실행 합니다.

   ```bash
   
   git clone https://github.com/jamesbak/databox-adls-loader.git
   cd databox-adls-loader
   ```

   이 명령은 도우미 스크립트를 포함 하는 GitHub 리포지토리를 복제 합니다.

2. 있는 있는지 확인 합니다 [jq](https://stedolan.github.io/jq/) 패키지가 로컬 컴퓨터에 설치 합니다.

   ```bash
   
   sudo apt-get install jq
   ```

3. 설치 합니다 [요청](http://docs.python-requests.org/en/master/) python 패키지 있습니다.

   ```bash
   
   pip install requests
   ```

4. 필요한 스크립트 실행 권한을 설정 합니다.

   ```bash
   
   chmod +x *.py *.sh

   ```

### <a name="ensure-that-your-data-is-organized-to-fit-onto-a-data-box-device"></a>데이터 Data Box 장치에 맞게 구성 되어 있는지 확인 합니다.

단일 Data Box 장치 크기를 초과 하는 데이터의 크기, 여러 Data Box 장치에 저장할 수 있는 그룹으로 파일을 분할할 수 있습니다.

데이터는 단일 Data Box 장치의 크기를 초과 하지 않습니다, 경우에 다음 섹션으로 진행할 수 있습니다.

1. 승격 된 권한으로 실행 된 `generate-file-list` 이전 섹션의 지침에 따라 다운로드 한 스크립트입니다.

   명령 매개 변수 설명은 다음과 같습니다.

   ```
   sudo -u hdfs ./generate-file-list.py [-h] [-s DATABOX_SIZE] [-b FILELIST_BASENAME]
                    [-f LOG_CONFIG] [-l LOG_FILE]
                    [-v {DEBUG,INFO,WARNING,ERROR}]
                    path

   where:
   positional arguments:
   path                  The base HDFS path to process.

   optional arguments:
   -h, --help            show this help message and exit
   -s DATABOX_SIZE, --databox-size DATABOX_SIZE
                        The size of each Data Box in bytes.
   -b FILELIST_BASENAME, --filelist-basename FILELIST_BASENAME
                        The base name for the output filelists. Lists will be
                        named basename1, basename2, ... .
   -f LOG_CONFIG, --log-config LOG_CONFIG
                        The name of a configuration file for logging.
   -l LOG_FILE, --log-file LOG_FILE
                        Name of file to have log output written to (default is
                        stdout/stderr)
   -v {DEBUG,INFO,WARNING,ERROR}, --log-level {DEBUG,INFO,WARNING,ERROR}
                        Level of log information to output. Default is 'INFO'.
   ```

2. 생성된 된 파일 목록을 HDFS를 통해 액세스할 수 있도록 복사 합니다 [DistCp](https://hadoop.apache.org/docs/stable/hadoop-distcp/DistCp.html) 작업 합니다.

   ```
   hadoop fs -copyFromLocal {filelist_pattern} /[hdfs directory]
   ```

### <a name="exclude-unnecessary-files"></a>불필요 한 파일 제외

DisCp 작업에서 일부 디렉터리를 제외할 수 해야 합니다. 예를 들어, 실행 하는 클러스터를 유지 하는 상태 정보를 포함 하는 디렉터리를 제외 합니다.

DistCp 작업을 시작 하려는 온-프레미스 Hadoop 클러스터에서 제외 하려는 디렉터리의 목록을 지정 하는 파일을 만듭니다.

예를 들면 다음과 같습니다.

```
.*ranger/audit.*
.*/hbase/data/WALs.*
```

## <a name="next-steps"></a>다음 단계

HDInsight 클러스터를 사용 하 여 Data Lake 저장소 Gen2 작동 하는 방법을 알아봅니다. [Azure HDInsight 클러스터에 Azure Data Lake Storage Gen2 사용](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md)을 참조하세요.
