---
title: Azure 오스트레일리아의 데이터 보안
description: 오스트레일리아 정부 정책, 규정 및 법규의 특정 요구 사항을 충족 하도록 오스트레일리아 지역 내에서 Azure를 구성 합니다.
author: Galey801
ms.service: azure-australia
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: grgale
ms.openlocfilehash: 0301d506fc5764dbfda496727da95b2a1f2e9c7e
ms.sourcegitcommit: 6cff17b02b65388ac90ef3757bf04c6d8ed3db03
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/29/2019
ms.locfileid: "68608341"
---
# <a name="data-security-in-azure-australia"></a>Azure 오스트레일리아의 데이터 보안

고객 데이터를 보호하기 위한 포괄적인 원칙은 다음과 같습니다.

* 암호화를 사용하여 데이터 보호
* 암호 관리
* 데이터 액세스 제한

## <a name="encrypting-your-data"></a>데이터 암호화

데이터 암호화는 디스크 수준 (미사용), 데이터베이스 (미사용 및 전송 중), 응용 프로그램 (전송 중) 및 네트워크 (전송 중)에서 적용할 수 있습니다. Azure에서 암호화를 달성 하는 방법에는 여러 가지가 있습니다.

|서비스/기능|설명|
|---|---|
|Storage 서비스 암호화|Azure Storage 서비스 암호화는 Storage 계정 수준에 사용하도록 설정되고 이로 인해 블록 Blob 및 페이지 Blob이 Azure Storage에 쓸 때 자동으로 암호화됩니다. Azure Storage에서 데이터를 읽을 때 데이터가 반환되기 전에 Storage 서비스에서 해당 암호가 해독됩니다. 응용 프로그램에 코드를 수정 하거나 추가 하지 않고도 SSE를 사용 하 여 데이터를 보호 합니다.|
|Azure Disk Encryption|Azure Disk Encryption을 사용하여 Azure Virtual Machine에서 사용되는 OS 디스크 및 데이터 디스크를 암호화합니다. Azure Key Vault와의 통합은 제어를 제공하고 디스크 암호화 키를 관리할 수 있도록 도와줍니다.|
|클라이언트 쪽 응용 프로그램 수준 암호화|클라이언트 쪽 암호화는 Java 및 .NET 저장소 클라이언트 라이브러리에 기본적으로 제공 되며, Azure Key Vault Api를 활용 하 여 쉽게 구현할 수 있습니다. Azure Key Vault를 사용 하 여 Azure Active Directory를 사용 하는 특정 개인에 대해 Azure Key Vault의 암호에 액세스할 수 있습니다.|
|전송 중 암호화|Azure 오스트레일리아에 연결 하는 데 사용할 수 있는 기본 암호화는 TLS (전송 수준 보안) 1.2 프로토콜 및 x.509 인증서를 지원 합니다. FIPS(Federal Information Processing Standard) (FIPS) 140-2 수준 1 암호화 알고리즘은 Azure 오스트레일리아 데이터 센터 간의 인프라 네트워크 연결에도 사용 됩니다.  Windows Server 2016, Windows 10, Windows Server 2012 R2, Windows 8.1 및 Azure 파일 공유는 VM과 파일 공유 간의 암호화에 SMB 3.0를 사용할 수 있습니다. 클라이언트 쪽 암호화를 사용 하 여 데이터를 클라이언트 응용 프로그램의 저장소로 전송 하기 전에 암호화 하 고 저장소 외부로 전송 된 후 데이터의 암호를 해독 합니다.|
|IaaS Vm|Azure Disk Encryption를 사용 합니다. Storage 서비스 암호화를 켜서 Azure Storage에 해당 디스크를 백업하는 데 사용되는 VHD 파일을 암호화하지만 새로 작성된 데이터만 암호화됩니다. 즉, VM을 만든 다음 VHD 파일을 보유하는 Storage 계정에 대해 Storage 서비스 암호화를 사용하도록 설정하면 원래 VHD 파일이 아닌 변경 내용만 암호화됩니다.|
|클라이언트 쪽 암호화|이 방법은 데이터를 전송 하기 전에 암호화 하 고 미사용 데이터를 암호화 하기 때문에 데이터를 암호화 하는 가장 안전한 방법입니다. 그러나 원치 않게, 스토리지를 사용하여 애플리케이션에 코드를 추가해야 합니다. 이러한 경우 전송 중인 데이터에 HTTPS를 사용 하 고 미사용 데이터를 암호화 하는 저장소 서비스 암호화 수 있습니다. 클라이언트 쪽 암호화는 클라이언트에서 더 많은 부하를 포함 합니다. 특히 대량의 데이터를 암호화 하 고 전송 하는 경우 확장성 계획에서이를 고려해 야 합니다.|
|

Azure의 암호화 옵션에 대 한 자세한 내용은 [저장소 보안 가이드](https://docs.microsoft.com/azure/storage/storage-security-guide)를 참조 하세요.

## <a name="protecting-data-by-managing-secrets"></a>비밀을 관리 하 여 데이터 보호

안전한 키 관리는 클라우드에서 데이터를 보호하기 위해 아주 중요합니다. 고객은 키 관리를 간소화하고 데이터를 암호화하기 위해 클라우드 애플리케이션 및 서비스에서 사용되는 키의 제어를 유지 관리하기 위해 노력해야 합니다.

### <a name="managing-secrets"></a>암호 관리

* 주요 자격 증명 모음을 사용하여 하드 코드된 구성 파일, 스크립트 또는 소스 코드를 통해 노출되는 암호의 위험을 최소화합니다. Azure Key Vault는 키(예: Azure Disk Encryption에 대한 암호화 키) 및 암호(예: 비밀번호)를 FIPS 140-2 Level 2 유효성 검사된 하드웨어 보안 모듈(HSM)에 저장하여 암호화합니다. 추가된 보증을 위해 해당 HSM에서 키를 생성하거나 가져올 수 있습니다.
* 응용 프로그램 코드 및 템플릿은 암호에 대 한 URI 참조를 포함 해야 합니다. 즉, 실제 암호는 코드, 구성 또는 소스 코드 리포지토리에 포함 되지 않습니다. 따라서 GitHub의 harvest-bots 등과 같은 내부 또는 외부 리포지토리에 대한 키 피싱 공격을 차단할 수 있습니다.
* 주요 자격 증명 모음 내에서 강력한 RBAC 제어를 사용합니다. 신뢰할 수 있는 직원이 퇴사하거나 회사 내의 새 그룹으로 이동하는 경우 암호에 액세스할 수 없도록 방지해야 합니다.  

자세한 내용은 [Azure Key Vault](azure-key-vault.md) 를 참조 하세요.

## <a name="isolation-to-restrict-data-access"></a>데이터 액세스를 제한하도록 격리

격리는 인증된 사용자, 서비스 및 애플리케이션에 대해서만 데이터 액세스를 제한하도록 경계, 구분 및 컨테이너를 사용하는 것입니다. 예를 들어 테 넌 트 간의 분리는 Microsoft Azure와 같은 다중 테 넌 트 클라우드 플랫폼에 대 한 필수 보안 메커니즘입니다. 논리적 격리는 하나의 테넌트가 다른 테넌트의 작업을 방해하지 않도록 방지하는 데 도움을 줍니다.

#### <a name="per-customer-isolation"></a>고객별 격리

Azure는 계층 2 VLAN 격리, 액세스 제어 목록, 부하 분산 장치 및 IP 필터를 통해 네트워크 액세스 제어 및 분리를 구현 합니다.

고객은 구독, 리소스 그룹, 가상 네트워크 및 서브넷 간에 리소스를 추가적으로 격리할 수 있습니다.

Microsoft Azure의 격리에 대 한 자세한 내용은 [Azure 공용 클라우드에서 격리](../security/fundamentals/isolation-choices.md)를 참조 하세요.

## <a name="next-steps"></a>다음 단계

[Azure VPN Gateway](vpn-gateway.md) 의 문서 검토