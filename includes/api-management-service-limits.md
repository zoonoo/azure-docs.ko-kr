---
title: 포함 파일
description: 포함 파일
services: api-management
author: vladvino
ms.assetid: 1b813833-39c8-46be-8666-fd0960cfbf04
ms.service: api-management
ms.topic: include
ms.date: 01/10/2020
ms.author: vlvinogr
ms.custom: include file
ms.openlocfilehash: 2d4c8c2c831bd6ef16f60c34a6353f4a742798f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "76159715"
---
| 리소스 | 제한 |
| ---------------------------------------------------------------------- | -------------------------- |
| 최대 배율 단위 수 | 지역당 10개<sup>1</sup> |
| 캐시 크기 | 5 단위<sup>2당</sup> GiB |
| HTTP 기관당 동시 백 엔드 연결<sup>3</sup> | 단위 당 2,048<sup>4</sup> |
| 캐시된 최대 응답 크기 | 2 MiB |
| 최대 정책 문서 크기 | 256 KiB<sup>5</sup> |
| 서비스 인스턴스당 최대 사용자 지정 게이트웨이 도메인 수<sup>6</sup> | 20 |
| 서비스 인스턴스당 최대 CA 인증서 수 | 10 |
| 구독당 최대 서비스 인스턴스 수<sup>7</sup> | 20 |
| 서비스 인스턴스당 최대 구독 수<sup>7</sup> | 500 |
| 서비스 인스턴스당 최대 클라이언트 인증서 수<sup>7</sup> | 50 |
| 서비스 인스턴스당 최대 API 수<sup>7</sup> | 50 |
| 서비스 인스턴스당 최대 API 작업 수<sup>7</sup> | 1,000 |
| 최대 총 요청 기간<sup>7</sup> | 30초 |
| 최대 버퍼링된 페이로드 크기<sup>7</sup> | 2 MiB |
| 최대 요청 URL 크기<sup>8</sup> | 4096바이트 |

<sup>1개</sup> 크기 조정 제한은 가격 책정 계층에 따라 다릅니다. 가격 책정 계층 및 확장 한도를 보려면 [API 관리 가격 책정을](https://azure.microsoft.com/pricing/details/api-management/)참조하십시오.<br/>
<sup>2개</sup> 단위 캐시당 크기는 가격 책정 계층에 따라 다릅니다. 가격 책정 계층 및 확장 한도를 보려면 [API 관리 가격 책정을](https://azure.microsoft.com/pricing/details/api-management/)참조하십시오.<br/>
<sup>3개</sup> 연결은 백 엔드에서 명시적으로 닫히지 않는 한 풀리고 다시 사용됩니다.<br/>
<sup>4개</sup> 이 제한은 기본, 표준 및 프리미엄 계층의 단위당 입니다. 개발자 계층은 1,024로 제한됩니다. 이 제한은 소비 계층에는 적용되지 않습니다.<br/>
<sup>5개</sup> 이 제한은 기본, 표준 및 프리미엄 계층에 적용됩니다. 소비 계층에서 정책 문서 크기는 4KiB로 제한됩니다.<br/>
<sup>6개</sup> 이 리소스는 프리미엄 계층에서만 사용할 수 있습니다.<br/>
<sup>7명</sup> 이 리소스는 소비 계층에만 적용됩니다.<br/>
<sup>8개</sup> 소비 계층에만 적용됩니다. 최대 2048바이트의 긴 쿼리 문자열을 포함합니다.<br/>
