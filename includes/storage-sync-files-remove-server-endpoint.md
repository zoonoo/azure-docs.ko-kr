---
title: 포함 파일
description: 포함 파일
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 05/31/2018
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: be3ce42ccdb9bedd02b8dead2426ac629fa12ef2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "72391720"
---
아니오: 서버 끝점을 제거하는 것은 서버를 재부팅하는 것과 는 다 됩니다! 서버 끝점을 제거하고 다시 만드는 것은 Azure File Sync의 동기화, 클라우드 계층화 또는 기타 측면과 관련된 문제를 해결하는 데 적합한 솔루션이 아닙니다. 서버 끝점을 제거하는 것은 파괴적인 작업입니다. 계층화 된 파일서버 끝점 네임 스페이스 외부에 있는 경우 데이터 손실이 발생할 수 있습니다. 자세한 내용은 [서버 끝점 네임스페이스 외부에 계층화된 파일이 있는 이유를](../articles/storage/files/storage-files-faq.md#afs-tiered-files-out-of-endpoint) 참조하십시오. 또는 서버 끝점 네임스페이스 내에 있는 계층화 된 파일에 액세스할 수 없는 파일이 발생할 수 있습니다. 이러한 문제는 서버 끝점을 다시 만들 때 해결되지 않습니다. 클라우드 계층화를 사용하도록 설정하지 않아도 계층화된 파일이 서버 엔드포인트 네임스페이스에 있을 수 있습니다. 따라서 이 특정 폴더에서 Azure File Sync 사용을 중지하거나 Microsoft 엔지니어가 명시적으로 지시하지 않는 한 서버 끝점을 제거하지 않는 것이 좋습니다. 서버 엔드포인트 제거에 대한 자세한 내용은 [서버 엔드포인트 제거](../articles/storage/files/storage-sync-files-server-endpoint.md#remove-a-server-endpoint)를 참조하세요.    
