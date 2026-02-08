# Quick Installation Guide

## 📋 Files Modified

Only **ONE** Go source file is modified:
```
script/script.go
```

## 🚀 Installation Methods

### Method 1: Apply Patch (Recommended)

1. Download `lucksystem-variable-length.patch`
2. Place it in your LuckSystem-2.3.2 directory
3. Apply the patch:

**Linux/Mac:**
```bash
cd LuckSystem-2.3.2
patch -p0 < lucksystem-variable-length.patch
go build -o lucksystem
```

**Windows (with Git Bash or WSL):**
```bash
cd LuckSystem-2.3.2
patch -p0 < lucksystem-variable-length.patch
go build -o lucksystem.exe
```

### Method 2: Manual Edit

1. Open `script/script.go` in your editor
2. Find line 175-177 and apply changes from `CHANGES.md`
3. Find line 179-197 and apply changes from `CHANGES.md`
4. Find line 200-243 and apply changes from `CHANGES.md`
5. Save and compile:

```bash
go build -o lucksystem
```

### Method 3: Download Pre-compiled Binary

Check the [Releases](../../releases) page for pre-compiled binaries.

## ✅ Verification

After compilation, test with a single file:

```bash
lucksystem script import \
  -s SCRIPT.PAK \
  -c UTF-8 \
  -O data/OPCODE.txt \
  -p data/GAME.py \
  -i test_script/ \
  -o SCRIPT_TEST.PAK
```

If successful, you should see:
```
Import: your_file_name
length: X -> Y
```

No panic error means it worked! ✅

## 🐛 Troubleshooting

### Error: "patch: **** malformed patch"
- Make sure patch file has Unix line endings (LF not CRLF)
- Use `dos2unix lucksystem-variable-length.patch` to convert

### Error: "go: command not found"
- Install Go: https://go.dev/dl/
- Minimum version: Go 1.16

### Compilation errors
- Make sure you're using LuckSystem 2.3.2 source code
- Download from: https://github.com/wetor/LuckSystem

## 📦 For AIR (Key/Visual Arts Games)

Additionally download `data/AIR.py` (merged plugin) to avoid dependency issues:

```
data/
└── AIR.py  (replaces both data/AIR.py and data/base/air.py)
```

## 🔗 Links

- **Original LuckSystem:** https://github.com/wetor/LuckSystem
- **Patch Issues:** (link to your GitHub issues page)
- **AIR Translation:** https://yoremitradfr.my.canva.site/
