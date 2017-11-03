---
title: "Azure 파일 동기화(미리 보기) 문제 해결 | Microsoft Docs"
description: "Azure 파일 동기화와 관련된 일반적인 문제 해결"
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
ms.openlocfilehash: 6bc5f2da2b8628671037b9257db746e73cd3afad
ms.sourcegitcommit: 76a3cbac40337ce88f41f9c21a388e21bbd9c13f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/25/2017
---
# <a name="troubleshoot-azure-file-sync-preview"></a>Azure 파일 동기화(미리 보기) 문제 해결
Azure File Sync(미리 보기)를 사용하여 온-프레미스 파일 서버의 유연성, 성능 및 호환성을 희생하지 않고 Azure 파일에서 조직의 파일 공유를 중앙 집중화할 수 있습니다. 이 작업은 Windows Server를 Azure 파일 공유의 빠른 캐시로 변환하여 수행합니다. Windows Server에서 사용할 수 있는 아무 프로토콜이나 사용하여 데이터를 로컬로(SMB, NFS 및 FTPS 포함) 액세스할 수 있으며 세계 전역에 걸쳐 필요한 만큼 캐시를 보유할 수 있습니다.

이 문서는 Azure 파일 동기화 배포와 관련된 문제를 해결하는 데 도움을 주기 위해 작성되었습니다. 여기서 정보를 찾지 못하는 경우, 이 가이드는 시스템에서 중요한 로그를 수집하여 문제를 더 자세히 조사하는 방법을 보여 줍니다. 여기에서 질문에 대한 답을 찾지 못한 경우 다음 채널을 통해 (제시된 채널 순서에 따라) 문의하시기 바랍니다.

1. 이 문서의 의견 섹션.
2. [Azure Storage 포럼](https://social.msdn.microsoft.com/Forums/home?forum=windowsazuredata)
3. [Azure Files UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files) 
4. Microsoft 지원: 새 지원 사례를 만들려면 Azure Portal의 “도움말 + 지원” 탭으로 이동하여 “새 지원 요청”을 클릭합니다.

## <a name="agent-installation-and-server-registration"></a>에이전트 설치 및 서버 등록
<a id="agent-installation-failures"></a>**에이전트 설치 오류를 해결하는 방법**  
Azure 파일 동기화 에이전트 설치에 실패할 경우 에이전트 설치 중 로깅을 사용하도록 설정하려면 관리자 권한 명령 프롬프트에서 다음 명령을 실행합니다.

```
StorageSyncAgent.msi /l*v Installer.log
```

설치에 실패한 후 원인을 확인하려면 installer.log를 검토합니다. 

> [!Note]  
> Microsoft 업데이트를 사용하도록 선택했으며 Windows 업데이트 서비스가 실행되지 않는 경우 에이전트 설치가 실패합니다.

<a id="server-registration-missing"></a>**Azure Portal의 등록된 서버 아래에 서버가 나열되지 않음**  
저장소 동기화 서비스의 등록된 서버 아래에 서버가 나열되지 않으면 다음 단계를 수행합니다.
1. 등록하려는 서버에 로그인합니다.
2. 파일 탐색기를 열고 저장소 동기화 에이전트 설치 디렉터리(기본 위치는 `C:\Program Files\Azure\StorageSyncAgent`)로 이동합니다. 
3. ServerRegistration.exe를 실행하고 마법사의 안내에 따라 서버를 저장소 동기화 서비스에 등록합니다.

<a id="server-already-registered"></a>**Azure File Sync 에이전트가 설치된 후 “서버가 이미 등록되었습니다.”라는 메시지가 표시됩니다.**  
![“This server is already registered”(서버가 이미 등록되었습니다.) 오류 메시지가 있는 서버 등록 대화 상자의 스크린샷](media/storage-sync-files-troubleshoot/server-registration-1.png)

서버가 전에 저장소 동기화 서비스에 등록된 경우 이 메시지가 표시됩니다. 현재 저장소 동기화 서비스에서 서버 등록을 취소하고 새 저장소 동기화 서비스에 등록하려면 [Azure 파일 동기화로 서버 등록 해제](storage-sync-files-server-registration.md#unregister-the-server-with-storage-sync-service) 단계를 수행하세요.

서버가 저장소 동기화 서비스의 등록된 서버 아래에 표시되지 않으면 등록을 취소하려는 서버에서 다음 PowerShell 명령을 실행합니다.

```PowerShell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Reset-StorageSyncServer
```

> [!Note]  
> 서버가 클러스터에 속하는 경우 클러스터 등록을 제거하는 선택적 `Reset-StorageSyncServer -CleanClusterRegistration` 매개 변수도 있습니다.

<a id="web-site-not-trusted"></a>**서버를 등록할 때 “웹 사이트를 신뢰할 수 없습니다.” 메시지가 여러 번 표시되는 이유는 무엇인가요?**  
이 오류는 서버 등록 중에 **Internet Explorer 보안 강화** 정책이 사용되도록 설정되었기 때문에 발생합니다. **Internet Explorer 보안 강화** 정책을 사용하지 않도록 적절히 설정하는 방법에 대한 자세한 내용은 [Azure 파일 동기화와 함께 사용하도록 Windows Server 준비](storage-sync-files-deployment-guide.md#prepare-windows-servers-for-use-with-azure-file-sync) 및 [Azure 파일 동기화를 배포하는 방법(미리 보기)](storage-sync-files-deployment-guide.md)을 참조하세요.

## <a name="sync-group-management"></a>동기화 그룹 관리
<a id="cloud-endpoint-using-share"></a>**클라우드 끝점 생성이 “지정된 Azure Files 공유가 이미 다른 클라우드 끝점에서 사용되고 있습니다.” 오류를 나타내며 실패합니다.**  
Azure 파일 공유가 다른 클라우드 끝점에서 이미 사용되고 있으면 이 오류가 발생합니다. 

이 오류가 표시되고 Azure 파일 공유가 현재 클라우드 끝점에서 사용되고 있지 않으면 아래의 단계를 수행하여 Azure 파일 공유에서 Azure File Sync 메타데이터를 지우세요.

> [!Warning]  
> 클라우드 끝점에서 현재 사용 중인 Azure 파일 공유에서 메타데이터를 삭제하면 Azure File Sync 작업이 실패합니다. 

1. Azure Portal에서 Azure 파일 공유로 이동합니다.  
2. Azure 파일 공유를 마우스 오른쪽 단추로 클릭하고 **메타데이터 편집**을 선택합니다.
3. SyncService를 마우스 오른쪽 단추로 클릭하고 **삭제**를 선택합니다.

<a id="cloud-endpoint-authfailed"></a>**AuthorizationFailed 오류로 인해 클라우드 끝점 생성 실패**  
이 문제는 사용자 계정에 클라우드 끝점 생성을 위한 권한이 없는 경우 발생합니다. 

클라우드 끝점을 만들려면 사용자 계정에 다음과 같은 Microsoft 인증 권한이 있어야 합니다.  
* 읽기: 역할 정의 가져오기
* 쓰기: 사용자 지정 역할 정의 만들기 또는 업데이트
* 읽기: 역할 할당 가져오기
* 쓰기: 역할 할당 만들기

다음과 같은 기본 제공 역할은 적절한 Microsoft 인증 권한을 갖고 있습니다.  
* 소유자
* 사용자 액세스 관리자

사용자 계정 역할에 적절한 권한이 있는지 확인하려면 다음을 수행합니다.  
* Azure Portal에서 **리소스 그룹**을 클릭합니다.
* 저장소 계정이 있는 리소스 그룹을 선택하고 **액세스 제어(IAM)**를 클릭합니다.
* 사용자 계정의 **역할**(예: 소유자, 참여자)을 클릭합니다.
* **리소스 공급자** 목록에서 **Microsoft 인증**을 선택합니다. 
* **역할 할당**에 **읽기** 권한과 **쓰기 권한**이 있어야 합니다.
* **역할 정의**에 **읽기**  권한과 **쓰기 권한**이 있어야 합니다.

<a id="cloud-endpoint-deleteinternalerror"></a>**MgmtInternalError 오류로 인해 끝점 삭제 실패**  
이 문제는 클라우드 끝점이 삭제되기 전에 Azure Files 공유 또는 저장소 계정이 삭제된 경우 발생할 수 있습니다. 향후 업데이트에서 이 문제가 해결될 예정이며, 이후에 클라우드 끝점을 삭제할 수 있습니다.

이 문제를 방지하려면 Azure Files 공유 또는 저장소 계정을 삭제하기 전에 클라우드 끝점을 삭제하면 됩니다.

## <a name="sync"></a>동기화
<a id="afs-change-detection"></a>**SMB 또는 포털을 통해 Azure Files 공유에서 직접 파일을 만들었습니다. 파일이 동기화 그룹의 서버와 동기화되는 데 시간이 얼마나 걸리나요?**  
[!INCLUDE [storage-sync-files-change-detection](../../../includes/storage-sync-files-change-detection.md)]

<a id="broken-sync"></a>**서버에서 작동하지 않는 동기화 문제 해결 방법**  
서버에서 동기화에 실패하는 경우 다음을 수행합니다.
- Azure 파일 공유에 동기화하려는 디렉터리에 대해 Azure Portal에 서버 끝점이 있는지 확인합니다.
    
    ![Azure Portal에 클라우드 및 서버 끝점이 모두 포함된 동기화 그룹의 스크린샷](media/storage-sync-files-troubleshoot/sync-troubleshoot-1.png)

- `Applications and Services\Microsoft\FileSync\Agent` 아래에 있는 이벤트 뷰어를 사용하여 운영 및 진단 이벤트 로그를 검토합니다.
- 서버가 인터넷에 연결되어 있는지 확인합니다.
- 서비스 MMC 스냅인을 열어 서버에서 Azure 파일 동기화 서비스가 실행 중인지 확인하고, 저장소 동기화 에이전트 서비스(FileSyncSvc)가 실행 중인지 확인합니다.

<a id="replica-not-ready"></a>**0x80c8300f - “복제본이 해당 작업을 수행할 준비가 되지 않았습니다.” 오류로 인해 동기화 실패**   
이 오류는 클라우드 끝점을 만든 뒤에 데이터가 포함된 Azure Files 공유를 사용하는 경우 발생할 수 있습니다. Azure Files 공유에서 변경 검색이 완료되면(최대 24시간이 걸릴 수 있음) 동기화가 올바르게 작동하기 시작합니다.

<a id="broken-sync-files"></a>**동기화에 실패한 개별 파일의 문제 해결 방법**  
개별 파일이 동기화에 실패한 경우 다음 단계를 수행합니다.
- 이벤트 뷰어의 `Applications and Services\Microsoft\FileSync\Agent` 아래에서 운영 및 진단 이벤트 로그를 검토합니다.
- 파일에 열린 핸들이 없는지 확인합니다.
    - 참고: Azure 파일 동기화는 VSS 스냅샷을 주기적으로 사용하여 열린 핸들로 파일을 동기화합니다.

## <a name="cloud-tiering"></a>클라우드 계층화 
<a id="files-fail-tiering"></a>**계층화에 실패한 파일의 문제 해결 방법**  
Azure Files에 파일을 계층화하지 못할 경우 다음 단계를 수행합니다.

- Azure 파일 공유에 파일이 있는지 확인합니다.
    - 참고: 계층화하려면 파일을 먼저 Azure 파일 공유에 동기화해야 합니다.
- 이벤트 뷰어의 `Applications and Services\Microsoft\FileSync\Agent` 아래에 있는 운영 및 진단 이벤트 로그를 검토합니다.
- 서버가 인터넷에 연결되어 있는지 확인합니다. 
- Azure 파일 동기화 필터 드라이버(StorageSync.sys 및 StorageSyncGuard.sys)가 실행 중인지 확인합니다.
    - 관리자 권한 명령 프롬프트를 열고, `fltmc`를 실행하여 StorageSync.sys 및 StorageSyncGuard.sys 파일 시스템 필터 드라이버가 나열되는지 확인합니다.

<a id="files-fail-recall"></a>**회수에 실패한 파일의 문제 해결 방법**  
파일을 회수하지 못하는 경우 다음 단계를 수행합니다.
- 이벤트 뷰어의 `Applications and Services\Microsoft\FileSync\Agent` 아래에 있는 운영 및 진단 이벤트 로그를 검토합니다.
- Azure 파일 공유에 파일이 있는지 확인합니다.
- 서버가 인터넷에 연결되어 있는지 확인합니다. 
- Azure 파일 동기화 필터 드라이버(StorageSync.sys 및 StorageSyncGuard.sys)가 실행 중인지 확인합니다.
    - 관리자 권한 명령 프롬프트를 열고, `fltmc`를 실행하여 StorageSync.sys 및 StorageSyncGuard.sys 파일 시스템 필터 드라이버가 나열되는지 확인합니다.

<a id="files-unexpectedly-recalled"></a>**서버에서 예기치 않게 회수되는 파일의 문제 해결 방법**  
바이러스 백신, 백업, 그리고 대량의 파일을 읽는 기타 응용 프로그램은 오프라인 특성을 존중하여 해당 파일의 내용 읽기를 건너뛰지 않는 경우 원치 않은 회수가 발생합니다. 지원 제품에 대한 오프라인 파일을 건너뛰면, 바이러스 백신 검색이나 백업 작업 등을 실행할 때 원치 않은 회수를 방지하는 데 도움이 됩니다.

오프라인 파일 읽기를 건너뛰도록 솔루션을 구성하는 방법은 소프트웨어 공급업체에 문의하세요.

파일 탐색기에서 파일을 찾아보는 것과 같은 기타 시나리오에서 원치 않은 회수가 더 발생할 수 있습니다. 서버의 파일 탐색기에서 클라우드 계층 파일이 있는 폴더를 열면 원치 않은 회수가 발생할 수 있으며, 서버에서 바이러스 백신을 사용하는 경우 더 많이 발생할 수 있습니다.

## <a name="general-troubleshooting"></a>일반적인 문제 해결
서버에서 Azure 파일 동기화에 문제가 발생하는 경우 다음을 수행하여 시작합니다.
- 이벤트 뷰어에서 진단 및 운영 이벤트 로그를 검토합니다.
    - 동기화, 계층화 및 회수 문제가 `Applications and Services\Microsoft\FileSync\Agent`의 진단 및 운영 이벤트 로그에 기록됩니다.
    - 서버를 관리하는 문제(예 : 구성 설정)가 `Applications and Services\Microsoft\FileSync\Management`의 진단 및 운영 이벤트 로그에 기록됩니다.
- 서버에서 Azure 파일 동기화 서비스가 실행 중인지 확인합니다.
    - 서비스 MMC 스냅인을 열고 저장소 동기화 에이전트 서비스(FileSyncSvc)가 실행 중인지 확인합니다.
- Azure 파일 동기화 필터 드라이버(StorageSync.sys 및 StorageSyncGuard.sys)가 실행 중인지 확인합니다.
    - 관리자 권한 명령 프롬프트를 열고, fltmc를 실행하여 StorageSync.sys 및 StorageSyncGuard.sys 파일 시스템 필터 드라이버가 나열되는지 확인합니다.

위의 단계를 수행한 후 문제가 해결되지 않으면, 다음 단계를 수행하여 AFSDiag 도구를 실행합니다.
1. AFSDiag 출력을 저장하는 데 사용할 디렉터리를 만듭니다(예: c:\output).
2. 관리자 권한으로 PowerShell 창을 열고 다음 명령을 실행합니다(각 명령 후 Enter 키 누름).

    ```PowerShell
    cd "c:\Program Files\Azure\StorageSyncAgent"
    Import-Module .\afsdiag.ps1
    Debug-Afs c:\output # Note: use the path created in step 1
    ```

3. Azure 파일 동기화 커널 모드 추적 수준에 대해 1을 입력하고(더 자세한 추적을 만들도록 지정하지 않는 경우) Enter 키를 누릅니다.
4. Azure 파일 동기화 사용자 모드 추적 수준에 대해 1을 입력하고(더 자세한 추적을 만들도록 지정하지 않는 경우) Enter 키를 누릅니다.
5. 문제를 재현하고 완료되면 D를 누릅니다.
6. 로그 및 추적 파일이 포함된 .zip 파일이 지정된 출력 디렉터리에 생성됩니다.

## <a name="see-also"></a>참고 항목
- [Azure Files 질문과 대답](storage-files-faq.md)
- [Windows에서 Azure Files 문제 해결](storage-troubleshoot-windows-file-connection-problems.md)
- [Linux에서 Azure Files 문제 해결](storage-troubleshoot-linux-file-connection-problems.md)
