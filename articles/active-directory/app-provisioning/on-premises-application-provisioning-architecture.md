---
title: Azure AD 온-프레미스 애플리케이션 프로비저닝 아키텍처 | Microsoft Docs
description: 온-프레미스 애플리케이션 프로비저닝 아키텍처의 개요를 설명합니다.
services: active-directory
author: billmath
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 05/28/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 696f2f9e54e350b2cce952cbec8d745779e1d00a
ms.sourcegitcommit: ff1aa951f5d81381811246ac2380bcddc7e0c2b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/07/2021
ms.locfileid: "111570361"
---
# <a name="azure-ad-on-premises-application-provisioning-architecture"></a>Azure AD 온-프레미스 애플리케이션 프로비저닝 아키텍처

>[!IMPORTANT]
> 온-프레미스 프로비저닝 미리 보기는 현재 초대 전용 미리 보기로 제공됩니다. [여기](https://aka.ms/onpremprovisioningpublicpreviewaccess)에서 기능에 대한 액세스를 요청할 수 있습니다. 일반 공급을 준비 중이므로 향후 몇 개월 동안 더 많은 고객 및 커넥터에 대한 미리 보기를 공개할 예정입니다.

## <a name="overview"></a>개요

다음 다이어그램에서는 온-프레미스 애플리케이션 프로비저닝의 작동 방식을 보여 줍니다.

![Architecture](.\media\on-premises-application-provisioning-architecture\arch-3.png)

사용자를 온-프레미스 애플리케이션으로 프로비저닝하는 세 가지 기본 구성 요소가 있습니다.

- 프로비저닝 에이전트는 Azure AD와 온-프레미스 환경 간에 연결을 제공합니다.
- ECMA 호스트는 Azure AD의 프로비저닝 요청을 대상 애플리케이션에 대한 요청으로 변환합니다. Azure AD와 애플리케이션 간에 게이트웨이 역할을 합니다. 이를 통해 Microsoft Identity Manager에서 사용되는 기존 ECMA2 커넥터를 가져올 수 있습니다. SCIM 애플리케이션 또는 SCIM 게이트웨이를 빌드한 경우에는 ECMA 호스트가 필요하지 않습니다.
- Azure AD 프로비저닝 서비스는 동기화 엔진 역할을 합니다.

>[!NOTE]
> MIM 동기화는 필요하지 않습니다. 그러나 MIM 동기화를 사용하여 ECMA 호스트로 가져오기 전에 ECMA 커넥터를 빌드하고 테스트할 수 있습니다.


### <a name="firewall-requirements"></a>방화벽 요구 사항

회사 네트워크에 대한 인바운드 연결을 열 필요가 없습니다. 프로비저닝 에이전트는 프로비저닝 서비스에 대한 아웃바운드 연결만 사용하므로 들어오는 연결에 대한 방화벽 포트를 열지 않아도 됩니다. 모든 연결은 아웃바운드이고 보안 채널을 통해 발생하기 때문에 경계(DMZ) 네트워크가 필요하지 않습니다. 

## <a name="agent-best-practices"></a>에이전트 모범 사례
- 자동 Azure AD Connect 프로비저닝 에이전트 자동 업데이트 서비스가 실행 중인지 확인합니다. 에이전트를 설치할 때 기본적으로 사용하도록 설정됩니다. Microsoft가 배포를 지원하려면 자동 업데이트가 필요합니다.
- 에이전트와 Azure 간 아웃바운드 TLS 통신에 대한 모든 형태의 인라인 검사를 방지합니다. 이 유형의 인라인 검사를 수행하면 통신 흐름이 저하됩니다.
- 에이전트는 Azure 및 애플리케이션과 둘 다 통신해야 하므로 에이전트의 배치는 두 연결의 대기 시간에 영향을 줍니다. 각 네트워크 연결을 최적화하여 엔드투엔드 트래픽의 대기 시간을 최소화할 수 있습니다. 각 연결은 다음을 통해 최적화할 수 있습니다.
- 홉의 두 끝 사이의 거리 줄이기
- 트래버스하기 적합한 네트워크 선택. 예를 들어 공용 인터넷보다 프라이빗 네트워크를 트래버스하는 것이 전용 링크가 있으므로 더 빠를 수 있습니다.

## <a name="provisioning-agent-questions"></a>프로비전 에이전트 질문
**프로비전 에이전트의 GA 버전은 무엇인가요?**

최신 GA 버전의 프로비저닝 에이전트는 [Azure AD Connect 프로비저닝 에이전트: 버전 릴리스 기록](provisioning-agent-release-version-history.md)을 참조하세요.

**내 프로비전 에이전트의 버전을 어떻게 알 수 있나요?**

 1. Provisioning Agent가 설치된 Windows Server에 로그인합니다.
 2. 제어판 -> 프로그램 제거/변경 메뉴로 이동
 3. Microsoft Azure AD Connect 프로비전 에이전트 항목에 해당하는 버전 찾기

**Microsoft에서 프로비전 에이전트 업데이트를 자동으로 푸시하나요?**

예. Microsoft Azure AD Connect 에이전트 업데이트 프로그램 Windows 서비스를 시작하여 실행하는 경우 Microsoft에서 프로비저닝 에이전트를 자동으로 업데이트합니다. 문제 해결을 지원하려면 에이전트가 최신 상태인지 확인해야 합니다.

**Azure AD Connect 또는 MIM(Microsoft Identity Manager)을 실행하는 동일한 서버에 프로비저닝 에이전트를 설치할 수 있나요?**

예. Azure AD Connect 또는 MIM을 실행하는 동일한 서버에 프로비저닝 에이전트를 설치할 수 있지만 필수는 아닙니다.

**아웃바운드 HTTP 통신에 프록시 서버를 사용하도록 프로비전 에이전트를 구성하려면 어떻게 할까요?**

프로비전 에이전트는 아웃바운드 프록시 사용을 지원합니다. 에이전트 구성 파일 **C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\AADConnectProvisioningAgent.exe.config** 를 편집하여 에이전트를 구성할 수 있습니다. 해당 구성 파일의 끝 쪽으로 닫는 </configuration> 태그 바로 앞에 다음 줄을 추가합니다. [proxy-server] 및 [proxy-port] 변수를 프록시 서버 이름 및 포트 값으로 바꿉니다.
```
    <system.net>
        <defaultProxy enabled="true" useDefaultCredentials="true">
            <proxy
                usesystemdefault="true"
                proxyaddress="http://[proxy-server]:[proxy-port]"
                bypassonlocal="true"
            />
        </defaultProxy>
    </system.net>
```
**프로비전 에이전트가 Azure AD 테넌트와 통신할 수 있고 에이전트에 필요한 포트를 차단하는 방화벽이 없는지 확인하려면 어떻게 할까요?**

필요한 모든 포트가 열려 있는지 확인할 수도 있습니다.

**프로비전 에이전트를 제거하려면 어떻게 할까요?**
1. Provisioning Agent가 설치된 Windows Server에 로그인합니다.
2. 제어판 -> 프로그램 제거/변경 메뉴로 이동
3. 다음 프로그램을 제거합니다.
     - Microsoft Azure AD Connect 프로비전 에이전트
     - Microsoft Azure AD Connect 에이전트 업데이트
     - Microsoft Azure AD Connect 프로비전 에이전트 패키지


## <a name="next-steps"></a>다음 단계

- [앱 프로비저닝](user-provisioning.md)
- [Azure AD ECMA 커넥터 호스트 필수 조건](on-premises-ecma-prerequisites.md)
- [Azure AD ECMA 커넥터 호스트 설치](on-premises-ecma-install.md)
- [Azure AD ECMA 커넥터 호스트 구성](on-premises-ecma-configure.md)
