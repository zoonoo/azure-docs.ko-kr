---
title: Azure에서 Windows 가상 머신 정품 인증 문제 해결 | Microsoft Docs
description: Azure에서 Windows 가상 머신 정품 인증 문제를 해결하기 위한 문제 해결 단계를 제공합니다.
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: top-support-issue, azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 11/15/2018
ms.author: genli
ms.openlocfilehash: 5c84588290ce769b556002469b6a11c6950bb878
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79476555"
---
# <a name="troubleshoot-azure-windows-virtual-machine-activation-problems"></a>Windows Azure 가상 컴퓨터 정품 인증 문제 해결

사용자 지정 이미지에서 만든 Azure Windows VM(가상 머신)을 활성화하는 데 문제가 발생하는 경우 문제를 해결하려면 이 문서에 제공된 정보를 사용할 수 있습니다. 

## <a name="understanding-azure-kms-endpoints-for-windows-product-activation-of-azure-virtual-machines"></a>Azure Virtual Machines의 Windows 제품 정품 인증을 위한 Azure KMS 엔드포인트 이해

Azure는 VM이 있는 클라우드 지역에 따라 KMS(키 관리 서비스) 활성화에 대해 서로 다른 끝점을 사용합니다. 이 문제 해결 가이드를 사용하는 경우 사용자의 지역에 해당하는 적절한 KMS 엔드포인트를 사용합니다.

* Azure 퍼블릭 클라우드 지역: kms.core.windows.net:1688
* Azure 중국 21Viant 국가 클라우드 지역: kms.core.chinacloudapi.cn:1688
* Azure 독일 국가 클라우드 지역: kms.core.cloudapi.de:1688
* Azure US Gov 국가 클라우드 지역: kms.core.usgovcloudapi.net:1688

## <a name="symptom"></a>증상

Windows Azure VM을 활성화하려고 할 때 다음 샘플과 유사한 오류 메시지가 표시됩니다.

**오류: 0xC004F074 소프트웨어 라이센싱 서비스에서 컴퓨터를 정품 인증할 수 없다고 보고했습니다. KMS(키 관리 서비스)에 연락할 수 없습니다. 자세한 내용은 응용 프로그램 이벤트 로그를 참조하십시오.**

## <a name="cause"></a>원인

일반적으로 Azure VM 정품 인증 문제는 Windows VM이 적절한 KMS 클라이언트 설정 키를 사용하여 구성되어 있지 않거나 Windows VM에 Azure KMS 서비스(kms.core.windows.net, 포트 1688)에 대한 연결 문제가 있는 경우에 발생합니다. 

## <a name="solution"></a>해결 방법

>[!NOTE]
>사이트 간 VPN 및 강제 터널링을 사용하는 경우 [Azure 사용자 지정 경로 사용을 참조하여 강제 터널링을 사용하여 KMS 활성화를 활성화합니다.](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-forced-tunneling) 
>
>ExpressRoute를 사용 하 고 기본 경로 게시 하는 경우, 참조 [ExpressRoute 회로에 연결 된 가상 네트워크에 인터넷 연결을 차단할 수 있습니까?](https://docs.microsoft.com/azure/expressroute/expressroute-faqs)

### <a name="step-1-configure-the-appropriate-kms-client-setup-key"></a>1단계 적절한 KMS 클라이언트 설정 키 구성

사용자 지정 이미지에서 만든 VM의 경우 VM에 적합한 KMS 클라이언트 설정 키를 구성해야 합니다.

1. 관리자 권한의 명령 프롬프트에서 **slmgr.vbs /dlv**를 실행합니다. 출력에서 설명 값을 확인하고 소매(소매 채널) 또는 볼륨(VOLUME_KMSCLIENT) 라이선스 미디어에서 만들어졌는지를 확인합니다.
  

    ```
    cscript c:\windows\system32\slmgr.vbs /dlv
    ```

2. **slmgr.vbs /dlv**가 소매 채널을 표시하는 경우 다음 명령을 실행하여 사용된 Windows Server의 버전에 [KMS 클라이언트 설정 키](https://technet.microsoft.com/library/jj612867%28v=ws.11%29.aspx?f=255&MSPPError=-2147217396)를 설정하고 활성화를 다시 시도합니다. 

    ```
    cscript c:\windows\system32\slmgr.vbs /ipk <KMS client setup key>

    cscript c:\windows\system32\slmgr.vbs /ato
     ```

    예를 들어 Windows Server 2016 데이터 센터에서 다음 명령을 실행합니다.

    ```
    cscript c:\windows\system32\slmgr.vbs /ipk CB7KF-BWN84-R7R2Y-793K2-8XDDG
    ```

### <a name="step-2-verify-the-connectivity-between-the-vm-and-azure-kms-service"></a>2단계 VM과 Azure KMS 서비스 간의 연결 확인

1. 정품 인증되지 않은 VM의 로컬 폴더에 [PSping](http:/technet.microsoft.com/sysinternals/jj729731.aspx) 도구를 다운로드하여 추출합니다. 

2. 시작하고 Windows PowerShell에서 검색하고 Windows PowerShell을 마우스 오른쪽 단추로 클릭한 다음 관리자 권한으로 실행을 선택합니다.

3. VM이 올바른 Azure KMS 서버를 사용하도록 구성되어 있는지 확인합니다. 이렇게 하려면 다음 명령을 실행합니다.
  
    ```powershell
    Invoke-Expression "$env:windir\system32\cscript.exe $env:windir\system32\slmgr.vbs /skms kms.core.windows.net:1688"
    ```

    명령이 다음을 반환해야 합니다. 키 관리 서비스 컴퓨터 이름을 kms.core.windows.net:1688으로 성공적으로 설정합니다.

4. KMS 서버에 연결한 Psping을 사용하여 확인합니다. Pstools.zip 다운로드를 추출한 폴더로 전환하고 다음을 실행합니다.
  
    ```
    \psping.exe kms.core.windows.net:1688
    ```
   출력의 마지막 두 번째 줄에서 전송 = 4, 수신 = 4, 손실 = 0 (0% 손실)이 표시되어야 합니다.

   손실이 0(영)보다 큰 경우 VM은 KMS 서버에 연결되어 있지 않습니다. 이 경우에 VM이 가상 네트워크에 있고 사용자 지정 DNS 서버를 지정하면 해당 DNS 서버가 kms.core.windows.net을 확인할 수 있어야 합니다. 또는 DNS 서버가 kms.core.windows.net을 확인할 수 있도록 변경합니다.

   모든 DNS 서버를 가상 네트워크에서 제거하면 VM은 Azure의 내부 DNS 서비스를 사용할 수 있습니다. 이 서비스는 kms.core.windows.net을 확인할 수 있습니다.
  
    또한 1688 포트가 있는 KMS 끝점으로의 아웃바운드 네트워크 트래픽이 VM의 방화벽에 의해 차단되지 않았는지 확인합니다.

5. 네트워크 [감시자 Next Hop을](https://docs.microsoft.com/azure/network-watcher/network-watcher-next-hop-overview) 사용하여 해당 VM에서 대상 IP 23.102.135.246(kms.core.windows.net) 또는 해당 지역에 적용되는 적절한 KMS 끝점의 IP가 **인터넷인지**확인합니다.  결과가 VirtualAppliance 또는 VirtualNetworkGateway인 경우 기본 경로가 있을 수 있습니다.  네트워크 관리자에게 문의하여 올바른 작업 과정을 결정합니다.  해당 솔루션이 조직의 정책과 일치하는 경우 [사용자 지정 경로일](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/custom-routes-enable-kms-activation) 수 있습니다.

6. kms.core.windows.net에 성공적으로 연결되었는지 확인한 후에 해당 관리자 권한 Windows PowerShell 프롬프트에서 다음 명령을 실행합니다. 이 명령은 여러 번 활성화되도록 시도합니다.

    ```powershell
    1..12 | ForEach-Object { Invoke-Expression "$env:windir\system32\cscript.exe $env:windir\system32\slmgr.vbs /ato" ; start-sleep 5 }
    ```

    성공적으로 활성화되면 다음과 같은 정보를 반환합니다.
    
    **윈도우 활성화 (R), ServerDatacenter 버전 (12345678-1234-1234-1234-1234-12345678) ...  제품이 성공적으로 활성화되었습니다.**

## <a name="faq"></a>FAQ 

### <a name="i-created-the-windows-server-2016-from-azure-marketplace-do-i-need-to-configure-kms-key-for-activating-the-windows-server-2016"></a>Azure Marketplace에서 Windows Server 2016을 만들었습니다. Windows Server 2016을 활성화하기 위해 KMS 키를 구성해야 하나요? 

 
아니요. Azure Marketplace의 이미지는 적절한 KMS 클라이언트 설정 키를 이미 구성했습니다. 

### <a name="does-windows-activation-work-the-same-way-regardless-if-the-vm-is-using-azure-hybrid-use-benefit-hub-or-not"></a>VM이 Azure HUB(Hybrid Use Benefit)를 사용하는지 여부와 상관없이 동일하게 Windows 정품 인증이 작동하나요? 

 
예. 
 

### <a name="what-happens-if-windows-activation-period-expires"></a>Windows 정품 인증 기간이 만료된 경우 어떻게 되나요? 

 
유예 기간이 만료되고 Windows가 여전히 활성화되지 않은 경우 Windows Server 2008 R2 및 Windows 이후 버전은 정품 인증에 대한 추가 알림을 표시합니다. 바탕 화면 배경 화면을 검은색으로 유지하고 Windows 업데이트를 통해 보안 및 중요 업데이트만을 설치하고 선택 사항 업데이트를 설치하지 않습니다. [라이선스 조건](https://technet.microsoft.com/library/ff793403.aspx) 페이지의 맨 아래에 있는 알림 섹션을 참조하세요.   

## <a name="need-help-contact-support"></a>도움 필요 시 지원에 문의

추가 도움이 필요한 경우 [지원에 문의](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)하여 문제를 신속하게 해결하세요.
