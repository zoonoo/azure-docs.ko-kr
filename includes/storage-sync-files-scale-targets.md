---
title: 포함 파일
description: 포함 파일
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 10/13/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 29518fb971649087d8a5afa39d69c7fc2c014f98
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2020
ms.locfileid: "93331105"
---
| 리소스 | 대상 | 하드 한도 |
|----------|--------------|------------|
| 지역당 스토리지 동기화 서비스 수 | 100개 스토리지 동기화 서비스 | 예 |
| 스토리지 동기화 서비스당 동기화 그룹 수 | 200개 동기화 그룹 | 예 |
| 스토리지 동기화 서비스당 등록된 서버 | 서버 99대 | 예 |
| 동기화 그룹당 클라우드 엔드포인트 수 | 1개 클라우드 엔드포인트 | 예 |
| 동기화 그룹당 서버 엔드포인트 수 | 100개 서버 엔드포인트 | 예 |
| 서버당 서버 엔드포인트 수 | 30개 서버 엔드포인트 | 예 |
| 동기화 그룹당 파일 시스템 개체(디렉터리 및 파일) 수 | 1억 개 개체 | 아니요 |
| 디렉터리에 있는 파일 시스템 개체(디렉터리 및 파일)의 최대 수 | 500만 개 개체 | 예 |
| 최대 개체(디렉터리 및 파일) 보안 설명자 크기 | 64KiB | 예 |
| 파일 크기 | 100GiB | 아니요 |
| 계층화할 파일에 대한 최소 파일 크기 | V9 이상: 파일 시스템 클러스터 크기를 기준으로 합니다(이중 파일 시스템 클러스터 크기). 예를 들어 파일 시스템 클러스터 크기가 4kb이면 최소 파일 크기는 8kb입니다.<br> V8 이하: 64KiB  | 예 |

> [!Note]  
> Azure 파일 동기화 엔드포인트는 Azure 파일 공유의 크기로 확장할 수 있습니다. Azure 파일 공유 크기 제한에 도달하면 동기화가 작동할 수 없습니다.
