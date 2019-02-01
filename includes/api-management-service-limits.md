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
ms.openlocfilehash: 646ebc79e3615b289b60e47c0a38595e7892d8cc
ms.sourcegitcommit: 58dc0d48ab4403eb64201ff231af3ddfa8412331
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/26/2019
ms.locfileid: "55084831"
---
| 리소스 | 제한 |
| --- | --- |
| 최대 배율 단위 수 | 지역당 10개<sup>1</sup> |
| 캐시 크기 | 단위당 5GB<sup>2</sup> |
| HTTP 기관당 동시 백 엔드 연결 수<sup>3</sup> | 단위당 2048<sup>4</sup> |
| 캐시된 최대 응답 크기 | 2MB |
| 최대 정책 문서 크기 | 256KB<sup>5</sup> | 
| 서비스 인스턴스당 최대 사용자 지정 게이트웨이 도메인 수<sup>6</sup> | 20 | 
| 구독당 최대 서비스 인스턴스 수<sup>7</sup> | 20 | 
| 서비스 인스턴스당 최대 구독 수<sup>7</sup> | 500 |
| 서비스 인스턴스당 최대 클라이언트 인증서 수<sup>7</sup> | 50 | 
| 서비스 인스턴스당 최대 API 수<sup>7</sup> | 50 | 
| 서비스 인스턴스당 최대 API 작업 수<sup>7</sup> | 1000 | 
| 최대 총 요청 기간<sup>7</sup> | 30초 | 
| 최대 버퍼링된 페이로드 크기<sup>7</sup> | 2MB | 


<sup>1</sup> 크기 조정 제한은 가격 책정 계층에 따라 다릅니다. 가격 책정 계층 및 해당 크기 조정 제한을 확인하려면 [API Management 가격 책정](https://azure.microsoft.com/pricing/details/api-management/)을 참조하세요.<br/>
<sup>2</sup> 단위당 캐시 크기는 가격 책정 계층에 따라 다릅니다. 가격 책정 계층 및 해당 크기 조정 제한을 확인하려면 [API Management 가격 책정](https://azure.microsoft.com/pricing/details/api-management/)을 참조하세요.<br/>
<sup>3</sup> 백 엔드에서 명시적으로 닫히지 않는 한 연결은 풀링되고 다시 사용됩니다.<br/>
<sup>4</sup> Basic, Standard 및 Premium 계층 단위당입니다. Developer 계층은 1024로 제한됩니다. Consumption 계층에는 적용되지 않습니다.<br/> 
<sup>5</sup> Basic, Standard 및 Premium 계층에 적용됩니다. Consumption 계층 정책 문서의 크기가 4KB로 제한됩니다.<br/>
<sup>6</sup> Premium 계층에서만 사용할 수 있습니다.<br/>
<sup>7</sup> Consumption 계층에만 적용됩니다.<br/>



