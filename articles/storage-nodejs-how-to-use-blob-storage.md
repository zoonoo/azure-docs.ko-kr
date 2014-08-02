<properties linkid="dev-nodejs-how-to-blob-storage" urlDisplayName="Blob Service" pageTitle="How to use blob storage (Node.js) | Microsoft Azure" metaKeywords="Get started Azure blob, Azure unstructured data, Azure unstructured storage, Azure blob, Azure blob storage, Azure blob Node.js" description="Learn how to use the Azure blob service to upload, download, list, and delete blob content. Samples written in Node.js." metaCanonical="" services="storage" documentationCenter="Node.js" title="How to Use the Blob Service from Node.js" authors="" solutions="" manager="" editor="" />

Node.js에서 Blob 서비스를 사용하는 방법
=======================================

이 가이드에서는 Azure Blob 서비스를 사용하여 일반 시나리오를 수행하는 방법을 보여 줍니다. 샘플은 Node.js API를 사용하여 작성되었습니다. Blob **업로드**, **나열**, **다운로드** 및 **삭제** 시나리오를 다룹니다. Blob에 대한 자세한 내용은 [다음 단계](#next-steps) 섹션을 참조하십시오.

목차
----

-   [Blob 서비스 정의](#what-is)
-   [개념](#concepts)
-   [Azure 저장소 계정 만들기](#create-account)
-   [Node.js 응용 프로그램 만들기](#create-app)
-   [저장소에 액세스하도록 응용 프로그램 구성](#configure-access)
-   [Azure 저장소 연결 문자열 설정](#setup-connection-string)
-   [방법: 컨테이너 만들기](#create-container)
-   [방법: 컨테이너에 Blob 업로드](#upload-blob)
-   [방법: 컨테이너의 Blob 나열](#list-blob)
-   [방법: Blob 다운로드](#download-blobs)
-   [방법: Blob 삭제](#delete-blobs)
-   [다음 단계](#next-steps)

Blob 서비스 정의
----------------

Azure Blob 서비스는 HTTP 또는 HTTPS를 통해 전 세계 어디에서나 액세스할 수 있는 다량의 구조화되지 않은 데이터를 저장하기 위한 것입니다. 단일 Blob의 크기는 수백 GB일 수 있으며, 단일 저장소 계정에 최대 100TB의 Blob이 포함될 수 있습니다. Blob의 일반적인 사용은 다음과 같습니다.

-   브라우저에 직접 이미지 또는 문서 제공
-   분산 액세스를 위해 파일 저장
-   동영상 및 오디오 스트리밍
-   보안 백업 및 재해 복구 수행
-   온-프레미스 또는 Azure 호스티드 서비스에서 분석하기 위해 데이터 저장

Blob을 사용하여 세상에 공개적으로 또는 내부 응용 프로그램 저장소에 비공개적으로 데이터를 표시할 수 있습니다.

개념
----

Blob 서비스에는 다음 구성 요소가 포함됩니다.

![Blob1](./media/storage-nodejs-how-to-use-blob-storage/blob1.jpg)

-   **URL 형식:** 다음 URL 형식을 사용하여 Blob에 주소를 지정할 수 있습니다.

        http://storageaccount.blob.core.windows.net/container/blob  

    다음 URL은 다이어그램에 있는 Blob 중 하나의 주소를 지정합니다.

        http://sally.blob.core.windows.net/movies/MOV1.AVI

-   **저장소 계정:** Azure 저장소에 대한 모든 액세스는 저장소 계정을 통해 수행됩니다. 이는 Blob 액세스를 위한 가장 높은 수준의 네임스페이스입니다. 전체 크기가 100TB를 초과하지 않을 경우 한 계정에 포함될 수 있는 컨테이너 수는 제한이 없습니다.

-   **컨테이너:** 컨테이너는 Blob 집합 그룹화를 제공합니다. 모든 Blob은 컨테이너에 있어야 합니다. 한 계정에 포함될 수 있는 컨테이너 수에는 제한이 없습니다. 한 컨테이너에 저장될 수 있는 Blob 수에도 제한이 없습니다.

-   **Blob:** 임의 형식 및 크기의 파일입니다. 블록 Blob과 페이지 Blob의 두 가지가 있습니다. 대부분의 파일은 블록 Blob입니다. 단일 블록 Blob의 크기는 최대 200GB일 수 있습니다. 이 자습서에서는 블록 Blob을 사용합니다. 다른 Blob 유형인 페이지 Blob의 크기는 최대 1TB일 수 있으며, 파일의 바이트 범위가 자주 수정되는 경우 더 효율적입니다.

Azure 저장소 계정 만들기
------------------------

저장소 작업을 사용하려면 Azure 저장소 계정이 필요합니다. 다음 단계에 따라 저장소 계정을 만들 수 있습니다. [REST API를 사용](http://msdn.microsoft.com/en-us/library/windowsazure/hh264518.aspx)하여 저장소 계정을 만들 수도 있습니다.

1.  [Azure 관리 포털](http://manage.windowsazure.com)에 로그인합니다.

2.  탐색 창 맨 아래쪽에서 **+새로 만들기**를 클릭합니다.

    ![+새로 만들기](./media/storage-nodejs-how-to-use-blob-storage/plus-new.png)

3.  **저장소 계정**을 클릭한 후 **빠른 생성**을 클릭합니다.

    ![빠른 생성 대화 상자](./media/storage-nodejs-how-to-use-blob-storage/quick-storage.png)

4.  URL에서 저장소 계정의 URI에 사용할 하위 도메인 이름을 입력합니다. 이 입력에는 3-24자의 소문자와 숫자를 사용할 수 있습니다. 이 값은 구독에 대한 Blob, 큐 또는 테이블 리소스의 주소를 지정하는 데 사용되는 URI 내의 호스트 이름이 됩니다.

5.  저장소를 찾을 영역/선호도 그룹을 선택합니다. Azure 응용 프로그램에서 저장소를 사용하는 경우 응용 프로그램을 배포할 영역과 동일한 영역을 선택합니다.

6.  **저장소 계정 만들기**를 클릭합니다.

Node.js 응용 프로그램 만들기
----------------------------

빈 Node.js 응용 프로그램을 만듭니다. Node.js 응용 프로그램을 만드는 방법에 대한 지침은 [Node.js 응용 프로그램을 만들어 Azure 웹 사이트에 배포하기](/en-us/develop/nodejs/tutorials/create-a-website-(mac)/), [Node.js 클라우드 서비스]({localLink:2221} "Node.js 웹 응용 프로그램")(Windows PowerShell 사용) 또는 [WebMatrix를 사용하는 웹 사이트](/en-us/develop/nodejs/tutorials/web-site-with-webmatrix/)를 참조하십시오.

저장소에 액세스하도록 응용 프로그램 구성
----------------------------------------

Azure 저장소를 사용하려면 저장소 REST 라이브러리와 통신하는 편리한 라이브러리 집합이 포함되어 있는 Node.js Azure 패키지를 다운로드하여 사용해야 합니다.

### NPM(Node Package Manager)을 사용하여 패키지 가져오기

1.  **PowerShell**(Windows), **Terminal**(Mac), **Bash**(Unix) 등과 같은 명령줄 인터페이스를 사용하여 샘플 응용 프로그램을 만든 폴더로 이동합니다.

2.  명령 창에 **npm install azure**를 입력합니다. 그러면 다음과 같이 출력됩니다.

        azure@0.7.5 node_modules\azure
        ├── dateformat@1.0.2-1.2.3
        ├── xmlbuilder@0.4.2
        ├── node-uuid@1.2.0
        ├── mime@1.2.9
        ├── underscore@1.4.4
        ├── validator@1.1.1
        ├── tunnel@0.0.2
        ├── wns@0.5.3
        ├── xml2js@0.2.7 (sax@0.5.2)
        └── request@2.21.0 (json-stringify-safe@4.0.0, forever-agent@0.5.0, aws-sign@0.3.0, tunnel-agent@0.3.0, oauth-sign@0.3.0, qs@0.6.5, cookie-jar@0.3.0, node-uuid@1.4.0, http-signature@0.9.11, form-data@0.0.8, hawk@0.13.1)

3.  **ls** 명령을 수동으로 실행하여 **node\_modules** 폴더가 만들어졌는지 확인할 수 있습니다. 이 폴더에서 저장소에 액세스하는 데 필요한 라이브러리가 들어 있는 **azure** 패키지를 찾습니다.

### 패키지 가져오기

메모장 또는 다른 텍스트 편집기를 사용하여 저장소를 사용할 응용 프로그램의 **server.js** 파일 맨 위에 다음을 추가합니다.

    var azure = require('azure');

Azure 저장소 연결 설정
----------------------

Azure 모듈은 AZURE\_STORAGE\_ACCOUNT 및 AZURE\_STORAGE\_ACCESS\_KEY 환경 변수를 읽고 Azure 저장소 계정에 연결하는 데 필요한 정보를 확인합니다. 이러한 환경 변수가 설정되어 있지 않은 경우 **createBlobService**를 호출할 때 계정 정보를 지정해야 합니다.

Azure 클라우드 서비스의 구성 파일에서 환경 변수를 설정하는 방법에 대한 예제는 [Node.js 클라우드 서비스 및 저장소](/en-us/develop/nodejs/tutorials/web-app-with-storage/)를 참조하십시오.

Azure 웹 사이트의 관리 포털에서 환경 변수를 설정하는 방법에 대한 예제는 [Node.js 웹 응용 프로그램 및 저장소](/en-us/develop/nodejs/tutorials/web-site-with-storage/)를 참조하십시오.

방법: 컨테이너 만들기
---------------------

**BlobService** 개체를 사용하여 컨테이너 및 Blob 작업을 수행할 수 있습니다. 다음 코드는 **BlobService** 개체를 만듭니다. **server.js**의 위쪽에 다음을 추가합니다.

    var blobService = azure.createBlobService();

모든 Blob은 컨테이너에 있습니다. **BlobService** 개체에서 **createContainerIfNotExists**를 호출하면 지정된 컨테이너(있는 경우)가 반환되거나 새 컨테이너가 지정된 이름으로 만들어집니다(컨테이너가 없는 경우). 기본적으로 새 컨테이너는 전용이며 이 컨테이너에서 Blob을 다운로드하려면 액세스 키를 사용해야 합니다.

    blobService.createContainerIfNotExists(containerName, function(error){
        if (!error) {
            // Container exists and is private
        }
    });

액세스 키를 사용하지 않고 액세스할 수 있도록 컨테이너의 파일을 공용으로 지정하려면 컨테이너의 액세스 수준을 **Blob** 또는 **container**로 설정할 수 있습니다. 액세스 수준을 **Blob**으로 설정하면 이 컨테이너 내의 Blob 콘텐츠와 메타데이터에는 익명 읽기 권한이 허용되지만, 컨테이너 내의 모든 Blob 나열과 같은 컨테이너 메타데이터에는 익명 읽기 권한이 허용되지 않습니다. 액세스 수준을 **container**로 설정하면 Blob 콘텐츠와 메타데이터뿐 아니라 컨테이너 메타데이터에 대한 익명 읽기 권한이 허용됩니다. 다음 예제에서는 액세스 수준을 **Blob**으로 설정하는 방법을 보여 줍니다.

    blobService.createContainerIfNotExists(containerName
        , {publicAccessLevel : 'blob'}
        , function(error){
            if (!error) {
                // Container exists and is public
            }
        });

또는 **setContainerAcl**로 액세스 수준을 지정하여 컨테이너의 액세스 수준을 수정할 수 있습니다. 다음 예제에서는 액세스 수준을 container로 변경합니다.

    blobService.setContainerAcl(containerName
        , 'container'
        , function(error){
            if (!error) {
                // Container access level set to 'container'
            }
        });

### 필터

**BlobService**를 사용하여 수행되는 작업에 선택적 필터링 작업을 적용할 수 있습니다. 필터링 작업은 로깅, 자동으로 다시 시도 등을 포함할 수 있습니다. 필터는 서명을 사용하여 메서드를 구현하는 개체입니다.

     function handle (requestOptions, next)

요청 옵션에 대한 전처리를 수행한 후 메서드는 다음 서명을 사용하여 콜백을 전달하는 "next"를 호출해야 합니다.

     function (returnObject, finalCallback, next)

이 콜백에서 returnObject(서버에 요청 응답 반환)를 처리한 후 콜백은 next(있는 경우)를 호출하여 다른 필터를 계속 처리하거나 finalCallback을 호출하여 서비스 호출을 종료해야 합니다.

Azure SDK for Node.js에는 재시도 논리를 구현하는 두 필터 **ExponentialRetryPolicyFilter** 및 **LinearRetryPolicyFilter**가 포함되어 있습니다. 다음은 **ExponentialRetryPolicyFilter**를 사용하는 **BlobService** 개체를 만듭니다.

    var retryOperations = new azure.ExponentialRetryPolicyFilter();
    var blobService = azure.createBlobService().withFilter(retryOperations);

방법: 컨테이너에 Blob 업로드
----------------------------

Blob에 데이터를 업로드하려면 **createBlockBlobFromFile**, **createBlockBlobFromStream** 또는 **createBlockBlobFromText** 메서드를 사용합니다. **createBlockBlobFromFile**은 파일 콘텐츠를 업로드하고, **createBlockBlobFromStream**은 스트림 콘텐츠를 업로드하고, **createBlockBlobFromText**는 지정된 텍스트 값을 업로드합니다.

다음 예제에서는 **test1.txt** 파일 콘텐츠를 'test1' Blob에 업로드합니다.

    blobService.createBlockBlobFromFile(containerName
        , 'test1'
        , 'test1.txt'
        , function(error){
            if (!error) {
                // File has been uploaded
            }
        });

방법: 컨테이너의 Blob 나열
--------------------------

컨테이너의 Blob을 나열하려면 **listBlobs** 메서드를 **for** 루프와 함께 사용하여 컨테이너에 있는 각 Blob의 이름을 표시합니다. 다음 코드는 컨테이너에 있는 각 Blob의 **이름**을 콘솔에 출력합니다.

    blobService.listBlobs(containerName, function(error, blobs){
        if (!error) {
            for(var index in blobs){
                console.log(blobs[index].name);
            }
        }
    });

방법: Blob 다운로드
-------------------

Blob에서 데이터를 다운로드하려면 **getBlobToFile**, **getBlobToStream** 또는 **getBlobToText**를 사용합니다. 다음 예제에서는 **getBlobToStream**을 사용하여 **test1** Blob의 콘텐츠를 다운로드한 다음 스트림을 사용하여 **output.txt** 파일에 저장하는 방법을 보여 줍니다.

    var fs=require('fs');
    blobService.getBlobToStream(containerName
        , 'test1'
        , fs.createWriteStream('output.txt')
        , function(error){
            if (!error) {
                // Wrote blob to stream
            }
        });

방법: Blob 삭제
---------------

마지막으로 Blob을 삭제하려면 **deleteBlob**을 호출합니다. 다음 예제에서는 'blob1'이라는 Blob을 삭제합니다.

    blobService.deleteBlob(containerName
        , 'blob1'
        , function(error){
            if (!error) {
                // Blob has been deleted
            }
        });

다음 단계
---------

이제 Blob 저장소의 기본 사항을 배웠으므로 다음 링크를 따라 좀 더 복잡한 저장소 작업을 수행하는 방법을 알아보십시오.

-   다음 MSDN 참조를 확인하십시오. [Azure에 데이터 저장 및 액세스](http://msdn.microsoft.com/en-us/library/windowsazure/gg433040.aspx)
-   [Azure 저장소 팀 블로그](http://blogs.msdn.com/b/windowsazurestorage/)(영문)를 방문하십시오.
-   GitHub에서 [Azure SDK for Node](https://github.com/WindowsAzure/azure-sdk-for-node) 리포지토리를 방문하십시오.

