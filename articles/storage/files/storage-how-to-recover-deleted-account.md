---
title: 삭제된 스토리지 계정 복구 방법
description: 삭제된 저장소 계정을 복구하는 방법 알아보기
author: todmccoy
manager: dcscontentpm
ms.service: storage
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: rogarana
ms.subservice: files
services: storage
tags: ''
ms.openlocfilehash: 05465d4a03335ac607ba8981116c66fd6dac9416
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78252626"
---
# <a name="how-to-recover-a-deleted-storage-account"></a>삭제된 스토리지 계정 복구 방법

Azure Storage는 자동화된 복제본을 통해 데이터 복원력을 제공하지만 사용자 또는 응용 프로그램 코드가 실수로든 악의적이든 데이터를 손상시키지 않도록 방지하지는 않습니다. 응용 프로그램 또는 사용자 오류의 인스턴스 중에 데이터 충실도를 유지하려면 감사 로그가 있는 보조 저장소 위치에 데이터를 복사하는 것과 같은 고급 기술이 필요합니다.

다음 표에서는 복제 전략에 따라 저장소 계정 복구 범위에 대한 개요를 제공합니다.

| |LRS|ZRS|GRS|RA - GRS|
|---|---|---|---|---|
|저장소 계정 Azure 리소스 관리자|yes|yes|yes|yes|
|스토리지 계정 클래식|yes|yes|yes|yes|

다음 정보를 수집하고 Microsoft 지원에 지원 요청을 제출하십시오.

* 스토리지 계정 이름
* 삭제 날짜
* 저장소 계정 리전
* 저장소 계정은 어떻게 삭제되었습니까?
* 저장소 계정을 삭제한 방법은 무엇입니까? (포털, 파워쉘 등)

중요 사항

* 일반적으로 저장소 서비스가 가비지 수집을 수행하기 위해 삭제된 시점으로부터 최대 15일이 걸릴 수 있으므로 저장소 계정 복구가 SLA를 사용하여 복구되지 않을 수 있습니다.
* Microsoft 지원은 최상의 노력을 기울여 컨테이너/계정을 복구하려고 시도하며 복구를 보장할 수 없습니다.

> [!NOTE]
> 계정을 다시 만든 경우 복구가 성공하지 못할 수 있습니다. 이미 계정을 다시 만든 경우 복구를 시도하려면 먼저 삭제해야 합니다.
