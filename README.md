# Kanata Configuration

Split keyboard configuration for kanata with custom layers, chords, and tap-hold modifiers.

## Hardware
- Split keyboard (QMK/Vial) with timing delays between halves
- Requires extended chord timeouts (100ms instead of 50ms) for cross-hand chords

## Layer Structure

### Base Layer
- **lalt**: tap=C-bspc, hold=ctl layer
- **ralt**: tap=ret, hold=sft layer  
- **rctl**: tap=esc, hold=num layer
- **spc**: tap=spc, hold=nav layer

### Nav Layer (space + key)
- `i/j/k/l` → M-up/M-left/M-down/M-right
- `a/s/d/f/g` → M-1/M-2/M-3/M-4/M-5
- Other keys → M-{key} for window management

### Num Layer (rctl + key)
- Home row: `1 2 3 4 5`
- Bottom row: `6 7 8 9 0`
- `i/j/k/l` → up/left/down/right arrows
- **lalt** in num layer → activates sym layer

### Sym Layer (rctl + lalt + key)
- Top row: `! @ # ] # { & * ( )`
- Home row: `# $ ( ) ` ~ - + = |`
- Bottom row: `% ^ [ ] \ _ / _ _ _`

### Sft Layer (ralt + key)
- All keys shifted (S-a, S-b, etc.)
- **lalt** in sft layer → num layer (BROKEN - see below)

### Ctl Layer (lalt + key)
- All keys with control (C-a, C-b, etc.)
- **ralt** in ctl layer → sym layer (BROKEN - see below)

## Timeouts
- `tap-timeout`: 150ms
- `hold-timeout`: 300ms
- `ralt-hold-timeout`: 150ms
- `fast-chord-timeout`: 50ms (same-hand, laptop keyboard)
- `slow-chord-timeout`: 100ms (cross-hand, split keyboard)

## Known Issues

### Sequential Layer Activation Bug

**Problem**: Attempting to activate num/sym layers via sequential key presses fails for specific keys.

**Desired behavior:**
- `ralt` (hold) → `lalt` (press) → activate num layer without shift modifier
- `lalt` (hold) → `ralt` (press) → activate sym layer without control modifier

**Actual behavior:**
- Layers activate correctly
- BUT certain keys fail to output: `a f c v b n ; ' , . /` and numbers `1 2 3 4 5`
- Other keys work fine

**Root cause**: 
This appears to be a fundamental kanata limitation when **two tap-hold keys are held simultaneously** where one activates a layer containing another layer-switching key. Kanata blocks certain key outputs in this multi-hold state.

**Evidence it's not a config issue:**
- Same layer definitions work perfectly when activated via `rctl` directly
- Problem persists regardless of using `unshift`, `unmod`, or plain key definitions
- Problem persists with both `multi lsft` approach and explicit `S-*` key definitions
- All approaches produce identical pattern of failing keys

### Approaches Tried (All Failed)

#### 1. Multi with modifiers
```lisp
;; In defalias
ralt (layer-while-held ralt_down)

;; In ralt_down layer
lalt (multi lsft (layer-while-held num))

;; Separately tried release-key
lalt (multi (release-key lsft) (layer-while-held num))
```
**Result**: Same keys fail

#### 2. Unshift wrappers
```lisp
;; In num layer
a (unshift 1)
s (unshift 2)
;; etc.
```
**Result**: Same keys fail

#### 3. Unmod wrappers
```lisp
;; In num layer
a (unmod 1)
s (unmod 2)
;; etc.
```
**Result**: Same keys fail

#### 4. Explicit layer definitions (current config)
```lisp
;; defalias
ralt (layer-while-held sft)
lalt (layer-while-held ctl)

;; sft layer - all S-* keys
laltsft (layer-while-held num)

;; ctl layer - all C-* keys
raltctl (layer-while-held sym)
```
**Result**: Same keys fail

#### 5. Various timeout adjustments
- Tried different timeout values
- Tried `tap-hold-press` vs `tap-hold-release`
- No effect on failing keys

### Hypothesis
This is likely a **kanata engine limitation or bug** when processing multiple held tap-hold keys that chain layer activations. The specific pattern of failing keys suggests kanata may be:
- Blocking keys that create ambiguous states
- Hitting internal key tracking limits
- Filtering certain HID scancodes in multi-hold scenarios

### Workarounds (Not Implemented)
1. **Chord-based activation**: Press `ralt+lalt` together instead of sequentially
2. **Tap-dance**: Double-tap ralt to toggle num layer
3. **Different physical key**: Use a separate key for layer access
4. **Accept limitation**: Use `rctl+lalt` for sym layer (works fine since rctl→num→lalt→sym)

## Management Scripts

### setup-kanata.sh
Sets up kanata as systemd service, copies config to `/etc/kanata/`

### restart-kanata.sh
Copies current config to `/etc/kanata/kanata-config.kbd` and restarts service

### remove-kanata.sh
Stops and removes kanata systemd service

## Validation
```bash
kanata --check --cfg /home/chris/.kanata/kanata.kbd
```

## Git Branches
- `main`: Stable configuration without sequential layer activation
- `mod-sequence-layers`: Experimental branch attempting sequential layer activation (FAILED)
