---
title: 파일 포함
description: 포함 파일
services: api-management
author: vladvino
ms.assetid: 1b813833-39c8-46be-8666-fd0960cfbf04
ms.service: api-management
ms.topic: include
ms.date: 04/14/2020
ms.author: vlvinogr
ms.custom: include file
ms.openlocfilehash: 072f13f5a0884cf95fe760e17ff0d770111f4da0
ms.sourcegitcommit: 67bddb15f90fb7e845ca739d16ad568cbc368c06
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82204665"
---
| 리소스 | 제한 |
| ---------------------------------------------------------------------- | -------------------------- |
| 최대 배율 단위 수 | 지역당 10개<sup>1</sup> |
| 캐시 크기 | 단가<sup>2</sup> 당 5 GiB |
| HTTP 권한 당 동시 백 엔드 연결<sup>3</sup> | 단위당 2048<sup>4</sup> |
| 캐시된 최대 응답 크기 | 2 MiB |
| 최대 정책 문서 크기 | 256 KiB<sup>5</sup> |
| 서비스 인스턴스당 최대 사용자 지정 게이트웨이 도메인 수<sup>6</sup> | 20 |
| 서비스 인스턴스당 최대 CA 인증서 수<sup>7</sup> | 10 |
| 구독 당 최대 서비스 인스턴스 수<sup>8</sup> | 20 |
| 서비스 인스턴스당 최대 구독 수<sup>8</sup> | 500 |
| 서비스 인스턴스당 최대 클라이언트 인증서 수<sup>8</sup> | 50 |
| 서비스 인스턴스당 최대 Api 수<sup>8</sup> | 50 |
| 서비스 인스턴스당 최대 API 작업 수<sup>8</sup> | 1,000 |
| 최대 총 요청 기간<sup>8</sup> | 30초 |
| 버퍼링 된 최대 페이로드 크기<sup>8</sup> | 2 MiB |
| 최대 요청 URL 크기<sup>9</sup> | 4096 바이트 |
| 자체 호스팅 게이트웨이의 최대 수<sup>10</sup> | 25 |

<sup>1</sup> 크기 조정 제한은 가격 책정 계층에 따라 달라 집니다. 가격 책정 계층 및 해당 크기 조정 제한에 대 한 자세한 내용은 [API Management 가격 책정](https://azure.microsoft.com/pricing/details/api-management/)을 참조 하세요.<br/>
<sup>2</sup> 단위 캐시 크기 당은 가격 책정 계층에 따라 달라 집니다. 가격 책정 계층 및 해당 크기 제한을 확인 하려면 [API Management 가격 책정](https://azure.microsoft.com/pricing/details/api-management/)을 참조 하세요.<br/>
<sup>3</sup> 백 엔드에서 명시적으로 닫히지 않는 한 연결은 풀링된 후 다시 사용 됩니다.<br/>
<sup>4</sup> 이 제한은 기본, 표준 및 프리미엄 계층의 단위당입니다. 개발자 계층은 1024 개로 제한 됩니다. 이 제한은 소비 계층에 적용 되지 않습니다.<br/>
<sup>5</sup> 이 제한은 Basic, Standard 및 Premium 계층에 적용 됩니다. 소비 계층에서 정책 문서 크기는 4 KiB으로 제한 됩니다.<br/>
<sup>6</sup> 여러 사용자 지정 도메인은 개발자 및 프리미엄 계층 에서만 지원 됩니다.<br/>
<sup>7</sup> CA 인증서는 소비 계층에서 지원 되지 않습니다.<br/>
<sup>8</sup> 이 리소스는 소비 계층에만 적용 됩니다.<br/>
<sup>9</sup> 소비 계층에만 적용 됩니다. 에는 최대 2048 바이트 길이의 쿼리 문자열이 포함 됩니다.<br/>
<sup>10</sup> 자체 호스팅 게이트웨이는 개발자 및 프리미엄 계층 에서만 지원 됩니다. 이 제한은 [자체 호스팅 게이트웨이 리소스](https://docs.microsoft.com/rest/api/apimanagement/2019-12-01/gateway)의 수에 적용 됩니다. 이 제한을 높이려면 [지원](https://azure.microsoft.com/support/options/)담당자에 게 문의 하세요. 자체 호스팅 게이트웨이 리소스와 연결 된 노드 (또는 복제본) 수는 프리미엄 계층에서 무제한 이며 개발자 계층의 단일 노드에 있습니다.
