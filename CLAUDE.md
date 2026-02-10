# Hiddify (ZEPHYR Fork)

基于 [hiddify/hiddify-app](https://github.com/hiddify/hiddify-app) 的 fork，集成 ZEPHYR 协议。

## GitHub

- 上游: `https://github.com/hiddify/hiddify-app` (origin)
- Fork: `https://github.com/lai3d/zephyr-hiddify` (zephyr)

## 相关仓库

- [lai3d/zephyr-hiddify-xray-core](https://github.com/lai3d/zephyr-hiddify-xray-core) — xray-core with ZEPHYR
- [lai3d/zephyr-hiddify-libcore](https://github.com/lai3d/zephyr-hiddify-libcore) — Go mobile libcore 子模块
- [lai3d/zephyr-ray2sing](https://github.com/lai3d/zephyr-ray2sing) — 配置转换工具

## 关键修改

### libcore/go.mod

```go
replace github.com/xtls/xray-core => github.com/lai3d/zephyr-hiddify-xray-core v0.0.0-20260131083807-38f086c93198
replace github.com/hiddify/ray2sing => github.com/lai3d/zephyr-ray2sing v0.0.0-20260130013817-f849c461d5d8
```

### 依赖修复

- `sentry_flutter`: 7.16.1 → 8.9.0 (修复 C++ 编译错误)
- `sentry_dart_plugin`: 升级到 ^2.1.0
- `lib/core/analytics/analytics_filter.dart`: 修复 Sentry beforeSend API 签名变更

## 构建 macOS

```bash
cd libcore && make macos
cd .. && fvm flutter build macos
```

## 构建 Android

```bash
# 1. 构建 libcore.aar
cd libcore
gomobile bind -v -androidapi=21 -javapkg=io.nekohasekai \
  -libname=box -tags='with_gvisor,with_quic,with_wireguard,with_ech,with_utls,with_clash_api,with_acme' \
  -target=android -o bin/libcore.aar \
  github.com/sagernet/sing-box/experimental/libbox ./mobile

# 2. 替换 AAR (包名必须为 io.nekohasekai.libbox)
cp libcore/bin/libcore.aar android/app/libs/hiddify-core.aar

# 3. 构建 APK
fvm flutter build apk --release
```

**重要**: `-javapkg=io.nekohasekai` 确保生成的 Java 包名为 `io.nekohasekai.libbox`，与 Kotlin 代码的 import 匹配。

## 环境

- Flutter: 3.24.3 (通过 FVM: `fvm use 3.24.3`)
- Go: 1.25.x
- Java: JDK 17
- `GOPRIVATE=github.com/lai3d/*`
