---
title: Azure Stack Edge Pro GPU 디바이스에 액세스하는 Windows 클라이언트에서 TLS 1.2 구성
description: Azure Stack Edge Pro GPU 디바이스에 액세스하는 Windows 클라이언트에서 TLS 1.2를 구성하는 방법을 설명합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: 47a6d3bbebdf3b2b14b1c40d7ea8fc93b4d19c6f
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105565336"
---
# <a name="configure-tls-12-on-windows-clients-accessing-azure-stack-edge-pro-device"></a>Azure Stack Edge Pro 디바이스에 액세스하는 Windows 클라이언트에서 TLS 1.2 구성

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Windows 클라이언트를 사용하여 Azure Stack Edge Pro 디바이스에 액세스하는 경우 클라이언트에서 TLS 1.2을 구성해야 합니다. 이 문서에서는 Windows 클라이언트에서 TLS 1.2를 구성하기 위한 리소스 및 지침을 제공합니다. 

여기에 제공된 지침은 Windows Server 2016을 실행하는 클라이언트에서 수행된 테스트를 기반으로 합니다.

## <a name="configure-tls-12-for-current-powershell-session"></a>현재 PowerShell 세션에 대해 TLS 1.2 구성

클라이언트에서 TLS 1.2을 구성하려면 다음 단계를 수행합니다.

1. 관리자 권한으로 PowerShell을 실행합니다.
2. 현재 PowerShell 세션에 대해 TLS 1.2를 설정하려면 다음을 입력합니다.
  
    ```azurepowershell
    $TLS12Protocol = [System.Net.SecurityProtocolType] 'Ssl3 , Tls12'
    [System.Net.ServicePointManager]::SecurityProtocol = $TLS12Protocol
    ```
## <a name="configure-tls-12-on-client"></a>클라이언트에서 TLS 1.2 구성

사용자 환경에 대한 시스템 수준 TLS 1.2을 설정하려면 다음 문서의 지침을 따르세요.

- [일반 - TLS 1.2를 사용하도록 설정하는 방법](/windows-server/security/tls/tls-registry-settings#tls-12)
- [클라이언트에서의 TLS 1.2 사용 설정 방법](/configmgr/core/plan-design/security/enable-tls-1-2-client)
- [사이트 서버 및 원격 사이트 시스템에서의 TLS 1.2 사용 방법](/configmgr/core/plan-design/security/enable-tls-1-2-server)
- [TLS/SSL(Schannel SSP)의 프로토콜](/windows-server/security/tls/manage-tls#configuring-tls-ecc-curve-order)
- [암호 그룹](/windows-server/security/tls/tls-registry-settings#tls-12): 특히 [TLS 암호 그룹 순서를 구성](/windows-server/security/tls/manage-tls#configuring-tls-cipher-suite-order)하면 현재 암호 그룹을 나열하고 다음 목록에서 누락된 항목을 앞에 추가해야 합니다.

    - TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
    - TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
    - TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
    - TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384

    레지스트리 설정을 직접 편집하여 이러한 암호 그룹을 추가할 수도 있습니다.

    ```azurepowershell
    New-ItemProperty -Path "$HklmSoftwarePath\Policies\Microsoft\Cryptography\Configuration\SSL\00010002" -Name "Functions"  -PropertyType String -Value ("TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384, TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384, TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384,TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384")
    ```

- 타원 곡선을 설정하는 방법

    현재 타원 곡선을 나열하고 다음 목록에서 누락된 항목을 앞에 추가해야 합니다.

    - P-256 
    - P-384

    레지스트리 설정을 직접 편집하여 이러한 타원 곡선을 추가할 수도 있습니다.
    
    ```azurepowershell
    New-ItemProperty -Path "$HklmSoftwarePath\Policies\Microsoft\Cryptography\Configuration\SSL\00010002" -Name "EccCurves" -PropertyType MultiString -Value @("NistP256", "NistP384")
    ```
    
    - [최소 RSA 키 교환 크기를 2048으로 설정](/windows-server/security/tls/tls-registry-settings#keyexchangealgorithm---client-rsa-key-sizes)합니다.



## <a name="next-steps"></a>다음 단계

[Azure Resource Manager에 연결](./azure-stack-edge-gpu-connect-resource-manager.md)