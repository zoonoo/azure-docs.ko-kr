## Azure 저장소 연결된 서비스

**Azure 저장소 연결된 서비스**에서 **계정 키**를 사용하여 Azure 저장소 계정을 Azure Data Factory에 연결할 수 있습니다. 이렇게 하면 데이터 팩터리에 Azure 저장소에 대한 전역 액세스를 제공합니다. 다음 테이블은 Azure 저장소 연결된 서비스에 특정된 JSON 요소에 대한 설명을 제공합니다.

| 속성 | 설명 | 필수 |
| :-------- | :----------- | :-------- |
| type | 형식 속성은 **AzureStorage**로 설정되어야 합니다. | 예 |
| connectionString | connectionString 속성에 대한 Azure 저장소에 연결하는 데 필요한 정보를 지정합니다. | 예 |

Azure 저장소에 대한 계정 키를 보거나 복사하는 단계는 [저장소 액세스 키 보기, 복사 및 다시 생성](../storage/storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys) 문서를 참조하세요.

**예제:**
  
	{  
		"name": "StorageLinkedService",  
		"properties": {  
			"type": "AzureStorage",  
			"typeProperties": {  
				"connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"  
			}  
		}  
	}  


## Azure 저장소 SAS 연결된 서비스  
SAS(공유 액세스 서명)는 저장소 계정의 리소스에 대한 위임된 권한을 제공합니다. 즉, 계정 액세스 키를 공유할 필요 없이 지정된 권한 집합을 사용하여 지정된 기간 동안 클라이언트에게 저장소 계정의 개체에 대한 제한된 권한을 부여할 수 있습니다. SAS는 저장소 리소스에 인증된 액세스를 수행하는 데 필요한 모든 정보가 쿼리 매개 변수에 있는 URI입니다. SAS를 사용하여 저장소 리소스에 액세스하려는 클라이언트는 SAS를 적절한 생성자 또는 메서드에 전달하면 됩니다. SAS에 대한 자세한 내용은 [공유 액세스 서명: SAS 모델 이해](../articles/storage/storage-dotnet-shared-access-signature-part-1.md)를 참조하세요.
  
Azure 저장소 SAS 연결된 서비스에서 SAS(공유 액세스 서명)을 사용하여 Azure 저장소 계정을 Azure Data Factory에 연결할 수 있습니다. 이렇게 하면 데이터 팩터리에 저장소의 모든/특정 리소스(BLOB/컨테이너)에 대한 제한된/시간 범위 액세스를 제공합니다. 다음 표는 Azure 저장소 SAS 연결된 서비스에 특정된 JSON 요소에 대한 설명을 제공합니다.

| 속성 | 설명 | 필수 |
| :-------- | :----------- | :-------- |
| type | 형식 속성은 **AzureStorageSas**로 설정되어야 합니다. | 예 |
| sasUri | BLOB, 컨테이너, 테이블 등의 Azure 저장소 리소스에 공유 액세스 서명 URI를 지정합니다. 자세한 내용은 아래의 참고 사항을 참조하세요. | 예 | 


**예제:**
  
	{  
		"name": "StorageSasLinkedService",  
		"properties": {  
			"type": "AzureStorageSas",  
			"typeProperties": {  
				"sasUri": "<storageUri>?<sasToken>"   
			}  
		}  
	}  

**SAS URI**를 만들 때 다음 사항을 고려하세요.

- Azure Data Factory는 **서비스 SAS**만 지원하며 계정 SAS는 지원하지 않습니다. 이러한 두 가지 유형에 대한 자세한 내용은 [공유 액세스 서명 유형](../articles/storage/storage-dotnet-shared-access-signature-part-1.md#types-of-shared-access-signatures)을 참조하세요.
- 데이터 팩터리에서 연결된 서비스(읽기, 쓰기, 읽기/쓰기)를 사용하는 방식에 따라 개체에 적절한 읽기/쓰기 **권한**을 설정해야 합니다.
- **만료 시간**을 적절하게 설정해야 합니다. Azure 저장소 개체에 대한 액세스가 파이프라인의 활성 기간 내에 만료되지 않아야 합니다.
- 필요에 따라 올바른 컨테이너/BLOB 또는 테이블 수준에서 URI가 만들어져야 합니다. Azure BLOB에 대한 SAS URI를 사용하면 Data Factory 서비스에서 특정 BLOB에 액세스할 수 있습니다. Azure BLOB 컨테이너에 대한 SAS URI를 사용하면 Data Factory 서비스의 해당 컨테이너에서 BLOB을 반복할 수 있습니다. 나중에 더 많은/더 적은 개체에 대한 액세스를 제공하거나 SAS URI를 업데이트해야 하는 경우 연결된 서비스를 새 URI로 업데이트합니다.   

<!---HONumber=AcomDC_0224_2016-->