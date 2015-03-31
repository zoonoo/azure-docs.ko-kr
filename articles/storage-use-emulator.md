<properties 
	pageTitle="개발 및 테스트에 Azure 저장소 에뮬레이터 사용" 
	description="개발 및 테스트에 Azure 저장소 에뮬레이터를 사용하는 방법에 대해 알아봅니다." 
	services="storage" 
	documentationCenter="" 
	authors="tamram" 
	manager="adinah" 
	editor=""/>
<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/20/2015" 
	ms.author="tamram"/>

# 개발 및 테스트에 Azure 저장소 에뮬레이터 사용

## 개요
Microsoft Azure 저장소 에뮬레이터는 개발 목적으로 Azure Blob, 큐 및 테이블 서비스를 에뮬레이트하는 로컬 환경을 제공합니다. 이 저장소 에뮬레이터를 사용하면 비용을 발생시키지 않고도 로컬에서 저장소 서비스에 대해 응용 프로그램을 테스트할 수 있습니다.

> [AZURE.NOTE] 저장소 에뮬레이터는 Microsoft Azure SDK의 일부로 제공됩니다. 독립 실행형 패키지로 저장소 에뮬레이터를 설치할 수도 있습니다.
저장소 에뮬레이터를 구성하려면 컴퓨터에 대한 관리 권한이 있어야 합니다. 
> 저장소 에뮬레이터의 어느 한 버전으로 작성한 데이터에 다른 버전으로 반드시 액세스할 수 있는 것은 아닙니다. 장기간 보존하려는 데이터는 저장소 에뮬레이터가 아닌 Azure 저장소 계정에 저장하는 것이 좋습니다.
 
저장소 에뮬레이터와 Azure 저장소 서비스 간에는 몇 가지 차이점이 있습니다. 이러한 차이점에 대한 자세한 내용은 [저장소 에뮬레이터와 Azure 저장소 서비스의 차이점](https://msdn.microsoft.com/library/azure/gg433135.aspx)을 참조하세요.

저장소 에뮬레이터는 Microsoft(r) SQL Server(tm) 인스턴스와 로컬 파일 시스템을 사용하여 Azure 저장소 서비스를 에뮬레이트합니다. 기본적으로 저장소 에뮬레이터는 Microsoft(r) SQL Server(tm) 2012 Express LocalDB의 데이터베이스용으로 구성됩니다. SQL Server Management Studio Express를 설치하여 LocalDB 설치를 관리할 수 있습니다. 저장소 에뮬레이터는 Windows 인증을 사용하여 SQL Server 또는 LocalDB에 연결합니다. 저장소 에뮬레이터 명령줄 도구 참조를 사용하여 저장소 에뮬레이터가 LocalDB가 아닌 SQL Server의 로컬 인스턴스에 액세스하도록 구성할 수 있습니다.

## 요청 인증

저장소 에뮬레이터는 단일 고정 계정과 알려진 인증 키만 지원합니다. 저장소 에뮬레이터에서 사용할 수 있는 자격 증명은 이 계정과 키뿐입니다. 아래에 이 계정과 키의 예제가 나와 있습니다.

    Account name: devstoreaccount1
    Account key: Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtrKBHBeksoGMGw==
    
> [AZURE.NOTE] 저장소 에뮬레이터에서 지원하는 인증 키는 클라이언트 인증 코드의 기능을 테스트할 때만 사용할 수 있으며 보안 기능은 제공하지 않습니다. 프로덕션 저장소 계정과 키를 저장소 에뮬레이터에서 사용할 수는 없습니다. 또한 개발 계정을 프로덕션 데이터에 사용해서는 안 됩니다.
 

## 저장소 에뮬레이터 시작 및 초기화
Azure 저장소 에뮬레이터를 시작하려면 시작 단추를 선택하거나 Windows 키를 누릅니다. Azure 저장소 에뮬레이터 입력을 시작하고 응용 프로그램 목록에서 Azure 저장소 에뮬레이터를 선택합니다. 

Azure 계산 에뮬레이터가 이미 실행되고 있는 경우 시스템 트레이 아이콘을 마우스 오른쪽 단추로 클릭하고 저장소 에뮬레이터 시작을 선택하여 저장소 에뮬레이터를 시작할 수도 있습니다. 계산 에뮬레이터를 실행하는 방법에 대한 자세한 내용은 [계산 에뮬레이터에서 Azure 응용 프로그램 실행](https://msdn.microsoft.com/library/azure/hh403990.aspx)을 참조하세요.

저장소 에뮬레이터가 시작되면 명령줄이 표시됩니다. 이 명령줄을 사용하여 저장소 에뮬레이터를 시작/중지하고, 데이터를 지우고, 최신 상태를 가져오고, 에뮬레이터를 초기화할 수 있습니다. 자세한 내용은 [저장소 에뮬레이터 명령줄 도구 참조](https://msdn.microsoft.com/library/azure/gg433005.aspx)를 참조하세요.

명령줄 창을 닫으면 저장소 에뮬레이터가 계속 실행됩니다. 명령줄을 다시 불러오려면 저장소 에뮬레이터를 시작하는 것처럼 위의 단계를 수행합니다.


저장소 에뮬레이터를 처음 실행하면 로컬 저장소 환경이 자동으로 초기화됩니다. 저장소 에뮬레이터 명령줄 도구를 사용하여 다른 데이터베이스 인스턴스를 가리키거나 기존 데이터베이스를 다시 초기화할 수 있습니다. 초기화 프로세스에서는 LocalDB에 데이터베이스가 작성되며 각 로컬 저장소 서비스용으로 HTTP 포트가 예약됩니다. 이 단계를 수행하려면 관리 권한이 필요합니다. 자세한 내용은 [저장소 에뮬레이터 명령줄 도구 참조](https://msdn.microsoft.com/library/azure/gg433005.aspx)를 참조하세요.

## 저장소 서비스 URI 정보

Azure 저장소 서비스에서 리소스 주소를 지정하는 방법은 리소스의 위치(Azure 또는 저장소 에뮬레이터 서비스)에 따라 달라집니다. Azure와 저장소 에뮬레이터에서 저장소 리소스 주소를 지정할 때는 각각 다른 URI 체계가 사용됩니다. 이처럼 서로 다른 URI가 사용되는 이유는 로컬 컴퓨터에서 도메인 이름 확인을 수행하지 않기 때문입니다. 두 URI 체계는 항상 계정 이름과 요청하는 리소스의 주소를 포함합니다.

## 클라우드에서 Azure 저장소 리소스의 주소 지정

Azure에서 저장소 리소스 주소를 지정하는 URI 체계의 경우 계정 이름은 URI 호스트 이름의 일부이고 주소를 지정하는 리소스는 URI 경로의 일부입니다. 저장소 리소스에 액세스할 때는 다음과 같은 기본 주소 지정 체계를 사용합니다.

    <http|https>://<account-name>.<service-name>.core.windows.net/<resource-path>


`<account-name>`은 저장소 계정의 이름입니다. `<service-name>`은 액세스하는 서비스의 이름이고 `<resource-path>`는 요청하는 리소스의 경로입니다. 다음 목록에는 각 저장소 서비스용 URI 체계가 나와 있습니다.

	Blob Service: <http|https>://<account-name>.blob.core.windows.net/<resource-path>
	Queue Service: <http|https>://<account-name>.queue.core.windows.net/<resource-path>
	Table Service: <http|https>://<account-name>.table.core.windows.net/<resource-path>

예를 들어 클라우드의 Blob에 액세스하려는 경우 다음 주소를 사용할 수 있습니다.
    
    http://myaccount.blob.core.windows.net/mycontainer/myblob.txt

> [AZURE.NOTE] 사용자 지정 도메인 이름을 클라우드의 Blob 저장소 끝점에 연결한 다음 Blob 및 컨테이너 주소를 지정하는 데 사용할 수 있습니다. 
 
## 저장소 에뮬레이터에서 로컬 Azure 저장소 리소스의 주소 지정

저장소 에뮬레이터에서는 로컬 컴퓨터가 도메인 이름 확인을 수행하지 않기 때문에 계정 이름이 URI 경로에 포함됩니다. 저장소 에뮬레이터에서 실행되는 리소스의 URI 체계는 다음 형식을 따릅니다.

    http://<local-machine-address>:<port>/<account-name>/<resource-path>

다음 형식을 사용하여 저장소 에뮬레이터에서 실행되는 리소스의 주소를 지정합니다.

	Blob Service: http://127.0.0.1:10000/<account-name>/<resource-path>
	Queue Service: http://127.0.0.1:10001/<account-name>/<resource-path>
	Table Service: http://127.0.0.1:10002/<account-name>/<resource-path>

예를 들어 저장소 에뮬레이터의 Blob에 액세스하려는 경우 다음 주소를 사용할 수 있습니다.

    http://127.0.0.1:10000/myaccount/mycontainer/myblob.txt

> [AZURE.NOTE] 로컬 저장소 리소스의 주소를 지정하는 프로토콜로 HTTPS를 사용할 수는 없습니다.
 
## RA-GRS를 사용하여 보조 계정 주소 지정
버전 3.1부터는 저장소 에뮬레이터 계정에 대해 RA-GRS(읽기 권한 지역 중복 복제)를 사용할 수 있습니다. 클라우드와 로컬 에뮬레이터의 저장소 리소스에 대해 계정 이름에 -secondary를 추가하면 보조 위치에 액세스할 수 있습니다. 예를 들어 저장소 에뮬레이터에서 읽기 전용 보조 계정을 사용하여 Blob에 액세스하려는 경우 다음 주소를 사용할 수 있습니다.

    http://127.0.0.1:10000/myaccount-secondary/mycontainer/myblob.txt 

> [AZURE.NOTE] 저장소 에뮬레이터를 사용하여 프로그래밍 방식으로 보조 계정에 액세스하려는 경우 .NET용 저장소 클라이언트 라이브러리 버전 3.2 이상을 사용합니다. 자세한 내용은 저장소 클라이언트 라이브러리 참조를 참조하세요.
 
## 명령줄 도구를 사용하여 저장소 에뮬레이터 초기화
저장소 에뮬레이터 명령줄 도구를 사용하여 기본 인스턴스가 아닌 다른 데이터베이스 인스턴스를 가리키도록 저장소 에뮬레이터를 초기화할 수 있습니다. 기본 LocalDB 데이터베이스 인스턴스를 사용하려는 경우에는 별도의 초기화 단계를 실행하지 않아도 됩니다.

이 도구는 기본적으로 C:\Program Files(x86)\Microsoft SDKs\Azure\Storage Emulator\ 디렉터리에 설치됩니다. 초기화는 에뮬레이터를 처음 시작할 때 자동으로 실행됩니다.

> [AZURE.NOTE] 아래에서 설명하는 'init' 명령을 실행하려면 관리 권한이 필요합니다.

1. **시작** 단추를 클릭하거나 **Windows** 키를 누릅니다. `Azure 저장소 에뮬레이터` 입력을 시작하여 Azure 저장소 에뮬레이터가 표시되면 선택합니다. 명령 프롬프트 창이 표시됩니다.


2. 명령 프롬프트 창에 다음 명령을 입력합니다. 여기서 `<SQLServerInstance>`는 SQL Server 인스턴스의 이름입니다. LocalDb를 사용하려면 SQL Server 인스턴스로 `(localdb)\v11.0`을 지정합니다.

    WAStorageEmulator init /sqlInstance <SQLServerInstance> 
    
기본 SQL Server 인스턴스를 사용하도록 에뮬레이터에 지시하는 다음 명령을 사용할 수도 있습니다.

    WAStorageEmulator init /server .\\ 

또는 데이터베이스를 다시 초기화하는 다음 명령을 사용할 수도 있습니다.

    WAStorageEmulator init /forceCreate 

## 저장소 에뮬레이터 명령줄 도구 참조

버전 3.0부터는 저장소 에뮬레이터를 시작하면 명령 프롬프트 팝업이 표시됩니다. 명령 프롬프트를 사용하여 에뮬레이터를 시작/중지할 수 있으며 상태를 쿼리하고 기타 작업을 수행할 수도 있습니다.

> [AZURE.NOTE] 계산 에뮬레이터가 설치되어 있으면 저장소 에뮬레이터를 시작할 때 시스템 트레이 아이콘이 표시됩니다. 해당 아이콘을 마우스 오른쪽 단추로 클릭하면 표시되는 메뉴에서 그래픽 방식으로 저장소 에뮬레이터를 시작하고 중지할 수 있습니다.

### 명령줄 구문

	WAStorageEmulator [/start] [/stop] [/status] [/clear] [/init] [/help]

### 옵션
옵션 목록을 보려면 명령 프롬프트에 '/help'를 입력합니다.

## 다음 단계
- [저장소 에뮬레이터 명령줄 도구 참조](https://msdn.microsoft.com/library/azure/gg433005.aspx)
- [저장소 에뮬레이터와 Azure 저장소 서비스의 차이점](https://msdn.microsoft.com/library/azure/gg433135.aspx)
- [저장소 에뮬레이터 릴리스 정보](https://msdn.microsoft.com/library/azure/dn683879.aspx)

<!--HONumber=47-->
