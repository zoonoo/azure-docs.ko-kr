<properties 
	pageTitle="Azure 저장소 | Microsoft Azure에 대한 구성 문자열 구성" 
	description="Azure 저장소 계정에 연결 문자열을 구성하는 방법에 대해 알아봅니다. 연결 문자열에는 저장소 계정의 리소스에 대한 프로그래밍 방식 액세스를 인증 하는 데 필요한 정보가 포함되어 있습니다. 연결 문자열은 자신이 소유한 계정에 대한 계정 액세스 키를 캡슐화하거나 액세스 키 없이 계정의 리소스에 액세스하기 위한 공유 액세스 서명을 포함할 수 있습니다." 
	services="storage" 
	documentationCenter="" 
	authors="tamram" 
	manager="adinah" 
	editor="cgronlun"/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/08/2015" 
	ms.author="tamram"/>

# Azure 저장소 연결 문자열 구성

## 개요

연결 문자열에는 Azure 저장소에 프로그래밍 방식으로 액세스하는 데 필요한 정보가 포함되어 있습니다. 사용자의 응용 프로그램은 연결 문자열을 사용하여 Azure 저장소에 액세스를 인증하는 데 필요한 정보를 제공합니다.

다음과 같은 작업을 수행하도록 연결 문자열을 구성할 수 있습니다.

- 서비스 또는 응용 프로그램을 로컬로 테스트하면서 Azure 저장소 에뮬레이터에 연결합니다.
- 저장소 서비스에 대한 기본 끝점 또는 사용자가 정의한 명시적 끝점을 사용하여 Azure의 저장소 계정에 연결합니다.
- 공유 액세스 서명(SAS)를 통해 저장소 계정의 리소스에 액세스합니다.

## 사용자의 연결 문자열 저장

사용자의 응용 프로그램이 실행 중일 때 Azure 저장소 액세스를 인증하려면 연결 문자열을 저장해야 합니다. 연결 문자열을 저장하기 위한 몇 가지 옵션이 있습니다.

- 데스크톱 또는 장치에서 실행 중인 응용 프로그램의 경우 연결 문자열을 app.config 파일 또는 다른 구성 파일에 저장할 수 있습니다. App.config 파일을 사용하는 경우 연결 문자열을 **AppSettings** 섹션에 추가합니다.
- Azure 클라우드 서비스에서 실행 중인 응용 프로그램의 경우, 연결 문자열을 [Azure 서비스 구성 스키마(.cscfg) 파일](https://msdn.microsoft.com/library/ee758710.aspx)에 저장할 수 있습니다. 연결 문자열을 서비스 구성 파일의 **ConfigurationSettings** 섹션에 추가합니다.

사용자의 연결 문자열을 구성 파일 내에 저장하면 연결 문자열을 업데이트하여 저장소 에뮬레이터와 클라우드의 Azure 저장소 계정 사이에 전환하기 쉽습니다. 저장소 계정을 가리키도록 연결 문자열을 편집하기만 하면 됩니다.

Azure [CloudConfigurationManager](https://msdn.microsoft.com/library/microsoft.windowsazure.cloudconfigurationmanager.aspx) 클래스를 사용하여 응용 프로그램이 실행 중인 위치와 상관없이 런타임에 사용자의 연결 문자열에 액세스할 수 있습니다.

## 저장소 에뮬레이터에 연결 문자열 만들기

[AZURE.INCLUDE [저장소-에뮬레이터-연결-문자열-포함](../../includes/storage-emulator-connection-string-include.md)]

저장소 에뮬레이터에 대한 자세한 내용은 [개발 및 테스트를 위한 Azure 저장소 에뮬레이터 사용](storage-use-emulator.md)을 참조하세요.

## Azure 저장소 계정에 연결 문자열 만들기

Azure 저장소 계정에 연결 문자열을 만들려면 아래 연결 문자열 형식을 사용 합니다. HTTP 또는 HTTPS(권장)를 통해 저장소 계정에 연결할지 여부를 나타내며, `myAccountName`을 저장소 계정의 이름으로 바꾸고, `myAccountKey`를 계정 액세스 키로 바꿉니다.

    DefaultEndpointsProtocol=[http|https];AccountName=myAccountName;AccountKey=myAccountKey

예를 들어 연결 문자열은 다음의 샘플 연결 문자열과 같이 표시될 수 있습니다.
 
	DefaultEndpointsProtocol=https;
	AccountName=storagesample;
	AccountKey=<account-key>

> [AZURE.NOTE]Azure 저장소는 연결 문자열에서 HTTP 및 HTTPS를 모두 지원하지만 HTTPS를 사용하는 것이 좋습니다.
    
## 명시적 저장소 끝점에 대한 연결 문자열 만들기

연결 문자열에서 서비스 끝점을 명시적으로 지정할 수 있습니다.

- 저장소 계정용 사용자 지정 도메인 이름을 Blob 서비스에 매핑합니다.
- 저장소 계정으로 저장소 리소스에 액세스하기 위한 공유 액세스 서명을 보유합니다.

명시적 Blob 끝점을 지정하는 연결 문자열을 만들려면 다음 형식으로 프로토콜 사양(HTTP 또는 HTTPS)을 포함하는 전체 서비스 끝점을 각 서비스에 대해 지정합니다.

	BlobEndpoint=myBlobEndpoint;
	QueueEndpoint=myQueueEndpoint;
	TableEndpoint=myTableEndpoint;
	FileEndpoint=myFileEndpoint;
	[credentials]


서비스 끝점은 하나 이상 지정해야 하지만 모두 지정할 필요는 없습니다. 예를 들어 사용자 지정 Blob 끝점에 사용할 연결 문자열을 만들 때는 큐 및 테이블 끝점은 필요한 경우에만 지정하면 됩니다. 연결 문자열에서 큐 및 테이블 끝점을 생략하면 해당 연결 문자열을 사용하여 코드에서 큐 및 테이블 서비스에 액세스할 수 없습니다.

연결 문자열에서 명시적으로 서비스 끝점을 지정한 경우 위 문자열에 `credentials`을 지정하는 두 가지 옵션이 있습니다.

- 계정 이름 및 키를 지정할 수 있습니다. `AccountName=myAccountName;AccountKey=myAccountKey` 
- 공유 액세스 서명을 지정할 수 있습니다. `SharedAccessSignature=base64Signature`

### 사용자 지정 도메인 이름을 사용하여 Blob 끝점 지정 

Blob 서비스에 사용할 사용자 지정 도메인 이름을 등록한 경우에는 연결 문자열에서 Blob 끝점을 명시적으로 구성할 수 있습니다. 연결 문자열에 표시되는 끝점 값은 Blob 서비스의 요청 URI를 생성하는 데 사용되며 코드로 반환되는 URI의 형식을 지정합니다.

예를 들어, 사용자 지정 도메인의 Blob 끝점에 대한 연결 문자열은 다음과 유사할 수 있습니다.

	DefaultEndpointsProtocol=https;
	BlobEndpoint=www.mydomain.com;
	AccountName=storagesample;
	AccountKey=<account-key> 


### 공유 액세스 서명을 사용하여 Blob 끝점 지정 

공유 액세스 서명을 통해 저장소 리소스에 액세스하기 위해 명시적 끝점을 사용하는 연결 문자열을 만들 수 있습니다. 이 경우 계정 이름 및 키 자격 증명 대신 연결 문자열의 일부로 공유 액세스 서명을 지정할 수 있습니다. 공유 액세스 서명 토큰은 액세스할 리소스, 해당 리소스를 사용할 수 있는 기간 및 부여되는 권한에 대한 정보를 캡슐화합니다. 공유 액세스 서명에 대한 자세한 내용은 [공유 액세스 서명을 사용하여 액세스 위임](https://msdn.microsoft.com/library/ee395415.aspx)을 참조하세요.

공유 액세스 서명을 포함하는 연결 문자열을 만들려면 다음과 같은 형식의 문자열을 지정합니다.

    BlobEndpoint=myBlobEndpoint; QueueEndpoint=myQueueEndpoint;TableEndpoint=myTableEndpoint;SharedAccessSignature=base64Signature

끝점은 기본 서비스 끝점 또는 사용자 지정 끝점일 수 있습니다. 여기서 `base64Signature`은 공유 액세스 서명의 서명 부분에 해당합니다. 이 서명은 SHA256 알고리즘을 사용하여 서명할 유효한 문자열과 키를 통해 계산되고 Base64로 인코딩되는 HMAC입니다.

### 끝점 접미사를 사용하여 연결 문자열 만들기

중국 Azure 또는 Azure 관리 방침과 같이 다른 끝점 접미사를 사용하여 지역이나 인스턴스에 저장소 서비스에 대한 연결 문자열을 만들려면 다음 연결 문자열 형식을 사용합니다. IHTTP 또는 HTTPS를 통해 저장소 계정에 연결할지 여부를 표시하며, `myAccountName`을 저장소 계정의 이름으로 바꾸고, `myAccountKey`를 계정 액세스 키로 바꾸고, `mySuffix`을 URI 접미사로 바꿉니다.


	DefaultEndpointsProtocol=[http|https];AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=mySuffix;


예를 들어 연결 문자열은 다음의 샘플 연결 문자열과 같이 표시될 수 있습니다.

	DefaultEndpointsProtocol=https;
	AccountName=storagesample;
	AccountKey=<account-key>;
	EndpointSuffix=core.chinacloudapi.cn;


 

<!---HONumber=July15_HO4-->