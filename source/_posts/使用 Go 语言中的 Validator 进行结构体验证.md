---
title: 使用 Go 语言中的 Validator 进行结构体验证
date: 2024-11-01 11:31:43
tags: 
    - golang
categories: 后端
---

# 使用 Go 语言中的 Validator 进行结构体验证：问题与解决

在日常开发中，数据验证是非常关键的一步。确保数据正确性可以有效地提升系统的健壮性。在使用 Go 语言构建应用程序时，`go-playground/validator` 是一个广受欢迎的验证库。最近，我在使用这个库进行结构体字段验证时遇到了一个小问题。本文将分享我遇到的问题及其解决方案，希望能对其他开发者有所帮助。

## 遇到的问题

我的代码中有两个结构体：`User` 和 `InterceptorConfig`。`User` 结构体用于存储用户信息，`InterceptorConfig` 包含一个结构体 slice `Interceptors`。起初，我对这些结构体应用了 `required` 标签进行验证，具体如下：

```go
type User struct {
    Name string `validate:"required"`
}

type InterceptorConfig struct {
    Interceptors []InterceptorConfigItem `json:"interceptors" validate:"required"`
}

type InterceptorConfigItem struct {
    Fields string `json:"fields" validate:"required"`
    Rule   string `json:"rule" validate:"required"`
}
```
在验证 `User` 结构体时，一旦 `Name` 字段为空，验证器会如期打印 "Name is required"。但是，当我验证 `InterceptorConfig` 结构体，即使 `Interceptors` 内部的 `InterceptorConfigItem` 的 `Fields` 和 `Rule` 字段均为空字符串时，验证却意外地通过了。

## 原因分析

深入研究之后，发现问题在于如何对 slice 类型进行 `required` 验证。`required` 标签仅验证 `Interceptors` 本身是否为非零值（即非空）。因为 `Interceptors` 是一个有元素的 slice，即使元素的内部字段为空，也被视为有效。换句话说，验证器只验证了 `Interceptors` 本身，不会自动递归检查 slice 中每个结构体的字段。

## 解决方案

为了确保 `Interceptors` 中每个 `InterceptorConfigItem` 的 `Fields` 和 `Rule` 字段均非空，我们应该使用 `dive` 标签。`dive` 指示验证器深入验证 slice 中的每个 struct 对象的字段。修改后的代码如下：

```go
type InterceptorConfig struct {
    Interceptors []InterceptorConfigItem `json:"interceptors" validate:"required,dive"`
}
```

通过添加 `dive` 标签，验证器会对 `Interceptors` slice 中的每个 `InterceptorConfigItem` 逐一应用 `required` 标签进行验证。
## dive 的意义详解

`dive` 是 `go-playground/validator` 库中的一个特殊标签，用于指导验证器深入（即递归地）验证 slice、array、map 或 channel 中的每个元素。

1. **深入验证集合** ：
   - `dive` 表示对集合内部的每个元素进行深入验证。这意味着你不仅仅验证集合整体，而且逐一检查其中的每个元素。
2. **配合其他标签使用** ：
   - 在 `dive` 后面可以附加其他验证规则，这些规则将被应用于集合中的每个元素。例如，`dive,required` 会检查集合中的每个元素都不为其零值。
3. **典型应用场景** ：
   - 验证二维切片或数组中的每个元素。
   - 验证 map 中每一个 value 的值。
   - 验证 channel 中传输的每个数据元素。
## 成功的经验

通过这次问题的解决，我更深入地理解了 `dive` 标签的强大之处，它让我可以灵活对集合进行深入验证。未来在使用 `validator` 处理复杂数据结构时，我会更加注意这些细节。

希望这篇文章能够帮助其他在使用 `go-playground/validator` 时遇到类似问题的开发者