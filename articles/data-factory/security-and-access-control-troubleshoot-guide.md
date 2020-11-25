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
ms.openlocfilehash: 21a1523016502bd7b0a8682461f1fc16acda2ebb
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2020
ms.locfileid: "96008731"
---
# <a name="troubleshoot-azure-data-factory-security-and-access-control-issues"></a>보안 및 액세스 제어 문제 Azure Data Factory 문제 해결

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

이 문서에서는 Azure Data Factory의 보안 및 액세스 제어에 대 한 일반적인 문제 해결 방법을 살펴봅니다.

## <a name="common-errors-and-messages"></a>일반적인 오류 및 메시지

### <a name="invalid-or-empty-authentication-key-issue-after-disabling-public-network-access"></a>공용 네트워크 액세스를 사용 하지 않도록 설정 하 고 나면 인증 키 문제가 잘못 되었거나 비어 있음

#### <a name="symptoms"></a>증상

Data Factory에 대 한 공용 네트워크 액세스를 사용 하지 않도록 설정한 후에 자체 호스팅 integration runtime이 "인증 키가 잘못 되었거나 비어 있습니다." 오류를 throw 합니다.

#### <a name="cause"></a>원인

이 문제는 공용 연결을 사용 하지 않도록 설정 하 고 개인 끝점을 설정 하는 데 도움이 되지 않는 DNS 확인 문제로 인 한 것일 수 있습니다.

#### <a name="resolution"></a>해결 방법

1. ADF의 FQDN을 사용 하 고 버퍼를 사용 하지 않도록 설정한 후에도 버퍼의 공용 끝점으로 이동 했습니다.

1. VM의 호스트 파일을 변경 하 여 개인 IP를 FQDN에 매핑하고 PsPing을 다시 실행 합니다. 버퍼는 ADF의 올바른 개인 IP로 이동할 수 있습니다.

1. 자체 호스팅 통합 런타임을 다시 등록 하면이를 확인할 수 있습니다.
 

### <a name="unable-to-register-ir-authentication-key-on-self-hosted-vms-due-to-private-link"></a>개인 링크로 인해 자체 호스트 된 Vm에 IR 인증 키를 등록할 수 없습니다.

#### <a name="symptoms"></a>증상

개인 링크를 사용 하도록 설정 되어 있어 자체 호스팅 VM에 IR 인증 키를 등록할 수 없습니다.

오류 정보는 다음과 같이 표시 됩니다.

`
Failed to get service token from ADF service with key *************** and time cost is: 0.1250079 seconds, the error code is: InvalidGatewayKey, activityId is: XXXXXXX and detailed error message is Client IP address is not valid private ip Cause Data factory couldn’t access the public network thereby not able to reach out to the cloud to make the successful connection.
`

#### <a name="cause"></a>원인

이 문제는 SHIR을 설치 하려고 시도 하는 VM에 의해 발생할 수 있습니다. 클라우드에 연결 하려면 공용 네트워크 액세스를 사용 하도록 설정 해야 합니다.

#### <a name="resolution"></a>해결 방법

 **해결 방법 1:** 아래 단계에 따라 문제를 해결할 수 있습니다.

1. 아래 링크로 이동 합니다. 
    
    https://docs.microsoft.com/rest/api/datafactory/Factories/Update

1. **사용해 보기** 옵션을 클릭 하 고 필요한 세부 정보를 모두 입력 합니다. **본문** 에도 아래 속성을 붙여 넣습니다.

    ```
    { "tags": { "publicNetworkAccess":"Enabled" } }
    ``` 

1. 페이지 끝에서 **실행** 을 클릭 하 여 함수를 실행 합니다. 응답 코드 200을 얻을 수 있는지 확인 합니다. 붙여넣은 속성은 JSON 정의에도 표시 됩니다.

1. 그런 다음 통합 런타임에 IR 인증 키를 다시 추가 해 볼 수 있습니다.


**해결 방법 2:** 솔루션에 대 한 다음 문서를 참조할 수 있습니다.

https://docs.microsoft.com/azure/data-factory/data-factory-private-link

사용자 인터페이스를 사용 하 여 공용 네트워크 액세스를 사용 하도록 설정 해 보세요.

![공용 네트워크 액세스 사용](media/self-hosted-integration-runtime-troubleshoot-guide/enable-public-network-access.png)

## <a name="next-steps"></a>다음 단계

문제 해결에 대 한 자세한 내용은 다음 리소스를 참조 하세요.

*  [Data Factory에 대 한 개인 링크](data-factory-private-link.md)
*  [Data Factory 블로그](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory 기능 요청](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure 비디오](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Microsoft Q&A 질문 페이지](/answers/topics/azure-data-factory.html)
*  [Data Factory에 대 한 스택 오버플로 포럼](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Data Factory에 대한 Twitter 정보](https://twitter.com/hashtag/DataFactory)