---
title: "Azure Storage에 대한 연결 문자열 구성 | Microsoft Docs"
description: "Azure 저장소 계정에 연결 문자열을 구성합니다. 연결 문자열에는 런타임 시 응용 프로그램에서 저장소 계정에 액세스하는 것을 인증하는 데 필요한 정보가 포함되어 있습니다."
services: storage
documentationcenter: 
author: tamram
manager: carmonm
editor: tysonn
ms.assetid: ecb0acb5-90a9-4eb2-93e6-e9860eda5e53
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/18/2016
ms.author: tamram
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 27c995e16f1137f6e18ebfc8210d492872d0e76c


---
# <a name="configure-azure-storage-connection-strings"></a>Azure 저장소 연결 문자열 구성
## <a name="overview"></a>개요
연결 문자열에는 런타임에 응용 프로그램에서 Azure 저장소 계정 데이터에 액세스하는 데 필요한 인증 정보가 포함되어 있습니다. 다음과 같은 작업을 수행하도록 연결 문자열을 구성할 수 있습니다.

* Azure 저장소 에뮬레이터에 연결합니다.
* Azure의 저장소 계정에 액세스
* SAS(공유 액세스 서명)를 통해 Azure의 지정된 리소스에 액세스

[!INCLUDE [storage-account-key-note-include](../../includes/storage-account-key-note-include.md)]

## <a name="storing-your-connection-string"></a>사용자의 연결 문자열 저장
Azure 저장소에 대해 만들어진 요청을 인증하려면 런타임에 응용 프로그램이 연결 문자열에 액세스해야 합니다. 연결 문자열을 저장하기 위한 몇 가지 옵션이 있습니다.

* 데스크톱 또는 장치에서 실행 중인 응용 프로그램의 경우 연결 문자열을 `app.config `파일 또는 `web.config` 파일에 저장할 수 있습니다. **AppSettings** 섹션에 연결 문자열을 추가합니다.
* Azure 클라우드 서비스에서 실행 중인 응용 프로그램의 경우, 연결 문자열을 [Azure 서비스 구성 스키마(.cscfg) 파일](https://msdn.microsoft.com/library/ee758710.aspx)에 저장할 수 있습니다. 연결 문자열을 서비스 구성 파일의 **ConfigurationSettings** 섹션에 추가합니다.
* 사용자 코드에서 직접 연결 문자열을 사용할 수도 있습니다. 그러나 대부분의 시나리오에서 구성 파일에 구성 문자열을 저장하는 것이 좋습니다.

사용자의 연결 문자열을 구성 파일 내에 저장하면 연결 문자열을 업데이트하여 저장소 에뮬레이터와 클라우드의 Azure 저장소 계정 사이에 전환하기 쉽습니다. 대상 환경을 가리키도록 연결 문자열을 편집하기만 하면 됩니다.

[Microsoft Azure 구성 관리자](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager/) 클래스를 사용하여 응용 프로그램이 실행 중인 위치와 상관없이 런타임에 사용자의 연결 문자열에 액세스할 수 있습니다.

## <a name="create-a-connection-string-to-the-storage-emulator"></a>저장소 에뮬레이터에 연결 문자열 만들기
[!INCLUDE [storage-emulator-connection-string-include](../../includes/storage-emulator-connection-string-include.md)]

저장소 에뮬레이터에 대한 자세한 내용은 [개발 및 테스트를 위한 Azure 저장소 에뮬레이터 사용](storage-use-emulator.md) 을 참조하세요.

## <a name="create-a-connection-string-to-an-azure-storage-account"></a>Azure 저장소 계정에 연결 문자열 만들기
Azure 저장소 계정에 연결 문자열을 만들려면 아래 연결 문자열 형식을 사용 합니다. HTTPS(권장) 또는 HTTP를 통해 저장소 계정에 연결할지 여부를 나타내며, `myAccountName`을 저장소 계정의 이름으로 바꾸고, `myAccountKey`를 계정 액세스 키로 바꿉니다.

    DefaultEndpointsProtocol=[http|https];AccountName=myAccountName;AccountKey=myAccountKey

예를 들어 연결 문자열은 다음의 샘플 연결 문자열과 같이 표시될 수 있습니다.

    DefaultEndpointsProtocol=https;AccountName=storagesample;AccountKey=<account-key>

> [!NOTE]
> Azure 저장소는 연결 문자열에서 HTTP 및 HTTPS를 모두 지원하지만 HTTPS를 사용하는 것이 좋습니다.
> 
> 

## <a name="create-a-connection-string-using-a-shared-access-signature"></a>공유 액세스 서명을 사용하여 연결 문자열 만들기
[!INCLUDE [storage-use-sas-in-connection-string-include](../../includes/storage-use-sas-in-connection-string-include.md)]

## <a name="creating-a-connection-string-to-an-explicit-storage-endpoint"></a>명시적 저장소 끝점에 대한 연결 문자열 만들기
기본 끝점을 사용하는 대신 연결 문자열에서 서비스 끝점을 명시적으로 지정할 수 있습니다. 명시적 Blob 끝점을 지정하는 연결 문자열을 만들려면 다음 형식으로 프로토콜 사양(HTTPS(권장) 또는 HTTP)을 포함하는 전체 서비스 끝점을 각 서비스에 대해 지정합니다.

    DefaultEndpointsProtocol=[http|https];
    BlobEndpoint=myBlobEndpoint;
    QueueEndpoint=myQueueEndpoint;
    TableEndpoint=myTableEndpoint;
    FileEndpoint=myFileEndpoint;
    AccountName=myAccountName;
    AccountKey=myAccountKey

명시적 끝점을 지정하고자 할 수 있는 하나의 시나리오로서 Blob 저장소 끝점을 사용자 지정 도메인에 매핑한 경우가 있습니다. 이 경우 연결 문자열에 있는 Blob 저장소에 대해 사용자 지정 끝점을 지정할 수 있으며, 응용 프로그램에서 다른 서비스에 대한 기본 끝점을 사용하는 경우 선택적으로 지정할 수 있습니다.

다음은 Blob 서비스에 대한 명시적 끝점을 지정하는 유효한 연결 문자열의 예제입니다.

    # Blob endpoint only
    DefaultEndpointsProtocol=https;
    BlobEndpoint=www.mydomain.com;
    AccountName=storagesample;
    AccountKey=account-key

    # All service endpoints
    DefaultEndpointsProtocol=https;
    BlobEndpoint=www.mydomain.com;
    FileEndpoint=myaccount.file.core.windows.net;
    QueueEndpoint=myaccount.queue.core.windows.net;
    TableEndpoint=myaccount;
    AccountName=storagesample;
    AccountKey=account-key

연결 문자열에 표시되는 끝점 값은 Blob 서비스의 요청 URI를 생성하는 데 사용되며 코드로 반환되는 URI의 형식을 지정합니다.

연결 문자열에서 서비스 끝점을 생략하도록 선택하면 해당 연결 문자열을 사용할 수 없어서 코드로부터 해당 서비스의 데이터에 액세스할 수 없습니다.

### <a name="creating-a-connection-string-with-an-endpoint-suffix"></a>끝점 접미사를 사용하여 연결 문자열 만들기
중국 Azure 또는 Azure 관리 방침과 같이 다른 끝점 접미사를 사용하여 지역이나 인스턴스에 저장소 서비스에 대한 연결 문자열을 만들려면 다음 연결 문자열 형식을 사용합니다. IHTTP 또는 HTTPS를 통해 저장소 계정에 연결할지 여부를 표시하며, `myAccountName`을 저장소 계정의 이름으로 바꾸고, `myAccountKey`를 계정 액세스 키로 바꾸고, `mySuffix`을 URI 접미사로 바꿉니다.

    DefaultEndpointsProtocol=[http|https];
    AccountName=myAccountName;
    AccountKey=myAccountKey;
    EndpointSuffix=mySuffix;


예를 들어 연결 문자열은 다음의 연결 문자열과 같이 표시될 수 있습니다.

    DefaultEndpointsProtocol=https;
    AccountName=storagesample;
    AccountKey=<account-key>;
    EndpointSuffix=core.chinacloudapi.cn;

## <a name="parsing-a-connection-string"></a>연결 문자열 구문 분석
[!INCLUDE [storage-cloud-configuration-manager-include](../../includes/storage-cloud-configuration-manager-include.md)]

## <a name="next-steps"></a>다음 단계
* [개발 및 테스트에 Azure 저장소 에뮬레이터 사용](storage-use-emulator.md)
* [Azure 저장소 탐색기](storage-explorers.md)
* [SAS(공유 액세스 서명) 사용](storage-dotnet-shared-access-signature-part-1.md)




<!--HONumber=Nov16_HO3-->


