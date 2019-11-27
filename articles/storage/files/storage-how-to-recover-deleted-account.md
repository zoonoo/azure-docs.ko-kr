---
title: Azure Storage | Azure 파일 공유 사용 Microsoft Docs
description: Windows 및 Windows Server에서 Azure 파일 공유를 사용하는 방법을 알아봅니다.
author: todmccoy
manager: dcscontentpm
ms.service: storage
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: rogarana
ms.subservice: files
services: storage
tags: ''
ms.openlocfilehash: e1e581134b4e3821659c43bb7f55003239594bd9
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74233793"
---
# <a name="how-to-recover-a-deleted-storage-account"></a>삭제 된 저장소 계정을 복구 하는 방법

Azure Storage는 자동화 된 복제본을 통해 데이터 복원 력을 제공 하지만 사용자 또는 응용 프로그램 코드에서 실수로 또는 악의적으로 데이터를 손상 시키는 것을 방지 하지 않습니다. 응용 프로그램 또는 사용자 오류 중 데이터 충실도를 유지 하려면 감사 로그를 사용 하 여 데이터를 보조 저장소 위치에 복사 하는 등의 고급 기술이 필요 합니다.

다음 표에서는 복제 전략에 따라 저장소 계정 복구 범위에 대 한 개요를 제공 합니다.

| |LRS|ZRS|GRS|RA-GRS|
|---|---|---|---|---|
|저장소 계정 Azure Resource Manager|예|예|예|예|
|Storage 계정 클래식|예|예|예|예|

다음 정보를 수집 하 고 Microsoft 지원를 사용 하 여 지원 요청을 파일 합니다.

* Storage 계정 이름
* 삭제 날짜
* 저장소 계정 영역
* 저장소 계정은 어떻게 삭제 되었습니까?
* 어떤 방법으로 저장소 계정을 삭제 하나요? (포털, PowerShell 등)

중요 사항

* 일반적으로 저장소 서비스에서 가비지 수집을 수행 하기 위해 삭제 하는 시간부터 최대 15 일이 소요 되므로 저장소 계정 복구가 SLA를 사용 하 여 복구 되지 않을 수 있습니다.
* Microsoft 지원는 최상의 노력으로 컨테이너/계정을 복구 하려고 하며 복구를 보장할 수 없습니다.

> [!NOTE]
> 계정을 다시 만든 경우 복구가 실패할 수 있습니다. 계정을 이미 다시 만든 경우 복구를 시도 하기 전에 먼저 삭제 해야 합니다.
