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
	ms.date="07/10/2015" 
	ms.author="spelluru"/>

# 파이프라인에서 온-프레미스 데이터를 사용할 수 있도록 설정

Azure 데이터 팩터리의 파이프라인에서 온-프레미스 데이터 원본을 사용할 수 있게 하려면 Azure 관리 포털이나 Azure PowerShell을 사용하여 데이터 팩터리에 온-프레미스 데이터 원본을 연결된 서비스로 추가해야 합니다.
 
온-프레미스 데이터 원본을 데이터 팩터리에 연결된 서비스를 추가할 수 있으려면 먼저 온-프레미스 컴퓨터에 Microsoft 데이터 관리 게이트웨이를 다운로드하여 설치하고 온-프레미스 데이터 원본의 연결된 서비스에서 게이트웨이를 사용하도록 구성해야 합니다.


## <a href="DMG"></a> 데이터 관리 게이트웨이

**데이터 관리 게이트웨이**는 온-프레미스 데이터 원본을 클라우드 서비스에 안전하고 관리되는 방식으로 연결하는 소프트웨어입니다. 데이터 관리 게이트웨이를 사용하면 다음을 수행할 수 있습니다.

- **하이브리드 데이터 액세스를 위해 온-프레미스 데이터에 연결** - 온-프레미스 데이터를 클라우드 서비스에 연결하여 클라우드 서비스를 활용하면서도 비즈니스는 계속 온-프레미스 데이터를 사용하여 운영할 수 있습니다.
- **보안 데이터 프록시 정의** - 데이터 관리 게이트웨이에서 표시되는 온-프레미스 데이터 원본을 정의할 수 있으므로 게이트웨이에서 클라우드 서비스의 데이터 요청을 인증하고 온-프레미스 데이터 원본을 보호합니다.
- **게이트웨이 관리를 통해 완전한 거버넌스 실현** - 데이터 관리 게이트웨이 내에서 모든 작업을 완벽히 모니터링 및 로깅하여 관리 및 거버넌스를 향상할 수 있습니다.
- **효율적으로 데이터 이동** - 데이터가 병렬로 전송되며, 자동 재시도 논리를 사용하여 일시적인 네트워크 문제를 빠르게 복구할 수 있습니다


데이터 관리 게이트웨이는 다음을 포함하여 다양한 온-프레미스 데이터 연결 기능을 제공합니다.

- **회사 방화벽 비침입** - 데이터 관리 게이트웨이는 설치하면 바로 작동하므로, 방화벽 연결을 열 필요도 없고 회사 네트워크 인프라 변경으로 지장을 주지도 않습니다. 
- **사용자의 인증서를 사용하여 자격 증명 암호화** - 데이터 원본에 연결하는 데 사용되는 자격 증명을 사용자가 전유하는 인증서를 사용하여 암호화합니다. 인증서 없이는 Microsoft를 포함하여 누구도 자격 증명을 일반 텍스트로 해독할 수 없습니다.

### 데이터 관리 게이트웨이를 사용할 때 고려할 사항
1.	데이터 관리 게이트웨이의 단일 인스턴스를 여러 온-프레미스 데이터 원본에 사용할 수 있지만, **게이트웨이는 Azure 데이터 팩터리에 연결**되어 있고 다른 데이터 팩터리와 공유할 수 없다는 점을 유념하세요.
2.	컴퓨터에 **데이터 관리 게이트웨이 인스턴스를 하나만** 설치할 수 있습니다. 온-프레미스 데이터 원본에 액세스해야 하는 데이터 팩터리가 2개인 경우 두 온-프레미스 컴퓨터에서 게이트웨이를 설치해야 하며, 이 경우 각 게이트웨이는 서로 다른 데이터 팩터리에 연결됩니다.
3.	**게이트웨이가 데이터 원본과 동일한 컴퓨터에 있지 않아도 되지만**, 데이터 원본에 더 가깝게 유지하면 게이트웨이가 데이터 원본에 연결하는 데 걸리는 시간이 줄어듭니다. 게이트웨이가 데이터 원본이 있는 리소스를 경합하지 않도록 온-프레미스 데이터 원본을 호스트하는 컴퓨터가 아닌 다른 컴퓨터에 게이트웨이를 설치하는 것이 좋습니다.
4.	**서로 다른 컴퓨터의 여러 게이트웨이가 동일한 온-프레미스 데이터 원본에 연결**할 수 있습니다. 예를 들어 두 게이트웨이가 2개의 데이터 팩터리를 처리하지만 동일한 온-프레미스 데이터 원본이 두 데이터 팩터리에 등록되어 있습니다. 
5.	컴퓨터에 **Power BI** 시나리오를 처리할 게이트웨이가 이미 설치된 경우 **별도의 Azure 데이터 팩터리용 게이트웨이**를 다른 컴퓨터에 설치하세요.

### 포트 및 보안 고려 사항 
- 데이터 관리 게이트웨이 설치 프로그램은 게이트웨이 컴퓨터에서 **8050** 및 **8051** 포트를 엽니다. 이러한 포트는 온-프레미스 연결된 서비스에 대한 자격 증명을 설정하고 데이터 원본과의 연결을 테스트할 수 있게 해주는 **자격 증명 관리자**(ClickOnce 응용프로그램)에서 사용됩니다. 인터넷에서 이러한 포트에 연결할 수는 없으며, 회사 방화벽에서 해당 포트를 열 필요도 없습니다.
- 온-프레미스 SQL Server 데이터베이스와 Azure SQL 데이터베이스 간에 데이터를 복사하는 경우 다음 사항을 확인합니다.
 
	- 게이트웨이 컴퓨터의 방화벽이 **TCP** 포트 **1433**에서 보내는 TCP 통신을 허용합니다.
	- **허용된 IP 주소**에 **게이트웨이 컴퓨터의 IP 주소**를 추가하도록 [Azure SQL 방화벽 설정](https://msdn.microsoft.com/library/azure/jj553530.aspx)을 구성합니다.

- 온-프레미스 SQL Server와 대상 간에 데이터를 복사하고 게이트웨이와 SQL Server 컴퓨터가 서로 다른 경우 다음을 수행 합니다. 게이트웨이가 SQL Server 인스턴스에서 수신 대기하는 포트를 통해 데이터베이스에 액세스할 수 있도록 SQL Server 컴퓨터의 [Windows 방화벽을 구성](https://msdn.microsoft.com/library/ms175043.aspx)합니다. 기본 인스턴스의 경우 포트 1433입니다.

- 데이터 원본에 대한 자격 증명을 설정하고 데이터 원본에 대한 연결을 테스트하려면 데이터 관리 게이트웨이에 연결할 수 있는 컴퓨터에서 **자격 증명 관리자** 응용프로그램을 시작해야 합니다.

### 게이트웨이 설치 - 필수 조건 

1.	지원되는 **운영 체제** 버전은 Windows 7, Windows 8/8.1, Windows Server 2008 R2, Windows Server 2012입니다.
2.	게이트웨이 컴퓨터에 대한 권장 **구성**은 최소한 2GHz, 4개 코어, 8GB RAM 및 80GB 디스크입니다.
3.	호스트 컴퓨터가 최대 절전 모드인 경우 게이트웨이가 데이터 요청에 응답할 수 없습니다. 따라서 게이트웨이를 설치하기 전에 컴퓨터에서 **전원 계획**을 적절하게 구성하세요. 컴퓨터가 최대 절전 모드로 구성된 경우 게이트웨이 설치에서 메시지가 표시됩니다.  

데이터 관리 게이트웨이는 호스팅되는 컴퓨터에서 원본 및 싱크 데이터를 직렬화 및 역직렬화합니다. 또한 복사될 때 데이터에 대해 형식 변환을 수행합니다. 복사 작업 동안 게이트웨이는 원본으로부터 메모리 버퍼까지 데이터를 읽으며 동시에 다른 기록기 스레드가 싱크에 버퍼를 씁니다. 최대 사용 시간 중에는 게이트웨어 호스트 컴퓨터에서 여러 복사 활동 작업을 동시에 실행할 수 있으므로 메모리 및 CPU 리소스의 소비가 유휴 시간보다 훨씬 더 많습니다. 따라서 데이터 관리 게이트웨이를 실행 중인 호스트 컴퓨터는 위에서 제안한 최소 컴퓨터 구성에서 요약한 것보다 더 많은 리소스가 또는 유휴 시간에는 더 적은 리소스가 필요할 수도 있습니다.


## 연습

이 연습에서는 온-프레미스 SQL Server 데이터베이스에서 Azure Blob으로 데이터를 이동하는 파이프라인이 포함된 데이터 팩터리를 만듭니다.

## 1단계: Azure 데이터 팩터리 만들기
이 단계에서는 Azure 관리 포털을 사용하여 **ADFTutorialOnPremDF**라는 Azure 데이터 팩터리 인스턴스를 만듭니다. 또한 Azure 데이터 팩터리 cmdlet을 사용하여 데이터 팩터리를 만들 수도 있습니다.

1.	[Azure Preview 포털][azure-preview-portal]에 로그인한 후 왼쪽 아래에서 **새로 만들기**를 클릭하고 **만들기** 블레이드에서 **데이터 분석**을 선택한 다음 **데이터 분석** 블레이드에서 **데이터 팩터리**를 클릭합니다.

	![새로 만들기->DataFactory][image-data-factory-new-datafactory-menu]
  
6. **새 데이터 팩터리** 블레이드에서 다음을 수행합니다.
	1. **ADFTutorialOnPremDF**를 **이름**으로 입력합니다.
	2. **리소스 그룹 이름**을 클릭하고 **ADFTutorialResourceGroup**을 선택합니다([Azure 데이터 팩터리 시작][adfgetstarted] 자습서를 수행한 경우). 기존 리소스 그룹을 선택하거나 리소스 그룹을 새로 만들 수 있습니다. 새 리소스 그룹을 만들려면 다음을 수행합니다.
		1. **새 리소스 그룹 만들기**를 클릭합니다.
		2. **리소스 그룹 만들기 블레이드**에서 리소스 그룹의 **이름**을 입력하고 **확인**을 클릭합니다.

7. **새 데이터 팩터리** 블레이드에서 **시작 보드에 추가**가 선택되어 있는지 확인합니다.

	![시작 보드에 추가][image-data-factory-add-to-startboard]

8. **새 데이터 팩터리** 블레이드에서 **만들기**를 클릭합니다.

	Azure 데이터 팩터리 이름은 전역적으로 고유해야 합니다. **데이터 팩터리 이름 “ADFTutorialOnPremDF”를 사용할 수 없습니다.** 오류가 표시되는 경우 데이터 팩터리 이름을 변경하고(예: yournameADFTutorialOnPremDF) 다시 만듭니다. 이 자습서의 나머지 단계를 수행하는 동안 ADFTutorialOnPremDF 대신에 이 이름을 사용합니다.

9. 왼쪽의 **알림** 허브를 클릭하고 만들기 프로세스에서 제공하는 알림을 찾습니다. **알림** 블레이드가 열려 있으면 **X**를 클릭하여 닫습니다.

	![알림 허브][image-data-factory-notifications-hub]

11. 만들기가 완료되면 아래와 같이 **데이터 팩터리** 블레이드가 표시됩니다.

	![데이터 팩터리 홈 페이지][image-data-factory-datafactory-homepage]

## 2단계: 데이터 관리 게이트웨이 만들기
5.	**ADFTutorialOnPremDF**의 **데이터 팩터리** 블레이드에서 **연결된 서비스**를 클릭합니다. 

	![데이터 팩터리 홈 페이지][image-data-factory-home-age]

2.	**연결된 서비스** 블레이드에서 **+ 데이터 게이트웨이**를 클릭합니다.

	![연결된 서비스 - 게이트웨이 추가 단추][image-data-factory-linkedservices-add-gateway-button]

2. **만들기** 블레이드에서 **adftutorialgateway**를 **이름**으로 입력하고 **확인**을 클릭합니다.

	![게이트웨이 만들기 블레이드][image-data-factory-create-gateway-blade]

3. 블레이드 **구성**에서 **이 컴퓨터에 직접 설치**를 클릭합니다. 이렇게 하면 컴퓨터에 게이트웨이의 설치 패키지가 다운로드되고 게이트웨이가 설치, 구성 및 등록됩니다.

	> [AZURE.NOTE]Internet Explorer 또는 Microsoft ClickOnce 호환 웹 브라우저를 사용하세요.

	![게이트웨이 - 구성 블레이드][image-data-factory-gateway-configure-blade]

	이는 하나의 단계로 게이트웨이를 다운로드, 설치, 구성 및 등록하는 가장 쉬운 방법(한 번 클릭)입니다. **Microsoft 데이터 관리 게이트웨이 구성 관리자** 응용프로그램이 컴퓨터에 설치된 것을 확인할 수 있습니다. 실행 파일 **ConfigManager.exe**를 **C:\\Program Files\\Microsoft Data Management Gateway\\1.0\\Shared** 폴더에서 찾을 수도 있습니다.

	또한 이 블레이드에서 링크를 클릭하여 게이트웨이를 수동으로 다운로드하여 설치하고 **REGISTER WITH KEY(키를 사용하여 등록)** 텍스트 상자에 표시된 키를 사용하여 등록할 수도 있습니다.
	
	모범 사례, 중요한 고려 사항 등을 포함하여 게이트웨이에 대한 자세한 내용은 [데이터 관리 게이트웨이](#DMG) 섹션을 참조하세요.

	>[AZURE.NOTE]데이터 관리 게이트웨이를 성공적으로 설치 및 구성하려면 로컬 컴퓨터의 관리자여야 합니다. 데이터 관리 게이트웨이 사용자 로컬 Windows 그룹에 사용자를 더 추가할 수 있습니다. 이 그룹의 구성원은 데이터 관리 게이트웨이 구성 관리자 도구를 사용하여 게이트웨이를 구성할 수 있습니다.

4. 왼쪽의 **알림** 허브를 클릭합니다. **알림** 블레이드에서 **'adftutorialgateway'에 대한 Express 설정 성공** 메시지가 표시될 때까지 대기합니다.

	![Express 설치 성공][express-setup-succeeded]
5. **만들기** 블레이드, **새 데이터 게이트웨이** 블레이드에서 차례로 **확인**을 클릭합니다.
6. **연결된 서비스** 블레이드(오른쪽 위 모퉁이의 **X**키를 눌러)를 닫고 **연결된 서비스** 블레이드를 다시 열어 최신 상태의 게이트웨이를 확인합니다. 
7. 게이트웨이의 **상태**가 **온라인**인지 확인합니다. 

	![게이트웨이 상태][gateway-status]
5. 컴퓨터에서 **Microsoft 데이터 관리 게이트웨이 구성 관리자** 응용프로그램을 시작합니다.

	![게이트웨이 구성 관리자][image-data-factory-gateway-configuration-manager]

6. 값이 다음과 같이 설정될 때까지 기다립니다.
	1. 서비스 **상태**가 **Started**로 설정되지 않은 경우 **서비스 시작**을 클릭하여 서비스를 시작하고 다른 필드가 새로 고쳐질 때까지 잠시 기다립니다.
	2. **게이트웨이 이름**이 **adftutorialgateway**로 설정됩니다.
	3. **인스턴스 이름**이 **adftutorialgateway**로 설정됩니다.
	4. **게이트웨이 키 상태**가 **Registered**로 설정됩니다.
	5. 맨 아래 상태 표시줄에 **Connected to Data Management Gateway Cloud Service(데이터 관리 게이트웨이 클라우드 서비스에 연결됨)**와 함께 **녹색 확인 표시**가 나타납니다.
	
7. **연결된 서비스** 블레이드에서 게이트웨이 **상태**가 **Good**인지 확인합니다.
8. 모든 블레이드를 닫아 **데이터 팩터리** 홈페이지로 돌아옵니다. 

## 2단계: 연결된 서비스 만들기 
이 단계에서는 2개의 연결된 서비스 **StorageLinkedService** 및 **SqlServerLinkedService**를 만듭니다. **SqlServerLinkedService**는 온-프레미스 SQL Server 데이터베이스를 연결하고 **StorageLinkedService** 연결된 서비스는 Azure Blob 저장소를 **ADFTutorialDataFactory**에 연결합니다. 이 연습의 뒷부분에서는 온-프레미스 SQL Server 데이터베이스에서 Azure Blob 저장소로 데이터를 복사하는 파이프라인을 만듭니다.

### 온-프레미스 SQL Server 데이터베이스에 연결된 서비스 추가
1.	**데이터 팩터리** 블레이드에서 **작성자 및 배포** 타일을 클릭하고 데이터 팩터리에 대한 **편집기**를 시작합니다.

	![작성 및 배포 타일][image-author-deploy-tile]

	데이터 팩터리 편집기에 대한 자세한 개요는 [데이터 팩터리 편집기][data-factory-editor] 토픽을 참조하세요.

2.	**편집기**의 도구 모음에서 **새 데이터 저장소** 단추를 클릭하고 드롭다운 메뉴에서 **온-프레미스 SQL Server 데이터베이스**를 선택합니다.

	![편집기 새 데이터 저장소 단추][image-editor-newdatastore-onpremsql-button]
    
3.	오른쪽 창에 온-프레미스 SQL Server 연결된 서비스를 만들기 위한 JSON 템플릿이 표시됩니다. 

	![온-프레미스 SQL 연결된 서비스 - 설정][image-editor-newdatastore-onpremsql-settings]

4.	JSON 창에서 다음을 수행합니다.
	1.	**gatewayName** 속성에 대해 **adftutorialgateway**를 입력하여 큰따옴표 안의 모든 텍스트를 바꿉니다.  
	2.	**SQL 인증**을 사용하는 경우 
		1.	**connectionString** 속성에 대해 **<servername>**, **<databasename>**, **<username>** 및 **<password>**를 온-프레미스 SQL Server, 데이터베이스, 사용자 계정의 이름 및 암호로 바꿉니다. 인스턴스 이름을 지정하려면 이스케이프 문자를 사용합니다. 예: **server\\instancename**. 	
		2.	마지막 두 속성(\*\*username\*\* 및 **password**)을 JSON 파일에서 제거하고 남은 JSON 스크립트의 마지막 줄 끝에 있는 **쉼표(,)** 문자를 제거합니다.
		
				{
				  "name": "SqlServerLinkedService",
				  "properties": {
				    "type": "OnPremisesSqlServer",
				    "typeProperties": {
				      "ConnectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;",
				      "gatewayName": "adftutorialgateway"
				    }
				  }
				}
	3.	**Windows 인증**을 사용하는 경우
		1. **connectionString** 속성에 대해 **<servername>** 및 **<databasename>**을 온-프레미스 SQL Server 및 데이터베이스의 이름으로 바꿉니다. **통합 보안**을 **True**로 설정합니다. 연결 문자열에서 **ID** 및 **암호**를 제거합니다.
			
				{
				  "name": "SqlServerLinkedService",
				  "properties": {
				    "type": "OnPremisesSqlServer",
				    "typeProperties": {
				      "ConnectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=True;",
				      "gatewayName": "adftutorialgateway",
				      "username": "<Specify user name if you are using Windows Authentication>",
				      "password": "<Specify password for the user account>"
				    }
				  }
				}		
		
6. 도구 모음에서 **배포**를 클릭하여 SqlServerLinkedService를 배포합니다. 제목 표시줄에 **연결된 서비스가 성공적으로 생성됨** 메시지가 표시되는지 확인합니다. 또한 왼쪽의 트리 뷰에 **SqlServerLinkedService**가 표시됩니다.
		   
	![SqlServerLinkedService 배포 성공][image-editor-sql-linked-service-successful]
	
  
**연결된 서비스** 블레이드에서 **새 데이터 저장소** 도구 모음 단추를 클릭하여 SQL Server 연결된 서비스를 만들 수도 있습니다. 이 경로로 진행하는 경우 포털에 액세스하는 컴퓨터에서 실행되는 자격 증명 관리자 ClickOnce 응용프로그램을 사용하여 데이터 원본에 대한 자격 증명을 설정합니다. 게이트웨이 컴퓨터와 다른 컴퓨터에서 포털에 액세스하는 경우 자격 증명 관리자 응용프로그램이 게이트웨이 컴퓨터에 연결할 수 있는지 확인해야 합니다. 응용프로그램이 게이트웨이 컴퓨터에 연결할 수 없는 경우, 데이터 원본에 대한 자격 증명을 설정하고 데이터 원본에 대한 연결을 테스트할 수 없습니다.

#### Azure 저장소 계정에 대한 연결된 서비스 추가
 
1. **편집기**에서, 도구 모음의 **새로운 데이터 저장소**를 클릭하고 드롭다운 메뉴에서 **Azure 저장소**를 선택합니다. 오른쪽 창에 Azure 저장소 연결된 서비스를 만들기 위한 JSON 템플릿이 표시됩니다. 

	![편집기 새 데이터 저장소 단추][image-editor-newdatastore-button]
    
6. **<accountname>** 및 **<accountkey>**를 Azure 저장소 계정의 계정 이름 및 계정 키 값으로 바꿉니다.

	![편집기 Blob 저장소 JSON][image-editor-blob-storage-json]
	
	JSON 속성에 대한 자세한 내용은 [JSON 스크립트 참조](http://go.microsoft.com/fwlink/?LinkId=516971)를 참조하세요.

6. 도구 모음에서 **배포**를 클릭하여 StorageLinkedService를 배포합니다. 제목 표시줄에 **연결된 서비스가 성공적으로 생성됨** 메시지가 표시되는지 확인합니다.

	![편집기 Blob 저장소 배포][image-editor-blob-storage-deploy]

 
## 3단계: 입력 및 출력 데이터 집합 만들기
이 단계에서는 복사 작업(온-프레미스 SQL Server 데이터베이스 = > Azure Blob 저장소)의 입력 및 출력 데이터를 나타내는 입력 및 출력 데이터 집합을 만듭니다. 데이터 집합 또는 테이블(사각형 데이터 집합)을 만들려면 먼저 다음을 수행해야 합니다(자세한 단계는 목록 다음에 나옴).

- 데이터 팩터리에 연결된 서비스로 추가한 SQL Server 데이터베이스에서 **emp**라는 테이블을 만들고 테이블에 몇 가지 샘플 항목을 삽입합니다.
- - [Azure 데이터 팩터리 시작][adfgetstarted] 문서의 자습서를 마치지 않은 경우 데이터 팩터리에 연결된 서비스로 추가한 Azure Blob 저장소 계정에 **adftutorial**이라는 Blob 컨테이너를 만듭니다.

### 자습서에 필요한 온-프레미스 SQL Server 준비

1. 온-프레미스 SQL Server 연결된 서비스(\*\*SqlServerLinkedService\*\*)에 대해 지정된 데이터베이스에서, 다음 SQL 스크립트를 사용하여 데이터베이스에 **emp** 테이블을 만듭니다.


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

1.	**데이터 팩터리 편집기**의 명령 모음에서 **새 데이터 집합**을 클릭하고 **온-프레미스 SQL**을 클릭합니다. 
2.	오른쪽 창의 JSON을 다음 텍스트로 바꿉니다.    

		{
		  "name": "EmpOnPremSQLTable",
		  "properties": {
		    "type": "SqlServerTable",
		    "linkedServiceName": "SqlServerLinkedService",
		    "typeProperties": {
		      "tableName": "emp"
		    },
		    "external": true,
		    "availability": {
		      "frequency": "Hour",
		      "interval": 1
		    },
		    "policy": {
		      "externalData": {
		        "retryInterval": "00:01:00",
		        "retryTimeout": "00:10:00",
		        "maximumRetry": 3
		      }
		    }
		  }
		}

	다음 사항에 유의하세요.
	
	- **type**을 **SqlServerTable**로 설정합니다.
	- **tablename**을 **emp**로 설정합니다.
	- **linkedServiceName**을 **SqlServerLinkedService**(2단계에서 만든 연결된 서비스)로 설정합니다.
	- Azure 데이터 팩터리의 다른 파이프라인에서 생성되지 않는 입력 테이블의 경우 **external** 속성을 **true**로 설정해야 합니다. 필요한 경우 **externalData** 섹션에서 정책을 지정할 수 있습니다.   

	JSON 속성에 대한 자세한 내용은 [JSON 스크립트 참조][json-script-reference]를 참조하세요.

2. 명령 모음에서 **배포**를 클릭하여 데이터 집합을 배포합니다(테이블은 사각형 데이터 집합임). 제목 표시줄에 **테이블이 성공적으로 배포됨** 메시지가 표시되는지 확인합니다.


### 출력 테이블 만들기

1.	**데이터 팩터리 편집기**의 명령 모음에서 **새 데이터 집합**을 클릭하고 **Azure Blob 저장소**를 클릭합니다.
2.	오른쪽 창의 JSON을 다음 텍스트로 바꿉니다. 

		{
		  "name": "OutputBlobTable",
		  "properties": {
		    "type": "AzureBlob",
		    "linkedServiceName": "StorageLinkedService",
		    "typeProperties": {
		      "folderPath": "adftutorial/outfromonpremdf",
		      "format": {
		        "type": "TextFormat",
		        "columnDelimiter": ","
		      }
		    },
		    "availability": {
		      "frequency": "Hour",
		      "interval": 1
		    }
		  }
		}
  
	다음 사항에 유의하세요.
	
	- **type**을 **AzureBlob**으로 설정합니다.
	- **linkedServiceName**을 **StorageLinkedService**(2단계에서 만든 연결된 서비스)로 설정합니다.
	- **folderPath**를 **adftutorial/outfromonpremdf**로 설정합니다. 여기서 outfromonpremdf는 adftutorial 컨테이너의 폴더입니다. **adftutorial** 컨테이너만 만들면 됩니다.
	- **가용성**은 **매시간** (\*\*빈도\*\*는 **시간**으로, **간격**은 **1**로 설정)로 설정됩니다. 데이터 팩터리 서비스는 Azure SQL 데이터베이스의 **emp** 테이블에 출력 데이터 조각을 1시간마다 생성합니다. 
 
	**입력 테이블**의 **fileName**을 지정하지 않는 경우 입력 폴더(\*\*folderPath\*\*)의 모든 파일/Blob이 입력으로 간주됩니다. JSON에서 fileName을 지정하는 경우에는 지정한 파일/Blob만 입력으로 간주됩니다. 예제는 [자습서][adf-tutorial]의 샘플 파일을 참조하세요.
 
	**출력 테이블**의 **fileName**을 지정하지 않는 경우, **folderPath**에 생성되는 파일의 이름은 다음과 같은 형식으로 지정됩니다. Data.<Guid>.txt (예: : Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.).

	**SliceStart** 시간을 기준으로 **folderPath** 및 **fileName**을 동적으로 설정하려면 partitionedBy 속성을 사용합니다. 다음 예제에서 folderPath는 SliceStart(처리 중인 조각의 시작 시간)의 Year, Month 및 Day를 사용하고 fileName은 SliceStart의 Hour를 사용합니다. 예를 들어 조각이 2014-10-20T08:00:00에 생성되는 경우 folderName은 wikidatagateway/wikisampledataout/2014/10/20으로 설정되고 fileName은 08.csv로 설정됩니다.

	  	"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
        "fileName": "{Hour}.csv",
        "partitionedBy": 
        [
        	{ "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
            { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } }, 
            { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } }, 
            { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } } 
        ],

 

	JSON 속성에 대한 자세한 내용은 [JSON 스크립트 참조][json-script-reference]를 참조하세요.

2.	명령 모음에서 **배포**를 클릭하여 데이터 집합을 배포합니다(테이블은 사각형 데이터 집합임). 제목 표시줄에 **테이블이 성공적으로 배포됨** 메시지가 표시되는지 확인합니다.
  

## 4단계: 파이프라인 만들기 및 실행
이 단계에서는 **EmpOnPremSQLTable**을 입력으로, **OutputBlobTable**을 출력으로 사용하는 **복사 작업**을 포함하는 **파이프라인**을 만듭니다.

1.	명령 모음에서 **새 파이프라인**을 클릭합니다. 단추가 표시되지 않는 경우 **...(줄임표)**를 클릭하여 명령 모음을 확장합니다.
2.	오른쪽 창의 JSON을 다음 텍스트로 바꿉니다.   


		{
		  "name": "ADFTutorialPipelineOnPrem",
		  "properties": {
		    "description": "This pipeline has one Copy activity that copies data from an on-prem SQL to Azure blob",
		    "activities": [
		      {
		        "name": "CopyFromSQLtoBlob",
		        "description": "Copy data from on-prem SQL server to blob",
		        "type": "Copy",
		        "inputs": [
		          {
		            "name": "EmpOnPremSQLTable"
		          }
		        ],
		        "outputs": [
		          {
		            "name": "OutputBlobTable"
		          }
		        ],
		        "typeProperties": {
		          "source": {
		            "type": "SqlSource",
		            "sqlReaderQuery": "select * from emp"
		          },
		          "sink": {
		            "type": "BlobSink"
		          }
		        },
		        "Policy": {
		          "concurrency": 1,
		          "executionPriorityOrder": "NewestFirst",
		          "style": "StartOfInterval",
		          "retry": 0,
		          "timeout": "01:00:00"
		        }
		      }
		    ],
		    "start": "2015-02-13T00:00:00Z",
		    "end": "2015-02-14T00:00:00Z",
		    "isPaused": false
		  }
		}
	다음 사항에 유의하세요.
 
	- activities 섹션에는 **type**이 **Copy**로 설정된 작업 하나밖에 없습니다.
	- 작업에 대한 **입력**을 **EmpOnPremSQLTable**로 설정하고 작업에 대한 **출력**을 **OutputBlobTable**로 설정합니다.
	- **transformation** 섹션에서 **SqlSource**를 **source type**으로 지정하고 **BlobSink를** **sink type**으로 지정합니다. - **SqlSource**의 **sqlReaderQuery** 속성에 대해 SQL 쿼리 **select \* from emp**를 지정합니다.

	**start** 속성 값을 현재 날짜로 바꾸고 **end** 값을 다음 날짜로 바꿉니다. start 및 end 날짜/시간은 둘 다 [ISO 형식](http://ko.wikipedia.org/wiki/ISO_8601)(영문)이어야 합니다. 예: 2014-10-14T16:32:41Z. **end** 시간은 선택 사항이지만 이 자습서에서는 사용합니다.
	
	**end** 속성 값을 지정하지 않는 경우 "\*\*start + 48시간\*\*"으로 계산됩니다. 파이프라인을 무기한 실행하려면 **end** 속성 값으로 **9/9/9999**를 지정합니다.
	
	각 Azure 데이터 팩터리 테이블에 대해 정의된 **가용성** 속성을 기준으로 데이터 조각이 처리되는 기간이 정의됩니다.
	
	위의 예에서는 각 데이터 조각이 1시간마다 생성되므로 24개 데이터 조각이 있게 됩니다.
	
2. 명령 모음에서 **배포**를 클릭하여 데이터 집합을 배포합니다(테이블은 사각형 데이터 집합임). 제목 표시줄에 **파이프라인이 성공적으로 배포됨** 메시지가 표시되는지 확인합니다.
5. 이제 **X**를 클릭하여 **편집기** 블레이드를 닫습니다. **X**를 다시 클릭하여 도구 모음 및 트리 뷰가 있는 ADFTutorialDataFactory 블레이드를 닫습니다. **저장하지 않은 편집 내용이 삭제됩니다** 메시지가 표시되면 **확인**을 클릭합니다.
6. **ADFTutorialOnPremDF**의 **데이터 팩터리** 블레이드로 돌아갑니다.

**축하합니다.** Azure 데이터 팩터리, 연결된 서비스, 테이블 및 파이프라인을 성공적으로 만들고 파이프라인을 예약했습니다.

### 다이어그램 뷰에서 데이터 팩터리 보기 
1. **Azure Preview 포털**에서 **ADFTutorialOnPremDF** 데이터 팩터리의 홈페이지에 있는 **다이어그램** 타일을 클릭합니다.

	![다이어그램 링크][image-data-factory-diagram-link]

2. 다음과 유사한 다이어그램이 표시됩니다.

	![다이어그램 뷰][image-data-factory-diagram-view]

	확대, 축소, 100% 확대, 크기에 맞게, 자동으로 파이프라인 및 테이블 위치 지정, 계보 정보 표시(선택한 항목의 업스트림 및 다운스트림 항목 강조 표시)를 수행할 수 있습니다. 개체(입출력 테이블 또는 파이프라인)를 두 번 클릭하면 해당 속성을 볼 수 있습니다.

## 5단계: 데이터 집합 및 파이프라인 모니터링
이 단계에서는 Azure 포털을 사용하여 Azure 데이터 팩터리에서 어떤 일이 일어나는지 모니터링합니다. 또한 PowerShell cmdlet을 사용하여 데이터 집합과 파이프라인을 모니터링할 수도 있습니다. 모니터링에 cmdlet을 사용하는 방법에 대한 자세한 내용은 [PowerShell을 사용하여 Azure 데이터 팩터리 모니터링 및 관리][monitor-manage-powershell]를 참조하세요.

1. 닫혀 있는 경우 **Azure Preview 포털**로 이동합니다.
2. **ADFTutorialOnPremDF**에 대한 블레이드가 열려있지 않은 경우, **시작 보드**에서 **ADFTutorialOnPremDF**를 클릭하여 엽니다.
3. 만든 테이블과 파이프라인의 **개수** 및 **이름**이 이 블레이드에 표시됩니다.

	![데이터 팩터리 홈 페이지][image-data-factory-homepage-2]
4. 이제 **데이터 집합** 타일을 클릭합니다.
5. **데이터 집합** 블레이드에서 **EmpOnPremSQLTable**을 클릭합니다.

	![EmpOnPremSQLTable 조각][image-data-factory-onprem-sqltable-slices]

6. 현재 시간까지의 데이터 조각이 이미 생성되어 **Ready** 상태입니다. SQL Server 데이터베이스에 데이터를 삽입했기 때문이며 데이터가 데이터베이스에 항상 있습니다. 맨 아래의 **Problem slices(문제 조각)** 섹션에 표시되는 조각이 없는지 확인합니다.


	**Recently updated slices(최근에 업데이트된 조각)** 및 **Recently failed slices(최근에 실패한 조각)** 목록은 둘 다 **마지막 업데이트 시간**을 기준으로 정렬됩니다. 조각의 업데이트 시간은 다음과 같은 상황에서 변경됩니다.
    

	-  **Set-AzureDataFactorySliceStatus**를 사용하거나 조각의 **조각** 블레이드에서 **실행**을 클릭하여 수동으로 조각 상태를 업데이트합니다.
	-  실행(예: 실행 시작, 실행 종료 및 실패, 실행 종료 및 성공 등)으로 인해 조각 상태가 변경됩니다.
 
	목록의 제목 또는 **...(줄임표)**을 클릭하여 더 큰 조각 목록을 표시합니다. 도구 모음의 **필터**를 클릭하여 조각을 필터링합니다.
	
	조각 시작/종료 시간을 기준으로 정렬된 데이터 조각을 보려면 **데이터 조각(조각 시간별)** 타일을 클릭합니다.

7. 이제 **데이터 집합** 블레이드에서 **OutputBlobTable**을 클릭합니다.

	![OputputBlobTable 조각][image-data-factory-output-blobtable-slices]
8. 현재 시간까지 조각이 생성되었고 **Ready** 상태인지 확인합니다. 현재 시간까지 조각의 상태가 **Ready**로 설정될 때까지 기다립니다.
9. 목록에서 아무 데이터 조각이나 클릭하면 **데이터 조각** 블레이드가 표시됩니다.

	![데이터 조각 블레이드][image-data-factory-dataslice-blade]

	조각이 **Ready** 상태가 아닌 경우 **Upstream slices that are not ready(준비되지 않은 업스트림 조각)** 목록에서 Ready 상태가 아니고 현재 조각의 실행을 차단하는 업스트림 조각을 확인할 수 있습니다.

10. 맨 아래 목록에서 **작업 실행**을 클릭하여 **작업 실행 세부 정보**를 표시합니다.

	![작업 실행 세부 정보 블레이드][image-data-factory-activity-run-details]

11. **X**를 클릭하여 모든 블레이드를 닫아 **ADFTutorialOnPremDF**의 홈 블레이드로 돌아옵니다.
14. (선택 사항) **파이프라인**을 클릭하고 **ADFTutorialOnPremDF**를 클릭한 다음 입력 테이블(\*\*Consumed\*\*) 또는 출력 테이블(\*\*Produced\*\*)을 드릴스루합니다.
15. **Azure 저장소 탐색기**와 같은 도구를 사용하여 출력을 확인합니다.

	![Azure 저장소 탐색기][image-data-factory-stroage-explorer]


## Azure PowerShell을 사용하여 게이트웨이 만들기 및 등록 
이 섹션에서는 Azure PowerShell cmdlet을 사용하여 게이트웨이를 만들고 등록하는 방법에 대해 설명합니다.

1. **Azure PowerShell**을 관리자 모드로 시작합니다. 
2. Azure 데이터 팩터리 cmdlet은 **AzureResourceManager** 모드에서 사용할 수 있습니다. 다음 명령을 실행하여 **AzureResourceManager** 모드로 전환합니다.     

        switch-azuremode AzureResourceManager


2. 다음과 같이 **New-AzureDataFactoryGateway** cmdlet을 사용하여 논리 게이트웨이를 만듭니다.

		New-AzureDataFactoryGateway -Name <gatewayName> -DataFactoryName <dataFactoryName> -ResourceGroupName ADF –Description <desc>

	**예제 명령 및 출력**:


		PS C:\> New-AzureDataFactoryGateway -Name MyGateway -DataFactoryName $df -ResourceGroupName ADF –Description “gateway for walkthrough”

		Name              : MyGateway
		Description       : gateway for walkthrough
		Version           :
		Status            : NeedRegistration
		VersionStatus     : None
		CreateTime        : 9/28/2014 10:58:22
		RegisterTime      :
		LastConnectTime   :
		ExpiryTime        :
		ProvisioningState : Succeeded


3. **New-AzureDataFactoryGatewayKey** cmdlet을 사용하여 새로 만든 게이트웨이의 등록 키를 생성하고 이 키를 지역 변수 **$Key**에 저장합니다.

		New-AzureDataFactoryGatewayKey -GatewayName <gatewayname> -ResourceGroupName ADF -DataFactoryName <dataFactoryName>

	
	**예제 명령 출력:**


		PS C:\> $Key = New-AzureDataFactoryGatewayKey -GatewayName MyGateway -ResourceGroupName ADF -DataFactoryName $df 

	
4. Azure PowerShell에서 **C:\\Program Files\\Microsoft Data Management Gateway\\1.0\\PowerShellScript\*\* 폴더로 전환하고 다음 명령에 표시된 대로 지역 변수 **$Key**와 연결된 **RegisterGateway.ps1** 스크립트를 실행하여 컴퓨터에 설치된 클라이언트 에이전트를 앞에서 만든 논리 게이트웨이에 등록합니다.

		PS C:\> .\RegisterGateway.ps1 $Key.GatewayKey
		
		Agent registration is successful!

5. **Get-AzureDataFactoryGateway** cmdlet을 사용하여 데이터 팩터리의 게이트웨이 목록을 가져올 수 있습니다. **상태**에 **online**이 표시되어 있으면 게이트웨이를 사용할 준비가 되었음을 나타냅니다.

		Get-AzureDataFactoryGateway -DataFactoryName <dataFactoryName> -ResourceGroupName ADF

게이트웨이를 제거하려면 **Remove-AzureDataFactoryGateway** cmdlet을 사용하고 게이트웨이 설명을 업데이트하려면 **Set-AzureDataFactoryGateway** cmdlet을 사용합니다. 이러한 cmdlet에 대한 구문 및 기타 세부 정보는 데이터 팩터리 Cmdlet 참조를 참조하세요.



[monitor-manage-using-powershell]: data-factory-monitor-manage-using-powershell.md
[adf-getstarted]: data-factory-get-started.md
[adf-tutorial]: data-factory-tutorial.md
[use-custom-activities]: data-factory-use-custom-activities.md
[use-pig-and-hive-with-data-factory]: data-factory-pig-hive-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[data-factory-introduction]: data-factory-introduction.md
[data-factory-editor]: data-factory-editor.md

[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456


[64bit-download-link]: http://go.microsoft.com/fwlink/?LinkId=517623
[32bit-download-link]: http://go.microsoft.com/fwlink/?LinkId=517624

[azure-preview-portal]: http://portal.azure.com
[adfgetstarted]: data-factory-get-started.md
[monitor-manage-powershell]: data-factory-monitor-manage-using-powershell.md





[json-script-reference]: http://go.microsoft.com/fwlink/?LinkId=516971
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456



[azure-powershell-install]: http://azure.microsoft.com/documentation/articles/install-configure-powershell/

[image-author-deploy-tile]: ./media/data-factory-use-onpremises-datasources/author-deploy-tile.png
[image-editor-newdatastore-button]: ./media/data-factory-use-onpremises-datasources/editor-newdatastore-button.png
[image-editor-blob-storage-json]: ./media/data-factory-use-onpremises-datasources/editor-blob-storage-settings.png
[image-editor-blob-storage-deploy]: ./media/data-factory-use-onpremises-datasources/editor-deploy-blob-linked-service.png
[image-editor-newdatastore-onpremsql-button]: ./media/data-factory-use-onpremises-datasources/editor-newdatastore-onpremsql-button.png
[image-editor-newdatastore-onpremsql-settings]: ./media/data-factory-use-onpremises-datasources/editor-onprem-sql-settings.png
[image-editor-sql-linked-service-successful]: ./media/data-factory-use-onpremises-datasources/editor-sql-linked-service-successful.png

[gateway-status]: ./media/data-factory-use-onpremises-datasources/gateway-status.png
[express-setup-succeeded]: ./media/data-factory-use-onpremises-datasources/express-setup-succeeded.png
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

[image-data-factory-get-storage-key]: ./media/data-factory-use-onpremises-datasources/OnPremGetStorageKey.png

[image-data-factory-linkedservices-add-gateway-button]: ./media/data-factory-use-onpremises-datasources/OnPremLinkedServicesAddGaewayButton.png

[image-data-factory-create-gateway-blade]: ./media/data-factory-use-onpremises-datasources/OnPremCreateGatewayBlade.png

[image-data-factory-gateway-configure-blade]: ./media/data-factory-use-onpremises-datasources/OnPremGatewayConfigureBlade.png

[image-data-factory-gateway-configuration-manager]: ./media/data-factory-use-onpremises-datasources/OnPremDMGConfigurationManager.png

[image-data-factory-oneclick-install]: ./media/data-factory-use-onpremises-datasources/OnPremOneClickInstall.png

[image-data-factory-diagram-link]: ./media/data-factory-use-onpremises-datasources/OnPremDiagramLink.png

[image-data-factory-diagram-view]: ./media/data-factory-use-onpremises-datasources/OnPremDiagramView.png

[image-data-factory-homepage-2]: ./media/data-factory-use-onpremises-datasources/OnPremDataFactoryHomePage2.png

[image-data-factory-stroage-explorer]: ./media/data-factory-use-onpremises-datasources/OnPremAzureStorageExplorer.png

[image-data-factory-home-age]: ./media/data-factory-use-onpremises-datasources/DataFactoryHomePage.png

[image-data-factory-onprem-sqltable-slices]: ./media/data-factory-use-onpremises-datasources/OnPremSQLTableSlicesBlade.png

[image-data-factory-output-blobtable-slices]: ./media/data-factory-use-onpremises-datasources/OutputBlobTableSlicesBlade.png

[image-data-factory-dataslice-blade]: ./media/data-factory-use-onpremises-datasources/DataSlice.png

[image-data-factory-activity-run-details]: ./media/data-factory-use-onpremises-datasources/ActivityRunDetailsBlade.png

[image-data-factory-new-datafactory-menu]: ./media/data-factory-use-onpremises-datasources/NewDataFactoryMenu.png

[image-data-factory-preview-portal-storage-key]: ./media/data-factory-get-started/PreviewPortalStorageKey.png

<!---HONumber=August15_HO6-->