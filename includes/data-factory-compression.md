### <a name="compression-support"></a>압축 지원
큰 데이터 집합을 처리하면 I/O 및 네트워크 병목 현상이 발생할 수 있습니다. 따라서 저장소의 압축된 데이터는 네트워크를 통해 데이터 전송의 속도를 높이고 디스크 공간을 절약할 수 없을 뿐만 아니라 빅 데이터 처리에서 상당한 성능 개선을 가져올 수 없습니다. 현재 압축은 Azure Blob 또는 온-프레미스 파일 시스템과 같은 파일 기반 데이터 저장소에 대해 지원됩니다.  

> [!NOTE]
> 현재 **AvroFormat**, **OrcFormat** 또는 **ParquetFormat**의 데이터에 대한 압축 설정은 지원되지 않습니다. 
> 
> 

데이터 집합에 대한 압축을 지정하려면 다음 예제와 같이 데이터 집합 JSON의 **압축** 속성을 사용합니다.   

```json
{  
    "name": "AzureBlobDataSet",  
      "properties": {  
        "availability": {  
            "frequency": "Day",  
              "interval": 1  
        },  
        "type": "AzureBlob",  
        "linkedServiceName": "StorageLinkedService",  
        "typeProperties": {  
            "fileName": "pagecounts.csv.gz",  
              "folderPath": "compression/file/",  
              "compression": {  
                "type": "GZip",  
                "level": "Optimal"  
              }  
        }  
      }  
}  
```

**압축** 섹션에는 두 가지 속성이 있습니다.  

* **유형:** **GZIP**, **Deflate** 또는 **BZIP2**가 될 수 있는 압축 코덱입니다.  
* **수준:** **최적** 또는 **가장 빠름**이 될 수 있는 압축 비율입니다. 
  
  * **가장 빠름:** 결과 파일이 최적으로 압축되지 않은 경우에도 압축 작업을 최대한 빨리 완료해야 합니다. 
  * **최적**: 작업이 완료되는데 시간이 오래 걸리더라도 압축 작업이 최적으로 압축되어야 합니다. 
    
    자세한 내용은 [압축 수준](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx) 항목을 참조하세요. 

위의 샘플 데이터 집합이 복사 작업의 출력으로 사용된다고 가정하면 복사 작업은 최적의 비율을 사용하여 GZIP 코덱으로 출력 데이터를 압축한 다음 압축된 데이터를 Azure Blob Storage에서 pagecounts.csv.gz라는 이름의 파일로 작성합니다.   

입력 데이터 집합 JSON에 압축 속성을 지정하는 경우 파이프라인은 원본에서 압축된 데이터를 읽을 수 있고 출력 데이터 집합 JSON 에서 속성을 지정하는 경우 복사 작업은 대상에 압축된 데이터를 작성할 수 있습니다. 다음은 몇 가지 샘플 시나리오입니다. 

* Azure Blob에서 GZIP 압축 데이터를 읽고 압축을 풀고 Azure SQL 데이터베이스에 결과 데이터를 작성합니다. 이 경우 압축 JSON 속성으로 입력 Azure Blob 데이터 집합을 정의합니다. 
* 온-프레미스 파일 시스템에서 일반 텍스트 파일에서 데이터를 읽고 GZip 형식을 사용하여 압축하고 Azure Blob에 압축된 데이터를 작성합니다. 이 경우 압축 JSON 속성으로 출력 Azure Blob 데이터 집합을 정의합니다.  
* Azure Blob에서 GZIP 압축 데이터를 읽고 압축을 풀고 BZIP2를 사용하여 압축하고 Azure Blob에 결과 데이터를 작성합니다. 이 경우 GZIP으로 설정된 압축 유형으로 입력 Azure Blob 데이터 집합을 정의하고 BZIP2로 설정된 압축 유형으로 출력 데이터 집합을 정의합니다.   



<!--HONumber=Nov16_HO3-->


