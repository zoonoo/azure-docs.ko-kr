---
title: Azure 파일 동기화 토폴로지 수정 | Microsoft Docs
description: Azure 파일 동기화 동기화 토폴로지를 수정하는 방법에 대한 지침
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 4/23/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 12bab4b8099b074f79d0e5307780ec34178f13b8
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111968471"
---
# <a name="modify-your-azure-file-sync-topology"></a>Azure 파일 동기화 토폴로지 수정

이 문서에서는 고객이 Azure 파일 동기화 토폴로지를 수정하는 가장 일반적인 방법과 권장되는 수행 방법을 설명합니다. Azure 파일 동기화 토폴로지를 수정하려면 아래 모범 사례를 참조하여 오류 및/또는 잠재적 데이터 손실을 방지하세요.

## <a name="migrate-a-server-endpoint-to-a-different-azure-file-sync-storage-sync-service"></a>다른 Azure 파일 동기화 스토리지 동기화 서비스로 서버 엔드포인트 마이그레이션

로컬 서버에서 데이터가 최신 상태인지 확인한 후에는 서버 엔드포인트의 프로비저닝을 해제합니다. 이 작업을 수행하는 방법에 대한 지침은 [Azure 파일 동기화 서버 엔드포인트 프로비저닝 해제](./file-sync-deprovision-server-endpoint.md#scenario-2-you-intend-to-delete-your-server-endpoint-and-stop-using-this-specific-azure-file-share)를 참조하세요. 그런 다음, 원하는 동기화 그룹 및 스토리지 동기화 서비스에서 다시 프로비저닝합니다.

서버와 연결된 모든 서버 엔드포인트를 다른 동기화 그룹 또는 스토리지 동기화 서비스와 마이그레이션하려면 [등록된 서버와 연결된 모든 서버 엔드포인트 프로비저닝 해제](#deprovision-all-server-endpoints-associated-with-a-registered-server)를 참조하세요.

## <a name="change-the-granularity-of-a-server-endpoint"></a>서버 엔드포인트의 세분성 변경

로컬 서버에서 데이터가 최신 상태인지 확인한 후([Azure 파일 동기화 서버 엔드포인트 프로비저닝 해제](./file-sync-deprovision-server-endpoint.md#scenario-2-you-intend-to-delete-your-server-endpoint-and-stop-using-this-specific-azure-file-share) 참조) 서버 엔드포인트의 프로비저닝을 해제합니다. 그런 다음, 원하는 세분성으로 다시 프로비저닝합니다.

## <a name="deprovision-azure-file-sync-topology"></a>Azure 파일 동기화 토폴로지 프로비저닝 해제

Azure 파일 동기화 리소스는 서버 엔드포인트, 동기화 그룹, 스토리지 서비스의 특정 순서로 프로비저닝 해제해야 합니다. 전체 흐름은 아래에 설명되어 있지만 원할 경우 어떤 수준에서도 중지할 수 있습니다. 

먼저 Azure Portal의 스토리지 동기화 서비스 리소스로 이동하여 스토리지 동기화 서비스에서 동기화 그룹을 선택합니다. 서버 엔드포인트를 삭제할 때 데이터 무결성 및 가용성을 보장하려면 [Azure 파일 동기화 서버 엔드포인트 프로비저닝 해제](./file-sync-deprovision-server-endpoint.md)의 단계를 따르세요. 동기화 그룹 또는 스토리지 동기화 서비스의 프로비저닝을 해제하려면 모든 서버 엔드포인트를 삭제해야 합니다. 특정 서버 엔드포인트를 삭제하려는 경우에만 여기서 중지할 수 있습니다. 

동기화 그룹의 모든 서버 엔드포인트를 삭제한 후에는 클라우드 엔드포인트를 삭제합니다. 

그런 다음, 동기화 그룹을 삭제합니다. 

삭제할 스토리지 동기화 서비스의 모든 동기화 그룹에 대해 이러한 단계를 반복합니다. 스토리지 동기화 서비스의 모든 동기화 그룹이 삭제되면 스토리지 동기화 서비스 리소스를 삭제합니다.

## <a name="rename-a-server-endpoint-path-or-sync-group"></a>서버 엔드포인트 경로 또는 동기화 그룹 이름 바꾸기

이 기능은 현재 지원되지 않습니다. 

현재 D 드라이브를 사용하고 있으며 클라우드로 마이그레이션할 예정인 경우 [VM의 D: 드라이브를 데이터 디스크로 만들기 - Azure Virtual Machines](../../virtual-machines/windows/change-drive-letter.md)를 참조하세요.

## <a name="deprovision-all-server-endpoints-associated-with-a-registered-server"></a>등록된 서버와 연결된 모든 서버 엔드포인트 프로비저닝 해제

프로비저닝을 해제하기 전에 데이터가 안전하고 완전히 업데이트되었는지 확인하려면 [Azure 파일 동기화 서버 엔드포인트 프로비저닝 해제](./file-sync-deprovision-server-endpoint.md)를 참조하세요.

스토리지 동기화 서비스 리소스로 이동하고 등록된 서버 탭으로 이동합니다. 등록을 취소하려는 서버를 선택하고 **서버 등록 취소** 를 선택합니다. 그러면 해당 서버와 연결된 모든 서버 엔드포인트의 프로비저닝을 즉시 해제할 수 있습니다.

## <a name="next-steps"></a>다음 단계
* [Azure 파일 동기화 서버 엔드포인트 프로비전 해제](./file-sync-deprovision-server-endpoint.md)