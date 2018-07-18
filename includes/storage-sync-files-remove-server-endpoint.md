---
title: 포함 파일
description: 포함 파일
services: storage
author: wmgries
ms.service: storage
ms.topic: include
ms.date: 05/31/2018
ms.author: wgries
ms.custom: include file
ms.openlocfilehash: 33dff710d83bd12a8db343a89c6e4576d1397ba7
ms.sourcegitcommit: c722760331294bc8532f8ddc01ed5aa8b9778dec
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2018
ms.locfileid: "34738686"
---
없음: 서버 끝점을 제거하는 것이 서버를 다시 부팅하는 것과 같은 결과를 가져오지 않습니다. 서버 끝점을 제거했다가 다시 만드는 것이 Azure File Sync의 동기화, 클라우드 계층화 또는 기타 측면의 문제를 해결하기 위한 적절한 해결 방법이 아닙니다. 서버 끝점을 제거하는 것은 파괴 작업으로, 계층화된 데이터가 서버 끝점 네임스페이스 외부에 있을 경우 데이터가 손실될 수 있고([계층화된 파일이 서버 끝점 네임스페이스 외부에 있는 이유](../articles/storage/files/storage-files-faq.md#afs-tiered-files-out-of-endpoint) 참조) 서버 끝점 네임스페이스 내에 있는 계층화된 파일에 액세스하지 못할 수 있습니다. 이러한 문제는 서버 끝점을 다시 만들어도 해결되지 않습니다. 클라우드 계층화를 사용하도록 설정하지 않아도 계층화된 파일이 서버 끝점 네임스페이스에 있을 수 있습니다. 따라서 특정 폴더에 Azure File Sync를 더 이상 사용하지 않거나 Microsoft 엔지니어가 명시적으로 이렇게 하도록 지시한 경우가 아니라면, 서버 끝점을 제거하지 않는 것이 좋습니다. 서버 끝점 제거에 대한 자세한 내용은 [서버 끝점 제거](../articles/storage/files/storage-sync-files-server-endpoint.md#remove-a-server-endpoint)를 참조하세요.    