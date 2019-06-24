---
title: 포함 파일
description: 포함 파일
services: storage
author: wmgries
ms.service: storage
ms.topic: include
ms.date: 05/05/2019
ms.author: wgries
ms.custom: include file
ms.openlocfilehash: 2614c9290bf31813d59ee753a31622bccf0682b8
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67182410"
---
| Resource | 대상 | 하드 한도 |
|----------|--------------|------------|
| 지역당 스토리지 동기화 서비스 수 | 저장소 동기화 서비스는 20 | 예 |
| 저장소 동기화 서비스당 동기화 그룹 수 | 100개 동기화 그룹 | 예 |
| 저장소 동기화 서비스당 등록된 서버 | 서버 99대 | 예 |
| 동기화 그룹당 클라우드 끝점 | 1개 클라우드 엔드포인트 | 예 |
| 동기화 그룹당 서버 끝점 | 50개 서버 엔드포인트 | 아닙니다. |
| 서버당 서버 엔드포인트 수 | 30개 서버 엔드포인트 | 예 |
| 동기화 그룹당 파일 시스템 개체(디렉터리 및 파일) 수 | 2500만 개 개체 | 아닙니다. |
| 디렉터리에 있는 파일 시스템 개체(디렉터리 및 파일)의 최대 수 | 1 백만 개체 | 예 |
| 최대 개체(디렉터리 및 파일) 보안 설명자 크기 | 64KiB | 예 |
| 파일 크기 | 100GiB | 아닙니다. |
| 계층화할 파일에 대한 최소 파일 크기 | 64KiB | 예 |
| 동시 동기화 세션 | V4 에이전트 이상: 제한을 사용 가능한 시스템 리소스에 따라 다릅니다. <BR> V3 에이전트: 프로세서 또는 서버 단위 8 개의 활성 동기화 세션의 최대 두 개의 활성 동기화 세션입니다. | 예

> [!Note]  
> Azure File Sync 엔드포인트는 Azure 파일 공유의 크기 확장할 수 있습니다. Azure 파일 공유 크기 제한에 도달 하는 경우 동기화 적용할 수 없습니다.