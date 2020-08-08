# hkb-2
남현우, 박지환 가계부 프로젝트

#### 위키 [링크](https://github.com/woowa-techcamp-2020/hkb-2/wiki)
#### 요구 사항 [링크](https://lucas.codesquad.kr/%EC%9A%B0%EC%95%84%ED%95%9C%ED%85%8C%ED%81%AC%EC%BA%A0%ED%94%842020/course/%EC%9B%B9%ED%92%80%EC%8A%A4%ED%83%9D/WEEK-5-%EA%B0%80%EA%B3%84%EB%B6%80(1-2)/%EB%AF%B8%EC%85%98-%EA%B0%80%EA%B3%84%EB%B6%80%EC%84%9C%EB%B9%84%EC%8A%A4)
<hr>


#### 프로젝트 소개
- 달별로 내역, 달력, 통계를 보여주는 가계부 웹 어플리케이션
- 내역을 추가, 업데이트 삭제가 가능하다.
- 통계는 막대 그래프와 파이 차트를 이용해 보여준다.

#### 기획
- [기획서](https://docs.google.com/presentation/d/17QLlxQxgFxyvvV6uh8_7sD3SeXIu5bN986nm94uycX8/edit#slide=id.g8b5e1ec338_0_0)
- 보완하거나 추가할 아이디어는 [추가 기획](https://github.com/woowa-techcamp-2020/hkb-2/wiki/%F0%9F%91%8D-%EC%B6%94%EA%B0%80-%EA%B8%B0%ED%9A%8D)에 추가

#### 프로젝트 구조

```bash
hkb-2
│   ├── LICENSE
│   ├── README.md
│   ├── client
│   │   ├── babel.config.js
│   │   ├── node_modules
│   │   ├── package-lock.json
│   │   ├── package.json
│   │   ├── src
│   │   ├── tsconfig.json
│   │   ├── webpack.common.js
│   │   ├── webpack.dev.js
│   │   └── webpack.prod.js
│   └── server
│       ├── node_modules
│       ├── package-lock.json
│       ├── package.json
│       ├── public
│       └── src
```

## Set-up

1. `$ git clone https://github.com/woowa-techcamp-2020/hkb-2`
2. `$ cd ./client`
3. `$ npm i`
4. `$ cd ../server`
5. `$ npm i`

## Database Structure
<p align="center">
  <img src="./assets/images/database.png" width="1000" />
</p>

## 환경 변수 설정

**해당 프로젝트의 환경변수는 .env 파일에 담겨 있습니다.**

> .env 파일은 올라가 있지 않으니 sample.env에 값을 입력하고 .env로 파일이름을 바꾸시면 됩니다. 
```bash
src
└─ server
   └─ sample.env
```

**sample.env**
```bash
DB_HOST=
DB_USER=
DB_NAME=
DB_PASS=
GITHUB_CLIENT_ID=
GITHUB_CLIENT_SECRET=
```

## Client

##### 컴포넌트 만들기
```bash
$ create-wooact-component.sh 컴포넌트 이름
```

ex) create-wooact-component.sh `App`

## Run dev server

```bash
cd hkb-2/client
npm run dev
cd hkb-2/server
npm run dev
```
> client의 경우 9000번 port에서 webpack-dev-server가 돌아갑니다.  

> server의 경우 3000번 port에서 nodemone이 돌아갑니다.

## 




