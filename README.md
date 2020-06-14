[![Build status](https://ci.appveyor.com/api/projects/status/12qqjwc35wo16n33/branch/master?svg=true)](https://ci.appveyor.com/project/Daniil35412/homework06/builds/33514777)


## Homework VI

```sh
% sed -i "" '/project(solver)/a\
set(SOLVER_VERSION_STRING "v\${SOLVER_VERSION}")
' CMakeLists.txt
% sed -i "" '/project(solver)/a\
set(SOLVER_VERSION\
  \${SOLVER_VERSION_MAJOR}.\${SOLVER_VERSION_MINOR}.\${SOLVER_VERSION_PATCH}.\${SOLVER_VERSION_TWEAK})
' CMakeLists.txt
% sed -i "" '/project(solver)/a\
set(SOLVER_VERSION_PATCH 0)
' CMakeLists.txt
% sed -i "" '/project(solver)/a\
set(SOLVER_VERSION_MINOR 0)
' CMakeLists.txt
% sed -i "" '/project(solver)/a\
set(SOLVER_VERSION_MAJOR 1)
' CMakeLists.txt
```

```sh
% touch DESCRIPTION && atom DESCRIPTION
% touch ChangeLog.md
% export DATE="`LANG=en_US date +'%a %b %d %Y'`"
% cat > ChangeLog.md <<EOF
* ${DATE} DaniilRyb <pochtaworld7@gmail.com> 1.0.0
- Initial RPM release
EOF
```

```sh

% cat > CPackConfig.cmake <<EOF
include(InstallRequiredSystemLibraries)
EOF
```

```sh
% cat >> CPackConfig.cmake <<EOF

# Установка контакта
set(CPACK_PACKAGE_CONTACT evgengrmit@icloud.com)
# Установка версии пакета
set(CPACK_PACKAGE_VERSION_MAJOR \${SOLVER_VERSION_MAJOR})
set(CPACK_PACKAGE_VERSION_MINOR \${SOLVER_VERSION_MINOR})
set(CPACK_PACKAGE_VERSION_PATCH \${SOLVER_VERSION_PATCH})
set(CPACK_PACKAGE_VERSION \${SOLVER_VERSION})
#  Установка с файлом описания проекта
set(CPACK_PACKAGE_DESCRIPTION_FILE \${CMAKE_CURRENT_SOURCE_DIR}/DESCRIPTION)
# Краткое описание проекта
set(CPACK_PACKAGE_DESCRIPTION_SUMMARY "Application Solver for square equation")
EOF
```

```sh
% cat >> CPackConfig.cmake <<EOF

# Установка лицензии
set(CPACK_RESOURCE_FILE_LICENSE \${CMAKE_CURRENT_SOURCE_DIR}/LICENSE)
# Добавление README.md
set(CPACK_RESOURCE_FILE_README \${CMAKE_CURRENT_SOURCE_DIR}/README.md)
EOF
```

```sh
$ cat >> CPackConfig.cmake <<EOF

# Имя пакета RPM
set(CPACK_RPM_PACKAGE_NAME "solver-devel")
# Лицензионная политика пакета RPM.
set(CPACK_RPM_PACKAGE_LICENSE "MIT")
# Группа пакетов RPM
set(CPACK_RPM_PACKAGE_GROUP "solver")
# Добавление файла с описанием изменений пакета
set(CPACK_RPM_CHANGELOG_FILE \${CMAKE_CURRENT_SOURCE_DIR}/ChangeLog.md)
# Выпуск пакета RPM
set(CPACK_RPM_PACKAGE_RELEASE 1)
EOF
```
N
```sh
% cat >> CPackConfig.cmake <<EOF

# Имя пакета DEBIAN
set(CPACK_DEBIAN_PACKAGE_NAME "AppSolver")
# Условия для работы пакета
set(CPACK_DEBIAN_PACKAGE_PREDEPENDS "cmake >= 3.0")
# Выпуск пакета DEBIAN
set(CPACK_DEBIAN_PACKAGE_RELEASE 1)
EOF
```

```sh
% cat >> CPackConfig.cmake <<EOF

# Имя созданного пакета
set(CPACK_DMG_VOLUME_NAME "solverOS")
# Минимальная версия OSX
set(CPACK_OSX_PACKAGE_VERSION 10.5)
set(CPACK_WIX_LICENSE_RTF ${CMAKE_CURRENT_SOURCE_DIR}/LICENSE.txt)
EOF
```

```sh
% cat >> CPackConfig.cmake <<EOF

# Помощь в установке пакета
set(CPACK_NSIS_HELP_LINK https://github.com/Evgengrmit/hw06)
# Помощь в использовании
set(CPACK_NSIS_URL_INFO_ABOUT https://github.com/Evgengrmit/hw06)
# Контактная информация
set(CPACK_NSIS_CONTACT evgengrmit@icloud.com)
EOF
```

```sh
% cat >> CPackConfig.cmake <<EOF

include(CPack)
EOF
```

```sh
% cat >> CMakeLists.txt <<EOF

include(CPackConfig.cmake)
EOF
```

```sh
% cat >> .travis.yml <<EOF
language: cpp
jobs:
  include:
  - os: windows
    script:
    - cmake -H. -B_build
    - cmake --build _build
    - cd _build
    - cpack -G DragNDrop
    - cd ..
  - os: linux
    script:
    - cmake -H. -B_build
    - cmake --build _build
    - cd _build
    - cpack -G DEB
    - cpack -G RPM
    - cd ..
addons:
  apt:
    packages:
      - cmake
      - cmake-data
      - rpm
EOF
```

```sh
% travis login --auto --ppro
% travis enable --pro
% travis setup releases --pro

% cat >> CMakeLists.txt <<EOF
file:
  - ./_build/solver-1.0.0.-Darwin.dmg
  - ./_build/solver-1.0.0.-Linux.deb
  - ./_build/solver-1.0.0.-Linux.rpm
skip_cleanup: true
on:
  tags: true
EOF
```
```sh
% git add .
% git commit -m "First release"
% git tag v1.0.0
% git push origin master --tags
```

```sh
% cat >> appveyor.yml <<EOF
image: Visual Studio 2019
platform:
  - x86
  - x64
configuration: Release

build_script:
  - cmd: cmake -H. -B_build
  - cmd: cmake --build _build --config Release
  - cmd: cd _build
  - cmd: ls
  - cmd: cpack -G NSIS
  - cmd: cd ..

artifacts:
  - path: ./_build/*.msi
    name: solver
deploy:
  release: $(APPVEYOR_REPO_TAG_NAME)
  description: 'Release description'
  provider: GitHub
  auth_token:
    secure: tZdq4qXRLud/z27+KjHqLP0jpdTdUkrQ1XmSJMbSAnd2KTvjSqLjRlueHzHPhzLe
  artifact: print
  on:
    APPVEYOR_REPO_TAG: true

EOF
```

```sh
$ cat .travis.yml
os: osx
script:
...
- cpack -G DragNDrop # dmg

$ cat .travis.yml
os: linux
script:
...
- cpack -G DEB # deb

$ cat .travis.yml
os: linux
addons:
  apt:
    packages:
    - rpm
script:
...
- cpack -G RPM # rpm

$ cat appveyor.yml
platform:
- x86
- x64
build_script:
...
- cpack -G NSIS # msi
```



## Links
- [DMG](https://cmake.org/cmake/help/latest/module/CPackDMG.html)
- [DEB](https://cmake.org/cmake/help/latest/module/CPackDeb.html)
- [RPM](https://cmake.org/cmake/help/latest/module/CPackRPM.html)
- [NSIS](https://cmake.org/cmake/help/latest/module/CPackNSIS.html)
- [Travis Client](https://github.com/travis-ci/travis.rb)
- [AppVeyour](https://www.appveyor.com/)
- [GitLab CI](https://about.gitlab.com/gitlab-ci/)

```
Copyright (c) 2015-2020 The ISC Authors
```
