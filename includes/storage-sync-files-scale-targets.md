---
title: 포함 파일
description: 포함 파일
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 05/05/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: c04875f92ee58c24567fbb4d73d8d01697810f99
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72391711"
---
| 리소스 | 대상 파악 | 하드 한도 |
|----------|--------------|------------|
| 지역당 스토리지 동기화 서비스 수 | 20 개의 저장소 동기화 서비스 | yes |
| 스토리지 동기화 서비스당 동기화 그룹 수 | 100개 동기화 그룹 | yes |
| 스토리지 동기화 서비스당 등록된 서버 | 서버 99대 | yes |
| 동기화 그룹당 클라우드 끝점 | 1개 클라우드 엔드포인트 | yes |
| 동기화 그룹당 서버 끝점 | 50개 서버 엔드포인트 | 아닙니다. |
| 서버당 서버 엔드포인트 수 | 30개 서버 엔드포인트 | yes |
| 동기화 그룹당 파일 시스템 개체(디렉터리 및 파일) 수 | 5000만 개체 | 아닙니다. |
| 디렉터리에 있는 파일 시스템 개체(디렉터리 및 파일)의 최대 수 | 500만 개체 | yes |
| 최대 개체(디렉터리 및 파일) 보안 설명자 크기 | 64KiB | yes |
| 파일 크기 | 100GiB | 아닙니다. |
| 계층화할 파일에 대한 최소 파일 크기 | 64KiB | yes |
| 동시 동기화 세션 | V4 에이전트 이상: 사용 가능한 시스템 리소스에 따라 제한이 다릅니다. <BR> V3 에이전트: 프로세서 당 2 개의 활성 동기화 세션 또는 서버당 최대 8 개의 활성 동기화 세션. | yes

> [!Note]  
> Azure File Sync 끝점은 Azure 파일 공유의 크기까지 확장할 수 있습니다. Azure 파일 공유 크기 제한에 도달 하면 동기화가 작동할 수 없습니다.
