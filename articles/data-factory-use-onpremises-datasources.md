<properties 
	pageTitle="파이프라인에서 온-프레미스 데이터를 사용할 수 있도록 설정 | Azure 데이터 팩터리" 
	description="온-프레미스 데이터 원본을 Azure 데이터 팩터리에 등록하고 데이터 원본에 데이터를 복사하거나 데이터 원본에서 데이터를 복사하는 방법에 대해 알아봅니다." 
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="2/10/2015" 
	ms.author="spelluru"/>

# 파이프라인에서 온-프레미스 데이터를 사용할 수 있도록 설정

Azure 데이터 팩터리의 파이프라인에서 온-프레미스 데이터 원본을 사용할 수 있게 하려면 Azure 관리 포털이나 Azure PowerShell을 사용하여 데이터 팩터리에 온-프레미스 데이터 원본을 연결된 서비스로 추가해야 합니다.
 
온-프레미스 데이터 원본을 데이터 팩터리에 연결된 서비스를 추가할 수 있으려면 먼저 온-프레미스 컴퓨터에 Microsoft 데이터 관리 게이트웨이를 다운로드하여 설치하고 온-프레미스 데이터 원본의 연결된 서비스에서 게이트웨이를 사용하도록 구성해야 합니다.
 
> [WACOM.NOTE] 현재 지원되는 온-프레미스 데이터 원본은 SQL Server뿐입니다.

## <a href="DMG"></a> 데이터 관리 게이트웨이

**데이터 관리 게이트웨이**는 온-프레미스 데이터 원본을 클라우드 서비스에 안전하고 관리되는 방식으로 연결하는 소프트웨어입니다. 데이터 관리 게이트웨이를 사용하면 다음을 수행할 수 있습니다.

- **하이브리드 데이터 액세스를 위해 온-프레미스 데이터에 연결** - 온-프레미스 데이터를 클라우드 서비스에 연결하여 클라우드 서비스를 활용하면서도 비즈니스는 계속 온-프레미스 데이터를 사용하여 운영할 수 있습니다.
- **보안 데이터 프록시 정의** - 데이터 관리 게이트웨이에서 표시되는 온-프레미스 데이터 원본을 정의할 수 있으므로 게이트웨이에서 클라우드 서비스의 데이터 요청을 인증하고 온-프레미스 데이터 원본을 보호합니다.
- **게이트웨이 관리를 통해 완전한 거버넌스 실현** - 데이터 관리 게이트웨이 내에서 모든 작업을 완벽히 모니터링 및 로깅하여 관리 및 거버넌스를 향상할 수 있습니다.
- **효율적으로 데이터 이동** - 데이터가 병렬로 전송되며, 자동 재시도 논리를 사용하여 간헐적인 네트워크 문제를 빠르게 복구할 수 있습니다.


데이터 관리 게이트웨이는 다음을 포함하여 다양한 온-프레미스 데이터 연결 기능을 제공합니다.

- **회사 방화벽 비침입** - 데이터 관리 게이트웨이는 설치하면 바로 작동하므로, 방화벽 연결을 열 필요도 없고 회사 네트워크 인프라 변경으로 지장을 주지도 않습니다.
- **사용자의 인증서를 사용하여 자격 증명 암호화** - 데이터 원본에 연결하는 데 사용되는 자격 증명을 사용자가 전유하는 인증서를 사용하여 암호화합니다. 인증서 없이는 Microsoft를 포함하여 누구도 자격 증명을 일반 텍스트로 해독할 수 없습니다.

### 게이트웨이 설치 - 모범 사례

1.	호스트 컴퓨터가 최대 절전 모드인 경우 게이트웨이가 데이터 요청에 응답할 수 없습니다. 따라서 게이트웨이를 설치하기 전에 컴퓨터에서 **전원 계획**을 적절하게 구성하세요. 
2.	데이터 관리 게이트웨이가 온-프레미스 데이터 원본에 연결할 수 있어야만 이 원본이 Azure 데이터 팩터리 서비스에 등록됩니다. 게이트웨이가 데이터 원본과 동일한 컴퓨터에 있지 않아도 되지만, **데이터 원본에 더 가깝게 유지**하면 게이트웨이가 데이터 원본에 연결하는 데 걸리는 시간이 줄어듭니다.

### 데이터 관리 게이트웨이를 사용할 때 고려할 사항
1.	데이터 관리 게이트웨이의 단일 인스턴스를 여러 온-프레미스 데이터 원본에 사용할 수 있지만, **게이트웨이는 Azure 데이터 팩터리에 연결되어 있고** 다른 데이터 팩터리와 공유할 수 없다는 점을 유념하세요.
2.	컴퓨터에 **데이터 관리 게이트웨이 인스턴스를 하나만** 설치할 수 있습니다. 온-프레미스 데이터 원본에 액세스해야 하는 데이터 팩터리가 2개인 경우 두 온-프레미스 컴퓨터에서 게이트웨이를 설치해야 하며, 이 경우 각 게이트웨이는 서로 다른 데이터 팩터리에 연결됩니다.
3.	컴퓨터에 **Power BI** 시나리오를 처리할 게이트웨이가 이미 설치된 경우 **별도의 Azure 데이터 팩터리용 시나리오**를 다른 컴퓨터에 설치하세요. 
 

## 연습

이 연습에서는 온-프레미스 SQL Server 데이터베이스에서 Azure Blob으로 데이터를 이동하는 파이프라인이 포함된 데이터 팩터리를 만듭니다. 

### 1단계: Azure 데이터 팩터리 만들기
이 단계에서는 Azure 관리 포털을 사용하여 **ADFTutorialOnPremDF**라는 Azure 데이터 팩터리 인스턴스를 만듭니다. 또한 Azure 데이터 팩터리 cmdlet을 사용하여 데이터 팩터리를 만들 수도 있습니다. 

1.	[Azure 미리 보기 포털][
2.	azure-preview-portal]에 로그인한 후 왼쪽 맨 아래에서 **새로 만들기**를 클릭하고 **새로 만들기** 블레이드에서 **데이터 팩터리**를 클릭합니다.

	![New->DataFactory][image-data-factory-new-datafactory-menu] 
	
	**새로 만들기** 블레이드에 **데이터 팩터리**가 표시되지 않는 경우 아래로 스크롤합니다.  
6. **새 데이터 팩터리** 블레이드에서 다음을 수행합니다.
	1. **ADFTutorialOnPremDF**를 **이름**에 입력합니다.
	2. **리소스 그룹 이름**을 클릭하고 **ADFTutorialResourceGroup**을 선택합니다([Azure 데이터 팩터리 시작][adfgetstarted]의 자습서를 수행한 경우). 기존 리소스 그룹을 선택하거나 리소스 그룹을 새로 만들 수 있습니다. 새 리소스 그룹을 만들려면 다음을 수행합니다.
		1. **새 리소스 그룹 만들기**를 클릭합니다.
		2. **리소스 그룹 만들기 블레이드**에서 리소스 그룹의 **이름**을 입력하고 **확인**을 클릭합니다.

7. **새 데이터 팩터리** 블레이드에서는 **Add to Startboard**가 선택되어 있습니다.

	![Add to Startboard][image-data-factory-add-to-startboard]

8. **새 데이터 팩터리** 블레이드에서 **만들기**를 클릭합니다.
9. 왼쪽의 **알림** 허브에서 만들기 프로세스에서 보낸 알림을 찾습니다.

	![NOTIFICATIONS hub][image-data-factory-notifications-hub]

11. 만들기가 완료되면 아래와 같이 데이터 팩터리 블레이드가 표시됩니다.

	![Data Factory Home Page][image-data-factory-datafactory-homepage]

12. 아래와 같이 **시작 보드**에도 표시됩니다. **데이터 팩터리 블레이드**가 아직 열려 있지 않은 경우 클릭하여 엽니다.

	![Startboard][image-data-factory-startboard]

### 2단계: 연결된 서비스 만들기 
이 단계에서는 2개의 연결된 서비스 **MyBlobStore** 및 **OnPremSqlLinkedService**를 만듭니다. **OnPremSqlLinkedService**는 온-프레미스 SQL Server 데이터베이스를 연결하고 **MyBlobStore** 연결 서비스는 Azure Blob 저장소를 **ADFTutorialDataFactory**에 연결합니다. 이 연습의 뒷부분에서는 온-프레미스 SQL Server 데이터베이스에서 Azure Blob 저장소로 데이터를 복사하는 파이프라인을 만듭니다. 

### 온-프레미스 SQL Server 데이터베이스에 연결된 서비스 추가
1.	**ADFTutorialOnPremDF**의 **데이터 팩터리** 블레이드에서 **연결된 서비스**를 클릭합니다. 

	![Data Factory Home Page][image-data-factory-home-age]

2.	**연결된 서비스** 블레이드에서 **+ Data gateway**를 클릭합니다.

	![Linked Services - Add a Gateway button][image-data-factory-linkedservices-add-gateway-button]

2. **만들기** 블레이드에서 **adftutorialgateway**를 **이름**에 입력하고 **확인**을 클릭합니다. 	

	![Create Gateway blade][image-data-factory-create-gateway-blade]

3. **구성** 블레이드에서 **Install directly on this computer**를 클릭합니다. 이렇게 하면 컴퓨터에 게이트웨이의 설치 패키지가 다운로드되고 게이트웨이가 설치, 구성 및 등록됩니다.

	![Gateway - Configure blade][image-data-factory-gateway-configure-blade]

	이는 하나의 단계로 게이트웨이를 다운로드, 설치, 구성 및 등록하는 가장 쉬운 방법(한 번 클릭)입니다. 컴퓨터에 설치된 **Microsoft 데이터 관리 게이트웨이 구성 관리자** 응용 프로그램을 확인할 수 있습니다. 실행 파일 **ConfigManager.exe**를 **C:\Program Files\Microsoft Data Management Gateway\1.0\Shared** 폴더에서 찾을 수도 있습니다.

	또한 이 블레이드에서 링크를 클릭하여 게이트웨이를 수동으로 다운로드하여 설치하고 **REGISTER WITH KEY** 텍스트 상자에 표시된 키를 사용하여 등록할 수도 있습니다.
	
	모범 사례, 중요한 고려 사항 등을 포함하여 게이트웨이에 대한 자세한 내용은 [데이터 관리 게이트웨이](#DMG) 섹션을 참조하세요. 

4. **새 데이터 게이트웨이** 블레이드에서 **확인**을 클릭합니다.
5. 컴퓨터에서 **Microsoft 데이터 관리 게이트웨이 구성 관리자** 응용 프로그램을   시작합니다.

	![Gateway Configuration Manager][image-data-factory-gateway-configuration-manager]

6. 값이 다음과 같이 설정될 때까지 기다립니다.
	1. 서비스 **상태**가 **Started**로 설정되지 않은 경우 **서비스 시작**을 클릭하여 서비스를 시작하고 다른 필드가 새로 고쳐질 때까지 잠시 기다립니다.
	2. **게이트웨이 이름**이 **adftutorialgateway**로 설정됩니다.
	3. **인스턴스 이름**이 **adftutorialgateway**로 설정됩니다.
	4. **Gateway key status**가 **Registered**로 설정됩니다.
	5. 맨 아래 상태 표시줄에 **Connected to Data Management Gateway Cloud Service**와 함께 **녹색 체크 표시**가 표시됩니다.  

7. **연결된 서비스** 블레이드에서 게이트웨이 **상태**가 **Good**인지 확인하고 **+ Data store**를 클릭합니다. 블레이드를 닫았다가 다시 열어 블레이드를 새로 고쳐야 합니다. 

	![Add Data Store button][image-data-factory-add-datastore-SQL-button]

8. **새 데이터 저장소** 블레이드에서 **OnPremSqlLinkedService**를 **이름**에 입력합니다.
9. **TYPE (Settings required)**을 클릭하고 **SQL Server**를 선택합니다. 
10. **새 데이터 저장소** 블레이드에서 **DATA GATEWAY (Configure required settings)**를 클릭합니다.

	![Data Gateway Configure link][image-data-factory-gateway-configure-link]
  
11. 앞에서 만든 **adftutorialgateway**를 선택합니다. 
12.	**새 데이터 저장소** 블레이드에서 **자격 증명**을 클릭하여 **자격 증명** 블레이드를 표시합니다.

	![Data Source Credentials Blade][image-data-factory-credentials-blade]

13.	**자격 증명** 블레이드에서 **Click here to set Credentials securely**를 클릭합니다. 다음 팝업 대화 상자가 시작됩니다.

	![One Click application install][image-data-factory-oneclick-install]

14. **실행**을 클릭하여 **자격 증명 관리자** 응용 프로그램을 설치합니다. Setting Credentials 대화 상자가 표시됩니다. 
15.	**Setting Credentials** 대화 상자에서 온-프레미스 SQL Server데이터베이스에 액세스할 때 서비스에서 사용할 수 있는 **사용자 이름** 및 **암호**를 입력하고 **확인**을 클릭합니다. **SQL 인증**만 이 대화 상자에서 지원됩니다. 대화 상자가 닫힐 때까지 기다립니다.
16.	**자격 증명** 블레이드에서 **확인**을 클릭하고 **새 데이터 저장소** 블레이드에서 **확인**을 클릭합니다. 연결된 서비스 블레이드에서 추가한 **OnPremSqlLinkedService** 연결된 서비스가 표시됩니다.

	![Linked Services Blade with OnPrem Store][image-data-factory-linkedservices-blade-onprem]

	
   


#### Azure 저장소 계정에 대한 연결된 서비스 추가

1.	**데이터 팩터리** 블레이드에서 **연결된 서비스** 타일을 클릭하여 **연결된 서비스** 블레이드를 시작합니다.
2. **연결된 서비스** 블레이드에서 **데이터 저장소 추가**를 클릭합니다.	
3. **새 데이터 저장소** 블레이드에서 다음을 수행합니다.
	1. 데이터 저장소의 **이름**을 입력합니다. 이 자습서에서는 **MyBlobStore**를 **이름**에 입력합니다.
	2. 데이터 저장소에 대한 **설명(선택 사항)**을 입력합니다.
	3. **유형**을 클릭합니다.
	4. **Azure 저장소 계정**을 선택하고 **확인**을 클릭합니다.
		
		![Azure Storage Account][image-data-factory-azure-storage-account]

4. 이제 아래와 같은 **새 데이터 저장소** 블레이드로 돌아옵니다.

	![Azure Storage settings][image-data-factory-azure-storage-settings]

5.  Azure 저장소 계정의 **계정 이름** 및 **계정 키**를 입력하고 **확인**을 클릭합니다.

  
6. 새 데이터 저장소** 블레이드에서 **확인**을 클릭하면 연결된 서비스**의 **데이터 저장소** 목록에 ****MyBlobStore****가 표시되어야 합니다. 왼쪽의 **알림** 허브에 메시지가 있는지 확인합니다.

	![Linked Services blade with MyBlobStore][image-data-factory-linkedservices-with-storage]

 
## 3단계: 입력 및 출력 데이터 집합 만들기
이 단계에서는 복사 작업(온-프레미스 SQL Server 데이터베이스 = > Azure Blob 저장소)의 입력 및 출력 데이터를 나타내는 입력 및 출력 데이터 집합을 만듭니다. 아직 Azure 포털에서는 데이터 집합 및 파이프라인을 만들 수 없으므로 Azure PowerShell cmdlet을 사용하여 테이블과 파이프라인을 만듭니다. 데이터 집합 또는 테이블(사각형 데이터 집합)을 만들려면 먼저 다음을 수행해야 합니다(자세한 단계는 목록 다음에 나옴).

- 데이터 팩터리에 연결된 서비스로 추가한 SQL Server 데이터베이스에서 **emp**라는 테이블을 만들고 테이블에 몇 가지 샘플 항목을 삽입합니다.
- - [Azure 데이터 팩터리 시작][adfgetstarted] 문서의 자습서를 마치지 않은 경우 데이터 팩터리에 연결된 서비스로 추가한 Azure Blob 저장소 계정에 **adftutorial**이라는 Blob 컨테이너를 만듭니다.

### 자습서에 필요한 온-프레미스 SQL Server 준비

1. 온-프레미스 SQL Server 연결된 서비스(**OnPremSqlLinkedService**)에 대해 지정한 데이터베이스에서 다음 SQL 스크립트를 사용하여 데이터베이스에 **emp** 테이블을 만듭니다.


        CREATE TABLE dbo.emp
		(
			ID int IDENTITY(1,1) NOT NULL, 
			FirstName varchar(50),
			LastName varchar(50),
    		CONSTRAINT PK_emp PRIMARY KEY (ID)
		)
		GO
 

2. 테이블에 일부 샘플을 삽입합니다. 


        INSERT INTO emp VALUES ('John', 'Doe')
		INSERT INTO emp VALUES ('Jane', 'Doe')



### 입력 테이블 만들기

1.	SQL Server 데이터베이스의 **emp** 테이블에 있는 데이터를 나타내는 데이터 팩터리 테이블에 대한 JSON 파일을 만듭니다. **메모장**을 시작하고 다음 JSON 스크립트를 복사하여 **EmpOnPremSQLTable.json**으로 C:\ADFGetStarted\**OnPrem** 폴더에 저장합니다. **C:\ADFGetStarted** 폴더에 **OnPrem** 하위 폴더가 없으면 만듭니다. 


        {
    		"name": "EmpOnPremSQLTable",
    		"properties":
    		{
        		"location":
        		{
            		"type": "OnPremisesSqlServerTableLocation",
            		"tableName": "emp",
            		"linkedServiceName": "OnPremSqlLinkedService"
        		},
        		"availability": 
        		{
            		"frequency": "Hour",
            		"interval": 1,       
	    			"waitOnExternal":
	    			{
        				"retryInterval": "00:01:00",
	        			"retryTimeout": "00:10:00",
	        			"maximumRetry": 3
	    			}
		  
        		}
    		}
		}

	다음 사항에 유의하세요. 
	
	- location **type**을 **OnPremisesSqlServerTableLocation**으로 설정합니다.
	- **tableName**을 **emp**로 설정합니다.
	- **linkedServiceName**을 **OnPremSqlLinkedService**(2단계에서 만든 연결된 서비스)로 설정합니다.
	- Azure 데이터 팩터리의 다른 파이프라인에서 생성되지 않는 입력 테이블의 경우 JSON에서 **waitOnExternal** 섹션을 지정해야 합니다. 이 섹션은 입력 데이터가 Azure 데이터 팩터리 서비스 외부에서 생성되었음을 나타냅니다.   

	JSON 속성에 대한 자세한 내용은 [JSON 스크립트 참조][json-script-reference](영문)를 참조하세요.

2. Azure 데이터 팩터리 cmdlet은 **AzureResourceManager** 모드에서 사용할 수 있습니다. **Azure PowerShell**을 시작하고 다음 명령을 실행하여 **AzureResourceManager** 모드로 전환합니다.     

        switch-azuremode AzureResourceManager

	[Azure PowerShell][azure-powershell-install]을 컴퓨터에 설치하지 않은 경우 다운로드합니다.
3. **New-AzureDataFactoryTable** cmdlet을 사용하여 **EmpOnPremSQLTable.json** 파일로 입력 테이블을 만듭니다.

		New-AzureDataFactoryTable -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialOnPremDF -File C:\ADFGetStarted\OnPrem\EmpOnPremSQLTable.json  
	
	다른 리소스 그룹을 사용하는 경우 명령을 업데이트합니다.

### 출력 테이블 만들기

1.	다음 단계에서 만들 파이프라인의 출력으로 사용할 데이터 팩터리 테이블에 대한 JSON 파일을 만듭니다. 메모장을 시작하고 다음 JSON 스크립트를 복사하여**OutputBlobTable.json**으로 **C:\ADFGetStarted\OnPrem** 폴더에 저장합니다.

		{
    		"name": "OutputBlobTable",
    		"properties":
    		{
        		"location": 
        		{
            		"type": "AzureBlobLocation",
            		"folderPath": "adftutorial/outfromonpremdf",
            		"format":
            		{
                		"type": "TextFormat",
                		"columnDelimiter": ","
            		},
            		"linkedServiceName": "MyBlobStore"
        		},
        		"availability": 
        		{
            		"frequency": "Hour",
            		"interval": 1
        		}
    		}
		}
  
	다음 사항에 유의하세요. 
	
	- location **type**은 **AzureBlobLocation**으로 설정합니다.
	- **linkedServiceName**을 **MyBlobStore**(2단계에서 만든 연결된 서비스)로 설정합니다.
	- **folderPath**를 **adftutorial/outfromonpremdf**로 설정합니다. 여기서 outfromonpremdf는 adftutorial 컨테이너의 폴더입니다. **adftutorial** 컨테이너만 만들면 됩니다.
	- **availability**를 **hourly**(**frequency**는 **hour**, **interval**은 **1**로 설정)로 설정합니다.  The Data Factory service will generate an output data slice every hour in the **emp** table in the Azure SQL Database. 

	입력 테이블**의 **fileName**을 지정하지 않는 경우** 입력 폴더(**folderPath**)의 모든 파일/Blob이 입력으로 간주됩니다. JSON에서 fileName을 지정하는 경우에는 지정한 파일/Blob만 입력으로 간주됩니다. 예제는 [tutorial][adf-tutorial]의 샘플 파일을 참조하세요.
 
	출력 테이블**의 **fileName**을 지정하지 않는 경우** **folderPath**에 생성되는 파일은 Data.<GUID>.txt 형식으로 이름이 지정됩니다(예: : Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.).

	**folderPath** 및 **fileName**을 **SliceStart** 시간을 기준으로 동적으로 설정하려면 partitionedBy 속성을 사용합니다. 다음 예제에서 folderPath는 SliceStart(처리 중인 조각의 시작 시간)의 Year, Month 및 Day를 사용하고 fileName은 SliceStart의 Hour를 사용합니다. 예를 들어 조각이 2014-10-20T08:00:00에 생성되는 경우 folderName은 wikidatagateway/wikisampledataout/2014/10/20으로 설정되고 fileName은 08.csv로 설정됩니다. 

	  	"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
        "fileName": "{Hour}.csv",
        "partitionedBy": 
        [
        	{ "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
            { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } }, 
            { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } }, 
            { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } } 
        ],

 

	JSON 속성에 대한 자세한 내용은 [JSON 스크립트 참조][json-script-reference](영문)를 참조하세요.

2.	**Azure PowerShell**에서 다음 명령을 실행하여 Azure SQL 데이터베이스의 테이블을 나타내는 다른 데이터 팩터리 테이블을 만듭니다.

		New-AzureDataFactoryTable -DataFactoryName ADFTutorialOnPremDF -File C:\ADFGetStarted\OnPrem\OutputBlobTable.json -ResourceGroupName ADFTutorialResourceGroup  

## 4단계: 파이프라인 만들기 및 실행
이 단계에서는 **EmpOnPremSQLTable**을 입력으로, **OutputBlobTable**을 출력으로 사용하는 **복사 작업**을 포함하는 **파이프라인**을 만듭니다.

1.	파이프라인에 대한 JSON 파일을 만듭니다. 메모장을 시작하고 다음 JSON 스크립트를 복사하여**ADFTutorialPipelineOnPrem.json**으로 **C:\ADFGetStarted\OnPrem** 폴더에 저장합니다.
	 

        {
			"name": "ADFTutorialPipelineOnPrem",
    		"properties":
    		{
        		"description" : "This pipeline has one Copy activity that copies data from an on-prem SQL to Azure blob",
	       		 "activities":
	        	[
			    	{
						"name": "CopyFromSQLtoBlob",
						"description": "Copy data from on-prem SQL server to blob",		
						"type": "CopyActivity",
						"inputs": [ {"name": "EmpOnPremSQLTable"} ],
						"outputs": [ {"name": "OutputBlobTable"} ],
						"transformation":
						{
							"source":
							{                               
								"type": "SqlSource",
								"sqlReaderQuery": "select * from emp"
							},
							"sink":
							{
								"type": "BlobSink"
							}	
						},
						"Policy":
						{
							"concurrency": 1,
							"executionPriorityOrder": "NewestFirst",
							"style": "StartOfInterval",
							"retry": 0,
							"timeout": "01:00:00"
						}		

				     }
	        	]
			}
		}

	다음 사항에 유의하세요.
 
	- activities 섹션에는 **type**이 **CopyActivity**로 설정된 작업만 있습니다.
	- **Input**은 **EmpOnPremSQLTable**로 설정되고 **output**은 **OutputBlobTable**로 설정됩니다.
	- **transformation** 섹션에서 **SqlSource**가 **source type**으로 지정되고 **BlobSink **가 **sink type**으로 지정됩니다.
	- SQL 쿼리 **select * from emp**가 **SqlSource**의 **sqlReaderQuery** 속성으로 지정됩니다.

2. **New-AzureDataFactoryPipeline** cmdlet을 사용하여 앞에서 만든 **ADFTutorialPipeline.json** 파일로 파이프라인을 만듭니다.

		New-AzureDataFactoryPipeline  -DataFactoryName ADFTutorialOnPremDF -File C:\ADFGetStarted\OnPrem\ADFTutorialPipelineOnPrem.json -ResourceGroupName ADFTutorialResourceGroup  

3. 파이프라인이 만들어지면 데이터 처리가 수행되는 기간을 지정할 수 있습니다. 파이프라인의 활성 기간을 지정하면 각 Azure 데이터 팩터리 테이블에 대해 정의된 **가용성** 속성을 기준으로 데이터 조각이 처리되는 기간이 정의됩니다.  다음 PowerShell 명령을 실행하여 파이프라인에 대한 활성 기간을 설정하고 Y를 입력하여 확인합니다. 


		Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialOnPremDF -StartDateTime 2014-09-29 -EndDateTime 2014-09-30 -Name ADFTutorialPipelineOnPrem  

	> [WACOM.NOTE] **StartDateTime** 값을 현재 날짜로, **EndDateTime** 값을 다음 날짜로 바꿉니다. StartDateTime 및 EndDateTime은 UTC 시간이며 [ISO 형식](http://en.wikipedia.org/wiki/ISO_8601)이어야 합니다. 예를 들면 다음과 같습니다. 2014-10-14T16:32:41Z.EndDateTime은 선택 사항이지만 이 자습서에서는 사용합니다.
	> **EndDateTime**을 지정하지 않는 경우 "**StartDateTime + 48시간**"으로 계산됩니다. 파이프라인을 무기한 실행하려면 **9/9/9999**를 **EndDateTime**으로 지정합니다.

	위의 예에서는 각 데이터 조각이 1시간마다 생성되므로 24개 데이터 조각이 있게 됩니다.
4. **Azure 미리 보기 포털**에서 **ADFTutorialOnPremDF** 데이터 팩터리의 홈페이지에서 **다이어그램** 타일을 클릭합니다. :

	![Diagram Link][image-data-factory-diagram-link]

5. 다음과 유사한 다이어그램이 표시됩니다.

	![Diagram View][image-data-factory-diagram-view]

	**축하합니다.** Azure 데이터 팩터리, 연결된 서비스, 테이블 및  	파이프라인을 만들었고 워크플로를 시작했습니다.

## 5단계: 데이터 집합 및 파이프라인 모니터링
이 단계에서는 Azure 포털을 사용하여 Azure 데이터 팩터리에서 어떤 일이 일어나는지 모니터링합니다. 또한 PowerShell cmdlet을 사용하여 데이터 집합과 파이프라인을 모니터링할 수도 있습니다. Cmdlet을 사용하여 모니터링하는 방법에 대한 자세한 내용은 [PowerShell을 사용하여 Azure 데이터 팩터리 모니터링 및 관리][monitor-manage-powershell]를 참조하세요.

1. 닫혀 있는 경우 **Azure 미리 보기 포털**로 이동합니다.
2. **ADFTutorialOnPremDF**에 대한 블레이드가 열려 있지 않은 경우 **시작 보드**에서 **ADFTutorialOnPremDF**를 클릭하여 엽니다.
3. 만든 테이블과 파이프라인의 **개수** 및 **이름**이 이 블레이드에 표시됩니다.

	![Data Factory Home Page][image-data-factory-homepage-2]
4. 이제 **데이터 집합** 타일을 클릭합니다.
5. **데이터 집합** 블레이드에서 **EmpOnPremSQLTable**을 클릭합니다.

	![EmpOnPremSQLTable slices][image-data-factory-onprem-sqltable-slices]

6. 현재 시간까지의 데이터 조각이 이미 생성되어 **Ready** 상태입니다. SQL Server 데이터베이스에 데이터를 삽입했기 때문이며 데이터가 데이터베이스에 항상 있습니다. 맨 아래의 **Problem slices** 섹션에 조각이 표시된 조각이 없는지 확인합니다.
7. 이제 **데이터 집합** 블레이드에서 **OutputBlobTable**을 클릭합니다.

	![OputputBlobTable slices][image-data-factory-output-blobtable-slices]
8. 현재 시간까지 조각이 생성되었고 **Ready** 상태인지 확인합니다.
9. 목록에서 아무 데이터 조각이나 클릭하면 **데이터 조각** 블레이드가 표시됩니다.

	![Data Slice Blade][image-data-factory-dataslice-blade]
10. 아래쪽 목록에서 **activity run**을 클릭하여 **activity run details**를 표시합니다.

	![Activity Run Details blade][image-data-factory-activity-run-details]

11. 모든 블레이드가 닫힐 때까지 **X**를 클릭하여 **ADFTutorialOnPremDF**의 홈 블레이드로 돌아옵니다.
14. (선택 사항) **파이프라인**을 클릭하고 **ADFTutorialOnPremDF**을 클릭하고 입력 테이블(**Consumed**) 또는 출력 테이블(**Produced**)을 드릴스루합니다.
15. **Azure 저장소 탐색기**와 같은 도구를 사용하여 출력을 확인합니다.

	![Azure Storage Explorer][image-data-factory-stroage-explorer]


## Azure PowerShell cmdlet을 사용하여 게이트웨이 만들기 및 등록
이 섹션에서는 Azure PowerShell cmdlet을 사용하여 게이트웨이를 만들고 등록하는 방법에 대해 설명합니다. 

1. **Azure PowerShell**을 관리자 모드로 실행합니다. 
2. Azure 데이터 팩터리 cmdlet은 **AzureResourceManager** 모드에서 사용할 수 있습니다. 다음 명령을 실행하여 **AzureResourceManager** 모드로 전환합니다.     

        switch-azuremode AzureResourceManager


2. 다음과 같이 **New-AzureDataFactoryGateway** cmdlet을 사용하여 논리 게이트웨이를 만듭니다.

		New-AzureDataFactoryGateway -Name <gatewayName> -DataFactoryName $df -Location "West US" -ResourceGroupName ADF -Description <desc>

	**예제 명령 및 출력**:


		PS C:\> New-AzureDataFactoryGateway -Name MyGateway -DataFactoryName $df -Location "West US" -ResourceGroupName ADF -Description "gateway for walkthrough"

		Name            : MyGateway
		Location        : West US
		Description     : gateway for walkthrough
		Version         :
		Status          : NeedRegistration
		VersionStatus   : None
		CreateTime      : 9/28/2014 10:58:22
		RegisterTime    :
		LastConnectTime :
		ExpiryTime      :


3. **New-AzureDataFactoryGatewayKey** cmdlet을 사용하여 새로 만든 게이트웨이의 등록 키를 생성하고 이 키를 로컬 변수 **$Key**에 저장합니다.

		New-AzureDataFactoryGatewayKey -GatewayName <gatewayname> -ResourceGroupName ADF -DataFactoryName $df 

	
	**예제 명령 출력:**


		PS C:\> $Key = New-AzureDataFactoryGatewayKey -GatewayName MyGateway -ResourceGroupName ADF -DataFactoryName $df 

	
4. Azure PowerShell에서 **C:\Program Files\Microsoft Data Management Gateway\1.0\PowerShellScript\** 폴더로 전환하고 다음 명령에 표시된 대로 로컬 변수 **$Key**와 연결된 **RegisterGateway.ps1** 스크립트를 실행하여 컴퓨터에 설치된 클라이언트 에이전트를 앞에서 만든 논리 게이트웨이에 등록합니다.

		PS C:\> .\RegisterGateway.ps1 $Key.GatewayKey
		
		Agent registration is successful!

5. **Get-AzureDataFactoryGateway** cmdlet을 사용하여 데이터 팩터리의 게이트웨이 목록을 가져올 수 있습니다. **Status**에 **online**이 표시되어 있으면 게이트웨이를 사용할 준비가 되었음을 나타냅니다.

		Get-AzureDataFactoryGateway -DataFactoryName $df -ResourceGroupName ADF

게이트웨이를 제거하려면 **Remove-AzureDataFactoryGateway** cmdlet을 사용하고 게이트웨이 설명을 업데이트하려면 **Set-AzureDataFactoryGateway** cmdlet을 사용합니다. 이러한 cmdlet에 대한 구문 및 기타 세부 정보는 데이터 팩터리 Cmdlet 참조를 참조하세요.  




## 참고 항목

문서 | 설명
------ | ---------------
[Azure 데이터 팩터리 시작][adf-getstarted] | 이 문서에서는 Azure Blob에서 Azure SQL 데이터베이스로 데이터를 복사하는 샘플 Azure 데이터 팩터리를 만드는 방법을 보여 주는 완전한 자습서를 제공합니다.
[데이터 팩터리에서 Pig 및 Hive 사용][use-pig-and-hive-with-data-factory] | 이 문서의 연습에서는 HDInsight 작업을 사용하여 hive/pig 스크립트로 입력 데이터를 처리하고 출력 데이터를 생성하는 방법을 보여 줍니다. 
[자습서: 데이터 팩터리를 사용하여 로그 파일 이동 및 처리][adf-tutorial] | 이 문서에서는 Azure 데이터 팩터리를 사용하는 실제 시나리오를 구현하여 로그 파일의 데이터에서 통찰력을 얻는 방법을 보여 주는 완전한 연습을 제공합니다.
[데이터 팩터리에서 사용자 지정 작업 사용][use-custom-activities] | 이 문서에서는 사용자 지정 작업을 만들어 파이프라인에서 사용하는 방법에 대한 단계별 지침이 포함된 연습을 제공합니다. 
[데이터 팩터리 문제 해결][troubleshoot] | 이 문서에서는 Azure 데이터 팩터리 문제를 해결하는 방법에 대해 설명합니다.  
[Azure 데이터 팩터리 개발자 참조][developer-reference] | 개발자 참조에는 cmdlet, JSON 스크립트, 함수 등에 대한 포괄적인 참조 콘텐츠가 포함되어 있습니다. 



[monitor-manage-using-powershell]: ../data-factory-monitor-manage-using-powershell
[adf-getstarted]: ../data-factory-get-started
[adf-tutorial]: ../data-factory-tutorial
[use-custom-activities]: ../data-factory-use-custom-activities
[use-pig-and-hive-with-data-factory]: ../data-factory-pig-hive-activities
[troubleshoot]: ../data-factory-troubleshoot
[data-factory-introduction]: ../data-factory-introduction

[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456


[64bit-download-link]: http://go.microsoft.com/fwlink/?LinkId=517623
[32bit-download-link]: http://go.microsoft.com/fwlink/?LinkId=517624

[azure-preview-portal]: http://portal.azure.com
[adfgetstarted]: ../data-factory-get-started
[monitor-manage-powershell]: ../data-factory-monitor-manage-using-powershell





[json-script-reference]: http://go.microsoft.com/fwlink/?LinkId=516971
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456



[azure-powershell-install]: http://azure.microsoft.com/documentation/articles/install-configure-powershell/


[image-data-factory-onprem-new-everything]: ./media/data-factory-use-onpremises-datasources/OnPremNewEverything.png

[image-data-factory-onprem-datastorage-cache-backup]: ./media/data-factory-use-onpremises-datasources/OnPremDataStorageCacheBackup.png

[image-data-factory-onprem-datastorage-see-all]: ./media/data-factory-use-onpremises-datasources/OnPremDataStorageSeeAll.png

[image-data-factory-onprem-dataservices-blade]: ./media/data-factory-use-onpremises-datasources/OnPremDataServicesBlade.png

[image-data-factory-onprem-datafactory-preview-blade]: ./media/data-factory-use-onpremises-datasources/DataFactoryPreviewBlade.png

[image-data-factory-onprem-create-resource-group]: ./media/data-factory-use-onpremises-datasources/OnPremCreateResourceGroup.png

[image-data-factory-add-to-startboard]: ./media/data-factory-use-onpremises-datasources/OnPremNewDataFactoryAddToStartboard.png

[image-data-factory-notifications-hub]: ./media/data-factory-use-onpremises-datasources/OnPremNotificationsHub.png

[image-data-factory-datafactory-homepage]: ./media/data-factory-use-onpremises-datasources/OnPremDataFactoryHomePage.png

[image-data-factory-startboard]: ./media/data-factory-use-onpremises-datasources/OnPremStartboard.png

[image-data-factory-linkedservices-blade]: ./media/data-factory-use-onpremises-datasources/OnPremLinkedServicesBlade.png

[image-data-factory-linkedservices-add-datastore-button]: ./media/data-factory-use-onpremises-datasources/ONPremLinkedServicesAddDataStoreButton.png

[image-data-factory-new-datastore-blade]: ./media/data-factory-use-onpremises-datasources/OnPremNewDataStoreBlade.png

[image-data-factory-azure-storage-account]: ./media/data-factory-use-onpremises-datasources/OnPremAzureStorageAccount.png

[image-data-factory-azure-storage-settings]: ./media/data-factory-use-onpremises-datasources/OnPremAzureStorageSettings.png

[image-data-factory-get-storage-key]: ./media/data-factory-use-onpremises-datasources/OnPremGetStorageKey.png

[image-data-factory-linkedservices-with-storage]: ./media/data-factory-use-onpremises-datasources/OnPremLinkedServicesWithMyBlobStore.png

[image-data-factory-linkedservices-add-gateway-button]: ./media/data-factory-use-onpremises-datasources/OnPremLinkedServicesAddGaewayButton.png

[image-data-factory-create-gateway-blade]: ./media/data-factory-use-onpremises-datasources/OnPremCreateGatewayBlade.png

[image-data-factory-gateway-configure-blade]: ./media/data-factory-use-onpremises-datasources/OnPremGatewayConfigureBlade.png

[image-data-factory-gateway-configuration-manager]: ./media/data-factory-use-onpremises-datasources/OnPremDMGConfigurationManager.png

[image-data-factory-add-datastore-SQL-button]: ./media/data-factory-use-onpremises-datasources/OnPremLinkedServicesAddSQLDataStoreButton.png

[image-data-factory-gateway-configure-link]: ./media/data-factory-use-onpremises-datasources/OnPremNewDataStoreDataGatewayConfigureLink.png

[image-data-factory-credentials-blade]: ./media/data-factory-use-onpremises-datasources/OnPremCredentionlsBlade.png

[image-data-factory-oneclick-install]: ./media/data-factory-use-onpremises-datasources/OnPremOneClickInstall.png

[image-data-factory-diagram-link]: ./media/data-factory-use-onpremises-datasources/OnPremDiagramLink.png

[image-data-factory-diagram-view]: ./media/data-factory-use-onpremises-datasources/OnPremDiagramView.png

[image-data-factory-homepage-2]: ./media/data-factory-use-onpremises-datasources/OnPremDataFactoryHomePage2.png

[image-data-factory-stroage-explorer]: ./media/data-factory-use-onpremises-datasources/OnPremAzureStorageExplorer.png

[image-data-factory-home-age]: ./media/data-factory-use-onpremises-datasources/DataFactoryHomePage.png

[image-data-factory-linkedservices-blade-onprem]: ./media/data-factory-use-onpremises-datasources/LinkedServiceBladeWithOnPremSql.png

[image-data-factory-onprem-sqltable-slices]: ./media/data-factory-use-onpremises-datasources/OnPremSQLTableSlicesBlade.png

[image-data-factory-output-blobtable-slices]: ./media/data-factory-use-onpremises-datasources/OutputBlobTableSlicesBlade.png

[image-data-factory-dataslice-blade]: ./media/data-factory-use-onpremises-datasources/DataSlice.png

[image-data-factory-activity-run-details]: ./media/data-factory-use-onpremises-datasources/ActivityRunDetailsBlade.png

[image-data-factory-new-datafactory-menu]: ./media/data-factory-use-onpremises-datasources/NewDataFactoryMenu.png

[image-data-factory-preview-portal-storage-key]: ./media/data-factory-get-started/PreviewPortalStorageKey.png

<!--HONumber=46--> 
