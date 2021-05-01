---
title: Azure Migrate를 사용하여 마이그레이션하기 위해 Windows Server 2003 서버 준비
description: Azure Migrate를 사용하여 마이그레이션하기 위해 Windows Server 2003 서버를 준비하는 방법에 대해 알아봅니다.
author: rahulg1190
ms.author: rahugup
ms.manager: bsiva
ms.topic: how-to
ms.date: 05/27/2020
ms.openlocfilehash: f8d3dea970d07d951467a44661e12000ba413f72
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96753753"
---
# <a name="prepare-windows-server-2003-machines-for-migration"></a>마이그레이션을 위해 Windows Server 2003 컴퓨터 준비

이 문서에서는 Azure로 마이그레이션하기 위해 Windows Server 2003을 실행하는 컴퓨터를 준비하는 방법을 설명합니다. 


> [!NOTE]
> [Windows Server 2003 추가 지원](/troubleshoot/azure/virtual-machines/run-win-server-2003#microsoft-windows-server-2003-end-of-support)은 2015년 7월 14일에 종료되었습니다.  Azure 지원 팀은 Azure에서 Windows Server 2003을 실행하는 것과 관련된 문제를 해결하기 위해 지원을 계속하고 있습니다. 하지만 이 지원은 OS 수준의 문제 해결 또는 패치가 필요하지 않은 문제로 제한됩니다. 최신 버전의 Windows Server를 실행하는 Azure 인스턴스로 애플리케이션을 마이그레이션하여 Azure 클라우드의 유연성과 안정성을 효과적으로 활용하는 것이 좋습니다. 하지만 Windows Server 2003을 Azure로 마이그레이션하도록 선택하면, Windows Server가 VMware 또는 Hyper-V에서 실행되는 VM인 경우 Azure Migrate: 서버 마이그레이션 도구를 사용할 수 있습니다.


- 에이전트 없는 마이그레이션을 사용하여 [Hyper-V VM](tutorial-migrate-hyper-v.md) 및 [VMware VM](tutorial-migrate-vmware.md)을 Azure로 마이그레이션할 수 있습니다.
- 마이그레이션 후 Azure VM에 연결하려면 Hyper-V Integration Services를 Azure VM에 설치해야 합니다. Windows Server 2003 컴퓨터에는 기본적으로 설치되어 있지 않습니다.
- Hyper-V Integration Services를 설치하기 위한 직접 다운로드 링크가 없으므로 다음을 수행해야 합니다.
    - 설치되어 있지 않은 Hyper-V VM의 경우, Hyper-V 역할을 사용하여 Windows Server 2012 R2/Windows Server 2012를 실행하는 컴퓨터에서 Integration Services에 대한 설치 파일을 추출하여 Windows Server 2003 컴퓨터에 설치 프로그램을 복사합니다. Windows Server 2016을 실행하는 컴퓨터에서는 설치 파일을 사용할 수 없습니다.
    - VMware VM의 경우, 마이그레이션 후 Azure VM이 시작될 때 Integration Services를 설치하는 시작 작업을 만듭니다.


## <a name="install-on-hyper-v-vms"></a>Hyper-V VM에 설치

마이그레이션하기 전에 Hyper-V Integration Services가 설치되어 있는지 확인하고 필요한 경우 설치합니다.

1. [이러한 지침](/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services#turn-an-integration-service-on-or-off-using-hyper-v-manager)에 따라 설치할지를 확인합니다.
2. 설치되어 있지 않은 경우 Hyper-V 역할을 사용하여 Windows Server 2012 R2/Windows Server 2012를 실행하는 컴퓨터에 로그인합니다.
3. **C:\Windows\System32\vmguest.iso** 에서 설치 파일로 이동하여 파일을 탑재합니다.
2. Windows Server 2003 컴퓨터에 설치 폴더를 복사하고 Integration Services를 설치합니다.
4. 설치 후에는 Integration Services의 기본 설정을 그대로 둘 수 있습니다. 

## <a name="install-on-vmware-vms"></a>VMware VM에 설치

1. Hyper-V 역할을 사용하여 Windows Server 2012 R2/Windows Server 2012를 실행하는 컴퓨터에 로그인합니다.
2. **C:\Windows\System32\vmguest.iso** 에서 설치 파일로 이동하여 파일을 탑재합니다.
3. VMware VM에 설치 폴더를 복사합니다.
4. VM의 명령줄에서 ```gpedit.msc```를 실행합니다.
5. **컴퓨터 구성** > **Windows 설정** > **스크립트(시작/종료)** 를 엽니다.
6. **시작** > **추가** > **스크립트 이름** 에 setup.exe 주소를 입력합니다.
7. Azure로 마이그레이션한 후 Azure VM이 처음 시작될 때 스크립트를 실행합니다.
8. Azure VM을 수동으로 다시 시작합니다. 부팅 진단에 다시 시작해야 한다는 팝업이 나타납니다.
9. 스크립트가 실행되고 Hyper-V Integration Services가 Azure VM에 설치된 후 시작에서 스크립트를 제거할 수 있습니다.
10. 설치 후에는 Integration Services의 기본 설정을 그대로 둘 수 있습니다. 

## <a name="next-steps"></a>다음 단계

- [VMware](migrate-support-matrix-vmware-migration.md) 및 [Hyper-V](migrate-support-matrix-hyper-v-migration.md) VM에 대한 마이그레이션 요구 사항을 검토합니다.
- [VMware](server-migrate-overview.md) 및 [Hyper-V](tutorial-migrate-hyper-v.md) VM을 마이그레이션합니다.
