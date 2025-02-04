1. 터미널에 아래 명령어로 MongoDB의 도커 이미지를 다운로드한다.
    ```bash
docker pull mongo
    ```
    - 버전 지정하지 않을 시 최신 버전이 다운로드된다.
        
2. 아래 명령어로 다운로드된 MongoDB의 이미지를 확인한다.
    ```bash
 docker images
    ```

3. MongoDB 컨테이너를 생성한다.
    ```bash
docker run --name mongoDB컨테이너이름 -p 27017:27017 -d mongo
    ```
    - MongoDB 기본 포트번호는 27017

4. 이제 MongoDB 컨테이너를 Docker에서 사용하면 된다.