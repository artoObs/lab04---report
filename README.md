[![Build](https://github.com/artoObs/lab04---report/actions/workflows/build.yml/badge.svg)](https://github.com/artoObs/lab04---report/actions/workflows/build.yml)

# Laboratory work IV

Данная лабораторная работа посвящена изучению систем непрерывной интеграции на примере сервиса Travis CI

```bash
$ open https://travis-ci.org
```
Так как работа переведена из Travis CI в GitHub Actions, то буду писать сначала команду/действие по условию, а потом на что меняется.

## Tasks

1. Авторизоваться на сервисе Travis CI с использованием GitHub аккаунта

      Не требуется

2. Создать публичный репозиторий с названием lab04 на сервисе GitHub

      Создан

3. Ознакомиться со ссылками учебного материала

      Ознакомлен

4. Включить интеграцию сервиса Travis CI с созданным репозиторием

      Не требуется, будет создан файл в процессе

5. Получить токен для Travis CLI с правами repo и user

      Токен получен, но с правами repo и workflow

6. Получить фрагмент вставки значка сервиса Travis CI в формате Markdown

      Копируется через интерфейс вкладки Actions

7. Выполнить инструкцию учебного материала

      Выполнено

8. Составить отчет и отправить ссылку личным сообщением в Slack

## Tutorial

```bash
$ export GITHUB_USERNAME=<имя_пользователя>
$ export GITHUB_TOKEN=<полученный_токен>
$ cd ${GITHUB_USERNAME}/workspace
$ pushd .
$ source scripts/activate
```
Выполнено

```bash
$ \curl -sSL https://get.rvm.io | bash -s -- --ignore-dotfiles
```
Установлен RVM

```bash
$ echo "source $HOME/.rvm/scripts/rvm" >> scripts/activate
```
Дописана подгрузка RVM в файл scripts/activate

```bash
$ . scripts/activate
```
Перезапущен scripts/activate

```bash
$ type -p curl >/dev/null || sudo apt install curl -y
$ curl -fsSL https://cli.github.com/packages/githubcli-archive-keyring.gpg | sudo dd of=/usr/share/keyrings/githubcli-archive-keyring.gpg
$ sudo chmod go+r /usr/share/keyrings/githubcli-archive-keyring.gpg
$ echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/githubcli-archive-keyring.gpg] https://cli.github.com/packages stable main" | sudo tee /etc/apt/sources.list.d/github-cli.list > /dev/null
$ sudo apt update
$ sudo apt install gh -y
```
Установка GitHub Actions

```bash
echo "${GITHUB_TOKEN}" | gh auth login --with-token
```
Авторизация через токен

```bash
$ git clone https://github.com/${GITHUB_USERNAME}/lab03 projects/lab04
$ cd projects/lab04
$ git remote remove origin
$ git remote add origin https://github.com/${GITHUB_USERNAME}/lab04
```
Клонирование и настройка репозитория

```bash
$ mkdir -p .github/workflows
$ cat > .github/workflows/build.yml <<'EOF'
name: Build

on:
  push:
    branches: [ "master", "main" ]
  pull_request:
    branches: [ "master", "main" ]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - name: Checkout repository
      uses: actions/checkout@v4

    - name: Setup CMake
      uses: jwlawson/actions-setup-cmake@v1.14
      with:
        cmake-version: '3.16.x'

    - name: Configure CMake
      run: cmake -H. -B_build -DCMAKE_INSTALL_PREFIX=_install

    - name: Build
      run: cmake --build _build

    - name: Install
      run: cmake --build _build --target install
EOF
```
Тут происходит создание конфигурационного файла GitHub Actions и оно кардинально отличается от Travis

```bash
$ BADGE="[![Build](https://github.com/${GITHUB_USERNAME}/lab04/actions/workflows/build.yml/badge.svg)](https://github.com/${GITHUB_USERNAME}/lab04/actions/workflows/build.yml)"
$ echo "${BADGE}" | cat - README.md > temp && mv temp README.md
```
Формирует строку Markdown со значком и сохраняет в README.md

```bash
$ git add .github/workflows/build.yml
$ git add README.md
$ git commit -m "added GitHub Actions"
$ git push origin master
```
Отправка коммитов

```bash
$ gh workflow list
```
Показывает файлы .yml в репозитории (travis repos)

```bash
$ gh run watch $(gh run list --limit 1 --json databaseId --jq '.[0].databaseId')
```
В реальном времени показывает логи выполнения запуска (travis whatsup)

```bash
$ gh run list --branch master
```
Фильтрует список запусков только по ветке master (travis branches)

```bash
$ gh run list
```
Выводит список последних запусков (travis history)

```bash
$ gh run view $(gh run list --limit 1 --json databaseId --jq '.[0].databaseId')
```
Получает ID самого последнего запуска и показывает подробную информацию о нём (travis show)

## Homework

Продолжаю из папки, где находились все файлы прошлой лабораторной.

```bash
$ git init
$ git add .
$ git commit -m "Initial commit"
$ git remote add origin https://github.com/artoObs/lab04---report.git
$ git push -u origin main
```
Отправил все файлы, что уже были в репозиторий.

Дальше через GitHub изменил build.yml:

```bash
name: CI

on:
  push:
    branches: [ "main", "master" ]
  pull_request:
    branches: [ "main", "master" ]

jobs:
  build:
    name: ${{ matrix.compiler }} on Linux
    runs-on: ubuntu-latest

    strategy:
      matrix:
        compiler: [gcc, clang]

    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Setup CMake
        uses: jwlawson/actions-setup-cmake@v1.14
        with:
          cmake-version: '3.16.x'

      - name: Install dependencies (if needed)
        run: sudo apt-get update && sudo apt-get install -y build-essential clang

      - name: Configure CMake
        run: cmake -H. -B_build -DCMAKE_INSTALL_PREFIX=_install -DCMAKE_C_COMPILER=${{ matrix.compiler }} -DCMAKE_CXX_COMPILER=${{ matrix.compiler == 'gcc' && 'g++' || 'clang++' }}

      - name: Build
        run: cmake --build _build

      - name: Install
        run: cmake --build _build --target install
```
Дальше требуется сборка всего файла, поэтому был добавлен новый CMakeLists.txt:

```bash
$ cat > CMakeLists.txt <<'EOF'
cmake_minimum_required(VERSION 3.4)
project(FormatterProject)

add_subdirectory(formatter_lib)
add_subdirectory(formatter_ex_lib)
add_subdirectory(solver_lib)
add_subdirectory(hello_world)
add_subdirectory(solver)
EOF

git add CMakeLists.txt
git commit -m "add root CMakeLists.txt"
git push origin main
```
После этого у меня возникали ошибки касательно CMakeLists.txt из прошлой лабораторной (связанные с субдиректориями и файлами .cpp). После починки всё заработало, значок загорелся зелёным.
