---
title: GO PKG
date: 2024-12-13 11:40:09
tags: pkg
categories: GO
---

# fmt

- `%v` vs `%#v`

  ```golang
  type Example struct {
      Name  string
      Value int
  }
  
  func main() {
      ex := Example{Name: "test", Value: 42}
  
      // Using %v
      fmt.Printf("Default format: %v\n", ex)
      // Output: Default format: {test 42}
  
      // Using %#v
      fmt.Printf("Go syntax representation: %#v\n", ex)
      // Output: Go syntax representation: main.Example{Name:"test", Value:42}
  }
  ```

  

# json

- `json:",inline"`

  > This means that the fields of the embedded struct will appear at the same level as the fields of the parent struct in the resulting JSON object.

  ```go
  package main
  
  import (
      "encoding/json"
      "fmt"
  )
  
  type Meta struct {
      Name string `json:"name"`
      Age  int    `json:"age"`
  }
  
  type Person struct {
      Meta `json:",inline"`
      Address string `json:"address"`
  }
  
  func main() {
      p := Person{
          Meta: Meta{
              Name: "John",
              Age:  30,
          },
          Address: "123 Main St",
      }
  
      data, _ := json.Marshal(p)
      fmt.Println(string(data))
  }
  
  # output:
  {
      "name": "John",
      "age": 30,
      "address": "123 Main St"
  }
  ```


# cobra

## PersistentFlag vs Flag

**PersistentFlag**

- Flags that are available to the command and all its subcommands. but not explicitly added to the main command's help output.

**Flag**

- Definition: Flags that are only available to the specific command they are defined on.

- Example: If you set a flag on a specific command, it will not be available to its parent or sibling commands.

# klog

> k8s.io/klog/v2

```go
klog.Info("setting.MountOptions", setting.MountOptions)

klog.Infof("volumeId[%s] using patch:%#v", setting.VolumeId, patch)

klog.ErrorS(err, "dingofs create fs error: %v", err)
```

