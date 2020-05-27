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
ms.openlocfilehash: 6a053b94813145f9ccd69158d18edb728d5dad61
ms.sourcegitcommit: 6a4fbc5ccf7cca9486fe881c069c321017628f20
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/27/2020
ms.locfileid: "74796030"
---
| 리소스 | 대상 | 하드 한도 |
|----------|--------------|------------|
| 지역당 스토리지 동기화 서비스 수 | 20 개의 저장소 동기화 서비스 | 예 |
| 스토리지 동기화 서비스당 동기화 그룹 수 | 100개 동기화 그룹 | 예 |
| 스토리지 동기화 서비스당 등록된 서버 | 서버 99대 | 예 |
| 동기화 그룹당 클라우드 끝점 | 1개 클라우드 엔드포인트 | 예 |
| 동기화 그룹당 서버 끝점 | 50개 서버 엔드포인트 | 아니요 |
| 서버당 서버 엔드포인트 수 | 30개 서버 엔드포인트 | 예 |
| 동기화 그룹당 파일 시스템 개체(디렉터리 및 파일) 수 | 1억 개체 | 아니요 |
| 디렉터리에 있는 파일 시스템 개체(디렉터리 및 파일)의 최대 수 | 500만 개체 | 예 |
| 최대 개체(디렉터리 및 파일) 보안 설명자 크기 | 64KiB | 예 |
| 파일 크기 | 100GiB | 아니요 |
| 계층화할 파일에 대한 최소 파일 크기 | V9: 파일 시스템 클러스터 크기 (이중 파일 시스템 클러스터 크기)를 기반으로 합니다. 예를 들어 파일 시스템 클러스터 크기가 4kb 이면 최소 파일 크기가 8kb 됩니다.<br> V8 및 이전: 64 KiB  | 예 |

> [!Note]  
> Azure File Sync 끝점은 Azure 파일 공유의 크기까지 확장할 수 있습니다. Azure 파일 공유 크기 제한에 도달 하면 동기화가 작동할 수 없습니다.
