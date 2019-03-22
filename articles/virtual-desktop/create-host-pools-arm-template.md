---
title: Azure Resource Manager 템플릿 (미리 보기)-Azure 사용 하 여 호스트 풀 만들기
description: Azure Resource Manager 템플릿을 사용 하 여 Windows 가상 데스크톱의 호스트 풀을 만드는 방법입니다.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: 7f4849c19d56bb385e7ad3ce0aa95e16d5c53c23
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/21/2019
ms.locfileid: "58318466"
---
# <a name="create-a-host-pool-with-an-azure-resource-manager-template-preview"></a>(미리 보기) Azure Resource Manager 템플릿을 사용 하 여 호스트 풀 만들기

호스트 풀은 Windows 가상 데스크톱 테 넌 트 (미리 보기) 환경 내에서 하나 이상의 동일한 가상 컴퓨터의 컬렉션입니다. 각 호스트 풀에는 사용자는 실제 데스크톱에서 일관 되 게 상호 작용할 수 있는 앱 그룹을 포함할 수 있습니다.

Microsoft에서 제공 하는 Azure Resource Manager 템플릿을 사용 하 여 Windows 가상 데스크톱 테 넌 트에 대 한 호스트 풀을 만들려면이 섹션의이 지침을 따릅니다. 이 문서에서는 Windows 가상 데스크톱에 호스트 풀을 만듭니다, Azure 구독에서 Vm을 사용 하 여 리소스 그룹 만들기, AD 도메인에 Vm 가입 및 Windows 가상 데스크톱을 사용 하 여 Vm을 등록 하는 방법을 알려줍니다.

## <a name="what-you-need-to-run-the-azure-resource-manager-template"></a>Azure Resource Manager 템플릿을 실행 해야 하는 항목

Azure Resource Manager 템플릿을 실행 하기 전에 다음 작업을 알고 있는지 확인 합니다.

- 여기서 사용 하려는 이미지의 원본은입니다. Azure 갤러리에서 되었거나 사용자 지정은?
- 도메인 가입 자격 증명입니다.
- 가상 데스크톱 Windows 자격 증명입니다.

Azure Resource Manager 템플릿을 사용 하 여 Windows 가상 데스크톱 호스트 풀을 만들 때 Azure 갤러리에서 관리 되는 이미지 또는 관리 되지 않는 이미지에서 가상 컴퓨터를 만들 수 있습니다. VM 이미지를 만드는 방법에 대 한 자세한 내용은 참조 하세요 [Azure에 업로드할 Windows VHD 또는 VHDX 준비](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image) 하 고 [Azure에서 일반화 된 VM의 관리 이미지 만들기](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource)합니다.

## <a name="run-the-azure-resource-manager-template-for-provisioning-a-new-host-pool"></a>새 호스트 풀을 프로 비전을 위한 Azure Resource Manager 템플릿을 실행합니다

를 시작 하려면로 이동 [이 GitHub URL](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/Create%20and%20provision%20WVD%20host%20pool)합니다.

### <a name="deploy-the-template-to-azure"></a>Azure에 템플릿 배포

Enterprise 구독에 배포 하는 경우 아래로 스크롤하여 선택 **Azure에 배포**, 후 이미지 원본에 따라 미리 작성 매개 변수를 건너뜁니다.

클라우드 솔루션 공급자 구독에 배포 하는 경우 Azure에 배포 하려면 다음이 단계를 수행 합니다.

1. 아래로 스크롤하여 마우스 오른쪽 단추로 클릭 **Azure에 배포**을 선택한 후 **복사 링크 위치**합니다.
2. 메모장과 같은 텍스트 편집기를 열고 링크를 붙여 넣습니다.
3. 바로 뒤 "https://portal.azure.com/" 해시 태그 (#) 하기 전에 다음을 입력 하 고는 at 기호 (@) 뒤에 테 넌 트 도메인 이름입니다. 다음은 사용 해야 하는 형식의 예: https://portal.azure.com/@Contoso.onmicrosoft.com#create/합니다.
4. Azure portal에 클라우드 솔루션 공급자 구독 관리자/참가자 권한 가진 사용자로 로그인 합니다.
5. 주소 표시줄에 텍스트 편집기에 복사한 링크를 붙여 넣습니다.

Windows 가상 데스크톱이 표시는 매개 변수에 대 한 시나리오에 대 한 입력 해야 하는 지침을 보려면 [Readme 파일](https://github.com/Azure/RDS-Templates/blob/master/wvd-templates/Create%20and%20provision%20WVD%20host%20pool/README.md)합니다. 추가 정보는 항상 최신 변경 내용으로 업데이트 됩니다.

## <a name="assign-users-to-the-desktop-application-group"></a>데스크톱 응용 프로그램 그룹에 사용자 할당

GitHub의 Azure Resource Manager 템플릿을 완료 된 후 가상 머신에서 전체 세션 데스크톱을 테스트 하려면 먼저 사용자 액세스를 할당 합니다.

먼저 [다운로드 하 여 Windows 가상 데스크톱 PowerShell 모듈을 가져옵니다](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) 아직 없는 경우 PowerShell 세션에서 사용 하도록 합니다.

데스크톱 응용 프로그램 그룹에 사용자를 할당 하려면 PowerShell 창을 열고 Windows 가상 데스크톱 환경에 로그인 하려면이 cmdlet을 실행 합니다.

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

다음으로이 cmdlet 사용 하 여 Azure Resource Manager 템플릿에 지정 된 테 넌 트 그룹에 컨텍스트를 설정:

```powershell
Set-RdsContext -TenantGroupName <Tenant Group name>
```

그런 다음이 cmdlet 사용 하 여 데스크톱 응용 프로그램 그룹에 사용자를 추가 합니다.

```powershell
Add-RdsAppGroupUser <tenantname> <hostpoolname> "Desktop Application Group" -UserPrincipalName <userupn>
```

사용자의 UPN에는 Azure Active Directory에서 사용자의 id는 같아야 합니다. (예를 들어 user1@contoso.com). 여러 사용자를 추가 하려는 경우 각 사용자에 대해이 cmdlet을 실행 해야 합니다.

다음이 단계를 완료 한 후 데스크톱 응용 프로그램 그룹에 추가 된 사용자는 지원 되는 원격 데스크톱 클라이언트를 사용 하 여 Windows 가상 데스크톱에 로그인 하 고 세션 데스크톱에 대 한 리소스를 참조 하세요. 수 있습니다.
