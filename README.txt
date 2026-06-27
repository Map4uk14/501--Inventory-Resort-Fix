================================================================================
  SEAMLESS INVENTORY SORT
  A G.A.M.M.A. RC3 quality-of-life patch
================================================================================

DESCRIPTION
-----------
When you modify or move an item, the engine appends it to the BOTTOM of the
list instead of keeping it in its correct sorted row. This mod snaps the item
back to its proper sorted position the instant it lands -- seamlessly, with no
flicker and without losing your scroll position.

Fixed actions (the vanilla "jumps to the end" behaviour):
  - Attaching / detaching weapon parts: scopes, HOLOGRAPHIC sights,
    suppressors, grenade launchers
  - Unloading a magazine
  - Repairing / upgrading / crafting / combining items
  - Picking items up into your bag
  - Moving items into or out of a stash, chest, corpse, or companion
  - Buying / selling at a trader

WORKS IN EVERY INVENTORY WINDOW
  - Inventory / repair         (your bag)
  - Loot / stash / container    (your side AND the container side)
  - Trade                       (your sale bag AND the trader's sale bag)

Bulk transfer (Ishmaeel's FastTransfer -- sweeping items with SHIFT held) is
handled specially: sorting is frozen while you hold shift and done once in a
single pass when you release it, so bulk stashing stays fast.


REQUIREMENTS
------------
  - G.A.M.M.A. RC3 (or Anomaly) with:
      * SortingPlus by RavenAscendant          (GAMMA mod 110)  -- required
      * Inventory Open Lag Reducer (sea-ex)     (GAMMA mod 464)  -- recommended

The seamless, in-place sorting reuses SeaX's (mod 464) optimized sort. Without
it, the inventory and loot windows fall back to a full rebuild (still correct,
just a slight visible refresh) and the trade window is left unsorted for safety.
All three are included and enabled by default in G.A.M.M.A. RC3.


INSTALLATION
------------
1. Place the "501- Seamless Inventory Sort" folder into:
     [Your GAMMA RC3 install]\mods\

2. Enable it in Mod Organizer 2 (left panel).

3. Load order does not matter. The script uses the "zz_" prefix so it loads
   after ui_inventory.script. Safe to add or remove mid-playthrough.


HOW IT WORKS (for mod authors)
------------------------------
SortingPlus places items by "kind" using an rKind row tracker. When the stock
*Mode_RefreshInventories functions add an item incrementally, FindFreeCell sees
the new item's kind != rKind.last and drops it at row_end + 1 (the end).

This mod hooks IMode_/LMode_/TMode_RefreshInventories. Right after the stock
refresh runs -- in the SAME frame, before the UI draws -- it re-sorts the
affected container so the item never renders at the end (this is what makes it
seamless, including the holo sight's two-stage weapon respawn).

The re-sort ("soft_resort") does NOT rebuild the container. It REPOSITIONS the
existing cells into their sorted slots via UICellItem:Set(area), reusing SeaX's
kind comparator + cell placement. Because nothing is added or removed:
  - there is no Reset()/Show(false) blink, even on huge stashes;
  - stacked-item children are left intact;
  - loot-box put/take callbacks are not re-fired (no rebuild feedback loop).

A position-aware fingerprint guards against redundant re-sorts.

Trade is SOFT-ONLY -- trade containers are never Reinit'd. A trade Reinit would
rebuild from ParseInventory(npc), which still contains basketed items (they stay
in the ruck until the deal is confirmed) and would re-add them to the sale bag,
causing the classic duplicate / stuck-in-basket bug. Repositioning cannot do
that, so it is safe.

Shift state is read with key_state(DIK_LSHIFT/RSHIFT), matching FastTransfer, so
bulk-transfer detection works with both shift keys.


COMPATIBILITY
-------------
  - Compatible with standard G.A.M.M.A. mods.
  - Works alongside Ishmaeel's FastTransfer (Nitpicker's Modpack, mod 124).
  - Modifies NO existing files -- a single new script only.
  - Safe to add or remove mid-playthrough.


CREDITS
-------
  SortingPlus                 -- RavenAscendant
  Inventory Open Lag Reducer  -- sea-ex
  FastTransfer                -- Ishmaeel (Nitpicker's Modpack)
  G.A.M.M.A. UI               -- Tronex
  G.A.M.M.A. RC3              -- Grok and the G.A.M.M.A. team

================================================================================
