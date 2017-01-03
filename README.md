# <a name="azure-technical-documentation-contributor-guide"></a>Azure 기술 설명서 참여자 가이드
[https://docs.microsoft.com/azure](https://docs.microsoft.com/azure)에 게시되는 Azure 기술 설명서에 대한 원본을 보관하는 GitHub 리포지토리입니다.

또한 이 리포지토리는 기술 설명서에 참가하는 데 도움이 되는 지침을 포함합니다. 참여자의 가이드에서 문서 목록은 [인덱스](contributor-guide/contributor-guide-index.md)를 참조하세요.

## <a name="contribute-to-azure-documentation"></a>Azure 설명서에 참가
Azure 설명서에 관심을 가져 주셔서 감사합니다.

* [참가 방법](#ways-to-contribute)
* [준수 사항](#code-of-conduct)
* [Azure 콘텐츠에 참가 방법](#about-your-contributions-to-azure-content)
* [리포지토리 조직](#repository-organization)
* [GitHub, Git 및 이 리포지토리 사용](#use-github-git-and-this-repository)
* [항목의 서식을 지정하는 Markdown을 사용하는 방법](#how-to-use-markdown-to-format-your-topic)
* [피드백, 의견 및 지원](./contributor-guide/feedback-and-comments.md)
* [추가 리소스](#more-resources)
* [모든 참여자 가이드 문서의 인덱스](contributor-guide/contributor-guide-index.md)(새 페이지를 엽니다.)

## <a name="ways-to-contribute"></a>참가 방법
[Azure 설명서](https://docs.microsoft.com/azure)에 대한 업데이트는 다음과 같이 제출할 수 있습니다.

* GitHub 사용자 인터페이스에서 기술 문서에 쉽게 참여할 수 있습니다. 이 리포지토리에서 문서를 찾거나 [https://docs.microsoft.com/azure](https://docs.microsoft.com/azure) 문서로 이동하여 해당 문서에 대한 GitHub 원본으로 이동하는 문서의 링크를 클릭합니다.
* 기존 문서의 많은 부분을 변경하거나 이미지를 추가 또는 변경하고 혹은 새 문서에 참가하는 경우, 이 리포지토리를 분기하고 Git Bash인 Markdown Pad를 설치하고 일부 git 명령을 알아보아야 합니다.

## <a name="code-of-conduct"></a>준수 사항
이 프로젝트에는 [Microsoft Open Source Code of Conduct](https://opensource.microsoft.com/codeofconduct/)(Microsoft 오픈 소스 준수 사항)이 적용됩니다. 자세한 내용은 [Code of Conduct FAQ](https://opensource.microsoft.com/codeofconduct/faq/)(준수 사항 FAQ)를 참조하거나 [opencode@microsoft.com](mailto:opencode@microsoft.com)에 추가 질문 또는 의견을 알려주세요.

## <a name="about-your-contributions-to-azure-content"></a>Azure 콘텐츠에 참가 방법
### <a name="minor-corrections"></a>사소한 수정
이 리포지토리의 설명서 및 코드 예제에 대해 제출한 사소한 수정 또는 확인 내용은 [docs.microsoft.com 사용 약관](https://docs.microsoft.com/legal/termsofuse)에서 다룹니다.

### <a name="larger-submissions"></a>대규모 제출
설명서 및 코드 예제에 대한 새롭거나 중요한 변경 내용을 통해 끌어오기 요청을 제출하면, Microsoft 직원이 아닌 경우 온라인 참가 사용권 계약(CLA)을 제출하도록 요청하는 GitHub의 설명이 전송됩니다. 온라인 양식 작성을 먼저 완료해야 Microsoft에서 끌어오기 요청을 수락할 수 있습니다.

## <a name="repository-organization"></a>리포지토리 조직
azure-docs 리포지토리의 콘텐츠는 https://docs.microsoft.com/azure에 있는 설명서의 구성을 따릅니다. 이 리포지토리에는 두 개의 루트 폴더가 포함됩니다.

### <a name="articles"></a>\articles
*\articles* 폴더는 *.md* 확장명의 markdown 파일 형식의 설명서를 포함합니다. 일반적으로 문서는 Azure 서비스별로 그룹화됩니다.

문서는 엄격한 파일 명명 지침을 따라야 합니다. 자세한 내용은 [당사의 파일 명명 지침](contributor-guide/file-names-and-locations.md)을 참조하세요.

*\articles* 폴더는 루트 디렉터리 문서 미디어 파일에 대한 *\media* 폴더를 포함하며 이 폴더 내에는 각 문서에 대한 이미지가 담긴 하위 폴더가 있습니다.  서비스 폴더는 각 서비스 폴더 내에서 문서에 대한 별도의 미디어 폴더를 포함합니다. 문서 이미지 폴더는 *.md* 파일 확장을 뺀 문서 파일과 이름이 동일합니다.

### <a name="includes"></a>\includes
하나 이상의 문서에 포함될 재사용 가능한 콘텐츠 섹션을 만들 수 있습니다. [기술 콘텐츠에 사용되는 사용자 지정 확장](contributor-guide/custom-markdown-extensions.md)을 참조하세요.

### <a name="markdown-templates"></a>\markdown templates
이 폴더는 문서에 필요한 기본 markdown 서식을 사용하는 표준 마크다운 템플릿이 포함됩니다.

### <a name="contributor-guide"></a>\contributor-guide
이 폴더는 참여자의 가이드 중 일부인 문서를 포함합니다.

## <a name="use-github-git-and-this-repository"></a>GitHub, Git 및 이 리포지토리 사용
참가 방법, 몇 가지 변경 사항을 위한 GitHub UI를 사용하는 방법 및 중요한 참가에 대한 리포지토리를 분기 및 복제하는 방법에 대한 내용은 [GitHub에서 작성을 위한 도구 설치 및 설정](contributor-guide/tools-and-setup.md)을 참조하세요.

GitBash를 설치하고 로컬에서 작동하도록 선택하면 새 로컬 작업 분기를 만들고 내용을 변경하며 주요 분기에 변경 내용을 다시 제출하는 단계는 [새 문서를 만들거나 기존 문서를 업데이트하기 위한 Git 명령](contributor-guide/git-commands-for-master.md)에 나열됩니다.

### <a name="branches"></a>분기
변경의 특정 범위를 대상으로 하는 로컬 작업 분기를 만드는 것이 좋습니다. 각 분기는 모두 단일 개념/문서로 제한하여 워크플로를 간소화하고 병합 충돌의 가능성을 줄여야 합니다.  다음은 새 분기에 대한 적절한 참가 범위입니다.

* 새 문서(및 연결된 이미지)
* 문서의 맞춤법 및 문법 편집
* 많은 문서 집합에 단일 서식 변경 내용을 적용(예: 새 저작권 바닥글)

## <a name="how-to-use-markdown-to-format-your-topic"></a>항목의 서식을 지정하는 Markdown을 사용하는 방법
이 리포지토리의 모든 문서는 GitHub 버전의 Markdown을 사용합니다.  다음은 리소스 목록입니다.

* [Markdown 기본 사항](https://help.github.com/articles/markdown-basics/)
* [인쇄 가능한 Markdown 치트 시트](./contributor-guide/media/documents/markdown-cheatsheet.pdf?raw=true)

## <a name="article-metadata"></a>문서 메타데이터
문서 메타데이터를 사용하면 작성자 특성, 참여자 특성, 이동 경로, 문서 설명 및 SEO 최적화뿐만 아니라 Microsoft가 콘텐츠의 성과를 평가하는 데 사용하는 보고 등의 특정 기능을 사용할 수 있습니다. 따라서 메타데이터는 중요합니다. [메타데이터가 제대로 수행되도록 하기 위한 지침은 다음과 같습니다](contributor-guide/article-metadata.md).

### <a name="labels"></a>레이블
자동화된 레이블은 당사가 끌어오기 요청 워크플로를 관리하고 여러분에게 끌어오기 요청에 대한 진행 상황을 알리는 데 도움을 주기 위해 끌어오기 요청에 할당됩니다.

* 참가 사용권 계약 관련
  * cla-not-required: 변경 내용이 비교적 사소하며 CLA 서명이 필요하지 않습니다.
  * cla-required: 변경 범위가 비교적 크며 CLA 서명이 필요합니다.
  * cla-signed: 참여자가 CLA에 서명했으므로 끌어오기 요청이 검토 단계로 진행될 수 있습니다.
* Pillar 레이블: PnP, 최신 앱 및 TDC와 같은 레이블은 끌어오기 요청을 검토해야 하는 내부 조직에서 끌어오기 요청을 분류하는 데 도움을 줍니다.
* 작성자에게 변경 내용 전송: 작성자에게 보류 중인 끌어오기 요청에 대해 알렸습니다.

## <a name="more-resources"></a>추가 리소스
모든 지침 항목은 [참여자 가이드의 인덱스](contributor-guide/contributor-guide-index.md)를 참조하세요.



<!--HONumber=Dec16_HO3-->


