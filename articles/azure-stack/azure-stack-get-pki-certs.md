---
title: Azure Stack 통합 시스템 배포에 대 한 Azure Stack 공개 키 인프라 인증서를 생성 합니다. | Microsoft Docs
description: Azure Stack 통합 시스템에 대 한 Azure Stack PKI 인증서 배포 프로세스를 설명합니다.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2019
ms.author: mabrigg
ms.reviewer: ppacent
ms.lastreviewed: 01/25/2019
ms.openlocfilehash: 9d358c021f795172e7ced0ba2a2f309a0a0dab6e
ms.sourcegitcommit: a4efc1d7fc4793bbff43b30ebb4275cd5c8fec77
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/21/2019
ms.locfileid: "56649730"
---
# <a name="azure-stack-certificates-signing-request-generation"></a>Azure Stack 인증서 서명 요청 만들기

Azure Stack 배포를 위한 적합 한 인증서 서명 요청 (Csr)를 만들려면 Azure Stack 준비 상태 검사기 도구를 사용할 수 있습니다. 인증서 요청, 생성 있고 충분 한 시간을 배포 하기 전에 테스트를 사용 하 여 유효성을 검사 합니다. 이 도구를 얻을 수 있습니다 [PowerShell 갤러리에서](https://aka.ms/AzsReadinessChecker)합니다.

다음 인증서를 요청 하는 Azure Stack 준비 상태 검사기 도구 (AzsReadinessChecker)를 사용할 수 있습니다.

- **표준 인증서 요청** 에 따라 [Azure Stack 배포를 위한 PKI 인증서를 생성할](azure-stack-get-pki-certs.md)합니다.
- **Platform-as-a-Service**: 플랫폼-as a service (PaaS) 이름에 지정 된 대로 인증서를 요청할 수 있습니다 [Azure Stack 공개 키 인프라 인증서 요구 사항-선택적 PaaS 인증서](azure-stack-pki-certs.md#optional-paas-certificates)합니다.

## <a name="prerequisites"></a>필수 조건

시스템은 Azure Stack 배포를 위한 PKI 인증서에 대 한 CSR(s)를 생성 하기 전에 다음 필수 구성 요소를 충족 해야 합니다.

- Microsoft Azure Stack 준비 검사
- 인증서 속성:
  - 지역 이름
  - 외부 정규화 된 도메인 이름 (FQDN)
  - 제목
- Windows 10 또는 Windows Server 2016

  > [!NOTE]  
  > 받게 되 면 인증서를 다시 인증 기관에서의 단계 [준비 Azure Stack PKI 인증서](azure-stack-prepare-pki-certs.md) 동일한 시스템에서 완료 해야 합니다.

## <a name="generate-certificate-signing-requests"></a>인증서 서명 요청을 생성 합니다.

다음이 단계를 사용 하 여 준비 하 고 Azure Stack PKI 인증서의 유효성 검사.

1. (5.1 이상) PowerShell 프롬프트에서 다음 cmdlet을 실행 하 여 AzsReadinessChecker를 설치 합니다.

    ```PowerShell  
        Install-Module Microsoft.AzureStack.ReadinessChecker
    ```

2. 선언 된 **주체** 정렬된 된 사전을으로 합니다. 예를 들면 다음과 같습니다.

    ```PowerShell  
    $subjectHash = [ordered]@{"OU"="AzureStack";"O"="Microsoft";"L"="Redmond";"ST"="Washington";"C"="US"}
    ```

    > [!note]  
    > 일반 이름 (CN)이 제공 하는 경우이 인증서 요청의 첫 번째 DNS 이름으로 덮어씁니다.

3. 이미 존재 하는 출력 디렉터리를 선언 합니다. 예를 들면 다음과 같습니다.

    ```PowerShell  
    $outputDirectory = "$ENV:USERPROFILE\Documents\AzureStackCSR"
    ```

4. Id 시스템을 선언

    Azure Active Directory

    ```PowerShell
    $IdentitySystem = "AAD"
    ```

    Active Directory Federation Services

    ```PowerShell
    $IdentitySystem = "ADFS"
    ```

5. 선언 **지역 이름** 와 **외부 FQDN** Azure Stack 배포를 위해 사용 합니다.

    ```PowerShell
    $regionName = 'east'
    $externalFQDN = 'azurestack.contoso.com'
    ```

    > [!note]  
    > `<regionName>.<externalFQDN>` 모든 외부 DNS 이름을 Azure Stack에서 만들어지면이 예제의 기본을 형성, 포털 `portal.east.azurestack.contoso.com`합니다.  

6. 각 DNS 이름에 대 한 요청을 서명 인증서를 생성 합니다.

    ```PowerShell  
    New-AzsCertificateSigningRequest -RegionName $regionName -FQDN $externalFQDN -subject $subjectHash -OutputRequestPath $OutputDirectory -IdentitySystem $IdentitySystem
    ```

    PaaS 서비스를 포함 하려면 스위치를 지정 합니다. ```-IncludePaaS```

7. 또는 개발/테스트 환경을 생성 하는 단일 인증서 요청을 여러 주체 대체 이름 추가 **-RequestType SingleCSR** 매개 변수 및 값 (**하지** 권장 프로덕션 환경):

    ```PowerShell  
    New-AzsCertificateSigningRequest -RegionName $regionName -FQDN $externalFQDN -subject $subjectHash -RequestType SingleCSR -OutputRequestPath $OutputDirectory -IdentitySystem $IdentitySystem
    ```

    PaaS 서비스를 포함 하려면 스위치를 지정 합니다. ```-IncludePaaS```

8. 출력을 검토 합니다.

    ```PowerShell  
    New-AzsCertificateSigningRequest v1.1809.1005.1 started.

    CSR generating for following SAN(s): dns=*.east.azurestack.contoso.com&dns=*.blob.east.azurestack.contoso.com&dns=*.queue.east.azurestack.contoso.com&dns=*.table.east.azurestack.cont
    oso.com&dns=*.vault.east.azurestack.contoso.com&dns=*.adminvault.east.azurestack.contoso.com&dns=portal.east.azurestack.contoso.com&dns=adminportal.east.azurestack.contoso.com&dns=ma
    nagement.east.azurestack.contoso.com&dns=adminmanagement.east.azurestack.contoso.com*dn2=*.adminhosting.east.azurestack.contoso.com@dns=*.hosting.east.azurestack.contoso.com
    Present this CSR to your Certificate Authority for Certificate Generation: C:\Users\username\Documents\AzureStackCSR\wildcard_east_azurestack_contoso_com_CertRequest_20180405233530.req
    Certreq.exe output: CertReq: Request Created

    Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
    New-AzsCertificateSigningRequest Completed
    ```

9. 제출 된 **합니다. 요청** CA (내부 또는 공용)에 생성 된 파일입니다.  출력 디렉터리 **새로 만들기-AzsCertificateSigningRequest** 인증 기관에 제출 하는 데 필요한 CSR(s)를 포함 합니다.  디렉터리도 포함 되어 있는, 참조를 위해 인증서 요청 생성 하는 동안 사용 되는 INF 파일이 들어 있는 하위 디렉터리입니다. CA를 충족 하는 생성 된 요청을 사용 하 여 인증서를 생성 해야 합니다 [Azure Stack에 대 한 PKI 요구 사항](azure-stack-pki-certs.md)합니다.

## <a name="next-steps"></a>다음 단계

[Azure Stack PKI 인증서를 준비 합니다.](azure-stack-prepare-pki-certs.md)
