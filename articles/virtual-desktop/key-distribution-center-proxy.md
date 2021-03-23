---
title: Kerberos 키 배포 센터 프록시 Windows 가상 데스크톱 설정-Azure
description: Kerberos 키 배포 센터 프록시를 사용 하도록 Windows 가상 데스크톱 호스트 풀을 설정 하는 방법입니다.
author: Heidilohr
ms.topic: how-to
ms.date: 03/20/2021
ms.author: helohr
manager: lizross
ms.openlocfilehash: bcf28fbc0d2f4ec9eeac5bcb8f0b2c9b65a62b6b
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104775040"
---
# <a name="configure-a-kerberos-key-distribution-center-proxy-preview"></a>Kerberos 키 배포 센터 프록시 구성 (미리 보기)

> [!IMPORTANT]
> 이 기능은 현재 공개 미리 보기로 제공됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며, 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

금융 또는 정부 조직과 같은 보안에 민감한 고객은 종종 스마트 카드를 사용 하 여 로그인 합니다. 스마트 카드는 MFA (다단계 인증)를 요구 하 여 배포를 더욱 안전 하 게 만듭니다. 그러나 Windows 가상 데스크톱 세션의 RDP 부분에 대해 스마트 카드에는 Kerberos 인증을 위한 AD (Active Directory) 도메인 컨트롤러와 직접 연결 또는 "줄 시야"가 필요 합니다. 이 직접 연결을 사용 하지 않으면 사용자가 원격 연결에서 조직의 네트워크에 자동으로 로그인 할 수 없습니다. Windows 가상 데스크톱 배포의 사용자는 KDC 프록시 서비스를 사용 하 여이 인증 트래픽을 프록시 하 고 원격으로 로그인 할 수 있습니다. KDC 프록시는 Windows 가상 데스크톱 세션의 원격 데스크톱 프로토콜에 대 한 인증을 허용 하 여 사용자가 안전 하 게 로그인 하도록 합니다. 이렇게 하면 홈에서 작업을 훨씬 쉽게 수행할 수 있으며 특정 재해 복구 시나리오를 더욱 원활 하 게 실행할 수 있습니다.

그러나 KDC 프록시를 설정 하는 작업은 일반적으로 Windows Server 2016 이상에서 Windows Server 게이트웨이 역할을 할당 하는 것과 관련이 있습니다. 원격 데스크톱 서비스 역할을 사용 하 여 Windows 가상 데스크톱에 로그인 하려면 어떻게 해야 하나요? 이에 대답 하기 위해 구성 요소를 간략히 살펴보겠습니다.

Windows 가상 데스크톱 서비스에 인증 해야 하는 두 가지 구성 요소가 있습니다.

- 사용자가 액세스할 수 있는 데스크톱 또는 응용 프로그램 목록을 사용자에 게 제공 하는 Windows 가상 데스크톱 클라이언트의 피드입니다. 이 인증 프로세스는 Azure Active Directory에서 발생 합니다. 즉,이 구성 요소는이 문서에 중점을 두지 않습니다.
- 사용자가 사용 가능한 리소스 중 하나를 선택 하 여 생성 되는 RDP 세션입니다. 이 구성 요소는 Kerberos 인증을 사용 하며, 원격 사용자에 대 한 KDC 프록시가 필요 합니다.

이 문서에서는 Azure Portal의 Windows 가상 데스크톱 클라이언트에서 피드를 구성 하는 방법을 보여 줍니다. RD 게이트웨이 역할을 구성 하는 방법을 알아보려면 [RD 게이트웨이 역할 배포](/windows-server/remote/rd-gateway-role)를 참조 하세요.

## <a name="requirements"></a>요구 사항

KDC 프록시를 사용 하 여 Windows 가상 데스크톱 세션 호스트를 구성 하려면 다음 항목이 필요 합니다.

- Azure Portal 및 Azure 관리자 계정에 대 한 액세스.
- 원격 클라이언트 컴퓨터는 Windows 10 또는 Windows 7을 실행 하 고 [Windows 데스크톱 클라이언트](/windows-server/remote/remote-desktop-services/clients/windowsdesktop) 를 설치 해야 합니다.
- 컴퓨터에 KDC 프록시가 이미 설치 되어 있어야 합니다. 이 작업을 수행 하는 방법에 [대 한 자세한 내용은 Windows 가상 데스크톱에 대 한 RD 게이트웨이 역할 설정](rd-gateway-role.md)을 참조 하세요.
- 컴퓨터의 OS는 Windows Server 2016 이상 이어야 합니다.

이러한 요구 사항을 충족 하는 것이 확인 되 면 시작할 준비가 된 것입니다.

## <a name="how-to-configure-the-kdc-proxy"></a>KDC 프록시를 구성 하는 방법

KDC 프록시를 구성 하려면:

1. Azure Portal에 관리자 권한으로 로그인합니다.

2. Windows 가상 데스크톱 페이지로 이동 합니다.

3. KDC 프록시를 사용 하도록 설정 하려는 호스트 풀을 선택한 다음 **RDP 속성** 을 선택 합니다.

    > [!div class="mx-imgBorder"]
    > ![호스트 풀을 선택 하 고, 예제 호스트 풀의 이름을 선택한 다음 RDP 속성을 선택 하는 사용자를 보여 주는 Azure Portal 페이지의 스크린샷](media/rdp-properties.png)

4. **고급** 탭을 선택 하 고 공백 없이 다음 형식으로 값을 입력 합니다.

    
    > kdcproxyname: s:\<fqdn\>
    

    > [!div class="mx-imgBorder"]
    > ![4 단계에서 설명한 대로 값을 입력 하 여 선택 된 고급 탭을 보여 주는 스크린샷](media/advanced-tab-selected.png)

5. **저장** 을 선택합니다.

6. 이제 선택한 호스트 풀에서 4 단계에서 입력 한 kdcproxyname 값을 포함 하는 RDP 연결 파일의 실행을 시작 해야 합니다.

## <a name="next-steps"></a>다음 단계

KDC 프록시의 원격 데스크톱 서비스 쪽을 관리 하 고 RD 게이트웨이 역할을 할당 하는 방법을 알아보려면 [RD 게이트웨이 역할 배포](/windows-server/remote/rd-gateway-role)를 참조 하세요.

KDC 프록시 서버 크기를 조정 하는 데 관심이 있는 경우에는 [RD 웹 및 게이트웨이 웹 front에 고가용성 추가](/windows-server/remote/remote-desktop-services/rds-rdweb-gateway-ha)에서 kdc 프록시에 대 한 고가용성을 설정 하는 방법을 알아봅니다.
