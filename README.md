# MSESP
**Documentation:** https://docs.mstudio45.com/home

## Installation using Wally
**Wally Package:** https://wally.run/package/mstudio45/msesp

Add `mstudio45/msesp` to your `wally.toml`:

```toml
[dependencies]
msesp = "mstudio45/msesp@2.1.2"
```

# Credits

I take no credit for this library, fork, or any associated scripts. All credits of this project go to the original owner [mstudio45](https://github.com/mstudio45/).

# Changes in this fork
1. **`Render()` — `Hide()` → `Hide(true)` on both early exits (lines 1882 and 1896)**
Both places where `Render()` exits now pass `force = true`:
```lua
-- Early visibility/camera check
Hide(true)  -- was: Hide()

-- Distance check
Hide(true)  -- was: Hide()
```
Without this, `Hide()` silently no-ops on every frame after the first hide because of the `ESP.Hidden` guard, meaning Highlight and BillboardGui instances could remain if Roblox happens to re-enable them engine-side.

2. **`Box3D.SetVisible` — clears control points when hiding (lines 1345–1347)**
```lua
if not Visible then
    BoxPath:SetControlPoints({})
end
```
Before moving `BoxPath` to `StorageFolder` and setting `.Visible = false`, the path's control points are now cleared. This prevents `Path2D` from keeping its last-drawn wireframe rendered, which caused Drawing objects such as
both 2D and 3D boxes and highlights to continue rendering even when the target is not within distance.

## What is NOT changed
Everything else is identical to the original MSESP created by [mstudio45](https://github.com/mstudio45/) — the Highlighter's `SetVisible` still uses the `Parent == Parent` early-exit guard, the Billboard component is untouched, the Skeleton component's `SetVisible` does not clear control points on hide (only the Box3D does), and no other logic was modified. The fork is a minimal two-point fix for 2D and 3D box distance rendering.
