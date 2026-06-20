# Fable Mind Cognitive Tracking State

## Current State
- Copied `Cloper_Jump_Online_v11_2.html` to `Cloper_Jump_Online_v12_2.html`.
- Analyzing the 23 requested tasks to prioritize complex features first.
- Implementing Fable 5 frameworks: Architectural debate on single-file monolithic management, Object Pooling for game loops, and FSM for complex UI transitions.

## Potential Risks/Blockers
- Injecting multiple heavy UI overlays (Admin Dashboard, Chat, 3D Design Mode) into a single HTML file will explode DOM nodes and slow down rendering.
- Over-frequent DOM updates in requestAnimationFrame.
- Race conditions during UI state toggling.

## Next Steps
- Implement Core Game Mechanics modifications (Speed, Gold, Fog, Respawn).
- Establish FSM and State patterns for UI menus.

### Task Batching Strategy
**Phase 1: Game Loop & Physics (Tasks 14, 16, 18, 21)**
- Task 21: Reduce speed by 30% (`baseSpeed`, `speed`, `maxSpeed` adjustments).
- Task 16: Gold spawn density/trails (`itemRate` update or cluster logic change).
- Task 18: Fog intensity and landscape (`this.scene.fog` tuning).
- Task 14: Defeat screen blur & auto-respawn logic (`onGameOver` overlay update & timeout logic).

**Phase 2: Core Overlays & UI Navigation (Tasks 1, 2, 8, 22)**
- Task 1: Floating navbar reorder [Follow US | Quests | Home | Guide | Profile].
- Task 2: Floating Action menu (Inventory, Events, Friends).
- Task 8: Play Now Map Selector.
- Task 22: Responsive layout (DPI).

**Phase 3: Deep Features & Modes (Tasks 3, 4, 5, 12)**
- Task 3: Profile Page (Avatar crop, UID copy).
- Task 4: Login/Register form.
- Task 5: Lucky Wheel Gacha.
- Task 12: Loot Box skip & animations.

**Phase 4: Economy & Network (Tasks 6, 7, 10, 11)**
- Task 6: IAP real money.
- Task 7: Manual Bank Transfer QR.
- Task 10: Friends system.
- Task 11: Mailbox.

**Phase 5: Admins & Assets (Tasks 15, 17, 19, 20, 23)**
- Task 15: 3D design mode.
- Task 17: Quests.
- Task 19: Live Chat.
- Task 20: Admin System.
- Task 23: Custom Canvas Icons.

### Internal Debate: Task 14 (Defeat Screen Blur & Auto-respawn vs Game Over overlay)
- **Problem**: The user requested a "Defeat Screen: Slightly blurred/transparent background to see the game. Auto-respawn after 3 seconds, pushing position back slightly." The current code stops the game completely (`this.active=false; cancelAnimationFrame()`) and brings up an opaque Game Over UI.
- **Debate**: Should we convert the game over into a soft-fail (respawn) or is it a hard-fail? The prompt says "Auto-respawn after 3 seconds, pushing position back slightly. Gold is competitive." This implies continuous play where dying is just a time penalty.
- **Resolution**: I need to alter `_gameOver()` to pause `this.speed` but keep `this.active=true` so particles and background elements keep rendering. Show a transparent UI overlay for 3 seconds, move `this.playerGroup.position.z` back slightly, reset `this._dead`, and resume speed.

### Internal Debate: Task 16 (Gold Density)
- **Problem**: "Gold spawns much more densely or in continuous trailing lines."
- **Resolution**: Alter `_spawnItemCluster()`. Instead of random small clusters, spawn a long straight/sine line of coins. Increase `this.itemRate` (make it smaller, so it triggers more often).
