---
title: "포함 파일"
description: "포함 파일"
services: backup
author: markgalioto
ms.service: backup
ms.topic: include
ms.date: 2/7/2018
ms.author: trinadhk
ms.custom: include file
ms.openlocfilehash: 7ca5b34961b4d0e3d4fcecb8175e3e0901d7049d
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/09/2018
---
Azure Backup에는 다음과 같은 제한이 적용됩니다.

| 제한 식별자 | 기본 제한 |
| --- | --- |
| 각 자격 증명 모음에 대해 등록할 수 있는 서버/컴퓨터 수 |Windows Server/Client/SCDPM의 경우 50개  <br/> IaaS VM의 경우 200개 |
| Azure 자격 증명 모음 저장소에 저장 된 데이터에 대한 데이터 원본의 크기 |최대 54400GB<sup>1</sup> |
| 각 Azure 구독에 만들 수 있는 백업 자격 증명 모음의 개수 |지역당 Recovery Services 자격 증명 모음 25개 |
| 하루에 백업을 예약할 수 있는 횟수 |Windows 서버/클라이언트의 경우 하루에 3번  <br/> SCDPM에 대해 하루에 2번 <br/> IaaS VM의 경우 하루에 한 번 |
| 백업용 Azure 가상 머신에 연결된 데이터 디스크 |16 |
| 백업용 Azure 가상 머신에 연결된 개별 데이터 디스크의 크기| 1024GB <sup>2</sup>|

* <sup>1</sup>IaaS VM 백업에는 54400GB 제한이 적용되지 않습니다.
* <sup>2</sup> 최대 4TB의 디스크를 지원하기 위한 [비공개 미리 보기](https://gallery.technet.microsoft.com/Instant-recovery-point-and-25fe398a?redir=0)가 있습니다. 

