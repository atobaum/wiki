---
title: "Github에서 GPG를 적용해보자"
summary: 'Github에서 GPG를 적용해보자'
layout: wiki
parent: ''
tags: [GPG, Git, Github]
toc: false
date: 2020-10-25T17:55:01+09:00
lastmod: 2020-10-25T17:55:01+09:00
---
Git에서는 commit이나 tag를 만들 때 gpg 서명을 할 수 있다. 이를 적용해보자.

## GPG 키 만들기
### GnuPG 설치
Mac에서 brew를 이용해 GnuPG를 설치하자. 이 글에서 사용하는 버전은 `2.2.23`이다.
```shell
brew install gpg
```
맥이 아닌 다른 운영체제는 지못미...

### GPG key 생성
이제 gpg키를 만들어보자:
```shell
gpg --full-gen-key
```

만약 간단하게 만들고 싶으면 `--gen-key`를 사용하자. name과 email만 입력하면 된다.

1. 맘에 드는 키 종류를 고르자. 여기서는 기본값인 `RAS and RSA`를 선택한다.
2. key size를 선택하자. 1024부터 4096까지 선택할 수 있는데 요즘은 컴퓨터가 좋아서 4096으로 해도 좋다. Github에서는 4096을 권장한다고 한다.
3. 마음에 드는 키 유효기간을 설정하자.
4. 마지막으로 name과 email을 설정하면 키가 만들어진다.

### 확인
다음 명령어를 입력하면 키가 잘 만들어졌는지 확인할 수 있다:
```shell
$ gpg --list-secret-keys --keyid-format LONG
/Users/test/.gnupg/pubring.kbx
---------------------------------
pub   rsa4096/ABCD1235 2020-10-25 [SC] [expires: 2021-10-25]
      316D443A0E92BB0A0CFB8C64581C8953EEFAAE4
uid                 [ultimate] test <test@test.com>
sub   rsa4096/QWER3455 2020-10-25 [E] [expires: 2021-10-25]
```

위에서 `rsa4096/ABCD1235`의 `ABCD1235`가 key id이다. 이 key id를 잘 기억하자.

## Git에 GPG key 등록
git에서 commit이나 tag를 만들 때 `-S` 옵션을 주면 gpg 서명을 할 수 있다.
```shell
git config --global user.signkey ABCD1234
git config --global commit.gpgsign true  # -S 옵션을 주지 않아도 서명한다.
```

## Githuib에 GPG key 등록
마지막으로 gpg public key를 github에 등록해야한다.
다음 명령어를 입력하면 GPG key가 출력된다. 맥에서는 뒤에 `| pbcopy`를 추가하면 바로 클립보드에 복사할 수 있다.
```shell
$ gpg --armor --export ABCD1235
```

복사한 키를 github setting - SSH and GPG keys에서 설정할 수 있다. 이 때 앞뒤의 `-----BEGIN PGP PUBLIC KEY BLOCK-----`, `-----END PGP PUBLIC KEY BLOCK-----`까지 입력해야한다.

이제 commit을 하고 push를 해보면 commit 옆에 Verified가 뜨는 것을 볼 수 있다.

## Referende
- [기계인간 위키 - GnuPG 사용법](https://johngrib.github.io/wiki/gpg/)
- [Github docs](https://docs.github.com/en/free-pro-team@latest/github/authenticating-to-github/generating-a-new-gpg-key)

## 더 보기

- [RSA](/wiki/rsa)