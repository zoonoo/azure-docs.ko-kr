---
title: 파일 포함
description: 포함 파일
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 05/05/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 95c553d26a3e79b53106b933c629c5884c3e004c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84466881"
---
| 리소스 | Target | 하드 한도 |
|----------|--------------|------------|
| 지역당 스토리지 동기화 서비스 수 | 100 저장소 동기화 서비스 | Yes |
| 스토리지 동기화 서비스당 동기화 그룹 수 | 200 동기화 그룹 | Yes |
| 스토리지 동기화 서비스당 등록된 서버 | 서버 99대 | Yes |
| 동기화 그룹당 클라우드 끝점 | 1개 클라우드 엔드포인트 | Yes |
| 동기화 그룹당 서버 끝점 | 50개 서버 엔드포인트 | No |
| 서버당 서버 엔드포인트 수 | 30개 서버 엔드포인트 | Yes |
| 동기화 그룹당 파일 시스템 개체(디렉터리 및 파일) 수 | 1억 개체 | No |
| 디렉터리에 있는 파일 시스템 개체(디렉터리 및 파일)의 최대 수 | 500만 개체 | Yes |
| 최대 개체(디렉터리 및 파일) 보안 설명자 크기 | 64KiB | Yes |
| 파일 크기 | 100GiB | No |
| 계층화할 파일에 대한 최소 파일 크기 | V9: 파일 시스템 클러스터 크기 (이중 파일 시스템 클러스터 크기)를 기반으로 합니다. 예를 들어 파일 시스템 클러스터 크기가 4kb 이면 최소 파일 크기가 8kb 됩니다.<br> V8 및 이전: 64 KiB  | Yes |

> [!Note]  
> Azure File Sync 끝점은 Azure 파일 공유의 크기까지 확장할 수 있습니다. Azure 파일 공유 크기 제한에 도달 하면 동기화가 작동할 수 없습니다.
