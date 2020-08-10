# 2020.08.10

## 박지환

### Github Action

Github 저장소를 기반으로 소프트웨어 개발 Workflow를 자동화 할 수 있는 도구

Github에서 제공한는 CI/CD 도구

* 저장소마다 최대 20개까지 등록 가능

#### 사용 방법

.github/workflows에 .yml 형식의 파일을 만들어 workflow를 정의

```yaml
name: Greet Everyone
# 이 Workflow는 저장소에 Push가 되면 실행 되도록 한다
on: [push]

jobs:
  # Job ID: 문자와 -(대시), _(언더바)로 구성된 Job 고유 식별자
  build:
    # Job 이름
    name: Greeting
    # 이 Job은 리눅스에서 실행된다
    runs-on: ubuntu-latest
    steps:
      # 이 Step은 Github이 제공하는 `hello-world-javascript-action` Action을 사용한다
      - name: Hello world
        uses: actions/hello-world-javascript-action@v1
        id: hello
      # 이 Step은 이전 Step에서 나온 출력을 사용한다
      - name: Echo the greeting's time
        run: echo 'The time was ${{ steps.hello.outputs.time }}.'
```

on 이벤트를 이용해 master에 push하거나 v1 태그가 push 되었을때 배포되게 할 수도 있을 것 같다.

```yaml
on:
  push:
    branches:
      - master
    tags:
      - v1
```





https://jonnung.dev/devops/2020/01/31/github_action_getting_started/