================================================================================
  INVENTORY SORT POSITION FIX
  A G.A.M.M.A. RC3 quality-of-life patch
================================================================================

DESCRIPTION
-----------
When items are modified or transferred, they land at the very bottom of the
inventory instead of staying in their correct sorted row. This mod fixes that.

Affected actions (vanilla bug):
  - Repairing a weapon or armour at a mechanic
  - Upgrading an item
  - Crafting or combining items
  - Attaching / detaching weapon parts (suppressors, scopes, etc.)
  - Moving items into a stash, chest, or trader window

All of the above would push the item to the last row of the inventory.
The fix places items in their correct sorted position and preserves your
scroll position so the inventory does not jump back to the top.

WORKS IN ALL INVENTORY MODES
  - Regular inventory  (pressing I / Tab)
  - Loot mode          (opening chests, stashes, corpses)
  - Trade mode         (talking to traders — both your side and trader side)


REQUIREMENTS
------------
  - Anomaly (any version) OR G.A.M.M.A. RC3
  - SortingPlus by RavenAscendant

The mod only uses functions from the standard Anomaly ui_inventory.script and
utils_ui.script — no GAMMA-specific code is referenced. It works on base Anomaly
with SortingPlus installed, as well as on GAMMA RC3 where SortingPlus is
included by default (mod 110).

This mod fixes a side effect introduced by SortingPlus's kind-based row sorting.
Without SortingPlus active, this patch is harmless but unnecessary.


INSTALLATION
------------
1. Copy the "501- Inventory Resort Fix" folder into:
     [Your GAMMA RC3 install]\mods\

2. Open Mod Organizer 2 and enable the mod in the left panel.

3. Priority / load order: any position works. The script file uses the "zz_"
   prefix to ensure it loads after ui_inventory.script alphabetically.
   No specific priority relative to other mods is required.


TECHNICAL EXPLANATION (for mod authors)
----------------------------------------
SortingPlus tracks which inventory row each item kind occupies via an rKind
state table. After the initial full sort, rKind points to the last kind placed.

When ui_inventory's *Mode_RefreshInventories functions add a new item
incrementally (via AddItem / TransferItem), FindFreeCell sees that the new
item's kind differs from rKind.last and jumps to row_end + 1 — placing the
item after everything else.

This mod monkey-patches IMode_RefreshInventories, LMode_RefreshInventories,
TMode_RefreshInventories, and Action_Move. When a new item is detected in a
bag, it schedules a full UICellContainer:Reinit() on the next engine tick
(via CreateTimeEvent delay 0), resetting the rKind state so all items are
placed in their correct sorted rows.

Scroll position is saved before Reinit and restored after via Scroll_SetPos.

For trade bags, ParseInventory returns all NPC items including those already
in the purchase basket, so basket items are stripped from the bag after Reinit
to prevent duplicates.


COMPATIBILITY
-------------
  Compatible with all standard G.A.M.M.A. mods.
  Does not modify any existing files — single new script only.
  Safe to add or remove mid-playthrough.


CREDITS
-------
  SortingPlus — RavenAscendant
  G.A.M.M.A. RC3 — Grok and the G.A.M.M.A. team
  G.A.M.M.A. UI — Tronex

================================================================================
