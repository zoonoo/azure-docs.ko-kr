---
title: Azure에서 Azure 재해 복구에 대한 이동성 서비스 프록시 설정 구성 | 마이크로 소프트 문서
description: 고객이 소스 환경에서 프록시를 사용할 때 모빌리티 서비스를 구성하는 방법에 대한 세부 정보를 제공합니다.
services: site-recovery
author: sideeksh
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 03/18/2020
ms.author: sideeksh
ms.openlocfilehash: 3d33b5a89a718a41e5c547551f6e7eb4f7033a63
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79503128"
---
# <a name="configure-mobility-service-proxy-settings-for-azure-to-azure-disaster-recovery"></a>Azure에서 Azure 재해 복구에 대한 이동성 서비스 프록시 설정 구성

이 문서에서는 [Azure 사이트 복구를](site-recovery-overview.md)사용하여 한 지역에서 다른 리전으로 Azure VM을 복제하고 복구할 때 대상 VM(대상 Azure 가상 시스템)에서 네트워킹 구성을 사용자 지정하는 방법에 대한 지침을 제공합니다.

이 문서의 목적은 Azure에서 Azure 재해 복구 시나리오에 있는 Azure 사이트 복구 모빌리티 서비스에 대한 프록시 설정을 구성하는 단계를 제공하는 것입니다. 

프록시는 엔드포인트에 대한 네트워크 연결을 허용/허용하지 않는 네트워크 게이트웨이입니다. 일반적으로 프록시는 네트워크 끝점에 액세스하려고 하는 클라이언트 컴퓨터 외부의 컴퓨터입니다. 바이패스 목록을 사용하면 클라이언트가 프록시를 거치지 않고 끝점에 직접 연결할 수 있습니다. 인증된 클라이언트만 프록시를 사용할 수 있도록 네트워크 관리자가 프록시에 대해 사용자 이름과 암호를 선택적으로 설정할 수 있습니다. 

## <a name="before-you-start"></a>시작하기 전에

Site Recovery가 [이 시나리오](azure-to-azure-architecture.md)에 재해 복구를 지원하는 방법을 알아봅니다.
[Azure 사이트 복구를](site-recovery-overview.md)사용하여 한 리전에서 다른 리전으로 Azure VM을 복제하고 복구할 때 [네트워킹 지침을](azure-to-azure-about-networking.md) 이해합니다.
조직의 요구 사항에 따라 프록시가 적절하게 설정되어 있는지 확인합니다.

## <a name="configure-the-mobility-service"></a>모빌리티 서비스 구성

모빌리티 서비스는 인증되지 않은 프록시만 지원합니다. 사이트 복구 끝점과의 통신을 위해 프록시 세부 정보를 입력하는 두 가지 방법을 제공합니다. 

### <a name="method-1-auto-detection"></a>방법 1: 자동 감지

모빌리티 서비스 자동은 복제를 활성화하는 동안 환경 설정 또는 IE 설정(Windows만)에서 프록시 설정을 감지합니다. 

- Windows OS: 복제 를 사용하도록 설정하는 동안 이동성 서비스는 로컬 시스템 사용자를 위한 Internet Explorer에서 구성된 프록시 설정을 검색합니다. 로컬 시스템 계정에 대한 프록시를 설정하려면 관리자는 psexec을 사용하여 명령 프롬프트를 실행한 다음 Internet Explorer를 시작할 수 있습니다. 
- Windows OS: 프록시 설정은 환경 변수http_proxy 및 no_proxy 구성됩니다. 
- Linux OS: 프록시 설정은 환경 변수가 no_proxy http_proxy /etc/profile 또는 /etc/환경에서 구성됩니다. 
- 자동 감지된 프록시 설정은 모빌리티 서비스 프록시 구성 파일 ProxyInfo.conf에 저장됩니다. 
- 프록시정보.conf의 기본 위치 
    - 윈도우: C:\프로그램데이터\마이크로소프트 Azure 사이트 복구\Config\ProxyInfo.conf 
    - 리눅스: /usr/로컬/인마지/config/ProxyInfo.conf


### <a name="method-2-provide-custom-application-proxy-settings"></a>방법 2: 사용자 지정 응용 프로그램 프록시 설정 제공

이 경우 고객은 이동성 서비스 config 파일 ProxyInfo.conf에서 사용자 지정 응용 프로그램 프록시 설정을 제공합니다. 이 방법을 사용하면 고객은 모빌리티 서비스 또는 Azure 사이트 복구 모빌리티 서비스에 대한 다른 프록시에 대해서만 프록시를 제공할 수 있습니다.

## <a name="proxy-template"></a>프록시 템플릿
ProxyInfo.conf는 다음 템플릿을 포함http://1.2.3.4 [프록시] 주소 = 포트 = 5678 바이 패스리스트 = hypervrecoverymanager.windowsazure.com, login.microsoftonline.com,blob.core.windows.net. BypassList는 '*.windows.net'과 같은 와일드카드를 지원하지 않지만 windows.net 제공하면 우회하기에 충분합니다. 

## <a name="next-steps"></a>다음 단계:
- Azure VM 복제에 대한 [네트워킹 지침을](site-recovery-azure-to-azure-networking-guidance.md) 읽어보십시오.
- [Azure VM을 복제](site-recovery-azure-to-azure.md)하여 재해 복구를 배포합니다.