---
title: Azure 스택 통합 시스템 배포에 대 한 Azure 스택 공개 키 인프라 인증서를 생성 | Microsoft Docs
description: Azure 스택 통합 시스템에 대 한 Azure 스택 PKI 인증서 배포 프로세스를 설명합니다.
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
ms.date: 04/11/2018
ms.author: mabrigg
ms.reviewer: ppacent
ms.openlocfilehash: fbf3c66979730a9162c56e8583f0a32977a0310d
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="azure-stack-certificates-signing-request-generation"></a>Azure 스택 인증서 서명 요청 만들기

이 문서에서 설명 하는 Azure 스택 준비 검사기 도구를 사용할 수 [PowerShell 갤러리에서](https://aka.ms/AzsReadinessChecker)합니다. 이 도구 Azure 스택 배포에 대 한 적합 한 인증서 서명 요청 (Csr)를 만듭니다. 인증서 요청, 생성와 충분 한 시간을 배포 하기 전에 테스트와 유효성을 검사 합니다. 

Azure 스택 준비 검사기 도구 (AzsReadinessChecker) 다음과 같은 인증서 요청을 수행합니다.

 - **표준 인증서 요청**  
    요청에 따라 [Azure 스택 배포를 위한 PKI 인증서를 생성할](azure-stack-get-pki-certs.md)합니다. 
 - **요청 유형**  
    단일 와일드 카드 인증서를 요청에 여러 와일드 카드 SAN에 여러 도메인 인증서를 요청 합니다.
 - **플랫폼-as a Service**  
    필요에 따라 플랫폼으로-서비스 (PaaS) 이름에 지정 된 대로 인증서를 요청 [Azure 스택 공개 키 인프라 인증서 요구 사항-옵션 PaaS 인증서](azure-stack-pki-certs.md#optional-paas-certificates)합니다.

## <a name="prerequisites"></a>필수 조건

시스템은 Azure 스택 배포를 위한 PKI 인증서에 대 한 CSR(s)를 생성 하기 전에 다음 선행 조건을 충족 해야 합니다.

 - Microsoft Azure 스택 준비 상태 검사
 - 인증서 속성:
    - 지역 이름
    - 외부 정규화 된 도메인 이름 (FQDN)
    - 제목
 - Windows 10 또는 Windows Server 2016

## <a name="generate-certificate-signing-requests"></a>인증서 서명 요청을 생성 합니다.

다음이 단계를 사용 하 여 준비 하 고 Azure 스택 PKI 인증서의 유효성을 검사 합니다. 

1.  (5.1 이상) PowerShell 프롬프트에서 다음 cmdlet을 실행 하 여 AzsReadinessChecker를 설치 합니다.

    ````PowerShell  
        Install-Module Microsoft.AzureStack.ReadinessChecker
    ````

2.  선언 된 **주체** 정렬 된 사전으로 합니다. 예:  

    ````PowerShell  
    $subjectHash = [ordered]@{"OU"="AzureStack";"O"="Microsoft";"L"="Redmond";"ST"="Washington";"C"="US"} 
    ````
    > [!note]  
    > CN (일반 이름)를 제공 하는 경우 인증서 요청의 첫 번째 DNS 이름으로 덮어쓰게 됩니다.

3.  이미 존재 하는 출력 디렉터리를 선언 합니다.

    ````PowerShell  
    $outputDirectory = "$ENV:USERNAME\Documents\AzureStackCSR" 
    ````

4. 선언 **지역 이름** 및 **외부 FQDN** Azure 스택 배포를 위한 것입니다.

    ```PowerShell  
    $regionName = 'east'
    $externalFQDN = 'azurestack.contoso.com'
    ````

    > [!note]  
    > `<regionName>.<externalFQDN>` 에 Azure 스택의 모든 외부 DNS 이름을 만들어지면이 예제의 기본을 형성, 포털 것 `portal.east.azurestack.contoso.com`합니다.

5. 생성 하려면 단일 인증서 요청 PaaS 서비스에 필요한 포함 하는 여러 주체 대체 이름을 사용:

    ```PowerShell  
    Start-AzsReadinessChecker -RegionName $regionName -FQDN $externalFQDN -subject $subjectHash -RequestType MultipleSAN -OutputRequestPath $OutputDirectory -IncludePaaS
    ````

6. 생성 하려면 PaaS 서비스 없이 각 DNS 이름에 대 한 요청을 서명 하는 개별 인증서:

    ```PowerShell  
    Start-AzsReadinessChecker -RegionName $regionName -FQDN $externalFQDN -subject $subjectHash -RequestType SingleSAN -OutputRequestPath $OutputDirectory
    ````

7. 출력을 검토 합니다.

    ````PowerShell  
    AzsReadinessChecker v1.1803.405.3 started
    Starting Certificate Request Generation

    CSR generating for following SAN(s): dns=*.east.azurestack.contoso.com&dns=*.blob.east.azurestack.contoso.com&dns=*.queue.east.azurestack.contoso.com&dns=*.table.east.azurestack.cont
    oso.com&dns=*.vault.east.azurestack.contoso.com&dns=*.adminvault.east.azurestack.contoso.com&dns=portal.east.azurestack.contoso.com&dns=adminportal.east.azurestack.contoso.com&dns=ma
    nagement.east.azurestack.contoso.com&dns=adminmanagement.east.azurestack.contoso.com
    Present this CSR to your Certificate Authority for Certificate Generation: C:\Users\username\Documents\AzureStackCSR\wildcard_east_azurestack_contoso_com_CertRequest_20180405233530.req
    Certreq.exe output: CertReq: Request Created

    Finished Certificate Request Generation

    AzsReadinessChecker Log location: C:\Program Files\WindowsPowerShell\Modules\Microsoft.AzureStack.ReadinessChecker\1.1803.405.3\AzsReadinessChecker.log
    AzsReadinessChecker Completed
    ````

8.  제출 된 **합니다. 필수** CA (내부 또는 공용)에 생성 된 파일입니다.  출력 디렉터리 **시작 AzsReadinessChecker** 인증 기관에 제출 하는 데 필요한 CSR(s)를 포함 합니다.  또한 참조로의 인증서 요청 생성 시 사용할 INF 파일이 들어 있는 하위 디렉터리를 포함 합니다. CA을 충족 하는 생성 된 요청을 사용 하 여 인증서를 생성 해야는 [Azure 스택 PKI 요구 사항](azure-stack-pki-certs.md)합니다.

## <a name="next-steps"></a>다음 단계
[Azure 스택 PKI 인증서를 준비 합니다.](azure-stack-prepare-pki-certs.md)
