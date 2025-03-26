---
title: clang-uml
date: 2025-03-20 19:27:14
tags: tool
categories: clang-uml
---

# clang-uml

## init

```shell
# init .clang-uml file
clang-uml --init

# use specify config file
clang-uml -c /path/to/configFile
```

## generate PlantUML

```shell
# generate xxx.puml base on default config
clang-uml # --progress

# draw svg picture base on above puml file
plantuml -tsvg xxx.puml
```

## generate  mermaid

```shell
# genrate xxx.mmd
clang-uml -g mermaid

# draw svg
mmdc -i xxx.mmd # -o <name>.svg

# draw big svg
vim config.json
{
  "maxTextSize": 1000000
}

mmdc -i diagram.mmd -c config.json
```

## other

```shell
# To find the exact function signature
 clang-uml --print-from -n client_class_diagram -c .clang-uml-sequence | grep main
```

