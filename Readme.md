# Magazine K 프로젝트 (wecode 35기 1차 프로젝트)
## 프로젝트 개요 :speech_balloon:

전 세계의 균형 잡힌 브랜드를 한 호에 담아 매거진으로 만들어 소개하는 MagazineB 웹사이트를 모티브 하여 **이커머스 웹 사이트의 필수 기능**을 구현한 프로젝트입니다.

- 개발 기간 : 2022/07/18 ~ 2022/07/29 (12일)
- 개발 인원 : 5 명
- Frontend : 주원영(PM), 길현민, 노정은
- Backend  : 김동규, 황유정

#### 적용 기술
<img src="https://img.shields.io/badge/Python-3776AB?style=for-the-badge&logo=Python&logoColor=white"/>&nbsp;
<img src="https://img.shields.io/badge/Django-092E20?style=for-the-badge&logo=Django&logoColor=white"/>&nbsp;
<img src="https://img.shields.io/badge/MySQL-4479A1?style=for-the-badge&logo=MySQL&logoColor=white"/>&nbsp;
<img src="https://img.shields.io/badge/Postman-FF6C37?style=for-the-badge&logo=Postman&logoColor=white"/>&nbsp;
<img src="https://img.shields.io/badge/aws-232F3E?style=for-the-badge&logo=Amazon AWS&logoColor=white">&nbsp;

## 프로젝트 구조
### DB 모델링
![database](Screenshot%20from%202022-07-30%2013-31-31.png)

## 📄 구현 기능에 대한 소개

### 회원가입 및 로그인 (황유정)

1. 회원가입 시 필수 입력값인 이름, 이메일, 패스워드는 정규 표현식 데이터 검증 후 조건에 성립하는 경우에만 가입할 수 있습니다. 
  - 데이터 검증에 성립하지 않을 경우 에러가 발생하는 입력값에 대한 각각의 에러 메세지를 반환합니다.
2. 회원가입 시 필수 입력값인 이메일은 UNIQUE 설정이 되어 있어 데이터가 중복되는 경우에는 가입할 수 없습니다.
  - 중복 이메일로 확인되는 경우 400 에러 메세지를 반환합니다.
3. bcrypt를 사용하여 사용자의 비밀번호를 해쉬화하여 DB에 저장합니다.
4. 로그인 시 유저가 입력한 비밀번호를 언해쉬화하여 체크하여 검증된 유저인 경우 jwt 토큰을 발행하여 응답합니다.
  - jwt 토큰 토큰에는 유저 id값만 담겨져있으며 상품 구매, 리뷰 작성과 같은 기능을 수행할 때 @login_decorator 검증 시 토큰이 활용됩니다.
5. 💡 회원가입 및 로그인에 대한 Unit test 코드 작성 후 추가하였습니다. (2023.03.01)


#### 배운 점 & 아쉬운 점
* *문서화의 중요성
- 프로젝트 기간 동안 여러 번의 DB 테이블 번복으로 인해 지체된 시간으로 각자가 맡은 기능을 구현하는데 급해 프런트 팀원과 협의할 때 구두로만 커뮤니케이션을 했습니다.
- 결과적으로 프로젝트에 대해 각자가 구현한 기능에 대해 정리할 때도 문서화되지 않았기 때문에 각 API에 대한 키값, 발생하는 오류, 해결 방법 등 대처에 아쉬웠던 부분이 많아 팀 회고에서도 문서화의 중요성에 대해 계속 회자되었습니다.

* 정규 표현식의 이해
- 정규 표현식을 이용한 유효성 검사는 처음 접했기 때문에 공식 문서와 여러 기술 블로그를 참조했습니다. 그리고 프런트와 통신 중 프런트 단에서도 입력 오류를 처리할 수 있어 보다 더 단단하게 회원 가입 처리를 할 수 있다는 점이 안심되었습니다.

* @login_decorator
- 사용자 로그인 시 jwt 토큰을 발행하였는데, 발행한 토큰을 검증하는 데코레이터 코드에 오류가 있어 해결하는 데 오래 결렸습니다. 😭 

#### 🎬 회원 가입 및 로그인 시연

![로그인-및-회원가입](https://user-images.githubusercontent.com/94777292/223303051-19f926f7-2433-4767-8ada-8b5340c64707.gif)



### 상품 상세 페이지 및 리뷰 기능(황유정)

1. path parameter를 사용한 엔드 포인트 [/products/list]로 요청받습니다.
- 요청에 성공한 경우 제품의 정보와, 코드 200을 반환합니다.
2. 구매한 상품이 배송 완료가 된 시점에 리뷰와 별점을 작성할 수 있고 삭제할 수 있습니다.
- 구매하지 않은 회원은 리뷰를 작성할 수 없으며, 구매했더라도 로그인한 상태가 아닐 경우 리뷰를 작성할 수 없습니다.
3. order 상태 값을 Enum class를 활용하여 코드에 적용하였습니다.
- 회원은 배송 완료 시점에 리뷰를 작성할 수 있기 때문에, Enum class로 선언한 값으로 제품의 상태 값을 넣어 필터링하여 조건에 맞는지 확인합니다.

#### 배운 점 & 아쉬운 점
* *패스 파라미터와 쿼리 파라미터의 차이점
 - 패스 파라미터는 특정 아이디 값의 상품을 가져올 때 사용합니다. ex) /product/1
- 쿼리 파라미터는 filtering 또는 ordering에 더 적절하게 사용할 수 있다는 점을 알게 됐습니다. </br>이번 프로젝트에는 패스 파라미터만 활용해 봤지만, 다음에는 쿼리 파라미터를 이용한 API를 작성해 보고 싶습니다.

* *Eunm class 활용
- Enum 클래스를 활용하면 코드를 보다 깔끔하게 작성할 수 있다는 점을 배웠습니다. Enum class 활용 시 class의 적절한 위치와 상수 작성 등을 여러 app 사용을 고려해서 사용해야 한다는 점을 알게 되었습니다.

#### 🎬 상품 상세 페이지 및 리뷰 기능 시연

**상품 상세 페이지**
![제품-상세-야](https://user-images.githubusercontent.com/94777292/223307229-c28d751f-614b-4adf-8abf-84dc869947ab.gif)
**제품 리뷰 테스트**
![리뷰테스츠](https://user-images.githubusercontent.com/94777292/223306639-1a3958b3-1d9c-4895-a102-71a28d571439.gif)
**상품 리뷰 작성**
![리뷰-정리](https://user-images.githubusercontent.com/94777292/223305983-4db87626-3941-40d4-9455-2a0d4e7ea7e9.gif)



#### Reference
이 프로젝트는 학습목적으로 만들었기 때문에 코드를 활용하여 이득을 취하거나 무단 배포할 경우 법적으로 문제될 수 있습니다.
