---
title: Azure 스택 통합 시스템 배포에 대 한 Azure 스택 공개 키 인프라 인증서 유효성 검사 | Microsoft Docs
description: Azure 스택 통합 시스템에 대 한 Azure 스택 PKI 인증서를 확인 하는 방법을 설명 합니다.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/22/2018
ms.author: mabrigg
ms.reviewer: ppacent
ms.openlocfilehash: 0bdadadb1f4ee5f76cde9d05b11e8d57b99ac191
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/05/2018
---
# <a name="validate-azure-stack-pki-certificates"></a>Azure 스택 PKI 인증서의 유효성을 검사합니다

이 문서에서 설명 하는 Azure 스택 인증서 검사기 도구를 확인 하기 위해 deploymentdata.json 파일에 포함 된 OEM에서 제공는 [PKI 인증서를 생성](azure-stack-get-pki-certs.md) 전 배포에 적합 합니다. 인증서는 충분 한 시간을 테스트 하 고 필요한 경우 발급 된 인증서를 가져오도록 유효성을 검사 해야 합니다.

다음 검사를 수행 하는 인증서 검사기 도구 (Certchecker):

- **PFX 읽기**합니다. 올바른 PFX 파일을 올바른 암호를 확인 하 고 공용 정보는 암호로 보호 되지 않는 경우 경고를 표시 합니다. 
- **서명 알고리즘**합니다. 서명 알고리즘은 s h a 1을 확인 합니다.
- **개인 키**합니다. 개인 키가 로컬 컴퓨터 특성을 사용 하 여 내보낸 확인 합니다. 
- **인증서 체인**합니다. 자체 서명 된 인증서에 대 한 그대로 포함 하 여 인증서 체인 확인 합니다. 
- **DNS 이름을**합니다. 각 끝점에 대 한 관련 DNS 이름을 포함 하는 SAN 또는 지 원하는 와일드 카드 표시 되는지 확인 합니다. 
- **키 사용**합니다. 디지털 서명 및 키 암호화를 포함 하는 키 사용 및 서버 인증 및 클라이언트 인증을 포함 하는 확장 된 키 사용을 확인 합니다.
- **키 크기**합니다. 키 크기는 2048 또는 더 큰 확인 합니다.
- **순서 체인**합니다. 다른 인증서 체인을 만드는 순서 올바른지 검사 합니다.
- **다른 인증서**합니다. 다른 인증서가 없습니다. 관련 리프 인증서와 체인 이외의 PFX에 패키지 된 확인 합니다.
- **프로필이 없습니다.**합니다. 새 사용자 데이터를 로드할 수 PFX 사용자 프로필 로드 없이 인증서를 서비스 하는 동안 gMSA 계정의 동작을 모방을 확인 합니다.

> [!IMPORTANT]  
> PKI 인증서 PFX 파일 및 암호를 중요 한 정보로 취급 되어야 합니다.

## <a name="prerequisites"></a>필수 조건
시스템은 Azure 스택 배포를 위한 PKI 인증서의 유효성 검사 하기 전에 다음 선행 조건을 충족 해야 합니다.
- CertChecker (에서 **PartnerToolKit** 아래 **\utils\certchecker**)
- SSL 인증서 내보낸 다음는 [준비 지침은](azure-stack-prepare-pki-certs.md)
- DeploymentData.json
- Windows 10 또는 Windows Server 2016

## <a name="perform-certificate-validation"></a>인증서 유효성 검사를 수행 합니다.

다음이 단계를 사용 하 여 준비 하 고 Azure 스택 PKI 인증서의 유효성을 검사 합니다. 

1. 내용을 추출 <partnerToolkit>새 디렉터리로 \utils\certchecker **c:\certchecker**합니다.

2. 관리자 권한으로 PowerShell을 열고 certchecker 폴더로 디렉터리를 변경 합니다.

  ```powershell
  cd c:\certchecker
  ```
 
3. 다음 PowerShell 명령을 실행 하 여 인증서에 대 한 디렉터리 구조를 만듭니다.

  ```powershell 
  $directories = "ACS","ADFS","Admin Portal","ARM Admin","ARM Public","Graph","KeyVault","KeyVaultInternal","Public Portal" 
  $destination = '.\certs' 
  $directories | % { New-Item -Path (Join-Path $destination $PSITEM) -ItemType Directory -Force}  
  ```

  >  [!NOTE]
  >  Azure 스택 배포에 대 한 id 공급자는 Azure AD를 하는 경우 제거는 **ADFS** 및 **그래프** 디렉터리입니다. 

4. 예를 들어 이전 단계에서 만든 적절 한 디렉터리에 사용 하 여 인증서를 배치 합니다. 
  - c:\certchecker\Certs\ACS\CustomerCertificate.pfx,  
  - c:\certchecker\Certs\Admin Portal\CustomerCertificate.pfx  
  - c:\certchecker\Certs\ARM Admin\CustomerCertificate.pfx  
  - 및 기타 등등... 

5. 복사 **deploymentdata.json** 에 **c:\certchecker** 디렉터리입니다.

6. PowerShell 창에서 다음 명령을 실행합니다. 

  ```powershell
  $password = Read-Host -Prompt "Enter PFX Password" -AsSecureString 
  .\CertChecker.ps1 -CertificatePath .\Certs\ -pfxPassword $password -deploymentDataJSONPath .\DeploymentData.json  
  ```

7. 출력에는 모든 인증서 및 확인 하는 모든 특성에 대 한 확인 포함 되어야 합니다. 

  ```powershell
  Starting Azure Stack Certificate Validation 1.1802.221.1
  Testing: ADFS\ContosoSSL.pfx
    Read PFX: OK
    Signature Algorithm: OK
    Private Key: OK
    Cert Chain: OK
    DNS Names: OK
    Key Usage: OK
    Key Size: OK
    Chain Order: OK
    Other Certificates: OK
    No Profile: OK
  Testing: KeyVaultInternal\ContosoSSL.pfx
    Read PFX: OK
    Signature Algorithm: OK
    Private Key: OK
    Cert Chain: OK
    DNS Names: OK
    Key Usage: OK
    Key Size: OK
    Chain Order: OK
    Other Certificates: OK
    No Profile: OK
  Testing: ACS\ContosoSSL.pfx
  WARNING: Pre-1803 certificate structure. The folder structure for Azure Stack 1803 and above is: ACSBlob, ACSQueue, ACSTable instead of ACS folder. Refer to deployment documentation for further informat
  ion.
    Read PFX: OK
    Signature Algorithm: OK
    Private Key: OK
    Cert Chain: OK
    DNS Names: OK
    Key Usage: OK
    Key Size: OK
    Chain Order: OK
    Other Certificates: OK
    No Profile: OK
  Detailed log can be found C:\CertChecker\CertChecker.log 
  ```

### <a name="known-issues"></a>알려진 문제 
**증상**: Certchecker 조기에 종료 하 고 다음 오류가 표시 됩니다. 
> 실패

> 세부 정보: 오류로 인해이 명령을 실행할 수 없습니다: 디렉터리 이름이 잘못 되었습니다. 

**원인**: 예를 들어 제한적인 폴더에서 certchecker.ps1 실행, c:\temp 또는 %temp% 

**해결 방법**: certchecker 도구 C:\CertChecker 예를 들어 새 디렉터리로 이동 


**증상**: Certchecker을 사용 하 여 이전 1803 (7 단계에서 위의 예제)에 대 한 경고를 제공 합니다.

> [!WARNING]
> Pre 1803 인증서 구조입니다. Azure 스택 1803에 대 한 구조 및 이상이 폴더: ACS 폴더 대신 ACSBlob, ACSQueue, ACSTable 합니다. 자세한 내용은 배포 설명서를 참조 합니다.

**원인**: CertChecker 사용을 발견 했습니다 단일 ACS 폴더 1803 하기 전에 배포에 대 한 올바른입니다. Azure 스택 버전 1803 및 배포 위에 ACSTable, ACSQueue, ACSBlob 폴더 구조 변경합니다. 이 기능을 지 원하는 Certchecker 이미 업데이트 수입니다.

**해결 방법**: 1802를 배포 하는 경우 작업이 필요 하지 않습니다. 1803 배포 and, ACSTable, ACSQueue, ACSBlob 바꿉니다 ACS 및 ACS 인증서 해당 폴더에 복사 합니다.

**증상**: 테스트를 건너

**원인**: CertChecker 종속성이 충족 되지 않으면 경우 특정 테스트를 건너뜁니다.
- 인증서 체인에 실패 하는 경우 다른 인증서는 건너뜁니다.
- 경우에 프로필이 없습니다.을 건너뜁니다.
  - 임시 사용자를 만들고 해당 사용자로 powershell을 실행 하는 기능을 제한 하는 보안 정책이 있습니다.
  - 개인 키 확인이 실패 합니다.

**해결 방법**: 세부 정보 구역에서 각 인증서의 일련의 테스트 도구 지침을 따릅니다.


## <a name="prepare-deployment-script-certificates"></a>배포 스크립트 인증서 준비 
마지막 단계로 준비 했으므로 모든 인증서를 배포 호스트에 적절 한 디렉터리에 배치 해야 합니다. 배포 호스트에서 라는 폴더를 만듭니다. 내보낸된 인증서 파일에 지정 된 해당 하위 폴더에 있는 인증서 * *와 위치는 [필수 인증서](https://docs.microsoft.com/azure/azure-stack/azure-stack-pki-certs#mandatory-certificates) 섹션:

```
\Certificates
\ACS\ssl.pfx
\Admin Portal\ssl.pfx
\ARM Admin\ssl.pfx
\ARM Public\ssl.pfx
\KeyVault\ssl.pfx
\KeyVaultInternal\ssl.pfx
\Public Portal\ssl.pfx
\ADFS\ssl.pfx*
\Graph\ssl.pfx*
```

<sup>*</sup> 별표로 표시 된 인증서 *는 AD FS는 id 저장소로 사용 될 때만 필요 합니다.


## <a name="next-steps"></a>다음 단계
[데이터 센터 id 통합](azure-stack-integrate-identity.md)
