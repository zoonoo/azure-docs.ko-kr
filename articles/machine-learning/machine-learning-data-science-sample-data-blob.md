<properties 
	pageTitle="Azure Blob 저장소에서 데이터 샘플링 | Azure" 
	description="Azure Blob 저장소에서 데이터 샘플링" 
	services="machine-learning" 
	documentationCenter="" 
	authors="sunliangms,fashah,msolhab" 
	manager="paulettm" 
	editor="cgronlun" />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/18/2015" 
	ms.author="sunliangms,fashah,msolhab,garye" /> 

#<a name="heading"></a>Azure Blob 저장소에서 데이터 샘플링

이 문서에서는 프로그래밍 방식으로 다운로드한 다음 샘플 Python 코드로 샘플링하여 Azure Blob 저장소에 저장된 데이터를 샘플링하는 방법에 대해 알아봅니다. 작업 단계는 다음과 같습니다.

1. 다음 샘플 Python 코드에서 Blob 서비스를 사용하여 Azure Blob 저장소에서 데이터를 다운로드합니다. 

	    from azure.storage import BlobService
    	import tables
    	
		STORAGEACCOUNTNAME= <storage_account_name>
		STORAGEACCOUNTKEY= <storage_account_key>
		LOCALFILENAME= <local_file_name>		
		CONTAINERNAME= <container_name>
		BLOBNAME= <blob_name>

    	#download from blob
    	t1=time.time()
    	blob_service=BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)
    	blob_service.get_blob_to_path(CONTAINERNAME,BLOBNAME,LOCALFILENAME)
    	t2=time.time()
    	print(("It takes %s seconds to download "+blobname) % (t2 - t1))

2. 다운로드한 파일에서 Pandas 데이터 프레임으로 데이터를 읽습니다.

		import pandas as pd

	    #directly ready from file on disk
    	dataframe_blobdata = pd.read_csv(LOCALFILE)

3. 다음과 같이 `numpy`의 `random.choice`를 사용하여 데이터를 샘플링합니다.

	    # A 1 percent sample
    	sample_ratio = 0.01 
    	sample_size = np.round(dataframe_blobdata.shape[0] * sample_ratio)
    	sample_rows = np.random.choice(dataframe_blobdata.index.values, sample_size)
    	dataframe_blobdata_sample = dataframe_blobdata.ix[sample_rows]

	이제 추가 탐색 및 기능 생성을 위해 1% 샘플을 사용하여 위의 데이터 프레임으로 작업할 수 있습니다.

##<a name="heading"></a>Azure 기계 학습에 연결

다음 샘플 코드를 사용하여 데이터를 다운 샘플링하고 Azure 기계 학습에서 직접 사용할 수 있습니다.

1. 데이터 프레임을 로컬 파일에 기록합니다.

		dataframe.to_csv(os.path.join(os.getcwd(),LOCALFILENAME), sep='\t', encoding='utf-8', index=False)

2. 다음 샘플 코드를 사용하여 Azure Blob에 로컬 파일을 업로드합니다.

		from azure.storage import BlobService
    	import tables

		STORAGEACCOUNTNAME= <storage_account_name>
		LOCALFILENAME= <local_file_name>
		STORAGEACCOUNTKEY= <storage_account_key>
		CONTAINERNAME= <container_name>
		BLOBNAME= <blob_name>

	    output_blob_service=BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)    
	    localfileprocessed = os.path.join(os.getcwd(),LOCALFILENAME) #assuming file is in current working directory
	    
	    try:
	   
	    #perform upload
	    output_blob_service.put_block_blob_from_path(CONTAINERNAME,BLOBNAME,localfileprocessed)
	    
	    except:	        
		    print ("Something went wrong with uploading to the blob:"+ BLOBNAME)

3. 아래 화면에 표시된 대로 Azure 기계 학습 *Reader Module*을 사용하여 Azure Blob에서 데이터를 읽습니다.
 
![reader blob][1]

[1]: ./media/machine-learning-data-science-sample-data-blob/reader_blob.png

<!--HONumber=49--> 