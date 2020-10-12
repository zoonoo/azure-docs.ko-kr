---
title: 새 세션 호스트를 사용 하 여 기존 Windows 가상 데스크톱 (클래식) 호스트 풀 확장-Azure
description: Windows 가상 데스크톱 (클래식)에서 새 세션 호스트를 사용 하 여 기존 호스트 풀을 확장 하는 방법입니다.
author: Heidilohr
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 61cf28b0f1ebee6a0312ec3f23f22b01c6c4919e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "88009174"
---
# <a name="expand-an-existing-host-pool-with-new-session-hosts-in-windows-virtual-desktop-classic"></a>Windows 가상 데스크톱 (클래식)의 새 세션 호스트를 사용 하 여 기존 호스트 풀 확장

>[!IMPORTANT]
>이 콘텐츠는 Azure Resource Manager Windows Virtual Desktop 개체를 지원하지 않는 Windows Virtual Desktop(클래식)에 적용됩니다. Azure Resource Manager Windows Virtual Desktop 개체를 관리하려는 경우 [이 문서](../expand-existing-host-pool.md)를 참조하세요.

호스트 풀 내에서 사용량을 증가 시킬 때 새 부하를 처리 하기 위해 새 세션 호스트를 사용 하 여 기존 호스트 풀을 확장 해야 할 수 있습니다.

이 문서에서는 새 세션 호스트를 사용 하 여 기존 호스트 풀을 확장 하는 방법을 설명 합니다.

## <a name="what-you-need-to-expand-the-host-pool"></a>호스트 풀을 확장 하는 데 필요한 항목

시작 하기 전에 다음 방법 중 하나를 사용 하 여 호스트 풀 및 세션 호스트 Vm (가상 머신)을 만들었는지 확인 합니다.

- [Azure Marketplace 제공](create-host-pools-azure-marketplace-2019.md)
- [GitHub Azure Resource Manager 템플릿](create-host-pools-arm-template.md)
- [PowerShell을 사용한 호스트 풀 만들기](create-host-pools-powershell-2019.md)

또한 호스트 풀 및 세션 호스트 Vm을 처음 만들 때에서 다음 정보가 필요 합니다.

- VM 크기, 이미지 및 이름 접두사
- 도메인 가입 및 Windows 가상 데스크톱 테 넌 트 관리자 자격 증명
- 가상 네트워크 이름 및 서브넷 이름

다음 세 개의 섹션에서는 호스트 풀을 확장 하는 데 사용할 수 있는 세 가지 방법을 설명 합니다. 편안 하 게 사용할 수 있는 배포 도구 중 하나를 사용 하 여 수행할 수 있습니다.

>[!NOTE]
>배포 단계 중에 이전 세션 호스트 VM 리소스를 현재 종료 하는 경우 해당 리소스에 대 한 오류 메시지가 표시 됩니다. 이러한 오류는 Azure에서 PowerShell DSC 확장을 실행 하 여 세션 호스트 Vm이 기존 호스트 풀에 올바르게 등록 되었는지 확인할 수 없기 때문에 발생 합니다. 이러한 오류를 안전 하 게 무시 하거나 배포 프로세스를 시작 하기 전에 기존 호스트 풀에서 모든 세션 호스트 Vm을 시작 하 여 오류를 방지할 수 있습니다.

## <a name="redeploy-from-azure"></a>Azure에서 다시 배포

[Azure Marketplace 제공](create-host-pools-azure-marketplace-2019.md) 또는 [GitHub Azure Resource Manager 템플릿을](create-host-pools-arm-template.md)사용 하 여 호스트 풀 및 세션 호스트 vm을 이미 만든 경우 Azure Portal에서 동일한 템플릿을 다시 배포할 수 있습니다. 템플릿을 다시 배포 하면 암호를 제외한 원래 템플릿에 입력 한 모든 정보가 자동으로 후 됩니다.

호스트 풀을 확장 하기 위해 Azure Resource Manager 템플릿을 다시 배포 하는 방법은 다음과 같습니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. Azure Portal 맨 위에 있는 검색 창에서 **리소스 그룹** 을 검색 하 고 **서비스**아래에서 항목을 선택 합니다.
3. 호스트 풀을 만들 때 만든 리소스 그룹을 찾아 선택 합니다.
4. 브라우저 왼쪽의 패널에서 **배포**를 선택 합니다.
5. 호스트 풀 만들기 프로세스에 적절 한 배포를 선택 합니다.
     - Azure Marketplace 제품을 사용 하 여 원래 호스트 풀을 만든 경우에는 해당 배포를 선택 합니다 **.**
     - GitHub Azure Resource Manager 템플릿을 사용 하 여 원래 호스트 풀을 만든 경우에는 **Microsoft. template**이라는 배포를 선택 합니다.
6. **재배포**를 선택 합니다.

     >[!NOTE]
     >다시 **배포**를 선택할 때 템플릿이 자동으로 다시 배포 되지 않으면 브라우저 왼쪽의 패널에서 **템플릿** 을 선택한 다음 **배포**를 선택 합니다.

7. 기존 호스트 풀의 현재 세션 호스트 Vm이 포함 된 리소스 그룹을 선택 합니다.

     >[!NOTE]
     >입력 한 리소스 그룹이 올바른 경우에도 다른 리소스 그룹을 선택 하 라는 오류가 표시 되 면 다른 리소스 그룹을 선택한 다음 원래 리소스 그룹을 선택 합니다.

8. *_ArtifactsLocation*에 다음 URL을 입력 합니다.`https://raw.githubusercontent.com/Azure/RDS-Templates/master/wvd-templates/`
9. 원하는 세션 호스트의 새 총 수를 *Rdsh 인스턴스 수*로 입력 합니다. 예를 들어 5 개의 세션 호스트에서 8로 호스트 풀을 확장 하는 경우 **8**을 입력 합니다.
10. 기존 도메인 UPN에 사용한 것과 동일한 기존 도메인 암호를 입력 합니다. 템플릿을 실행할 때 오류가 발생 하므로 사용자 이름을 변경 하지 마십시오.
11. *테 넌 트 관리 Upn 또는 응용 프로그램 id*에 입력 한 사용자 또는 응용 프로그램 id에 대해 사용한 것과 동일한 테 넌 트 관리자 암호를 입력 합니다. 다시 한 번, 사용자 이름을 변경 하지 않습니다.
12. 등록을 완료 하 여 호스트 풀을 확장 합니다.

## <a name="run-the-azure-marketplace-offering"></a>Azure Marketplace 제공 실행

[새 호스트 풀을 프로 비전 하기 위해 Azure Marketplace 제품 실행](create-host-pools-azure-marketplace-2019.md#run-the-azure-marketplace-offering-to-provision-a-new-host-pool)에 도달할 때까지 [Azure Marketplace를 사용 하 여 호스트 풀 만들기](create-host-pools-azure-marketplace-2019.md) 의 지침을 따릅니다. 해당 시점으로 이동 하면 각 탭에 대해 다음 정보를 입력 해야 합니다.

### <a name="basics"></a>기본 사항

*기본 데스크톱 사용자*를 제외 하 고이 섹션의 모든 값은 호스트 풀 및 세션 호스트 vm을 처음 만들 때 입력 한 값과 일치 해야 합니다.

1.    *구독*에서 호스트 풀을 처음 만든 구독을 선택 합니다.
2.    *리소스 그룹*에 대해 기존 호스트 풀 세션 호스트 vm이 있는 리소스 그룹을 선택 합니다.
3.    *지역*에서 기존 호스트 풀 세션 호스트 vm이 있는 지역을 선택 합니다.
4.    *Hostpool 이름*에 기존 호스트 풀의 이름을 입력 합니다.
5.    *바탕 화면 유형*에서 기존 호스트 풀과 일치 하는 데스크톱 유형을 선택 합니다.
6.    *기본 데스크톱 사용자*의 경우 Windows 가상 데스크톱 클라이언트에 로그인 할 추가 사용자의 쉼표로 구분 된 목록을 입력 하 고 Azure Marketplace 제공이 완료 되 면 데스크톱에 액세스 합니다. 예를 들어를 할당 하 user3@contoso.com 고 액세스 하려면 user4@contoso.com user3@contoso.com 을 입력 user4@contoso.com 합니다.
7.    **다음: 가상 머신 구성**을 선택 합니다.

>[!NOTE]
>*기본 데스크톱 사용자*를 제외 하 고 모든 필드는 기존 호스트 풀에서 구성 된 것과 정확히 일치 해야 합니다. 일치 하는 항목이 없으면 새 호스트 풀이 생성 됩니다.

### <a name="configure-virtual-machines"></a>가상 머신 구성

이 섹션의 모든 매개 변수 값은 Vm의 총 수를 제외 하 고 호스트 풀 및 세션 호스트 Vm을 처음 만들 때 입력 한 값과 일치 해야 합니다. 입력 하는 Vm의 수는 확장 된 호스트 풀의 Vm 수입니다.

1. 기존 세션 호스트 Vm과 일치 하는 VM 크기를 선택 합니다.

    >[!NOTE]
    >찾고 있는 특정 VM 크기가 VM 크기 선택기에 표시되지 않는 경우, 이는 Azure Marketplace 도구에 아직 등록되지 않았기 때문입니다. VM 크기를 요청하려면 [Windows Virtual Desktop UserVoice 포럼](https://windowsvirtualdesktop.uservoice.com/forums/921118-general)에서 요청을 만들거나 기존 요청에 찬성합니다.

2. *사용 프로필*, *총 사용자*및 *Virtual machines 수* 매개 변수 수를 사용자 지정 하 여 호스트 풀에 포함할 총 세션 호스트 수를 선택 합니다. 예를 들어 호스트 풀을 5 개의 세션 호스트에서 8로 확장 하는 경우이 옵션을 구성 하 여 8 개의 가상 컴퓨터를 가져오도록 합니다.
3. 가상 머신의 이름에 사용할 접두사를 입력합니다. 예를 들어, “prefix”라는 이름을 입력하는 경우 가상 머신은 “prefix-0”, “prefix-1” 등으로 불립니다.
4. **다음: 가상 머신 설정**을 선택 합니다.

### <a name="virtual-machine-settings"></a>가상 머신 설정

이 섹션의 모든 매개 변수 값은 호스트 풀 및 세션 호스트 Vm을 처음 만들 때 입력 한 값과 일치 해야 합니다.

1. *이미지 원본* 및 *이미지 OS 버전*의 경우 호스트 풀을 처음 만들 때 제공한 것과 동일한 정보를 입력 합니다.
2. *AD 도메인 가입 UPN* 및 연결 된 암호에 대해 vm을 Active Directory 도메인에 가입 시킬 호스트 풀을 처음 만들 때 제공한 것과 동일한 정보를 입력 합니다. 이러한 자격 증명을 사용 하 여 가상 머신에서 로컬 계정을 만들 수 있습니다. 이러한 로컬 계정을 다시 설정 하 여 나중에 자격 증명을 변경할 수 있습니다.
3. 가상 네트워크 정보에 대해 기존 호스트 풀 세션 호스트 Vm이 있는 위치와 동일한 가상 네트워크 및 서브넷을 선택 합니다.
4. **다음: Windows 가상 데스크톱 정보 구성**을 선택 합니다.

### <a name="windows-virtual-desktop-information"></a>Windows 가상 데스크톱 정보

이 섹션의 모든 매개 변수 값은 호스트 풀 및 세션 호스트 Vm을 처음 만들 때 입력 한 값과 일치 해야 합니다.

1. *Windows Virtual Desktop 테넌트 그룹 이름*의 경우, 테넌트를 포함하는 테넌트 그룹의 이름을 입력합니다. 특정 테넌트 그룹 이름이 제공되지 않은 경우 기본값을 그대로 유지합니다.
2. *Windows Virtual Desktop 테넌트 이름*의 경우, 이 호스트 풀을 만들 테넌트의 이름을 입력합니다.
3. 호스트 풀 및 세션 호스트 Vm을 처음 만들 때 사용한 것과 동일한 자격 증명을 지정 합니다. 서비스 주체를 사용 하는 경우 서비스 사용자가 있는 Azure Active Directory 인스턴스의 ID를 입력 합니다.
4. **다음: 검토 + 만들기**를 선택 합니다.

## <a name="run-the-github-azure-resource-manager-template"></a>GitHub Azure Resource Manager 템플릿 실행

[새 호스트 풀을 프로 비전 하기 위해 Azure Resource Manager 템플릿 실행](create-host-pools-arm-template.md#run-the-azure-resource-manager-template-for-provisioning-a-new-host-pool) 의 지침을 따르고, *인스턴스 수*를 제외 하 고 동일한 매개 변수 값을 모두 제공 합니다. 템플릿을 실행 한 후 호스트 풀에서 원하는 세션 호스트 Vm의 수를 입력 합니다. 예를 들어 5 개의 세션 호스트에서 8로 호스트 풀을 확장 하는 경우 **8**을 입력 합니다.

## <a name="next-steps"></a>다음 단계

기존 호스트 풀을 확장 했으므로 Windows 가상 데스크톱 클라이언트에 로그인 하 여 사용자 세션의 일부로 테스트할 수 있습니다. 다음 클라이언트 중 하나를 사용 하 여 세션에 연결할 수 있습니다.

- [Windows Desktop 클라이언트를 사용하여 연결](connect-windows-7-10-2019.md)
- [웹 클라이언트를 사용하여 연결](connect-web-2019.md)
- [Android 클라이언트와 연결](connect-android-2019.md)
- [macOS 클라이언트와 연결](connect-macos-2019.md)
- [iOS 클라이언트와 연결](connect-ios-2019.md)
