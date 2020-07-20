---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 06/19/2020
ms.author: alkohli
ms.openlocfilehash: 526b3ad89e128d264b5d14d8cc87d9a81d431a9c
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/09/2020
ms.locfileid: "86208898"
---
데이터 복사를 시작하기 전에 다음을 수행합니다.

1. *복사 로그*를 다운로드합니다. **연결 및 복사** 페이지에서 **복사 로그**를 선택합니다. 메시지가 표시되면 시스템에 로그를 저장합니다. 

    ![복사 로그 다운로드 1](../articles/databox/media/data-box-deploy-export-copy-data/download-copy-log-1.png)

  
    *자세한 정보 로그*를 다운로드하는 단계를 반복합니다. 

2. *자세한 정보 로그*를 검토합니다. *자세한 정보 로그*에는 Azure Storage 계정에서 성공적으로 내보낸 모든 파일의 목록이 포함되어 있습니다. 로그에는 파일 크기 및 체크섬 계산도 포함됩니다.

    ```powershell
    <File CloudFormat="BlockBlob" Path="validblobdata/test1.2.3.4" Size="1024" crc64="7573843669953104266">
    </File><File CloudFormat="BlockBlob" Path="validblobdata/helloEndWithDot..txt" Size="11" crc64="7320094093915972193">
    </File><File CloudFormat="BlockBlob" Path="validblobdata/test..txt" Size="12" crc64="17906086011702236012">
    </File><File CloudFormat="BlockBlob" Path="validblobdata/test1" Size="1024" crc64="7573843669953104266">
    </File><File CloudFormat="BlockBlob" Path="validblobdata/test1.2.3" Size="1024" crc64="7573843669953104266">
    </File><File CloudFormat="BlockBlob" Path="validblobdata/.......txt" Size="11" crc64="7320094093915972193">
    </File><File CloudFormat="BlockBlob" Path="validblobdata/copylogb08fa3095564421bb550d775fff143ed====..txt" Size="53638" crc64="1147139997367113454">
    </File><File CloudFormat="BlockBlob" Path="validblobdata/testmaxChars-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-12345679" Size="1024" crc64="7573843669953104266">
    </File><File CloudFormat="BlockBlob" Path="export-ut-container/file0" Size="0" crc64="0">
    </File><File CloudFormat="BlockBlob" Path="export-ut-container/file1" Size="0" crc64="0">
    </File><File CloudFormat="BlockBlob" Path="export-ut-container/file4096_000001" Size="4096" crc64="16969371397892565512">
    </File><File CloudFormat="BlockBlob" Path="export-ut-container/file4096_000000" Size="4096" crc64="16969371397892565512">
    </File><File CloudFormat="BlockBlob" Path="export-ut-container/64KB-Seed10.dat" Size="65536" crc64="10746682179555216785">
    </File><File CloudFormat="BlockBlob" Path="export-ut-container/LiveSiteReport_Oct.xlsx" Size="7028" crc64="6103506546789189963">
    </File><File CloudFormat="BlockBlob" Path="export-ut-container/NE_Oct_GeoReport.xlsx" Size="103197" crc64="13305485882546035852">
    </File><File CloudFormat="BlockBlob" Path="export-ut-container/64KB-Seed1.dat" Size="65536" crc64="3140622834011462581">
    </File><File CloudFormat="BlockBlob" Path="export-ut-container/1mbfiles-0-0" Size="1048576" crc64="16086591317856295272">
    </File><File CloudFormat="BlockBlob" Path="export-ut-container/file524288_000001" Size="524288" crc64="8908547729214703832">
    </File><File CloudFormat="BlockBlob" Path="export-ut-container/4mbfiles-0-0" Size="4194304" crc64="1339017920798612765">
    </File><File CloudFormat="BlockBlob" Path="export-ut-container/file524288_000000" Size="524288" crc64="8908547729214703832">
    </File><File CloudFormat="BlockBlob" Path="export-ut-container/8mbfiles-0-1" Size="8388608" crc64="3963298606737216548">
    </File><File CloudFormat="BlockBlob" Path="export-ut-container/1mbfiles-0-1" Size="1048576" crc64="11061759121415905887">
    </File><File CloudFormat="BlockBlob" Path="export-ut-container/XLS-10MB.xls" Size="1199104" crc64="2218419493992437463">
    </File><File CloudFormat="BlockBlob" Path="export-ut-container/8mbfiles-0-0" Size="8388608" crc64="1072783424245035917">
    </File><File CloudFormat="BlockBlob" Path="export-ut-container/4mbfiles-0-1" Size="4194304" crc64="9991307204216370812">
    </File><File CloudFormat="BlockBlob" Path="export-ut-container/VL_Piracy_Negtive10_TPNameAndGCS.xlsx" Size="12398699" crc64="13526033021067702820">
    </File>
    ``````

3. 오류가 있는지 *복사 로그*를 검토합니다. 이 로그는 오류로 인해 복사할 수 없는 파일을 나타냅니다.

    오류가 없고 Azure에서 Data Box 디바이로 데이터를 복사하는 동안 모든 파일이 복사된 경우 복사 로그의 샘플 출력은 다음과 같습니다.

    ```powershell
    <CopyLog Summary="Summary">
      <Status>Succeeded</Status>
      <TotalFiles_Blobs>5521</TotalFiles_Blobs>
      <FilesErrored>0</FilesErrored>
    </CopyLog>
    ``` 
    복사 로그에 오류가 발생하고 일부 파일을 Azure에서 복사하지 못한 경우 샘플 출력은 다음과 같습니다.

    ```powershell
    <ErroredEntity CloudFormat="AppendBlob" Path="export-ut-appendblob/wastorage.v140.3.0.2.nupkg">
      <Category>UploadErrorCloudHttp</Category>
      <ErrorCode>400</ErrorCode>
      <ErrorMessage>UnsupportBlobType</ErrorMessage>
      <Type>File</Type>
    </ErroredEntity><ErroredEntity CloudFormat="AppendBlob" Path="export-ut-appendblob/xunit.console.Primary_2020-05-07_03-54-42-PM_27444.hcsml">
      <Category>UploadErrorCloudHttp</Category>
      <ErrorCode>400</ErrorCode>
      <ErrorMessage>UnsupportBlobType</ErrorMessage>
      <Type>File</Type>
    </ErroredEntity><ErroredEntity CloudFormat="AppendBlob" Path="export-ut-appendblob/xunit.console.Primary_2020-05-07_03-54-42-PM_27444 (1).hcsml">
      <Category>UploadErrorCloudHttp</Category>
      <ErrorCode>400</ErrorCode>
      <ErrorMessage>UnsupportBlobType</ErrorMessage>
      <Type>File</Type>
    </ErroredEntity><CopyLog Summary="Summary">
      <Status>Failed</Status>
      <TotalFiles_Blobs>4</TotalFiles_Blobs>
      <FilesErrored>3</FilesErrored>
    </CopyLog>    
    ```

    해당 파일을 내보낼 수 있는 다음과 같은 옵션이 있습니다. 

    - 네트워크를 통해 복사할 수 없는 파일을 전송할 수 있습니다. 
    - 데이터 크기가 사용 가능한 디바이스 용량보다 큰 경우 부분 복사가 발생하고 복사되지 않은 모든 파일이 이 로그에 나열됩니다. 이 로그를 입력 XML로 사용하여 새 Data Box 순서를 만든 다음, 이러한 파일을 복사할 수 있습니다.
