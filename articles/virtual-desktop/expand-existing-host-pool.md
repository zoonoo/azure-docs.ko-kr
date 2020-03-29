---
title: 새 세션 호스트를 사용 하 여 기존 호스트 풀 확장 - Azure
description: Windows 가상 데스크톱에서 새 세션 호스트를 사용하여 기존 호스트 풀을 확장하는 방법
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 02/21/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: aee5195fe86fed3e631908a38d3bdb7d5e4883b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79365222"
---
# <a name="expand-an-existing-host-pool-with-new-session-hosts"></a>새 세션 호스트를 사용 하 여 기존 호스트 풀 확장

호스트 풀 내에서 사용량을 늘리면 새 세션 호스트를 사용하여 기존 호스트 풀을 확장하여 새 로드를 처리해야 할 수 있습니다.

이 문서에서는 새 세션 호스트를 사용 하 여 기존 호스트 풀을 확장 하는 방법을 설명 합니다.

## <a name="what-you-need-to-expand-the-host-pool"></a>호스트 풀을 확장하는 데 필요한 사항

시작하기 전에 다음 방법 중 하나를 사용하여 호스트 풀 및 세션 호스트 가상 시스템(VM)을 만드나요?

- [Azure 마켓플레이스 오퍼링](./create-host-pools-azure-marketplace.md)
- [GitHub Azure 리소스 관리자 템플릿](./create-host-pools-arm-template.md)
- [PowerShell을 사용한 호스트 풀 만들기](./create-host-pools-powershell.md)

또한 호스트 풀 및 세션 호스트 VM을 처음 만들 때의 다음 정보도 필요합니다.

- VM 크기, 이미지 및 이름 접두사
- 도메인 조인 및 Windows 가상 데스크톱 테넌트 관리자 자격 증명
- 가상 네트워크 이름 및 서브넷 이름

다음 세 섹션은 호스트 풀을 확장하는 데 사용할 수 있는 세 가지 방법입니다. 익숙한 배포 도구를 사용하여 수행할 수 있습니다.

>[!NOTE]
>배포 단계에서 현재 종료된 경우 이전 세션 호스트 VM 리소스에 대한 오류 메시지가 표시됩니다. 이러한 오류는 Azure에서 PowerShell DSC 확장을 실행하여 세션 호스트 VM이 기존 호스트 풀에 올바르게 등록되어 있는지 확인할 수 없기 때문에 발생합니다. 이러한 오류를 무시하거나 배포 프로세스를 시작하기 전에 기존 호스트 풀의 모든 세션 호스트 VM을 시작하여 오류를 방지할 수 있습니다.

## <a name="redeploy-from-azure"></a>Azure에서 재배포

[Azure Marketplace 오퍼링](./create-host-pools-azure-marketplace.md) 또는 [GitHub Azure 리소스 관리자 템플릿을](./create-host-pools-arm-template.md)사용하여 호스트 풀 및 세션 호스트 VM을 이미 만든 경우 Azure 포털에서 동일한 템플릿을 다시 배포할 수 있습니다. 템플릿을 다시 배포하면 암호를 제외한 원래 템플릿에 입력한 모든 정보가 자동으로 다시 입력됩니다.

호스트 풀을 확장하기 위해 Azure 리소스 관리자 템플릿을 다시 배포하는 방법은 다음과 같습니다.

1. [Azure 포털에](https://portal.azure.com/)로그인합니다.
2. Azure 포털 상단의 검색 창에서 리소스 **그룹을** 검색하고 **서비스**에서 항목을 선택합니다.
3. 호스트 풀을 만들 때 만든 리소스 그룹을 찾아 선택합니다.
4. 브라우저 왼쪽패널에서 **배포를 선택합니다.**
5. 호스트 풀 생성 프로세스에 적합한 배포를 선택합니다.
     - Azure Marketplace 오퍼링을 사용 하 여 원래 호스트 풀을 만든 경우 **rds.wvd 프로비저닝-호스트 풀로**시작 하는 배포를 선택 합니다.
     - GitHub Azure 리소스 관리자 템플릿을 사용 하 여 원래 호스트 풀을 만든 경우 **Microsoft.Template**라는 배포를 선택 합니다.
6. **재배포를 선택합니다.**
     
     >[!NOTE]
     >**재배포를**선택할 때 템플릿이 자동으로 다시 배포되지 않으면 브라우저 왼쪽의 패널에서 **템플릿을** 선택한 다음 **배포를**선택합니다.

7. 기존 호스트 풀의 현재 세션 호스트 VM이 포함된 리소스 그룹을 선택합니다.
     
     >[!NOTE]
     >입력한 리소스 그룹이 올바르더라도 다른 리소스 그룹을 선택하라는 오류가 표시되면 다른 리소스 그룹을 선택한 다음 원래 리소스 그룹을 선택합니다.

8. *_artifactsLocation*다음 URL을 입력합니다.`https://raw.githubusercontent.com/Azure/RDS-Templates/master/wvd-templates/`
9. 원하는 세션 호스트의 새 총 수를 *Rdsh 인스턴스 수에*입력합니다. 예를 들어 호스트 풀을 5개의 세션 호스트에서 8로 확장하는 경우 **8을**입력합니다.
10. 기존 도메인 UPN에 사용한 것과 동일한 기존 도메인 암호를 입력합니다. 템플릿을 실행할 때 오류가 발생하므로 사용자 이름을 변경하지 마십시오.
11. 테넌트 관리자 Upn 또는 응용 프로그램 ID에 대해 입력한 사용자 또는 응용 프로그램 ID에 사용한 것과 동일한 *테넌트 관리자 암호를*입력합니다. 다시 한 번 사용자 이름을 변경하지 마십시오.
12. 제출을 완료하여 호스트 풀을 확장합니다.

## <a name="run-the-azure-marketplace-offering"></a>Azure 마켓플레이스 오퍼링 실행

Azure 마켓플레이스 실행에 도달할 때까지 새 호스트 풀을 프로비전할 때까지 [Azure 마켓플레이스를 사용하여 호스트 풀 만들기의](./create-host-pools-azure-marketplace.md) 지침을 [따릅니다.](./create-host-pools-azure-marketplace.md#run-the-azure-marketplace-offering-to-provision-a-new-host-pool) 이 시점에 도달하면 각 탭에 대해 다음 정보를 입력해야 합니다.

### <a name="basics"></a>기본 사항

이 섹션의 모든 값은 *기본 데스크톱 사용자를*제외한 호스트 풀 및 세션 호스트 VM을 처음 만들 때 제공한 값과 일치해야 합니다.

1.    *구독의*경우 호스트 풀을 처음 만든 구독을 선택합니다.
2.    *리소스 그룹의*경우 기존 호스트 풀 세션 호스트 VM이 있는 동일한 리소스 그룹을 선택합니다.
3.    *Region의*경우 기존 호스트 풀 세션 호스트 VM이 있는 동일한 지역을 선택합니다.
4.    *Hostpool 이름의*경우 기존 호스트 풀의 이름을 입력합니다.
5.    *데스크톱 유형의*경우 기존 호스트 풀과 일치하는 데스크톱 유형을 선택합니다.
6.    *기본 데스크톱 사용자의*경우 Windows 가상 데스크톱 클라이언트에 로그인하고 Azure Marketplace 오퍼링이 완료된 후 데스크톱에 액세스하려는 추가 사용자의 쉼표 구분 목록을 입력합니다. 예를 들어 에 대한 할당 user3@contoso.com user4@contoso.com 및 액세스를 user3@contoso.comuser4@contoso.com지정하려면 을 입력합니다.
7.    **다음 선택 : 가상 컴퓨터를 구성합니다.**

>[!NOTE]
>기본 *데스크톱 사용자를*제외한 모든 필드는 기존 호스트 풀에서 구성된 것과 정확히 일치해야 합니다. 불일치가 있는 경우 새 호스트 풀이 생성됩니다.

### <a name="configure-virtual-machines"></a>가상 머신 구성

이 섹션의 모든 매개 변수 값은 총 VM 수를 제외하고 호스트 풀 및 세션 호스트 VM을 처음 만들 때 제공한 값과 일치해야 합니다. 입력하는 VM 수는 확장된 호스트 풀의 VM 수입니다.

1. 기존 세션 호스트 VM과 일치하는 VM 크기를 선택합니다.
    
    >[!NOTE]
    >찾고 있는 특정 VM 크기가 VM 크기 선택기에 표시되지 않는 경우, 이는 Azure Marketplace 도구에 아직 등록되지 않았기 때문입니다. VM 크기를 요청하려면 [Windows Virtual Desktop UserVoice 포럼](https://windowsvirtualdesktop.uservoice.com/forums/921118-general)에서 요청을 만들거나 기존 요청에 찬성합니다.

2. *사용 프로필,* *총 사용자*및 가상 시스템 매개 변수 *수를* 사용자 지정하여 호스트 풀에 사용할 총 세션 호스트 수를 선택합니다. 예를 들어 호스트 풀을 5개의 세션 호스트에서 8개로 확장하는 경우 이러한 옵션을 구성하여 8개의 가상 시스템에 도착하도록 구성합니다.
3. 가상 머신의 이름에 사용할 접두사를 입력합니다. 예를 들어, “prefix”라는 이름을 입력하는 경우 가상 머신은 “prefix-0”, “prefix-1” 등으로 불립니다.
4. **다음 : 가상 컴퓨터 설정을**선택합니다.

### <a name="virtual-machine-settings"></a>가상 머신 설정

이 섹션의 모든 매개 변수 값은 호스트 풀 및 세션 호스트 VM을 처음 만들 때 제공한 값과 일치해야 합니다.

1. *이미지 소스* 및 이미지 *OS 버전의*경우 호스트 풀을 처음 만들 때 제공한 것과 동일한 정보를 입력합니다.
2. *AD 도메인이 UPN* 및 연결된 암호를 조인하는 경우 VM을 Active Directory 도메인에 조인하기 위해 호스트 풀을 처음 만들 때 제공한 것과 동일한 정보를 입력합니다. 이러한 자격 증명은 가상 컴퓨터에서 로컬 계정을 만드는 데 사용됩니다. 이러한 로컬 계정을 재설정하여 나중에 자격 증명을 변경할 수 있습니다.
3. 가상 네트워크 정보의 경우 기존 호스트 풀 세션 호스트 VM이 있는 위치에 대해 동일한 가상 네트워크 및 서브넷을 선택합니다.
4. **다음 선택 : Windows 가상 데스크톱 정보 구성**.

### <a name="windows-virtual-desktop-information"></a>윈도우 가상 데스크톱 정보

이 섹션의 모든 매개 변수 값은 호스트 풀 및 세션 호스트 VM을 처음 만들 때 제공한 값과 일치해야 합니다.

1. *Windows Virtual Desktop 테넌트 그룹 이름*의 경우, 테넌트를 포함하는 테넌트 그룹의 이름을 입력합니다. 특정 테넌트 그룹 이름이 제공되지 않은 경우 기본값을 그대로 유지합니다.
2. *Windows Virtual Desktop 테넌트 이름*의 경우, 이 호스트 풀을 만들 테넌트의 이름을 입력합니다.
3. 호스트 풀 및 세션 호스트 VM을 처음 만들 때 사용한 것과 동일한 자격 증명을 지정합니다. 서비스 주체를 사용하는 경우 서비스 주체가 있는 Azure Active Directory 인스턴스의 ID를 입력합니다.
4. 다음 을 선택 **: 검토 + 만들기**.

## <a name="run-the-github-azure-resource-manager-template"></a>GitHub Azure 리소스 관리자 템플릿 실행

새 호스트 [풀을 프로비전하기 위한 Azure 리소스 관리자 실행 템플릿의](./create-host-pools-arm-template.md#run-the-azure-resource-manager-template-for-provisioning-a-new-host-pool) 지침에 따라 *Rdsh 인스턴스 수를*제외한 모든 동일한 매개 변수 값을 제공합니다. 템플릿을 실행한 후 호스트 풀에서 원하는 세션 호스트 VM 수를 입력합니다. 예를 들어 호스트 풀을 5개의 세션 호스트에서 8로 확장하는 경우 **8을**입력합니다.

## <a name="next-steps"></a>다음 단계

기존 호스트 풀을 확장한 후 Windows 가상 데스크톱 클라이언트에 로그인하여 사용자 세션의 일부로 테스트할 수 있습니다. 다음 클라이언트중 한 가지와 함께 세션에 연결할 수 있습니다.

- [Windows Desktop 클라이언트와 연결](./connect-windows-7-and-10.md)
- [웹 클라이언트를 사용하여 연결](./connect-web.md)
- [Android 클라이언트와 연결](./connect-android.md)
- [macOS 클라이언트와 연결](./connect-macos.md)
- [iOS 클라이언트와 연결](./connect-ios.md)
