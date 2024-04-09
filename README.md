# Here-sMyPet
A spring board to show off your Pet

1. 프로젝트 소개(24.04.02 ~ 04.05)
- 각자의 반려동물 사진을 올려서 자랑하고, 선플로 응원하는 게시판입니다.

2. 프로젝트 기능
프로젝트의 주요 기능은 다음과 같습니다.

게시판 - CRU 기능(D는 X), 페이징
사용자 - 로그인/회원가입으로 권한 설정
댓글 - x

3. 팀소개 (백엔드 4명)
팀장: 원준표
팀원: 권진우, 진성길, 백서준

5. 사용 기술
- 주요 프레임워크: Java, Flask, Python
- DB: SQLalchemy
- 프런트엔드: HTML, CSS
 
5. 에러사항 모음
준표 에러사항 1: 각각 구축된 DB가 동시 실행이 안됨
문제점: 하나의 가상환경에 구축된 DB를 합치는 건 불가능하다(R DBMS 라는 신박한 걸 써야 되는데 이건 수준이 안됨)
해결방안(with 한결 튜터님, 예린 튜터님):

- 각각 작성했던 [app.py](http://app.py) 에서 겹치는 건 모두 제외하고, route만 추가해주고 경로에 대한 함수 이름 변경(하나의 app.py, [model.py](http://model.py) 파일로 만들 것)
- html+css 양식에 너무 제한되지 말고 일단 기존 db에 테이블을 추가해서 하나의 링크에서 움직이도록 경로를 만들기 (ex. __ 링크 뒤에 /post/write 등 과 같은 새로운 경로를 지정하여 작동하는지 확인
- 새로운 과제: 이제 서로 다른 두 개를 충돌하지 않게 어떻게 합칠까…

준표 에러사항 2: 기존의 methods가 계속을 인식을 못해 page405 에러가 지속적으로 발생
해결방안(with 예린 튜터님):
- @app.route('/post/write') → @app.route('/post/write', methods=['GET', 'POST'])
- a 타입 이었던 모든 형태의 텍스트를 form 으로 바꾸어서 작성(그래야 테이블에 추가 가능)

<form action="/post" method="post">
<input type="text" name="title" placeholder="제목">
<input type="text" name="author" placeholder="글쓴이">
<textarea name="content" placeholder="내용"></textarea>
<button type="submit">등록</button>
</form>

- 새로운 과제: 자, 이제 405 에러에서 완전 다른 형태의 에러로 넘어갔다… 다시 에러 수정.

준표 에러사항 3: datetime을 제대로 읽지 못하여 [app.py](http://app.py) 에서 에러 발생
해결방안(with 예린 튜터님):
- 일단 pip 내에 datetime 이 깔려있지 않다.
- pip install datetime 을 깔고 UTCNOW 로 설정
- 시작에 from datetime import datetime 추가
- 문제 해결

준표 에러사항 4: flask 실행 시, css 파일이 제대로 실행이 안되고 다 깨짐
문제점: html에서 브라우저로 실행했을 때는 css 파일이 적용되고, flask 가상환경 서버에서는 적용이 안됨
해결방안(민준 튜터님):
<link rel="stylesheet" href="C:\Users\thewo\Desktop\게시판\static/style.css">
→ 슬래시를 다 일단 바꿔보자
<link rel="stylesheet" href="C:/Users/thewo/Desktop/게시판/static/style.css">
→ 여전히 css 적용이 안됨
**<link rel="stylesheet" href="{{ url_for('static', filename='CSS파일이름.css') }}">**
→ 이제 html, flask 모두에서 css 적용 가능, 다른 html도 모두 url을 저렇게 변경 완료 하니 css 적용 완료! 굿!

준표 에러사항 5: list.html 에서 post 테이블을 제대로 읽어오지 못함
문제점: post define이 제대로 되지 않았다고 뜸
해결방안(예린 튜터님): ‘s’ 가 빠져 있네요…

@app.route('/list')
def list():
    posts = Post.query.all()
    return render_template('list.html', posts=posts)

그리고 5개 적는거보다, for 문으로 바꿔서 쓰죠!
문제해결 완료! + 코드 길이 줄임

준표 에러사항 6: list.html에서 불러온 제목 링크 생성이 안됨
문제점: 제목을 넘겨 받는 게 아니라, POST.ID를 넘겨 받아야 글을 읽어올 수 있다. 제목에다가 링크를 다는 게 아니다.
해결방안: 새로운 route 경로 지정, id=id일때의 filter 를 걸어서 post를 받고, html 상에서 링크 연결.
app.py
@app.route('/view/<int:id>')
def viewpost(id):
    post = Post.query.filter_by(id=id).first()
    return render_template('view.html', post=post)

view.html  
<div class="title"><a href="{{ url_for('viewpost', id=post.id) }}">{{ post.title }}</a></div>

준표 에러사항 7: 최신 게시물이 위로 안 올라오고 아래에 쌓인다
문제점: list.html에서 post를 불러올 때 순차적으로 불러오기에 역순 배열이 필요하다.
해결방안: for post in posts|reverse 
reverse 파이썬 함수를 통해 배열만 거꾸로 하면, 위로 순차적으로 쌓인다.

권진우 에러 사항 1:
문제 : 엔드포인트의 라우트 데코레이터가 GET 요청만을 처리하도록 설정되어 있어서 POST 요청을 처리하지 못했어요 그래서 사용자가 로그인 폼을 제출할 때 POST 요청이 발생하면 처리할 수 있는 코드가 없어서 Builderror 발생!! 이를 해결하기 위해 /login 엔드포인트에 대한 라우트 데코레이터를 GET 및 POST 요청을 모두 처리할 수 있도록 수정
BuildError
werkzeug.routing.exceptions.BuildError: Could not build url for endpoint 'list'. Did you mean 'register' instead? ( URL을 빌드하는 동안 발생하는 오류 )
@app.route('/login', methods=['POST'])
해결 : @app.route('/login', methods=['GET', 'POST']) 
결론 : GET 및 POST 요청을 모두 처리하도록 변경

권진우 에러사항 2: 

문제 : SQLite 데이터베이스에 컬럼이 없다는 것을 나타내고 있다!!
post 테이블에 user_id 컬럼이 없다
OperationalError
sqlalchemy.exc.OperationalError: (sqlite3.OperationalError) no such column: post.user_id
[SQL: SELECT [post.id](http://post.id/) AS post_id, post.title AS post_title, post.content AS post_content, post.created_at AS post_created_at, post.user_id AS post_user_id
FROM post]
(Background on this error at:

해결 : site.db 삭제후 다시 실행하여 데이터베이스 업데이트 컬럼추가+ 하여서
결론 : 업데이트 한거를 최신화 안해서 기존에 있던 db를 삭제 그리고 다시 최신화 하여서 user_id 만들었다! 해결 완료 우으응 뀨뀨~

진성길 에러사항 1:
SQLite 데이터베이스에 user_id 컬럼이 없다는 에러 발생
post 테이블에 user_id 컬럼이 없음을 확인
데이터베이스를 최신화하고 user_id 컬럼을 추가함

site.db 파일 삭제
프로그램 다시 실행하여 데이터베이스 업데이트 및 컬럼 추가
새로운 데이터베이스를 통해 업데이트된 컬럼인 user_id 추가 확인

업데이트한 내용을 최신화하지 않아 발생한 문제였음.
기존의 데이터베이스를 삭제하고 다시 최신화하여 user_id 컬럼을 추가함으로써 문제 해결
