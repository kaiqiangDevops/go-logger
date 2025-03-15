# Go 日志库

[![License](https://img.shields.io/badge/license-MIT-blue.svg)](LICENSE)[![Go Version](https://img.shields.io/badge/go-1.18%2B-brightgreen)](https://golang.org/dl/)[![Build status](https://img.shields.io/badge/go-dev-black.svg)](https://pkg.go.dev/github.com/kaiqiangDevops/go-logger)

基于 [logrus](https://github.com/sirupsen/logrus) 和 [lumberjack](https://github.com/natefinch/lumberjack) 封装的日志库，支持 JSON 格式化输出、日志滚动和多级别日志管理。

------

## 功能特性

- ✅ **多日志级别**: `Debug`/`Info`/`Warn`/`Error`
- ✅ **JSON 结构化日志**: 包含时间戳、调用位置和上下文信息
- ✅ **日志文件滚动**: 按日期切割文件，支持压缩和自动清理
- ✅ **自定义配置**: 灵活设置日志路径、备份策略和压缩选项
- ✅ **调用链追踪**: 自动记录日志产生的文件、函数和行号

------

## 安装依赖

```bash
go get github.com/sirupsen/logrus
go get gopkg.in/natefinch/lumberjack.v2
```

## 快速开始

### 基础用法

```go
package main

import (
    "github.com/yourusername/log"
)

func main() {
    // 初始化日志系统
    log.InitLogger(
        "debug",          // 日志级别
        "./logs",         // 日志存储目录
        7,               // 最大备份文件数
        30,              // 文件保留天数
        true,            // 启用压缩
    )

    log.Info("服务启动成功")
    log.Debugf("当前版本: %s", "v1.0.0")
    log.WithField("port", 8080).Warn("高危操作")
}
```

### 日志输出示例

```json
{"timestamp": "2025-03-15 14:30:45", "level": "INFO", "func": "main.main", "file": "app.go:12", "msg": "服务启动成功"}
{"timestamp": "2025-03-15 14:30:46", "level": "DEBUG", "func": "main.init", "file": "config.go:25", "msg": "当前版本: v1.0.0"}
```

## 配置说明

### `InitLogger` 参数详解

| 参数名       | 类型   | 必填 | 默认值 | 说明                              |
| :----------- | :----- | :--- | :----- | :-------------------------------- |
| `logLevel`   | string | 是   | -      | 日志级别（debug/info/warn/error） |
| `logPath`    | string | 是   | -      | 日志文件存储目录                  |
| `maxBackups` | int    | 否   | 7      | 最大保留的旧日志文件数量          |
| `maxAge`     | int    | 否   | 30     | 日志文件保留天数（单位：天）      |
| `compress`   | bool   | 否   | true   | 是否压缩旧日志文件                |

### 日志方法

| 方法             | 功能描述           | 示例                                          |
| :--------------- | :----------------- | :-------------------------------------------- |
| `Debug(args)`    | 输出调试信息       | `log.Debug("内存占用:", 1024)`                |
| `Debugf(format)` | 格式化输出调试信息 | `log.Debugf("CPU使用率: %.2f%%", 75.5)`       |
| `Info(args)`     | 输出普通信息       | `log.Info("服务启动")`                        |
| `Infof(format)`  | 格式化输出普通信息 | `log.Infof("用户 %s 登录", "Alice")`          |
| `Warn(args)`     | 输出警告信息       | `log.Warn("磁盘空间不足")`                    |
| `Warnf(format)`  | 格式化输出警告信息 | `log.Warnf("请求超时: %dms", 5000)`           |
| `Error(args)`    | 输出错误信息       | `log.Error("数据库连接失败")`                 |
| `Errorf(format)` | 格式化输出错误信息 | `log.Errorf("文件 %s 未找到", "config.yaml")` |

## 注意事项

1. **文件权限**
   确保程序对日志目录有读写权限，否则会导致日志写入失败。
2. **日志滚动规则**
   - 日志文件按日期自动命名（格式：`app-YYYYMMDD.logger`）
   - 每日凌晨生成新文件（依赖系统时间）
3. **性能建议**
   - 生产环境建议使用 `info` 或更高级别
   - 避免在循环中频繁输出 `debug` 日志
4. **错误处理**
   - 无效的日志级别会自动降级为 `info`
   - 初始化失败时会打印警告信息到标准输出
