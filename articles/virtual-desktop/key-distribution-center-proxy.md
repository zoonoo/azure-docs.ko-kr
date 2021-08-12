---
title: Kerberos 키 배포 센터 프록시 설정 Azure Virtual Desktop - Azure
description: Kerberos 키 배포 센터 프록시를 사용하도록 Azure Virtual Desktop 호스트 풀을 설정하는 방법입니다.
author: Heidilohr
ms.topic: how-to
ms.date: 05/04/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 55c68902cebbb6832a9c09c5390d9f43d381bf21
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/09/2021
ms.locfileid: "111757556"
---
# <a name="configure-a-kerberos-key-distribution-center-proxy"></a>Kerberos 키 배포 센터 프록시 구성

금융 조직이나 정부 조직과 같이 보안에 민감한 고객은 스마트 카드를 사용하여 로그인하는 경우가 많습니다. 스마트 카드는 MFA(다단계 인증)를 요구하여 배포를 더욱 안전하게 만듭니다. 그러나 Azure Virtual Desktop 세션의 RDP 부분에 대한 스마트 카드에는 Kerberos 인증을 위한 AD(Active Directory) 도메인 컨트롤러와 직접 연결, 즉 “가시선”이 필요합니다. 이 직접 연결을 사용하지 않으면 사용자가 원격 연결에서 조직의 네트워크에 자동으로 로그인할 수 없습니다. Azure Virtual Desktop 배포의 사용자는 KDC 프록시 서비스를 사용하여 이 인증 트래픽을 프록시하고 원격으로 로그인할 수 있습니다. KDC 프록시는 Azure Virtual Desktop 세션의 원격 데스크톱 프로토콜에 대한 인증을 허용하여 사용자가 안전하게 로그인하도록 합니다. 따라서 더욱 쉽게 재택근무할 수 있으며 특정 재해 복구 시나리오를 더욱 원활하게 실행할 수 있습니다.

그러나 KDC 프록시를 설정하면 일반적으로 Windows Server 2016 이상에서 Windows Server 게이트웨이 역할이 할당됩니다. 원격 데스크톱 서비스 역할을 사용하여 Azure Virtual Desktop에 로그인하려면 어떻게 해야 하나요? 이 질문에 답변하기 위해 구성 요소를 간략하게 살펴보겠습니다.

Azure Virtual Desktop 서비스에는 인증해야 하는 두 가지 구성 요소가 있습니다.

- 사용자에게 액세스 권한이 있는 사용 가능한 데스크톱 또는 애플리케이션 목록을 제공하는 Azure Virtual Desktop 클라이언트의 피드. 이 인증 프로세스는 Azure Active Directory에서 수행되므로, 이 구성 요소는 이 문서에서 중점적으로 다루지 않습니다.
- 사용자가 사용 가능한 리소스 중 하나를 선택한 결과로 생성되는 RDP 세션. 이 구성 요소는 Kerberos 인증을 사용하며, 원격 사용자에 대한 KDC 프록시가 필요합니다.

이 문서에서는 Azure Portal에서 Azure Virtual Desktop 클라이언트의 피드를 구성하는 방법을 보여 줍니다. RD 게이트웨이 역할을 구성하는 방법을 알아보려면 [RD 게이트웨이 역할 배포](/windows-server/remote/remote-desktop-services/remote-desktop-gateway-role)를 참조하세요.

## <a name="requirements"></a>요구 사항

KDC 프록시를 사용하여 Azure Virtual Desktop 세션 호스트를 구성하려면 다음 항목이 필요합니다.

- Azure Portal에 대한 액세스 권한 및 Azure 관리자 계정.
- 원격 클라이언트 머신은 Windows 10 또는 Windows 7을 실행하고 있어야 하며 [Windows 데스크톱 클라이언트](/windows-server/remote/remote-desktop-services/clients/windowsdesktop)가 설치되어 있어야 합니다. 현재 웹 클라이언트는 지원되지 않습니다.
- KDC 프록시가 머신에 이미 설치되어 있어야 합니다. 이 작업을 수행하는 방법을 알아보려면 [ Azure Virtual Desktop에 대한 RD 게이트웨이 역할 설정](/windows-server/remote/remote-desktop-services/remote-desktop-gateway-role)을 참조하세요.
- 머신의 OS는 Windows Server 2016 이상이어야 합니다.

이러한 요구 사항을 충족하는지 확인했으면 시작할 준비가 된 것입니다.

## <a name="how-to-configure-the-kdc-proxy"></a>KDC 프록시를 구성하는 방법

KDC 프록시를 구성하려면 다음을 수행합니다.

1. Azure Portal에 관리자 권한으로 로그인합니다.

2. Azure Virtual Desktop 페이지로 이동합니다.

3. KDC 프록시를 사용하도록 설정하려는 호스트 풀을 선택한 다음, **RDP 속성** 을 선택합니다.

4. **고급** 탭을 선택하고 공백 없이 다음 형식으로 값을 입력합니다.

    
    > kdcproxyname:s:\<fqdn\>
    

    > [!div class="mx-imgBorder"]
    > ![4단계에서 설명한 대로 고급 탭을 선택하고 값을 입력하는 것을 보여 주는 스크린샷](media/advanced-tab-selected.png)

5. **저장** 을 선택합니다.

6. 이제 선택한 호스트 풀이 4단계에서 입력한 kdcproxyname 값을 포함하는 RDP 연결 파일의 실행을 시작해야 합니다.

## <a name="next-steps"></a>다음 단계

KDC 프록시의 원격 데스크톱 서비스 쪽을 관리하고 RD 게이트웨이 역할을 할당하는 방법을 알아보려면 [RD 게이트웨이 역할 배포](/windows-server/remote/remote-desktop-services/remote-desktop-gateway-role)를 참조하세요.

KDC 프록시 서버를 스케일링하는 데 관심이 있는 경우 [RD 웹 및 게이트웨이 웹 프런트에 고가용성 추가](/windows-server/remote/remote-desktop-services/rds-rdweb-gateway-ha)에서 KDC 프록시에 대해 고가용성을 설정하는 방법을 알아봅니다.