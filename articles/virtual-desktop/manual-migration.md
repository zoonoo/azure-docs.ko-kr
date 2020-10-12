---
title: Windows 가상 데스크톱에서 수동으로 마이그레이션 (클래식)-Azure
description: Windows 가상 데스크톱 (클래식)에서 Windows 가상 데스크톱으로 수동으로 마이그레이션하는 방법
author: Heidilohr
ms.topic: how-to
ms.date: 09/11/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 74527f57340f850b60dd00dcd054992c423a49c7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90039046"
---
# <a name="migrate-manually-from-windows-virtual-desktop-classic"></a>Windows 가상 데스크톱에서 수동으로 마이그레이션 (클래식)

Windows 가상 데스크톱 (클래식)은 PowerShell cmdlet, REST Api 및 서비스 개체를 사용 하 여 해당 서비스 환경을 만듭니다. Windows 가상 데스크톱 서비스 환경의 "개체"는 Windows 가상 데스크톱에서 만드는 작업입니다. 서비스 개체에는 테 넌 트, 호스트 풀, 응용 프로그램 그룹 및 세션 호스트가 포함 됩니다.

그러나 Windows 가상 데스크톱 (클래식)은 Azure와 통합 되지 않습니다. Azure가 통합 되지 않은 경우 사용자가 만드는 모든 개체는 Azure 구독에 연결 되어 있지 않기 때문에 Azure Portal에서 자동으로 관리 되지 않습니다.

Windows 가상 데스크톱의 최신 주요 업데이트는 서비스의 변화를 전체 Azure 통합으로 표시 합니다. Windows 가상 데스크톱에서 만든 개체는 Azure Portal에 의해 자동으로 관리 됩니다.

이 문서에서는 최신 버전의 Windows 가상 데스크톱으로 마이그레이션하는 것을 고려해 야 하는 이유를 설명 합니다. 그런 다음 Windows 가상 데스크톱 (클래식)에서 Windows 가상 데스크톱의 최신 업데이트로 수동으로 마이그레이션하는 방법을 설명 합니다.

## <a name="why-migrate"></a>마이그레이션해야 하는 이유는 무엇인가요?

특히 수동으로 수행 해야 하는 주요 업데이트는 불편할 수 있습니다. 그러나 자동으로 마이그레이션할 수 없는 몇 가지 이유는 다음과 같습니다.

- 클래식 릴리스를 사용 하 여 만든 기존 서비스 개체는 Azure에 표시 되지 않습니다. 해당 범위는 Windows 가상 데스크톱 서비스 이상으로 확장 되지 않습니다.
- 최신 업데이트를 사용 하 여 서비스의 응용 프로그램 ID를 변경 하 여 Windows 가상 데스크톱 (클래식)에 대해 수행한 방식으로 앱에 대 한 동의를 제거 합니다. 새 응용 프로그램 ID로 인증 되지 않은 경우 Windows 가상 데스크톱을 사용 하 여 새 Azure 개체를 만들 수 없습니다.

문제가 있더라도 클래식 버전에서 마이그레이션하는 것은 여전히 중요 합니다. 마이그레이션한 후에 수행할 수 있는 작업은 다음과 같습니다.

- Azure Portal를 통해 Windows 가상 데스크톱을 관리 합니다.
- 응용 프로그램 그룹에 AD (Azure Active Directory) 사용자 그룹을 할당 합니다.
- 향상 된 Log Analytics 기능을 사용 하 여 배포 문제를 해결 합니다.
- Azure 네이티브 Role-Based 액세스 제어를 사용 하 여 관리 액세스를 관리 합니다.

## <a name="when-should-i-migrate"></a>언제 마이그레이션해야 하나요?

사용자에 게 마이그레이션해야 하는지 여부를 확인 하는 경우 배포의 현재 및 향후 상황을 고려해 야 합니다.

특히 수동으로 마이그레이션하는 것이 좋습니다.

- 적은 수의 사용자로 테스트 호스트 풀을 설정 했습니다.
- 적은 수의 사용자를 포함 하는 프로덕션 호스트 풀을 설치 하는 경우 최종적으로는 수백 명의 사용자가 될 계획입니다.
- 쉽게 복제할 수 있는 간단한 설정이 있습니다. 예를 들어 Vm에서 갤러리 이미지를 사용 하는 경우입니다.

> [!IMPORTANT]
> 안정화 하는 데 오랜 시간이 걸리거나 많은 사용자가 있는 고급 구성을 사용 하는 경우 수동으로 마이그레이션하지 않는 것이 좋습니다.

## <a name="prepare-for-migration"></a>마이그레이션 준비

시작 하기 전에 환경을 마이그레이션할 준비가 되었는지 확인 해야 합니다.

마이그레이션 프로세스를 시작 하는 데 필요한 사항은 다음과 같습니다.

- Azure 구독-새 Azure 서비스 개체를 만듭니다.
- 다음 역할에 할당 되었는지 확인 합니다.
    
    - 참가자
    - 사용자 액세스 관리자
    
    참가자 역할을 통해 구독에서 Azure 개체를 만들 수 있으며 사용자 액세스 관리자 역할을 통해 응용 프로그램 그룹에 사용자를 할당할 수 있습니다.

## <a name="how-to-migrate-manually"></a>수동으로 마이그레이션하는 방법

마이그레이션 프로세스를 준비한 후에는 실제로 마이그레이션해야 합니다.

Windows 가상 데스크톱 (클래식)에서 Windows 가상 데스크톱으로 수동으로 마이그레이션하려면 다음을 수행 합니다.

1. Azure Portal를 사용 하 [여 호스트 풀 만들기](create-host-pools-azure-marketplace.md) 의 지침에 따라 Azure Portal를 사용 하 여 모든 상위 수준 개체를 만듭니다.
2. 이미 사용 하 고 있는 가상 컴퓨터를 가져오려면 [Windows 가상 데스크톱 호스트 풀에 가상 컴퓨터 등록](create-host-pools-powershell.md#register-the-virtual-machines-to-the-windows-virtual-desktop-host-pool) 의 지침에 따라 가상 컴퓨터를 1 단계에서 만든 새 호스트 풀에 수동으로 등록 합니다.
3. 새 RemoteApp 앱 그룹을 만듭니다.
4. 사용자 또는 사용자 그룹을 새 데스크톱 및 RemoteApp 앱 그룹에 게시 합니다.
5. [Multi-factor Authentication 설정](set-up-mfa.md)의 지침에 따라 새 개체를 허용 하도록 조건부 액세스 정책을 업데이트 합니다.

가동 중지 시간을 방지 하려면 먼저 기존 세션 호스트를 작은 그룹의 Azure Resource Manager 통합 호스트 풀에 한 번에 등록 해야 합니다. 그런 다음 사용자를 천천히 새 Azure Resource Manager 통합 앱 그룹으로 전환 합니다.

## <a name="next-steps"></a>다음 단계

마이그레이션한 후에는 [자습서](create-host-pools-azure-marketplace.md)를 확인 하 여 Windows 가상 데스크톱이 어떻게 작동 하는지 알아봅니다. [기존 호스트 풀 확장](expand-existing-host-pool.md) 및 [RDP 속성 사용자 지정](customize-rdp-properties.md)에서 고급 관리 기능에 대해 알아봅니다.

서비스 개체에 대해 자세히 알아보려면 [Windows 가상 데스크톱 환경](environment-setup.md)을 확인 하세요.
