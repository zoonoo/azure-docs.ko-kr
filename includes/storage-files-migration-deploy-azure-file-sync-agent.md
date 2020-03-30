---
title: Azure 파일 동기화 에이전트 배포
description: Azure 파일 동기화 에이전트 배포. 마이그레이션 문서 간에 공유되는 공통 텍스트 블록입니다.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 694becc49667204ef2071a140bb6330285089039
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78209416"
---
이 섹션에서는 Windows 서버에 Azure 파일 동기화 에이전트를 설치합니다.
[배포 가이드는](../articles/storage/files/storage-sync-files-deployment-guide.md) **IE 강화 보안을**해제해야 한다는 것을 보여 줍니다. IE 강화 보안Azure 파일 동기화에 적용할 수 없는 보안 조치이며, 이 기능을 해제하면 문제 없이 Azure를 인증할 수 있습니다.

PowerShell을 열고 다음 명령으로 필요한 PowerShell 모듈을 설치합니다. 메시지가 표시되면 전체 모듈과 NuGet 공급자를 설치해야 합니다.

```powershell
Install-Module -Name Az -AllowClobber
Install-Module -Name Az.StorageSync
```

서버에서 인터넷에 도달하는 데 문제가 있는 경우 지금이 해결해야 할 때입니다. Azure File Sync는 인터넷에 사용할 수 있는 모든 네트워크 연결을 사용합니다.
프록시 서버가 인터넷에 도달하도록 요구하는 것도 지원됩니다. 지금 컴퓨터 전체 프록시를 구성하거나 에이전트 설치 중에 파일 동기화만 사용할 프록시를 지정할 수 있습니다.

즉, 이 서버에 대 한 방화벽을 열어야 하는 경우, 다음 그것은 당신에 게 허용 하는 접근 될 수 있습니다. 서버 설치가 끝나면 서버 등록이 완료되면 Azure의 정확한 끝점 URL을 보여 주는 네트워크 연결 보고서가 표시되며, 해당 파일 동기화는 선택한 지역에 대해 통신해야 합니다. 또한 이 보고서는 통신이 필요한 이유를 알려줍니다. 보고서를 사용하여 이 서버 주변의 방화벽을 특정 URL로 잠글 수 있습니다.

또한 방화벽을 광범위하게 열지 않고 서버를 제한하여 상위 DNS 이름 공간과 통신하도록 제한하는 보다 보수적인 접근 방식을 따를 수도 있습니다 [Azure File Sync proxy and firewall settings](../articles/storage/files/storage-sync-files-firewall-and-proxy.md) . 자체 네트워킹 모범 사례를 따르십시오.

서버 *설치* 마법사가 끝나면 서버 *등록* 마법사가 나타납니다.
이전 서버에서 저장소 동기화 서비스 Azure 리소스에 서버를 등록합니다.

이러한 단계는 먼저 설치해야 하는 위의 PowerShell [모듈을](../articles/storage/files/storage-sync-files-deployment-guide.md)포함하여 배포 가이드에서 자세히 설명합니다.

최신 에이전트를 사용해야 하며 Microsoft 다운로드 센터: [Azure 파일 동기화 - 에이전트에서](https://aka.ms/AFS/agent "Azure 파일 동기화 에이전트 다운로드")다운로드할 수 있습니다.

설치 및 서버 등록에 성공한 후 이 단계를 성공적으로 완료한 지 확인할 수 있습니다. 서버가 바로 거기에 나열되어 표시됩니다.
