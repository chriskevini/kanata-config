# Kanata Configuration - Session Context

## Current State
**File**: `/home/chris/.kanata/kanata.kbd`
**Git repo**: Initialized with commits on `mod-sequence-layers` branch
**Hardware**: Split keyboard (QMK/Vial) with timing delays between halves

## Recent Changes

### Session 1 (Previous)
- Fixed `space+ralt+j` not triggering immediately by changing `j` from chord alias to plain key
- Cleaned up configuration (removed unused chord aliases, renamed aliases to max 8 chars)
- Added consistent spacing alignment to all layers
- Investigated tap-hold variants (changed to `tap-hold-press` initially)

### Session 2
1. **Created git repository**
   - Made initial commit with kanata.kbd
   - Added management scripts (setup, restart, remove)

2. **Fixed d+f chord for tab**
   - Problem: d+f chord worked on laptop keyboard but not split keyboard
   - Root cause: Split keyboard has latency between halves
   - Solution: Created separate `df` chord group with 100ms timeout (same pattern as existing `qw` chord)
   - Committed: "Add d+f chord for tab with extended timeout for split keyboards"

3. **Fixed sym_layer activation**
   - Problem: rctl+lalt+key didn't trigger sym_layer
   - Root cause: `lalt_num` was mapped to plain `lalt` instead of `(layer-while-held sym_layer)`
   - Solution: Changed `lalt_num` to activate sym_layer
   - Committed: "Fix sym_layer activation via lalt in num_layer"

4. **Reduced accidental space+t triggers**
   - Problem: Accidentally triggering M-t when typing space+t
   - Solution: Changed `spc` from `tap-hold-press` to `tap-hold-release`
   - Status: Needs testing

### Session 3 (Current - mod-sequence-layers branch)
1. **Created git repository**
   - Made initial commit with kanata.kbd
   - Added management scripts (setup, restart, remove)

2. **Fixed d+f chord for tab**
   - Problem: d+f chord worked on laptop keyboard but not split keyboard
   - Root cause: Split keyboard has latency between halves
   - Solution: Created separate `df` chord group with 100ms timeout (same pattern as existing `qw` chord)
   - Committed: "Add d+f chord for tab with extended timeout for split keyboards"

3. **Fixed sym_layer activation**
   - Problem: rctl+lalt+key didn't trigger sym_layer
   - Root cause: `lalt_num` was mapped to plain `lalt` instead of `(layer-while-held sym_layer)`
   - Solution: Changed `lalt_num` to activate sym_layer
   - Committed: "Fix sym_layer activation via lalt in num_layer"

4. **Reduced accidental space+t triggers**
   - Problem: Accidentally triggering M-t when typing space+t
   - Solution: Changed `spc` from `tap-hold-press` to `tap-hold-release`
   - Status: Needs testing

## Current Configuration Overview

### Layers
- `base`: Main typing layer with chords
- `game`: Toggle layer for gaming (activated by w+x chord)
- `nav`: Activated by holding space, contains Meta+arrow keys and Meta+numbers
- `num`: Activated by holding rctl, contains numbers and arrow keys
- `sym`: Activated by rctl+lalt, contains symbols
- `ralt_down`: Transparent layer activated when ralt is held (provides shift on ralt position)
- `ralt_then_lalt`: Activated when ralt is held then lalt is pressed (num layer without shift)

### Key Tap-Hold Configs
- `lalt`: tap=C-bspc, hold=lctl (tap-hold-release)
- `ralt`: tap=ret, hold=ralt_down layer (tap-hold-release)
- `rctl`: tap=esc, hold=num layer (tap-hold-press)
- `spc`: tap=spc, hold=nav layer (tap-hold-release)

### Timeouts
- `tap-timeout`: 150ms
- `hold-timeout`: 300ms
- `ralt-hold-timeout`: 150ms
- `fast-chord-timeout`: 50ms (for fast chords: c+v)
- `slow-chord-timeout`: 100ms (for slow chords: q+w, d+f, w+x - split keyboard timing)

### Chords
- `q+w` → esc (100ms timeout)
- `d+f` → tab (100ms timeout)
- `w+x` → switch to game layer (100ms timeout)
- `c+v` → M-spc (50ms timeout)
- `esc+tab` → switch to base layer (from game layer, 50ms timeout)

### Nav Layer (space+key)
- `i/j/k/l` → M-up/M-left/M-down/M-right (arrow navigation)
- `a/s/d/f/g` → M-1/M-2/M-3/M-4/M-5 (window switching)
- Other keys mapped to M-{key} for various shortcuts

### Num Layer (rctl+key)
- Home row: `1 2 3 4 5`
- Bottom row: `6 7 8 9 0`
- `j/k/l` → left/down/right arrows
- `i` → up arrow

### Sym Layer (rctl+lalt+key)
- Top row: `! @ [ ] { } & * ( )`
- Home row: `# $ ( ) ` ~ - + = |`
- Bottom row: `% ^ [ ] \ _ / _ _ _`

### Sequential Modifier Layers
- `ralt_down`: Transparent layer with shift on ralt position
- `ralt_then_lalt`: When ralt is held and lalt is pressed, activates num layer keys without shift modifier

## Open Issues (Todo List)

### High Priority
1. **Test sequential modifier layer activation**
   - Test: ralt→lalt should activate num layer without shift
   - Test: verify shift still works when only ralt is held
   - May need to implement lalt→ralt for sym layer as well

### Medium Priority
2. **Setup .kanata as submodule in parent repo**
   - Parent directory is also a git repo
   - Goal: Push .kanata to its own remote and link as submodule in parent
   - Steps: Create remote repo → push .kanata → add as submodule in parent

### Resolved
- ~~Fix apostrophe key timeout with ralt~~ (Fixed: added apostrophe to all layers)
- ~~Fix spc+ralt+i/j/k not triggering immediately~~ (Not pursuing - may be hardware limitation)

## Useful Chord Ideas (For Future)
Suggested potentially useful chords for future consideration:
- Same-hand home row: `a+s`, `s+d`, `j+k`, `k+l`
- Adjacent keys: `e+r`, `u+i`, `i+o`, `r+t`
- Vertical chords: `w+s`, `e+d`, `r+f`
- Common actions: `a+f`, `s+f`

Note: Focus on same-side chords due to split keyboard timing issues.

## Important Notes
- **Split keyboard timing**: Slow chords need 100ms timeout instead of 50ms due to latency between keyboard halves
- **Restart command**: `./restart-kanata.sh` (requires sudo, copies config to `/etc/kanata/kanata-config.kbd`)
- **Config validation**: `kanata --check --cfg /home/chris/.kanata/kanata.kbd`
- **Active process**: Kanata runs as systemd service using `/etc/kanata/kanata-config.kbd`
- **Naming convention**: Aliases follow `[action][layer]` format (e.g., `escgame`, `tabgame`). Base layer aliases omit layer suffix.
- **Branch**: Currently on `mod-sequence-layers` branch for testing sequential modifier layer activation
