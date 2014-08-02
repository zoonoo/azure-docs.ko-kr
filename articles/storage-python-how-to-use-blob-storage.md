<properties linkid="develop-python-blob-service" urlDisplayName="Blob Service" pageTitle="How to use blob storage (Python) | Microsoft Azure" metaKeywords="Azure blob service Python, Azure blobs Python" description="Learn how to use the Azure Blob service to upload, list, download, and delete blobs." metaCanonical="" disqusComments="1" umbracoNaviHide="0" services="storage" documentationCenter="Python" title="How to use the Blob service from Python" authors="" videoId="" scriptId="" />

Python에서 Blob 저장소 서비스를 사용하는 방법
=============================================

이 가이드에서는 Azure Blob 저장소 서비스를 사용하여 일반 시나리오를 수행하는 방법을 보여 줍니다. 샘플은 Python API를 사용하여 작성되었습니다. Blob **업로드**, **나열**, **다운로드** 및 **삭제** 시나리오를 다룹니다. Blob에 대한 자세한 내용은 [다음 단계](#next-steps) 섹션을 참조하십시오.

목차
----

[Blob 저장소 정의](#what-is)
 [개념](#concepts)
 [Azure 저장소 계정 만들기](#create-account)
 [방법: 컨테이너 만들기](#create-container)
 [방법: 컨테이너에 Blob 업로드](#upload-blob)
 [방법: 컨테이너의 Blob 나열](#list-blob)
 [방법: Blob 다운로드](#download-blobs)
 [방법: Blob 삭제](#delete-blobs)
 [방법: 대규모 Blob 업로드 및 다운로드](#large-blobs)
 [다음 단계](#next-steps)

[WACOM.INCLUDE [howto-blob-storage](../includes/howto-blob-storage.md)]

Azure 저장소 계정 만들기
------------------------

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

방법: 컨테이너 만들기
---------------------

**참고:** Python 또는 클라이언트 라이브러리를 설치해야 하는 경우 [Python 설치 가이드](../python-how-to-install/)(영문)를 참조하십시오.

**BlobService** 개체를 통해 컨테이너 및 Blob에 대한 작업을 할 수 있습니다. 다음 코드는 **BlobService** 개체를 만듭니다. 프로그래밍 방식으로 Azure 저장소에 액세스하려는 Python 파일의 맨 위쪽에 다음을 추가합니다.

    from azure.storage import *

다음 코드는 저장소 계정 이름 및 계정 키를 사용하는 **BlobService** 개체를 만듭니다. 'myaccount' 및 'mykey'를 실제 계정 및 키로 바꾸십시오.

    blob_service = BlobService(account_name='myaccount', account_key='mykey')

모든 저장소 Blob은 컨테이너에 있습니다. **BlobService** 개체를 사용하면 컨테이너가 없는 경우 새로 만들 수 있습니다.

    blob_service.create_container('mycontainer')

기본적으로 새 컨테이너는 전용이므로 이 컨테이너에서 Blob을 다운로드하려면 저장소 액세스 키(위에서 한 것과 같이)를 지정해야 합니다. 컨테이너 내의 파일을 모든 사용자가 사용할 수 있도록 설정하려는 경우 다음 코드를 사용하여 컨테이너를 만들고 공용 액세스 수준을 전달할 수 있습니다.

    blob_service.create_container('mycontainer', x_ms_blob_public_access='container') 

또는 다음 코드를 사용하여 컨테이너를 만든 후 수정할 수 있습니다.

    blob_service.set_container_acl('mycontainer', x_ms_blob_public_access='container')

이렇게 변경한 후에는 인터넷의 모든 사용자가 공용 컨테이너의 Blob을 볼 수 있지만 수정하거나 삭제는 할 수 없습니다.

방법: 컨테이너에 Blob 업로드
----------------------------

Blob에 파일을 업로드하려면 **put\_blob** 메서드를 사용하여 Blob을 만들고 Blob의 콘텐츠로 파일 스트림을 사용합니다. 먼저 **task1.txt**(다른 파일 이름도 가능)라는 파일을 만들어 Python 파일과 동일한 디렉터리에 저장합니다.

    myblob = open(r'task1.txt', 'r').read()
    blob_service.put_blob('mycontainer', 'myblob', myblob, x_ms_blob_type='BlockBlob')

방법: 컨테이너의 Blob 나열
--------------------------

컨테이너의 Blob을 나열하려면 **list\_blobs** 메서드를 **for** 루프와 함께 사용하여 컨테이너의 각 Blob 이름을 표시합니다. 다음 코드는 컨테이너에 있는 각 Blob의 **이름** 및 **url**을 콘솔에 출력합니다.

    blobs = blob_service.list_blobs('mycontainer')
    for blob in blobs:
        print(blob.name)
        print(blob.url)

방법: Blob 다운로드
-------------------

Blob을 다운로드하려면 **get\_blob** 메서드를 사용하여 Blob 콘텐츠를 스트림 개체로 전송한 다음 이 개체를 로컬 파일에 저장합니다.

    blob = blob_service.get_blob('mycontainer', 'myblob')
    with open(r'out-task1.txt', 'w') as f:
        f.write(blob)

방법: Blob 삭제
---------------

마지막으로 Blob을 삭제하려면 **delete\_blob**을 호출합니다.

    blob_service.delete_blob('mycontainer', 'myblob') 

방법: 대규모 Blob 업로드 및 다운로드
------------------------------------

블록 Blob의 최대 크기는 200GB입니다. 64MB보다 작은 Blob의 경우 앞에서 살펴본 대로 **put\_blob** 또는 **get\_blob**의 단일 호출을 사용하여 Blob을 업로드하거나 다운로드할 수 있습니다. 64MB보다 큰 Blob의 경우 4MB 이하의 블록으로 Blob을 업로드하거나 다운로드해야 합니다.

다음 코드는 임의 크기의 블록 Blob을 업로드 또는 다운로드하는 함수의 예제를 보여 줍니다.

    import base64

    chunk_size = 4 * 1024 * 1024

    def upload(blob_service, container_name, blob_name, file_path):
        blob_service.create_container(container_name, None, None, False)
        blob_service.put_blob(container_name, blob_name, '', 'BlockBlob')

        block_ids = []
        index = 0
        with open(file_path, 'rb') as f:
            while True:
                data = f.read(chunk_size)
                if data:
                    length = len(data)
                    block_id = base64.b64encode(str(index))
                    blob_service.put_block(container_name, blob_name, data, block_id)
                    block_ids.append(block_id)
                    index += 1
                else:
                    break

        blob_service.put_block_list(container_name, blob_name, block_ids)

    def download(blob_service, container_name, blob_name, file_path):
        props = blob_service.get_blob_properties(container_name, blob_name)
        blob_size = int(props['content-length'])

        index = 0
        with open(file_path, 'wb') as f:
            while index < blob_size:
                chunk_range = 'bytes={}-{}'.format(index, index + chunk_size - 1)
                data = blob_service.get_blob(container_name, blob_name, x_ms_range=chunk_range)
                length = len(data)
                index += length
                if length > 0:
                    f.write(data)
                    if length < chunk_size:
                        break
                else:
                    break

200GB보다 큰 Blob이 필요한 경우 블록 Blob 대신 페이지 Blob을 사용할 수 있습니다. 페이지 Blob의 최대 크기는 1TB이며, 페이지가 512바이트 페이지 경계에 맞춰 정렬됩니다. 페이지 Blob을 만들려면 **put\_blob**을 사용하고 페이지 Blob에 쓰려면 **put\_page**를 사용하고 페이지 Blob에서 읽으려면 **get\_blob**을 사용합니다.

다음 단계
---------

이제 Blob 저장소의 기본 사항을 배웠으므로 다음 링크를 따라 좀 더 복잡한 저장소 작업을 수행하는 방법을 알아보십시오.

-   MSDN 참조: [Azure에서 데이터 저장 및 액세스](http://msdn.microsoft.com/en-us/library/windowsazure/gg433040.aspx)
-   [Azure 저장소 팀 블로그](http://blogs.msdn.com/b/windowsazurestorage/)(영문)를 방문하십시오.

  [Next Steps]: #next-steps
  [What is Blob Storage?]: #what-is
  [Concepts]: #concepts
  [Create an Azure Storage Account]: #create-account
  [How To: Create a Container]: #create-container
  [How To: Upload a Blob into a Container]: #upload-blob
  [How To: List the Blobs in a Container]: #list-blob
  [How To: Download Blobs]: #download-blobs
  [How To: Delete a Blob]: #delete-blobs
  [How To: Upload and Download Large Blobs]: #large-blobs
  [Storing and Accessing Data in Azure]: http://msdn.microsoft.com/en-us/library/windowsazure/gg433040.aspx
  [Azure Storage Team Blog]: http://blogs.msdn.com/b/windowsazurestorage/
