<properties title="Build a web application with Python and Flask (MVC) using DocumentDB" pageTitle="Build a web app with Python and Flask using DocumentDB | Azure" description="Learn how to use DocumentDB to store and access data from an Python and Flask (MVC) web application hosted on Azure."  metaKeywords="NoSQL, DocumentDB,  database, document-orientated database, JSON, account" services="documentdb"  solutions="data-management" documentationCenter=""  authors="hawong" manager="jhubbard" editor="cgronlun" videoId="" scriptId="" />

<tags ms.service="documentdb" ms.workload="data-management" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/20/2014" ms.author="hawong"></tags>

# <a name="_Toc395888515"></a><a name="_Toc395809324">DocumentDB를 사용하여 Python 및 Flask(MVC)로 웹 응용 프로그램 작성</a>

<a name="_Toc395809324">

# <a name="_Toc395888516"></a><a name="_Toc395809325"></a><a name="_Toc389865467"></a><a name="_Toc389828008">개요</a>

## <a name="_Toc395888517"></a><a name="_Toc395809326">시나리오</a>

고객이 Azure DocumentDB를 효율적으로 활용하여
JSON 문서를 저장 및 쿼리할 수 있는 방법을 강조하기 위해 이 문서에서는
Azure DocumentDB를 사용하여 투표 웹 응용 프로그램을 작성하는
종단 간 연습을 제공합니다.

이 연습에서는 Azure가 제공하는 DocumentDB 서비스를 사용하여
Azure에 호스트된 Python 웹 응용 프로그램에서 데이터를 저장하고 액세스하는
방법을 보여 주며 이전에 Python 및 Azure 웹 사이트를 사용한 경험이
있다고 가정합니다.

다음 내용을 배웁니다.

1. DocumentDB 계정 만들기 및 프로비전

2. Python MVC 응용 프로그램 만들기

3. 웹 응용 프로그램에서 Azure DocumentDB에 연결 및 사용

4. Azure 웹 사이트에 웹 응용 프로그램 배포

이 연습을 따라 설문 조사에 투표할 수 있는 간단한
투표 응용 프로그램을 작성합니다.

![대체 텍스트][]

<a name="_Toc395888520"></a><a name="_Toc395809329">필수 조건</a>

이 문서의 지침을 따르기 전에 다음이 설치되어 있는지
확인해야 합니다.

Visual Studio 2013(또는 [Visual Studio Express][]
(무료 버전))

Python Tools for Visual Studio([여기][]서 사용 가능)

Azure SDK for Visual Studio 2013 버전 2.4 이상(
[여기][1]서 사용 가능)

Azure Cross-platform Command Line Tools([Microsoft
웹 플랫폼 설치 관리자를 통해 사용 가능][])

# <a name="_Toc395888519"></a><a name="_Toc395809328">DocumentDB 데이터베이스 계정 만들기</a>

Azure에서 DocumentDB 데이터베이스 계정을 프로비전하려면 Azure
관리 포털을 열고 홈페이지에서 Azure 갤러리 타일을 클릭하거나
화면 왼쪽 아래에 있는 "+"를 클릭합니다.

![대체 텍스트][2]

Azure 갤러리가 열리며, 여기서 사용 가능한 많은 Azure 서비스를
선택할 수 있습니다. 갤러리의 범주 목록에서 "데이터, 저장소 및
백업"을 선택합니다.

![대체 텍스트][3]

여기서 Azure DocumentDB에 대한 옵션을 선택합니다.

![대체 텍스트][4]

그런 다음 화면 맨 아래에서 "만들기"를 선택합니다.

![대체 텍스트][5]

"새 DocumentDB" 블레이드가 열립니다. 여기서 새 계정의
이름, 지역, 배율, 리소스 그룹 및 기타 설정을
지정할 수 있습니다.

![대체 텍스트][6]

계정의 값을 제공한 후 "만들기"를 클릭하면 프로비저닝 프로세스에서
데이터베이스 계정을 만들기 시작합니다.
프로비저닝 프로세스가 완료되면 포털의 알림 영역에
알림이 표시되며 시작 화면의 타일(만들도록
선택한 경우)이 완료된 작업을 표시하도록
변경됩니다.

![대체 텍스트][7]

프로비저닝이 완료된 후 시작 화면에서 DocumentDB 타일을
클릭하면 새로 만든 이 DocumentDB 계정의 기본 블레이드가
표시됩니다.

![대체 텍스트][8]
![대체 텍스트][9]

"키" 단추를 사용하여 끝점 URL과 기본 키에 액세스한 후
다음에 만들 웹 응용 프로그램에서 해당 값을 사용하므로
클립보드로 복사하여 유용하게 유지합니다.

</a>

# <a name="_Toc395888520"></a><a name="_Toc395809329">새 Python Flask 웹 응용 프로그램 만들기</a>

Visual Studio, 파일 -\> 새 프로젝트 -\> Python -\> 이름이 **tutorial**인
Flask 웹 프로젝트를 엽니다. 외부 패키지를 설치할지 여부를
묻습니다. 가상 환경에 설치를 클릭한 다음
만들기를 클릭합니다. 프로젝트에 필요한 Python 가상
환경이 설정됩니다.

Flask를 처음 사용하는 경우 Flask는 Python에서 웹 응용 프로그램을
더 빨리 작성하는 데 도움이 되는 웹 프레임워크입니다. [Flask 자습서에 액세스하려면 여기를 클릭하세요][].

![대체 텍스트][10]

# <a name="_Toc395888520"></a><a name="_Toc395809329">프로젝트에 Flask 패키지 추가</a>

프로젝트가 설정된 후 프로젝트에 필요한 특정 Flask
패키지를 추가해야 합니다(예: 폼). **env**를 마우스 오른쪽 단추로 클릭하고
**다음 Python 패키지를 설치합니다(이 순서를 따르는
것이 중요함)**.

    flask==0.9
    flask-login
    flask-openid
    flask-mail==0.7.6
    sqlalchemy==0.7.9
    flask-sqlalchemy==0.16
    sqlalchemy-migrate==0.7.2
    flask-whooshalchemy==0.55a
    flask-wtf==0.8.4
    pytz==2013b
    flask-babel==0.8
    flup

![대체 텍스트][11]

**참고:** 출력 창에 실패가 표시되는 경우가 드물게 발생합니다. 이 경우
오류가 정리와 관련이 있는지 확인하세요. 정리에 실패하지만
설치는 성공하는 경우가 있습니다(이를 확인하려면
출력 창에서 위로 스크롤).
<a name="verify-the-virtual-environment"></a> 이 경우
계속해도 됩니다.

</h1>
# <a name="_Toc395888522"></a><a name="_Toc395809331">가상 환경 확인</a>

모두 올바르게 설치되었는지 확인해 보겠습니다. **F5** 키를
클릭하여 웹 사이트를 시작합니다. Flask 개발 서버가 실행되고
웹 브라우저가 시작됩니다. 다음 페이지가 표시되어야 합니다.

![대체 텍스트][12]

# <a name="_Toc395888523"></a><a name="_Toc395809332">프로젝트에 DocumentDB 추가</a>

DocumentDB Python SDK는 PyPi에 호스트되며 pip로
설치할 수 있습니다.

솔루션 탐색기에서 Python Environments 노드를 확장하고 해당 환경을 마우스 오른쪽 단추로
클릭한 다음 "Install Python Package…"를 선택합니다.

![대체 텍스트][13]

PyPi 패키지의 이름인 "--pre pydocumentdb"를 입력합니다. 설치할
버전을 제어하려는 경우 선택적으로 알려진 버전을 제공할 수
있습니다. 버전을 생략하면 안정적인 최신 버전이
설치됩니다. 전체 이름 "--pre pydocumentdb"를
입력해야 합니다.

![대체 텍스트][14]

pydocumentdb 패키지가 사용자 환경에 다운로드되어
설치되며, 완료되면 pydocumentdb가 사용자 환경 아래에
모듈로 표시됩니다.

</h1>
# <a name="_Toc395888524"></a><a name="_Toc395809333">데이터베이스, 컬렉션 및 문서 정의 만들기</a>

솔루션 탐색기에서 tutorial이라는 폴더에 python 파일 **forms.py**를
추가합니다. forms.py에 다음 코드를 추가합니다.
폴링 응용 프로그램을 만들려고 합니다. 이 작업을 위해
사용자 입력에 따라 토글되는 부울 필드 세 개를 만듭니다.

</h1>

    #forms.py
    from flask.ext.wtf import Form
    from wtforms import TextField, BooleanField
    from wtforms.validators import Required
    import pydocumentdb.documents as documents
    import pydocumentdb.document_client as document_client
    import pydocumentdb.errors as errors
    class LoginForm(Form):
        openid = TextField('openid')
        remember_me = BooleanField('remember_me', default = False)
        remember_me1 = BooleanField('remember_me1', default = False)
        remember_me2 = BooleanField('remember_me2', default = False)

<a name="_Toc395888520"></a>
============================

### <a name="_Toc395809338">데이터베이스, 컬렉션 및 문서 만들기</a>

#참고: 인증 자격 증명을 앱 대신 구성 파일에 유지하는 것이
더 안전합니다. 여기서는 편의상 소스 코드에
배치합니다. views.py에 새 함수를 만들고 이름을
create로 지정합니다. **views.py**에 다음을 추가합니다. 기존 코드를
삭제하지 마세요.

</h1>

    @app.route('/create')
    def create():
        """Renders the contact page."""
        host = 'https://meetdemo.documents.azure.com:443/'
        masterKey = '7xPDjHxJSuAUPI2BEWXF2VNVO6c3MN4q+941NAQwPphIqOGW/gE+pB1CQNp4K2F9/4T1bTl040t6JDeyCBmj3A=='
        client = document_client.DocumentClient(host, {'masterKey': masterKey})
        databases = client.ReadDatabases().ToArray()
        #delete any existing databases for simplicity
        for database in databases:
            client.DeleteDatabase(database['_self'])
        #create database
        db = client.CreateDatabase({ 'id': 'sample database' })
        # create collection
        collection = client.CreateCollection(db['_self'],{ 'id': 'sample collection' })
        # create document
        document = client.CreateDocument(collection['_self'],
        { 'id': 'sample document',
        'Web Site': '0 votes',
        'Cloud Service': '0 votes',
        'Virtual Machine': '0 votes',
        'name': 'sample document' })
        return render_template('create.html',title='Create Page',year=datetime.now().year,
        message='You just created a new database - sample database a collection - sample collection and a document - sample document that has your votes. Your old votes have been deleted')

<a name="_Toc395888529"></a><a name="_Toc395809338">Accept users vote and update the document</a>
=================================================================================================

### <a name="_Toc395809338">필수 가져오기 추가

# <a name="_Toc395888529"></a>

**views.py**의 맨 위에 다음 import 문을 추가합니다. 이러한 문은
DocumentDB의 PythonSDK 및 Flask 패키지를 가져옵니다.

    from wtforms import Form, BooleanField, TextField, PasswordField, validators
    from forms import LoginForm
    import pydocumentdb.documents as documents
    import pydocumentdb.document_client as document_client
    import pydocumentdb.errors as errors

### <a name="_Toc395809338">데이터베이스, 컬렉션 및 문서 읽기</a>

**views.py**에 다음 코드를 추가합니다. 이 코드는 폼 설정과
데이터베이스, 컬렉션 및 문서 읽기를 처리합니다. views.py의
기존 코드를 추가하지 마세요. 이 코드를 끝에 추가하면 됩니다.

    @app.route('/vote', methods=['GET', 'POST'])
    def vote(): 
        form = LoginForm()
        replaced_document ={}
        if form.validate_on_submit(): # is user submitted vote  
            host = 'https://meetdemo.documents.azure.com:443/'
            masterKey = '7xPDjHxJSuAUPI2BEWXF2VNVO6c3MN4q+941NAQwPphIqOGW/gE+pB1CQNp4K2F9/4T1bTl040t6JDeyCBmj3A=='
            client = document_client.DocumentClient(host, {'masterKey': masterKey})
            databases = client.ReadDatabases().ToArray()   #Read database
            db =databases[0] #For simplicity we are assuming there is only one database
            collections = client.ReadCollections(db['_self']).ToArray() #Read collection
            coll =collections[0] #For simplicity we are assuming there is only one collection
            documents = client.ReadDocuments(coll['_self']).ToArray() #Read document
            doc = documents[0]   #For simplicity we are assuming there is only document

### <a name="_Toc395809338">투표 등록 및 문서 수정</a>

            replaced_document = doc
            if form.remember_me.data:
                print 'choice 1'             
                setvar = doc['Web Site']
                finalvar = setvar.split();
                votes = int(finalvar[0])
                votes = votes+1
                doc['Web Site'] = str(votes)+' '+finalvar[1]
                replaced_document = client.ReplaceDocument(doc['_self'],
                                                       doc)
                print replaced_document
            elif form.remember_me1.data:
                print 'choice 2'             
                setvar = doc['Cloud Service']
                finalvar = setvar.split();
                votes = int(finalvar[0])
                votes = votes+1
                doc['Cloud Service'] = str(votes)+' '+finalvar[1]
                replaced_document = client.ReplaceDocument(doc['_self'],
                                                       doc)
            else:
                print 'choice 2'             
                setvar = doc['Virtual Machine']
                finalvar = setvar.split();
                votes = int(finalvar[0])
                votes = votes+1
                doc['Virtual Machine'] = str(votes)+' '+finalvar[1]
                replaced_document = client.ReplaceDocument(doc['_self'],
                                                       doc)

### <a name="_Toc395809338">결과 표시</a>

    return render_template('results.html', 
            title = 'Website = ' + replaced_document['Web Site'] + '\n' +'Cloud Service = ' + replaced_document['Cloud Service'] + '\n' + 'Virtual Machine = ' + replaced_document['Virtual Machine'])
        else :        
            return render_template('vote.html', 
            title = 'Vote',
            form = form,
            providers = app.config['OPENID_PROVIDERS'])

    @app.route('/results')
    def results():
        """Renders the results page."""
        return render_template(
            'results.html',
            title='Results',
            year=datetime.now().year,
            message='Your application description page.')

### <a name="_Toc395809338">html 파일 만들기</a>

templates 폴더 아래에 다음 html 파일을 추가합니다.
create.html, results.html, vote.html

**create.html**에 다음 코드를 추가합니다. 이 코드는
새 데이터베이스, 컬렉션 및 문서를 만들었다는 메시지 표시를 처리합니다.

    {% extends "layout.html" %}
    {% block content %}
    <h2>{{ title }}.</h2>
    <h3>{{ message }}</h3>
    <p><a href="{{ url_for('vote') }}" class="btn btn-primary btn-large">Click here to Vote &raquo;</a></p>
    {% endblock %}

**results.html**에 다음 코드를 추가합니다. 이 코드는
설문 조사 결과 표시를 처리합니다.

    {% extends "layout.html" %}
    {% block content %}
    <h3>Results of the vote</h3>
    <pre>{{ title }}</pre>
    <p><a href="{{ url_for('vote') }}" class="btn btn-primary btn-large">Vote Again &raquo;</a></p>
    {% endblock %}

**vote.html**에 다음 코드를 추가합니다. 이 코드는
설문 조사 표시 및 투표 수락을 처리합니다. 투표를 등록하면 제어가
views.py로 전달되며, 여기서 투표 완료를 인식하고 그에 따라 문서를
추가합니다.

    {% extends "layout.html" %}
    {% block content %}
    <h1>What is your favorite way to host an application on Azure?</h1>
    <form action="" method="post" name="login">
    {{form.hidden_tag()}}
    <p> </p>
        {{form.remember_me}} Website
        {{form.remember_me1}} Cloud
        {{form.remember_me2}} Virtual Machine
        <p><input type="submit" value="Cast your vote"></p>
    </form>
    {% endblock %}

# <a name="_Toc395888529"></a>

**layout.html**의 코드를 삭제하고 다음 코드로 바꿉니다. 이 코드는
탐색 모음 및 라우팅을 위한 해당 URL 설정을 처리합니다.

index.html의 코드를 삭제하고 다음 코드로 바꿉니다. 이 코드는
응용 프로그램의 방문 페이지 역할을 합니다.

</h1>
## <a name="_Toc395888532"></a><a name="_Toc395809341">구성 파일 추가 및 \_\_init\_\_.py 변경</a>

프로젝트 이름 자습서를 마우스 오른쪽 단추로 클릭하고 **config.py** 파일을 추가합니다.
이 구성은 Flask의 폼에 필요합니다. 이 파일을 사용하여
비밀 키를 제공할 수도 있습니다. config.py에 다음 코드를 추가합니다.

    CSRF_ENABLED = True
    SECRET_KEY = 'you-will-never-guess'
    OPENID_PROVIDERS = [
       ]

유사한 방식으로 **\_\_init\_\_.py** 파일을 추가합니다. tutorial 폴더 아래에서
파일을 찾습니다. 원래 코드를 다음 코드로 바꿉니다. 이 코드는
뷰와 config.py 파일 간의 연결을 처리합니다.

    from flask import Flask
    app = Flask(__name__)
    app.config.from_object('config')
    import tutorial.views

위에서 언급한 단계를 따르면 솔루션 탐색기가
다음과 같이 표시됩니다.

![대체 텍스트][15]

# <a name="_Toc395888534"></a><a name="_Toc395809343"></a><a name="_Toc395637774">로컬에서 응용 프로그램 실행</a>

Visual Studio에서 F5 키 또는 실행 단추를 누르면
화면에 다음이 표시됩니다.

![대체 텍스트][16]

투표를 클릭하고 옵션을 선택합니다.

![대체 텍스트][17]

투표할 때마다 해당 카운터가 증가합니다. 다음에
투표할 때 결과를 확인할 수 있습니다.

![대체 텍스트][18]

</h1>
# <a name="_Toc395888534"></a><a name="_Toc395809343"></a><a name="_Toc395637774">Azure 웹 사이트에 응용 프로그램 배포</a>

이제 완료된 응용 프로그램이 DocumentDB에서 올바르게 작동하므로
Azure 웹 사이트에 배포하겠습니다. 솔루션 탐색기에서
프로젝트를 마우스 오른쪽 단추로 클릭하고(로컬에서 실행하고
있지 않은지 확인) 게시를 선택합니다.

![대체 텍스트][19]

자격 증명을 제공하고 새 웹 사이트를 만들거나 이전 웹 사이트를 다시 사용하여
Azure 웹 사이트를 구성합니다.

![대체 텍스트][20]

몇 초 후에 Visual Studio에서 웹 응용 프로그램 게시를 완료하고
브라우저를 시작하며, Azure에서 실행되는 작업 내용을
확인할 수 있습니다.

</h1>
# <a name="_Toc395809338">결론</a>

축하합니다. 지금까지 Azure DocumentDB를 사용하여 첫 Python 응용 프로그램을
작성하고 Azure 웹 사이트에 게시했습니다.

전체 솔루션을 보려면 [여기를 클릭][]하세요. 참고: 위에서 언급한 대로
가상 환경을 추가하고 python 도구 및
패키지를 설치해야 합니다.

</h1>

  [대체 텍스트]: ./media/documentdb-python-application/image1.png
  [Visual Studio Express]: http://www.visualstudio.com/en-us/products/visual-studio-express-vs.aspx
  [여기]: https://pytools.codeplex.com/releases/view/123624
  [1]: http://go.microsoft.com/fwlink/?linkid=254281&clcid=0x409
  [Microsoft 웹 플랫폼 설치 관리자를 통해 사용 가능]: http://www.microsoft.com/web/downloads/platform.aspx
  [2]: ./media/documentdb-python-application/image2.png
  [3]: ./media/documentdb-python-application/image3.png
  [4]: ./media/documentdb-python-application/image4.png
  [5]: ./media/documentdb-python-application/image5.png
  [6]: ./media/documentdb-python-application/image6.png
  [7]: ./media/documentdb-python-application/image7.png
  [8]: ./media/documentdb-python-application/image8.png
  [9]: ./media/documentdb-python-application/image9.png
  [Flask 자습서에 액세스하려면 여기를 클릭하세요]: http://blog.miguelgrinberg.com/post/the-flask-mega-tutorial-part-i-hello-world
  [10]: ./media/documentdb-python-application/image10.png
  [11]: ./media/documentdb-python-application/image11.png
  [12]: ./media/documentdb-python-application/image12.png
  [13]: ./media/documentdb-python-application/image13.png
  [14]: ./media/documentdb-python-application/image14.png
  [15]: ./media/documentdb-python-application/image15.png
  [16]: ./media/documentdb-python-application/image16.png
  [17]: ./media/documentdb-python-application/image17.png
  [18]: ./media/documentdb-python-application/image18.png
  [19]: ./media/documentdb-python-application/image19.png
  [20]: ./media/documentdb-python-application/image20.png
  [여기를 클릭]: http://go.microsoft.com/fwlink/?LinkID=509840&clcid=0x409
