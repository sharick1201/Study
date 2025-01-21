
### 상황
feature/#9 PR 리뷰를 위해 gradle을 다시 build하고 Application을 실행시켰는데, 어플리케이션 실행에 실패했다.
```
***************************
APPLICATION FAILED TO START
***************************

Description:

Web server failed to start. Port 8080 was already in use.

Action:

Identify and stop the process that's listening on port 8080 or configure this application to listen on another port.

```

### 원인
해당 8080 포트가 이미 다른 프로세스에 의해 실행 중이라 발생했다. 아마 내 작업을 위해 이전에 실행된 게 안 끊긴 듯하다.

### 해결
터미널 명령어들로 실행 중인 포트를 끊어주면 된다. 
1. 사용 중인 포트를 확인한다.

```

```
