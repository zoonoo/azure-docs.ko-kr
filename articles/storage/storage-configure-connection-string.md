<properties 
	pageTitle="Azure 연결 문자열 구성" 
	description="Azure에서 저장소 계정의 연결 문자열을 구성하는 방법에 대해 알아봅니다." 
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
	ms.date="02/20/2015" 
	ms.author="tamram"/>

# Azure 저장소 연결 문자열 구성

## 개요

연결 문자열에는 Azure에서 저장소 계정에 프로그래밍 방식으로 액세스하는데 필요한 정보가 포함됩니다. 다음과 같은 방법으로 Azure 저장소에 연결하도록 연결 문자열을 구성할 수 있습니다.

- 서비스 또는 응용 프로그램을 로컬로 테스트하면서 Azure 저장소 에뮬레이터에 연결합니다.

- 저장소 서비스의 기본 끝점을 사용하여 Azure에서 저장소 계정에 연결합니다.

- 저장소 서비스의 명시적 끝점을 사용하여 Azure에서 저장소 계정에 연결합니다.

응용 프로그램이 Azure에서 실행 중인 클라우드 서비스인 경우, 일반적으로 [Azure 서비스 구성 스키마(.cscfg) 파일](https://msdn.microsoft.com/library/ee758710.aspx)에 연결 문자열을 저장합니다. 응용 프로그램이 데스크톱 등의 다른 환경에서 실행되는 경우에는 연결 문자열을 app.config 파일 또는 다른 구성 파일에 저장할 수 있습니다. Azure `CloudConfigurationManager` 클래스를 사용하여 실행 위치와 상관없이 런타임 시 연결 문자열에 액세스할 수 있습니다.

## 저장소 에뮬레이터에 연결 문자열 만들기

저장소 에뮬레이터에는 알려진 이름 및 키를 사용하는 로컬 계정입니다. 계정 이름과 키는 모든 사용자에 대해 동일하므로 바로 가기 문자열 형식을 사용하여 연결 문자열 내에서 저장소 에뮬레이터를 참조할 수 있습니다. 연결 문자열의 값을  `UseDevelopmentStorage=true`로 설정합니다.

저장소 에뮬레이터에 대해 서비스를 테스트할 때 사용할 HTTP 프록시를 지정할 수도 있습니다. 이렇게 하면 저장소 서비스에 대한 작업을 디버깅하면서 HTTP 요청과 응답을 관찰하는 데 유용할 수 있습니다. 프록시를 지정하려면 `DevelopmentStorageProxyUri` 옵션을 연결 문자열에 추가하고 해당 값을 프록시 URI로 설정합니다. 예를 들어 저장소 에뮬레이터를 가리키고 HTTP 프록시를 구성하는 연결 문자열은 다음과 같습니다.

    UseDevelopmentStorage=true;DevelopmentStorageProxyUri=http://myProxyUri

## Azure 저장소 계정에 연결 문자열 만들기

Azure 저장소 계정에 연결 문자열을 만들려면 아래 연결 문자열 형식을 사용 합니다. HTTP 또는 HTTPS를 통해(권장) 저장소 계정에 연결할 지 여부를 표시하며  `myAccountName`을 저장소 계정의 이름으로 바꾸고  `myAccountKey`를 계정 액세스 키로 바꿉니다.

    DefaultEndpointsProtocol=[http|https];AccountName=myAccountName;AccountKey=myAccountKey

예를 들어 연결 문자열은 다음의 샘플 연결 문자열과 같이 표시될 수 있습니다.

```        DefaultEndpointsProtocol=https;AccountName=storagesample;AccountKey=KWPLd0rpW2T0U7K2pVpF8rYr1BgYtB7wYQw33AYiXeUoquiaY6o0TWqduxmPHlqeCNZ3LU0DHptbeIAy5l/Yhg==
```

> [AZURE.NOTE] Azure 저장소는 연결 문자열에서 HTTP 및 HTTPS를 모두 지원하지만 HTTPS를 사용하는 것이 좋습니다.
    
## 명시적 저장소 끝점에 연결 문자열 만들기

연결 문자열에서 서비스 끝점을 명시적으로 지정할 수 있습니다.

- 저장소 계정용 사용자 지정 도메인 이름을 Blob 서비스에 매핑합니다.
- 저장소 계정으로 저장소 리소스에 액세스하기 위한 공유 액세스 서명을 보유합니다.

명시적 Blob 끝점을 지정하는 연결 문자열을 만들려면 다음 형식으로 프로토콜 사양(HTTP 또는 HTTPS)을 포함하는 전체 서비스 끝점을 각 서비스에 대해 지정합니다.

``` 
BlobEndpoint=myBlobEndpoint;QueueEndpoint=myQueueEndpoint;TableEndpoint=myTableEndpoint;FileEndpoint=myFileEndpoint;[credentials]
```

서비스 끝점은 하나 이상 지정해야 하지만 모두 지정할 필요는 없습니다. 예를 들어 사용자 지정 Blob 끝점에 사용할 연결 문자열을 만들 때는 큐 및 테이블 끝점은 필요한 경우에만 지정하면 됩니다. 연결 문자열에서 큐 및 테이블 끝점을 생략하면 해당 연결 문자열을 사용하여 코드에서 큐 및 테이블 서비스에 액세스할 수 없습니다.

연결 문자열에서 명시적으로 서비스 끝점을 지정한 경우 위 문자열에  `credentials`을 지정하는 두 가지 옵션이 있습니다.

- 계정 이름 및 키를 지정할 수 있습니다. `AccountName=myAccountName;AccountKey=myAccountKey` 
- 공유 액세스 서명을 지정할 수 있습니다. `SharedAccessSignature=base64Signature`

### 사용자 지정 도메인 이름을 사용하여 Blob 끝점 지정 

Blob 서비스에 사용할 사용자 지정 도메인 이름을 등록한 경우에는 연결 문자열에서 Blob 끝점을 명시적으로 구성할 수 있습니다. 연결 문자열에 표시되는 끝점 값은 Blob 서비스의 요청 URI를 생성하는 데 사용되며 코드로 반환되는 URI의 형식을 지정합니다. 

예를 들어, 사용자 지정 도메인의 Blob 끝점에 대한 연결 문자열은 다음과 유사할 수 있습니다.

```
DefaultEndpointsProtocol=https;BlobEndpoint=www.mydomain.com;AccountName=storagesample;AccountKey=KWPLd0rpW2T0U7K2pVpF8rYr1BgYtB7wYQw33AYiXeUoquiaY6o0TWqduxmPHlqeCNZ3LU0DHptbeIAy5l/Yhg== 
```

### 공유 액세스 서명을 사용하여 Blob 끝점 지정 

공유 액세스 서명을 통해 저장소 리소스에 액세스하기 위해 명시적 끝점을 사용하는 연결 문자열을 만들 수 있습니다. 이 경우 계정 이름 및 키 자격 증명 대신 연결 문자열의 일부로 공유 액세스 서명을 지정할 수 있습니다. 공유 액세스 서명 토큰은 액세스할 리소스, 해당 리소스를 사용할 수 있는 기간 및 부여되는 권한에 대한 정보를 캡슐화합니다. 공유 액세스에 대한 자세한 내용은 [공유 액세스 서명을 사용하여 액세스 위임](https://msdn.microsoft.com/library/ee395415.aspx)을 참조하세요.

공유 액세스 서명을 포함하는 연결 문자열을 만들려면 다음과 같은 형식의 문자열을 지정합니다.

    BlobEndpoint=myBlobEndpoint; QueueEndpoint=myQueueEndpoint;TableEndpoint=myTableEndpoint;SharedAccessSignature=base64Signature

끝점은 기본 서비스 끝점 또는 사용자 지정 끝점일 수 있습니다. 여기서 `base64Signature`는 공유 액세스 서명의 서명 부분에 해당합니다. 이 서명은 SHA256 알고리즘을 사용하여 서명할 유효한 문자열과 키를 통해 계산되고 Base64로 인코딩되는 HMAC입니다.

### 끝점 접미사를 사용하여 연결 문자열 만들기

중국 Azure 또는 Azure 관리 방침과 같이 다른 끝점 접미사를 사용하여 지역이나 인스턴스에 저장소 서비스에 대한 연결 문자열을 만들려면 다음 연결 문자열 형식을 사용합니다. IHTTP 또는 HTTPS를 통해 저장소 계정에 연결할 지 여부를 표시하며,  `myAccountName`을 저장소 계정의 이름으로 바꾸고,  `myAccountKey`를 계정 액세스 키로 바꾸고  `mySuffix`를 URI 접미사로 바꿉니다.


	DefaultEndpointsProtocol=[http|https];AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=mySuffix;


예를 들어 연결 문자열은 다음의 샘플 연결 문자열과 같이 표시될 수 있습니다.

	DefaultEndpointsProtocol=https;AccountName=storagesample;AccountKey=KWPLd0rpW2T0U7K2pVpF8rYr1BgYtR7wYQk33AYiXeUoquiaY6o0TWqduxmPHlqeCNZ3LU0DHptbeIHy5l/Yhg==;EndpointSuffix=core.chinacloudapi.cn;



<!--HONumber=52--> 