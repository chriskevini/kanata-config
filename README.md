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
- **lalt** in sft layer → num layer (works on split keyboard, has limitations on laptop keyboard)

### Ctl Layer (lalt + key)
- All keys with control (C-a, C-b, etc.)
- **ralt** in ctl layer → sym layer (works on split keyboard, has limitations on laptop keyboard)

## Chords

All chords use a single 100ms timeout (suitable for split keyboard with cross-hand timing delays):

- `q+w` → esc
- `d+f` → tab
- `w+x` → switch to game layer
- `c+v` → M-spc (Meta+Space)
- `x+c` → M-A-spc (Meta+Alt+Space)
- `esc+tab` → switch to base layer (from game layer)

## Timeouts
- `tap-timeout`: 150ms
- `hold-timeout`: 300ms
- `ralt-hold-timeout`: 150ms
- `chord-timeout`: 100ms (all chords)

## Known Issues

### Sequential Layer Activation - Laptop Keyboard Limitation

**Problem**: Sequential layer activation (`ralt→lalt` for num layer, `lalt→ralt` for sym layer) blocks certain keys on laptop keyboards.

**Status**: 
- ✅ **Works perfectly on split keyboard** (QMK/Vial)
- ⚠️ **Partially broken on laptop keyboard** - certain keys blocked: `a f c v b n ; ' , . /` and numbers `1 2 3 4 5`

**Root cause**: 
This is a **laptop keyboard hardware limitation**, not a kanata issue. Most laptop keyboards have limited key rollover due to keyboard matrix design, which prevents certain key combinations from being registered simultaneously. This is commonly known as "ghosting" or "key blocking."

**Evidence**:
- Same configuration works flawlessly on split keyboard
- Problem is consistent across different kanata configurations
- The specific pattern of failing keys suggests hardware matrix limitations
- Split keyboards typically have better key rollover (NKRO or 6KRO minimum)

**Workarounds**:
1. **Use split keyboard**: Primary hardware - full functionality
2. **Use `rctl+lalt` for sym layer**: Works on both keyboards since rctl→num→lalt→sym
3. **Chord-based activation**: Could implement `ralt+lalt` pressed together (not yet implemented)
4. **Different physical key**: Map layer access to a key with better matrix position

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
- `master`: Main configuration with consolidated chord groups
- `mod-sequence-layers`: Configuration with sequential layer activation (sft/ctl layers)
