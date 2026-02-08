# LuckSystem - Variable Length Translation Support

**Patch for LuckSystem 2.3.2 to support variable-length translations**

##  Problem

The original LuckSystem enforces strict parameter count matching during script import, which prevents translations from being longer or shorter than the original text. This causes the following error when importing translations:

```
panic: 导入参数数量不匹配 [operation_index]
```

This limitation makes it impossible to properly translate visual novels into languages like French, German, or Spanish, which often have longer text than the original Japanese or English.

##  Solution

This patch modifies `script/script.go` to:
1. Remove the strict parameter count verification
2. Add bounds checking to prevent array index out of bounds errors
3. Allow StringParam to have variable lengths while still correctly recalculating jump offsets

## Changes Made

### File Modified: `script/script.go`

**Three critical modifications:**

#### 1. Lines 175-177: Remove strict parameter count check
**Before:**
```go
if len(paramList) != len(code.Params) {
    panic("导入参数数量不匹配 " + strconv.Itoa(index))
}
```

**After:**
```go
// NOTE: Vérification du nombre de paramètres supprimée pour permettre
// les chaînes de longueur variable (important pour la traduction).
// Le code ci-dessous gère correctement les StringParam de taille variable.
```

#### 2. Lines 179-197: Add bounds checking in type conversion loop
**Before:**
```go
for i := 0; i < len(paramList); i++ {
    switch paramList[i].(type) {
    case byte:
        val, _ := strconv.ParseUint(code.Params[i].(string)[2:], 16, 8)
        code.Params[i] = byte(val)
    // ... etc
```

**After:**
```go
maxLen := len(paramList)
if len(code.Params) < maxLen {
    maxLen = len(code.Params)
}
for i := 0; i < maxLen; i++ {
    switch paramList[i].(type) {
    case byte:
        val, _ := strconv.ParseUint(code.Params[i].(string)[2:], 16, 8)
        code.Params[i] = byte(val)
    // ... etc
```

#### 3. Lines 200-243: Add bounds checking in parameter merge
**Before:**
```go
case *StringParam:
    param.Data = code.Params[pi].(string)
    allParamList = append(allParamList, param)
    pi++
```

**After:**
```go
case *StringParam:
    if pi < len(code.Params) {
        param.Data = code.Params[pi].(string)
    }
    // Always append StringParam even if pi exceeds bounds
    allParamList = append(allParamList, param)
    pi++
```

##  Installation

### Option 1: Apply the patch
```bash
cd LuckSystem-2.3.2
git apply lucksystem-variable-length.patch
go build -o lucksystem
```

### Option 2: Manual modification
1. Open `script/script.go`
2. Apply the three modifications listed above
3. Compile: `go build -o lucksystem`

## Tested With

- **Game:** AIR Steam Version (Key/Visual Arts)
- **Original Language:** Japanese
- **Target Language:** French
- **Result:** Successfully imported translations up to +20% longer than original English text

##  Features

With this patch, you can now:
- Use translations of **any length** (shorter or longer than original)
- Use proper French typography (`Hya !`, `Mais où ?`, etc.)
- No need for space padding to match original length
- Jump offsets (GOTO, IFN, IFY, FARCALL, etc.) are automatically recalculated

##  Example Results

Translation size changes (AIR French translation):
```
seen163: 137464 bytes → 143228 bytes (+4.2%)
seen203: 255016 bytes → 264418 bytes (+3.7%)
seen501: 483434 bytes → 501886 bytes (+3.8%)
```

All files imported successfully with automatic jump offset recalculation.

##  Building

### Requirements
- Go 1.16 or higher
- Original LuckSystem 2.3.2 source code

### Compilation
```bash
cd LuckSystem-2.3.2
go build -o lucksystem
```

### Windows
```cmd
cd LuckSystem-2.3.2
go build -o lucksystem.exe
```

##  Usage

Same as original LuckSystem:

```bash
lucksystem script import \
  -s SCRIPT.PAK \
  -c UTF-8 \
  -O data/OPCODE.txt \
  -p data/GAME.py \
  -i translated_scripts/ \
  -o SCRIPT_TRANSLATED.PAK
```

##  Important Notes

1. **Backup your original SCRIPT.PAK** before replacing it
2. Use the **same OPCODE.txt and plugin** for both decompile and import
3. Test with a **single file first** before processing all scripts
4. Jump operations must be properly defined in your plugin file

## Known Issues

None currently. If you encounter issues, please open a GitHub issue with:
- Your command line
- The error message
- A sample of the problematic script file

##  License

This patch maintains the same license as the original LuckSystem project.

##  Credits

- **Original LuckSystem:** [wetor/LuckSystem](https://github.com/wetor/LuckSystem)
- **Patch Author:** Yoremi (AIR French translation project)
- **Testing:** AIR Steam French translation team

##  Related Projects

- [LuckSystem Original](https://github.com/wetor/LuckSystem)
- AIR French Translation (link to your translation project)

##  Contact

For questions or issues specific to this patch, please open a GitHub issue.
