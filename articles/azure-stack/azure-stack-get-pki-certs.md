---
title: Azure 스택 통합 시스템 배포에 대 한 Azure 스택 공개 키 인프라 인증서를 생성 | Microsoft Docs
description: Azure 스택 PKI 인증서 배포에 대해 통합 Azure 스택 시스템에 설명합니다.
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
ms.date: 03/22/2018
ms.author: mabrigg
ms.reviewer: ppacent
ms.openlocfilehash: fc2ec96113310f54d32a67ea5fa31725600046c9
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/05/2018
---
# <a name="generate-pki-certificates-for-azure-stack-deployment"></a>Azure 스택 배포를 위한 PKI 인증서를 생성 합니다.
배웠으므로 [PKI 인증서 요구 사항](azure-stack-pki-certs.md) Azure 스택 배포에 대 한에서 CA (인증 기관) 사용자가 선택한 해당 인증서를 확보 해야 합니다. 

## <a name="request-certificates-using-an-inf-file"></a>INF 파일을 사용 하 여 인증서 요청
내부 CA 또는 공용 CA에서 인증서를 요청할 수 한 가지 방법은 INF 파일을 사용 하는 것입니다. Windows 기본 제공 certreq.exe 유틸리티 인증서 세부 정보를 지정 하는 INF 파일을 사용 하 여이 섹션에 설명 된 대로 요청 파일을 생성 수 있습니다. 

### <a name="sample-inf-file"></a>샘플 INF 파일 
예제에서는 인증서 요청 INF 파일 (내부 또는 공용) CA에 제출에 대 한 오프 라인 인증서 요청 파일을 만드는 데 사용할 수 있습니다. INF 단일 와일드 카드 인증서에 모든 필수 끝점 (선택적 PaaS 서비스 포함)를 설명 합니다. 

해당 지역의 같으면 샘플 INF 파일 가정 **바다** 외부 FQDN 값은 **바다&#46;contoso&#46;com**합니다. 이러한 값을 생성 하기 전에 사용자 환경에 맞게 변경는 합니다. 배포에 대 한 INF 파일입니다. 

    
    [Version] 
    Signature="$Windows NT$"

    [NewRequest] 
    Subject = "C=US, O=Microsoft, L=Redmond, ST=Washington, CN=portal.sea.contoso.com"

    Exportable = TRUE                   ; Private key is not exportable 
    KeyLength = 2048                    ; Common key sizes: 512, 1024, 2048, 4096, 8192, 16384 
    KeySpec = 1                         ; AT_KEYEXCHANGE 
    KeyUsage = 0xA0                     ; Digital Signature, Key Encipherment 
    MachineKeySet = True                ; The key belongs to the local computer account 
    ProviderName = "Microsoft RSA SChannel Cryptographic Provider" 
    ProviderType = 12 
    SMIME = FALSE 
    RequestType = PKCS10
    HashAlgorithm = SHA256

    ; At least certreq.exe shipping with Windows Vista/Server 2008 is required to interpret the [Strings] and [Extensions] sections below

    [Strings] 
    szOID_SUBJECT_ALT_NAME2 = "2.5.29.17" 
    szOID_ENHANCED_KEY_USAGE = "2.5.29.37" 
    szOID_PKIX_KP_SERVER_AUTH = "1.3.6.1.5.5.7.3.1" 
    szOID_PKIX_KP_CLIENT_AUTH = "1.3.6.1.5.5.7.3.2"

    [Extensions] 
    %szOID_SUBJECT_ALT_NAME2% = "{text}dns=*.sea.contoso.com&dns=*.blob.sea.contoso.com&dns=*.queue.sea.contoso.com&dns=*.table.sea.contoso.com&dns=*.vault.sea.contoso.com&dns=*.adminvault.sea.contoso.com&dns=*.dbadapter.sea.contoso.com&dns=*.appservice.sea.contoso.com&dns=*.scm.appservice.sea.contoso.com&dns=api.appservice.sea.contoso.com&dns=ftp.appservice.sea.contoso.com&dns=sso.appservice.sea.contoso.com&dns=adminportal.sea.contoso.com&dns=management.sea.contoso.com&dns=adminmanagement.sea.contoso.com" 
    %szOID_ENHANCED_KEY_USAGE% = "{text}%szOID_PKIX_KP_SERVER_AUTH%,%szOID_PKIX_KP_CLIENT_AUTH%"

    [RequestAttributes]
    

## <a name="generate-and-submit-request-to-the-ca"></a>생성 및 CA에 요청을 제출 합니다.
에서는 다음 워크플로 사용자 지정 CA에서 인증서를 요청 하려면 이전에 생성 예제 INF 파일을 사용 하는 방법을 설명 합니다.

1. **편집 하 고 INF 파일을 저장할**합니다. 샘플 복사본 제공 하 고 새 텍스트 파일에 저장 합니다. 주체 이름 및 외부 FQDN로 파일을 저장 및 배포에 일치 하는 값으로 대체 한 합니다. INF 파일입니다.
2. **Certreq를 사용 하 여 요청을 생성**합니다. 관리자 권한으로 명령 프롬프트를 시작 하 고 요청 (.req) 파일을 생성 하려면 다음 명령을 실행 하는 Windows 컴퓨터를 사용 하 여: `certreq -new <yourinffile>.inf <yourreqfilename>.req`합니다.
3. **CA에 제출**합니다. 제출 된 합니다. 필수 파일을 CA (내부 또는 공용 일 수 있음)를 생성 합니다.
4. **가져옵니다. CER**합니다. CA 반환은 합니다. CER 파일입니다. 가져오기 요청 파일을 생성 되는 동일한 Windows 컴퓨터를 사용 하는 합니다. 컴퓨터/개인 저장소에 반환 된 CER 파일입니다. 
5. **키를 내보내고 복사 합니다. 배포 폴더에는 PFX**합니다. 인증서 (개인 키 포함)는입니다. PFX 파일을 찾아 복사는 합니다. PFX 파일에 설명 된 배포 폴더에 [Azure 스택 배포에 대 한 PKI 요구 사항](azure-stack-pki-certs.md)합니다.

## <a name="next-steps"></a>다음 단계
[Azure 스택 PKI 인증서를 준비 합니다.](azure-stack-prepare-pki-certs.md)
