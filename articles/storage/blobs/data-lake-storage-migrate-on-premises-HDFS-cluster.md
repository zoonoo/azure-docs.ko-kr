---
title: 온프레미 HDFS 저장소에서 Azure 데이터 상자를 사용하여 Azure 저장소로 마이그레이션
description: 온-프레미스 HDFS 저장소에서 Azure 저장소로 데이터 마이그레이션
author: normesta
ms.service: storage
ms.date: 02/14/2019
ms.author: normesta
ms.topic: conceptual
ms.subservice: data-lake-storage-gen2
ms.reviewer: jamesbak
ms.openlocfilehash: c0c6a8637223727a9b0c88245d939605f6a8530e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78302003"
---
# <a name="migrate-from-on-prem-hdfs-store-to-azure-storage-with-azure-data-box"></a>온프레미 HDFS 저장소에서 Azure 데이터 상자를 사용하여 Azure 저장소로 마이그레이션

데이터 상자 장치를 사용하여 Hadoop 클러스터의 온-프레미스 HDFS 저장소에서 Azure 저장소(Blob 저장소 또는 데이터 레이크 저장소 Gen2)로 데이터를 마이그레이션할 수 있습니다. 80TB 데이터 박스 또는 770TB 데이터 박스 헤비 중에서 선택할 수 있습니다.

이 문서에서는 다음 작업을 완료하는 데 도움이 됩니다.

> [!div class="checklist"]
> * 데이터 마이그레이션을 준비합니다.
> * 데이터를 데이터 상자 또는 데이터 상자 무거운 장치에 복사합니다.
> * 장치를 Microsoft로 다시 발송합니다.
> * 파일 및 디렉터리에 액세스 권한 적용(데이터 레이크 스토리지 Gen2만 해당)

## <a name="prerequisites"></a>사전 요구 사항

마이그레이션을 완료하려면 이러한 사항이 필요합니다.

* Azure Storage 계정.

* 원본 데이터가 포함된 온-프레미스 Hadoop 클러스터입니다.

* [Azure 데이터 상자 장치](https://azure.microsoft.com/services/storage/databox/).

  * [데이터 상자](https://docs.microsoft.com/azure/databox/data-box-deploy-ordered) 또는 [데이터 상자 무거운](https://docs.microsoft.com/azure/databox/data-box-heavy-deploy-ordered)주문 . 

  * [케이블을](https://docs.microsoft.com/azure/databox/data-box-deploy-set-up) 연결하고 데이터 박스 또는 [데이터 박스 헤비를](https://docs.microsoft.com/azure/databox/data-box-heavy-deploy-set-up) 온-프레미스 네트워크에 연결합니다.

준비가 되면 시작해 봅시다.

## <a name="copy-your-data-to-a-data-box-device"></a>데이터 상자 장치에 데이터 복사

데이터가 단일 데이터 상자 장치에 맞는 경우 데이터를 데이터 상자 장치에 복사합니다. 

데이터 크기가 데이터 상자 장치의 용량을 초과하는 경우 선택적 절차를 사용하여 [여러 데이터 박스 장치간에 데이터를 분할한](#appendix-split-data-across-multiple-data-box-devices) 다음 이 단계를 수행합니다. 

온-프레미스 HDFS 저장소의 데이터를 데이터 상자 장치로 복사하려면 몇 가지 사항을 설정한 다음 [DistCp](https://hadoop.apache.org/docs/stable/hadoop-distcp/DistCp.html) 도구를 사용합니다.

다음 단계에 따라 Blob/Object 저장소의 REST API를 통해 데이터를 데이터 상자 장치에 복사합니다. REST API 인터페이스를 통해 장치가 클러스터에 HDFS 저장소로 표시됩니다.

1. REST를 통해 데이터를 복사하기 전에 데이터 상자 또는 데이터 상자 헤비의 REST 인터페이스에 연결할 보안 및 연결 프리미티브를 식별합니다. 데이터 상자의 로컬 웹 UI에 로그인하고 **연결 및 복사** 페이지로 이동합니다. 장치에 대한 Azure 저장소 계정에 대해 **액세스 설정에서** **REST를**찾습니다.

    !["연결 및 복사" 페이지](media/data-lake-storage-migrate-on-premises-HDFS-cluster/data-box-connect-rest.png)

2. 액세스 저장소 계정 및 업로드 데이터 대화 상자에서 **Blob 서비스 끝점** 및 **저장소 계정 키를**복사합니다. Blob 서비스 끝점에서 `https://` 는 및 후행 슬래시를 생략합니다.

    이 경우 끝점은 다음과 `https://mystorageaccount.blob.mydataboxno.microsoftdatabox.com/`입니다. 사용할 URI의 호스트 부분은 다음과 `mystorageaccount.blob.mydataboxno.microsoftdatabox.com`입니다. 예를 들어 HTTP를 [통해 REST에 연결하는 방법을 참조하세요.](/azure/databox/data-box-deploy-copy-data-via-rest) 

     !["저장소 계정에 액세스하고 데이터 업로드" 대화 상자](media/data-lake-storage-migrate-on-premises-HDFS-cluster/data-box-connection-string-http.png)

3. 각 노드에 끝점과 데이터 상자 또는 데이터 `/etc/hosts` 상자 무거운 노드 IP 주소를 추가합니다.

    ```    
    10.128.5.42  mystorageaccount.blob.mydataboxno.microsoftdatabox.com
    ```

    DNS에 다른 메커니즘을 사용하는 경우 데이터 상자 끝점을 확인할 수 있는지 확인해야 합니다.

4. 쉘 변수를 `azjars` `hadoop-azure` 및 `azure-storage` jar 파일의 위치로 설정합니다. 이러한 파일은 Hadoop 설치 디렉토리에서 찾을 수 있습니다.

    이러한 파일이 있는지 확인하려면 다음 `ls -l $<hadoop_install_dir>/share/hadoop/tools/lib/ | grep azure`명령을 사용합니다. `<hadoop_install_dir>` 자리 표시자를 Hadoop을 설치한 디렉터리의 경로로 바꿉습니다. 정규화된 경로를 사용해야 합니다.

    예제:

    `azjars=$hadoop_install_dir/share/hadoop/tools/lib/hadoop-azure-2.6.0-cdh5.14.0.jar` `azjars=$azjars,$hadoop_install_dir/share/hadoop/tools/lib/microsoft-windowsazure-storage-sdk-0.6.0.jar`

5. 데이터 복사본에 사용할 저장소 컨테이너를 만듭니다. 또한 이 명령의 일부로 대상 디렉터리를 지정해야 합니다. 이 시점에서 더미 대상 디렉토리가 될 수 있습니다.

    ```
    hadoop fs -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.<blob_service_endpoint>=<account_key> \
    -mkdir -p  wasb://<container_name>@<blob_service_endpoint>/<destination_directory>
    ```

    * `<blob_service_endpoint>` 자리 표시자를 Blob 서비스 끝점의 이름으로 바꿉니다.

    * `<account_key>` 자리 표시자를 계정의 액세스 키로 바꿉습니다.

    * `<container-name>` 자리 표시자를 컨테이너 이름으로 바꿉니다.

    * `<destination_directory>` 자리 표시자를 데이터를 복사할 디렉터리 이름으로 바꿉니다.

6. 목록 명령을 실행하여 컨테이너 및 디렉터리가 만들어졌도록 합니다.

    ```
    hadoop fs -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.<blob_service_endpoint>=<account_key> \
    -ls -R  wasb://<container_name>@<blob_service_endpoint>/
    ```

   * `<blob_service_endpoint>` 자리 표시자를 Blob 서비스 끝점의 이름으로 바꿉니다.

   * `<account_key>` 자리 표시자를 계정의 액세스 키로 바꿉습니다.

   * `<container-name>` 자리 표시자를 컨테이너 이름으로 바꿉니다.

7. Hadoop HDFS의 데이터를 데이터 상자 Blob 저장소로 복사하여 이전에 만든 컨테이너에 복사합니다. 복사할 디렉터리를 찾을 수 없는 경우 명령이 자동으로 만듭니다.

    ```
    hadoop distcp \
    -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.<blob_service_endpoint<>=<account_key> \
    -filters <exclusion_filelist_file> \
    [-f filelist_file | /<source_directory> \
           wasb://<container_name>@<blob_service_endpoint>/<destination_directory>
    ```

    * `<blob_service_endpoint>` 자리 표시자를 Blob 서비스 끝점의 이름으로 바꿉니다.

    * `<account_key>` 자리 표시자를 계정의 액세스 키로 바꿉습니다.

    * `<container-name>` 자리 표시자를 컨테이너 이름으로 바꿉니다.

    * 자리 `<exlusion_filelist_file>` 표시자를 파일 제외 목록이 포함된 파일 이름으로 바꿉니다.

    * 자리 `<source_directory>` 표시자를 복사할 데이터가 포함된 디렉터리 이름으로 바꿉니다.

    * `<destination_directory>` 자리 표시자를 데이터를 복사할 디렉터리 이름으로 바꿉니다.

    이 `-libjars` 옵션은 `hadoop-azure*.jar` 및 종속 `azure-storage*.jar` 파일을 에서 사용할 `distcp`수 있도록 하는 데 사용됩니다. 일부 클러스터에 대해 이미 이러한 이 가 발생할 수 있습니다.

    다음 예제에서는 `distcp` 명령을 사용하여 데이터를 복사하는 방법을 보여 주십습니다.

    ```
     hadoop distcp \
    -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.mystorageaccount.blob.mydataboxno.microsoftdatabox.com=myaccountkey \
    -filter ./exclusions.lst -f /tmp/copylist1 -m 4 \
    /data/testfiles \
    wasb://hdfscontainer@mystorageaccount.blob.mydataboxno.microsoftdatabox.com/data
    ```
  
    복사 속도를 향상하려면 다음을 수행하십시오.

    * 매퍼 수를 변경해 보십시오. (위의 예는 `m` = 4 매퍼를 사용합니다.)

    * 여러 `distcp` 번 병렬로 실행해 보십시오.

    * 대용량 파일은 작은 파일보다 성능이 뛰어나다는 것을 기억하십시오.

## <a name="ship-the-data-box-to-microsoft"></a>데이터 상자를 마이크로소프트에 발송

다음 단계에 따라 데이터 상자 장치를 준비하고 Microsoft로 제공합니다.

1. 먼저 [데이터 상자 또는 데이터 상자 헤비에 발송할 준비를 합니다.](https://docs.microsoft.com/azure/databox/data-box-deploy-copy-data-via-rest)

2. 장치 준비가 완료되면 BOM 파일을 다운로드합니다. 나중에 이러한 BOM 또는 매니페스트 파일을 사용하여 Azure에 업로드된 데이터를 확인합니다.

3. 장치를 종료하고 케이블을 제거합니다.

4. UPS를 사용하여 픽업을 예약합니다.

    * 데이터 상자 장치의 경우 [데이터 상자 발송을](https://docs.microsoft.com/azure/databox/data-box-deploy-picked-up)참조하십시오.

    * 데이터 상자 무거운 장치의 경우 [데이터 상자 무거운 발송을](https://docs.microsoft.com/azure/databox/data-box-heavy-deploy-picked-up)참조하십시오.

5. Microsoft에서 장치를 받으면 데이터 센터 네트워크에 연결되고 장치 주문을 할 때 지정한 저장소 계정에 데이터가 업로드됩니다. BOM 파일에 대해 모든 데이터가 Azure에 업로드되는지 확인합니다. 

## <a name="apply-access-permissions-to-files-and-directories-data-lake-storage-gen2-only"></a>파일 및 디렉터리에 액세스 권한 적용(데이터 레이크 스토리지 Gen2만 해당)

Azure Storage 계정에 데이터가 이미 있습니다. 이제 파일 및 디렉터리에 액세스 권한을 적용합니다.

> [!NOTE]
> 이 단계는 Azure Data Lake Storage Gen2를 데이터 저장소로 사용하는 경우에만 필요합니다. 계층적 네임스페이스가 없는 Blob 저장소 계정만 데이터 저장소로 사용하는 경우 이 섹션을 건너뛸 수 있습니다.

### <a name="create-a-service-principal-for-your-azure-data-lake-storage-gen2-account"></a>Azure 데이터 레이크 저장소 Gen2 계정에 대한 서비스 주체 만들기

서비스 주체를 만들려면 [방법: 포털을 사용하여 리소스에 액세스할 수 있는 Azure AD 응용 프로그램 및 서비스 주체를 만듭니다.](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)

* 문서의 [애플리케이션을 역할에 할당](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#assign-a-role-to-the-application) 섹션에 있는 단계를 수행할 때 **Storage Blob 데이터 참가자** 역할을 서비스 주체에 할당해야 합니다.

* 문서의 섹션에서 [로그인하기 위한 값 Get에서](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) 단계를 수행할 때 응용 프로그램 ID 및 클라이언트 비밀 값을 텍스트 파일에 저장합니다. 곧 이 값들이 필요합니다.

### <a name="generate-a-list-of-copied-files-with-their-permissions"></a>사용 권한이 있는 복사된 파일 목록 생성

온-프레미스 Hadoop 클러스터에서 다음 명령을 실행합니다.

```bash

sudo -u hdfs ./copy-acls.sh -s /{hdfs_path} > ./filelist.json
```

이 명령은 사용 권한이 있는 복사된 파일 목록을 생성합니다.

> [!NOTE]
> HDFS의 파일 수에 따라 이 명령을 실행하는 데 시간이 오래 걸릴 수 있습니다.

### <a name="generate-a-list-of-identities-and-map-them-to-azure-active-directory-add-identities"></a>ID 목록을 생성하고 Azure Active Directory(ADD) ID에 매핑합니다.

1. 스크립트를 `copy-acls.py` 다운로드합니다. 다운로드 [도우미 스크립트를 참조 하 고](#download-helper-scripts) 이 문서의 섹션을 실행 하려면 가장자리 노드를 설정 합니다.

2. 이 명령을 실행하여 고유한 ID 목록을 생성합니다.

   ```bash
   
   ./copy-acls.py -s ./filelist.json -i ./id_map.json -g
   ```

   이 스크립트는 ADD `id_map.json` 기반 ID에 매핑하는 데 필요한 ID를 포함하는 명명된 파일을 생성합니다.

3. 텍스트 편집기에서 `id_map.json` 파일을 엽니다.

4. 파일에 나타나는 각 JSON 개체에 대해 `target` AAD 사용자 주체 이름(UPN) 또는 OBJECTId(OID)의 특성을 적절한 매핑된 ID로 업데이트합니다. 작업이 완료되면 파일을 저장합니다. 다음 단계에서 이 파일이 필요합니다.

### <a name="apply-permissions-to-copied-files-and-apply-identity-mappings"></a>복사된 파일에 사용 권한을 적용하고 ID 매핑 적용

이 명령을 실행하여 Data Lake Storage Gen2 계정에 복사한 데이터에 대한 권한을 적용합니다.

```bash
./copy-acls.py -s ./filelist.json -i ./id_map.json  -A <storage-account-name> -C <container-name> --dest-spn-id <application-id>  --dest-spn-secret <client-secret>
```

* `<storage-account-name>` 자리 표시자를 스토리지 계정 이름으로 바꿉니다.

* `<container-name>` 자리 표시자를 컨테이너 이름으로 바꿉니다.

* `<application-id>` 및 `<client-secret>` 자리 표시자를 서비스 주체를 만들 때 수집한 응용 프로그램 ID 및 클라이언트 보안 정보로 바꿉습니다.

## <a name="appendix-split-data-across-multiple-data-box-devices"></a>부록: 여러 데이터 박스 장치 간에 데이터 분할

데이터를 데이터 상자 장치로 이동하기 전에 일부 도우미 스크립트를 다운로드하고 데이터가 데이터 상자 장치에 맞게 구성되었는지 확인하고 불필요한 파일을 제외해야 합니다.

<a id="download-helper-scripts" />

### <a name="download-helper-scripts-and-set-up-your-edge-node-to-run-them"></a>도우미 스크립트를 다운로드하고 에지 노드를 설정하여 실행

1. 온-프레미스 Hadoop 클러스터의 가장자리 또는 헤드 노드에서 다음 명령을 실행합니다.

   ```bash
   
   git clone https://github.com/jamesbak/databox-adls-loader.git
   cd databox-adls-loader
   ```

   이 명령은 도우미 스크립트를 포함하는 GitHub 리포지토리를 복제합니다.

2. [jq](https://stedolan.github.io/jq/) 패키지가 로컬 컴퓨터에 설치되어 있는지 확인합니다.

   ```bash
   
   sudo apt-get install jq
   ```

3. 요청 파이썬 패키지를 [설치합니다.](https://2.python-requests.org/en/master/)

   ```bash
   
   pip install requests
   ```

4. 필요한 스크립트에 대한 실행 권한을 설정합니다.

   ```bash
   
   chmod +x *.py *.sh

   ```

### <a name="ensure-that-your-data-is-organized-to-fit-onto-a-data-box-device"></a>데이터 박스 장치에 맞게 데이터가 구성되었는지 확인

데이터 크기가 단일 데이터 상자 장치의 크기를 초과하는 경우 파일을 여러 데이터 상자 장치에 저장할 수 있는 그룹으로 분할할 수 있습니다.

데이터가 singe 데이터 박스 장치의 크기를 초과하지 않는 경우 다음 섹션으로 진행할 수 있습니다.

1. 높은 사용 권한을 사용하면 `generate-file-list` 이전 섹션의 지침에 따라 다운로드한 스크립트를 실행합니다.

   다음은 명령 매개 변수에 대한 설명입니다.

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

2. 생성된 파일 목록을 HDFS에 복사하여 [DistCp](https://hadoop.apache.org/docs/stable/hadoop-distcp/DistCp.html) 작업에 액세스할 수 있도록 합니다.

   ```
   hadoop fs -copyFromLocal {filelist_pattern} /[hdfs directory]
   ```

### <a name="exclude-unnecessary-files"></a>불필요한 파일 제외

DisCp 작업에서 일부 디렉터리를 제외해야 합니다. 예를 들어 클러스터를 계속 실행하는 상태 정보가 포함된 디렉터리를 제외합니다.

DistCp 작업을 시작할 예정인 온-프레미스 Hadoop 클러스터에서 제외할 디렉터리 목록을 지정하는 파일을 만듭니다.

예를 들면 다음과 같습니다.

```
.*ranger/audit.*
.*/hbase/data/WALs.*
```

## <a name="next-steps"></a>다음 단계

데이터 레이크 스토리지 Gen2가 HDInsight 클러스터와 어떻게 작동하는지 알아보십시오. [Azure HDInsight 클러스터에 Azure Data Lake Storage Gen2 사용](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md)을 참조하세요.
