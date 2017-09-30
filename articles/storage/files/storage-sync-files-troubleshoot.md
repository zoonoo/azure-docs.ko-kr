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
ms.date: 09/19/2017
ms.author: wgries
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: cf3f3cf63cafc3b883d26144a53066ee421eb2a6
ms.contentlocale: ko-kr
ms.lasthandoff: 09/25/2017

---

# <a name="troubleshoot-azure-file-sync-preview"></a>Azure 파일 동기화(미리 보기) 문제 해결
Azure 파일 동기화(미리 보기)를 사용하면 공유를 Windows 서버 온-프레미스 또는 Azure에 복제할 수 있습니다. 그러면 관리자와 사용자는 Windows Server를 통해(예: SMB 또는 NFS 공유를 통해) 파일 공유에 액세스하게 됩니다. 이는 지사와 같이 Azure 데이터 센터에서 멀리 떨어진 곳에서 데이터에 액세스하고 수정하는 시나리오에 특히 유용합니다. 여러 Windows Server 끝점 간(예: 여러 지사 간)에 데이터가 복제될 수 있습니다.

이 문서는 Azure 파일 동기화 배포와 관련된 문제를 해결하는 데 도움을 주기 위해 작성되었습니다. 여기서 정보를 찾지 못하는 경우, 이 가이드는 시스템에서 중요한 로그를 수집하여 문제를 더 자세히 조사하는 방법을 보여 줍니다. 다음 옵션은 Azure 파일 동기화에 대한 지원을 얻기 위해 사용할 수 있습니다.

- Microsoft 지원: 새 지원 사례를 만들려면 Azure Portal의 “도움말 + 지원” 탭으로 이동하여 “새 지원 요청”을 클릭합니다.
- [Azure Storage 포럼](https://social.msdn.microsoft.com/Forums/en-US/home?forum=windowsazuredata)

## <a name="how-to-troubleshoot-agent-installation-failures"></a>에이전트 설치 오류를 해결하는 방법
Azure 파일 동기화 에이전트 설치에 실패할 경우 에이전트 설치 중 로깅을 사용하도록 설정하려면 관리자 권한 명령 프롬프트에서 다음 명령을 실행합니다.

```
StorageSyncAgent.msi /l*v Installer.log
```

설치에 실패한 후 원인을 확인하려면 installer.log를 검토합니다.

## <a name="server-is-not-listed-under-registered-servers-in-the-azure-portal"></a>Azure Portal의 등록된 서버 아래에 서버가 나열되지 않음
저장소 동기화 서비스의 등록된 서버 아래에 서버가 나열되지 않으면 다음 단계를 수행합니다.
1. 등록하려는 서버에 로그인합니다.
2. 파일 탐색기를 열고 저장소 동기화 에이전트 설치 디렉터리(기본 위치는 `C:\Program Files\Azure\StorageSyncAgent`)로 이동합니다. 
3. ServerRegistration.exe를 실행하고 마법사의 안내에 따라 서버를 저장소 동기화 서비스에 등록합니다.

## <a name="server-registration-displays-the-following-message-after-installing-the-azure-file-sync-agent-this-server-is-already-registered"></a>Azure 파일 동기화 에이전트가 설치된 후 “서버가 이미 등록되었습니다.”라는 메시지가 표시됩니다.
![“This server is already registered”(서버가 이미 등록되었습니다.) 오류 메시지가 있는 서버 등록 대화 상자의 스크린샷](media/storage-sync-files-troubleshoot/server-registration-1.png)

서버가 전에 저장소 동기화 서비스에 등록된 경우 이 메시지가 표시됩니다. 현재 저장소 동기화 서비스에서 서버 등록을 취소하고 새 저장소 동기화 서비스에 등록하려면 [Azure 파일 동기화로 서버 등록 해제](storage-sync-files-server-registration.md#unregister-the-server-with-storage-sync-service) 단계를 수행하세요.

## <a name="how-to-troubleshoot-sync-not-working-on-a-server"></a>서버에서 작동하지 않는 동기화 문제 해결 방법
서버에서 동기화에 실패하는 경우 다음을 수행합니다.
- Azure 파일 공유에 동기화하려는 디렉터리에 대해 Azure Portal에 서버 끝점이 있는지 확인합니다.
    
    ![Azure Portal에 클라우드 및 서버 끝점이 모두 포함된 동기화 그룹의 스크린샷](media/storage-sync-files-troubleshoot/sync-troubleshoot-1.png)

- `Applications and Services\Microsoft\FileSync\Agent` 아래에 있는 이벤트 뷰어를 사용하여 운영 및 진단 이벤트 로그를 검토합니다.
- 서버가 인터넷에 연결되어 있는지 확인합니다.
- 서비스 MMC 스냅인을 열어 서버에서 Azure 파일 동기화 서비스가 실행 중인지 확인하고, 저장소 동기화 에이전트 서비스(FileSyncSvc)가 실행 중인지 확인합니다.

## <a name="how-to-troubleshoot-individual-files-failing-to-sync"></a>동기화에 실패한 개별 파일의 문제 해결 방법 
개별 파일이 동기화에 실패한 경우 다음 단계를 수행합니다.
- 이벤트 뷰어의 `Applications and Services\Microsoft\FileSync\Agent` 아래에서 운영 및 진단 이벤트 로그를 검토합니다.
- 파일에 열린 핸들이 없는지 확인합니다.
    - 참고: Azure 파일 동기화는 VSS 스냅샷을 주기적으로 사용하여 열린 핸들로 파일을 동기화합니다.

## <a name="how-to-troubleshoot-files-that-fail-to-tier"></a>계층화에 실패한 파일의 문제 해결 방법
Azure Files에 파일을 계층화하지 못할 경우 다음 단계를 수행합니다.

- Azure 파일 공유에 파일이 있는지 확인합니다.
    - 참고: 계층화하려면 파일을 먼저 Azure 파일 공유에 동기화해야 합니다.
- 이벤트 뷰어의 `Applications and Services\Microsoft\FileSync\Agent` 아래에 있는 운영 및 진단 이벤트 로그를 검토합니다.
- 서버가 인터넷에 연결되어 있는지 확인합니다. 
- Azure 파일 동기화 필터 드라이버(StorageSync.sys 및 StorageSyncGuard.sys)가 실행 중인지 확인합니다.
    - 관리자 권한 명령 프롬프트를 열고, `fltmc`를 실행하여 StorageSync.sys 및 StorageSyncGuard.sys 파일 시스템 필터 드라이버가 나열되는지 확인합니다.

## <a name="how-to-troubleshoot-files-that-fail-to-be-recalled"></a>회수에 실패한 파일의 문제 해결 방법
파일을 회수하지 못하는 경우 다음 단계를 수행합니다.
- 이벤트 뷰어의 `Applications and Services\Microsoft\FileSync\Agent` 아래에 있는 운영 및 진단 이벤트 로그를 검토합니다.
- Azure 파일 공유에 파일이 있는지 확인합니다.
- 서버가 인터넷에 연결되어 있는지 확인합니다. 
- Azure 파일 동기화 필터 드라이버(StorageSync.sys 및 StorageSyncGuard.sys)가 실행 중인지 확인합니다.
    - 관리자 권한 명령 프롬프트를 열고, `fltmc`를 실행하여 StorageSync.sys 및 StorageSyncGuard.sys 파일 시스템 필터 드라이버가 나열되는지 확인합니다.

## <a name="how-to-troubleshoot-files-being-unexpectedly-recalled-on-a-server"></a>서버에서 예기치 않게 회수되는 파일의 문제 해결 방법
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
