---
title: Azure에 대 한 azure 재해 복구에 대 한 모바일 서비스 프록시 설정 구성 | Microsoft Docs
description: 고객이 원본 환경에서 프록시를 사용 하는 경우 모바일 서비스를 구성 하는 방법에 대 한 세부 정보를 제공 합니다.
services: site-recovery
author: sideeksh
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 03/18/2020
ms.author: sideeksh
ms.openlocfilehash: 429ffcab147142ae2e96de13b7c9e1e5ee1ac7ba
ms.sourcegitcommit: e995f770a0182a93c4e664e60c025e5ba66d6a45
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86133223"
---
# <a name="configure-mobility-service-proxy-settings-for-azure-to-azure-disaster-recovery"></a>Azure에 대 한 azure 재해 복구에 대 한 모바일 서비스 프록시 설정 구성

이 문서에서는 [Azure Site Recovery](site-recovery-overview.md)를 사용 하 여 한 지역에서 다른 지역으로 azure vm을 복제 및 복구할 때 대상 azure vm (가상 머신)에서 네트워킹 구성을 사용자 지정 하는 방법에 대 한 지침을 제공 합니다.

이 문서의 목적은 Azure에서 Azure로 재해 복구 시나리오에서 Azure Site Recovery 모바일 서비스에 대 한 프록시 설정을 구성 하는 단계를 제공 하는 것입니다. 

프록시는 끝점에 대 한 네트워크 연결을 허용 하거나 허용 하지 않는 네트워크 게이트웨이입니다. 일반적으로 프록시는 네트워크 끝점에 액세스를 시도 하는 클라이언트 컴퓨터 외부의 컴퓨터입니다. 바이패스 목록을 사용 하면 클라이언트가 프록시를 거치지 않고도 끝점에 직접 연결할 수 있습니다. 인증 된 클라이언트만 프록시를 사용할 수 있도록 네트워크 관리자가 프록시에 대 한 사용자 이름 및 암호를 선택적으로 설정할 수 있습니다. 

## <a name="before-you-start"></a>시작하기 전에

Site Recovery가 [이 시나리오](azure-to-azure-architecture.md)에 재해 복구를 지원하는 방법을 알아봅니다.
[Azure Site Recovery](site-recovery-overview.md)를 사용 하 여 한 지역에서 다른 지역으로 Azure vm을 복제 및 복구할 때의 [네트워킹 지침](azure-to-azure-about-networking.md) 을 이해 합니다.
조직의 요구에 따라 프록시가 적절 하 게 설정 되었는지 확인 합니다.

## <a name="configure-the-mobility-service"></a>모바일 서비스 구성

모바일 서비스는 인증 되지 않은 프록시만 지원 합니다. Site Recovery 끝점과의 통신에 대 한 프록시 정보를 입력 하는 두 가지 방법을 제공 합니다. 

### <a name="method-1-auto-detection"></a>방법 1: 자동 검색

모바일 서비스는 복제를 사용 하도록 설정 하는 동안 환경 설정 또는 IE 설정에서 프록시 설정을 자동으로 검색 합니다 (Windows에만 해당). 

- Windows OS: 복제를 사용 하도록 설정 하는 동안 모바일 서비스는 로컬 시스템 사용자에 대해 Internet Explorer에 구성 된 프록시 설정을 검색 합니다. 로컬 시스템 계정에 대 한 프록시를 설정 하려면 관리자가 psexec를 사용 하 여 명령 프롬프트를 시작 하 고 Internet Explorer를 사용할 수 있습니다. 
- Windows OS: 프록시 설정은 http_proxy 및 no_proxy 환경 변수로 구성 됩니다. 
- Linux OS: 프록시 설정은/etc/profile 또는/etc/environment에서 환경 변수 http_proxy no_proxy로 구성 됩니다. 
- 자동 검색 된 프록시 설정은 모바일 서비스 프록시 구성 파일 ProxyInfo. 
- ProxyInfo의 기본 위치 
    - Windows: C:\ProgramData\Microsoft Azure 사이트 복원 
    - Linux:/usr/local/InMage/config/ProxyInfo.conf


### <a name="method-2-provide-custom-application-proxy-settings"></a>방법 2: 사용자 지정 응용 프로그램 프록시 설정 제공

이 경우, 고객은 모바일 서비스 구성 파일 ProxyInfo. 이 방법을 사용 하면 고객이 컴퓨터의 나머지 응용 프로그램에 대 한 프록시 (또는 프록시 없음) 보다 모바일 서비스에 대 한 프록시 또는 Azure Site Recovery 모바일 서비스에 대 한 다른 프록시를 제공할 수 있습니다.

## <a name="proxy-template"></a>프록시 템플릿
ProxyInfo는 다음 템플릿 [proxy] Address = http://1.2.3.4 Port = 5678 BypassList = hypervrecoverymanager. windowsazure.servicebus, microsoftonline,,를 포함 합니다. BypassList는 ' *. windows.net '와 같은 와일드 카드를 지원 하지 않지만 windows.net는 무시 해도 좋습니다. 

## <a name="next-steps"></a>다음 단계:
- Azure VM 복제를 위한 [네트워킹 지침](./azure-to-azure-about-networking.md)에 대해 알아봅니다.
- [Azure VM을 복제](./azure-to-azure-quickstart.md)하여 재해 복구를 배포합니다.
