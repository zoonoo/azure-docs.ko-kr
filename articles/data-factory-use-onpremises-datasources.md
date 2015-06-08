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
	ms.date="05/06/2015" 
	ms.author="spelluru"/>

# 파이프라인에서 온-프레미스 데이터를 사용할 수 있도록 설정

Azure 데이터 팩터리의 파이프라인에서 온-프레미스 데이터 원본을 사용할 수 있게 하려면 Azure 관리 포털이나 Azure PowerShell을 사용하여 데이터 팩터리에 온-프레미스 데이터 원본을 연결된 서비스로 추가해야 합니다.
 
온-프레미스 데이터 원본을 데이터 팩터리에 연결된 서비스를 추가할 수 있으려면 먼저 온-프레미스 컴퓨터에 Microsoft 데이터 관리 게이트웨이를 다운로드하여 설치하고 온-프레미스 데이터 원본의 연결된 서비스에서 게이트웨이를 사용하도록 구성해야 합니다.


## <a href="DMG"></a> 데이터 관리 게이트웨이

**데이터 관리 게이트웨이** 온-프레미스 데이터 원본을 안전 하 고 관리 되는 방식으로 클라우드 서비스에 연결 하는 소프트웨어입니다. 데이터 관리 게이트웨이를 사용하면 다음을 수행할 수 있습니다.

- **하이브리드 데이터 액세스에 대 한 온-프레미스 데이터에 연결** – 온-프레미스 데이터를 사용 하 여 실행 하는 비즈니스를 유지 하면서 클라우드 서비스를 활용할 수 있는 클라우드 서비스에 온-프레미스 데이터를 연결할 수 있습니다.
- **보안 데이터 프록시 정의** – 해당 게이트웨이 클라우드 서비스에서 데이터 요청을 인증 하 고 온-프레미스 데이터 원본 보호 하므로 온-프레미스 데이터 원본을 데이터 관리 게이트웨이 사용 하 여 노출 됩니다 정의할 수 있습니다.
- **전체 관리 방침에 대 한 게이트웨이 관리** – 전체 모니터링 및 관리 및 관리 방침에 대 한 데이터 관리 게이트웨이 내의 모든 활동의 로깅을 사용 하 여 제공 됩니다.
- **데이터를 효율적으로 이동할** – 데이터를 전송에 간헐적인 복원 력 있는 네트워크 문제 자동 재시도 논리를 병렬로 합니다.


데이터 관리 게이트웨이는 다음을 포함하여 다양한 온-프레미스 데이터 연결 기능을 제공합니다.

- **회사 방화벽 비-침입** – 설치 후 바로 작동 하는 데이터 관리 게이트웨이, 방화벽을 열어야 할 필요 없이 연결 또는 요구 침입적 인 변경 회사 네트워크 인프라에 있습니다. 
- **인증서와 함께 자격 증명을 암호화** – 데이터 원본에 연결할 때 사용 하는 자격 증명은 완벽 하 게 사용자가 소유한 인증서로 암호화 됩니다. 인증서 없이는 Microsoft를 포함하여 누구도 자격 증명을 일반 텍스트로 해독할 수 없습니다.

### 데이터 관리 게이트웨이를 사용할 때 고려할 사항
1.	데이터 관리 게이트웨이의 단일 인스턴스에서 여러 온-프레미스 데이터 원본에 대해 사용할 수 있지만 염두에 하는 **게이트웨이 Azure 데이터 팩터리 관련이** 다른 데이터 팩터리로 공유 될 수 있습니다.
2.	사용할 수 있습니다 **데이터 관리 게이트웨이 인스턴스가 하나만** 컴퓨터에 설치 합니다. 온-프레미스 데이터 원본에 액세스해야 하는 데이터 팩터리가 2개인 경우 두 온-프레미스 컴퓨터에서 게이트웨이를 설치해야 하며, 이 경우 각 게이트웨이는 서로 다른 데이터 팩터리에 연결됩니다.
3.	 **게이트웨이 데이터 소스와 동일한 컴퓨터에 있이 필요가 없습니다**, 하지만 데이터 원본에 더 밀접 하 게 유지 하면 데이터 원본에 연결할 게이트웨이에 대 한 시간이 줄어듭니다. 데이터 소스를 사용 하 여 리소스에 대 한 게이트웨이 경합 하지 않습니다 있도록 온-프레미스 데이터 원본을 호스팅하는 것과에서 다른 되는 컴퓨터에 게이트웨이 설치 하는 것이 좋습니다.
4.	사용할 수 있습니다 **동일한 온-프레미스 데이터 원본에 연결 하는 서로 다른 컴퓨터에 여러 게이트웨이**. 예를들어, 두 데이터 팩터리를 제공 하는 두 게이트웨이 할 수 있습니다 하지만 동일한 온-프레미스 데이터 원본에 등록 된 데이터 팩터리입니다. 
5.	사용자 컴퓨터의 서비스 제공에 설치 된 게이트웨이 이미 있는 경우는 **Power BI** 시나리오를 설치 하십시오는 **Azure 데이터 팩터리에 대 한 별도 게이트웨이** 다른 컴퓨터에 있습니다.

### 포트 및 보안 고려 사항 
- 데이터 관리 게이트웨이 설치 프로그램이 열립니다 **8050** 및 **8051** 게이트웨이 컴퓨터에서 포트입니다. 이러한 포트에서 사용 되는 **자격 증명 관리자** (ClickOnce 응용 프로그램)는 온-프레미스 연결 서비스에 대 한 자격 증명을 설정 하 고 데이터 원본 연결을 테스트할 수 있습니다. 인터넷에서 이러한 포트를 연결할 수 하 고 필요 없는 이러한 회사 방화벽에서 열립니다.
- 시작/끝 온-프레미스 SQL Server 데이터베이스를 사용 하 여 Azure SQL 데이터베이스에서 데이터를 복사 하는 경우에 다음 사항을 확인 합니다.
 
	- 보내는 TCP 통신을 허용 하는 게이트웨이 컴퓨터의 방화벽이 **TCP** 포트 **1433**
	- 구성 [SQL Azure 방화벽 설정을](https://msdn.microsoft.com/library/azure/jj553530.aspx) 추가 하는 **게이트웨이 컴퓨터의 IP 주소** 에 **허용 IP 주소**.

- 데이터를 복사 하 여에서 온-프레미스 SQL Server를 모든 대상 및 게이트웨이 및 SQL Server 컴퓨터는 서로 다른 경우 다음을 수행 합니다.: [Windows 방화벽을 구성](https://msdn.microsoft.com/library/ms175043.aspx) SQL Server에서 컴퓨터는 게이트웨이 SQL Server 인스턴스에서 수신 대기 포트를 통해 데이터베이스에 액세스할 수 있도록 합니다. 기본 인스턴스의 경우 포트 1433입니다.

- 시작 해야는 **자격 증명 관리자** 데이터 원본에 대 한 자격 증명을 설정 하 고 데이터 원본에 연결을 테스트할 수 있게 되기를 데이터 관리 게이트웨이에 연결할 수 있는 컴퓨터에 응용 프로그램입니다.

### 게이트웨이 설치-필수 구성 요소 

1.	지원 되는 **운영 체제** 버전은 Windows 7, Windows 8/8.1, Windows Server 2008 R2, Windows Server 2012입니다.
2.	권장 되는 **구성** 게이트웨이에 대 한 컴퓨터는 적어도 2 g h z, 4 개 코어, 8GB RAM과 80GB 디스크입니다.
3.	호스트 컴퓨터 최대 절전 모드로 전환 하는 게이트웨이의 데이터 요청에 응답할 수 없습니다. 따라서 적절 한 구성 **전원 관리 옵션** 게이트웨이 설치 하기 전에 컴퓨터에 있습니다. 게이트웨이 설치 메시지는 컴퓨터가 절전 모드로 구성 된 경우 메시지를 표시 합니다.  


 

## 연습

이 연습에서는 온-프레미스 SQL Server 데이터베이스에서 Azure Blob으로 데이터를 이동하는 파이프라인이 포함된 데이터 팩터리를 만듭니다.

## 1 단계: 프로그램 Azure 데이터 팩터리 만들기
이 단계에서는 명명 된 Azure 데이터 팩터리 인스턴스를 만들고 Azure 관리 포털 사용 **ADFTutorialOnPremDF**. 또한 Azure 데이터 팩터리 cmdlet을 사용하여 데이터 팩터리를 만들 수도 있습니다.

1.	로그인 한 후는 [Azure 미리 보기 포털][azure-preview-portal], 클릭 **새로** 왼쪽 아래 모퉁이에서 선택 **데이터 분석** 에 **만들기** 블레이드를 클릭 하 고 **데이터 팩터리** 에 **데이터 분석** 블레이드.

	![새로운 사용할->][image-data-factory-new-datafactory-menu]
  
6. 에 **새 데이터 팩터리** 블레이드:
	1. 입력 **ADFTutorialOnPremDF** 에 대 한는 **이름**.
	2. 클릭 하 여 **리소스 그룹 이름** 선택 하 고 **ADFTutorialResourceGroup** (에서 자습서 실행 한 것 [Azure 데이터 팩터리 시작][adfgetstarted]. 기존 리소스 그룹을 선택하거나 리소스 그룹을 새로 만들 수 있습니다. 새 리소스 그룹을 만들려면 다음을 수행합니다.
		1. 클릭 하 여 **새 리소스 그룹을 만들려면**.
		2. 에 **만들기 리소스 그룹 블레이드**, 를 입력 한 **이름** 를클릭하고 리소스 그룹에 대 한 **확인**.

7.  **시작 보드에 추가** 시 검사 되는 **새 데이터 팩터리** 블레이드.

	![시작 보드에 추가][image-data-factory-add-to-startboard]

8. 에 **새 데이터 팩터리** 블레이드를 클릭 하 여 **만들기**.

	> [AZURE.NOTE]**데이터 팩터리 이름 "ADFTutorialOnPremDF"를 사용할 수 없는 경우**  
9. 생성 프로세스에서 알림을 찾습니다는 **알림을** 왼쪽의 허브입니다. 클릭 **X** 를 닫으려면는 **알림을** 블레이드 열려 있으면 됩니다.

	![알림 허브][image-data-factory-notifications-hub]

11. 만들기를 완료 한 후 기다리면는 **데이터 팩터리** 아래와 같이 블레이드:

	![데이터 팩터리 홈페이지][image-data-factory-datafactory-homepage]

## 2 단계: 데이터 관리 게이트웨이 만들기
5.	에 **데이터 팩터리** 에 대 한 블레이드 **ADFTutorialOnPremDF**, 를 클릭 하 여 **연결 된 서비스**. 

	![데이터 팩터리 홈페이지][image-data-factory-home-age]

2.	에 **연결 된 서비스** 블레이드를 클릭 하 여 **+ 데이터 게이트웨이**.

	![연결 된 서비스-게이트웨이 단추를 추가 합니다.][image-data-factory-linkedservices-add-gateway-button]

2. 에 **만들기** 블레이드, 입력 **adftutorialgateway** 에 대 한는 **이름**, 클릭 하 고 **확인**.

	![블레이드 게이트웨이 만들기][image-data-factory-create-gateway-blade]

3. 블레이드 **구성**에서 **이 컴퓨터에 직접 설치**를 클릭합니다. 이렇게 하면 컴퓨터에 게이트웨이의 설치 패키지가 다운로드되고 게이트웨이가 설치, 구성 및 등록됩니다.

	> [AzURE.NOTE]Internet Explorer 또는 Microsoft ClickOnce 호환 웹 브라우저를 사용 하십시오.

	![게이트웨이-블레이드를 구성 합니다.][image-data-factory-gateway-configure-blade]

	이는 하나의 단계로 게이트웨이를 다운로드, 설치, 구성 및 등록하는 가장 쉬운 방법(한 번 클릭)입니다. 볼 수는 **Microsoft 데이터 관리 게이트웨이 구성 관리자** 응용 프로그램이 컴퓨터에 설치 합니다. 실행 파일을 찾을 수도 있습니다 **ConfigManager.exe** 폴더에서: **C:\Program Files\Microsoft 데이터 관리 Gateway\1.0\Shared**.

	또한 다운로드 하 고이 블레이드에서 링크를 사용 하 여 게이트웨이 수동으로 설치 하 고 수에 표시 된 키를 사용 하 여 등록은 **키와 등록** 텍스트 상자입니다.
	
	참조 [데이터 관리 게이트웨이](#DMG) 중요 한 고려 사항 및 모범 사례를 포함 하 여 게이트웨이에 대 한 세부 정보에 대 한 섹션입니다.

	>[AZURE.NOTE]로컬 컴퓨터에 설치 하 고 성공적으로 데이터 관리 게이트웨이 구성 관리자 여야 합니다. 데이터 관리 게이트웨이 사용자 로컬 Windows 그룹에 추가 사용자를 추가할 수 있습니다. 이 그룹의 구성원 게이트웨이 구성 하려면 데이터 관리 게이트웨이 구성 관리자 도구를 사용할 수 없게 됩니다.

4. 클릭는 **알림을** 왼쪽의 허브입니다. 나타날 때까지 대기 **'adftutorialgateway' 성공에 대 한 설치 Express** 에서 메시지는 **알림을** 블레이드입니다.

	![Express 설치 성공][express-setup-succeeded]
5. 클릭 **확인** 에 **만들기** 블레이드 및 다음에 **새 데이터 게이트웨이** 블레이드.
6. 닫기는 **연결 된 서비스** 블레이드 (키를 눌러 **X** 오른쪽 위 모퉁이에 있는 단추)를 닫은 후 다시는 **연결 된 서비스** 게이트웨이의 최신 상태를 보려면 블레이드입니다. 
7. 확인 하는 **상태** 게이트웨이 **온라인**. 

	![게이트웨이 상태][gateway-status]
5. 시작 **Microsoft 데이터 관리 게이트웨이 구성 관리자** 컴퓨터에 응용 프로그램입니다.

	![게이트웨이 구성 관리자][image-data-factory-gateway-configuration-manager]

6. 값이 다음과 같이 설정될 때까지 기다립니다.
	1. 하는 경우 서비스 **상태** 로 설정 되지 않은 **시작 됨**, 클릭 **서비스를 시작** 에 서비스를 시작 하 고 새로 고칠 수 있는 다른 필드에 1 분 기다립니다.
	2. **게이트웨이 이름** 로 설정 된 **adftutorialgateway**.
	3. **인스턴스 이름을** 로 설정 된 **adftutorialgateway**.
	4. **게이트웨이 키 상태** 로 설정 된 **등록 된**.
	5. 상태 표시줄 아래 표시 **데이터 관리 게이트웨이 클라우드 서비스에 연결 된** 와 함께 **녹색 확인 표시가**.
	
7. 에 **연결 된 서비스** 블레이드를 확인 하는 **상태** 게이트웨이 **좋은**.
8. 에 도달할 때까지 모든 블레이드가 닫습니다는 **데이터 팩터리** 홈페이지로 이동 합니다. 

## 2 단계: 연결 된 서비스 만들기 
이 단계에서는 두 연결 된 서비스 만듭니다: **StorageLinkedService** 및 **SqlServerLinkedService**.  **SqlServerLinkedService** 온-프레미스 SQL Server 데이터베이스 연결 및 **StorageLinkedService** 연결 된 서비스에 연결 하기 위해 Azure blob 저장소는 **ADFTutorialDataFactory**. 이 연습의 뒷부분에서는 온-프레미스 SQL Server 데이터베이스에서 Azure Blob 저장소로 데이터를 복사하는 파이프라인을 만듭니다.

### 온-프레미스 SQL Server 데이터베이스에 연결된 서비스 추가
1.	에 **데이터 팩터리** 블레이드, 클릭 **작성자 및 배포** 타일을 시작 하는 **편집기** 데이터 팩터리에 대 한 합니다.

	![작성 및 배포 타일][image-author-deploy-tile]

	> [AZURE.NOTE][데이터 팩터리 편집기][data-factory-editor]
2.	에 **편집기**, 를 클릭 하 여 **새로운 데이터 저장소** 선택 도구 모음에서 단추 **온-프레미스 SQL server 데이터베이스** 의 드롭다운 메뉴에서에서. 

	![새 데이터 저장소 단추 편집기][image-editor-newdatastore-onpremsql-button]
    
3.	오른쪽 창에는 온-프레미스 연결 된 SQL Server 서비스를 만들기 위한 JSON 템플릿을 표시 되어야 합니다. ![온-프레미스 SQL 연결 된 서비스-설정][image-editor-newdatastore-onpremsql-settings]

4.	JSON 창에서 다음을 수행 합니다.
	1.	에 대 한는 **gatewayName** 속성, 입력 **adftutorialgateway** 큰따옴표 안에 모든 텍스트를 바꿉니다.  
	2.	사용 중인 경우 **SQL 인증**: 
		1.	에 대 한는 **connectionString** 속성을 대체 **< 서버 이름 >**, **< 데이터베이스 이름 >**, **< 사용자 이름 >**, 및 **< 암호 >** 온-프레미스 SQL server, 데이터베이스, 사용자 계정 및 암호의 이름으로 합니다.	
		2.	마지막 두 속성을 제거 합니다 (* * 사용자 이름 * * 및 **암호**) JSON에서 파일을 제거는 **쉼표 (,)** 나머지 JSON 스크립트에서 마지막 줄의 끝에 문자입니다.
		
				{
	    			"name": "SqlServerLinkedService",
	    			"properties": {
		        		"type": "OnPremisesSqlLinkedService",
		        		"ConnectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;",
		        		"gatewayName": "adftutorialgateway"
	    			}
				}
	3.	사용 중인 경우 **Windows 인증**:
		1. 에 대 한는 **connectionString** 속성을 대체 **< 서버 이름 >** 및 **< 데이터베이스 이름 >** 온-프레미스 SQL server 및 데이터베이스의 이름으로 합니다. 설정 **통합 보안** 를 **True**. 제거 **ID** 및 **암호** 의 연결 문자열입니다.
			
				{
    				"name": "SqlServerLinkedService",
    				"properties": {
        				"type": "OnPremisesSqlLinkedService",
        				"ConnectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=True;",
		   				"gatewayName": "adftutorialgateway",
				        "username": "<Specify user name if you are using Windows Authentication>",
				        "password": "<Specify password for the user account>"
    				}
				}		
		
6. 클릭 하 여 **배포** 는 SqlServerLinkedService를 배포 하려면 도구 모음입니다. 메시지가 표시 되는지 확인 **연결 된 서비스 만든 성공적으로** 제목 표시줄에 있습니다. 또한 나타납니다는 **SqlServerLinkedService** 왼쪽 트리 뷰에서 합니다.
		   
	![SqlServerLinkedService 배포 성공][image-editor-sql-linked-service-successful]
	
  
> [AZURE.NOTE]클릭 하 여 연결 된 SQL Server 서비스를 만들 수도 있습니다 **새로운 데이터 저장소** 에서 도구 모음 단추는 **연결 된 서비스** 블레이드입니다. 이 경로 이동 하는 경우 포털에 액세스 하는 컴퓨터에서 실행 되는 자격 증명 관리자 ClickOnce 응용 프로그램을 사용 하 여 데이터 원본에 대 한 자격 증명을 설정 합니다. 포털에서 게이트웨이 컴퓨터와에서는 다른 컴퓨터에 액세스 하는 경우에 자격 증명 관리자 응용 프로그램 게이트웨이 컴퓨터에 연결할 수 있는지 확인 해야 합니다. 응용 프로그램 게이트웨이 컴퓨터에 연결할 수 없으면, 것은 없도록 데이터 원본에 대 한 자격 증명을 설정 하 고 데이터 원본에 대 한 연결을 테스트 합니다.

#### Azure 저장소 계정에 대한 연결된 서비스 추가
 
1. 에 **편집기**, 를 클릭 하 여 **새로운 데이터 저장소** 선택 도구 모음에서 단추 **Azure 저장소** 의 드롭다운 메뉴에서에서. 오른쪽 창에는 연결 된 Azure 저장소 서비스를 만들기 위한 JSON 템플릿을 표시 되어야 합니다. 

	![새 데이터 저장소 단추 편집기][image-editor-newdatastore-button]
    
6. 대체 **< accountname >** 및 **< accountkey >** 계정 이름 및 Azure 저장소 계정에 대 한 계정 키 값입니다.

	![JSON 편집기 Blob 저장소][image-editor-blob-storage-json]
	
	> [AZURE.NOTE]참조 [JSON 스크립팅 참조](http://go.microsoft.com/fwlink/?LinkId=516971) JSON 속성에 대 한 세부 정보에 대 한 합니다.

6. 클릭 하 여 **배포** 는 StorageLinkedService를 배포 하려면 도구 모음입니다. 메시지가 표시 되는지 확인 **연결 된 서비스 만든 성공적으로** 제목 표시줄에 있습니다.

	![편집기 Blob 저장소 배포][image-editor-blob-storage-deploy]

 
## 3 단계: 입력 및 출력 만듭니다 데이터 집합
이 단계에서는 복사 작업(온-프레미스 SQL Server 데이터베이스 = > Azure Blob 저장소)의 입력 및 출력 데이터를 나타내는 입력 및 출력 데이터 집합을 만듭니다. 데이터 집합 또는 테이블(사각형 데이터 집합)을 만들려면 먼저 다음을 수행해야 합니다(자세한 단계는 목록 다음에 나옴).

- 라는 테이블을 만들어 **emp** SQL Server 데이터베이스에 연결 된 서비스에 추가한 데이터 팩터리 및 insert 두 테이블에 샘플 항목입니다.
- - 자습서를 통과 하지 않은 경우 [Azure 데이터 팩터리 시작][adfgetstarted] 문서, 명명 된 blob 컨테이너 만들기 **adftutorial** Azure에서 blob 데이터 팩터리를 연결 된 서비스로 추가 하는 저장소 계정입니다.

### 자습서에 필요한 온-프레미스 SQL Server 준비

1. 온-프레미스에 대 한 지정한 데이터베이스의 SQL Server 서비스를 연결 (* * SqlServerLinkedService * *)를 만들려면 다음 SQL 스크립트를 사용는 **emp** 데이터베이스의 테이블에에서 있습니다.


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

1.	에 **데이터 팩터리 편집기**, 를 클릭 하 여 **새 데이터 집합** 클릭 하 여 확인 하 고 명령 모음에서 **온-프레미스 SQL**. 
2.	오른쪽 창에서 JSON을 다음 텍스트로 바꿉니다.    

        {
    		"name": "EmpOnPremSQLTable",
    		"properties":
    		{
        		"location":
        		{
            		"type": "OnPremisesSqlServerTableLocation",
            		"tableName": "emp",
            		"linkedServiceName": "SqlServerLinkedService"
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
	
	- 위치 **유형** 로 설정 된 **OnPremisesSqlServerTableLocation**.
	- **tableName** 로 설정 된 **emp**.
	- **linkedServiceName** 로 설정 된 **SqlServerLinkedService** (2 단계에서에서이 연결 된 서비스 만든 했습니다).
	- 지정 해야 하지 Azure 데이터 공장에서 다른 파이프라인에 의해 생성 된 입력된 테이블에 대 한 **waitOnExternal** JSON에서 섹션입니다. 이 섹션은 입력 데이터가 Azure 데이터 팩터리 서비스 외부에서 생성되었음을 나타냅니다.   

	참조 [JSON 스크립팅 참조][json-script-reference] JSON 속성에 대 한 세부 정보에 대 한 합니다.

2. 클릭 하 여 **배포** 데이터 집합을 배포 하려면 명령 모음에서 (테이블은 사각형 데이터 집합). 메시지 라는 제목 표시줄에 표시 되는지 확인 **테이블 배포 성공적으로**.


### 출력 테이블 만들기

1.	에 **데이터 팩터리 편집기**, 를 클릭 하 여 **새 데이터 집합** 클릭 하 여 확인 하 고 명령 모음에서 **Azure Blob 저장소**.
2.	오른쪽 창에서 JSON을 다음 텍스트로 바꿉니다. 

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
            		"linkedServiceName": "StorageLinkedService"
        		},
        		"availability": 
        		{
            		"frequency": "Hour",
            		"interval": 1
        		}
    		}
		}
  
	다음 사항에 유의하세요.
	
	- 위치 **유형** 로 설정 된 **AzureBlobLocation**.
	- **linkedServiceName** 로 설정 된 **StorageLinkedService** (2 단계에서에서이 연결 된 서비스 만든 했습니다).
	- **folderPath** 로 설정 된 **adftutorial/outfromonpremdf** outfromonpremdf adftutorial 컨테이너에 있는 폴더입니다. 만들 필요가 **adftutorial** 컨테이너입니다.
	-  **가용성** 로 설정 된 **매시간** (* * 주파수 * *로 설정 합니다. **시간** 및 **간격** 로 설정 **1**). 데이터 팩터리 서비스는 출력 데이터 조각에 1 시간 마다 생성 됩니다는 **emp** Azure SQL 데이터베이스의 테이블입니다. 

	지정 하지 않으면는 **fileName** 에 대 한 프로그램 **입력된 테이블**, 입력된 폴더에서 모든 파일/blob (* * folderPath * *) 입력으로 간주 됩니다. JSON에서 fileName을 지정하는 경우에는 지정한 파일/Blob만 입력으로 간주됩니다. 샘플 파일을 참조는 [자습서][adf-tutorial] 에 대 한 예입니다.
 
	**출력 테이블**의 **fileName**을 지정하지 않는 경우, **folderPath**에 생성되는 파일의 이름은 다음과 같은 형식으로 지정됩니다. Data.<Guid>.txt (예: : Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.).

	설정 하려면 **folderPath** 및 **fileName** 기반으로 동적으로 **SliceStart** 시간, partitionedBy 속성을 사용 합니다. 다음 예제에서 folderPath는 SliceStart(처리 중인 조각의 시작 시간)의 Year, Month 및 Day를 사용하고 fileName은 SliceStart의 Hour를 사용합니다. 예를 들어 조각이 2014-10-20T08:00:00에 생성되는 경우 folderName은 wikidatagateway/wikisampledataout/2014/10/20으로 설정되고 fileName은 08.csv로 설정됩니다.

	  	"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
        "fileName": "{Hour}.csv",
        "partitionedBy": 
        [
        	{ "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
            { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } }, 
            { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } }, 
            { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } } 
        ],

 

	참조 [JSON 스크립팅 참조][json-script-reference] JSON 속성에 대 한 세부 정보에 대 한 합니다.

2.	클릭 하 여 **배포** 데이터 집합을 배포 하려면 명령 모음에서 (테이블은 사각형 데이터 집합). 메시지 라는 제목 표시줄에 표시 되는지 확인 **테이블 배포 성공적으로**.
  

## 4 단계: 만들고 파이프라인을 실행 합니다.
이 단계에서는 만듭니다는 **파이프라인** 하나로 **복사 작업** 사용 하 여 **EmpOnPremSQLTable** 입력으로 및 **OutputBlobTable** 출력으로.

1.	클릭 하 여 **새 파이프라인** 명령 모음에서. 보이지 않으면 단추를 클릭 하 여 **... (줄임표)** 명령 모음을 확장 합니다.
2.	오른쪽 창에서 JSON을 다음 텍스트로 바꿉니다.   


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
	        	],
				"start": "2015-02-13T00:00:00Z",
        		"end": "2015-02-14T00:00:00Z",
        		"isPaused": false
			}
		}

	다음 사항에 유의하세요.
 
	- 작업 섹션에는 활동에만 해당 **유형** 로 설정 된 **CopyActivity**.
	- **입력** 활동으로 설정 됩니다 **EmpOnPremSQLTable** 및 **출력** 활동으로 설정 됩니다 **OutputBlobTable**.
	- 에 **변환** 섹션 **SqlSource** 로 지정 된는 **원본 유형** 및 **BlobSink **로 지정 된는 **싱크 종류**. - SQL 쿼리 **선택 * emp에서** 에 대해 지정 된는 **sqlReaderQuery** 속성의 **SqlSource**.

	> [AZURE.NOTE]값을 바꿉니다는 **시작** 은 현재 날짜를 사용 하 여 속성 및 **끝** 다음날을 사용 하 여 값입니다. 모두 시작 및 끝 datetime에 있어야 합니다. [ISO 형식](http://en.wikipedia.org/wiki/ISO_8601). 예: 2014-10-14T16:32:41Z 합니다.  **끝** 시간은 선택적 이지만이 자습서에서 사용할 것입니다. 에 대 한 값을 지정 하지 않으면는 **끝** 로 계산 됩니다 속성을 "* * 시작 + 48 시간 * *"입니다. 파이프라인을 무기한으로 실행 하려면 지정 **9/9/9999** 에 대 한 값으로는 **끝** 속성입니다. 에 따라 데이터 조각을 처리할 수 시간 기간을 정의 하는 **가용성** 각 Azure 데이터 팩터리 테이블에 대해 정의 된 속성입니다. 위의 예에서는 각 데이터 조각이 1시간마다 생성되므로 24개 데이터 조각이 있게 됩니다.
	
2. 클릭 하 여 **배포** 데이터 집합을 배포 하려면 명령 모음에서 (테이블은 사각형 데이터 집합). 메시지 라는 제목 표시줄에 표시 되는지 확인 **파이프라인 배포 성공적으로**.
5. 이제 닫습니다는 **편집기** 블레이드를 클릭 하 여 **X**. 클릭 하 여 **X** 를 다시 도구 모음 및 트리 보기와 ADFTutorialDataFactory 블레이드를 닫습니다. 표시 되 면 **저장 되지 않은 편집 내용을 무시 됩니다** 메시지를 클릭 하 여 **확인**.
6. 돌아갈 수는 **데이터 팩터리** 에 대 한 블레이드는 **ADFTutorialOnPremDF**.

**축하합니다.** Azure 데이터 팩터리, 연결된 서비스, 테이블 및 파이프라인을 성공적으로 만들고 파이프라인을 예약했습니다.

### 다이어그램 뷰에서 데이터 팩터리 보기 
1. 에 **Azure 미리 보기 포털**, 를 클릭 하 여 **다이어그램** 타일에 대 한 홈페이지에서는 **ADFTutorialOnPremDF** 데이터 팩터리.:

	![다이어그램 링크][image-data-factory-diagram-link]

2. 다음과 유사한 다이어그램이 표시됩니다.

	![다이어그램 보기][image-data-factory-diagram-view]

	확대, 축소, 확대/축소 맞춤, 자동으로 파이프라인 및 테이블의 위치 및 계보 정보를 표시 하려면 (은 선택 된 항목의 항목을 업스트림 및 다운스트림 강조 하 고), 100%로 확대/축소 수 있습니다. 이중 blick (입/출력 테이블 또는 파이프라인)에 대 한 속성을 보려면 개체에 지정할 수 있습니다.

## 5 단계: 데이터 집합 및 파이프라인 모니터링
이 단계에서는 Azure 포털을 사용하여 Azure 데이터 팩터리에서 어떤 일이 일어나는지 모니터링합니다. 또한 PowerShell cmdlet을 사용하여 데이터 집합과 파이프라인을 모니터링할 수도 있습니다. 모니터링에 대 한 cmdlet을 사용 하는 방법에 대 한 세부 정보를 참조 하십시오. [모니터 및 PowerShell을 사용 하 여 관리 Azure 데이터 팩터리][monitor-manage-powershell].

1. 이동 **Azure 미리 보기 포털** (닫아도 것) 하는 경우
2. 경우에 대 한 블레이드 **ADFTutorialOnPremDF** 가 열려있지 않으면 클릭 하 여 엽니다 **ADFTutorialOnPremDF** 에 **시작 보드**.
3. 표시 되어야는 **count** 및 **이름** 테이블 및이 블레이드에서 만든 파이프라인.

	![데이터 팩터리 홈페이지][image-data-factory-homepage-2]
4. 이제 클릭 **데이터 집합** 바둑판식으로 배열 합니다.
5. 에 **데이터 집합** 블레이드를 클릭 하 여는 **EmpOnPremSQLTable**.

	![EmpOnPremSQLTable 조각][image-data-factory-onprem-sqltable-slices]

6. 현재 시간까지 데이터 조각이 이미 생성 되었습니다 보이며 스페이스인 **준비**. SQL Server 데이터베이스에 데이터를 삽입했기 때문이며 데이터가 데이터베이스에 항상 있습니다. 확인 되지 조각에 표시 하는 **문제 조각** 아래쪽 섹션입니다.


	모두 **조각 최근에 업데이트** 및 **최근에 조각 실패 한** 목록을 기준으로 정렬 됩니다는 **마지막 업데이트 시간**. 다음과 같은 상황에서 조각의 업데이트 시간이 변경 됩니다.
    

	-  수동으로 업데이트할 있습니다 조각의 상태, 예를 사용 하 여는 **집합 AzureDataFactorySliceStatus** (또는)를 클릭 하 여 **실행** 에 **조각** 블레이드는 조각에 대 한 합니다.
	-  실행으로 인해 상태를 변경 하는 조각 (예: 시작을 실행 하는, 종료 하 고 실패 한 실행, 실행을 종료 하 고 성공, 등).
 
	목록 또는 의 제목을 클릭 **... (타원)** 더 큰 조각 목록을 확인 합니다. 클릭 하 여 **필터** 슬라이스를 필터링 하려면 도구 모음입니다.
	
	기준으로 정렬 된 조각 시작/종료 시간 대신 데이터 조각이 보려면 클릭 합니다. **(시간별 slice) 데이터 조각이** 바둑판식으로 배열 합니다.

7. 이제는 **데이터 집합** 블레이드를 클릭 하 여 **OutputBlobTable**.

	![OputputBlobTable 조각][image-data-factory-output-blobtable-slices]
8. 현재 시간까지 조각 생성 된 확인 하 고 **준비**. 상태는 현재 시간까지 조각으로 설정 될 때까지 대기 **준비**.
9. 목록에서 데이터 조각을 아무 것 이나 클릭 하 고 표시 되어야는 **데이터 조각을** 블레이드입니다.

	![데이터 조각 블레이드][image-data-factory-dataslice-blade]

	조각에 없는 경우는 **준비** 상태를 준비 하지 않으며는 현재 조각에서 실행할 수 없도록 차단 여부를 지정 하는 업스트림 조각을 볼 수 있습니다는 **준비 하지 않은 업스트림 조각** 목록입니다.

10. 클릭할는 **활동 실행** 보려면 아래쪽에 있는 목록에서 **활동 실행 세부**.

	![활동 실행 정보 블레이드][image-data-factory-activity-run-details]

11. 클릭 하 여 **X** 에 대 한 홈 블레이드로 다시 가져올 때까지 모든 블레이드를 닫으려면는 **ADFTutorialOnPremDF**.
14. (선택 사항) 클릭 하 여 **파이프라인**, 클릭 **ADFTutorialOnPremDF**, 입력된 테이블을 통해 드릴 및 (* * 사용 됨 * *) 출력 테이블 또는 (* * 생산 * *).
15. 와 같은 도구를 사용 하 여 **Azure 저장소 탐색기** 출력을 확인 합니다.

	![Azure 저장소 탐색기][image-data-factory-stroage-explorer]


## 만들기 및 Azure PowerShell을 사용 하 여 게이트웨이 등록 합니다. 
이 섹션에서는 Azure PowerShell cmdlet을 사용하여 게이트웨이를 만들고 등록하는 방법에 대해 설명합니다.

1. 시작 **Azure PowerShell** 관리자 모드에서. 
2. 사용할 수 있는 Azure 데이터 팩터리 cmdlet는 **AzureResourceManager** 모드입니다. 전환 하려면 다음 명령을 실행 하는 **AzureResourceManager** 모드입니다.     

        switch-azuremode AzureResourceManager


2. 사용 하 여는 **새로 AzureDataFactoryGateway** cmdlet을 다음과 같이 논리적 게이트웨이 만듭니다.

		New-AzureDataFactoryGateway -Name <gatewayName> -DataFactoryName <dataFactoryName> -ResourceGroupName ADF –Description <desc>

	**예제 명령 및 출력**:


		PS C:> New-AzureDataFactoryGateway -Name MyGateway -DataFactoryName $df -ResourceGroupName ADF –Description “gateway for walkthrough”

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


3. 사용 하는 **새로 AzureDataFactoryGatewayKey** 새로 만든된 게이트웨이에 대 한 등록 키를 생성 하 고 지역 변수에 키를 저장 하는 cmdlet **$Key**:

		New-AzureDataFactoryGatewayKey -GatewayName <gatewayname> -ResourceGroupName ADF -DataFactoryName <dataFactoryName>

	
	**예제 명령 출력:**


		PS C:> $Key = New-AzureDataFactoryGatewayKey -GatewayName MyGateway -ResourceGroupName ADF -DataFactoryName $df 

	
4. Azure PowerShell에서 폴더로 전환: **C:\Program Files\Microsoft 데이터 관리 Gateway\1.0\PowerShellScript** 및 실행 **RegisterGateway.ps1** 로컬 변수와 연결 된 스크립트 **$Key** 이전에 만들 논리 게이트웨이와 함께 컴퓨터에 설치 된 클라이언트 에이전트에 등록 하려면 다음 명령에 표시 된 것 처럼.

		PS C:> .\RegisterGateway.ps1 $Key.GatewayKey
		
		Agent registration is successful!

5. 사용할 수는 **Get AzureDataFactoryGateway** cmdlet 데이터 공장에서 게이트웨이 목록을 가져올 수 있습니다. 때는 **상태** 보여줍니다 **온라인**, 게이트웨이 사용할 준비가 것을 의미 합니다.

		Get-AzureDataFactoryGateway -DataFactoryName <dataFactoryName> -ResourceGroupName ADF

사용 하 여 게이트웨이 제거할 수는 **제거 AzureDataFactoryGateway** cmdlet 및 update 설명을 사용 하 여 게이트웨이 **집합 AzureDataFactoryGateway** cmdlet. 이러한 cmdlet에 대한 구문 및 기타 세부 정보는 데이터 팩터리 Cmdlet 참조를 참조하세요.



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

<!---HONumber=GIT-SubDir-->