<properties 
	pageTitle="Azure 데이터 팩터리를 사용하여 온-프레미스 및 클라우드 간 데이터 이동" 
	description="데이터 관리 게이트웨이 및 Azure Data Factory를 사용하여 온-프레미스와 클라우드 간에 데이터 이동을 알아봅니다." 
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
	ms.date="10/29/2015" 
	ms.author="spelluru"/>

# 온-프레미스 원본과 클라우드 간에 데이터 관리 게이트웨이로 데이터 이동
최신 데이터 통합에 대한 도전 과제 중 하나는 온-프레미스와 클라우드에서 원활한 데이터 이동입니다. 데이터 팩터리는 데이터 관리 게이트웨이와 이 통합을 원활하게 합니다. 데이터 팩터리 관리 게이트웨이는 하이브리드 파이프라인을 사용하도록 온-프레미스를 설치할 수 있는 에이전트입니다.

이 문서에서는 데이터 팩터리를 사용하여 온-프레미스 데이터 저장소를 클라우드 데이터 저장소 및 클라우드 처리와 통합하는 개요를 제공합니다. 이 문서는 [데이터 이동 활동](data-factory-data-movement-activities.md) 문서 및 기타 데이터 팩터리 핵심 개념 문서를 작성합니다. 다음 개요는 사용자가 파이프라인, 활동, 데이터 집합 및 복사 작업과 같은 데이터 팩터리 개념을 알고 있다고 가정합니다.

데이터 게이트웨이는 다음 기능을 제공합니다.

1.	데이터 팩터리 내 온-프레미스 데이터 원본 및 클라우드 데이터 원본을 모델로 데이터를 이동합니다.
2.	데이터 팩터리 클라우드 대시보드를 사용하여 게이트웨이 상태에 대한 가시성을 사용하는 모니터링 및 관리를 위한 유리의 단일 창이 있습니다.
3.	온-프레미스 데이터 원본에 안전한 액세스를 관리합니다.
	1. 회사 방화벽에 필요한 변경 내용이 없습니다. 게이트웨이는 아웃 바운드 HTTP 기반 연결이 인터넷을 열게 합니다.
	2. 인증서를 사용하여 온-프레미스 데이터 저장소에 대한 자격 증명을 암호화합니다.
4.	효율적인 데이터 이동 - 데이터가 병렬로 전송되며, 자동 재시도 논리를 사용하여 일시적인 네트워크 문제를 빠르게 복구할 수 있습니다

## 데이터 관리 게이트웨이를 사용할 때 고려할 사항
1.	데이터 관리 게이트웨이의 단일 인스턴스를 여러 온-프레미스 데이터 원본에 사용할 수 있지만, **단일 게이트웨이 인스턴스는 하나의 Azure Data Factory에 연결**되어 있고 다른 데이터 팩터리와 공유할 수 없다는 점을 유념하세요.
2.	단일 컴퓨터에 **데이터 관리 게이트웨이 인스턴스를 하나만** 설치할 수 있습니다. 온-프레미스 데이터 원본에 액세스해야 하는 데이터 팩터리가 2개인 경우 두 온-프레미스 컴퓨터에서 게이트웨이를 설치해야 하며, 이 경우 각 게이트웨이는 서로 다른 데이터 팩터리에 연결됩니다.
3.	**게이트웨이가 데이터 원본과 동일한 컴퓨터에 있지 않아도 되지만**, 데이터 원본에 더 가깝게 유지하면 게이트웨이가 데이터 원본에 연결하는 데 걸리는 시간이 줄어듭니다. 게이트웨이가 데이터 원본이 있는 리소스를 경합하지 않도록 온-프레미스 데이터 원본을 호스트하는 컴퓨터가 아닌 다른 컴퓨터에 게이트웨이를 설치하는 것이 좋습니다.
4.	**서로 다른 컴퓨터의 여러 게이트웨이가 동일한 온-프레미스 데이터 원본에 연결**할 수 있습니다. 예를 들어 두 게이트웨이가 2개의 데이터 팩터리를 처리하지만 동일한 온-프레미스 데이터 원본이 두 데이터 팩터리에 등록되어 있습니다.
5.	컴퓨터에 **Power BI** 시나리오를 처리할 게이트웨이가 이미 설치된 경우 **별도의 Azure Data Factory용 게이트웨이**를 다른 컴퓨터에 설치하세요.
6.	**ExpressRoute를 사용하는 경우 게이트웨이를 사용**해야 합니다. 
7.	**ExpressRoute** 및 **게이트웨이 사용**을 사용하는 경우 데이터 원본을 온-프레미스 데이터 원본(즉, 방화벽 뒤)으로 처리하여 서비스와 데이터 원본 간에 연결을 설정해야 합니다. 

## 게이트웨이 설치 - 필수 조건
1.	지원되는 **운영 체제** 버전은 Windows 7, Windows 8/8.1, Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2입니다.
2.	게이트웨이 컴퓨터에 대한 권장 **구성**은 최소한 2GHz, 4개 코어, 8GB RAM 및 80GB 디스크입니다.
3.	호스트 컴퓨터가 최대 절전 모드인 경우 게이트웨이가 데이터 요청에 응답할 수 없습니다. 따라서 게이트웨이를 설치하기 전에 컴퓨터에서 **전원 계획**을 적절하게 구성하세요. 컴퓨터가 최대 절전 모드로 구성된 경우 게이트웨이 설치에서 메시지가 표시됩니다.

또한 특정 주기에 복사 활동이 실행되는 사실 때문에 컴퓨터에서 리소스 사용(CPU, 메모리)은 유휴 시간 사용량이 가장 많을 때와 동일한 패턴을 따릅니다. 리소스 사용률은 이동하는 데이터 양에 따라 달라집니다. 여러 복사 작업이 진행 중인 경우 사용량이 많은 시간 동안 리소스 사용량이 증가하는 것을 볼 수 있습니다. 위가 최소 구성인 반면 항상 데이터 이동에 대한 특정 부하에 따라 위에서 설명한 최소 구성 보다 더 많은 리소스를 사용하는 구성이 좋습니다.

## 설치
Microsoft 다운로드 센터에서 MSI 설치 패키지를 다운로드하여 데이터 관리 게이트웨이를 설치할 수 있습니다. MSI는 유지된 모든 설정을 사용하여 기존 데이터 관리 게이트웨이를 최신 버전으로 업그레이드하는 데 사용할 수 있습니다. 아래의 단계별 연습 과정에 따라 Azure 포털에서 MSI 패키지의 링크를 찾을 수 있습니다.

### 설치 모범 사례입니다.
1.	게이트웨이에 대한 호스트 컴퓨터에서 전원 계획을 구성하여 컴퓨터는 최대 절전 모드로 합니다. 호스트 컴퓨터가 최대 절전 모드인 경우 게이트웨이가 데이터 요청에 응답할 수 없습니다.
2.	게이트웨이에 연결된 인증서를 백업해야 합니다.

### 설치 문제를 해결합니다.
회사가 방화벽 또는 프록시 서버를 사용하는 경우 데이터 관리 게이트웨이가 Microsoft 클라우드 서비스에 연결할 수 없는 경우 추가 단계가 필요할 수 있습니다.

#### 이벤트 뷰어로 게이트웨이 로그 보기:

게이트웨이 구성 관리자 응용 프로그램은 "연결 끊김" 또는 "연결"과 같은 게이트웨이의 상태를 보여줍니다.

자세한 내용은 Windows 이벤트 로그의 게이트웨이 로그에서 살펴볼 수 있습니다. 문제 해결 게이트웨이 관련 문제가 이벤트 뷰어에서 오류 수준 이벤트를 찾는 동안 **응용 프로그램 및 서비스 로그**>**데이터 관리 게이트웨이**의 Windows **이벤트 뷰어**를 사용하여 찾을 수 있습니다.


#### 방화벽 관련 문제에 대한 가능한 증상:

1. 게이트웨이를 등록하려고 하는 경우 다음과 같은 오류가 나타납니다. "게이트웨이 키를 등록하지 못했습니다. 게이트웨이 키를 다시 등록하려 하기 전에 이터 관리 게이트웨이가 연결된 상태이며 데이터 관리 게이트웨이 호스트 서비스가 시작되었는지 확인합니다."
2. 구성 관리자를 여는 경우 상태가 "연결 끊김" 또는 "연결"로 표시됩니다. "이벤트 뷰어"> "응용 프로그램 및 서비스 로그" > "데이터 관리 게이트웨이"에서 Windows 이벤트 로그를 보는 경우 "원격 서버에 연결할 수 없습니다." 또는 "데이터 관리 게이트웨이의 구성 요소가 응답이 없어 및 자동으로 다시 시작됩니다. 구성 요소 이름은 게이트웨이입니다."와 같은 오류 메시지가 표시됩니다.

방화벽 또는 프록시 서버의 부적절한 구성이 발생시키며 자신을 인증하려면 데이터 관리 게이트웨이가 클라우드 서비스에 연결할 수 없도록 차단합니다.

가능한 범위에 있는 두 방화벽은 다음과 같습니다. 조직의 중앙 라우터에서 실행되는 회사 방화벽 및 게이트웨이를 설치한 로컬 컴퓨터에서 데몬으로 구성 Windows 방화벽입니다. 다음은 일부 고려 사항입니다.

- 회사 방화벽에 대한 인바운드 정책을 변경하지 않아도 됩니다.
- 회사 방화벽 및 Windows 방화벽은 모두 TCP 포트에 대한 아웃 바운드 규칙을 사용해야 합니다. 80, 443, 및 9305에서 9354까지입니다. 클라우드 서비스와 데이터 관리 게이트웨이 간에 연결을 설정하기 위해 Microsoft Azure 서비스 버스를 사용합니다.

MSI 설치는 게이트웨이 컴퓨터에 인바운드 포트에 대한 Windows 방화벽 규칙을 자동으로 구성합니다.(위의 포트 및 보안 고려 사항 섹션을 참조)

하지만 위에 언급한 아웃 바운드 포트가 기본적으로 로컬 컴퓨터 및 회사 방화벽에서 설치할 수 있다고 가정합니다. 그렇지 않은 경우 이러한 아웃 바운드 포트를 사용하도록 설정해야 합니다. 타사 방화벽으로 Windows 방화벽을 바꾼 경우 이러한 포트를 수동으로 열 수 있어야 합니다.

회사가 프록시 서버를 사용하는 경우 Microsoft Azure를 화이트 리스트에 추가해야 합니다. [Microsoft 다운로드 센터](http://msdn.microsoft.com/library/windowsazure/dn175718.aspx)에서 유효한 Microsoft Azure IP 주소의 목록을 다운로드할 수 있습니다.

## 데이터 게이트웨이 사용 – 단계별 연습
이 연습에서는 온-프레미스 SQL Server 데이터베이스에서 Azure Blob으로 데이터를 이동하는 파이프라인이 포함된 데이터 팩터리를 만듭니다.

### 1단계: Azure Data Factory 만들기
이 단계에서는 Azure 관리 포털을 사용하여 **ADFTutorialOnPremDF**라는 Azure Data Factory 인스턴스를 만듭니다. 또한 Azure Data Factory cmdlet을 사용하여 데이터 팩터리를 만들 수도 있습니다.

1.	[Azure Preview 포털](https://portal.azure.com)에 로그인한 후 왼쪽 아래에서 **새로 만들기**를 클릭하고 **만들기** 블레이드에서 **데이터 분석**을 선택한 다음 **데이터 분석** 블레이드에서 **데이터 팩터리**를 클릭합니다.

	![새로 만들기->DataFactory](./media/data-factory-move-data-between-onprem-and-cloud/NewDataFactoryMenu.png)
  
6. **새 데이터 팩터리** 블레이드에서 다음을 수행합니다.
	1. **ADFTutorialOnPremDF**를 **이름**으로 입력합니다.
	2. **리소스 그룹 이름**을 클릭하고 **ADFTutorialResourceGroup**을 선택합니다. 기존 리소스 그룹을 선택하거나 리소스 그룹을 새로 만들 수 있습니다. 새 리소스 그룹을 만들려면 다음을 수행합니다.
		1. **새 리소스 그룹 만들기**를 클릭합니다.
		2. **리소스 그룹 만들기 블레이드**에서 리소스 그룹의 **이름**을 입력하고 **확인**을 클릭합니다.

7. **새 데이터 팩터리** 블레이드에서 **시작 보드에 추가**가 선택되어 있는지 확인합니다.

	![시작 보드에 추가](./media/data-factory-move-data-between-onprem-and-cloud/OnPremNewDataFactoryAddToStartboard.png)

8. **새 데이터 팩터리** 블레이드에서 **만들기**를 클릭합니다.

	Azure Data Factory 이름은 전역적으로 고유해야 합니다. **데이터 팩터리 이름 “ADFTutorialOnPremDF”를 사용할 수 없습니다.** 오류가 표시되는 경우 데이터 팩터리 이름을 변경하고(예: yournameADFTutorialOnPremDF) 다시 만듭니다. 이 자습서의 나머지 단계를 수행하는 동안 ADFTutorialOnPremDF 대신에 이 이름을 사용합니다.

9. 만들기 프로세스 중 다음 이미지에서처럼 제목 표시줄의 **알림** 단추를 클릭하여 알림을 검색합니다. 다시 클릭하면 알림 창이 닫힙니다.

	![알림 허브](./media/data-factory-move-data-between-onprem-and-cloud/OnPremNotificationsHub.png)

11. 만들기가 완료되면 아래와 같이 **데이터 팩터리** 블레이드가 표시됩니다.

	![데이터 팩터리 홈 페이지](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDataFactoryHomePage.png)

### 2단계: 데이터 관리 게이트웨이 만들기
5. **데이터 팩터리** 블레이드에서 **작성자 및 배포** 타일을 클릭하여 데이터 팩터리에 대한 **편집기**를 시작합니다.

	![작성 및 배포 타일](./media/data-factory-move-data-between-onprem-and-cloud/author-deploy-tile.png) 
6.	데이터 팩터리 편집기에서 도구 모음의 **...(말줄임표)**를 클릭한 다음 **새 데이터 케이트웨이**를 클릭합니다. 

	![도구 모음의 새 데이터 게이트웨이](./media/data-factory-move-data-between-onprem-and-cloud/NewDataGateway.png)
2. **만들기** 블레이드에서 **adftutorialgateway**를 **이름**으로 입력하고 **확인**을 클릭합니다. 	

	![게이트웨이 만들기 블레이드](./media/data-factory-move-data-between-onprem-and-cloud/OnPremCreateGatewayBlade.png)

3. 블레이드 **구성**에서 **이 컴퓨터에 직접 설치**를 클릭합니다. 이렇게 하면 컴퓨터에 게이트웨이의 설치 패키지가 다운로드되고 게이트웨이가 설치, 구성 및 등록됩니다.

	> [AZURE.NOTE]Internet Explorer 또는 Microsoft ClickOnce 호환 웹 브라우저를 사용하세요.

	![게이트웨이 - 구성 블레이드](./media/data-factory-move-data-between-onprem-and-cloud/OnPremGatewayConfigureBlade.png)

	이는 하나의 단계로 게이트웨이를 다운로드, 설치, 구성 및 등록하는 가장 쉬운 방법(한 번 클릭)입니다. **Microsoft 데이터 관리 게이트웨이 구성 관리자** 응용프로그램이 컴퓨터에 설치된 것을 확인할 수 있습니다. 실행 파일 **ConfigManager.exe**를 **C:\\Program Files\\Microsoft Data Management Gateway\\1.0\\Shared** 폴더에서 찾을 수도 있습니다.

	또한 이 블레이드에서 링크를 클릭하여 게이트웨이를 수동으로 다운로드하여 설치하고 **REGISTER WITH KEY(키를 사용하여 등록)** 텍스트 상자에 표시된 키를 사용하여 등록할 수도 있습니다.
	
	모범 사례, 중요 고려 사항 등을 포함하여 게이트웨이에 대한 자세한 내용은 이 문서의 시작 부분에 있는 섹션을 참조하세요.

	>[AZURE.NOTE]데이터 관리 게이트웨이를 성공적으로 설치 및 구성하려면 로컬 컴퓨터의 관리자여야 합니다. 데이터 관리 게이트웨이 사용자 로컬 Windows 그룹에 사용자를 더 추가할 수 있습니다. 이 그룹의 구성원은 데이터 관리 게이트웨이 구성 관리자 도구를 사용하여 게이트웨이를 구성할 수 있습니다.

5. 몇 분 정도 기다리면 컴퓨터에서 **데이터 관리 게이트웨이 구성 관리자** 응용 프로그램이 시작됩니다. **검색** 창에서 **데이터 관리 게이트웨이**를 입력하여 이 유틸리티에 액세스합니다. **C:\\Program Files\\Microsoft Data Management Gateway\\1.0\\Shared** 폴더에서 **ConfigManager.exe** 실행 파일을 찾을 수도 있습니다.

	![게이트웨이 구성 관리자](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDMGConfigurationManager.png)

6. 값이 다음과 같이 설정될 때까지 기다립니다.
	1. **상태**가 **Started**로 설정됩니다.
	2. **게이트웨이 이름**이 **adftutorialgateway**로 설정됩니다.
	3. **인스턴스 이름**이 **adftutorialgateway**로 설정됩니다.
	4. **등록**이 **Registered**로 설정됩니다.
	5. 맨 아래 상태 표시줄에 **Connected to Data Management Gateway Cloud Service(데이터 관리 게이트웨이 클라우드 서비스에 연결됨)**와 함께 **녹색 확인 표시**가 나타납니다.

8. **인증서** 탭으로 전환합니다. 이 탭에 지정된 인증서는 포털에서 지정한 온-프레미스 데이터 저장소에 대한 자격 증명을 암호화/해독하는 데 사용됩니다. **변경**을 클릭하여 그 대신 사용자 고유의 인증서를 사용합니다. 기본적으로 게이트웨이는 데이터 팩터리 서비스에서 자동으로 생성되는 인증서를 사용합니다.

	![게이트웨이 인증서 구성](./media/data-factory-move-data-between-onprem-and-cloud/gateway-certificate.png)
9. (선택 사항) 게이트웨이와 관련된 모든 문제를 해결하는 데 사용할 수 있는 자세한 정보 표시 로깅을 활성화하려면 **진단** 탭으로 전환하고 **문제 해결용 자세한 정보 표시 로깅 사용** 옵션을 체크합니다. 로깅 정보는 **응용 프로그램 및 서비스 로그** -> **데이터 관리 게이트웨이** 노드 아래의 **이벤트 뷰어**에서 찾을 수 있습니다. 

	![진단 탭](./media/data-factory-move-data-between-onprem-and-cloud/diagnostics-tab.png)

	또한 게이트웨이를 사용하여 온-프레미스 데이터 원본에 대한 **연결을 테스트**하는 데 이 페이지를 사용할 수 있습니다.
10. Azure 포털의 **구성** 블레이드, **새 데이터 게이트웨이** 블레이드에서 차례로 **확인**을 클릭합니다.
6. 왼쪽 트리의 **데이터 게이트웨이** 아래에 **adftutorialgateway**가 표시되어야 합니다. 이 항목을 클릭하면 연결된 JSON이 나타납니다. 
	

### 3단계: 연결된 서비스 만들기 
이 단계에서는 2개의 연결된 서비스 **StorageLinkedService** 및 **SqlServerLinkedService**를 만듭니다. **SqlServerLinkedService**는 온-프레미스 SQL Server 데이터베이스를 연결하며 **StorageLinkedService** 연결 서비스는 Azure Blob 저장소를 데이터 팩터리에 연결합니다. 이 연습의 뒷부분에서는 온-프레미스 SQL Server 데이터베이스에서 Azure Blob 저장소로 데이터를 복사하는 파이프라인을 만듭니다.

#### 온-프레미스 SQL Server 데이터베이스에 연결된 서비스 추가
1.	**데이터 팩터리 편집기**의 도구 모음에서 **새 데이터 저장소**를 클릭하고 **SQL Server**를 선택합니다. 

	![새로운 SQL Server 연결 서비스](./media/data-factory-move-data-between-onprem-and-cloud/NewSQLServer.png) 
3.	**JSON 편집기**에서 다음을 수행합니다. 
	1. **gatewayName**에 **adftutorialgateway**를 지정합니다.	
	2. Windows 인증을 사용하는 경우
		1. **connectionString**에서 
			1. **Integrated Security**를 **true**로 설정합니다.
			2. 데이터베이스 **server name**과 **database name**을 지정합니다. 
			2. **User ID** 및 **Password**를 제거합니다. 
		3. **userName** 및 **password** 속성에 사용자 이름과 암호를 지정합니다.
		
				"typeProperties": {
            		"connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=True;",
            		"gatewayName": "adftutorialgateway",
            		"userName": "<Specify user name if you are using Windows Authentication>",
            		"password": "<Specify password for the user account>"
        		}

	4. SQL 인증을 사용하는 경우
		1. **connectionString**에서 데이터베이스 **server name**, **database name**, **User ID** 및**Password**를 지정합니다.       
		2. 마지막 두 JSON 속성, 즉 **userName** 및 **password**를 JSON에서 제거합니다.
		3. **gatewayName** 속성 값을 지정하는 행의 마지막에서 **,(쉼표)**를 제거합니다. 

				"typeProperties": {
            		"connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;",
	           		"gatewayName": "<Name of the gateway that the Data Factory service should use to connect to the on-premises SQL Server database>"
    		    }
	   
2.	명령 모음에서 **배포**를 클릭하여 SQL Server 연결 서비스를 배포합니다.

#### Azure 저장소 계정에 대한 연결된 서비스 추가
 
1. **데이터 팩터리 편집기**의 명령 모음에서 **새 데이터 집합**을 클릭하고 **Azure 저장소**를 클릭합니다.
2. **계정 이름**에 Azure 저장소 계정의 이름을 입력합니다.
3. **계정 키**에 Azure 저장소 계정의 키를 입력합니다.
4. **배포**를 클릭하여 **StorageLinkedService**를 배포합니다.
   
 
### 4단계: 입력 및 출력 데이터 집합 만들기
이 단계에서는 복사 작업(온-프레미스 SQL Server 데이터베이스 = > Azure Blob 저장소)의 입력 및 출력 데이터를 나타내는 입력 및 출력 데이터 집합을 만듭니다. 데이터 집합 또는 테이블(사각형 데이터 집합)을 만들려면 먼저 다음을 수행해야 합니다(자세한 단계는 목록 다음에 나옴).

- 데이터 팩터리에 연결된 서비스로 추가한 SQL Server 데이터베이스에서 **emp**라는 테이블을 만들고 테이블에 몇 가지 샘플 항목을 삽입합니다.
- 데이터 팩터리에 연결된 서비스로 추가한 Azure BLOB 저장소 계정에서 **adftutorial**로 명명된 BLOB 컨테이너를 만듭니다.

### 자습서에 필요한 온-프레미스 SQL Server 준비

1. 온-프레미스 SQL Server 연결된 서비스(**SqlServerLinkedService**)에 대해 지정된 데이터베이스에서, 다음 SQL 스크립트를 사용하여 데이터베이스에 **emp** 테이블을 만듭니다.


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

1. **데이터 팩터리 편집기**의 명령 모음에서 **새 데이터 집합**을 클릭하고 **SQL Server 테이블**을 클릭합니다. 
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
	- Azure Data Factory의 다른 파이프라인에서 생성되지 않는 입력 테이블의 경우 **external**을 **true**로 설정해야 합니다. 이 섹션은 입력 데이터가 Azure Data Factory 서비스 외부에서 생성되었음을 나타냅니다. **Policy** 섹션에서 **externalData** 요소를 사용하여 외부 데이터 정책을 선택적으로 지정할 수 있습니다.    

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
	- **availability**는 **hourly**(**frequency**는 **hour**로, **interval**은 **1**로 설정)로 설정됩니다. 데이터 팩터리 서비스는 Azure SQL 데이터베이스의 **emp** 테이블에 출력 데이터 조각을 1시간마다 생성합니다. 

	**입력 테이블**의 **fileName**을 지정하지 않는 경우 입력 폴더(**folderPath**)의 모든 파일/Blob이 입력으로 간주됩니다. JSON에서 fileName을 지정하는 경우에는 지정한 파일/Blob만 입력으로 간주됩니다. 예제는 [자습서][adf-tutorial]의 샘플 파일을 참조하세요.
 
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
  

### 5단계: 파이프라인 만들기 및 실행
이 단계에서는 **EmpOnPremSQLTable**을 입력으로, **OutputBlobTable**을 출력으로 사용하는 **복사 작업**을 포함하는 **파이프라인**을 만듭니다.

1.	**데이터 팩터리** 블레이드에서 **작성자 및 배포** 타일을 클릭하여 데이터 팩터리에 대한 **편집기**를 시작합니다.

	![작성 및 배포 타일](./media/data-factory-move-data-between-onprem-and-cloud/author-deploy-tile.png) 
2.	명령 모음에서 **새 파이프라인**을 클릭합니다. 단추가 표시되지 않는 경우 **...(줄임표)**를 클릭하여 명령 모음을 확장합니다.
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
	- **transformation** 섹션에서 **SqlSource**를 **source type**으로 지정하고 **BlobSink**를 **sink type**으로 지정합니다.
	- **SqlSource**의 **sqlReaderQuery** 속성에 대해 SQL 쿼리 **select * from emp**를 지정합니다.

	**start** 속성 값을 현재 날짜로 바꾸고 **end** 값을 다음 날짜로 바꿉니다. start 및 end 날짜/시간은 둘 다 [ISO 형식](http://en.wikipedia.org/wiki/ISO_8601)(영문)이어야 합니다. 예: 2014-10-14T16:32:41Z. **end** 시간은 선택 사항이지만 이 자습서에서는 사용합니다.
	
	**end** 속성 값을 지정하지 않는 경우 "**start + 48시간**"으로 계산됩니다. 파이프라인을 무기한 실행하려면 **end** 속성 값으로 **9/9/9999**를 지정합니다.
	
	각 Azure 데이터 팩터리 테이블에 대해 정의된 **가용성** 속성을 기준으로 데이터 조각이 처리되는 기간이 정의됩니다.
	
	위의 예에서는 각 데이터 조각이 1시간마다 생성되므로 24개 데이터 조각이 있게 됩니다.
	
2. 명령 모음에서 **배포**를 클릭하여 데이터 집합을 배포합니다(테이블은 사각형 데이터 집합임). 제목 표시줄에 **파이프라인이 성공적으로 배포됨** 메시지가 표시되는지 확인합니다.
5. 이제 **X**를 클릭하여 **편집기** 블레이드를 닫습니다. **X**를 다시 클릭하여 도구 모음 및 트리 뷰가 있는 ADFTutorialDataFactory 블레이드를 닫습니다. **저장하지 않은 편집 내용이 삭제됩니다** 메시지가 표시되면 **확인**을 클릭합니다.
6. **ADFTutorialOnPremDF**의 **데이터 팩터리** 블레이드로 돌아갑니다.

**축하합니다.** Azure Data Factory, 연결된 서비스, 테이블 및 파이프라인을 성공적으로 만들고 파이프라인을 예약했습니다.

#### 다이어그램 뷰에서 데이터 팩터리 보기 
1. **Azure Preview 포털**에서 **ADFTutorialOnPremDF** 데이터 팩터리의 홈페이지에 있는 **다이어그램** 타일을 클릭합니다.

	![다이어그램 링크](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDiagramLink.png)

2. 다음과 유사한 다이어그램이 표시됩니다.

	![다이어그램 뷰](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDiagramView.png)

	확대, 축소, 100% 확대, 크기에 맞게, 자동으로 파이프라인 및 테이블 위치 지정, 계보 정보 표시(선택한 항목의 업스트림 및 다운스트림 항목 강조 표시)를 수행할 수 있습니다. 개체(입출력 테이블 또는 파이프라인)를 두 번 클릭하면 해당 속성을 볼 수 있습니다.

### 6단계: 데이터 집합 및 파이프라인 모니터링
이 단계에서는 Azure 포털을 사용하여 Azure Data Factory에서 어떤 일이 일어나는지 모니터링합니다. 또한 PowerShell cmdlet을 사용하여 데이터 집합과 파이프라인을 모니터링할 수도 있습니다. 모니터링에 대한 자세한 내용은 [파이프라인 모니터링 및 관리](monitor-manage-pipelines.md) 문서를 참조하세요.

1. 닫혀 있는 경우 **Azure Preview 포털**로 이동합니다.
2. **ADFTutorialOnPremDF**에 대한 블레이드가 열려있지 않은 경우, **시작 보드**에서 **ADFTutorialOnPremDF**를 클릭하여 엽니다.
3. 만든 테이블과 파이프라인의 **개수** 및 **이름**이 이 블레이드에 표시됩니다.

	![데이터 팩터리 홈 페이지](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDiagramView.png)
4. 이제 **데이터 집합** 타일을 클릭합니다.
5. **데이터 집합** 블레이드에서 **EmpOnPremSQLTable**을 클릭합니다.

	![EmpOnPremSQLTable 조각](./media/data-factory-move-data-between-onprem-and-cloud/OnPremSQLTableSlicesBlade.png)

6. 현재 시간까지의 데이터 조각이 이미 생성되어 **Ready** 상태입니다. SQL Server 데이터베이스에 데이터를 삽입했기 때문이며 데이터가 데이터베이스에 항상 있습니다. 맨 아래의 **Problem slices(문제 조각)** 섹션에 표시되는 조각이 없는지 확인합니다.


	**Recently updated slices(최근에 업데이트된 조각)** 및 **Recently failed slices(최근에 실패한 조각)** 목록은 둘 다 **마지막 업데이트 시간**을 기준으로 정렬됩니다. 조각의 업데이트 시간은 다음과 같은 상황에서 변경됩니다.
    

	-  **Set-AzureDataFactorySliceStatus**를 사용하거나 조각의 **조각** 블레이드에서 **실행**을 클릭하여 수동으로 조각 상태를 업데이트합니다.
	-  실행(예: 실행 시작, 실행 종료 및 실패, 실행 종료 및 성공 등)으로 인해 조각 상태가 변경됩니다.
 
	목록의 제목 또는 **...(줄임표)**을 클릭하여 더 큰 조각 목록을 표시합니다. 도구 모음의 **필터**를 클릭하여 조각을 필터링합니다.
	
	조각 시작/종료 시간을 기준으로 정렬된 데이터 조각을 보려면 **데이터 조각(조각 시간별)** 타일을 클릭합니다.

7. 이제 **데이터 집합** 블레이드에서 **OutputBlobTable**을 클릭합니다.

	![OputputBlobTable slices][image-data-factory-output-blobtable-slices]
8. 현재 시간까지 조각이 생성되었고 **Ready** 상태인지 확인합니다. 현재 시간까지 조각의 상태가 **Ready**로 설정될 때까지 기다립니다.
9. 목록에서 아무 데이터 조각이나 클릭하면 **데이터 조각** 블레이드가 표시됩니다.

	![데이터 조각 블레이드](./media/data-factory-move-data-between-onprem-and-cloud/DataSlice.png)

	조각이 **Ready** 상태가 아닌 경우 **Upstream slices that are not ready(준비되지 않은 업스트림 조각)** 목록에서 Ready 상태가 아니고 현재 조각의 실행을 차단하는 업스트림 조각을 확인할 수 있습니다.

10. 맨 아래 목록에서 **작업 실행**을 클릭하여 **작업 실행 세부 정보**를 표시합니다.

	![활동 실행 세부 정보 블레이드][image-data-factory-activity-run-details]

11. **X**를 클릭하여 모든 블레이드를 닫아
12. **ADFTutorialOnPremDF**의 홈 블레이드로 돌아갑니다.
14. (선택 사항) **파이프라인**을 클릭하고 **ADFTutorialOnPremDF**를 클릭한 다음 입력 테이블(**Consumed**) 또는 출력 테이블(**Produced**)을 드릴스루합니다.
15. **Azure 저장소 탐색기**와 같은 도구를 사용하여 출력을 확인합니다.

	![Azure 저장소 탐색기](./media/data-factory-move-data-between-onprem-and-cloud/OnPremAzureStorageExplorer.png)

## 컴퓨터 간에 게이트웨이 이동
이 섹션에서는 컴퓨터 간에 게이트웨이 클라이언트를 이동하는 단계를 제공합니다.

2. 포털에서 **데이터 팩터리 홈 페이지**로 이동하여 **연결된 서비스** 타일을 클릭합니다. 

	![데이터 게이트웨이 링크](./media/data-factory-move-data-between-onprem-and-cloud/DataGatewaysLink.png) 
3. **연결된 서비스** 블레이드의 **데이터 게이트웨이** 섹션에서 게이트웨이를 선택합니다.
	
	![게이트웨이가 선택된 연결된 서비스 블레이드](./media/data-factory-move-data-between-onprem-and-cloud/LinkedServiceBladeWithGateway.png)
4. **데이터 게이트웨이** 블레이드에서 **데이터 게이트웨이 다운로드 및 설치**를 클릭합니다.
	
	![게이트웨이 다운로드 링크](./media/data-factory-move-data-between-onprem-and-cloud/DownloadGatewayLink.png) 
5. **구성** 블레이드에서 **데이터 게이트웨이 다운로드 및 설치**를 클릭하고 지침에 따라 데이터 게이트웨이를 컴퓨터에 설치합니다. 

	![구성 블레이드](./media/data-factory-move-data-between-onprem-and-cloud/ConfigureBlade.png)
6. **Microsoft 데이터 관리 게이트웨이 구성 관리자**를 열어 둡니다. 
 
	![구성 관리자](./media/data-factory-move-data-between-onprem-and-cloud/ConfigurationManager.png)	
7. 포털의 **구성** 블레이드에서 명령 모음에 있는 **키 다시 만들기**를 클릭하고 경고 메시지가 나타나면 **예**를 클릭합니다. 키 텍스트 옆의 **복사 단추**를 클릭하여 키를 클립보드에 복사합니다. 키를 다시 만드는 즉시 이전 컴퓨터의 게이트웨이가 작동 중지됩니다.  
	
	![키 다시 만들기](./media/data-factory-move-data-between-onprem-and-cloud/RecreateKey.png)
	 
8. 컴퓨터에서 **데이터 관리 게이트웨이 구성 관리자**의 **게이트웨이 등록** 페이지에 있는 텍스트 상자에 **키**를 붙여 넣습니다. (선택 사항) **게이트웨이 키 표시** 확인란을 클릭하여 키 텍스트를 확인합니다.
 
	![키 복사 및 등록](./media/data-factory-move-data-between-onprem-and-cloud/CopyKeyAndRegister.png)
9. **등록**을 클릭하여 클라우드 서비스에 게이트웨이를 등록합니다.
10. **인증서 지정** 페이지에서 **찾아보기**를 클릭하여 이전 게이트웨이에 사용된 것과 동일한 인증서를 선택하고 **암호**를 입력한 다음 **마침**을 클릭합니다. 
 
	![인증서 지정](./media/data-factory-move-data-between-onprem-and-cloud/SpecifyCertificate.png)

	이전 게이트웨이에서 인증서를 내보낼 수 있습니다. 이전 컴퓨터에서 데이터 관리 게이트웨이 구성 관리자를 시작하고 **인증서** 탭으로 전환한 후 **내보내기** 단추를 클릭하고 지침을 따르면 됩니다. 
10. 게이트웨이 등록에 성공하면 게이트웨이 구성 관리자의 홈 페이지에 **등록**이 **등록됨**으로 설정되고 **상태**가 **시작됨**으로 설정됩니다. 

## 자격 증명 및 보안 설정

데이터 팩터리 편집기를 사용하는 대신 연결된 서비스 블레이드를 사용하여 SQL Server 연결 서비스를 만들 수도 있습니다.
 
3.	데이터 팩터리 홈 페이지에서 **연결된 서비스** 타일을 클릭합니다. 
4.	**연결된 서비스** 블레이드의 명령 모음에서 **새 데이터 저장소**를 클릭합니다. 
4.	**이름**에 **SqlServerLinkedService**를 입력합니다. 
2.	**형식** 옆의 화살표를 클릭하고 **SQL Server**를 선택합니다.

	![새 데이터 저장소 만들기](./media/data-factory-move-data-between-onprem-and-cloud/new-data-store.png)
3.	**형식** 설정에서 자세한 설정을 수행해야 합니다.
4.	**데이터 게이트웨이** 설정의 경우 방금 만든 게이트웨이를 선택합니다. 

	![SQL 서버 설정](./media/data-factory-move-data-between-onprem-and-cloud/sql-server-settings.png)
4.	**서버** 설정에 데이터베이스 서버의 이름을 입력합니다.
5.	**데이터베이스** 설정에 데이터베이스의 이름을 입력합니다.
6.	**자격 증명** 옆의 화살표를 클릭합니다.

	![자격 증명 블레이드](./media/data-factory-move-data-between-onprem-and-cloud/credentials-dialog.png)
7.	**자격 증명** 블레이드에서 **자격 증명을 안전하게 설정하려면 여기를 클릭하세요**를 클릭합니다.
8.	**자격 증명 설정** 대화 상자에서 다음을 수행합니다.

	![자격 증명 대화 상자 설정](./media/data-factory-move-data-between-onprem-and-cloud/setting-credentials-dialog.png)
	1.	데이터 팩터리 서비스가 데이터베이스 연결에 사용할 **인증**을 선택합니다. 
	2.	**USERNAME** 설정에 대해 데이터베이스에 액세스할 수 있는 사용자의 이름을 입력합니다. 
	3.	**PASSWORD** 설정에 대해 사용자 암호를 입력합니다.  
	4.	**확인**을 클릭하여 대화 상자를 닫습니다. 
4. **확인**을 클릭하여 **자격 증명** 블레이드를 닫습니다. 
5. **새 데이터 저장소** 블레이드에서 **확인**을 클릭합니다. 	
6. **SqlServerLinkedService**의 상태가 연결된 서비스 블레이드에서 온라인으로 설정되었는지 확인합니다.
	![SQL Server 연결된 서비스 상태](./media/data-factory-move-data-between-onprem-and-cloud/sql-server-linked-service-status.png)

게이트웨이 컴퓨터와 다른 컴퓨터에서 포털에 액세스하는 경우 자격 증명 관리자 응용프로그램이 게이트웨이 컴퓨터에 연결할 수 있는지 확인해야 합니다. 응용프로그램이 게이트웨이 컴퓨터에 연결할 수 없는 경우, 데이터 원본에 대한 자격 증명을 설정하고 데이터 원본에 대한 연결을 테스트할 수 없습니다.

Azure 포털에서 시작된 "자격 증명 설정" 응용 프로그램을 사용하여 온-프레미스 데이터 원본에 대한 자격 증명을 설정하는 경우 포털은 게이트웨이 컴퓨터에서 데이터 관리 게이트웨이 구성 관리자의 인증서 탭에 지정한 인증서를 사용하여 자격 증명을 암호화합니다.

자격 증명을 암호화하기 위한 API 기반 접근 방식을 하는 경우 [New-AzureDataFactoryEncryptValue](https://msdn.microsoft.com/library/azure/dn834940.aspx) PowerShell cmdlet을 사용하여 자격 증명을 암호화합니다. Cmdlet은 해당 게이트웨이 구성하는 인증서를 사용하여 자격 증명을 암호화를 사용합니다. 이 cmdlet에서 반환되는 암호화된 자격을 증명하고 [New-AzureDataFactoryLinkedService](https://msdn.microsoft.com/library/azure/dn820246.aspx) cmdlet 또는 포털의 데이터 팩터리 편집기에서 JSON 조각을 사용하는 JSON 파일에서 connectionString의 EncryptedCredential 요소에 추가할 수 있습니다.

	"connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=True;EncryptedCredential=<encrypted credential>",

**참고:** "자격 증명 설정" 응용 프로그램을 사용하는 경우 위와 같이 연결된 서비스에서 자동으로 암호화된 자격 증명을 설정합니다.

데이터 팩터리 편집기를 사용하여 자격 증명을 설정할 수 있는 방법이 한 가지 더 있습니다. 편집기를 사용하여 SQL 서버 연결된 서비스를 만들고 일반 텍스트에서 자격 증명을 입력하면 게이트웨이가 사용하도록 구성한 인증서가 아니라 데이터 팩터리 서비스를 소유하는 인증서를 사용하는 자격 증명이 암호화됩니다. 경우에 따라 이 방법은 약간 빠를 수 있는 반면에 안전성이 떨어집니다. 따라서 개발/테스트 목적에 대해서만이 방법을 수행하는 것이 좋습니다.


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

	
4. Azure PowerShell에서 **C:\\Program Files\\Microsoft Data Management Gateway\\1.0\\PowerShellScript** 폴더로 전환하고 다음 명령에 표시된 대로 지역 변수 **$Key**와 연결된 **RegisterGateway.ps1** 스크립트를 실행하여 컴퓨터에 설치된 클라이언트 에이전트를 앞에서 만든 논리 게이트웨이에 등록합니다.

		PS C:\> .\RegisterGateway.ps1 $Key.GatewayKey
		
		Agent registration is successful!

5. **Get-AzureDataFactoryGateway** cmdlet을 사용하여 데이터 팩터리의 게이트웨이 목록을 가져올 수 있습니다. **상태**에 **online**이 표시되어 있으면 게이트웨이를 사용할 준비가 되었음을 나타냅니다.

		Get-AzureDataFactoryGateway -DataFactoryName <dataFactoryName> -ResourceGroupName ADF

게이트웨이를 제거하려면 **Remove-AzureDataFactoryGateway** cmdlet을 사용하고 게이트웨이 설명을 업데이트하려면 **Set-AzureDataFactoryGateway** cmdlet을 사용합니다. 이러한 cmdlet에 대한 구문 및 기타 세부 정보는 데이터 팩터리 Cmdlet 참조를 참조하세요.


## 데이터 관리 게이트웨이를 사용하는 복사에 대한 데이터 흐름
데이터 파이프라인에는 복사 활동을 사용하여 온-프레미스 데이터를 클라우드로 수집하거나 결과 데이터를 클라우드에서 온-프레미스 데이터 저장소에 다시 내보내는 경우 복사 작업은 내부적으로 게이트웨이를 사용하여 온-프레미스 데이터 원본에서 클라우드로 그리고 반대로 데이터를 전송합니다.

다음은 높은 수준의 데이터 흐름 및 데이터 게이트웨이를 사용한 복사본에 대한 단계의 요약입니다.
![게이트웨이를 사용하는 데이터 흐름](./media/data-factory-move-data-between-onprem-and-cloud/data-flow-using-gateway.png)

1.	데이터 개발자는 [Azure 포털](http://portal.azure.com) 또는 [PowerShell Cmdlet](https://msdn.microsoft.com/library/dn820234.aspx) 중 하나를 사용하는 Azure Data Factory에 대한 새 게이트웨이를 만듭니다. 
2.	데이터 개발자는 게이트웨이로 "연결된 서비스" 패널을 사용하여 온-프레미스 데이터 저장소에 대한 새 연결 된 서비스를 정의합니다. 연결된 서비스 데이터 설정의 일부로서 개발자는 단계별 연습에 표시된 대로 자격 증명 설정 응용 프로그램을 사용하여 인증 유형 및 자격 증명을 지정합니다. 자격 증명 설정 응용 프로그램 대화 상자는 연결을 테스트하는 데이터 저장소 및 자격 증명을 저장하는 게이트웨이와 통신합니다.
3.	게이트웨이는 클라우드에서 자격 증명을 저장하기 전에 게이트웨이(데이터 개발자가 제공함)에 연결된 인증서로 자격 증명을 암호화합니다.
4.	데이터 팩터리 이동 서비스는 공유 Azure 서비스 버스 큐를 사용하는 컨트롤 채널을 통해 작업의 예정 및 관리에 대한 게이트웨이와 통신합니다. 복사 작업이 시작되어야 할 경우 데이터 팩터리는 자격 증명 정보와 함께 요청을 나열합니다. 게이트웨이는 큐를 폴링한 후에 작업을 시작합니다.
5.	게이트웨이는 동일한 인증서로 자격 증명의 암호를 해독하고 적절한 인증 형식으로 온-프레미스 데이터 저장소에 연결합니다.
6.	게이트웨이는 데이터 파이프라인에서 복사 활동을 구성하는 방법에 따라 온-프레미스 저장소에서 클라우드 저장소에 또는 클라우드 저장소에서 온-프레미스 데이터 저장소에 데이터를 복사합니다. 참고: 이 단계의 경우 게이트웨이는 보안(HTTPS) 채널을 통해 클라우드 기반 저장소 서비스(예: Azure Blob, Azure SQL 등)와 직접 통신합니다.

### 포트 및 보안 고려 사항

1. 위에서 언급한 것처럼 단계별 연습에서 데이터 팩터리를 사용하여 온-프레미스 데이터 저장소에 대한 자격 증명을 설정하는 여러 방법이 있습니다. 포트 고려 사항은 이러한 옵션에 따라 달라집니다.	

	- **자격 증명 설정** 앱 사용: 기본적으로 데이터 관리 게이트웨이 설치 프로그램이 로컬 windows 방화벽에서 게이트웨이 컴퓨터에 **8050** 및 **8051** 포트를 엽니다. 이러한 포트는 자격 증명 설정 응용 프로그램에서 사용하여 게이트웨이에 자격 증명을 릴레이합니다. 로컬 windows 방화벽에서 컴퓨터에 이러한 포트를 엽니다. 인터넷에서 연결될 수는 없으며, 회사 범위의 방화벽에서 해당 포트를 열 필요도 없습니다.
	2.	[New-AzureDataFactoryEncryptValue](https://msdn.microsoft.com/library/dn834940.aspx) powershell commandlet을 사용합니다. a. Powershell 명령을 사용하여 자격 증명을 암호화하고 결과적으로 게이트웨이 설치하여 windows 방화벽의 게이트웨이 컴퓨터에서 인바운드 포트를 열기를 원하지 않으면 설치하는 동안 다음 명령을 사용하여 수행할 수 있습니다.
	
			msiexec /q /i DataManagementGateway.msi NOFIREWALL=1
3.	**자격 증명 설정** 응용 프로그램을 사용하면 데이터 원본에 대한 자격 증명을 설정하고 데이터 원본에 대한 연결을 테스트하려면 데이터 관리 게이트웨이에 연결할 수 있는 컴퓨터에서 시작해야 합니다.
4.	온-프레미스 SQL Server 데이터베이스와 Azure SQL 데이터베이스 간에 데이터를 복사하는 경우 다음 사항을 확인합니다.	
	- 	게이트웨이 컴퓨터의 방화벽이 **TCP** 포트 **1433**에서 보내는 TCP 통신을 허용합니다.
	- 	**허용된 IP 주소**에 **게이트웨이 컴퓨터의 IP 주소**를 추가하도록 [Azure SQL 방화벽 설정](https://msdn.microsoft.com/library/azure/jj553530.aspx)을 구성합니다.
5.	온-프레미스 SQL Server와 대상 간에 데이터를 복사하고 게이트웨이와 SQL Server 컴퓨터가 서로 다른 경우 다음을 수행 합니다. 게이트웨이가 SQL Server 인스턴스에서 수신 대기하는 포트를 통해 데이터베이스에 액세스할 수 있도록 SQL Server 컴퓨터의 [Windows 방화벽을 구성](https://msdn.microsoft.com/library/ms175043.aspx)합니다. 기본 인스턴스의 경우 포트 1433입니다.

<!---HONumber=Nov15_HO3-->