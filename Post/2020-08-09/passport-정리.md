## Passport에 대해 알아보자

하이욤. 

이번 프로젝트에서 node.js의 인증을 위해 passport 모듈을 사용하라는 요구사항이 있었는데 처음 써보기 때문에 공부해보면서 정리해보려고 해.

### passport란?

먼저 passport가 무엇인지 알아보자

passport는 node.js에서 인증을 위한 middleware입니다. passport는 오직 인증 요청에만 목적을 두고 있다고 하네

요즘 OAuth를 이용한 소셜 로그인이 인기가 많아졌는데 passport는 각 service provide별로 다른 인증 요구사항들을 알고 독립적인 모듈로 인증 모듈을 제공한다고 하네... 쩝; 무슨 말인지는 실습하면서 알아보자 :smirk:

### passport 설치

이제 passport를 깔아봅시다.

```bash
 npm i passport
```

passport는 runtime에 필요하기 때문에 dependency에 설치합니다.

### passport strategies

passport를 사용하기 위해서는 어떤 strategy를 사용할지 설정 해줘야한다.

가장 대표적이고 쉬운 strategy는 username, passowrd를 가지고 인증하는 `LocalStrategy`이다. OAuth, OpenID 전략도 있다.

`LocalStrategy`를 설정하고 사용해보자

먼저 passport-local 모듈을 설치해야한다. 위에서 독립적인 모듈로 인증 모듈을 제공한다고 했는데 필요한 전략에 따라 모듈을 설치할 수 있다는 의미인 것 같다.

passport-local를 깔아준다.

```bash
npm i passport-local
```

다음으로 passport-local을 통해 strategy를 설정해줘야하는데 공식 문서에는 구체적으로 어디에 설정하라고 나와있지는 않다. 원래는 별도의 파일을 만들어야겠지만 편의를 위해 app.js에서 설정을 해주겠다.

**app.js** 

```js
import passport from 'passport'
import LocalStrategy from 'passport-local'

passport.use(new LocalStrategy(
  function(username, password, done) {
    User.findOne({ username: username }, function (err, user) {
      if (err) { return done(err); }
      if (!user) {
        return done(null, false, { message: 'Incorrect username.' });
      }
      if (!user.validPassword(password)) {
        return done(null, false, { message: 'Incorrect password.' });
      }
      return done(null, user);
    });
  }
));
...
```

`User.findOne`부분은 Application에 따라 다르게 구현될 것이다. 대충 DB에서 request로 들어온 username에 해당하는 user를 찾는 로직이라고 생각하면 될 것 같다. 

```js
function(username, password, done)
```

이 부분을 verify callback이라고 하는데 로그인에 성공하면 `return done(null, user)`, 실패하면 `return done(null, false)`를 실행시키면 된다.

```js
// 이런 식으로 메시지를 전달해줄 수도 있다.
// flash message라고 부른다
return done(null, false, { message: '비밀번호가 맞지 않습니다' });
```

만약 서버에 에러가 발생하면 전통적인 node.js 방식처럼 `return done(err)`를 실행시키면 된다.

###### 즉, done의 첫번째 인자는 err, 두번째 인자에 정보가 들어간다고 생각하면 된다.



### passport middleware

이제 strategy 설정을 마쳤으니 passport를 middleware로 사용하기 위해 설정해줘야 한다.

**app.js**

```js
import session from "express-session"

app.use(session({ secret: "cats" }));
app.use(passport.initialize());
app.use(passport.session());
...
```

이때 passport의 session 기능은 선택인데 일반적인 웹 어플리케이션처럼 한번 로그인하면 세션이 유지되어야 하는 상호아이면 쓰는게 좋다. 만약 쓴다면 session 설정을 먼저 해줘야한다. express-session이 없다면 깔아보자

```bash
npm i express-session
```

기본적으로 session은 cookie를 이용해 세션을 유지한다.

### passport session

자 이제 입력한 username과 passoword를 통해 우리 사이트 회원인걸 알았으니 어떻게 해야 될까?

```js
passport.serializeUser(function(user, done) {
  done(null, user.id);
});

passport.deserializeUser(function(id, done) {
  User.findById(id, function(err, user) {
    done(err, user);
  });
});
```

**(1) serializeUser**

아까 로그인이 성공하면 done(null, user)가 실행된다고 했다. 이때 done의 두번째 인자가 serializeUser의 callback 함수의 첫번째 인자로 들어간다고 한다. 즉, 로그인에 성공하면 done(null, user)가 실행되고 serializeUser의 callback 함수가 실행되는 것이다. 이 함수는 세션에 user의 id를 기록한다.

**(2) deserializeuser**

로그인에 성공한 이후 페이지를 방문하면 deserializeUser의 callback 함수가 실행된다. 세션에 저장된 id를 이용해 해당 유저를 DB에서 찾고 req.user에 정보를 담아 보낸다. 다음 미들웨어는 req.user를 통해 user 정보에 접근할 수 있다.

### passport routes

이제 route에 passport를 적용해보자.

```js
app.post('/login',
  passport.authenticate('local', { successRedirect: '/',
                                   failureRedirect: '/login',
                                   failureFlash: true })
);
```

위와 같은 패턴은 인증에 성공하거나 실패했을때 redirect 주소를 다 넘겨 다음 미들웨어가 없습니다.

다음 미들웨어에서 req.user를 이용해 로직을 구현할 수도 있지 않을까요?

```js
app.post('/login',
  passport.authenticate('local'),
  function(req, res){
	  if(!req.user) return res.send('로그인을 다시 해주세요')
	  return res.send('welcome')
  }
);
```

마찬가지로 다른 Route에서도 req.user에 유저 정보가 담겨있으니 이를 통해 로직을 진행하면 될 것 같습니다.

### 마무으리

지금까지 Local Strategy로 passport를 적용하는 방법에 대해 알아봤습니다.

다음에는 OpenID, OAuth strategy도 공부해봐야겠습니다.

마지막으로 실제 코드를 돌려보면서 테스트해보았는데요

req.user와 req.session을 console로 찍어봤을때

**req.user**

```js
{
  id: 1,
  username: 'jack',
  password: 'secret',
  displayName: 'Jack',
  emails: [ { value: 'jack@example.com' } ]
} 
```

**req.session**

```js
Session {
  cookie: { path: '/', _expires: null, originalMaxAge: null, httpOnly: true },
  passport: { user: 1 }
}
```

다음과 같이 나오네요.

자 이제 client에서 쿠키에 잘 저장되었는지 볼까요?

아래와 같이 sessionId와 쿠키 정책이 잘 저장되어 있네요.

쿠키 정책은 express-session의 옵션을 통해 변경할 수 있습니다.

```js
connect.sid=s%3AWKNdgG0i8zvN-dNJrvjsg6rRAuFBtCcf.JID7xRCHC0%2BqPHnKKSaZO%2FT7cgFAEG7oVB3rLSncQzM; Path=/; Domain=localhost; HttpOnly;
```

이제 요청을 보낼때마다 쿠키에 있는 sessionId를 통해 passport가 해당 user를 확인하고 req.user에 user를 담아서 다음 미들웨어로 보내겠죠?

### 소감

세션 관련해서 직접 만드는게 좋다는 생각이었는데 실제로 사용해보니 편리하고 자유도가 높아서 좋은 것 같습니다.

req.user를 통해 세션을 판단할 수 있는 점이 정말 편리하네요.

다만, 아직 실제 어플리케이션에 적용하진 않아서 실제 서비스 구현에서는 어떨지 모르겠습니다. 사용해보면서 판단해야할 것 같습니다.



감사합니다 😄 

[github 주소](https://github.com/parkjihwanjay/passport-local-test)


#### 참고

1. http://www.passportjs.org/docs/
2. https://m.blog.naver.com/PostView.nhn?blogId=pjok1122&logNo=221565691611&proxyReferer=https:%2F%2Fwww.google.com%2F