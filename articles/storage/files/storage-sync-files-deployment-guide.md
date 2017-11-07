---
title: "Azure 파일 동기화를 배포하는 방법(미리 보기) | Microsoft Docs"
description: "Azure 파일 동기화를 배포하는 방법을 처음부터 끝까지 알아봅니다."
services: storage
documentationcenter: 
author: wmgries
manager: klaasl
editor: jgerend
ms.assetid: 297f3a14-6b3a-48b0-9da4-db5907827fb5
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/08/2017
ms.author: wgries
ms.openlocfilehash: b31b6ae413f72c626e2601ba860aad44ddaa29cd
ms.sourcegitcommit: 76a3cbac40337ce88f41f9c21a388e21bbd9c13f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/25/2017
---
# <a name="how-to-deploy-azure-file-sync-preview"></a>Azure 파일 동기화를 배포하는 방법(미리 보기)
Azure File Sync(미리 보기)를 사용하여 온-프레미스 파일 서버의 유연성, 성능 및 호환성을 희생하지 않고 Azure 파일에서 조직의 파일 공유를 중앙 집중화할 수 있습니다. 이 작업은 Windows Server를 Azure 파일 공유의 빠른 캐시로 변환하여 수행합니다. Windows Server에서 사용할 수 있는 아무 프로토콜이나 사용하여 데이터를 로컬로(SMB, NFS 및 FTPS 포함) 액세스할 수 있으며 세계 전역에 걸쳐 필요한 만큼 캐시를 보유할 수 있습니다.

이 가이드의 단계를 수행하기 전에 [Azure Files 배포에 대한 계획](storage-files-planning.md) 및 [Azure 파일 동기화 배포에 대한 계획](storage-sync-files-planning.md)을 읽어보는 것이 좋습니다.

## <a name="prerequisites"></a>필수 조건
* Azure 파일 동기화를 배포하려는 동일한 지역에 저장소 계정 및 Azure 파일 공유가 있어야 합니다. 자세한 내용은 다음을 참조하세요.
    - Azure 파일 동기화의 [지역 가용성](storage-sync-files-planning.md#region-availability)
    - 저장소 계정을 만드는 방법에 대한 단계별 지침을 제공하는 [저장소 계정 만들기](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)
    - 파일 공유를 만드는 방법에 대한 단계별 지침을 제공하는 [파일 공유 만들기](storage-how-to-create-file-share.md)
* Azure 파일 동기화와 동기화할 하나 이상의 지원되는 Windows Server 또는 Windows Server 클러스터. 지원되는 Windows Server 버전에 대한 자세한 내용은 [Windows Server와의 상호 운용성](storage-sync-files-planning.md#azure-file-sync-interoperability)을 참조하세요.

## <a name="deploy-the-storage-sync-service"></a>저장소 동기화 서비스 배포 
저장소 동기화 서비스는 Azure 파일 동기화를 나타내는 최상위 Azure 리소스입니다. 저장소 동기화 서비스를 배포하려면 [Azure Portal](https://portal.azure.com/)로 이동한 후 Azure 파일 동기화를 검색합니다. 검색 결과에서 "Azure 파일 동기화(미리 보기)"를 선택한 후 "만들기"를 선택하여 "저장소 동기화 배포" 탭을 엽니다.

결과 블레이드는 다음 정보를 요구합니다.

- **이름**: 저장소 동기화 서비스의 고유 이름(구독별)입니다.
- **구독**: 저장소 동기화 서비스를 만들 구독입니다. 조직의 구성 전략에 따라 둘 이상의 구독에 액세스할 수 있습니다. Azure 구독은 각 클라우드 서비스(예: Azure Files)에 대한 비용을 청구하기 위한 가장 기본적인 컨테이너입니다.
- **리소스 그룹**: 리소스 그룹은 저장소 계정 또는 저장소 동기화 서비스와 같은 Azure 리소스의 논리적 그룹입니다. Azure 파일 동기화에 대해 새 리소스 그룹을 만들거나 기존 리소스 그룹을 사용할 수 있습니다(HR 리소스나 특정 프로젝트에 리소스를 그룹화하는 등, 리소스 그룹을 조직의 리소스를 로컬로 격리하는 데 사용되는 컨테이너로 사용하는 것이 권장됨).
- **위치**: Azure 파일 동기화를 배포하려는 지역입니다. 이 목록에는 지원되는 지역만 표시됩니다.

"저장소 동기화 배포" 양식이 완료되면 "만들기"를 클릭하여 저장소 동기화 서비스를 배포합니다.

## <a name="prepare-windows-servers-for-use-with-azure-file-sync"></a>Azure 파일 동기화와 함께 사용하도록 Windows Server 준비
장애 조치(Failover) 클러스터의 서버 노드를 포함하여 Azure 파일 동기화를 사용하려는 서버에 대해 다음 단계를 완료합니다.

장애 조치(Failover) 클러스터의 서버 노드를 비롯하여 Azure 파일 동기화를 사용하려는 모든 서버에 대해 다음 단계를 완료합니다.

1. Internet Explorer 보안 강화 구성을 사용하지 않도록 설정합니다. 이 작업은 초기 서버 등록을 위해서만 필요하며, 서버가 등록된 후에 다시 사용하도록 설정할 수 있습니다.
    1. [서버 관리자]를 엽니다.
    2. **로컬 서버**를 클릭합니다.  
        !["로컬 서버"는 서버 관리자 UI의 왼쪽에 있음](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-1.PNG)
    3. 속성 하위 창에서 **IE 보안 강화 구성** 링크를 선택합니다.  
        ![서버 관리자 UI의 “IE 보안 강화 구성"](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-2.PNG)
    4. Internet Explorer 보안 강화 구성 팝업 창에서 관리자 및 사용자 둘 다에 대해 **해제**를 선택합니다.  
        ![“해제"가 선택된 Internet Explorer 보안 강화 구성 팝업 창](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-3.png)

2. PowerShell 5.1을 실행하고 있는지 확인합니다.\* (PowerShell 5.1은 Windows Server 2016에서 기본값입니다.) $PSVersionTable 개체의 PSVersion 속성 값을 확인하여 PowerShell 5.1.\*을 실행하고 있는지 확인할 수 있습니다.

    ```PowerShell
    $PSVersionTable.PSVersion
    ```

    - 대부분의 Windows Server 2012 R2 설치의 경우처럼 PSVersion이 5.1.\*보다 낮으면 [WMF(Windows Management Framework) 5.1](https://www.microsoft.com/download/details.aspx?id=54616)을 다운로드한 후 설치하여 쉽게 업그레이드할 수 있습니다. Windows Server 2012 R2에 대해 다운로드하고 설치할 적절한 패키지는 **Win8.1AndW2K12R2-KB\*\*\*\*\*\*\*-x64.msu**입니다.

3. [Azure PowerShell 설치 및 구성](https://docs.microsoft.com/powershell/azure/install-azurerm-ps)합니다. 항상 최신 버전의 Azure PowerShell 모듈을 설치하는 것이 좋습니다.

## <a name="install-the-azure-file-sync-agent"></a>Azure 파일 동기화 에이전트 설치
Azure 파일 동기화 에이전트는 Windows Server가 Azure 파일 공유와 동기화되도록 하는 다운로드 가능 패키지입니다. 이 에이전트는 [Microsoft 다운로드 센터](https://go.microsoft.com/fwlink/?linkid=858257)에서 다운로드할 수 있습니다. 다운로드한 MSI 패키지를 두 번 클릭하여 Azure 파일 동기화 에이전트 설치를 시작합니다.

> [!Important]  
> 장애 조치(Failover) 클러스터에서 Azure 파일 동기화를 사용하려는 경우 Azure 파일 동기화 에이전트를 클러스터의 모든 노드에 설치해야 합니다.

Azure 파일 동기화 에이전트 설치 패키지는 너무 많은 추가 프롬프트 없이 비교적 빠르게 설치되어야 합니다. 다음 작업이 권장됩니다.
- 기본 설치 경로 `C:\Program Files\Azure\StorageSyncAgent`를 그대로 유지하여 문제 해결 및 서버 유지 관리를 간소화합니다.
- Azure 파일 동기화를 최신 상태로 유지하기 위해 Microsoft 업데이트를 사용하도록 설정합니다. 기능 업데이트 및 핫픽스를 포함하여 Azure 파일 동기화 에이전트에 대한 모든 업데이트는 Microsoft 업데이트에서 수행됩니다. Azure 파일 동기화에 대해 최신 업데이트를 설치하는 것이 좋습니다. 자세한 내용은 [Azure 파일 동기화 업데이트 정책](storage-sync-files-planning.md#azure-file-sync-agent-update-policy)을 참조하세요.

Azure 파일 동기화 에이전트 설치의 최종 단계에서 서버 등록 UI가 자동으로 시작됩니다. Azure 파일 동기화에 이 서버를 등록하는 방법에 대해서는 다음 섹션을 참조하세요.

## <a name="register-windows-server-with-storage-sync-service"></a>저장소 동기화 서비스에 Windows Server 등록
저장소 동기화 서비스에 Windows Server를 등록하면 서버(또는 클러스터)와 저장소 동기화 서비스 간에 신뢰 관계가 설정됩니다. 서버 등록 UI는 Azure 파일 동기화 에이전트가 설치된 후에 자동으로 시작되지만 그렇지 않은 경우 `C:\Program Files\Azure\StorageSyncAgent\ServerRegistration.exe` 위치에서 수동으로 열 수 있습니다. 서버 등록 UI가 열리면 **로그인**을 클릭하여 시작합니다.

로그인한 후에 다음 정보를 묻는 메시지가 나타납니다.

![서버 등록 UI 스크린샷](media/storage-sync-files-deployment-guide/register-server-scubed-1.png)

- **Azure 구독**: 저장소 동기화 서비스가 포함된 구독입니다(위의 [저장소 동기화 서비스 배포](#deploy-the-storage-sync-service) 참조). 
- **리소스 그룹**: 저장소 동기화 서비스를 포함하는 리소스 그룹입니다.
- **저장소 동기화 서비스**: 등록하려는 저장소 동기화 서비스의 이름입니다.

드롭다운 목록에서 적절한 정보를 선택한 경우 **등록**을 클릭하여 서버 등록을 완료합니다. 등록 프로세스의 일부로 추가 로그인을 요구하는 메시지가 표시됩니다.

## <a name="create-a-sync-group"></a>동기화 그룹 만들기
동기화 그룹은 파일 집합에 대한 동기화 토폴로지를 정의합니다. 동기화 그룹 내의 끝점은 서로 동기화된 상태를 유지합니다. 동기화 그룹은 Azure 파일 공유를 나타내는 하나 이상의 클라우드 끝점과 Windows Server의 경로를 나타내는 하나의 서버 끝점을 포함해야 합니다. 동기화 그룹을 만들려면 [Azure Portal](https://portal.azure.com/)에서 저장소 동기화 서비스로 이동한 후 **+동기화 그룹**을 클릭합니다.

![Azure Portal에서 새 동기화 그룹 만들기](media/storage-sync-files-deployment-guide/create-sync-group-1.png)

결과 창에서는 클라우드 끝점을 사용하여 동기화 그룹을 만들기 위해 다음 정보를 요구합니다.

- **동기화 그룹 이름**: 만들 동기화 그룹의 이름입니다. 이 이름은 저장소 동기화 서비스 내에서 고유해야 하지만 사용자에게 논리적인 어떤 이름도 될 수 있습니다.
- **구독**: 위의 [저장소 동기화 서비스 배포](#deploy-the-storage-sync-service)에서 저장소 동기화 서비스를 배포한 구독입니다.
- **저장소 계정**: "저장소 계정”을 클릭하면 동기화하려는 Azure 파일 공유가 포함된 저장소 계정을 선택할 수 있는 추가 창이 팝업됩니다.
- **Azure 파일 공유**: 동기화하려는 Azure 파일 공유의 이름입니다.

서버 끝점을 추가하려면 새로 만든 동기화 그룹으로 이동한 후 "서버 끝점 추가"를 클릭합니다.

![동기화 그룹 창에서 새 서버 끝점 추가](media/storage-sync-files-deployment-guide/create-sync-group-2.png)

서버 끝점을 만들려면 결과 "서버 끝점 추가" 창에 다음 정보를 지정해야 합니다.

- **등록된 서버**: 서버 끝점을 만들 서버 또는 클러스터의 이름입니다.
- **경로**: 동기화 그룹의 일부분으로 동기화할 Windows Server의 경로입니다.
- **클라우드 계층화**: 덜 자주 사용하거나 액세스하는 파일을 Azure Files로 계층화되도록 하는 클라우드 계층화를 사용하거나 사용하지 않도록 설정하는 스위치입니다.
- **사용 가능한 볼륨 공간**: 서버 끝점이 있는 볼륨에 예약하려는 사용 가능한 공간의 크기입니다. 예를 들어 단일 서버 끝점이 있는 볼륨에서 사용 가능한 볼륨 공간을 50%로 설정하는 경우 데이터 양의 절반 정도가 Azure Files로 계층화됩니다. 클라우드 계층화를 사용하도록 설정할지 여부에 관계없이, Azure 파일 공유는 항상 동기화 그룹에 데이터의 전체 복사본을 유지합니다.

“만들기”를 클릭하여 서버 끝점을 추가합니다. 파일은 이제 Azure 파일 공유 및 Windows Server와 동기화된 상태를 유지합니다. 

> [!Important]  
> 동기화 그룹에 포함된 클라우드 또는 서버 끝점을 변경할 수 있고, 파일이 동기화 그룹에 포함된 다른 끝점과 동기화되도록 할 수 있습니다. 클라우드 끝점(Azure Files 공유)을 직접 변경하는 경우 먼저 Azure Files 동기화 변경 검색 작업에서 변경 내용을 검색해야 합니다. Azure File Sync 변경 검색 작업은 클라우드 끝점에 대해 24시간에 한 번씩 시작됩니다. 자세한 내용은 [Azure Files FAQ](storage-files-faq.md#afs-change-detection)를 참조하세요.

## <a name="next-steps"></a>다음 단계
- [Azure 파일 동기화 서버 끝점 추가/제거](storage-sync-files-server-endpoint.md)
- [Azure 파일 동기화로 서버 등록/등록 취소](storage-sync-files-server-registration.md)