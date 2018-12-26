---
title: Azure Stack 데이터 센터 통합-DNS
description: Azure Stack DNS DNS 데이터 센터와 통합 하는 방법 알아보기
services: azure-stack
author: jeffgilb
manager: femila
ms.service: azure-stack
ms.topic: article
ms.date: 10/15/2018
ms.author: jeffgilb
ms.reviewer: wfayed
keywords: ''
ms.openlocfilehash: bf412809f9d10296ad600e64abb6d870dbb88d3e
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/16/2018
ms.locfileid: "49339681"
---
# <a name="azure-stack-datacenter-integration---dns"></a>Azure Stack 데이터 센터 통합-DNS
Azure Stack 끝점에 액세스할 수 있으려면 (**포털**, **adminportal**를 **management**, **adminmanagement**등.)  외부 Azure Stack에서 Azure Stack에서 사용 하려는 DNS 영역을 호스트 하는 DNS 서버를 사용 하 여 Azure Stack DNS 서비스를 통합 해야 합니다.

## <a name="azure-stack-dns-namespace"></a>Azure Stack DNS 네임 스페이스
Azure Stack을 배포 하는 경우 DNS와 관련 된 몇 가지 중요 한 정보를 제공 하면 됩니다.


|필드  |설명  |예|
|---------|---------|---------|
|지역|Azure Stack 배포의 지리적 위치입니다.|`east`|
|외부 도메인 이름|Azure Stack 배포에 사용 하려는 영역의 이름입니다.|`cloud.fabrikam.com`|
|내부 도메인 이름|Azure Stack에서 인프라 서비스에 사용 되는 내부 영역 이름입니다.  디렉터리 서비스 통합 및 개인 (연결할 수 없음에서 외부 Azure Stack 배포).|`azurestack.local`|
|DNS 전달자|DNS 쿼리, DNS 영역 및 Azure Stack, 회사 인트라넷 또는 공용 인터넷에서 외부 호스트 되는 레코드를 전달 하는 데 사용 되는 DNS 서버입니다.|`10.57.175.34`<br>`8.8.8.8`|
|명명 접두사 (선택 사항)|할에 Azure Stack 인프라 역할 인스턴스의 컴퓨터 이름을 원하는 명명 접두사입니다.  기본값은 제공 되지 않으면 `azs`합니다.|`azs`|

Azure Stack 배포 및 끝점의 정규화 된 도메인 이름 (FQDN)에 영역 매개 변수 및 외부 도메인 이름 매개 변수 조합입니다. 예제에서 값을 사용 하 여 앞의 표에서,이 Azure Stack 배포에 대 한 FQDN 이름은 다음과 같습니다.

`east.cloud.fabrikam.com`

따라서이 배포에 대 한 끝점의 예는 다음 Url 같습니다.

`https://portal.east.cloud.fabrikam.com`

`https://adminportal.east.cloud.fabrikam.com`

이 예제에서는 DNS 네임 스페이스에는 Azure Stack 배포를 사용 하려면 다음 조건을 필요 합니다.

- 영역 `fabrikam.com` 도메인 등록자, 회사 내부 DNS 서버 또는 이름 확인 요구 사항에 따라 둘 다를 사용 하 여 등록 됩니다.
- 자식 도메인 `cloud.fabrikam.com` 영역에 있는 `fabrikam.com`합니다.
- 영역을 호스트 하는 DNS 서버 `fabrikam.com` 고 `cloud.fabrikam.com` Azure Stack 배포에서 연결할 수 있습니다.

Azure Stack 끝점 및 외부 Azure Stack에서 인스턴스에 대 한 DNS 이름을 확인할 수, 사용 하려는 부모 영역을 호스트 하는 DNS 서버를 사용 하 여 Azure Stack에 대 한 외부 DNS 영역을 호스트 하는 DNS 서버를 통합 해야 합니다.


## <a name="resolution-and-delegation"></a>확인 및 위임

DNS 서버에는 다음 두 가지 유형이 있습니다.

- 권한 있는 DNS 서버는 DNS 영역을 호스트 합니다. 해당 영역의 레코드에 대한 DNS 쿼리에만 대답합니다.
- 재귀 DNS 서버에서 DNS 영역을 호스트 하지 않습니다. 권한이 있는 DNS 서버를 호출하고 필요한 데이터를 수집하여 모든 DNS 쿼리에 응답합니다.

Azure Stack에 신뢰할 수 있는 및 재귀 DNS 서버입니다. 재귀 서버는 Azure Stack 배포에 대 한 개인 영역 내부 및 외부 공용 DNS 영역을 제외한 모든 항목의 이름을 확인 하는 데 사용 됩니다. 

![Azure Stack DNS 아키텍처](media/azure-stack-integrate-dns/Integrate-DNS-01.png)

## <a name="resolving-external-dns-names-from-azure-stack"></a>Azure Stack에서 외부 DNS 이름 확인

Azure Stack 외부 끝점에 대 한 DNS 이름을 확인 하기 위해 (예: www.bing.com), Azure Stack은 Azure Stack 권한을 보유 하지 않은 DNS 요청을 전달 하는 데 사용할 수 있는 DNS 서버를 제공 해야 합니다. 배포에 대 한 Azure Stack에 대 한 요청을 전달 하는 DNS 서버 (DNS 전달자 필드)에서 배포 워크시트에 필요 합니다. 내결함성에 대 한이 필드에 두 개 이상의 서버를 제공 합니다. 이러한 값이 없는 Azure Stack 배포에 실패 합니다.

### <a name="configure-conditional-dns-forwarding"></a>조건부 DNS 전달을 구성합니다

> [!IMPORTANT]
> 이 AD FS 배포에만 적용 됩니다.

기존 DNS 인프라를 사용 하 여 이름 확인을 사용 하려면 조건부 전달을 구성 합니다.

조건부 전달자를 추가 하려면 권한 있는 끝점을 사용 해야 합니다.

이 절차에서는 Azure Stack에서 권한 있는 끝점과 통신할 수 있는 데이터 센터 네트워크에서 컴퓨터를 사용 합니다.

1. 관리자 권한 Windows PowerShell 세션 (관리자 권한으로 실행)를 열고 권한 있는 끝점의 IP 주소에 연결 합니다. CloudAdmin 인증용 자격 증명을 사용 합니다.

   ```
   $cred=Get-Credential 
   Enter-PSSession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $cred
   ```

2. 권한 있는 끝점에 연결한 후 다음 PowerShell 명령을 실행 합니다. 도메인 이름과 함께 제공 되는 샘플 값을 대체 하 고 사용 하려는 DNS 서버의 IP 주소입니다.

   ```
   Register-CustomDnsServer -CustomDomainName "contoso.com" -CustomDnsIPAddresses "192.168.1.1","192.168.1.2"
   ```

## <a name="resolving-azure-stack-dns-names-from-outside-azure-stack"></a>외부 Azure Stack에서 Azure Stack의 DNS 이름 확인
권한 있는 서버가 외부 DNS 영역 정보를 저장 하 및 모든 사용자가 만든 영역입니다. 영역 위임 또는 외부 Azure Stack에서 Azure Stack의 DNS 이름을 확인 하기 위해 조건부 전달을 사용 하도록 설정 하려면 이러한 서버와 통합 합니다.

## <a name="get-dns-server-external-endpoint-information"></a>DNS 서버 외부 끝점 정보 가져오기

Azure Stack 배포에 DNS 인프라와 통합 하려면 다음 정보가 필요 합니다.

- DNS 서버 Fqdn
- DNS 서버 IP 주소

Azure Stack의 DNS 서버에 대 한 Fqdn의 형식이:

`<NAMINGPREFIX>-ns01.<REGION>.<EXTERNALDOMAINNAME>`

`<NAMINGPREFIX>-ns02.<REGION>.<EXTERNALDOMAINNAME>`

서버 dns Fqdn 샘플 값을 사용 하는 같습니다.

`azs-ns01.east.cloud.fabrikam.com`

`azs-ns02.east.cloud.fabrikam.com`


이 정보 라는 파일에 모든 Azure Stack 배포의 끝에 만들어져서 `AzureStackStampDeploymentInfo.json`합니다. 이 파일에는 `C:\CloudDeployment\logs` 배포 가상 컴퓨터의 폴더입니다. Azure Stack 배포에 사용 된 어떤 값 확실 하지 않은 경우 여기에서 값을 가져올 수 있습니다.

배포 가상 머신을 더 이상 사용할 수 없거나 액세스할 수, 하는 경우 권한 있는 끝점에 연결 하 고 실행 하 여 값을 얻을 수 없습니다는 `Get-AzureStackInfo` PowerShell cmdlet. 자세한 내용은 [privileged 끝점](azure-stack-privileged-endpoint.md)합니다.

## <a name="setting-up-conditional-forwarding-to-azure-stack"></a>Azure Stack에 조건부 전달 설정

DNS 인프라를 사용 하 여 Azure Stack을 통합 하는 가장 간단 하 고 가장 안전한 방법은 부모 영역을 호스팅하는 서버에서 영역의 조건부 전달을 수행 됩니다. Azure Stack 외부 DNS 네임 스페이스에 대 한 DNS 서버를 직접 제어 부모 영역을 호스트 하는 경우이 방법이 권장 됩니다.

Dns 조건부 전달을 수행 하는 방법을 잘 모르는 경우 다음 TechNet 문서 참조: [도메인 이름에 대 한 조건부 전달자를 할당](https://technet.microsoft.com/library/cc794735), 또는 DNS 솔루션에 관련 된 설명서입니다.

회사 도메인 이름의 하위 도메인을 같이 외부 Azure Stack DNS 영역을 지정한 위치 시나리오에서 조건부 전달을 사용할 수 없습니다. DNS 위임을 구성 해야 합니다.

예제:

- 회사 DNS 도메인 이름: `contoso.com`
- Azure Stack 외부 DNS 도메인 이름: `azurestack.contoso.com`

## <a name="delegating-the-external-dns-zone-to-azure-stack"></a>Azure Stack에 외부 DNS 영역 위임

Azure Stack 배포를 외부에서 확인할 수에 DNS 이름에 대 한 DNS 위임을 설정 해야 합니다.

각 등록 기관에는 도메인에 대한 이름 서버 레코드를 변경하는 자체 DNS 관리 도구가 있습니다. 기관의 DNS 관리 페이지에서 NS 레코드를 편집 하 고 Azure Stack에서 사용 하 여 영역에 대 한 NS 레코드를 바꿉니다.

대부분의 DNS 등록 기관 위임을 완료 하려면 DNS 서버를 두 개를 제공 해야 합니다.

## <a name="next-steps"></a>다음 단계

[방화벽 통합](azure-stack-firewall.md)
