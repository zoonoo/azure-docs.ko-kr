---
title: Azure File Sync 에이전트 배포
description: Azure File Sync 에이전트를 배포 하는 중입니다. 마이그레이션 문서 사이에 공유 되는 공통 텍스트 블록
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 694becc49667204ef2071a140bb6330285089039
ms.sourcegitcommit: 5192c04feaa3d1bd564efe957f200b7b1a93a381
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/02/2020
ms.locfileid: "78209416"
---
이 섹션에서는 Windows Server에 Azure File Sync 에이전트를 설치 합니다.
[배포 가이드](../articles/storage/files/storage-sync-files-deployment-guide.md) 에서는 **IE 보안 강화**를 해제 해야 함을 보여 줍니다. IE 보안 강화는 Azure File Sync에는 적용 되지 않으며,이 기능을 해제 하면 문제 없이 Azure에 인증할 수 있는 보안 조치입니다.

PowerShell을 열고 다음 명령을 사용 하 여 필수 PowerShell 모듈을 설치 합니다. 메시지가 표시 되 면 전체 모듈 및 NuGet 공급자를 설치 해야 합니다.

```powershell
Install-Module -Name Az -AllowClobber
Install-Module -Name Az.StorageSync
```

서버에서 인터넷에 연결 하는 데 문제가 있는 경우 지금 해결할 시간입니다. Azure File Sync은 인터넷에 대 한 사용 가능한 네트워크 연결을 사용 합니다.
프록시 서버를 인터넷에 연결 해야 하는 것도 지원 됩니다. 에이전트를 설치 하는 동안 지금 컴퓨터 차원 프록시를 구성 하거나 파일 동기화에 사용 되는 프록시를 지정할 수 있습니다.

즉,이 서버에 대 한 방화벽을 열어야 하는 경우 적합 한 접근 방법이 될 수 있습니다. 서버를 설치한 후 서버를 등록 한 후에는 Azure에서 정확한 끝점 Url을 보여 주는 네트워크 연결 보고서가 있습니다 .이 보고서에는 선택한 지역에 대 한 파일 동기화가 통신 해야 합니다. 또한이 보고서는 통신이 필요한 이유를 알려 줍니다. 이 보고서를 사용 하 여이 서버 주변의 방화벽을 특정 Url로 잠글 수 있습니다.

방화벽 전체를 열지 않고 상위 수준 DNS 이름 공간과 통신 하도록 서버를 제한 하는 보다 보수적인 접근 방식을 따를 수도 있습니다. [Azure File Sync 프록시 및 방화벽 설정](../articles/storage/files/storage-sync-files-firewall-and-proxy.md) 문서에서 사용할 수 있는 추가 설명서 및 세부 정보입니다. 사용자 고유의 네트워킹 모범 사례를 따르세요.

서버 *설치* 마법사가 끝날 때 서버 *등록* 마법사가 팝업 됩니다.
이전에서 저장소 동기화 서비스 Azure 리소스에 서버를 등록 합니다.

이러한 단계는 먼저 설치 해야 하는 위의 PowerShell 모듈을 포함 하 여 배포 가이드에 자세히 설명 되어 있습니다. [에이전트 설치를 Azure File Sync](../articles/storage/files/storage-sync-files-deployment-guide.md)합니다.

최신 에이전트를 사용 해야 하며 Microsoft 다운로드 센터: [Azure File Sync-에이전트](https://aka.ms/AFS/agent "Azure File Sync 에이전트 다운로드")에서 다운로드할 수 있습니다.

성공적으로 설치 및 서버를 등록 한 후에는이 단계를 성공적으로 완료 했는지 확인할 수 있습니다. Azure Portal의 저장소 동기화 서비스 리소스로 이동한 다음 왼쪽 메뉴를 따라 "등록 된 서버"로 이동 합니다. 서버가 바로 나열 됩니다.
