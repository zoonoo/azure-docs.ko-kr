---
title: 보안 및 액세스 제어 문제 해결
description: Azure Data Factory에서 보안 및 액세스 제어 문제를 해결 하는 방법에 대해 알아봅니다.
services: data-factory
author: lrtoyou1223
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 11/19/2020
ms.author: lle
ms.reviewer: craigg
ms.openlocfilehash: 99c03ae4430d1a4caf575bdb9900200af0217bf1
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/11/2020
ms.locfileid: "97109772"
---
# <a name="troubleshoot-azure-data-factory-security-and-access-control-issues"></a>보안 및 액세스 제어 문제 Azure Data Factory 문제 해결

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

이 문서에서는 Azure Data Factory의 보안 및 액세스 제어에 대 한 일반적인 문제 해결 방법을 살펴봅니다.

## <a name="common-errors-and-messages"></a>일반적인 오류 및 메시지


### <a name="connectivity-issue-of-copy-activity-in-cloud-data-store"></a>클라우드 데이터 저장소에서 복사 작업의 연결 문제

#### <a name="symptoms"></a>증상

원본/싱크 데이터 저장소에 대 한 연결 문제가 발생 하면 여러 종류의 오류 메시지가 반환 될 수 있습니다.

#### <a name="cause"></a>원인 

문제는 대개 다음과 같은 요인으로 인해 발생 합니다.

1. 자체 호스팅 IR 노드의 프록시 설정 (자체 호스트 IR을 사용 하는 경우)

1. 자체 호스팅 IR 노드의 방화벽 설정 (자체 호스트 IR을 사용 하는 경우)

1. 클라우드 데이터 저장소의 방화벽 설정

#### <a name="resolution"></a>해결 방법

1. 다음 사항에 따라 문제가 연결 문제로 인해 발생 하는지 확인 합니다.

   - 이 오류는 소스/싱크 커넥터에서 throw 됩니다.

   - 복사가 시작 될 때 작업이 실패 합니다.

   - 한 노드에서는 Azure IR 또는 자체 호스팅 IR에 대해 일관 된 오류가 발생 합니다 .이는 노드의 일부만 문제가 있는 경우 다중 노드 자체 호스팅 IR에 대 한 임의 오류가 발생할 수 있기 때문입니다.

1. 동일한 데이터 저장소에 대 한 실행이 Azure IR에서 성공할 수 있으므로 **자체 호스팅 IR** 을 사용 하는 경우 프록시, 방화벽 및 네트워크 설정을 확인 합니다. 문제를 해결 하려면 다음 링크를 참조 하세요.

   [자체 호스팅 IR 포트 및 방화벽](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#ports-and-firewalls) 
    [ADLS 커넥터](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-store)
  
1. **Azure IR** 를 사용 하는 경우 데이터 저장소의 방화벽 설정을 사용 하지 않도록 설정 해 보세요. 이러한 방식으로 다음 두 가지 경우에 대 한 문제를 해결할 수 있습니다.
  
   * [AZURE IR IP 주소](https://docs.microsoft.com/azure/data-factory/azure-integration-runtime-ip-addresses) 는 허용 목록에 없습니다.

   * *신뢰할 수 있는 Microsoft 서비스에서이 저장소 계정에 액세스 하도록 허용* 기능은 [Azure Blob Storage](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#supported-capabilities) 및 [ADLS Gen 2](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#supported-capabilities)에 대해 해제 되어 있습니다.

   * ADLS Gen1에 대해 *Azure 서비스에 대 한 액세스 허용* 이 사용 하도록 설정 되어 있지 않습니다.

1. 위의 방법이 작동 하지 않는 경우 도움이 필요 하면 Microsoft에 문의 하세요.


### <a name="invalid-or-empty-authentication-key-issue-after-public-network-access-is-disabled"></a>공용 네트워크 액세스를 사용 하지 않도록 설정 하 고 나면 인증 키 문제가 잘못 되었거나 비어 있음

#### <a name="symptoms"></a>증상

Data Factory에 대 한 공용 네트워크 액세스를 사용 하지 않도록 설정한 후에는 "인증 키가 잘못 되었거나 비어 있습니다." 라는 오류를 발생 시킵니다.

#### <a name="cause"></a>원인

이 문제는 공용 연결을 사용 하지 않도록 설정 하 고 개인 끝점을 설정 하는 데 도움이 되지 않는 DNS 확인 문제로 인 한 것일 수 있습니다.

다음 단계를 수행 하 Data Factory FQDN이 공용 IP 주소로 확인 되는지 확인할 수 있습니다.

1. Data Factory 개인 끝점과 동일한 VNET에서 Azure VM을 만들었는지 확인 합니다.

2. Azure VM에서 PsPing 및 Ping을 실행 하 여 FQDN을 Data Factory 합니다.

   `ping <dataFactoryName>.<region>.datafactory.azure.net`

   `psping.exe <dataFactoryName>.<region>.datafactory.azure.net:443`

   > [!Note]
   > PsPing 명령에 포트를 지정 해야 하며, 443 포트는이 아닙니다.

3. 두 명령이 지정 된 영역을 기반으로 하는 ADF 공용 IP로 확인 되었는지 확인 합니다 (형식 xxx. xxx. xxx. 0).

#### <a name="resolution"></a>해결 방법

- [Azure Data Factory에 대 한 Azure 개인 링크](https://docs.microsoft.com/azure/data-factory/data-factory-private-link#dns-changes-for-private-endpoints)의 문서를 참조할 수 있습니다. 지침은 개인 IP 주소에 대 한 FQDN Data Factory 확인 하도록 개인 DNS 영역/서버를 구성 하기 위한 것입니다.

- 현재 개인 DNS 영역/서버를 구성 하지 않으려는 경우 다음 단계를 임시 솔루션으로 수행 하세요. 그러나 사용자 지정 DNS는 장기 솔루션으로 권장 됩니다.

  1. Windows에서 호스트 파일을 변경 하 고 개인 IP (ADF 개인 끝점)를 ADF FQDN에 매핑합니다.
  
     Azure VM에서 경로 "C:\Windows\System32\drivers\etc"로 이동 하 여 메모장에서 **호스트** 파일을 엽니다. 파일의 끝에 있는 FQDN에 개인 IP 매핑 줄을 추가 하 고 변경 내용을 저장 합니다.
     
     ![호스트에 매핑 추가](media/self-hosted-integration-runtime-troubleshoot-guide/add-mapping-to-host.png)

  1. 위의 확인 단계에서 동일한 명령을 다시 실행 하 여 개인 IP를 포함 하는 응답을 확인 합니다.

  1. 자체 호스팅 통합 런타임을 다시 등록 하면 문제를 해결 해야 합니다.
 

### <a name="unable-to-register-ir-authentication-key-on-self-hosted-vms-due-to-private-link"></a>개인 링크로 인해 자체 호스트 된 Vm에 IR 인증 키를 등록할 수 없습니다.

#### <a name="symptoms"></a>증상

개인 링크를 사용 하도록 설정 되어 있어 자체 호스팅 VM에 IR 인증 키를 등록할 수 없습니다.

오류 정보는 다음과 같이 표시 됩니다.

`
Failed to get service token from ADF service with key *************** and time cost is: 0.1250079 seconds, the error code is: InvalidGatewayKey, activityId is: XXXXXXX and detailed error message is Client IP address is not valid private ip Cause Data factory couldn’t access the public network thereby not able to reach out to the cloud to make the successful connection.
`

#### <a name="cause"></a>원인

이 문제는 자체 호스팅 IR을 설치 하려고 하는 VM 때문에 발생할 수 있습니다. 클라우드에 연결 하려면 공용 네트워크 액세스가 사용 하도록 설정 되어 있는지 확인 해야 합니다.

#### <a name="resolution"></a>해결 방법

 **해결 방법 1:** 아래 단계에 따라 문제를 해결할 수 있습니다.

1. [팩터리-업데이트](https://docs.microsoft.com/rest/api/datafactory/Factories/Update) 페이지로 이동 합니다.

1. 오른쪽 위에서 **사용해 보기** 단추를 선택 합니다.

1. **매개 변수** 에서 필요한 정보를 입력 합니다. 

1. **본문** 아래에 다음 속성을 붙여넣습니다.
    ```
    { "tags": { "publicNetworkAccess":"Enabled" } }
    ``` 

1. **실행** 을 선택 하 여 함수를 실행 합니다. 

1. **응답 코드: 200** 이 표시 되는지 확인 합니다. 붙여넣은 속성은 JSON 정의에도 표시 되어야 합니다.

1. 통합 런타임에 IR 인증 키를 다시 추가 합니다.


**해결 방법 2:** 솔루션에 대 한 다음 문서를 참조할 수 있습니다.

https://docs.microsoft.com/azure/data-factory/data-factory-private-link

다음 스크린샷에 표시 된 것 처럼 사용자 인터페이스에서 공용 네트워크 액세스를 사용 하도록 설정 해 봅니다.

![공용 네트워크 액세스 사용](media/self-hosted-integration-runtime-troubleshoot-guide/enable-public-network-access.png)

## <a name="next-steps"></a>다음 단계

문제 해결에 대 한 자세한 내용은 다음 리소스를 참조 하세요.

*  [Data Factory에 대 한 개인 링크](data-factory-private-link.md)
*  [Data Factory 블로그](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory 기능 요청](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure 비디오](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Microsoft Q&페이지](/answers/topics/azure-data-factory.html)
*  [Data Factory에 대 한 스택 오버플로 포럼](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Data Factory에 대한 Twitter 정보](https://twitter.com/hashtag/DataFactory)
