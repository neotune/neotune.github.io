---
layout: article
title: git 서버별 ssh key 설정 방법
mathjax: true
---
# git 서버별 ssh key 설정 방법

git 서버가 여러개 있을 경우(회사 내부, github, bitbucket 등) 개인키/공개키를 쌍으로 등록해서 서버마다 다른키를 적용 할 수 있다.

기존에 사용하는 ssh key 가 있고 github 접속을 위해 ssh-key를 추가 발급하는 설정으로 진행 맥을 기준으로 설명 하고 있습니다

### 설정방법

1. $HOME/.ssh/ 폴더로 이동하여 별도의 폴더를 만든다 (ex:github)

   ```bash
   $ mkdir ~/.ssh/github
   ```

2. ssh-keygen

   ```bash
   $ ssh-keygen -t rsa -b 4096 -C "github에 등록한 이메일주소"
   ```

3. key 저장경로 지정 (ex:$HOME/.ssh/github/id_rsa)

   ```bash
   $ ssh-keygen -t rsa -b 4096 -C "github에 등록한 이메일주소
   Generating public/private rsa key pair.
   Enter file in which to save the key (/Users/username/.ssh/id_rsa): ~/.ssh/github/id_rsa
   Enter passphrase (empty for no passphrase):
   Enter same passphrase again:
   Your identification has been saved in /Users/username/.ssh/github/id_rsa.
   Your public key has been saved in /Users/username/.ssh/github/id_rsa.pub.
   The key fingerprint is:
   12:23:56:78:90:12:23:ab:cd:ef:e7:d0:b9:02:1c:12 username@username.kr
   The key's randomart image is:
   +--[ RSA 2048]----+
   |    o*.o.* o     |
   |   oE * = %      |
   |    *  . * =     |
   |     . .. o .    |
   |      . S.       |
   |         .       |
   |                 |
   |                 |
   |                 |
   +-----------------+
   ```

4. 생성한 ssh-key github에 SSH keys에 등록

   ```bash
   $ pbcopy < ~/.ssh/github/id_rsa.pub
   ```

5. config 파일 생성 (ex:vi $HOME/.ssh/config)

   ```bash
   $ vi ~/.ssh/config
   ```

6. 다음과 같이 작성

   ```bash
   Host github.com
       PreferredAuthentications publickey
       IdentityFile ~/.ssh/github/id_rsa
   Host *
   	PreferredAuthentications publickey
       IdentityFile ~/.ssh/id_rsa
   ```

7. 권한 부여

   ```bash
   $ chmod 440 ~/.ssh/config
   ```

8. 정상적으로 접속 되는지 확인 (debug 메세지에 사용하는 id_rsa 위치 출력 확인)

   ```bash
   $ ssh -v git@github.com 
   ```