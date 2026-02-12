# iOS App Store 可复用构建Workflow

这是一个可在多个iOS项目中复用的GitHub Actions workflow，包含：
- ✅ 自动化构建和打包
- ✅ 代码混淆（防止App Store 4.3拒审）
- ✅ App Store Connect自动上传
- ✅ 完整的证书和Profile管理

## 🚀 快速开始

### 在你的iOS项目中使用

在你的项目的 `.github/workflows/build.yml` 中：

```yaml
name: Build and Upload to App Store

on:
  workflow_dispatch:

jobs:
  build:
    uses: ZClee128/uploadAppStore/.github/workflows/reusable-ios-build.yml@main
    with:
      workspace_name: "YourApp.xcworkspace"
      scheme_name: "YourApp"
      bundle_id: "com.yourcompany.yourapp"
      provisioning_profile_name: "YourApp"
      upload_to_appstore: true
    secrets:
      BUILD_CERTIFICATE_BASE64: ${{ secrets.BUILD_CERTIFICATE_BASE64 }}
      P12_PASSWORD: ${{ secrets.P12_PASSWORD }}
      BUILD_PROVISION_PROFILE_BASE64: ${{ secrets.BUILD_PROVISION_PROFILE_BASE64 }}
      KEYCHAIN_PASSWORD: ${{ secrets.KEYCHAIN_PASSWORD }}
      TEAM_ID: ${{ secrets.TEAM_ID }}
      APPLE_ID: ${{ secrets.APPLE_ID }}
      APP_SPECIFIC_PASSWORD: ${{ secrets.APP_SPECIFIC_PASSWORD }}
```

### 必需的Secrets配置

在你的项目的 Settings → Secrets 中添加：

| Secret名称 | 说明 |
|-----------|------|
| `BUILD_CERTIFICATE_BASE64` | 开发者证书（.p12）的Base64编码 |
| `P12_PASSWORD` | .p12文件的密码 |
| `BUILD_PROVISION_PROFILE_BASE64` | Provisioning Profile的Base64编码 |
| `KEYCHAIN_PASSWORD` | 临时keychain的密码（随机字符串） |
| `TEAM_ID` | Apple Developer Team ID |
| `APPLE_ID` | App Store Connect登录邮箱 |
| `APP_SPECIFIC_PASSWORD` | App专用密码 |

#### 如何获取Base64编码

```bash
# 证书
base64 -i YourCertificate.p12 | pbcopy

# Provisioning Profile
base64 -i YourProfile.mobileprovision | pbcopy
```

## 📝 参数说明

| 参数 | 必需 | 默认值 | 说明 |
|-----|------|--------|------|
| `workspace_name` | ✅ | - | Xcode workspace文件名 |
| `scheme_name` | ✅ | - | Xcode scheme名称 |
| `bundle_id` | ✅ | - | App Bundle ID |
| `provisioning_profile_name` | ✅ | - | Provisioning Profile名称 |
| `configuration` | ❌ | Release | 构建配置 |
| `xcode_version` | ❌ | latest-stable | Xcode版本 |
| `upload_to_appstore` | ❌ | true | 是否上传到App Store |
| `obfuscation_script_path` | ❌ | scripts/advanced_obfuscate.py | 混淆脚本路径 |

## 🎭 代码混淆功能

此workflow自动包含代码混淆，每次构建生成不同的二进制签名：

1. 在你的项目中添加 `scripts/advanced_obfuscate.py`
2. 脚本会生成 `ObfuscationBundle.swift` 
3. 将此文件添加到Xcode项目中编译

**混淆脚本下载：** [advanced_obfuscate.py](https://github.com/ZClee128/uploadAppStore/blob/main/scripts/advanced_obfuscate.py)

## 📂 完整示例

查看示例项目配置：[example-project.yml](https://github.com/ZClee128/uploadAppStore/blob/main/examples/example-project.yml)

## 🔧 高级用法

### 仅构建不上传

```yaml
with:
  upload_to_appstore: false
```

### 使用特定Xcode版本

```yaml
with:
  xcode_version: "15.0"
```

### 自定义混淆脚本路径

```yaml
with:
  obfuscation_script_path: "tools/my_obfuscator.py"
```

## ❓ 常见问题

**Q: 为什么我的构建失败？**  
A: 检查Secrets是否正确配置，证书是否为Distribution类型。

**Q: 可以用于多个app吗？**  
A: 可以！每个app创建一个workflow文件，配置不同的参数即可。

**Q: 混淆功能是必需的吗？**  
A: 不是。如果没有混淆脚本，workflow会自动跳过混淆步骤。

**Q: 如何更新workflow？**  
A: 修改引用的版本号，例如 `@main` 改为 `@v1.0.0`

## 📜 许可证

MIT License

## 🤝 贡献

欢迎提交Issue和Pull Request！
