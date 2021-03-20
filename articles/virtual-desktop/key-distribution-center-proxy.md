---
title: Kerberos 키 배포 센터 프록시 Windows 가상 데스크톱 설정-Azure
description: Kerberos 키 배포 센터 프록시를 사용 하도록 Windows 가상 데스크톱 호스트 풀을 설정 하는 방법입니다.
author: Heidilohr
ms.topic: how-to
ms.date: 01/30/2021
ms.author: helohr
manager: lizross
ms.openlocfilehash: 102ddc1c8937c66a92416ddb6d5f2d25f2a3c349
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99219658"
---
# <a name="configure-a-kerberos-key-distribution-center-proxy-preview"></a>Kerberos 키 배포 센터 프록시 구성 (미리 보기)

> [!IMPORTANT]
> 이 기능은 현재 공개 미리 보기로 제공됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며, 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

이 문서에서는 호스트 풀에 대해 KDC (Kerberos 키 배포 센터) 프록시 (미리 보기)를 구성 하는 방법을 보여 줍니다. 이 프록시를 통해 조직에서는 엔터프라이즈 경계 외부에서 Kerberos로 인증할 수 있습니다. 예를 들어 KDC 프록시를 사용 하 여 외부 클라이언트에 스마트 카드 인증을 사용 하도록 설정할 수 있습니다.

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

6. 이제 선택한 호스트 풀이 포함 된 kdcproxyname 필드를 사용 하 여 RDP 연결 파일을 발급 하기 시작 합니다.

## <a name="next-steps"></a>다음 단계

원격 데스크톱 서비스의 RDGateway 역할은 KDC 프록시 서비스를 포함 합니다. Windows 가상 데스크톱의 대상으로 설정 하는 방법은 [Windows 가상 데스크톱에서 RD 게이트웨이 역할 배포](rd-gateway-role.md) 를 참조 하세요.
