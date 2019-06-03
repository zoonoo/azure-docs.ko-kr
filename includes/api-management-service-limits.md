---
title: 포함 파일
description: 포함 파일
services: api-management
author: vladvino
ms.assetid: 1b813833-39c8-46be-8666-fd0960cfbf04
ms.service: api-management
ms.topic: include
ms.date: 03/22/2018
ms.author: vlvinogr
ms.custom: include file
ms.openlocfilehash: fc945a7e9389c8aec48a6a1dba969fbf92002d3a
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66238637"
---
| Resource | 제한 |
| --- | --- |
| 최대 배율 단위 수 | 지역당 10개<sup>1</sup> |
| 캐시 크기 | 단위당 5GB<sup>2</sup> |
| 백 엔드에 대 한 동시 연결<sup>3</sup> HTTP 기관 당 | 단위당 2,048<sup>4</sup> |
| 캐시된 최대 응답 크기 | 2MB |
| 최대 정책 문서 크기 | 256KB<sup>5</sup> | 
| 서비스 인스턴스당 최대 사용자 지정 게이트웨이 도메인 수<sup>6</sup> | 20 |
| 서비스 인스턴스당 CA 인증서의 최대 수 | 10 | 
| 구독당 최대 서비스 인스턴스 수<sup>7</sup> | 20 | 
| 서비스 인스턴스당 최대 구독 수<sup>7</sup> | 500 |
| 서비스 인스턴스당 최대 클라이언트 인증서 수<sup>7</sup> | 50 | 
| 서비스 인스턴스당 최대 API 수<sup>7</sup> | 50 | 
| 서비스 인스턴스당 최대 API 작업 수<sup>7</sup> | 1,000 | 
| 최대 총 요청 기간<sup>7</sup> | 30초 | 
| 최대 버퍼링된 페이로드 크기<sup>7</sup> | 2MB | 


<sup>1</sup>가격 책정 계층에 따라 크기 조정 제한 합니다. 가격 책정 계층 및 해당 크기 조정 제한을 참조 하세요 [API Management 가격 책정](https://azure.microsoft.com/pricing/details/api-management/)합니다.<br/>
<sup>2</sup>당 단위 캐시 크기에 따라 달라 집니다 가격 책정 계층입니다. 가격 책정 계층 및 해당 크기 조정 제한을 참조 하세요 [API Management 가격 책정](https://azure.microsoft.com/pricing/details/api-management/)합니다.<br/>
<sup>3</sup>연결이 풀링되 고 백 엔드에 의해 명시적으로 닫아야만 닫힙니다 하지 않는 한 다시 사용 됩니다.<br/>
<sup>4</sup>이 한도 Basic, Standard 및 Premium 계층 단위당입니다. 개발자 계층은 1,024 개로 제한 됩니다. 이 제한은 사용 계층에 적용 되지 않습니다.<br/> 
<sup>5</sup>이 제한은 Basic, Standard 및 Premium 계층에 적용 됩니다. 소비 계층에서는 정책 문서 크기는 4KB로 제한 합니다.<br/>
<sup>6</sup>이 리소스는 프리미엄 계층 에서만 사용할 수 있습니다.<br/>
<sup>7</sup>이 리소스는 소비 계층만에 적용 됩니다.<br/>



