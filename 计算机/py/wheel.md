---
tags:
  - Python
created: "2026-09-09"
---

# wheel（.whl）：可安装的分发包

## 是什么

wheel 是 Python 的二进制分发格式，本质是 zip 压缩包改扩展名。`uv build` / `pip wheel` 产出，`pip install xxx.whl` 直接安装。对应 npm publish 的 tarball，但 wheel 是「装好的成品」，安装时不需要构建。

## 里面有什么

```text
demo_pkg-0.1.0-py3-none-any.whl
├── demo_pkg/                 # 包代码
│   ├── __init__.py
│   └── py.typed
└── demo_pkg-0.1.0.dist-info/ # 元数据
    ├── METADATA              # 名字/版本/依赖
    ├── WHEEL
    └── RECORD                # 安装文件清单
```

纯 Python 包的 wheel 里就是 `.py` 源码明文，谁解压谁看光，不混淆不加密。没有 pyproject.toml、没有测试、没有开发工程——只有运行最小单元。拿到 whl ≠ 拿到源码工程。

## 文件名标签

```text
demo_pkg-0.1.0-py3-none-any.whl
{name}-{version}-{python}-{abi}-{platform}
```

- `py3`：面向 Python 3
- `none`：纯 Python，无 ABI（不编译 C 扩展）
- `any`：平台无关

C 扩展包的标签会变成 `cp313-cp313-macosx_arm64` 这类平台绑定值，不能到处装。这也是纯 Python 包标 `any` 的原因。

## 安装机制

```bash
pip install demo_pkg-0.1.0-py3-none-any.whl   # 直接把 whl 给他（离线/内网）
pip install demo-pkg                           # publish 到 PyPI 后全世界可装
```

两条路等价：解压到 site-packages + 按 RECORD 登记。装完 whl 文件本身可删，代码已在 site-packages 里，import 即用。

## sdist vs wheel

`uv build` 在 dist/ 下产出两个制品：

- `.whl`：成品，pip 默认优先装
- `.tar.gz`（sdist）：源码包，wheel 装不了才退回，安装时需按 build-system 现场构建

## 相关笔记

[[pyproject.toml]]
[[uv]]
