# 2020.08.10

## 박지환



### ORM?

* Object-relational mapping
* 장점
  * 객체 지향적 코드 -> 직관적, 비지니스 로직에 집중
  * 재사용 및 유지보수의 편리성이 증가
  * DBMS에 대한 종속성 감소
    * ORM은 DB에 종속적이지 ㅇ낳다.
* 단점
  * 설계가 중요
  * 프로젝트가 복잡할 경우 난이도 상승

### Sequelize?

* ORM Framework 중 하나

  * 다른 Framework로는 Prisma가 있다.

* Node.js에서 가장 많이 사용 + MySQL 지원

  * Promise를 기본으로 지원

    => 우리 프로젝트에 적합

#### 사용방법

**Database 연결** 

```js
const { Sequelize } = require('sequelize');
const sequelize = new Sequelize('database', 'username', 'password', {
  host: 'localhost',
  dialect: /* one of 'mysql' | 'mariadb' | 'postgres' | 'mssql' */
});
```

**Model 만들기**

```js
const { Sequelize, DataTypes } = require('sequelize');
// sqlite라고 가정
const sequelize = new Sequelize('sqlite::memory:');

const User = sequelize.define('User', {
  // Model attributes are defined here
  firstName: {
    type: DataTypes.STRING,
    allowNull: false
  },
  lastName: {
    type: DataTypes.STRING
    // allowNull defaults to true
  }
}, {
  // Other model options go here
  // table 이름을 명시
 tableName : 'bmart'
});

// `sequelize.define` also returns the model
console.log(User === sequelize.models.User); // true
```

- 만약 table 이름을 적지 않으면 모델 이름을 복수화한 형태로 Table이 생성

  ex) model : User => Table : Users

**Model과 DB 연결**

```js
1. User.sync() - This creates the table if it doesn't exist (and does nothing if it already exists)

2. User.sync({ force: true }) - This creates the table, dropping it first if it already existed

3. User.sync({ alter: true }) - This checks what is the current state of the table in the database (which columns it has, what are their data types, etc), and then performs the necessary changes in the table to make it match the model.
```

**Model Instance**

* Model의 instance는 Table의 한 row에 해당하며 DAO의 역할을 한다

  ```js
  1
  const jane = User.build({ name: "Jane" });
  await jane.save();
  2
  const jane = await User.create({ name: "Jane" });
  ```

**Model Querying**

* ex) select

  ```js
  const users = await User.findAll();
  ```

  ===

  ```sql
  SELECT * FROM Users
  ```

  

##### Prisma

* GraphQL 스키마를 기반으로 DB를 자동으로 생성
* GraphQL를 기반으로한 ORM
* GraphQL를 사용한다면 **Prisma**가 더 좋은 선택지 일듯
* MySQL 지원
  * 8, 5.6, 5.7 지원
  * https://www.prisma.io/docs/more/supported-databases



## 총평

장고를 했던 경험이 있어 ORM이 그렇게 낯설지는 않다.  

편할 것 같기도 하지마 Sequlize 자체가 새로운 라이브러리이고 Sequelize 문법을 공부해야하기 때문에 오히려 시간이 더 걸릴 것 같기도 하다.

중요한 점은 이번 프로젝트에 얼마나 필요하고 적합한지 인데 이는 ERD를 작성해보아야 알 것 같다.

만약, Join이 많이 필요한 DB 구조라면 쓰지 말고 그렇지 않다면 쓰는 것이 낫다고 생각한다.