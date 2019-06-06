---
title: 문서 형식과 명명 규칙 - Custom Translator
titleSuffix: Azure Cognitive Services
description: Custom Translator의 문서 형식과 명명 규칙에 대해 설명하는 가이드입니다. 이 개념은 문서를 효율적으로 관리하고 이름 충돌을 피하는 데 도움이 됩니다.
author: rajdeep-in
manager: christw
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: v-pawal
ms.topic: conceptual
ms.openlocfilehash: 29b74aeaaae0bcfd5f6ae4a3c38cc00176292899
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66386923"
---
# <a name="document-formats-and-naming-convention-guidance"></a>문서 형식 및 명명 규칙 가이드

사용자 지정 번역에 사용하는 파일은 길이가 최소 **4**자 이상이어야 합니다.

이 표에는 번역 시스템을 빌드할 때 사용할 수 있는 지원되는 파일 형식이 모두 나열되어 있습니다.

| 형식            | 확장   | 설명                                                                                                                                                                                                                                                                    |
|-------------------|--------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| XLIFF             | .XLF, .XLIFF | 번역 메모리 시스템에서 내보낸 병렬 문서 형식입니다. 사용되는 언어는 파일 안에 정의되어 있습니다.                                                                                                                                                              |
| TMX               | .TMX         | 번역 메모리 시스템에서 내보낸 병렬 문서 형식입니다. 사용되는 언어는 파일 안에 정의되어 있습니다.                                                                                                                                                              |
| ZIP               | .ZIP         | ZIP은 아카이브 파일 형식입니다.                                                                                                                                                                                                        |
| Locstudio         | .LCL         | 병렬 문서용 Microsoft 형식입니다.                                                                                                                                                                                                                                      |
| Microsoft Word    | .DOCX        | Microsoft Word 문서                                                                                                                                                                                                                                                        |
| Adobe Acrobat     | .PDF         | Adobe Acrobat Portable 문서                                                                                                                                                                                                                                                |
| HTML              | .HTML, .HTM  | HTML 문서                                                                                                                                                                                                                                                                  |
| 텍스트 파일         | .TXT         | Utf-16 또는 utf-8 인코딩된 텍스트 파일입니다. 파일 이름에 일본어 문자를 사용할 수 없습니다.                                                                                                                                                                                        |
| 정렬된 텍스트 파일 | .ALIGN       | 확장자 `.ALIGN`은 문서 쌍의 문장들이 완벽히 정렬된 경우 사용할 수 있는 특수 확장자입니다. Custom Translator에 `.ALIGN` 파일을 입력하면 문장 정렬이 수행되지 않습니다. |
| Excel 파일        | .XLSX        | Excel 파일(2013 이상). 스프레드시트의 첫 번째 줄/행이 언어 코드여야 합니다.                                                                                                                                                                                                                                                      |

## <a name="dictionary-formats"></a>사전 형식

사전의 경우, Custom Translator는 훈련 세트에 대해 지원되는 모든 파일 형식을 지원합니다. Excel 사전을 사용하는 경우에는 스프레드시트의 첫 번째 줄/행이 언어 코드여야 합니다.

## <a name="zip-file-formats"></a>Zip 파일 형식

문서를 하나의 zip 파일로 묶어서 업로드할 수 있습니다. Custom Translator는 zip 파일 형식 ZIP, GZ, TGZ를 지원합니다.

TXT, HTML, HTM, PDF, DOCX, ALIGN 확장명의 zip 파일에서 각 문서는 이 명명 규칙을 따라야 합니다.

{document name}\_{language code}: 여기서 {document name}은 문서 이름이고 {language code}는 문서에 해당 언어의 문장이 포함되어 있음을 나타내는 ISO LanguageID(2자)입니다. 언어 코드 앞에는 밑줄(_)이 와야 합니다.

예를 들어, 영어-스페인어 시스템을 위해 하나의 zip 파일로 2개의 병렬 문서를 업로드하려면 파일 이름이 각각 “data_en”과 “data_es”가 되어야 합니다.

번역 메모리 파일(TMX, XLF, XLIFF, LCL, XLSX)은 특정 언어 명명 규칙을 따르지 않아도 됩니다.  

## <a name="next-steps"></a>다음 단계

- [프로젝트](workspace-and-project.md#what-is-a-custom-translator-project)를 만들고 관리하는 방법을 알아봅니다.
