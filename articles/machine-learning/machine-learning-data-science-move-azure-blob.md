<properties 
	pageTitle="Azure Blob 저장소의 데이터 이동 | Microsoft Azure" 
	description="Azure Blob 저장소의 데이터 이동" 
	services="machine-learning,storage" 
	documentationCenter="" 
	authors="msolhab" 
	manager="paulettm" 
	editor="cgronlun" />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/29/2015" 
	ms.author="sunliangms;sachouks;mohabib;bradsev" />

# Azure Blob 저장소의 데이터 이동

[Azure 기계 학습의 ADAPT(고급 분석 프로세스 및 기술) 시나리오](../machine-learning-data-science-plan-sample-scenarios.md) 문서는 고급 분석 프로세스에서 사용되는 다양한 데이터 과학 워크플로에 필요한 리소스를 확인하도록 도와줍니다. 사용자 시나리오에 따라 Azure Blob 저장소의 데이터를 이동해야 하는 경우 다음 방법 중 하나를 사용합니다.

- [Azure 저장소 탐색기 사용](#explorer)
- [AzCopy 명령줄 유틸리티 사용](#AzCopy)
- [Python에서 Azure SDK 사용](#PythonSDK)


> [AZURE.TIP]하나의 대안으로, [Azure 데이터 팩터리](https://azure.microsoft.com/ko-kr/services/data-factory/)를 사용하여 Azure blob 저장소에서 데이터를 다운로드하여 게시된 Azure 기계 학습 웹 서비스로 전달하고, 예측 분석 결과를 받아 이를 저장소에 업로드하는 파이프라인을 만들고 예약할 수 있습니다. 자세한 내용은 [Azure 데이터 팩터리 및 Azure 기계 학습을 사용하여 예측 파이프라인 만들기](../data-factory/data-factory-create-predictive-pipelines.md)를 참조하세요.

<para></para>

> [AZURE.NOTE]Azure blob 저장소에 대한 전체 지침은 [Azure Blob 기본 사항](../storage-dotnet-how-to-use-blobs.md) 및 [Azure Blob 서비스](https://msdn.microsoft.com/library/azure/dd179376.aspx)를 참조하세요.

데이터를 업로드/다운로드하려면 Azure 저장소 계정 이름 및 계정 키를 알아야 합니다. 더 자세한 정보를 얻을 지침을 보려면 [저장소 계정 관리](../storage-create-storage-account.md)에서 "방법: 저장소 액세스 키 보기, 복사 및 다시 생성” 섹션을 참조하세요. 이 문서에서는 사용자에게 Azure 저장소 계정 및 해당하는 저장소 키가 있다고 가정합니다.


<a id="explorer"></a>
## Azure 저장소 탐색기 사용 

Azure 저장소 탐색기는 Azure 저장소 계정의 데이터를 검사하고 변경할 수 있는 무료 Windows 기반 도구입니다. [Azure 저장소 탐색기](http://azurestorageexplorer.codeplex.com/)에서 다운로드할 수 있습니다. 다음 단계에서는 Azure 저장소 탐색기를 사용하여 데이터를 업로드/다운로드하는 방법을 설명합니다.

1.  Azure 저장소 탐색기 실행 
2.  액세스하려는 저장소 탐색기가 Azure 저장소 탐색기에 추가되지 않았으면 "계정 추가" 단추를 클릭하여 계정을 추가합니다. 이미 추가되었으면 "--저장소 계정 선택--" 드롭다운 목록에서 계정을 선택합니다. ![작업 영역 만들기][1] <br>
3. 저장소 계정 이름 및 저장소 계정 키를 입력한 다음 저장소 계정 추가를 클릭합니다. 저장소 계정을 여러 개 추가할 수 있으며 각 계정은 탭에 표시됩니다. 이 저장소 계정의 컨테이너가 왼쪽 패널에 표시됩니다. 오른쪽 패널에서 컨테이너를 선택하면 해당 컨테이너의 blob를 볼 수 있습니다. ![작업 영역 만들기][2] <br> ![작업 영역 만들기][3] <br>
4. "업로드" 단추를 클릭하여 데이터를 업로드합니다. 파일 시스템에서 업로드할 파일을 하나 이상 선택하고 "열기"를 클릭하여 파일 업로드를 시작합니다.
5. 해당 컨테이너의 blob를 선택하고 "다운로드" 단추를 클릭하여 데이터를 다운로드합니다 .

<a id="AzCopy"></a>
## AzCopy 사용

AzCopy는 데이터를 업로드 및 다운로드하는 명령줄 유틸리티입니다.

**경고** 고급 분석 프로세스 초반에 설정된 가상 컴퓨터와 다른 컴퓨터를 사용할 경우 [AzCopy 다운로드 및 설치](../storage-use-azcopy.md#install)의 설치 지침에 따라 AzCopy를 설치하세요.

###blob에 파일을 업로드/blob의 파일을 다운로드하는 예제:

	# Uploading from local file system
	AzCopy /Source:<your_local_directory> /Dest: https://<your_account_name>.blob.core.windows.net/<your_container_name> /DestKey:<your_account_key> /S 

	# Downloading blobs to local file system
	AzCopy /Source:https://<your_account_name>.blob.core.windows.net/<your_container_name>/<your_sub_directory_at_blob>  /Dest:<your_local_directory> /SourceKey:<your_account_key> /Pattern:<file_pattern> /S

	# Transferring blobs between Azure containers
	AzCopy /Source:https://<your_account_name1>.blob.core.windows.net/<your_container_name1>/<your_sub_directory_at_blob1> /Dest:https://<your_account_name2>.blob.core.windows.net/<your_container_name2>/<your_sub_directory_at_blob2> /SourceKey:<your_account_key1> /DestKey:<your_account_key2> /Pattern:<file_pattern> /S
	
	<your_account_name>: your storage account name
	<your_account_key>: your storage account key
	<your_container_name>: your container name
	<your_sub_directory_at_blob>: the sub directory in the container 
	<your_local_directory>: directory of local file system where files to be uploaded from or the directory of local file system files to be downloaded to
	<file_pattern>: pattern of file names to be transferred. The standard wildcards are supported

> [AZURE.TIP]1. 파일을 업로드하는 경우 /S는 파일을 재귀적으로 업로드합니다. 이 매개 변수가 없으면 하위 디렉터리의 모든 파일이 업로드되지 않습니다. 2. 파일을 다운로드하는 경우 /S는 지정된 디렉터리 및 하위 디렉터리의 모든 파일 또는 제공된 디렉터리 및 하위 디렉터리에 지정된 패턴과 일치하는 모든 파일이 다운로드될 때까지 컨테이너를 재귀적으로 검색합니다. 3. /Source 매개 변수를 사용하여 특정 blob 파일을 다운로드하도록 지정할 수 없습니다. 특정 파일을 다운로드하려면 /Pattern 매개 변수를 사용하여 다운로드할 blob 파일 이름을 지정하세요. /S 매개 변수를 사용하면 AzCopy에서 파일 이름 패턴을 재귀적으로 검색할 수 있습니다. 패턴 매개 변수가 없으면 AzCopy는 해당 디렉터리의 모든 파일을 다운로드합니다.

AzCopy 사용법에 대한 자세한 내용은 [AzCopy 명령줄 유틸리티 시작하기](../storage-use-azcopy.md#install)를 참조하세요.


<a id="PythonSDK"></a>
## Python 사용

Azure SDK에 제공되는 Python API를 사용하여 다음을 수행할 수 있습니다.

- 컨테이너 만들기
- 컨테이너에 Blob 업로드
- Blob 다운로드
- 컨테이너의 Blob 나열
- Blob 삭제

이 섹션에서는 blob를 나열, 업로드 및 다운로드하는 방법을 설명합니다. Python API 사용법에 대한 자세한 내용은 [Python에서 Blob 저장소 서비스를 사용하는 방법](../storage-python-how-to-use-blob-storage.md)을 참조하세요.

> [AZURE.NOTE]고급 분석 프로세스 초반에 설정된 가상 컴퓨터와 다른 컴퓨터를 사용할 경우 [Python Azure SDK](../python-how-to-install.md)를 설치해야만 아래의 샘플 코드를 사용할 수 있습니다.

### Blob에 데이터 업로드

프로그래밍 방식으로 Azure 저장소에 액세스하려는 Python 코드의 맨 위쪽에 다음 코드 조각을 추가합니다.

	from azure.storage import BlobService

**BlobService** 개체를 통해 컨테이너 및 Blob에 대한 작업을 할 수 있습니다. 다음 코드에서는 저장소 계정 이름 및 계정 키를 사용하는 BlobService 개체를 만듭니다. 계정 이름 및 계정 키를 실제 계정 및 키로 바꾸세요.
	
	blob_service = BlobService(account_name="<your_account_name>", account_key="<your_account_key>")

다음 메서드를 사용하여 blob에 데이터를 업로드합니다.
 
1. put_block_blob_from_path(지정된 경로의 파일 콘텐츠 업로드)
2. put_block_blob_from_file(이미 열려 있는 파일/스트림의 콘텐츠 업로드)
3. put_block_blob_from_bytes(바이트 배열 업로드)
4. put_block_blob_from_text(지정된 인코딩을 사용하여 지정된 텍스트 값 업로드)
 
다음은 로컬 파일을 컨테이너에 업로드하는 샘플 코드입니다.
	
	blob_service.put_block_blob_from_path("<your_container_name>", "<your_blob_name>", "<your_local_file_name>")

다음은 로컬 디렉터리의 모든 파일(디렉터리 제외)을 blob 저장소에 업로드하는 샘플 코드입니다.

	from azure.storage import BlobService
	from os import listdir
	from os.path import isfile, join
	
	# Set parameters here
	ACCOUNT_NAME = "<your_account_name>"
	ACCOUNT_KEY = "<your_account_key>"
	CONTAINER_NAME = "<your_container_name>"
	LOCAL_DIRECT = "<your_local_directory>"		
	
	blob_service = BlobService(account_name=ACCOUNT_NAME, account_key=ACCOUNT_KEY)
	# find all files in the LOCAL_DIRECT (excluding directory)
	local_file_list = [f for f in listdir(LOCAL_DIRECT) if isfile(join(LOCAL_DIRECT, f))]
	
	file_num = len(local_file_list)
	for i in range(file_num):
	    local_file = join(LOCAL_DIRECT, local_file_list[i])
	    blob_name = local_file_list[i]
	    try:
	        blob_service.put_block_blob_from_path(CONTAINER_NAME, blob_name, local_file)
	    except:
	        print "something wrong happened when uploading the data %s"%blob_name

### Blob에서 데이터 다운로드

다음 메서드를 사용하여 blob에서 데이터를 다운로드합니다. 1. get_blob_to_path 2. get_blob_to_file 3. get_blob_to_bytes 4. get_blob_to_text

데이터의 크기가 64MB를 초과할 경우 필요한 청크를 수행하는 메서드입니다.

다음은 컨테이너에 있는 blob의 콘텐츠를 로컬 파일에 다운로드하는 샘플 코드입니다.

	blob_service.get_blob_to_path("<your_container_name>", "<your_blob_name>", "<your_local_file_name>")

다음은 컨테이너의 모든 blob를 다운로드하는 샘플 코드입니다. 이 샘플 코드는 list_blobs를 사용하여 컨테이너의 사용 가능한 blob 목록을 가져오고 로컬 디렉터리에 다운로드합니다.

	from azure.storage import BlobService
	from os.path import join
	
	# Set parameters here
	ACCOUNT_NAME = "<your_account_name>"
	ACCOUNT_KEY = "<your_account_key>"
	CONTAINER_NAME = "<your_container_name>"
	LOCAL_DIRECT = "<your_local_directory>"		
	
	blob_service = BlobService(account_name=ACCOUNT_NAME, account_key=ACCOUNT_KEY)
	
	# List all blobs and download them one by one
	blobs = blob_service.list_blobs(CONTAINER_NAME)
	for blob in blobs:
	    local_file = join(LOCAL_DIRECT, blob.name)
	    try:
	        blob_service.get_blob_to_path(CONTAINER_NAME, blob.name, local_file)
	    except:
	        print "something wrong happened when downloading the data %s"%blob.name

<!-- Images -->

[1]: ./media/machine-learning-data-science-move-azure-blob/data-science-process-uploading-data-to-blob-storage-img1.png
[2]: ./media/machine-learning-data-science-move-azure-blob/data-science-process-uploading-data-to-blob-storage-img2.png
[3]: ./media/machine-learning-data-science-move-azure-blob/data-science-process-uploading-data-to-blob-storage-img3.png
 

<!---HONumber=July15_HO1-->