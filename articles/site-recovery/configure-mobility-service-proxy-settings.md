---
title: Azure 대 Azure 재해 복구에 대한 Mobility Service 프록시 설정 구성 | Microsoft Docs
description: 고객이 원본 환경에서 프록시를 사용할 때 Mobility Service를 구성하는 방법에 대한 세부 정보를 제공합니다.
services: site-recovery
author: sideeksh
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 03/18/2020
ms.author: sideeksh
ms.openlocfilehash: 429ffcab147142ae2e96de13b7c9e1e5ee1ac7ba
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "86133223"
---
# <a name="configure-mobility-service-proxy-settings-for-azure-to-azure-disaster-recovery"></a>Azure 대 Azure 재해 복구에 대한 Mobility Service 프록시 설정 구성

이 문서에서는 [Azure Site Recovery](site-recovery-overview.md)를 사용하여 한 지역에서 다른 지역으로 Azure VM을 복제 및 복구할 때 대상 Azure VM(가상 머신)에서 네트워킹 구성을 사용자 지정하는 방법에 대한 지침을 제공합니다.

이 문서의 목적은 Azure 대 Azure 재해 복구 시나리오에서 Azure Site Recovery Mobility Service에 대해 프록시 설정을 구성하기 위한 단계를 안내하는 것입니다. 

프록시는 엔드포인트에 대한 네트워크 연결을 허용하거나 허용하지 않는 네트워크 게이트웨이입니다. 일반적으로 프록시는 네트워크 엔드포인트에 액세스를 시도하는 클라이언트 컴퓨터 외의 컴퓨터입니다. 바이패스 목록을 사용하면 클라이언트가 프록시를 거치지 않고도 엔드포인트에 직접 연결할 수 있습니다. 사용자 이름 및 암호는 네트워크 관리자를 통해 프록시에 선택적으로 설정될 수 있으므로 인증된 클라이언트만 프록시를 사용할 수 있습니다. 

## <a name="before-you-start"></a>시작하기 전에

Site Recovery가 [이 시나리오](azure-to-azure-architecture.md)에 재해 복구를 지원하는 방법을 알아봅니다.
[Azure Site Recovery](azure-to-azure-about-networking.md)를 사용하여 한 지역에서 다른 지역으로 Azure VM을 복제 및 복구할 때의 [네트워킹 지침](site-recovery-overview.md)에 대해 파악합니다.
프록시가 조직의 필요에 따라 적절하게 설정되었는지 확인합니다.

## <a name="configure-the-mobility-service"></a>Mobility Service 구성

Mobility Service는 인증되지 않은 프록시만 지원합니다. Site Recovery 엔드포인트와의 통신에 대한 프록시 세부 정보를 입력하는 두 가지 방법을 안내합니다. 

### <a name="method-1-auto-detection"></a>방법 1: 자동 검색

Mobility Service는 복제를 사용하도록 설정하는 동안 환경 설정 또는 IE 설정(Windows에만 해당)에서 프록시 설정을 자동으로 검색합니다. 

- Windows OS: 복제를 사용하도록 설정하는 동안 Mobility Service는 로컬 시스템 사용자에 대해 Internet Explorer에 구성된 프록시 설정을 검색합니다. 로컬 시스템 계정에 대한 프록시를 설정하려면 관리자가 psexec를 사용하여 명령 프롬프트를 시작하고 Internet Explorer를 사용할 수 있습니다. 
- Windows OS: 프록시 설정은 환경 변수 http_proxy 및 no_proxy로 구성됩니다. 
- Linux OS: 프록시 설정은 /etc/profile 또는/etc/environment에서 환경 변수 http_proxy, no_proxy로 구성됩니다. 
- 자동 검색된 프록시 설정은 Mobility Service 프록시 구성 파일 ProxyInfo.conf로 저장됩니다. 
- ProxyInfo.conf 기본 위치 
    - Windows: C:\ProgramData\Microsoft Azure Site Recovery\Config\ProxyInfo.conf 
    - Linux: /usr/local/InMage/config/ProxyInfo.conf


### <a name="method-2-provide-custom-application-proxy-settings"></a>방법 2: 사용자 지정 애플리케이션 프록시 설정 제공

이 경우, 고객은 Mobility Service 구성 파일 ProxyInfo.conf에서 사용자 지정 애플리케이션 프록시 설정을 제공합니다. 이 방법을 사용하면 고객이 컴퓨터에 있는 나머지 애플리케이션용 프록시(또는 프록시 없이)보다는 Mobility Service 전용 프록시 또는 Azure Site Recovery Mobility Service용 다른 프록시를 제공할 수 있습니다.

## <a name="proxy-template"></a>프록시 템플릿
ProxyInfo.conf는 다음 템플릿 [proxy] Address=http://1.2.3.4 Port=5678 BypassList=hypervrecoverymanager.windowsazure.com,login.microsoftonline.com,blob.core.windows.net을 포함합니다. BypassList는 '*.windows.net'과 같은 와일드 카드를 지원하지는 않지만 windows.net을 제공하면 우회하기에 충분합니다. 

## <a name="next-steps"></a>다음 단계:
- Azure VM 복제를 위한 [네트워킹 지침](./azure-to-azure-about-networking.md)에 대해 알아봅니다.
- [Azure VM을 복제](./azure-to-azure-quickstart.md)하여 재해 복구를 배포합니다.
