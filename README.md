# Android 16 (One UI 8.0) IGameManagerService Binder IPC Mapping & Performance Override

Reverse-engineered IPC transaction codes for `IGameManagerService` extracted directly from Android 16 framework Smali sources.

This repository provides full documentation and automation scripts to interact with the low-level Binder interface (`service call game`), enabling direct performance and frame-rate pipeline overrides without relying on high-level APIs or restricted CLI tools.

---

> ⚠️ **Verified Device & Firmware Environment**
> 
> * **Hardware:** Samsung Galaxy S21 FE 5G (`SM-G990B2`)
> * **OS / Platform:** Android 16 / Samsung One UI 8.0 (Build: `BP2A.250605.031.A3.G990B2XXSJIZF1`)
> * **Behavior Notice:** On Samsung One UI 8.0, running `cmd game mode <package>` directly against non-game targets (like `com.android.systemui`) returns `Unknown command` or `is not a game`.
> * **Why Binder Overrides Work:** Direct Binder calls via `service call game 2 ...` bypass `cmd game` validation checks and execute RPCs directly at the `GameManagerService` IPC layer.

---

## 1. Interface Definition & Transaction Mapping

* **Interface Descriptor:** `android.app.IGameManagerService`
* **Service Name:** `game`

| Transaction ID (Hex) | Transaction ID (Dec) | Smali Method / Field Name | Signature Parameters |
| :--- | :--- | :--- | :--- |
| `0x1` | 1 | `TRANSACTION_getGameMode` | `(String packageName, int userId)` |
| **`0x2`** | **2** | **`TRANSACTION_setGameMode`** | **`(String packageName, int gameMode, int userId)`** |
| `0x3` | 3 | `TRANSACTION_getAvailableGameModes` | `(String packageName, int userId)` |
| `0x4` | 4 | `TRANSACTION_isAngleEnabled` | `(String packageName, int userId)` |
| `0x5` | 5 | `TRANSACTION_notifyGraphicsEnvironmentSetup` | `(String packageName, int userId)` |
| `0x6` | 6 | `TRANSACTION_setGameState` | `(String packageName, GameState state, int userId)` |
| `0x7` | 7 | `TRANSACTION_getGameModeInfo` | `(String packageName, int userId)` |
| `0x8` | 8 | `TRANSACTION_setGameServiceProvider` | `(String packageName)` |
| `0x9` | 9 | `TRANSACTION_updateResolutionScalingFactor` | `(String packageName, int gameMode, float scalingFactor, int userId)` |
| `0xa` | 10 | `TRANSACTION_getResolutionScalingFactor` | `(String packageName, int gameMode, int userId)` |
| `0xb` | 11 | `TRANSACTION_updateCustomGameModeConfiguration` | `(String packageName, GameModeConfiguration config, int userId)` |
| `0xc` | 12 | `TRANSACTION_addGameModeListener` | `(IGameModeListener listener)` |
| `0xd` | 13 | `TRANSACTION_removeGameModeListener` | `(IGameModeListener listener)` |
| `0xe` | 14 | `TRANSACTION_addGameStateListener` | `(IGameStateListener listener)` |
| `0xf` | 15 | `TRANSACTION_removeGameStateListener` | `(IGameStateListener listener)` |
| `0x10` | 16 | `TRANSACTION_toggleGameDefaultFrameRate` | `(boolean enabled)` |

---

## 2. Usage Examples (ADB / LADB Shell)

### Individual Target Execution

Force **Performance Mode** (`gameMode = 1`) for any package (including system interfaces) for Primary User (`userId = 0`):

```bash
# Command Structure:
# service call game 2 s16 "<PACKAGE_NAME>" i32 <GAME_MODE> i32 <USER_ID>

# Force Performance Mode on System UI (Unlocks smooth quick panel and system animations)
service call game 2 s16 "com.android.systemui" i32 1 i32 0

# Force Performance Mode on Standoff 2
service call game 2 s16 "com.axlebolt.standoff2" i32 1 i32 0
