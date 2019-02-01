### 맥 zsh 쉘에서 git branch 명령어 실행 시 branch 리스트가 화면 전환되어 출력될때

맥에서 git branch -l 명령어 이용시 기존 bash 쉘에서는 명령어 아래에 바로 출력이 되었는데 zsh 쉘로 전환 후 갑자기 git 에서 branch 리스트를 화면전환하여 보여주고 있어서 불편했습니다

아래와 같이 명령어를 입력하면 해결 됩니다

```bash
$ git config --global pager.branch false
```

