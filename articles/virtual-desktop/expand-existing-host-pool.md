---
title: 새 세션 호스트를 사용 하 여 기존 호스트 풀 확장-Azure
description: Windows 가상 데스크톱에서 새 세션 호스트를 사용 하 여 기존 호스트 풀을 확장 하는 방법입니다.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: be76c665e1f5319b3e1ff1976e44fee9cd90ea6b
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/30/2020
ms.locfileid: "82607201"
---
# <a name="expand-an-existing-host-pool-with-new-session-hosts"></a>새 세션 호스트를 사용 하 여 기존 호스트 풀 확장

>[!IMPORTANT]
>이 콘텐츠는 Azure Resource Manager Windows 가상 데스크톱 개체를 사용 하 여 스프링 2020 업데이트에 적용 됩니다. Azure Resource Manager 개체 없이 Windows 가상 데스크톱 2019 릴리스를 사용 하는 경우 [이 문서](./virtual-desktop-fall-2019/expand-existing-host-pool-2019.md)를 참조 하세요.
>
> Windows 가상 데스크톱 스프링 2020 업데이트는 현재 공개 미리 보기로 제공 됩니다. 이 미리 보기 버전은 서비스 수준 계약 없이 제공 되며 프로덕션 워크 로드에 사용 하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

호스트 풀 내에서 사용량을 증가 시킬 때 새 부하를 처리 하기 위해 새 세션 호스트를 사용 하 여 기존 호스트 풀을 확장 해야 할 수 있습니다.

이 문서에서는 새 세션 호스트를 사용 하 여 기존 호스트 풀을 확장 하는 방법을 설명 합니다.

## <a name="what-you-need-to-expand-the-host-pool"></a>호스트 풀을 확장 하는 데 필요한 항목

시작 하기 전에 다음 방법 중 하나를 사용 하 여 호스트 풀 및 세션 호스트 Vm (가상 머신)을 만들었는지 확인 합니다.

- [Azure Portal](./create-host-pools-azure-marketplace.md)
- [PowerShell을 사용한 호스트 풀 만들기](./create-host-pools-powershell.md)

또한 호스트 풀 및 세션 호스트 Vm을 처음 만들 때에서 다음 정보가 필요 합니다.

- VM 크기, 이미지 및 이름 접두사
- 도메인 가입 관리자 자격 증명
- 가상 네트워크 이름 및 서브넷 이름

## <a name="add-virtual-machines-with-the-azure-portal"></a>Azure Portal를 사용 하 여 가상 컴퓨터 추가

가상 컴퓨터를 추가 하 여 호스트 풀을 확장 하려면:

1. Azure Portal에 로그인합니다.

2. **Windows 가상 데스크톱**을 검색 하 고 선택 합니다.

3. 화면 왼쪽의 메뉴에서 **호스트 풀**을 선택한 다음 가상 컴퓨터를 추가할 호스트 풀의 이름을 선택 합니다.

4. 화면 왼쪽의 메뉴에서 **가상 컴퓨터** 를 선택 합니다.

5. **+ 추가** 를 선택 하 여 호스트 풀 만들기를 시작 합니다.

6. 기본 탭을 무시 하 고 대신 **VM 세부 정보** 탭을 선택 합니다. 여기에서 호스트 풀에 추가 하려는 VM (가상 머신)의 세부 정보를 보고 편집할 수 있습니다.

7. Vm을 만들 리소스 그룹을 선택한 다음 지역을 선택 합니다. 현재 사용 중인 지역이 나 새 지역을 선택할 수 있습니다.
   
8. **Vm 수**에 사용할 세션 호스트의 새 총 수를 입력 합니다. 예를 들어 5 개의 세션 호스트에서 8로 호스트 풀을 확장 하는 경우 **8**을 입력 합니다. 
   
    >[!NOTE]
    >Vm의 크기 또는 이미지를 편집할 수 없습니다. 호스트 풀의 모든 Vm이 동일한 크기 인지 확인 하는 것이 중요 하기 때문입니다.
    
9. 가상 **네트워크 정보**를 보려면 가상 컴퓨터를 가입 시킬 가상 네트워크 및 서브넷을 선택 합니다. 현재 기존 컴퓨터에서 사용 하는 것과 동일한 가상 네트워크를 선택 하거나 7 단계에서 선택한 지역에 더 적합 한 다른 가상 네트워크를 선택할 수 있습니다.

10. **관리자 계정**에는 선택한 가상 네트워크와 연결 된 Active Directory 도메인 사용자 이름 및 암호를 입력 합니다. 이러한 자격 증명은 가상 컴퓨터를 가상 네트워크에 조인 하는 데 사용 됩니다.

      >[!NOTE]
      >관리자 이름이 여기에 지정 된 정보를 준수 하는지 확인 합니다. 그리고 계정에 MFA를 사용 하도록 설정 되어 있지 않습니다.

11. 가상 컴퓨터를 그룹화 할 태그가 있는 경우 **태그** 탭을 선택 합니다. 그렇지 않은 경우이 탭을 건너뜁니다. 

12. **검토 + 만들기** 탭을 선택 합니다. 선택 항목을 검토 하 고, 모든 것이 제대로 표시 되 면 **만들기**를 선택 합니다. 

## <a name="next-steps"></a>다음 단계

기존 호스트 풀을 확장 했으므로 Windows 가상 데스크톱 클라이언트에 로그인 하 여 사용자 세션의 일부로 테스트할 수 있습니다. 다음 클라이언트 중 하나를 사용 하 여 세션에 연결할 수 있습니다.

- [Windows Desktop 클라이언트와 연결](./connect-windows-7-and-10.md)
- [웹 클라이언트를 사용하여 연결](./connect-web.md)
- [Android 클라이언트와 연결](./connect-android.md)
- [macOS 클라이언트와 연결](./connect-macos.md)
- [iOS 클라이언트와 연결](./connect-ios.md)
