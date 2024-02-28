## app & db 서버
1. app
- Maven build로 jar 만들기 (Goals에 "package" 작성. 쌍따옴표 적지 않음)
- jar파일 wsl 내 폴더에 옮기기
- Dockerfile 작성
  ```
  FROM openjdk:17
  ARG JAR_FILE=*.jar
  COPY ${JAR_FILE} todolist.jar
  ENTRYPOINT ["java","-jar","todolist.jar"]
  ```
- Build & Run
  ```
  sudo docker build -t todolist . -f Dockerfile
  sudo docker run -d --name todolist -p 8080:8080 todolist
   ```

2. db
- Dockerfile 작성
  ```
  # mariadb 10.11.5
  FROM mariadb:10.11.5

  # root 패스워드 설정
  ENV MYSQL_ROOT_PASSWORD maria
  ```
- 패스워드 설정이 잘 되지 않아 docker exec 활용, mariadb 서버 내에 직접 접속하여 변경함. (alter user 'root'@'localhost' identified by 'maria';)
- Build & Run
  ```
  sudo docker build -t mariadb . -f Dockerfile
  sudo docker run --name mariadb -p 3306:3306 -d mariadb
   ```

3. compose
- docker-compose.yml 작성
   ```
  version: '3.8'

  services:
    mariadb-db:
      image: mariadb:latest
      environment:
        MYSQL_ROOT_PASSWORD: maria
        MYSQL_DATABASE: todo_db
  
    spring-app:
      build: .
      ports:
        - "8080:8080"
      depends_on:
        - mariadb-db
      environment:
        SPRING_DATASOURCE_URL: jdbc:mariadb://mariadb-db:3306/todo_db
        SPRING_DATASOURCE_USERNAME: root
        SPRING_DATASOURCE_PASSWORD: maria
   ```
- docker-compose up -d 실행

4. push
- compose 해서 생성된 devops-spring-app 이미지 push


## 추후 수정 사항
- docker-compose.yml에 proxy 관련 내용 추가
- 기타 등등.
