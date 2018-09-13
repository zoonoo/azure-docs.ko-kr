---
title: Azure Stack 공개 키 인프라 인증서 요구 사항 for Azure Stack 통합 시스템 | Microsoft Docs
description: Azure Stack 통합 시스템이 Azure Stack PKI 인증서 배포 요구 사항을 설명합니다.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/05/2018
ms.author: mabrigg
ms.reviewer: ppacent
ms.openlocfilehash: 8de34e4ac01dea9cf4a0c718883e8cc828be6403
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/12/2018
ms.locfileid: "44714620"
---
# <a name="azure-stack-public-key-infrastructure-certificate-requirements"></a>Azure Stack 공개 키 인프라 인증서 요구 사항

Azure Stack에는 소수의 Azure Stack 서비스 및 테 넌 트 Vm에 할당 된 외부에서 액세스할 수 있는 공용 IP 주소를 사용 하는 공용 인프라 네트워크를 있습니다. Azure Stack 배포 하는 동안 이러한 Azure Stack 인프라를 공용 끝점에 대 한 적절 한 DNS 이름 갖는 PKI 인증서가 필요 합니다. 이 문서에 대 한 정보를 제공합니다.

- 인증서를 Azure Stack을 배포 하는 데 필요한
- 이러한 사양은 일치 하는 인증서를 얻는 프로세스는
- 준비의 유효성을 검사 하 고 배포 하는 동안 해당 인증서를 사용 하는 방법

> [!Note]  
> 배포 하는 동안 인증서 (Azure AD 또는 AD FS)에 대 한 배포 하는 id 공급자와 일치 하는 배포 폴더로 복사 해야 합니다. 모든 끝점에 대 한 단일 인증서를 사용 하는 경우 아래 표에 설명 된 대로 각 배포 폴더에 인증서 파일을 복사 해야 합니다. 폴더 구조는 미리 배포 가상 컴퓨터에서 빌드되고에서 찾을 수 있습니다: C:\CloudDeployment\Setup\Certificates 합니다. 

## <a name="certificate-requirements"></a>인증서 요구 사항
다음 목록에서는 Azure Stack을 배포 하는 데 필요한 인증서 요구 사항을 설명 합니다. 
- 인증서는 내부 인증 기관 또는 공용 인증 기관에서 발급 되어야 합니다. 공용 인증 기관에서 사용 하는 경우에 Microsoft 신뢰할 수 있는 루트 인증 기관 프로그램의 일부로 기본 운영 체제 이미지에 포함 되어야 합니다. 여기서 전체 목록을 찾을 수 있습니다. https://gallery.technet.microsoft.com/Trusted-Root-Certificate-123665ca 
- Azure Stack 인프라에는 인증서에 게시 하는 인증 기관의 인증서 해지 목록 (CRL) 위치에 네트워크 액세스를 권한이 있어야 합니다. 이 CRL http 끝점이 있어야 합니다.
- 인증서 배포 또는 위의 모든 공용 인증 기관에서 제공 하는 인증서에 서명 하는 데 사용 되는 동일한 내부 인증 기관에서 발급 하거나 해야 인증서를 회전 하는 경우
- 자체 서명 된 인증서의 사용은 지원 되지 않습니다.
- 배포 및 회전 하거나 인증서의 주체 이름과 주체 대체 이름 (SAN) 필드에서 모든 네임 스페이스를 포함 하는 단일 인증서를 사용 하거나 사용할 수에 대 한 개인 인증서 아래에 네임 스페이스의 각 Azure Stack 서비스를 활용 하려면 필요 합니다. 참고: 두 방법 모두는와 같은 필요한 끝점에 대 한 와일드 카드를 사용 해야 **KeyVault** 하 고 **KeyVaultInternal**합니다. 
- 인증서 서명 알고리즘은 3DES 이어야 합니다. 알고리즘 보다 강력한 해야 하므로 SHA1 일 수 없습니다. 
- 인증서 형식 공개 및 개인 키는 모두 Azure Stack 설치에 필요한으로 PFX 여야 합니다. 
- 인증서 pfx 파일에는 해당 "Key Usage" 필드에 "KeyEncipherment" 및 "디지털 시그니처" 값을 있어야 합니다.
- 인증서 pfx 파일에는 "확장 된 키 사용" 필드에 "서버 인증 (1.3.6.1.5.5.7.3.1)" 및 "클라이언트 인증 (1.3.6.1.5.5.7.3.2)" 값 있어야 합니다.
- 인증서의 "발급 대상:" 필드 안 동일 해당 "발급자:" 필드입니다.
- 모든 인증서 pfx 파일에 암호가 동일 해야 배포 시
- 인증서 pfx 암호는 복잡 한 암호를 사용 해야 합니다.
- 주체 이름과 주체 대체 이름 주체 대체 이름 확장명 (x509v3_config) 일치에서 하는지 확인 합니다. 주체 대체 이름 필드를 사용 하면 (웹 사이트, IP 주소, 일반 이름)는 단일 SSL 인증서로 보호 되도록 추가 호스트 이름을 지정할 수 있습니다.

> [!NOTE]  
> 자체 서명 인증서는 지원 되지 않습니다.

> [!NOTE]  
> 중간 인증 기관 인증서 체인-의-트러스트 IS 있는지 지원 합니다. 

## <a name="mandatory-certificates"></a>필수 인증서
이 섹션의 표에 Azure AD에 대 한 필요한 Azure Stack 공용 끝점 PKI 인증서 및 AD FS Azure Stack 배포 합니다. 인증서 요구 사항 영역 뿐만 아니라 사용 된 네임 스페이스 별로 그룹화 됩니다 및 각 네임 스페이스에 대 한 요구 되는 인증서입니다. 테이블에는 솔루션 공급자는 공용 끝점 마다 다른 인증서를 복사 하는 폴더를 설명 합니다. 

각 Azure Stack 인프라를 공용 끝점에 대 한 적절 한 DNS 이름 가진 인증서가 필요 합니다. 각 끝점의 DNS 이름 형식으로 표현 됩니다.  *&lt;접두사 >.&lt; 지역 >. &lt;fqdn >* 합니다. 

배포 [region]에 [externalfqdn] 값은 지역 및 Azure Stack 시스템에 대해 선택한는 외부 도메인 이름과 일치 해야 합니다. 예를 들어 지역 이름 되었으면 *Redmond* 외부 도메인 이름 및 *contoso.com*에 DNS 이름을 형식이 *&lt;접두사 >. redmond.contoso.com*. 합니다  *&lt;접두사 >* 값은 인증서로 보안 끝점을 설명 하기 위해 Microsoft에서 폴더도 있습니다. 또한 합니다  *&lt;접두사 >* 특정 끝점을 사용 하는 Azure Stack 서비스에 종속 된 외부 인프라 끝점의 값입니다. 

> [!note]  
> 인증서는 해당 디렉터리에 끝점 복사 모든 디렉터리에 복사 하는 주체 및 주체 대체 이름 (SAN) 필드의 모든 네임 스페이스를 포함 한 와일드 카드 인증서 또는 각 개별 인증서 제공 수 있습니다. 기억, 두 옵션 모두 같은 끝점에 대 한 와일드 카드 인증서를 사용 해야 **acs** 및 Key Vault는 필수입니다. 

| 배포 폴더 | 필요한 인증서 주체 및 주체 대체 이름 (SAN) | 범위 (지역당) | 하위 네임 스페이스 |
|-------------------------------|------------------------------------------------------------------|----------------------------------|-----------------------------|
| 공용 포털 | portal.&lt;region>.&lt;fqdn> | 포털 | &lt;region>.&lt;fqdn> |
| 관리 포털 | adminportal.&lt;region>.&lt;fqdn> | 포털 | &lt;region>.&lt;fqdn> |
| Azure Resource Manager 공용 | management.&lt;region>.&lt;fqdn> | Azure 리소스 관리자 | &lt;region>.&lt;fqdn> |
| Azure 리소스 관리자 관리 | adminmanagement.&lt;region>.&lt;fqdn> | Azure 리소스 관리자 | &lt;region>.&lt;fqdn> |
| ACSBlob | *.blob.&lt;region>.&lt;fqdn><br>(와일드 카드 SSL 인증서) | Blob Storage | blob.&lt;region>.&lt;fqdn> |
| ACSTable | *.table.&lt;region>.&lt;fqdn><br>(와일드 카드 SSL 인증서) | Table Storage | table.&lt;region>.&lt;fqdn> |
| ACSQueue | *.queue.&lt;region>.&lt;fqdn><br>(와일드 카드 SSL 인증서) | Queue Storage | queue.&lt;region>.&lt;fqdn> |
| KeyVault | *.vault.&lt;region>.&lt;fqdn><br>(와일드 카드 SSL 인증서) | Key Vault | vault.&lt;region>.&lt;fqdn> |
| KeyVaultInternal | *.adminvault.&lt;region>.&lt;fqdn><br>(와일드 카드 SSL 인증서) |  내부 Keyvault |  adminvault.&lt;region>.&lt;fqdn> |
| 관리 확장 호스트 | *.adminhosting 합니다. \<지역 >. \<fqdn > (와일드 카드 SSL 인증서) | 관리 확장 호스트 | adminhosting 합니다. \<지역 >. \<fqdn > |
| 공용 확장 호스트 | *.hosting 합니다. \<지역 >. \<fqdn > (와일드 카드 SSL 인증서) | 공용 확장 호스트 | 호스팅. \<지역 >. \<fqdn > |

Azure AD 배포 모드를 사용 하 여 Azure Stack을 배포 하는 경우 이전 표에 나열 된 인증서를 요청 해야 합니다. 그러나 AD FS 배포 모드를 사용 하 여 Azure Stack을 배포 하는 경우 다음 표에 설명 된 인증서를 요청 해야 합니다.

|배포 폴더|필요한 인증서 주체 및 주체 대체 이름 (SAN)|범위 (지역당)|하위 네임 스페이스|
|-----|-----|-----|-----|
|ADFS|adfs.*&lt;region>.&lt;fqdn>*<br>(SSL 인증서)|ADFS|*&lt;region>.&lt;fqdn>*|
|그래프|graph.*&lt;region>.&lt;fqdn>*<br>(SSL 인증서)|그래프|*&lt;region>.&lt;fqdn>*|
|

> [!IMPORTANT]
> 이 섹션에 나열 된 모든 인증서에 동일한 암호가 있어야 합니다. 

## <a name="optional-paas-certificates"></a>선택적 PaaS 인증서
계획 이라면 Azure Stack 한 후 추가 Azure Stack의 PaaS 서비스 (SQL, MySQL 및 App Service)를 배포 하는 배포 및 구성 된, PaaS 서비스의 끝점을 처리 하기 위해 추가 인증서를 요청 해야 합니다. 

> [!IMPORTANT]
> App Service, SQL 및 MySQL 리소스 공급자에 대해 사용 하는 인증서는 전역 Azure Stack 끝점에 대해 사용 되는 동일한 루트 인증 기관을 해야 합니다. 

다음 표에 끝점 및 App Service 및 SQL 및 MySQL 어댑터와 필요한 인증서를 있습니다. 이러한 인증서를 Azure Stack 배포 폴더로 복사할 필요가 없습니다. 대신, 추가 리소스 공급자를 설치할 때 이러한 인증서를 제공 합니다. 

|범위 (지역당)|인증서|필요한 인증서 주체 및 주체 대체 이름 (San)|하위 네임 스페이스|
|-----|-----|-----|-----|
|SQL, MySQL|SQL 및 MySQL|&#42;.dbadapter.*&lt;region>.&lt;fqdn>*<br>(와일드 카드 SSL 인증서)|dbadapter.*&lt;region>.&lt;fqdn>*|
|App Service|웹 트래픽이 기본 SSL 인증서|&#42;.appservice.*&lt;region>.&lt;fqdn>*<br>&#42;.scm.appservice.*&lt;region>.&lt;fqdn>*<br>&#42;.sso.appservice.*&lt;region>.&lt;fqdn>*<br>(다중 도메인 와일드 카드 SSL 인증서<sup>1</sup>)|appservice.*&lt;region>.&lt;fqdn>*<br>scm.appservice.*&lt;region>.&lt;fqdn>*|
|App Service|API|api.appservice.*&lt;region>.&lt;fqdn>*<br>(SSL 인증서<sup>2</sup>)|appservice.*&lt;region>.&lt;fqdn>*<br>scm.appservice.*&lt;region>.&lt;fqdn>*|
|App Service|FTP|ftp.appservice.*&lt;region>.&lt;fqdn>*<br>(SSL 인증서<sup>2</sup>)|appservice.*&lt;region>.&lt;fqdn>*<br>scm.appservice.*&lt;region>.&lt;fqdn>*|
|App Service|SSO|sso.appservice.*&lt;region>.&lt;fqdn>*<br>(SSL 인증서<sup>2</sup>)|appservice.*&lt;region>.&lt;fqdn>*<br>scm.appservice.*&lt;region>.&lt;fqdn>*|

<sup>1</sup> 여러 와일드 카드 주체 대체 이름을 사용 하 여 하나의 인증서가 필요 합니다. 단일 인증서에 San 여러 와일드 카드 모든 공용 인증 기관에서 지원 되지 않는 경우 

<sup>2</sup> 는 &#42;.appservice. *&lt;지역 >. &lt;fqdn >* 이러한 세 가지 인증서 대신 와일드 카드 인증서를 사용할 수 없습니다 (api.appservice. *&lt;지역 >. &lt;fqdn >*, ftp.appservice. *&lt;지역 >. &lt;fqdn >*, 및 sso.appservice. *&lt;지역 >. &lt;fqdn >* 합니다. App Service는 이러한 끝점에 대 한 별도 인증서를 사용 하도록 명시적으로 필요합니다. 

## <a name="learn-more"></a>자세한 정보
설명 하는 방법 [Azure Stack 배포를 위한 PKI 인증서를 생성할](azure-stack-get-pki-certs.md)합니다. 

## <a name="next-steps"></a>다음 단계
[Id 통합](azure-stack-integrate-identity.md)

