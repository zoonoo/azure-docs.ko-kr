---
title: 포함 파일
description: 포함 파일
services: storage
author: fauhse
ms.service: storage
ms.topic: include
ms.date: 2/20/2020
ms.author: fauhse
ms.custom: include file
ms.openlocfilehash: 7c1ea3fbf8e5cef4b1e8f8a2b0963fdd66584f5a
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106075596"
---
이 섹션에서는 Windows Server 인스턴스에 Azure 파일 동기화 에이전트를 설치합니다.

[배포 가이드](../articles/storage/files/storage-sync-files-deployment-guide.md)에서는 **Internet Explorer 보안 강화 구성** 을 해제해야 함을 보여 줍니다. 이 보안 조치는 Azure 파일 동기화에는 적용되지 않습니다. 이 기능을 해제하면 문제 없이 Azure에 인증할 수 있습니다.

PowerShell을 열고 다음 명령을 사용하여 필요한 PowerShell 모듈을 설치합니다. 메시지가 표시되면 전체 모듈 및 NuGet 공급자를 설치해야 합니다.

```powershell
Install-Module -Name Az -AllowClobber
Install-Module -Name Az.StorageSync
```

서버에서 인터넷에 연결하는 데 문제가 있는 경우 지금이 해결할 시간입니다. Azure 파일 동기화는 인터넷에 대해 사용 가능한 모든 네트워크 연결을 사용합니다. 프록시 서버를 인터넷에 연결해야 하는 것도 지원됩니다. 에이전트를 설치하는 동안 시스템 차원 프록시를 지금 구성하거나 Azure 파일 동기화만 사용할 프록시를 지정할 수 있습니다.

프록시를 구성하는 경우 이 서버에 대한 방화벽을 열어야 하므로 이 방법이 허용될 수 있습니다. 서버를 설치하고 서버 등록을 완료하면 네트워크 연결 보고서는 사용자가 선택한 지역에서 Azure 파일 동기화가 통신해야 하는 정확한 Azure 엔드포인트 URL을 표시합니다. 또한 이 보고서는 통신이 필요한 이유를 알려 줍니다. 이 보고서를 사용하여 이 서버 주변의 방화벽을 특정 URL로 잠글 수 있습니다.

방화벽 전체를 열지 않고 서버가 상위 수준 DNS 네임스페이스와 통신하도록 제한하는 보다 보수적인 접근 방식을 따를 수도 있습니다. 자세한 내용은 [Azure 파일 동기화 프록시 및 방화벽 설정](../articles/storage/files/storage-sync-files-firewall-and-proxy.md)을 참조하세요. 자체 네트워킹 모범 사례를 따르세요.

서버 설치 마법사가 끝날 때 서버 등록 마법사가 열립니다. 이전의 스토리지 동기화 서비스의 Azure 리소스에 서버를 등록합니다.

이러한 단계는 배포 가이드 [Azure 파일 동기화 에이전트 설치](../articles/storage/files/storage-sync-files-deployment-guide.md)에 자세히 설명되어 있습니다. 여기에는 먼저 설치해야 하는 PowerShell 모듈이 포함됩니다.

최신 에이전트를 사용하세요. Microsoft 다운로드 센터에서 다운로드할 수 있습니다. [Azure 파일 동기화 에이전트](https://aka.ms/AFS/agent "Azure 파일 동기화 에이전트 다운로드").

성공적으로 설치하고 서버를 등록한 후에는 이 단계가 성공적으로 완료되었는지 확인할 수 있습니다. Azure Portal의 스토리지 동기화 서비스 리소스로 이동합니다. 왼쪽 메뉴에서 **등록된 서버** 로 이동합니다. 여기에 서버가 나열됩니다.
