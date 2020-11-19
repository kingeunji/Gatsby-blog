---
title: Github 계정으로 SSH키 발급하기
date: 2020-11-19 15:11:94
category: development
draft: false
---

회사나 개인 계정으로 작업을 할 때 매번 계정 권한을 변경 해줘야 해서 답답했다.  
그래서 SSH key 를 등록해서 사용하는 법에 대해 찾아봤당.

### 1. SSH 키 발급

```bash
ssh-keygen -t rsa -C "계정1@email.com" -f "키ID-1"
ssh-keygen -t rsa -C "계정2@email.com" -f "키ID-2"
```

해당 명령어 실행 시 ~/.ssh 경로에 확장자가 없는 비밀키와 .pub 확장자가 가진 공개키가 생성된다.

### 2. GitHub 계정에 SSH 키 등록하기

계정을 등록할 공개키의 내용을 복사한 뒤 Github 및 Gitlab 에서 키를 등록한다.

1. **Settings**로 이동한다.
2. **SSH and GPG keys** 를 선택한다.
3. **New SSH key**를 선택하고 원하는 이름을 입력한 뒤 복사한 공개키를 붙여넣는다.
4. **Add key**를 눌러 등록을 완료한다.

### 3. SSH config 수정하기

계정 별 다른 SSH 키를 사용해야 하기 때문에 ~/.ssh/config 파일을 수정한다.

```bash
# deepnatural
Host github.com-deepnatural
	HostName ssh.github.com
	User git
  IdentityFile ~/.ssh/id_rsa

# kingeunji-local
Host github.com-kingeunji
	HostName ssh.github.com
	User git
  IdentityFile ~/.ssh/kingeunji_gmail
```

### 4. Git Local config 수정

각 사용할 Repository 폴더 내에서 계정을 다르게 사용하기 위해서 로컬 설정을 한다.

```bash
git config --local user.name sim-eunji
git config --local user.email kingeunji12@gmail.com
```

### 5. remote URL 수정하기

remote url을 수정해서 ssh key를 사용하도록 한다.

```bash
git remote set-url origin git@github.com-kingeunji:repo_user/repo_name.git
```
