---
title: macOS에서 SMB를 통해 Azure 파일 공유 탑재 | Microsoft Docs
description: Finder 또는 Terminal를 사용 하 여 macOS에서 SMB를 통해 Azure 파일 공유를 탑재 하는 방법에 대해 알아봅니다. Azure Files는 사용하기 쉬운 Microsoft 클라우드 파일 시스템입니다.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 09/23/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 119f4c0ea434bc431b40c905d9142e187b7d9474
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91326068"
---
# <a name="mount-azure-file-share-over-smb-with-macos"></a>macOS에서 SMB를 통해 Azure 파일 공유 탑재
[Azure Files](storage-files-introduction.md)는 사용하기 쉬운 Microsoft 클라우드 파일 시스템입니다. Azure 파일 공유는 macOS High 시에라리온 10.13 +에서 업계 표준 SMB 3 프로토콜을 사용 하 여 탑재할 수 있습니다. 이 문서에서는 두 가지 방법, 즉 찾기(Finder) UI 및 터미널을 사용하여 macOS에 Azure 파일 공유를 탑재합니다.

## <a name="prerequisites-for-mounting-an-azure-file-share-on-macos"></a>macOS에 Azure 파일 공유를 탑재하기 위한 필수 구성 요소
* **스토리지 계정 이름**: Azure 파일 공유를 탑재하려면 스토리지 계정의 이름이 필요합니다.

* **스토리지 계정 키**: Azure 파일 공유를 탑재하려면 기본(또는 보조) 스토리지 키가 필요합니다. SAS 키는 현재 탑재를 지원하지 않습니다.

* **445 포트가 열려 있는지 확인**: SMB는 445 TCP 포트를 통해 통신합니다. 클라이언트 컴퓨터(Mac)에서 방화벽이 445 TCP 포트를 차단하고 있지 않은지 확인합니다.

## <a name="mount-an-azure-file-share-via-finder"></a>찾기를 통해 Azure 파일 공유 탑재
1. **찾기를 엽니다**. 찾기는 기본적으로 macOS에서 열리지만 도킹 스테이션에서 "macOS 얼굴 아이콘"을 클릭하여 현재 선택된 애플리케이션인지 확인할 수 있습니다.  
    ![macOS 얼굴 아이콘](./media/storage-how-to-use-files-mac/mount-via-finder-1.png)

2. **"이동" 메뉴에서 "서버에 연결"을 선택**합니다. 필수 조건에서 UNC 경로를 사용 하 여 시작 이중 백슬래시 ( `\\` )를로 변환 하 `smb://` 고 다른 모든 백슬래시 ( `\` )는 슬래시 ()를 전달 `/` 합니다. 링크는 다음과 같습니다. !["서버에 연결" 대화 상자](./media/storage-how-to-use-files-mac/mount-via-finder-2.png)

3. **사용자 이름과 암호를 묻는 메시지가 표시되면 스토리지 계정 이름과 스토리지 계정 키를 사용합니다**. &quot;서버에 연결&quot; 대화 상자에서 &quot;연결&quot;을 클릭하면 사용자 이름과 암호를 입력하라는 메시지가 표시됩니다. (이 경우 macOS 사용자 이름이 자동으로 채워져 있습니다.) macOS 키 집합에 스토리지 계정 이름/스토리지 계정 키를 배치할 수 있는 옵션이 있습니다.

4. **Azure 파일 공유를 원하는 대로 사용합니다**. 공유 이름과 스토리지 계정 키를 사용자 이름과 암호로 대체하면 해당 공유가 탑재됩니다. 일반적으로 파일 공유로 파일 끌어 놓기를 포함하여 로컬 폴더/파일 공유를 사용하는 것처럼 사용할 수 있습니다.

    ![탑재된 Azure 파일 공유의 스냅샷](./media/storage-how-to-use-files-mac/mount-via-finder-3.png)

## <a name="mount-an-azure-file-share-via-terminal"></a>터미널을 통해 Azure 파일 공유 탑재
1.  `<storage-account-name>`, `<storage-account-key>` 및를 `<share-name>`   사용자 환경에 적합 한 값으로 바꿉니다.

    ```
    open smb://<storage-account-name>:<storage-account-key>@<storage-account-name>.file.core.windows.net/<share-name>
    ```

2. **원하는 대로 Azure 파일 공유를 사용합니다**. Azure 파일 공유는 이전 명령으로 지정된 탑재 지점에 탑재됩니다.  

    ![탑재된 Azure 파일 공유의 스냅샷](./media/storage-how-to-use-files-mac/mount-via-terminal-1.png)

## <a name="next-steps"></a>다음 단계
* [공유 컴퓨터 및 서버에 Mac 연결-Apple 지원](https://support.apple.com/guide/mac-help/connect-mac-shared-computers-servers-mchlp1140/mac)