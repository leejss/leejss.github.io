---
layout: post
title: Powershell Alias 설정하기
date: 2021-04-30 15:51 +0900
tags: Powershell
---

## Set-Alias 또는 New-Alias

```shell
> Set-Alias 별칭 실제커맨드
> New-Alias 별칭 실제커맨드

> Get-Alias # Show all alias
```

`New-Alias`는 ovverride시 error를 발생한다. (더욱 안전하다.)

alias를 설정했으면 `$profile`에 저장해야한다.

```shell
> notepad $profile # 위의 별칭을 설정하는 line을 입력한다음 저장한다. 그래야 새로운 터미널을 열때 alias를 사용할 수 있다.
```

### 활용

oh-my-posh theme을 적용하기 위해서는 다음과 같은 커맨드가 필요하다.

```shell
> Set-PoshPrompt -Theme 테마이름
```

alias를 설정하여 다음과 같이 단순하게 만들 수 있다.

```shell
> New-Alias spp Set-PoshPrompt
> ssp powerline
```

## Function

Set-Alias 또는 New-Alias로 alias를 설정하는 경우 argument와 함께 alias를 설정할 수 없다.

이럴 경우 함수를 만들어 alias와 동일한 기능을 만들 수 있다.

```shell
function sppt([string]$theme)
{
    Set-PoshPrompt -Theme "$theme"
}
```

만든 함수를 `$profile`에 저장한다.

### 활용

```shell
> sppt powerline
```

이제 간편하게 theme을 적용할 수 있다.

## 참고

[How to: Create a PowerShell alias with parameters](https://seankilleen.com/2020/04/how-to-create-a-powershell-alias-with-parameters/){:target="_blank"}